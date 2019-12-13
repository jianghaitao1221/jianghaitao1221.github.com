---
layout:     post
title:      "数据分析学习（十一）"
subtitle:   "\"Apache Parquet\""
date:       2019-11-17 10:00:00
author:     "Jht"
header-img: "img/post-bg-data-analysis.jpg"
catalog: true
tags:
    - analysis
---

## Parquet

Parquet 是一种面向分析的、通用的列式存储格式，由Twitter和Cloudera合作开发，2015年5月从Apache的孵化器里毕业成为 Apache 顶级项目。它是语言、平台无关的，并且不需要和任何一种数据处理框架绑定，目前能够和Parquet适配的组件包括下面这些，可以看出基本上通常使用的查询引擎和计算框架都已适配，并且可以很方便的将其它序列化工具生成的数据转换成Parquet格式。

- 查询引擎: Hive, Impala, Pig, Presto, Drill, Tajo, HAWQ, IBM Big SQL
- 计算框架: MapReduce, Spark, Cascading, Crunch, Scalding, Kite
- 数据模型: Avro, Thrift, Protocol Buffers, POJOs

### 行存与列存的区别

![](/img/in-post/parquet/parquet1.png)

在行式存储中一行的多列是连续的写在一起的，在列式存储中数据按列分开存储，例如可以只读取 A.B.C 这一列的数据而不去读 A.E 和 A.B.D，那么如何根据读取出来的各个列的数据重构出一行记录呢？

Google 的Dremel系统解决了这个问题，核心思想是使用“record shredding and assembly algorithm”来表示复杂的嵌套数据类型，同时辅以按列的高效压缩和编码技术，实现降低存储空间，提高 IO 效率，降低上层应用延迟。

Parquet 就是基于 Dremel 的数据模型和算法实现的。

列式存储优点：

- 把IO只给查询需要用到的数据
  - 只加载需要被计算的列
- 空间节省
  - 列式的压缩效果更好
  - 可以针对数据类型进行编码
- 只读取需要的列，支持向量运算，能够获取更好的扫描性能。

![](/img/in-post/parquet/parquet20.png)

可以看出Parquet较之于其他的二进制文件存储格式能够更有效的利用存储空间。
    
### 项目组成

数据从内存到 Parquet 文件或者反过来的过程主要由以下三个部分组成

![](/img/in-post/parquet/parquet2.png)

- 存储格式 (storage format)
  - parquet-format项目定义了 Parquet 内部的数据类型、存储格式等。
- 对象模型转换器 (object model converters)
  - 这部分功能由parquet-mr项目来实现，主要完成外部对象模型与 Parquet 内部数据类型的映射。
- 对象模型 (object models)
  - 对象模型可以简单理解为内存中的数据表示，Avro, Thrift, Protocol Buffers, Hive SerDe, Pig Tuple, Spark SQL InternalRow 等这些都是对象模型。

Parquet项目由以下几个子项目组成:

- parquet-format：定义了所有格式规范，以及由 Thrift 序列化的元数据信息等。
- parquet-mr：包括多个实现了读写 Parquet 文件的功能模块，并且提供一些和其它组件适配的工具，例如Hadoop Input/Output Formats、Pig loaders、Hive Serde等。
- parquet-cpp：用于读写 Parquet 文件的 C++ 库。
- parquet-rs：用于读写 Parquet 文件的 Rust 库。
- parquet-compatibility：包含验证不同语言之间读写Parquet文件的兼容性测试等。

### 数据模型

Parquet 是一种支持嵌套的数据模型，和 Protocol Buffers 的数据模型类似，它的 schema 就是一个嵌套 message。

每个schema包含多个字段，每一个字段又可以包含多个字段，每一个字段有三个属性：repetition、type 和 name. repetiton 可以是以下三种：

- required（出现1次）
- repeated（出现0次或多次）
- optional（出现0次或1次）


代码表示就是:

```
message AddressBook  {    
  required string owner;    
  repeated string ownerPhoneNumbers;    
  repeated group contacts {          
      required string name;          
      optional string phoneNumber;    
  }
}
```

举例如图：

![](/img/in-post/parquet/parquet3.png)


Parquet 格式的数据类型没有复杂的 Map, List, Set 等，而是使用 repeated fields 和 groups 来表示。

例如 List和Set可以被表示成一个 repeated field。

