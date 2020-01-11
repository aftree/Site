# 大数据概述

## 大数据概述 <a id="&#x5927;&#x6570;&#x636E;&#x6982;&#x8FF0;"></a>

### 大数据产生的背景： <a id="&#x5927;&#x6570;&#x636E;&#x4EA7;&#x751F;&#x7684;&#x80CC;&#x666F;&#xFF1A;"></a>

**基于海量的存储与处理面临挑战：**

1. 数据量大，数据种类多。
2. 海量数据的高存储成本，大数据两下数据处理性能不足，流式数据处理缺失。
3. 优先的扩展能力，单一数据源。
4. 数据资产对外增值。

**行业技术标准的日益形成：**

1. 数据处理技术分布式演进趋势：Hadoop成为开放的事实标准。
2. 各种技术特点：
   1. SMP：单机、Scale UP。性能存在瓶颈，扩展性差。
   2. SMP+MPP混合：集群、Share Everything。结构化、关系型。Flash cache+分布式块存储+IB。
   3. MPP：集群、Share Nothing，结构化，关系型，通用的硬件。
   4. Hadoop：集群，Share Nothing，开放、全球生态、结构化、半结构化、非结构化，高性能，实施。

### 大数据的前世今生： <a id="&#x5927;&#x6570;&#x636E;&#x7684;&#x524D;&#x4E16;&#x4ECA;&#x751F;&#xFF1A;"></a>

1. 大数据的提出：

   1996年，SGI首次提出大数据。

2. 描述大数据：

   2001年，Gartner在评论中首先定义大数据的三个维度：数据容量、速度和种类。

3. 大数据的实现：

   业界把3V扩展到了11V，但主要包括Volume、Velocity、Variety、Value等。

### 大数据定义： <a id="&#x5927;&#x6570;&#x636E;&#x5B9A;&#x4E49;&#xFF1A;"></a>

**大数据：指无法在可承受的时间内用软硬件进行捕捉、管理和处理的数据集合，需要新处理模式才能使数据集合称为具有更强的决策力、洞察力和流程优化等能力的海量、多样化的信息资产。**

### 数据类型： <a id="&#x6570;&#x636E;&#x7C7B;&#x578B;&#xFF1A;"></a>

1. 结构化数据：

   是指可以存储在数据库里，可以用二维表结果来逻辑表达实现的数据。

2. 非结构化数据：

   不方便用二维表结果来逻辑表来表现的数据。包括所有格式的办公文档、文本、图片、XML、HTML、各类报表、图像、音频、视频信息等等。

3. 半结构化数据：

   介于结构化数据和非结构化数据之间的数据。HTML文档就属于半结构数据。

分析当代的数据集合，由25%的结构化数据和75%的非结构化和半结构数据构成。

### 大数据的价值： <a id="&#x5927;&#x6570;&#x636E;&#x7684;&#x4EF7;&#x503C;&#xFF1A;"></a>

* 在卫星测绘领域：

  大数据具备**海量数据存储**服务能力，每天能存储1TB的数据，整个系统可以存储PB级别的数据。

* 在金融领域：

  大数据可以帮助金融机构盘活客户少量数据资产，**深挖存量数据价值。**

* 在能源勘测领域：

  大数据工具能有效降低能源公司的勘测成本，通过大数据分析，每口油井的勘探成本从800万美金降到300万美金。

* 在媒体娱乐领域：也有很多重要应用。
* 对应企业：在竞争能力、决策及时、成本控制有广泛的应用前景。
* 对于事业组织：在科学探索、知识服务、社会安全领域有强烈需求。

### 大数据的特征（4V）： <a id="&#x5927;&#x6570;&#x636E;&#x7684;&#x7279;&#x5F81;&#xFF08;4V&#xFF09;&#xFF1A;"></a>

1. 量大（Volume）：存储大，计算量大；
2. 样多（Variety）：来源多，格式多；
3. 快速（Velocity）: 生成速度快，处理速度要求快。
4. 价值（Value）：价值密度低，和数据总量的大小成反比。

### 大数据带来的挑战： <a id="&#x5927;&#x6570;&#x636E;&#x5E26;&#x6765;&#x7684;&#x6311;&#x6218;&#xFF1A;"></a>

1. **传统网络架构不适用大数据时代。**

   **从垂直访问到水平访问。**

   传统网络架构对南北向的网络流量需求支持良好，但不适应大数据映月宫对东西流量的需求。为了满足对东西流量的传输需求，要对传统网络架构进行重构。

2. **数据中心将面临巨大挑战。**

   **同时访问子系统压力大。**

   传统数据中心，计算、存储等各个子系统相对独立。用于大数据处理的数据中心，需要更高的资源利用率、自动化，需要使用虚拟化、云计算等技术对这些子系统进行整合和拉通。在重构过程中，增加了技术上的复杂性，给规划、建设、运维带来的压力和新的挑战。

3. **数据仓库架构不适用高速反应的要求。**

   **非结构化数据无法处理。**

   传统数据仓库对各类结构化关系型数据库支持良好，但不适应非结构化数据和半结构数据在数据处理上的需求。无法高效、迅速处理非结构化和半结构化的数据。同时，在存储非结构和半结构化数据的原始数据上，数据仓库也面临挑战。

