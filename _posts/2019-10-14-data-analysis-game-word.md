---
layout:     post
title:      "数据分析学习（四）"
subtitle:   " \"游戏运营必备的指标\""
date:       2019-10-14 07:00:00
author:     "Jht"
header-img: "img/post-bg-data-analysis.jpg"
catalog: true
tags:
    - analysis
---

## 游戏运营的数据分析指标

以下指标以TalkingData AARRR模型为基础，结合移动游戏的行业特点，给出了移动游戏运营者在业务运营各阶段应当关注的关键数据指标。

### AARRR模型

AARRR是Acquisition、Activation、Retention、Revenue、Refer。

- 获取用户（Acquisition）
- 提高活跃度（Activation）
- 提高留存率（Retention）
- 获取收入（Revenue）
- 自传播（Refer）
  - 是基于社交网络的病毒式传播，这已经成为获取用户的一个新途径。这个方式的成本低，效果有可能非常好；唯一的前提是产品自身要足够好，有很好的口碑。

AARRR模型指出了移动游戏运营两个核心点：

- 以用户为中心，以完整的用户生命周期为线索
- 把控产品整体的成本/收入关系，用户生命周期价值(LTV)远大于用户获取成本(CAC)就意味着产品运营的成功
     
### 用户获取-Acquisition

这个阶段是业务的投入期。运营者通过各种推广渠道(Channel)，以各种方式获取目标用户。

这个阶段数据分析最重要的就是通过组合各种维度(如时间、地域、渠道)对各种营销渠道的效果进行评估，从而更加优化合理的确定投入策略，最小化用户获取成本(CAC)

关键数据:

- 1．用户数量(以时间、地域、版本、推广渠道等不同维度来拆解分析新增、总数及增长率，组合各种维度来分析各种营销渠道的用户获取效果以及目标用户分布)：
  - 点击用户数(Click)
  - 安装用户数(Install)
  - 注册用户数(Sign-Up)
  - 在线用户数(Login)：最高在线(PCU)、平均在线(ACU)、日活跃(DAU)、周活跃(WAU)、月活跃(MAU)
  - 有效用户数：不同类型产品会有不同的定义(可能是注册用户或者登录用户或者付费用户)
- 2．渠道转化率：点击->安装->注册->登录的转化比率(分渠道)
- 3.1 自然增长用户Organic Users
  - 非推广手段获得的用户，如果此数据增长率相对Marketing Users的增长率很高，或者说明产品已经进入成熟稳定期，或者说明营销推广需要加强了。
- 3.2 推广获得用户 Marketing Users：推广渠道获得的用户，含有渠道标签，用于宏观的评价渠道推广效果。
- 4．虚假用户数(One Session/Day User)：顾名思义，一次会话用户。主要用于监控渠道刷量作弊。同时也可反映目标用户的使用习惯，判断渠道获取的用户是否有效，从而评价渠道推广质量
- 5．渠道增长率：评价渠道长期运转健康度
- 6．渠道份额：渠道对比
- 7．CAC(Consumer Acquisition Cost)

#### 相关名词解释

