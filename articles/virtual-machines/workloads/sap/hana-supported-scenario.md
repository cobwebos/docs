---
title: Azure 上 SAP HANA 支持的方案（大型实例）*微软文档
description: Azure SAP HANA（大型实例）的支持方案及其体系结构详细信息
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 019f462d4264d19bcc4806d91223029a95f9d819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617177"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>HANA 大型实例的支持方案
本文介绍了 HANA 大型实例 （HLI） 支持的方案和体系结构详细信息。

>[!NOTE]
>如果本文中未提及所需的方案，请与 Microsoft 服务管理团队联系以评估您的要求。
在设置 HLI 单元之前，请与 SAP 或服务实施合作伙伴验证设计。

## <a name="terms-and-definitions"></a>术语和定义
让我们了解本文中使用的术语和定义：

- **SID**：HANA 系统的系统标识符
- **HLI**： 哈纳大实例
- **DR**： 灾难恢复
- **普通 DR**：仅出于 DR 目的使用专用资源的系统设置
- **多用途 DR**： 配置为将非生产环境与为 DR 事件配置的生产实例一起使用的 DR 站点系统 
- **单 SID**：安装一个实例的系统
- **多 SID**：配置多个实例的系统;也称为 MCOS 环境
- **HSR**： SAP HANA 系统复制

## <a name="overview"></a>概述
HANA 大型实例支持各种体系结构，可帮助您满足业务需求。 以下各节介绍体系结构方案及其配置详细信息。 

派生体系结构设计完全从基础结构角度设计，您必须咨询 SAP 或实施合作伙伴进行 HANA 部署。 如果本文未列出您的方案，请与 Microsoft 帐户团队联系，查看体系结构并为您派生解决方案。

> [!NOTE]
> 这些架构完全符合定制数据集成 （TDI） 设计，并由 SAP 提供支持。

本文介绍了每个受支持的体系结构中的两个组件的详细信息：

- 以太网
- 存储

### <a name="ethernet"></a>以太网

每个预配服务器都预配置了以太网接口集。 在每个 HLI 单元上配置的以太网接口分为四种类型：

- **A**： 用于客户端访问或由客户端访问使用。
- **B**： 用于节点到节点的通信。 此接口在所有服务器上配置（无论请求的拓扑如何），但仅用于横向扩展方案。
- **C**： 用于节点到存储连接。
- **D**： 用于用于 STONITH 设置的节点到 iSCSI 设备连接。 仅当请求 HSR 设置时，才配置此接口。  

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点|
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | STONITH |
| A | 第 II 类 | vlan\<租户 无> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan\<租户 No+2> | team0.tenant+2 | 节点到节点|
| C | 第 II 类 | vlan\<租户 No+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<租户 No+3> | team0.tenant+3 | STONITH |

根据在 HLI 单元上配置的拓扑选择接口。 例如，接口"B"为节点到节点通信设置，这在配置横向扩展拓扑时非常有用。 此接口不用于单个节点、放大配置。 有关接口使用情况的详细信息，请查看所需的方案（本文后面）。 

如有必要，您可以自行定义其他 NIC 卡。 但是，*无法*更改现有 NIC 的配置。

>[!NOTE]
>您可能会发现其他接口是物理接口或绑定。 您应该只考虑用例前面提到的接口。 任何其他都可以忽略。

具有两个分配 IP 地址的单位的分布应如下所示：

- 以太网"A"应具有分配给的 IP 地址，该 IP 地址在您提交给 Microsoft 的服务器 IP 池地址范围内。 此 IP 地址应保存在操作系统的 */etc/主机*目录中。

- 以太网"C"应具有用于与 NFS 通信的已分配 IP 地址。 不需要在*等/主机*目录中维护此地址，以允许租户中的实例到实例流量。 *not*

对于 HANA 系统复制或 HANA 横向扩展部署，不适合具有两个分配的 IP 地址的刀片配置。 如果只有两个分配的 IP 地址，并且想要部署此类配置，请与 Azure 服务管理上的 SAP HANA 联系。 他们可以在第三个 VLAN 中为您分配第三个 IP 地址。 对于在三个 NIC 端口上具有三个分配 IP 地址的 HANA 大型实例单元，适用以下使用规则：

- 以太网"A"应具有分配给的 IP 地址，该 IP 地址超出您提交给 Microsoft 的服务器 IP 池地址范围。 不应在操作系统*的等/主机*目录中维护此 IP 地址。