![](/img/in-post/parquet/parqest-set.png)

Map 可以表示成一个包含有key-value对的repeated field，而且key是 required的。

![](/img/in-post/parquet/parquet-map.png)

### Striping/Assembly算法


上文介绍了Parquet的数据模型，AddressBook中蓝色的部分称为尾节点，实际上parquet把一个树状结构，通过Striping/Assembly编码算法，转换成二维表结构。

在Document中存在多个非required列，由于Parquet一条记录的数据分散的存储在不同的列中，如何组合不同的列值组成一条记录是由Striping/Assembly算法决定的，在该算法中列的每一个值都包含三部分：value、repetition level和definition level。


#### Definition levels

嵌套数据类型的特点是有些 field 可以是空的，也就是没有定义。如果一个 field 是定义的，那么它的所有的父节点都是被定义的。从根节点开始遍历，当某一个 field 的路径上的节点开始是空的时候我们记录下当前的深度作为这个 field 的 Definition Level。如果一个 field 的 Definition Level 等于这个 field 的最大 Definition Level 就说明这个 field 是有数据的。对于 required 类型的 field 必须是有定义的，所以这个 Definition Level 是不需要的。


```
message ExampleDefinitionLevel {
  optional group a {
    optional group b {
      optional string c;
    }
  }
}
```
它包含一个列a.b.c，这个列的的每一个节点都是optional类型的。从根节点开始加（不包括根节点）

![](/img/in-post/parquet/parquet8.png)

![](/img/in-post/parquet/parquet9.png)


```
message ExampleDefinitionLevel {
  optional group a {
    required group b {
      optional string c;
    }
  }
}
```

![](/img/in-post/parquet/parquet10.png)


#### Repetition Levels

记录该field的值是在哪一个深度上重复的。只有repeated类型的field需要Repetition Level。

Repetition Level = 0 表示开始一个新的 record。

![](/img/in-post/parquet/parquet11.png)

Repetition Levels的值为：

![](/img/in-post/parquet/parquet12.png)


- repeated level=0，表示需要创建一个新的根节点(新记录)
- repeated level=1，表示需要创建一个新的level1节点
- repeated level=2，表示需要创建一个新的level2节点
  
本例中当读取文件构建每条记录的结果如下：

![](/img/in-post/parquet/parquet13.png)

下面用 AddressBook 的例子来说明 Striping 和 assembly 的过程。

![](/img/in-post/parquet/parquet14.png)

下面这样两条 record：

```
AddressBook {
 owner: "Julien Le Dem",
 ownerPhoneNumbers: "555 123 4567",
 ownerPhoneNumbers: "555 666 1337",
 contacts: {
   name: "Dmitriy Ryaboy",
   phoneNumber: "555 987 6543",
 },
 contacts: {
   name: "Chris Aniszczyk"
 }
}

AddressBook {
 owner: "A. Nonymous"
}

```

以 contacts.phoneNumber 这一列为例，"555 987 6543"这个 contacts.phoneNumber 的 Definition Level 是最大 Definition Level=2。而如果一个 contact 没有 phoneNumber，那么它的 Definition Level 就是 1。如果连 contact 都没有，那么它的 Definition Level 就是 0。

下面我们拿掉其他三个 column 只看 contacts.phoneNumber 这个 column，把上面的两条 record 简化成下面的样子:

```
AddressBook {
 contacts: {
   phoneNumber: "555 987 6543"
 }
 contacts: {
 }
}
AddressBook {
}

```

这两条记录的序列化过程如图:

![](/img/in-post/parquet/parquet16.png)

如果我们要把这个 column 写到磁盘上，磁盘上会写入这样的数据

![](/img/in-post/parquet/parquet17.png)


注意：NULL 实际上不会被存储，如果一个 column value 的 Definition Level 小于该 column 最大 Definition Level 的话，那么就表示这是一个空值。


可以看出在 Parquet 列式存储中，对于一个 schema 的所有叶子节点会被当成 column 存储，而且叶子节点一定是 primitive 类型的数据。对于这样一个 primitive 类型的数据会衍生出三个 sub columns (R, D, Value)，也就是从逻辑上看除了数据本身以外会存储大量的 Definition Level 和 Repetition Level。那么这些 Definition Level 和 Repetition Level 是否会带来额外的存储开销呢？实际上这部分额外的存储开销是可以忽略的。因为对于一个 schema 来说 level 都是有上限的，而且非 repeated 类型的 field 不需要 Repetition Level，required 类型的 field 不需要 Definition Level，也可以缩短这个上限。例如对于7 层嵌套的 schema 来说，只需要3个bits 就可以表示这两个Level了。