- 平均同时在线人数（ACU: Average Concurrent Users）
  - 即在一定时间段抓取一次数据，以一定周期为期限；周期内的ACU可取时间段的平均数据。[例如：系统每一小时抓取一次数据，全天24小时共24个不同时刻的在线数据，则每天的ACU是这24个数据的平均值（每个公司有每个公司的定义，一般ACU取平均值，若针对某一时刻，则直接在某时刻内直接统计用户数）
- 最高同时在线人数（PCU:Peak concurrent users）
  - 即在一定时间内，抓取最高在线数据。（例如：单天最高在线：系统每小时统计一次数据，全天24小时共24个不同时刻的在线数据，则24个时间段内最高的用户在线数据为PCU）作者：黑喵爱吃棉花糖
- 新用户转化率(New Users Conversion Rate)
  - Clicks → Install → Register → Login(可根据渠道进行划分)
  
#### 日新登用户数(Daily New Users)

##### 指标定义

每日注册并登录游戏的用户数。

##### 反应问题

 - 渠道贡献的新用户份额情况
 - 宏观走势，是否需要进行投放
 - 是否存在渠道作弊行为。 

周新登用户数为本周 7 天日新登用户数累计之和，月新登用户数计算同上
根据需要，可细分为自然增长用户 [ 非推广期 ] 和推广用户 [ 推广期 ]

#### 日一次会话用户数(Daily One Session Users)

##### 指标定义

一次会话用户，即新登用户中只 有一次会话，且会话时长低于规定阈值。

##### 反应问题

- 推广渠道是否有刷量作弊行为
- 渠道推广质量是否合格
- 用户导入是否存在障碍点,如：网络状况、加载时间等。

周一次会话用户数为本周 7 天日一次会话用户数累计之和
月一次会话用户数计算同上
游戏引导设计分析点之一
DOSU 有助于评估新登用户质量，进一步分析则需要定义活跃用户的月一次 会话用户数。

#### 用户获取成本(Customer Acquisition Cost)

##### 指标定义

CAC=Promotion Cost ÷ Valid New Users	

- Promotion Cost: 推广成本
- Valid	New	Users: 有效新登用户

##### 反应问题

- 获取有效新登用户的成本是多少
- 如何选择正确的渠道优化投放
- 渠道推广成本是多少。

CAC计算要根据渠道来进行细分
CAC	By Channel = Promotion Cost ÷ Valid	Channel	New	Users。


### 用户活跃度与用户留存(Activation and Retention)

传统较粗犷的数据运营通常只会关注到用户数量这个层次，而实际上除了关注用户数量之外，用户的质量对于运营者来讲其实更为关键。AARRR模型为我们指出了一条精细化数据运营的定律，就是LTV(用户生命周期价值) > >CAC。也就是说，在投入成本获取用户后需要着重的关注和提升用户在整个生命周期中所创造的实际收入价值，从而确保获得最大的ROI。

移动游戏的用户生命周期运营可以归纳为如下的这个转化过程：

获得用户(下载安装) -> 转化成活跃用户(登录使用) ->留住用户(回访留存)->转化成付费用户(应用内支付) 。

#### 用户活跃(Activation)

用户活跃是用户价值转化过程最开始的一步。

#####  活跃用户

###### 指标定义

- 活跃用户：一段时间内启动/登录过移动游戏的用户
  - 每日活跃用户数量(DAU)
  - 每周活跃用户数量(WAU)
  - 每月活跃用户数量(MAU)
- 活跃用户比例：一段时间内活跃用户数量/一段时间内累计用户数量
  - 日活跃率
  - 周活跃率
  - 月活跃率
- 一次性用户(One-Day User)
  - 根据当前时间，自新增以来再没有使用过应用的用户。只有新增时的一次启动/登录，之后再无启动/登录。
- 一次性用户比例
  - 一次性用户数/累计用户数。

###### 反应问题

游戏用户质量。活跃用户的绝对数量低，或相对总用户数量比例低，说明用户的质量不高，应结合渠道等维度深入分析是否目标用户群是否准确或者深入分析产品使用是否存在问题。反之并不能绝对说明用户质量高，产品使用不存在问题，还应当结合其它指标深入分析判断。

一次性用户。虽然从定义上这部分用户也属于活跃用户，但应当格外给予关注。绝大部分一次性用户都是无效的量，不能创造任何价值。比如渠道的刷量作弊会带来大量一次性用户。在观测活跃用户数量的同时，请同时注重观测此指标，以客观评价分群体(如渠道)的用户质量。对于移动游戏来讲，健康的一次性用户比例应当不大于15%

产品状况：活跃度可以有效的反映用户首次游戏体验情况。游戏的界面效果、启动加载时间、交互操作体验、用户引导等因素都将对用户的活跃度带来直接影响。

###### 健康表现

成熟、健康的游戏运营的MAU从长期的发展趋势来看，应当呈现出稳定的趋势曲线(图)

![](/img/in-post/analysis/game-word-1.jpg)

以下指标着重反应的是活跃用户的参与使用情况， 也是游戏产品质量的有效体现。 在做用户活跃度分析的时候可以综合各个指标进行分析，从而发现产品运营中的问题，指导产品优化。

##### 启动次数

又叫参与次数(Engagement Count)

###### 指标定义

用户对移动游戏的一次使用记为一次启动。启动次数就是用户对游戏的启动总量。可以按不同时间区间进行统计。做数据追踪统计时，一般建议30秒内重复开启记录为一次完整使用，不单独计量。
- 日启动次数(DEC)
- 周启动次数(WEC)
- 月启动次数(MEC)
- 日平均启动次数：该日平均每用户启动应用次数。 日启动次数/日启动用户数

###### 反应问题

启动次数反应游戏的用户使用频率。可以作为游戏产品质量的一个指标。

- 衡量用户粘性 [ 日平均参与次数 ]
- 什么渠道，什么用户参与频率较高
- 用户对产品参与频率是什么样的。

###### 健康表现

不同类型的移动游戏会有不同级别的启动次数量级。该指标应当结合用户分布维度来看，主要用户应当分布在较高的启动次数上。(图)

![](/img/in-post/analysis/game-word-2.jpg)

##### 使用时长

###### 指标定义

- 平均单次使用时长
  - 一定时间内，用户平均每次游戏使用的多长时间 = 时间内用户总使用时长/启动次数
- 平均日使用时长
  - 日总在线时长/日活跃用户数 
  - 粗略计算公式 AT=ACU*24/DAU

###### 反应问题

- 用户的游戏参与怎么样
- 产品质量把控指标
- 渠道质量如何
- 与单次使用时长结合分析留存和流失问题
- 用户持续游戏能力如何

帮助分析作弊行为，版本粘性和效果
根据需要，可以观察用户每周，双周，月的平均使用时长情况，了解游戏的粘性。

###### 健康表现

不同类型的移动游戏会有不同级别的使用时长量级。 好的游戏应当有更长的使用时长。该指标应当结合用户分布维度来看，主要用户应当分布在较高的使用时长上。如果存在大量短使用时长用户存在，排除产品主要因素之外说明目标用户群体存在问题， 可能存在如渠道作弊等异常情况。该指标可作为监控渠道用户获取质量的一个指标。

![](/img/in-post/analysis/game-word-3.jpg)


##### DAU/MAU

###### 指标定义

当日的日活跃用户数与30日活跃用户数的比值 

###### 反应问题

DAU/MAU是社交游戏类和在线类应用常用的一项评估指标，被用来分析用户粘度。比值越趋近于1表明用户活跃度越高，在比值低于0.2时，应用的传播性和互动性将会很弱。行业中也常用DAU/MAU乘以30来计算每月用户平均活跃天数。

###### 健康表现

好的游戏会有更高的DAU/MAU比值。通常健康的Freemium游戏 DAU/MAU不低于0.15, 并且长期趋势呈现平稳的曲线。如果长期趋势曲线出现急剧增减，就要结合其它指标综合分析问题原因了。

![](/img/in-post/analysis/game-word-4.jpg)


#### 用户留存Retention

用户的留存(Retention)可以告诉您用户对游戏的忠诚度有多高。简单的讲，就是留住活跃的用户。用户留存是用户最终向付费转化，创造实际收入价值的过程中最关键的阶段。

##### 指标定义

用户在某段时间内开始使用游戏，经过一段时间后，仍然继续使用游戏的被认作是留存用户；这部分用户占当时新增用户的比例即是留存率。

- 日留存(1Day Retention)
- 三日留存(3Day Retention)
- 周留存(7Day Retention)
- 月留存(30Day Retention)

##### 反应问题

- 用户对于游戏的适应性如何
- 评估渠道用户质量
- 投放渠道效果评估
- 用户对于游戏的粘性如何
- 新登用户什么时期流失会加剧。

在行业中，很多应用都很重视首日留存率(1Day Retention)这项指标，这是对应用质量的直接反映，这项指标还可以在一定程度上说明用户首次体验的满意度。
关注留存率的同时需要关注用户流失节点

##### 健康表现

用户的留存在推广渠道，产品版本既定的情况下应当呈现一定的发展趋势。一般来讲用户留存会呈现如下的发展趋势曲线：

![](/img/in-post/analysis/game-word-5.jpg)

通常，用户随时间推移的留存会明显的出现三个时期，如上图：

- 流失期——用户新进入后的前几天是流失量最大的时期，留存率显著下降，是流失期。其中第一天的留存率被称为“首日留存率”。
- 蒸馏期——在经过几天大幅度流失后，用户留存会进入小幅度下降时期，这就如同是蒸馏过程，是蒸馏期。
- 稳定期——经过一段时间蒸馏后，用户留存会呈现出一种很稳定的态势，不会有明显的增减，可称为稳定期，这段时间会保持较长时间。

#### 用户流失Churn

统计时间区间内，用户在不同时期离开游戏的情况。

##### 指标定义

- 日流失率 (1Day Churn)
  - 统计日登录游戏，但随后七日未登录游戏的用户占统计日活跃用户比例，此定义按需求可延长观测长度
- 周流失率 (7Day Churn)
  - 上周登录过游戏，但本周未登录游戏的用户占上周周活跃用户比例
- 月流失率 (30Day Churn)
  - 上个月登录过游戏，但本月未登录过游戏的用户占上个月月活跃用户比例

##### 反应问题

- 活跃用户的生命周期是多少
- 哪一个渠道的流失率比较高
- 拉动收入的运营手段
- 版本更新对于用户的流失影响是多大
- 什么时期的流失率比较高

流失率+留存率≠100%
日流失率的定义可以根据需求进行调整，比如统计当日登录游戏，但随后14日或者30日未登录游戏的用户数
流失率在游戏进入稳定期是值得关注的，稳定期的活跃和收入都比较理想，如果流失率波动较大，就需要引起警惕。需要仔细关注是哪一部分用户离开了游戏，流失率作为一个风向标，具有预警作用。

#### 用户生命周期

用户的生命周期是指用户从开始使用一款游戏应用到卸载应用的整个过程，因为移动应用很难捕捉用户的卸载动作，通常会根据用户的使用频率低于某个极限值来判断用户流失。

LTV(Lifetime Value)就是一个用户在生命周期内创造的价值总和。对于移动游戏来讲就是一个用户在生命周期中创造的收入综合。 

### 收入(Revenue)

移动游戏用户创造的价值最终将体现为游戏运营收入(Revenue)。衡量游戏用户创造价值的关键指标概念：用户生命周期价值(LTV, Lifetime value)

目前移动游戏主要通过以下三种模式创造收入：

- 付费下载
- 应用内广告
- 应用内付费

应用内付费(IAP)目前已经成为未来移动游戏盈利模式的主要发展趋势，越来越多的游戏采用F2P(Free to play)+ IAP的盈利模式。以下的指标也主要是针对应用内付费模式的移动游戏。

#### ARPPU

ARPPU, Average Revenue per Paying User, 即平均每付费用户收入。一般以月为单位计算，计算方法如下： 月游戏总收入/月付费用户数。

ARPPU反映的是平均每个付费玩家的付费额度。对于F2P的游戏来讲，大多数玩家是不花钱的，ARPPU计算的是那部分花钱用户的情况。


对于付费用户来讲付费额度分布也不平均，一般少量大额付费用户(whales, 鲸鱼用户)带来的收入会占整体付费收入的绝大部分。因此，在做收入分析时应着重对这部分用户的收入变化做重点的分析，并根据实际情况采取相应的行动策略(如增强VIP客服等)。

![](/img/in-post/analysis/game-word-6.jpg)

#### ARPU

ARPU, Average Revenue per User, 即平均每用户(活跃用户)收入。一般以月为单位计算，计算方法如下： 月游戏总收入/月活跃用户数。

ARPU反映的是总体收入在整体用户中均摊的情况，通常该值会远小于ARPPU。ARPU可以用来评估各个用户获取渠道的质量。


#### 付费转化率(Conversion Rate)

##### 指标定义

付费用户占整体活跃用户的比例。一般以月为单位计算。

计算方法如下：月付费用户数/月活跃用户数

##### 反映问题

游戏产品引导玩家付费的能力如何？

玩家的付费倾向和意愿如何？

收入、ARPPU、付费转化率之间存在如下的关系：Revenue = ARPPU * MAU * 付费转化率

运营者应当通过监测付费转化率，结合其它产品运营指标因素(如游戏应用事件转化情况等)以制定提升收入的策略。

##### 健康表现

付费转化率不能直接反映收入的变化情况。付费转化率低并不一定意味着付费用户的减少，有可能是某一时期(如推广活动后)有大量新用户进入游戏造成，还应结合首次付费时间等其它指标因素综合考量对收入变化的影响。相反，付费转化率变高也不一定就意味着用户付费额的增加。

不同类型的游戏的付费转化率水平也有所不同。例如对于社交游戏来讲，付费转化率会因类型的不同在1%-5%范围内变化。 MMO等硬核游戏的付费转化率则依赖于传播途径以及地域等因素也会有所不同(10%-50%)。


#### APA

Active Payment Account

##### 指标定义

统计时间区间内，成功付费的用户数。一般以月计。 

##### 反映问题

- 游戏产品的付费用户规模如何
- APA如何构成？如鲸鱼用户、海豚用户、小鱼用户的比例
- 付费用户的整体稳定性如何。

APA包含历史付费用户在统计时间区间内再次付费的用户以及在统计时间区间内新转化为付费的用户
APA根据需求可细分为充值活跃用户和消费活跃用户
APA的计算是去重的。

#### 月付费率Monthly Payment Ratio

##### 指标定义

统计时间区间内，付费用占活跃用户的比例。一般以月计。
计算公式：MPR=APA/MAU

##### 反映问题

- 游戏产品的付费引导是否合理
- 用户付费倾向与意愿（需结合首次付费功能、道具、等级，整体分析）
- 付费转化是否达到预期效果。

MPR包含历史付费用户在统计时间区间内再次付费的用户以及在统计时间区间内新转化为付费的用户
MPR的高低并不一定代表游戏付费用户的增加或者减少
游戏类型的不同，相应的MPR表现也是不同的

#### 用户生命周期价值(LTV)

用户的生命周期是指一个用户从第一次启动游戏应用，到最后一次启动游戏应用之间的时间。LTV就是某个用户在生命周期内为该游戏应用创造的收入总计，可以看成是一个长期累计的ARPU值。

每个用户平均的LTV = 每月ARPU * 用户按月计的平均生命周期。

比如，如果游戏的ARPU = $0.5, 游戏用户平均生命周期为3个月， 那么LTV = $0.5 * 3 = $1.5

LTV帮助运营者了解平均玩家会在游戏里呆多久，他们会花多少钱。结合之前提过的用户获取成本(CAC)， LTV – CAC的差值，就可以视为该游戏应用从每个用户身上获取的利润。所以最大化利润，就变成如何在降低CAC的同时，提高LTV，使得这两者之间的差值最大化。结合分群(segmentation)，断代(cohort)等分析方法，可以针对特定的群体或渠道计算LTV和CAC，从而评估特定特定群体和渠道的利润。

### 自传播Refer

#### K因子K-Factor

K-Factor= 感染率 × 转化率

转化率：当感染后转化为新用户的比率。
感染率：每个用户发送的邀请数量，一般取平均值
若 K>1，游戏用户群通过自传播增长较快
若 K<1，游戏用户群到达一定规模后就会停止通过自传播增长。


## 参考资料

[移动游戏运营必备的数据分析指标](http://www.woshipm.com/operate/98725.html)

[移动游戏运营数据分析指标白皮书](http://mi.talkingdata.com/report-detail.html?id=374)