---

layout:     post
title:      "kibana查询语法----lucene查询语法"
subtitle:   "elk学习二"
date:       2017-5-24 09:00:00
author:     "Jht"
header-img: "img/elk-bg.png"
catalog: true
tags:
    - elk
    - lucene
    - devops
---

## kibana查询

elasticsearch构建在Lucene之上。所以kibana使用lucene进行查询。

## Lucene查询

Lucene查询语法以可读的方式书写，然后使用JavaCC进行词法转换，转换成机器可识别的查询。

### 词语查询

词语搜索，支持`单词`和`语句`。

```bash
#单词：
"test","hello"
#语句：
"hello,world!"
```


### 字段查询

```bash
title:hello 
#或者 
title:"hello title"
```

### 修饰符查询

#### 通配符查询

`? * 不能用作第一个字符，例如：?text *text,但是在kibana中是支持这种搜索语法的。`

```bash
# ?匹配单个字符=
te?t  #想要搜索test或者text

# *匹配0个或多个字符
test* #想要搜索test  tests  tester

```

#### 模糊词查询

```bash
#想要搜索和test相近的词
test~
#可以搜索出text或者tests等词
#还可以指定需要多少相似度
cromm~0.3 #会匹配到from和chrome
#数值范围0.0 ~ 1.0，默认0.5，越大越接近搜索的原始值
```

#### 邻近词查询

语句模糊查询，只不过不是单词的模糊，而是单词之间内容的模糊。

```bash
"hello world"~10
#可以匹配"hello your world"
#或者"hello,Tom,world"
```

#### 范围查询

支持范围搜索，可以指定最小值和最大值。如果是单词，则会按照字典顺序搜索。

- `{}`尖括号表示不包含最小值和最大值，可以单独使用
- `[]`方括号表示包含最小值和最大值，可以单独使用

```bash
grade:{60,80]
#搜索名字在A和C之间的
name:{A,C}
#返回，b1、b2、b3

```

#### 布尔操作符

`注意必须使用大写`

```bash
AND or &&
OR or ||
NOT or !
+ #搜索结果中必须包含此项,作用于AND的差不多，但是支持单独使用
- #不能含有此项,效果类似NOT
# +hello -world test：结果中必须存在hello，不能有world，test可有可无
```
#### 分组

`支持使用小括号对每个子句进行分组，形成更为复杂的查询逻辑。`

```bash
hello AND (tom OR jerry) #要搜索包含hello的文档中，也包含tom或者jerry的
```

#### 转义字符

`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \`
上字符当作值搜索的时候需要用`\`转义