对于存储关系型的 record，record 中的元素都是非空的（NOT NULL in SQL）。Repetion Level 和 Definition Level 都是 0，所以这两个 sub column 就完全不需要存储了。所以在存储非嵌套类型的时候，Parquet 格式也是一样高效的。

#### 例子


![](/img/in-post/parquet/parquet18.png)


Repetition Level是记录该列的值是在哪一个级别上重复的。举个例子说明：

- 对于Name.Language.Code  我们一共有三条非Null的记录。

  - 第一个是”en-us”，出现在第一个Name的第一个Lanuage的第一个Code里面。在此之前，这三个元素是没有重复过的，都是第一个。所以其R为0。
  - 第二个是”en”，出现在下一个Lanuage里面。也就是说Lanague是重复的元素。Name.Language.Code中Lanague排第二个，所以其R为2
  - 第三个是”en-gb”，出现在下一个Name中，Name是重复元素，排第一个，所以其R为1。

我们可以想象，将所有的没有值的列，设值为NULL。如果是数组列，我们也想象有一个NULL值。有了Repetition Level，我们就可以很好的用列表示嵌套的结构了

- 对于Name.url  我们一共有三条非Null的记录。

  - 第一个是”http://A”，在它之前路径Name.url没有出现，所以r值是0。所以d值是2
  - 第二个是”http://B”，在其路径上Name重复出现了，而且url非repeated，所以这里要计算Name的级别，相对于根，Name的等级是1，r值是1， 非NULL， d值还是2
  - 对于第三个Name，url是NULL，此时在之前已经出现，r值是1， 是NULL，考虑url非required，是我们想象出来的数据，所以d值是1
  - 第三个是”http://C”，路径 Name.url没出现过，r值是0，同上，d值是2

- 对于Links.Forward  我们一共有四条非Null的记录。

  - 第一个是”20”，路径Links.Forward没出现过，r值是0，links是optinal，非required，所以d是2
  - 第二个是”40”，路径Links.Forward出现了，只有forward是repeated的，所以r=1， d=2
  - 第三个是”60”，路径Links.Forward出现了，只有forward是repeated的，所以r=1， d=2
  - 第四个是”80”，路径Links.Forward没出现过，r值是0，所以r=0， d=2

### 文件存储格式

那么如何把内存中每个 AddressBook 对象按照列式存储格式存储下来呢？

在Parquet 格式的存储中，一个 schema 的树结构有几个叶子节点，实际的存储中就会有多少 column。

![](/img/in-post/parquet/parquet4.png)

Parquet文件是以二进制方式存储的，所以是不可以直接读取的，文件中包括该文件的数据和元数据，因此Parquet格式文件是自解析的。在HDFS文件系统和Parquet文件中存在如下几个概念。

- HDFS块(Block)：它是HDFS上的最小的副本单位，HDFS会把一个Block存储在本地的一个文件并且维护分散在不同的机器上的多个副本，通常情况下一个Block的大小为256M、512M等。
- HDFS文件(File)：一个HDFS的文件，包括数据和元数据，数据分散存储在多个Block中。
- 行组(Row Group)：按照行将数据物理上划分为多个单元，每一个行组包含一定的行数，在一个HDFS文件中至少存储一个行组，Parquet读写的时候会将整个行组缓存在内存中，所以如果每一个行组的大小是由内存大的小决定的，例如记录占用空间比较小的Schema可以在每一个行组中存储更多的行。
- 列块(Column Chunk)：在一个行组中每一列保存在一个列块中，行组中的所有列连续的存储在这个行组文件中。一个列块中的值都是相同类型的，不同的列块可能使用不同的算法进行压缩。
- 页(Page)：每一个列块划分为多个页，一个页是最小的编码的单位，在同一个列块的不同页可能使用不同的编码方式。

#### 文件格式

通常情况下，在存储Parquet数据的时候会按照Block大小设置行组的大小，由于一般情况下每一个Mapper任务处理数据的最小单位是一个Block，这样可以把每一个行组由一个Mapper任务处理，增大任务执行并行度。Parquet文件的格式如下图所示