- 以太网"B"应只保存在*等/主机*目录中，以便在各种实例之间进行通信。 这些是在横向扩展的 HANA 配置中维护的 IP 地址，因为 HANA 用于节点间配置的 IP 地址。

- 以太网"C"应具有用于与 NFS 存储通信的已分配 IP 地址。 此类地址不应在*等/主机*目录中保留。

- 以太网"D"应仅用于访问起搏器的 STONITH 设备。 当您配置 HANA 系统复制并且希望使用基于 SBD 的设备实现操作系统的自动故障转移时，需要此接口。


### <a name="storage"></a>存储
存储根据请求的拓扑进行预配置。 卷大小和装载点因服务器数量、SKU 数量和配置的拓扑而异。 有关详细信息，请查看所需的方案（本文后面）。 如果您需要更多存储空间，可以以 1 TB 的增量购买。

>[!NOTE]
>装载点 /usr/sap/\<SID>是指向 /hana/共享装载点的符号链接。


## <a name="supported-scenarios"></a>支持的方案

下一节中的体系结构图使用以下符号：

[![体系结构图表](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

以下是支持的方案：

* 具有一个 SID 的单节点
* 单节点 MCOS
* 带 DR 的单节点（正常）
* 带 DR 的单节点（多用途）
* 具有 STONITH 的 HSR
* 带 DR 的 HSR（普通/多用途） 
* 主机自动故障转移 (1+1) 
* 使用备用节点的横向扩展
* 不使用备用节点的横向扩展
* 使用 DR 的横向扩展

## <a name="single-node-with-one-sid"></a>具有一个 SID 的单节点

此拓扑支持一个使用一个 SID 的扩展配置中的节点。

### <a name="architecture-diagram"></a>体系结构关系图  

![具有一个 SID 的单节点](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<租户 无> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan\<租户 No+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<租户 No+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<租户 No+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
预配置了以下安装点：

| 装入点 | 用例 | 
| --- | --- |
|/hana/shared/SID | HANA 安装 | 
|/hana/data/SID/mnt00001 | 数据文件安装 | 
|/hana/log/SID/mnt00001 | 日志文件安装 | 
|/hana/logbackups/SID | 重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。

## <a name="single-node-mcos"></a>单节点 MCOS

此拓扑支持具有多个 S 的扩展配置中的一个节点。

### <a name="architecture-diagram"></a>体系结构关系图  

![单节点 MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<租户 无> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan\<租户 No+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<租户 No+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<租户 No+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
预配置了以下安装点：

| 装入点 | 用例 | 
| --- | --- |
|/hana/shared/SID1 | 用于 SID1 的 HANA 安装 | 
|/hana/data/SID1/mnt00001 | 为 SID1 安装数据文件 | 
|/hana/log/SID1/mnt00001 | SID1 的日志文件安装 | 
|/hana/logbackups/SID1 | SID1 的重做日志 |
|/hana/shared/SID2 | SID2 的 HANA 安装 | 
|/hana/data/SID2/mnt00001 | SID2 的数据文件安装 | 
|/hana/log/SID2/mnt00001 | SID2 的日志文件安装 | 
|/hana/logbackups/SID2 | SID2 的重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 卷大小分布基于内存中的数据库大小。 要了解多 SID 环境中支持内存中的数据库大小，请参阅[概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。

## <a name="single-node-with-dr-using-storage-replication"></a>使用存储复制的 DR 的单节点
 
此拓扑支持具有一个或多个 SID 的扩展配置中的一个节点，并基于存储复制到主 SID 的 DR 站点。 在图中，主站点只描述了单 SID 系统，但也支持 MCOS 系统。

### <a name="architecture-diagram"></a>体系结构关系图  

![使用存储复制的 DR 的单节点](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<租户 无> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan\<租户 No+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<租户 No+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<租户 No+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
预配置了以下安装点：

| 装入点 | 用例 | 
| --- | --- |
|/hana/shared/SID | 用于 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 为 SID 安装数据文件 | 
|/hana/log/SID/mnt00001 | SID 的日志文件安装 | 
|/hana/logbackups/SID | SID 的重做日志 |


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分布基于内存中的数据库大小。 要了解多 SID 环境中支持内存中的数据库大小，请参阅[概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- 在 DR 站点：为 DR HLI 单元的生产 HANA 实例安装配置了卷和装载点（标记为"HANA 安装需要"。 
- 在 DR 站点：数据、日志备份和共享卷（标记为"存储复制"）通过生产站点的快照进行复制。 这些卷仅在故障转移期间装载。 有关详细信息，请参阅[灾难恢复故障转移过程](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)。
- *SKU 类型 I 类*的启动卷将复制到 DR 节点。


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>使用存储复制的具有 DR（多用途）的单节点
 
此拓扑支持具有一个或多个 SID 的扩展配置中的一个节点，并基于存储复制到主 SID 的 DR 站点。 在图中，主站点只描述了单 SID 系统，但也支持多 SID （MCOS） 系统。 在 DR 站点，当生产操作从主站点运行时，HLI 单元用于 QA 实例。 在 DR 故障转移（或故障转移测试）期间，DR 站点的 QA 实例将被关闭。

### <a name="architecture-diagram"></a>体系结构关系图  

![使用存储复制的具有 DR（多用途）的单节点](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<租户 无> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan\<租户 No+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<租户 No+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<租户 No+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
预配置了以下安装点：

| 装入点 | 用例 | 
| --- | --- |
|**在主站点**|
|/hana/shared/SID | 用于生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 用于生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 用于生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在 DR 站点**|
|/hana/shared/SID | 用于生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 用于生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 用于生产 SID 的日志文件安装 | 
|/hana/shared/QA-SID | QA SID 的 HANA 安装 | 
|/hana/data/QA-SID/mnt00001 | QA SID 的数据文件安装 | 
|/hana/log/QA-SID/mnt00001 | QA SID 的日志文件安装 |
|/hana/logbackups/QA-SID | QA SID 的重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分布基于内存中的数据库大小。 要了解多 SID 环境中支持内存中的数据库大小，请参阅[概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- 在 DR 站点：为 DR HLI 单元的生产 HANA 实例安装配置了卷和装载点（标记为"HANA 安装需要"。 
- 在 DR 站点：数据、日志备份和共享卷（标记为"存储复制"）通过生产站点的快照进行复制。 这些卷仅在故障转移期间装载。 有关详细信息，请参阅[灾难恢复故障转移过程](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)。 
- 在 DR 站点：为 QA 实例安装配置 QA 的数据、日志备份、日志和共享卷（标记为"QA 实例安装"）。
- *SKU 类型 I 类*的启动卷将复制到 DR 节点。

## <a name="hsr-with-stonith-for-high-availability"></a>具有 STONITH 的 HSR，提供高可用性
 
此拓扑支持 HANA 系统复制配置的两个节点。 此配置仅支持节点上的单个 HANA 实例。 这意味着*不支持*MCOS 方案。

> [!NOTE]
> 截至 2019 年 12 月，此体系结构仅支持 SUSE 操作系统。


### <a name="architecture-diagram"></a>体系结构关系图  

![具有 STONITH 的 HSR，提供高可用性](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 用于 STONITH |
| A | 第 II 类 | vlan\<租户 无> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan\<租户 No+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<租户 No+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<租户 No+3> | team0.tenant+3 | 用于 STONITH |

### <a name="storage"></a>存储
预配置了以下安装点：

| 装入点 | 用例 | 
| --- | --- |
|**在主节点上**|
|/hana/shared/SID | 用于生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 用于生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 用于生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在辅助节点上**|
|/hana/shared/SID | 用于辅助 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 为辅助 SID 安装数据文件 | 
|/hana/log/SID/mnt00001 | 为辅助 SID 安装日志文件 | 
|/hana/logbackups/SID | 辅助 SID 的重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分布基于内存中的数据库大小。 要了解多 SID 环境中支持内存中的数据库大小，请参阅[概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- STONITH：为 STONITH 设置配置 SBD。 但是，使用 STONITH 是可选的。


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>具有 HSR 和 DR 的高可用性以及存储复制
 
此拓扑支持 HANA 系统复制配置的两个节点。 支持普通和多用途 DR。 这些配置仅支持节点上的单个 HANA 实例。 这意味着这些配置*不支持*MCOS 方案。

在图中，在 DR 站点中描述了一个多用途方案，其中 HLI 单元用于 QA 实例，而生产操作从主站点运行。 在 DR 故障转移（或故障转移测试）期间，DR 站点的 QA 实例将被关闭。 

### <a name="architecture-diagram"></a>体系结构关系图  

![具有 HSR 和 DR 的高可用性以及存储复制](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 用于 STONITH |
| A | 第 II 类 | vlan\<租户 无> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan\<租户 No+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<租户 No+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<租户 No+3> | team0.tenant+3 | 用于 STONITH |

### <a name="storage"></a>存储
预配置了以下安装点：

| 装入点 | 用例 | 
| --- | --- |
|**在主站点的主节点上**|
|/hana/shared/SID | 用于生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 用于生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 用于生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在主站点的辅助节点上**|
|/hana/shared/SID | 用于辅助 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 为辅助 SID 安装数据文件 | 
|/hana/log/SID/mnt00001 | 为辅助 SID 安装日志文件 | 
|/hana/logbackups/SID | 辅助 SID 的重做日志 |
|**在 DR 站点**|
|/hana/shared/SID | 用于生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 用于生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 用于生产 SID 的日志文件安装 | 
|/hana/shared/QA-SID | QA SID 的 HANA 安装 | 
|/hana/data/QA-SID/mnt00001 | QA SID 的数据文件安装 | 
|/hana/log/QA-SID/mnt00001 | QA SID 的日志文件安装 |
|/hana/logbackups/QA-SID | QA SID 的重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分布基于内存中的数据库大小。 要了解多 SID 环境中支持内存中的数据库大小，请参阅[概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- STONITH：为 STONITH 设置配置 SBD。 但是，使用 STONITH 是可选的。
- 在 DR 站点：主节点和辅助节点复制*需要两组存储卷*。
- 在 DR 站点：为 DR HLI 单元的生产 HANA 实例安装配置了卷和装载点（标记为"HANA 安装需要"。 
- 在 DR 站点：数据、日志备份和共享卷（标记为"存储复制"）通过生产站点的快照进行复制。 这些卷仅在故障转移期间装载。 有关详细信息，请参阅[灾难恢复故障转移过程](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)。 
- 在 DR 站点：为 QA 实例安装配置 QA 的数据、日志备份、日志和共享卷（标记为"QA 实例安装"）。
- *SKU 类型 I 类*的启动卷将复制到 DR 节点。


## <a name="host-auto-failover-11"></a>主机自动故障转移 (1+1)
 
此拓扑支持主机自动故障转移配置中的两个节点。 有一个节点具有主/辅助角色，另一个节点作为备用节点。 *SAP 仅支持此方案，适用于 S/4 HANA。* 有关详细信息，请参阅[OSS 注释 2408419 - SAP S/4HANA - 多节点支持](https://launchpad.support.sap.com/#/notes/2408419)。



### <a name="architecture-diagram"></a>体系结构关系图  

![主机自动故障转移 (1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点通信 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<租户 无> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan\<租户 No+2> | team0.tenant+2 | 节点到节点通信 |
| C | 第 II 类 | vlan\<租户 No+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<租户 No+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
预配置了以下安装点：

| 装入点 | 用例 | 
| --- | --- |
|**在主节点和备用节点上**|
|/hana/shared | 用于生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 用于生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 用于生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |



### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 待机时：为备用单元的 HANA 实例安装配置了卷和装载点（标记为"HANA 安装需要"。
 

## <a name="scale-out-with-standby"></a>使用备用节点的横向扩展
 
此拓扑支持横向扩展配置中的多个节点。 有一个节点具有主角色，一个或多个节点具有辅助角色，一个或多个节点作为备用节点。 但是，在任何单个时间点只能有一个主节点。


### <a name="architecture-diagram"></a>体系结构关系图  

![使用备用节点的横向扩展](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点通信 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<租户 无> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan\<租户 No+2> | team0.tenant+2 | 节点到节点通信 |
| C | 第 II 类 | vlan\<租户 No+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<租户 No+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
预配置了以下安装点：

| 装入点 | 用例 | 
| --- | --- |
|**在主节点、辅助节点和备用节点上**|
|/hana/shared | 用于生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 用于生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 用于生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |


## <a name="scale-out-without-standby"></a>不使用备用节点的横向扩展
 
此拓扑支持横向扩展配置中的多个节点。 有一个节点具有主角色，一个或多个节点具有辅助角色。 但是，在任何单个时间点只能有一个主节点。


### <a name="architecture-diagram"></a>体系结构关系图  

![不使用备用节点的横向扩展](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点通信 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<租户 无> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan\<租户 No+2> | team0.tenant+2 | 节点到节点通信 |
| C | 第 II 类 | vlan\<租户 No+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<租户 No+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
预配置了以下安装点：

| 装入点 | 用例 | 
| --- | --- |
|**在主节点和辅助节点上**|
|/hana/shared | 用于生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 用于生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 用于生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。

## <a name="scale-out-with-dr-using-storage-replication"></a>使用存储复制使用 DR 横向扩展
 
此拓扑支持使用 DR 的横向扩展中的多个节点。 支持普通和多用途 DR。 在该关系图中，仅展示单一目的 DR。 可在有或没有备用节点的情况下请求此拓扑。


### <a name="architecture-diagram"></a>体系结构关系图  

![使用存储复制使用 DR 横向扩展](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点通信 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<租户 无> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan\<租户 No+2> | team0.tenant+2 | 节点到节点通信 |
| C | 第 II 类 | vlan\<租户 No+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<租户 No+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
预配置了以下安装点：

| 装入点 | 用例 | 
| --- | --- |
|**在主节点上**|
|/hana/shared | 用于生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 用于生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 用于生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在 DR 节点上**|
|/hana/shared | 用于生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 用于生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 用于生产 SID 的日志文件安装 | 


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
-  在 DR 站点：为 DR HLI 单元的生产 HANA 实例安装配置了卷和装载点（标记为"HANA 安装需要"。 
- 在 DR 站点：数据、日志备份和共享卷（标记为"存储复制"）通过生产站点的快照进行复制。 这些卷仅在故障转移期间装载。 有关详细信息，请参阅[灾难恢复故障转移过程](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)。 
- *SKU 类型 I 类*的启动卷将复制到 DR 节点。


## <a name="single-node-with-dr-using-hsr"></a>使用 HSR 使用 DR 的单节点
 
此拓扑支持一个使用一个 SID 进行扩展配置的节点，将 HANA 系统复制到主 SID 的 DR 站点。 在图中，主站点只描述了单 SID 系统，但也支持多 SID （MCOS） 系统。

### <a name="architecture-diagram"></a>体系结构关系图  

![使用 HSR 使用 DR 的单节点](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI/HSR |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<租户 无> | team0.tenant | 客户端到 HLI/HSR |
| B | 第 II 类 | vlan\<租户 No+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<租户 No+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<租户 No+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下安装点在 HLI 单元（主和 DR）上进行了预配置：

| 装入点 | 用例 | 
| --- | --- |
|/hana/shared/SID | 用于 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 为 SID 安装数据文件 | 
|/hana/log/SID/mnt00001 | SID 的日志文件安装 | 
|/hana/logbackups/SID | SID 的重做日志 |


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分布基于内存中的数据库大小。 要了解多 SID 环境中支持内存中的数据库大小，请参阅[概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- 主节点使用 HANA 系统复制与 DR 节点同步。 
- [全局覆盖](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用于将 ExpressRoute 电路链接在一起，在区域网络之间建立专用网络。



## <a name="single-node-hsr-to-dr-cost-optimized"></a>单个节点 HSR 到 DR（成本优化） 
 
 此拓扑支持一个使用一个 SID 进行扩展配置的节点，将 HANA 系统复制到主 SID 的 DR 站点。 在图中，主站点只描述了单 SID 系统，但也支持多 SID （MCOS） 系统。 在 DR 站点，当生产操作从主站点运行时，Qa 实例使用 HLI 单元。 在 DR 故障转移（或故障转移测试）期间，DR 站点的 QA 实例将被关闭。

### <a name="architecture-diagram"></a>体系结构关系图  

![单个节点 HSR 到 DR（成本优化）](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI/HSR |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<租户 无> | team0.tenant | 客户端到 HLI/HSR |
| B | 第 II 类 | vlan\<租户 No+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<租户 No+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<租户 No+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
预配置了以下安装点：

| 装入点 | 用例 | 
| --- | --- |
|**在主站点**|
|/hana/shared/SID | 用于生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 用于生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 用于生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在 DR 站点**|
|/hana/shared/SID | 用于生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 用于生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 用于生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|/hana/shared/QA-SID | QA SID 的 HANA 安装 | 
|/hana/data/QA-SID/mnt00001 | QA SID 的数据文件安装 | 
|/hana/log/QA-SID/mnt00001 | QA SID 的日志文件安装 |
|/hana/logbackups/QA-SID | QA SID 的重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分布基于内存中的数据库大小。 要了解多 SID 环境中支持内存中的数据库大小，请参阅[概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- 在 DR 站点：为 DR HLI 单元中的生产 HANA 实例安装配置卷和装载点（标记为"DR 站点上的 PROD 实例"）。 
- 在 DR 站点：为 QA 实例安装配置 QA 的数据、日志备份、日志和共享卷（标记为"QA 实例安装"）。
- 主节点使用 HANA 系统复制与 DR 节点同步。 
- [全局覆盖](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用于将 ExpressRoute 电路链接在一起，在区域网络之间建立专用网络。

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>使用 HSR 提供高可用性和灾难恢复 
 
 此拓扑支持两个节点，用于针对本地区域的高可用性的 HANA 系统复制配置。 对于 DR，DR 区域的第三个节点使用 HSR（异步模式）与主站点同步。 

### <a name="architecture-diagram"></a>体系结构关系图  

![使用 HSR 提供高可用性和灾难恢复](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI/HSR |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<租户 无> | team0.tenant | 客户端到 HLI/HSR |
| B | 第 II 类 | vlan\<租户 No+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<租户 No+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<租户 No+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
预配置了以下安装点：

| 装入点 | 用例 | 
| --- | --- |
|**在主站点**|
|/hana/shared/SID | 用于生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 用于生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 用于生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在 DR 站点**|
|/hana/shared/SID | 用于生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 用于生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 用于生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 在 DR 站点：为 DR HLI 单元中的生产 HANA 实例安装配置卷和装载点（标记为"PROD DR 实例"）。 
- 主站点节点使用 HANA 系统复制与 DR 节点同步。 
- [全局覆盖](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用于将 ExpressRoute 电路链接在一起，在区域网络之间建立专用网络。

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>通过 HSR 的高可用性和灾难恢复（成本优化）
 
 此拓扑支持两个节点，用于用于本地区域的高可用性的 HANA 系统复制配置。 对于 DR，DR 区域的第三个节点使用 HSR（异步模式）与主站点同步，而另一个实例（例如 QA）已经从 DR 节点耗尽。 

### <a name="architecture-diagram"></a>体系结构关系图  

![通过 HSR 的高可用性和灾难恢复（成本优化）](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI/HSR |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<租户 无> | team0.tenant | 客户端到 HLI/HSR |
| B | 第 II 类 | vlan\<租户 No+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<租户 No+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<租户 No+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
预配置了以下安装点：

| 装入点 | 用例 | 
| --- | --- |
|**在主站点**|
|/hana/shared/SID | 用于生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 用于生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 用于生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在 DR 站点**|
|/hana/shared/SID | 用于生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 用于生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 用于生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|/hana/shared/QA-SID | QA SID 的 HANA 安装 | 
|/hana/data/QA-SID/mnt00001 | QA SID 的数据文件安装 | 
|/hana/log/QA-SID/mnt00001 | QA SID 的日志文件安装 |
|/hana/logbackups/QA-SID | QA SID 的重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 在 DR 站点：为 DR HLI 单元中的生产 HANA 实例安装配置卷和装载点（标记为"PROD DR 实例"）。 
- 在 DR 站点：为 QA 实例安装配置 QA 的数据、日志备份、日志和共享卷（标记为"QA 实例安装"）。
- 主站点节点使用 HANA 系统复制与 DR 节点同步。 
- [全局覆盖](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用于将 ExpressRoute 电路链接在一起，在区域网络之间建立专用网络。

## <a name="scale-out-with-dr-using-hsr"></a>使用 HSR 使用 DR 横向扩展
 
此拓扑支持使用 DR 的横向扩展中的多个节点。 可在有或没有备用节点的情况下请求此拓扑。 主站点节点使用 HANA 系统复制（异步模式）与 DR 站点节点同步。


### <a name="architecture-diagram"></a>体系结构关系图  

[![使用 HSR 使用 DR 横向扩展](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI/HSR |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点通信 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<租户 无> | team0.tenant | 客户端到 HLI/HSR |
| B | 第 II 类 | vlan\<租户 No+2> | team0.tenant+2 | 节点到节点通信 |
| C | 第 II 类 | vlan\<租户 No+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<租户 No+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
预配置了以下安装点：

| 装入点 | 用例 | 
| --- | --- |
|**在主节点上**|
|/hana/shared | 用于生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 用于生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 用于生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在 DR 节点上**|
|/hana/shared | 用于生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 用于生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 用于生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 在 DR 站点：为 DR HLI 单元的生产 HANA 实例安装配置卷和装载点。 
- 主站点节点使用 HANA 系统复制与 DR 节点同步。 
- [全局覆盖](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用于将 ExpressRoute 电路链接在一起，在区域网络之间建立专用网络。


## <a name="next-steps"></a>后续步骤
- HANA 大型实例的[基础架构和连接](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- HANA 大型实例[的高可用性和灾难恢复](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