### 大数据与云计算之间的关系： <a id="&#x5927;&#x6570;&#x636E;&#x4E0E;&#x4E91;&#x8BA1;&#x7B97;&#x4E4B;&#x95F4;&#x7684;&#x5173;&#x7CFB;&#xFF1A;"></a>

* 大数据是需求，云计算是解决之道。
* 云计算是平台，大数据是应用。

云计算之于大数据，云计算是底层平台，大数据是应用。云计算作为底层平台整合计算、存储和网络等资源，同时提供基础脚骨资源弹性伸缩的能力。大数据在云计算平台的支撑下，调度下层资源，进行数据源加载，计算和最终结果输出等动作。

### 如何面对大数据： <a id="&#x5982;&#x4F55;&#x9762;&#x5BF9;&#x5927;&#x6570;&#x636E;&#xFF1A;"></a>

从传统的被动应对业务，到主动挖掘价值。

新的需求：管理方法，技术工具，基础架构，思维方式等。

## 电信大数据应用 <a id="&#x7535;&#x4FE1;&#x5927;&#x6570;&#x636E;&#x5E94;&#x7528;"></a>

### 大数据带给电信行业的机会与挑战： <a id="&#x5927;&#x6570;&#x636E;&#x5E26;&#x7ED9;&#x7535;&#x4FE1;&#x884C;&#x4E1A;&#x7684;&#x673A;&#x4F1A;&#x4E0E;&#x6311;&#x6218;&#xFF1A;"></a>

1. 挑战一：

   电信行业生态圈的信息产业遇到了革命性的变化，运营商相关业务的发展更加依赖数据，如传统的语音、窄带、宽带数据以及超宽带，数据经济等相关业务的数据量越来越大。

2. 挑战二：

   是OTT、虚拟运行商的介入，使得运行商竞争环境更加的复杂和激烈。

3. 挑战三：

   是客户消费模式的改变，需要大数据分析深入洞察用户的需求，进行定制化的服务，改善客户体验。

4. 挑战四：

   是提升精细化的管理水平，以数据为中心的运营支撑一体化，精细化成为必然趋势，而数据将成为企业的核心资产。

### 电信行业大数据典型商业需求： <a id="&#x7535;&#x4FE1;&#x884C;&#x4E1A;&#x5927;&#x6570;&#x636E;&#x5178;&#x578B;&#x5546;&#x4E1A;&#x9700;&#x6C42;&#xFF1A;"></a>

大数据的总体目标是构建同一的数据采集与整合能力，大数据分析处理能力，计算及数据服务能力，大数据应用能力，和互联网化的数据开放能力，支撑业务创新与商业成功。

1. 延长用户生命周期

   大数据建模支撑用户生命周期的营销和维系。

2. 提升业务网使用量

   基于大数的营销体系有效运作，支撑多批次，小群体，高成功率，多用户触点的营销。

3. 对外价值变现

   时间对外合作，MR数据轨迹形成商业价值，用户行为轨迹形成商业价值。

### 电信大数据三大场景应用场景： <a id="&#x7535;&#x4FE1;&#x5927;&#x6570;&#x636E;&#x4E09;&#x5927;&#x573A;&#x666F;&#x5E94;&#x7528;&#x573A;&#x666F;&#xFF1A;"></a>

1. 场景一：潜在离网用户维挽场景。

   通过大数据的应用管理，对潜在的离网用户进行数据分析。通过大数据实现用户管理，营销策划，营销实施和闭环反馈的拉通。当海量的大数来临后，用大数平台对所有用户进行分类、识别和管理，如常见的后付费、预付费。用户识别之后，根据用户的大数据分析结构触发营销策略。比如，用户的余额不足，签约到期，体验不好投诉或者用户流量溢出时，对其进行分析。对用户在内部进行聚到选择，匹配响应的资源套餐。通过用户的选择进行效果的反馈，

2. 场景二：综合网管分析平台-基站关联分析场景。

   根据离网用户的位置轨迹，用户的业务行为，基站地图以及基站网络质量KPI获得数据源。然后进行大数据的建模分析，判断离网用户是否与其常出没的基站存在管联，进而输出质差的基站列表，基站供需平衡度。经常出没已识别质差以及基站的未离网用户列表，最后，确定客服务的商用场景。如预付费，后付费维挽场景，网络优化以4G基站选址等。

3. 场景三：数据变现场景：户外数字媒体/非数字媒体价值评估场景。

   例如：先阶段户外媒体行业缺乏受众测量的方法。行业交易混乱，如何去进行户外广告的价值评估？

   可以通过大数据平台去分析人流量，车流量、覆盖率等相关信息，根据所得的信息来进行统一的管理，获得相应的需求描述。得到目标人群的属性，MR，工参，用户行为，RNC信令，地图等相关数据，同时结合户外的LED广告屏，公交站的广告牌，进而整合所有的数据，得出最终的广告资源价值评估，广告投放效果监测。广告投放时段和内容规划以及精准的营销策划。

### 中国电信兴业大数据应用方向: <a id="&#x4E2D;&#x56FD;&#x7535;&#x4FE1;&#x5174;&#x4E1A;&#x5927;&#x6570;&#x636E;&#x5E94;&#x7528;&#x65B9;&#x5411;"></a>

数字与数字化服务业务。

支撑自由业务提升，支撑非通信价值变现。进而实运行时的业务数字化。
