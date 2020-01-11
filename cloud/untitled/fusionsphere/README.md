# FusionSphere

## FusionSphere-概念

###  认识FusionSpherer

* 华为公司面向多行业客户推出的云操作系统产品。 
* 专门为云设计和优化。 
  * 提供强大的虚拟化功能和资源池管理。 
  * 丰富的云基础服务组件和工具。 
  * 开放的API接口等。 
* 水平整合数据中心物理和虚拟资源，垂直优化业务平台，让企业云计算更加简捷。

|   | FusionSpherer |
| :---: | :---: |
| 虚拟基础架构层 | FusionComputer CNA |
| 虚拟资源管理层 | FusionComputer VRM |
| 云资源管理层 | FusionManager/FusionSpherer Openstack |

## FusionSpherer-组成构建及分述 

### FusionSpherer构成部件

![&#x56FE;&#xFF1A;FusionSpherer&#x6784;&#x6210;&#x90E8;&#x4EF6;](../../../.gitbook/assets/image%20%282%29.png)

### FusionComputer计算设备虚拟化：

FusionComputer是建立虚拟化环境用到的必选功能模块。

FusionComputer包含模块及模块间的示意图：

![&#x56FE;&#xFF1A;FusionComputer&#x6A21;&#x5757;&#x5173;&#x7CFB;](../../../.gitbook/assets/image%20%284%29.png)

### FusionStorage

* 华为FusionStorage是一款软件定义分布式块存储软件。 
* FusionStorage可以为FusionSphere,VMware和物理数据库环境提供高扩展，高性能，高可靠的块存储服务。可以独立购买和使用，对于构建FusionSphere环境，其是可选功能模块 。 
* FusionStorage包含模块及模块间关系示意 

![&#x56FE;&#xFF1A;FusionStorage&#x6A21;&#x5757;&#x5173;&#x7CFB;](../../../.gitbook/assets/image%20%285%29.png)

### FusionNetwork

1. 华为软件定义网络功能模块。
2. FusionNetwork是建立/使用高级网络功能， 灵活配置管理网络功能的组件。对于构建FusionSphere环境， 其是可选功能模块
3. Fusion Network包含模块及模块间关系示意:

![&#x56FE;&#xFF1A;FusionNetwork&#x6A21;&#x5757;&#x5173;&#x7CFB;](../../../.gitbook/assets/image.png)

### OpenStack模型： <a id="OpenStack&#x6A21;&#x578B;&#xFF1A;"></a>

1. OpenStack模块是实现虚拟化环境的统一模型。
2. OpenStack被引入FusionSphere，实现异构虚拟化环境的同一资源抽象，管理和分配。
3. OpenStack插件化扩展示意图：

![OpenStack](https://cshihong.github.io/2018/03/04/FusionSphere%E6%95%B4%E4%BD%93%E4%BB%8B%E7%BB%8D/OpenStack.png)

图:OpenStack插件化示意图

## FusionSphere-兼容性，规格参数 <a id="FusionSphere-&#x517C;&#x5BB9;&#x6027;&#xFF0C;&#x89C4;&#x683C;&#x53C2;&#x6570;"></a>

### 硬件兼容性包括： <a id="&#x786C;&#x4EF6;&#x517C;&#x5BB9;&#x6027;&#x5305;&#x62EC;&#xFF1A;"></a>

1. 服务器
2. 存储设备
3. 网络设备
4. I/O设备，如网卡，RAID卡，HBA卡，GPU等。
5. Guest OS，Linux，Windows。

### 软件兼容性主要包括： <a id="&#x8F6F;&#x4EF6;&#x517C;&#x5BB9;&#x6027;&#x4E3B;&#x8981;&#x5305;&#x62EC;&#xFF1A;"></a>

1. FusionSphere应用兼容性列表：

   如数据库软件，中间件，Web服务器软件，Email，HA，备份容灾，安全类软件，企业应用软件等。

2. FusionSpherer OpenStack应用兼容性类别：

   如数据库软件，Web服务器软件，文件服务软件，DNS软件，ICT应用软件等。

### FusionSpherer规格参数： <a id="FusionSpherer&#x89C4;&#x683C;&#x53C2;&#x6570;&#xFF1A;"></a>

![&#x89C4;&#x683C;&#x53C2;&#x6570;](https://cshihong.github.io/2018/03/04/FusionSphere%E6%95%B4%E4%BD%93%E4%BB%8B%E7%BB%8D/%E8%A7%84%E6%A0%BC%E5%8F%82%E6%95%B0.png)

## FusionSpherer-三个主要场景及典型部署形式 <a id="FusionSpherer-&#x4E09;&#x4E2A;&#x4E3B;&#x8981;&#x573A;&#x666F;&#x53CA;&#x5178;&#x578B;&#x90E8;&#x7F72;&#x5F62;&#x5F0F;"></a>

### 服务器虚拟化场景： <a id="&#x670D;&#x52A1;&#x5668;&#x865A;&#x62DF;&#x5316;&#x573A;&#x666F;&#xFF1A;"></a>

![&#x670D;&#x52A1;&#x5668;](https://cshihong.github.io/2018/03/04/FusionSphere%E6%95%B4%E4%BD%93%E4%BB%8B%E7%BB%8D/%E6%9C%8D%E5%8A%A1%E5%99%A8.png)

图：服务器虚拟化场景

![&#x573A;&#x666F;1](https://cshihong.github.io/2018/03/04/FusionSphere%E6%95%B4%E4%BD%93%E4%BB%8B%E7%BB%8D/%E5%9C%BA%E6%99%AF1.png)

：图：服务器虚拟化典型部署

图：FusionSpherer服务器虚拟化场景典型部署

1. Local FusionManager定位于本地数据中心虚拟化环境接入管理等配置。
2. FusionManager ServiceCenter定位千全局资源管理分配，租户portal和自服务等功能。
3. ManageOne数据中心统一管理软件包含ManageOne ServiceCenter运营中心和ManageOne OperationCenter运维中心。
4. ManageOne ServiceCenter负责企业资源统一运营管理。
5. ManageOne OperationCenter负责企业资源统一运维管理。

### 云数据中心场景： <a id="&#x4E91;&#x6570;&#x636E;&#x4E2D;&#x5FC3;&#x573A;&#x666F;&#xFF1A;"></a>

![&#x4E91;&#x6570;&#x636E;](https://cshihong.github.io/2018/03/04/FusionSphere%E6%95%B4%E4%BD%93%E4%BB%8B%E7%BB%8D/%E4%BA%91%E6%95%B0%E6%8D%AE.png)

图：云数据中心场景

![&#x4E91;&#x6570;&#x636E;&#x4E2D;&#x5FC3;](https://cshihong.github.io/2018/03/04/FusionSphere%E6%95%B4%E4%BD%93%E4%BB%8B%E7%BB%8D/%E4%BA%91%E6%95%B0%E6%8D%AE%E4%B8%AD%E5%BF%83.png)

图：云数据中心典型部署场景

![&#x4E91;&#x6570;](https://cshihong.github.io/2018/03/04/FusionSphere%E6%95%B4%E4%BD%93%E4%BB%8B%E7%BB%8D/%E4%BA%91%E6%95%B0.png)

### 图：云数据中心典型部署场景 <a id="&#x56FE;&#xFF1A;&#x4E91;&#x6570;&#x636E;&#x4E2D;&#x5FC3;&#x5178;&#x578B;&#x90E8;&#x7F72;&#x573A;&#x666F;"></a>

### NFV场景： <a id="NFV&#x573A;&#x666F;&#xFF1A;"></a>

![NFV](https://cshihong.github.io/2018/03/04/FusionSphere%E6%95%B4%E4%BD%93%E4%BB%8B%E7%BB%8D/NFV.png)

### 图：FusionSphere NFV场景 <a id="&#x56FE;&#xFF1A;FusionSphere-NFV&#x573A;&#x666F;"></a>

## FusionSphere-灾备方案 <a id="FusionSphere-&#x707E;&#x5907;&#x65B9;&#x6848;"></a>

### 华为数据保护—-eBackup产品： <a id="&#x534E;&#x4E3A;&#x6570;&#x636E;&#x4FDD;&#x62A4;&#x2014;-eBackup&#x4EA7;&#x54C1;&#xFF1A;"></a>

![&#x6570;&#x636E;&#x4FDD;&#x62A4;](https://cshihong.github.io/2018/03/04/FusionSphere%E6%95%B4%E4%BD%93%E4%BB%8B%E7%BB%8D/%E6%95%B0%E6%8D%AE%E4%BF%9D%E6%8A%A4.png)

图：华为数据保护

### 华为业务连续性容灾容BCManager产品: <a id="&#x534E;&#x4E3A;&#x4E1A;&#x52A1;&#x8FDE;&#x7EED;&#x6027;&#x5BB9;&#x707E;&#x5BB9;BCManager&#x4EA7;&#x54C1;"></a>

![&#x4E1A;&#x52A1;](https://cshihong.github.io/2018/03/04/FusionSphere%E6%95%B4%E4%BD%93%E4%BB%8B%E7%BB%8D/%E4%B8%9A%E5%8A%A1.png)

### 图：华为业务连续性容灾容BCManager产品  <a id="&#x56FE;&#xFF1A;&#x534E;&#x4E3A;&#x4E1A;&#x52A1;&#x8FDE;&#x7EED;&#x6027;&#x5BB9;&#x707E;&#x5BB9;BCManager&#x4EA7;&#x54C1;"></a>