![](/img/in-post/parquet/parquet5.png)


上图展示了一个Parquet文件的内容，一个文件中可以存储多个行组，文件的首位都是该文件的Magic Code，用于校验它是否为一个Parquet文件，Footer length了文件元数据的大小，通过该值和文件长度可以计算出元数据的偏移量，文件的元数据中包括每一个行组的元数据信息和该文件存储数据的Schema信息。除了文件中每一个行组的元数据，每一页的开始都会存储该页的元数据，在Parquet中，有三种类型的页：数据页、字典页和索引页。数据页用于存储当前行组中该列的值，字典页存储该列值的编码字典，每一个列块中最多包含一个字典页，索引页用来存储当前行组下该列的索引

在执行MR任务的时候可能存在多个Mapper任务的输入是同一个Parquet文件的情况，每一个Mapper通过InputSplit标示处理的文件范围，如果多个InputSplit跨越了一个Row Group，Parquet能够保证一个Row Group只会被一个Mapper任务处理。

#### 映射下推(Project PushDown)

说到列式存储的优势，映射下推是最突出的，它意味着在获取表中原始数据时只需要扫描查询中需要的列，由于每一列的所有值都是连续存储的，所以分区取出每一列的所有值就可以实现TableScan算子，而避免扫描整个表文件内容。

在Parquet中原生就支持映射下推，执行查询的时候可以通过Configuration传递需要读取的列的信息，这些列必须是Schema的子集，映射每次会扫描一个Row Group的数据，然后一次性得将该Row Group里所有需要的列的Cloumn Chunk都读取到内存中，每次读取一个Row Group的数据能够大大降低随机读的次数，除此之外，Parquet在读取的时候会考虑列是否连续，如果某些需要的列是存储位置是连续的，那么一次读操作就可以把多个列的数据读取到内存。

#### 谓词下推(Predicate PushDown)

在数据库之类的查询系统中最常用的优化手段就是谓词下推了，通过将一些过滤条件尽可能的在最底层执行可以减少每一层交互的数据量，从而提升性能，例如”select count(1) from A Join B on A.id = B.id where A.a > 10 and B.b < 100”SQL查询中，在处理Join操作之前需要首先对A和B执行TableScan操作，然后再进行Join，再执行过滤，最后计算聚合函数返回，但是如果把过滤条件A.a > 10和B.b < 100分别移到A表的TableScan和B表的TableScan的时候执行，可以大大降低Join操作的输入数据。

无论是行式存储还是列式存储，都可以在将过滤条件在读取一条记录之后执行以判断该记录是否需要返回给调用者，在Parquet做了更进一步的优化，优化的方法时对每一个Row Group的每一个Column Chunk在存储的时候都计算对应的统计信息，包括该Column Chunk的最大值、最小值和空值个数。通过这些统计值和该列的过滤条件可以判断该Row Group是否需要扫描。另外Parquet未来还会增加诸如Bloom Filter和Index等优化数据，更加有效的完成谓词下推。

在使用Parquet的时候可以通过如下两种策略提升查询性能：1、类似于关系数据库的主键，对需要频繁过滤的列设置为有序的，这样在导入数据的时候会根据该列的顺序存储数据，这样可以最大化的利用最大值、最小值实现谓词下推。2、减小行组大小和页大小，这样增加跳过整个行组的可能性，但是此时需要权衡由于压缩和编码效率下降带来的I/O负载。



## 参考资料	

[Apache Parquet (Data Format)](https://parquet.apache.org/)	

[Apache Parquet Documentation](https://parquet.apache.org/documentation/latest/)	

[大数据：Parquet文件存储格式](https://www.cnblogs.com/lxbmaomao/p/9332749.html)	

[第63课：Spark sql 下的parquet内幕](https://bbs.pinggu.org/thread-4650864-1-1.html)	

[Apache Parquet 干货分享](https://www.jianshu.com/p/8b32d05cc80b)	

[深入分析 Parquet 列式存储格式](https://www.infoq.cn/article/in-depth-analysis-of-parquet-column-storage-format/)	

[Google Dremel 原理 - 如何能 3 秒分析 1PB](https://www.oschina.net/question/12_76159)

[Dremel made simple with Parquet](https://www.jianshu.com/p/03d48f2ad86e)
