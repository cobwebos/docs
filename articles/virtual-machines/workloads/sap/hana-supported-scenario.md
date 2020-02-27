---
title: Azure 上的 SAP HANA 支持方案（大型实例） |Microsoft Docs
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
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617177"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>HANA 大型实例的支持方案
本文介绍 HANA 大型实例（B-HLI）的支持方案和体系结构详细信息。

>[!NOTE]
>如果本文未提及所需方案，请联系 Microsoft 服务管理团队以评估你的要求。
设置 B-HLI 单元之前，请先验证 SAP 或服务实现合作伙伴的设计。

## <a name="terms-and-definitions"></a>术语和定义
让我们了解本文中使用的术语和定义：

- **SID**： HANA 系统的系统标识符
- **B-hli**： Hana 大型实例
- **DR**：灾难恢复
- **正常 dr**：系统设置，专用资源仅用于 DR 目的
- 多**用途 dr**：配置为使用非生产环境的 dr 站点系统，以及为 DR 事件配置的生产实例 
- **单 SID**：安装有一个实例的系统
- **多 SID**：配置了多个实例的系统;也称为 MCOS 环境
- **HSR**： SAP HANA 系统复制

## <a name="overview"></a>概述
HANA 大型实例支持各种体系结构，以帮助您满足您的业务需求。 以下部分介绍体系结构方案及其配置详细信息。 

派生的体系结构设计纯粹是从基础结构的角度来看的，你必须为 HANA 部署咨询 SAP 或实现合作伙伴。 如果你的方案未在本文中列出，请联系 Microsoft 帐户团队查看体系结构并为你派生解决方案。

> [!NOTE]
> 这些体系结构完全符合定制的数据集成（TDI）设计并支持 SAP。

本文介绍了每个受支持的体系结构中的两个组件的详细信息：

- 以太网
- 存储

### <a name="ethernet"></a>以太网

每个预配的服务器都预配置了一组以太网接口。 每个 eec 单位配置的以太网接口分为四种类型：

- **答**：用于或通过客户端访问。
- **B**：用于节点到节点的通信。 此接口在所有服务器上配置（无论请求的拓扑），但仅用于扩展方案。
- **C**：用于节点到存储的连接。
- **D**：用于 STONITH 设置的节点到 iSCSI 设备连接。 仅当请求 HSR 安装程序时才配置此接口。  

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 B-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点|
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | STONITH |
| A | 第 II 类 | vlan\<tenantNo > | team0.tenant | 客户端到 B-HLI |
| B | 第 II 类 | vlan\<tenantNo + 2 > | team0.tenant+2 | 节点到节点|
| C | 第 II 类 | vlan\<tenantNo + 1 > | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo + 3 > | team0.tenant+3 | STONITH |

可以根据在 B-HLI 设备上配置的拓扑来选择接口。 例如，接口 "B" 设置为节点到节点的通信，这在配置了扩展拓扑时非常有用。 此接口不用于单节点、纵向扩展配置。 有关接口用法的详细信息，请查看所需方案（本文后面的部分）。 

如有必要，可以自行定义其他 NIC 卡。 但是，*不能*更改现有 nic 的配置。

>[!NOTE]
>你可能会发现其他接口是物理接口或捆绑。 你应仅考虑前面提到的用于用例的接口。 任何其他可以忽略。

具有两个分配的 IP 地址的单元的分配应如下所示：

- 以太网 "A" 应有一个分配的 IP 地址，该地址在你提交给 Microsoft 的服务器 IP 池地址范围内。 此 IP 地址应保留在 OS 的 */etc/hosts*目录中。

- 应为以太网 "C" 分配用于通信到 NFS 的 IP 地址。 *不*需要在*etc/hosts*目录中维护此地址即可在租户中允许实例到实例的通信。

对于 HANA 系统复制或 HANA 扩展部署，具有两个分配的 IP 地址的边栏选项卡配置不合适。 如果只有两个分配的 IP 地址，并且想要部署此类配置，请联系 Azure 服务管理上的 SAP HANA。 他们可以在第三个 VLAN 中为你分配第三个 IP 地址。 对于三个 NIC 端口上有三个分配的 IP 地址的 HANA 大型实例单元，以下用法规则适用：

- 以太网 "A" 应有一个分配的 IP 地址，该地址不在你提交给 Microsoft 的服务器 IP 池地址范围内。 此 IP 地址不应在操作系统的*etc/hosts*目录中维护。

- 以太网 "B" 应专门在*etc/hosts*目录中进行维护，以便在不同实例之间进行通信。 这些是要在横向扩展 HANA 配置中维护的 IP 地址，作为 HANA 用于节点间配置的 IP 地址。

- 应为以太网 "C" 分配用于通信到 NFS 存储的 IP 地址。 此类型的地址不应在*etc/hosts*目录中维护。

- 应专门使用以太网 "D" 来访问用于 Pacemaker 的 STONITH 设备。 当你配置 HANA 系统复制并想要使用基于 SBD 的设备实现操作系统的自动故障转移时，此接口是必需的。


### <a name="storage"></a>存储
存储基于请求的拓扑进行预配置。 根据服务器的数量、Sku 数量和配置的拓扑，卷大小和装入点会有所不同。 有关详细信息，请查看所需的方案（本文后面的部分）。 如果需要更多存储空间，可以按 1 TB 的增量购买。

>[!NOTE]
>装入点/usr/sap/\<SID > 是指向/hana/shared 装入点的符号链接。


## <a name="supported-scenarios"></a>支持的方案

下一节中的体系结构关系图使用以下表示法：

[体系结构关系图 ![表](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

下面是受支持的方案：

* 具有一个 SID 的单节点
* 单节点 MCOS
* 包含 DR 的单节点（正常）
* 包含 DR （多用途）的单节点
* 具有 STONITH 的 HSR
* HSR （正常/多用途） 
* 主机自动故障转移 (1+1) 
* 使用备用节点的横向扩展
* 不使用备用节点的横向扩展
* 使用 DR 的横向扩展

## <a name="single-node-with-one-sid"></a>具有一个 SID 的单节点

此拓扑支持具有一个 SID 的向上扩展配置中的一个节点。

### <a name="architecture-diagram"></a>体系结构关系图  

![具有一个 SID 的单节点](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 B-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo > | team0.tenant | 客户端到 B-HLI |
| B | 第 II 类 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo + 1 > | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|/hana/shared/SID | HANA 安装 | 
|/hana/data/SID/mnt00001 | 数据文件安装 | 
|/hana/log/SID/mnt00001 | 日志文件安装 | 
|/hana/logbackups/SID | 重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。

## <a name="single-node-mcos"></a>单节点 MCOS

此拓扑支持具有多个 Sid 的扩展配置中的一个节点。

### <a name="architecture-diagram"></a>体系结构关系图  

![单节点 MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 B-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo > | team0.tenant | 客户端到 B-HLI |
| B | 第 II 类 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo + 1 > | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|/hana/shared/SID1 | 适用于 SID1 的 HANA 安装 | 
|/hana/data/SID1/mnt00001 | SID1 的数据文件安装 | 
|/hana/log/SID1/mnt00001 | SID1 的日志文件安装 | 
|/hana/logbackups/SID1 | SID1 的重做日志 |
|/hana/shared/SID2 | 适用于 SID2 的 HANA 安装 | 
|/hana/data/SID2/mnt00001 | SID2 的数据文件安装 | 
|/hana/log/SID2/mnt00001 | SID2 的日志文件安装 | 
|/hana/logbackups/SID2 | SID2 的重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 卷大小分布基于内存中的数据库大小。 若要了解多 SID 环境中支持的内存中的数据库大小，请参阅[概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。

## <a name="single-node-with-dr-using-storage-replication"></a>使用存储复制进行灾难恢复的单节点
 
此拓扑支持具有一个或多个 Sid 的向上扩展配置中的一个节点，并支持基于存储的复制到灾难恢复站点中的主 SID。 在此图中，主站点上只描述了一个单一 SID 系统，但同时还支持 MCOS 系统。

### <a name="architecture-diagram"></a>体系结构关系图  

![使用存储复制进行灾难恢复的单节点](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 B-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo > | team0.tenant | 客户端到 B-HLI |
| B | 第 II 类 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo + 1 > | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|/hana/shared/SID | 适用于 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | SID 的日志文件安装 | 
|/hana/logbackups/SID | SID 的重做日志 |


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分布基于内存中的数据库大小。 若要了解多 SID 环境中支持的内存中的数据库大小，请参阅[概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- 在灾难恢复站点：在 DR 中安装生产 HANA 实例的卷和装入点（对于 HANA 安装，标记为 "必需"）。 
- 在 DR 站点上：数据、日志备份和共享卷（标记为 "存储复制"）通过生产站点的快照进行复制。 仅在故障转移期间装入这些卷。 有关详细信息，请参阅[灾难恢复故障转移过程](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)。
- *SKU 类型 I 类*的启动卷被复制到 DR 节点。


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>使用存储复制的单节点，含 DR （多用途）
 
此拓扑支持具有一个或多个 Sid 的向上扩展配置中的一个节点，并支持基于存储的复制到灾难恢复站点中的主 SID。 在此关系图中，主站点上只描述了一个单一 SID 系统，但同时支持多 SID （MCOS）系统。 在灾难恢复站点，当生产操作从主站点运行时，将使用速度用于 QA 实例。 在 DR 故障转移（或故障转移测试）期间，DR 站点上的 QA 实例将被关闭。

### <a name="architecture-diagram"></a>体系结构关系图  

![使用存储复制的单节点，含 DR （多用途）](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 B-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo > | team0.tenant | 客户端到 B-HLI |
| B | 第 II 类 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo + 1 > | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在 DR 站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/shared/QA-SID | 适用于 QA SID 的 HANA 安装 | 
|/hana/data/QA-SID/mnt00001 | 为 QA SID 安装数据文件 | 
|/hana/log/QA-SID/mnt00001 | 问答 SID 的日志文件安装 |
|/hana/logbackups/QA-SID | QA SID 的重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分布基于内存中的数据库大小。 若要了解多 SID 环境中支持的内存中的数据库大小，请参阅[概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- 在灾难恢复站点：在 DR 中安装生产 HANA 实例的卷和装入点（对于 HANA 安装，标记为 "必需"）。 
- 在 DR 站点上：数据、日志备份和共享卷（标记为 "存储复制"）通过生产站点的快照进行复制。 仅在故障转移期间装入这些卷。 有关详细信息，请参阅[灾难恢复故障转移过程](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)。 
- 在 DR 站点上：为 qa 实例安装配置了用于 QA 的数据、日志备份、日志和共享卷（标记为 "QA 实例安装"）。
- *SKU 类型 I 类*的启动卷被复制到 DR 节点。

## <a name="hsr-with-stonith-for-high-availability"></a>HSR with STONITH 实现高可用性
 
此拓扑支持 HANA 系统复制配置的两个节点。 仅节点上的单个 HANA 实例支持此配置。 这意味着*不*支持 MCOS 方案。

> [!NOTE]
> 截至2019年12月，仅 SUSE 操作系统支持此体系结构。


### <a name="architecture-diagram"></a>体系结构关系图  

![HSR with STONITH 实现高可用性](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 B-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 用于 STONITH |
| A | 第 II 类 | vlan\<tenantNo > | team0.tenant | 客户端到 B-HLI |
| B | 第 II 类 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo + 1 > | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo + 3 > | team0.tenant+3 | 用于 STONITH |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主节点上**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在辅助节点上**|
|/hana/shared/SID | 辅助 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 辅助 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 辅助 SID 的日志文件安装 | 
|/hana/logbackups/SID | 辅助 SID 的重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分布基于内存中的数据库大小。 若要了解多 SID 环境中支持的内存中的数据库大小，请参阅[概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- STONITH：为 STONITH 设置配置 SBD。 但是，STONITH 的使用是可选的。


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>通过存储复制实现 HSR 和灾难恢复的高可用性
 
此拓扑支持 HANA 系统复制配置的两个节点。 正常和多用途 DRs 均受支持。 仅节点上的单个 HANA 实例支持这些配置。 这意味着这些配置*不*支持 MCOS 方案。

在此图中，多用途方案在 DR 站点上进行描述，其中，在生产操作从主站点运行时，将在 在 DR 故障转移（或故障转移测试）期间，DR 站点上的 QA 实例将被关闭。 

### <a name="architecture-diagram"></a>体系结构关系图  

![通过存储复制实现 HSR 和灾难恢复的高可用性](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 B-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 用于 STONITH |
| A | 第 II 类 | vlan\<tenantNo > | team0.tenant | 客户端到 B-HLI |
| B | 第 II 类 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo + 1 > | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo + 3 > | team0.tenant+3 | 用于 STONITH |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主站点的主节点上**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在主站点的辅助节点上**|
|/hana/shared/SID | 辅助 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 辅助 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 辅助 SID 的日志文件安装 | 
|/hana/logbackups/SID | 辅助 SID 的重做日志 |
|**在 DR 站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/shared/QA-SID | 适用于 QA SID 的 HANA 安装 | 
|/hana/data/QA-SID/mnt00001 | 为 QA SID 安装数据文件 | 
|/hana/log/QA-SID/mnt00001 | 问答 SID 的日志文件安装 |
|/hana/logbackups/QA-SID | QA SID 的重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分布基于内存中的数据库大小。 若要了解多 SID 环境中支持的内存中的数据库大小，请参阅[概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- STONITH：为 STONITH 设置配置 SBD。 但是，STONITH 的使用是可选的。
- 在 DR 站点：主节点和辅助节点复制*需要两组存储卷*。
- 在灾难恢复站点：在 DR 中安装生产 HANA 实例的卷和装入点（对于 HANA 安装，标记为 "必需"）。 
- 在 DR 站点上：数据、日志备份和共享卷（标记为 "存储复制"）通过生产站点的快照进行复制。 仅在故障转移期间装入这些卷。 有关详细信息，请参阅[灾难恢复故障转移过程](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)。 
- 在 DR 站点上：为 qa 实例安装配置了用于 QA 的数据、日志备份、日志和共享卷（标记为 "QA 实例安装"）。
- *SKU 类型 I 类*的启动卷被复制到 DR 节点。


## <a name="host-auto-failover-11"></a>主机自动故障转移 (1+1)
 
此拓扑支持主机自动故障转移配置中的两个节点。 有一个节点具有主/辅助角色，另一个节点作为备用节点。 *SAP 仅对 S/4 HANA 支持此方案。* 有关详细信息，请参阅[OSS 说明 2408419-SAP S/4HANA 支持](https://launchpad.support.sap.com/#/notes/2408419)。



### <a name="architecture-diagram"></a>体系结构关系图  

![主机自动故障转移 (1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 B-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点通信 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo > | team0.tenant | 客户端到 B-HLI |
| B | 第 II 类 | vlan\<tenantNo + 2 > | team0.tenant+2 | 节点到节点通信 |
| C | 第 II 类 | vlan\<tenantNo + 1 > | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主节点和备用节点上**|
|/hana/shared | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |



### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 备用：在备用设备上为 HANA 实例安装配置（标记为 "HANA 安装必需"）的卷和装入点。
 

## <a name="scale-out-with-standby"></a>使用备用节点的横向扩展
 
此拓扑支持横向扩展配置中的多个节点。 有一个节点具有一个主角色、一个或多个具有辅助角色的节点，以及一个或多个节点作为备用节点。 但是，在任何单个时间点，只能有一个主节点。


### <a name="architecture-diagram"></a>体系结构关系图  

![使用备用节点的横向扩展](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 B-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点通信 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo > | team0.tenant | 客户端到 B-HLI |
| B | 第 II 类 | vlan\<tenantNo + 2 > | team0.tenant+2 | 节点到节点通信 |
| C | 第 II 类 | vlan\<tenantNo + 1 > | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主节点、辅助节点和备用节点上**|
|/hana/shared | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |


## <a name="scale-out-without-standby"></a>不使用备用节点的横向扩展
 
此拓扑支持横向扩展配置中的多个节点。 有一个节点具有主角色，一个或多个节点具有辅助角色。 但是，在任何单个时间点，只能有一个主节点。


### <a name="architecture-diagram"></a>体系结构关系图  

![不使用备用节点的横向扩展](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 B-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点通信 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo > | team0.tenant | 客户端到 B-HLI |
| B | 第 II 类 | vlan\<tenantNo + 2 > | team0.tenant+2 | 节点到节点通信 |
| C | 第 II 类 | vlan\<tenantNo + 1 > | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主节点和辅助节点上**|
|/hana/shared | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。

## <a name="scale-out-with-dr-using-storage-replication"></a>使用存储复制进行 DR 扩展
 
此拓扑支持使用 DR 的横向扩展中的多个节点。 正常和多用途 DRs 均受支持。 在该关系图中，仅展示单一目的 DR。 可在有或没有备用节点的情况下请求此拓扑。


### <a name="architecture-diagram"></a>体系结构关系图  

![使用存储复制进行 DR 扩展](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 B-HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点通信 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo > | team0.tenant | 客户端到 B-HLI |
| B | 第 II 类 | vlan\<tenantNo + 2 > | team0.tenant+2 | 节点到节点通信 |
| C | 第 II 类 | vlan\<tenantNo + 1 > | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主节点上**|
|/hana/shared | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在 DR 节点上**|
|/hana/shared | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
-  在灾难恢复站点：在 DR 中安装生产 HANA 实例的卷和装入点（对于 HANA 安装，标记为 "必需"）。 
- 在 DR 站点上：数据、日志备份和共享卷（标记为 "存储复制"）通过生产站点的快照进行复制。 仅在故障转移期间装入这些卷。 有关详细信息，请参阅[灾难恢复故障转移过程](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)。 
- *SKU 类型 I 类*的启动卷被复制到 DR 节点。


## <a name="single-node-with-dr-using-hsr"></a>使用 HSR 进行灾难恢复的单节点
 
此拓扑支持具有一个 SID 的向上扩展配置中的一个节点，并使用 HANA 系统将系统复制到主 SID 的 DR 站点。 在此关系图中，主站点上只描述了一个单一 SID 系统，但同时支持多 SID （MCOS）系统。

### <a name="architecture-diagram"></a>体系结构关系图  

![使用 HSR 进行灾难恢复的单节点](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 B-HLI/HSR |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo > | team0.tenant | 客户端到 B-HLI/HSR |
| B | 第 II 类 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo + 1 > | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点在每个机架单元（主和灾难恢复）上预先配置：

| 装入点 | 用例 | 
| --- | --- |
|/hana/shared/SID | 适用于 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | SID 的日志文件安装 | 
|/hana/logbackups/SID | SID 的重做日志 |


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分布基于内存中的数据库大小。 若要了解多 SID 环境中支持的内存中的数据库大小，请参阅[概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- 主节点通过使用 HANA 系统复制与 DR 节点同步。 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用于将 ExpressRoute 线路链接在一起，以在区域网络之间建立专用网络。



## <a name="single-node-hsr-to-dr-cost-optimized"></a>单节点 HSR 到 DR （成本优化） 
 
 此拓扑支持具有一个 SID 的向上扩展配置中的一个节点，并使用 HANA 系统将系统复制到主 SID 的 DR 站点。 在此关系图中，主站点上只描述了一个单一 SID 系统，但同时支持多 SID （MCOS）系统。 在灾难恢复站点，当生产操作从主站点运行时，将使用速度为 QA 实例。 在 DR 故障转移（或故障转移测试）期间，DR 站点上的 QA 实例将被关闭。

### <a name="architecture-diagram"></a>体系结构关系图  

![单节点 HSR 到 DR （成本优化）](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 B-HLI/HSR |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo > | team0.tenant | 客户端到 B-HLI/HSR |
| B | 第 II 类 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo + 1 > | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在 DR 站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|/hana/shared/QA-SID | 适用于 QA SID 的 HANA 安装 | 
|/hana/data/QA-SID/mnt00001 | 为 QA SID 安装数据文件 | 
|/hana/log/QA-SID/mnt00001 | 问答 SID 的日志文件安装 |
|/hana/logbackups/QA-SID | QA SID 的重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分布基于内存中的数据库大小。 若要了解多 SID 环境中支持的内存中的数据库大小，请参阅[概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
- 在灾难恢复站点：在 DR 中安装生产 HANA 实例的卷和装入点，并将其标记为 "在 DR 站点上生产实例"。 
- 在 DR 站点上：为 qa 实例安装配置了用于 QA 的数据、日志备份、日志和共享卷（标记为 "QA 实例安装"）。
- 主节点通过使用 HANA 系统复制与 DR 节点同步。 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用于将 ExpressRoute 线路链接在一起，以在区域网络之间建立专用网络。

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>HSR 的高可用性和灾难恢复 
 
 此拓扑支持两个节点，用于本地区域的高可用性的 HANA 系统复制配置。 对于 DR，DR 区域中的第三个节点通过使用 HSR （async mode）与主站点同步。 

### <a name="architecture-diagram"></a>体系结构关系图  

![HSR 的高可用性和灾难恢复](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 B-HLI/HSR |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo > | team0.tenant | 客户端到 B-HLI/HSR |
| B | 第 II 类 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo + 1 > | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在 DR 站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 在灾难恢复站点：在 DR B-HLI 单位为生产 HANA 实例安装配置（标记为 "生产 DR 实例"）卷和装入点。 
- 主站点节点通过使用 HANA 系统复制与 DR 节点同步。 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用于将 ExpressRoute 线路链接在一起，以在区域网络之间建立专用网络。

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>HSR 的高可用性和灾难恢复（成本优化）
 
 此拓扑支持两个节点，用于本地区域的高可用性的 HANA 系统复制配置。 对于 DR，DR 区域中的第三个节点通过使用 HSR （async mode）与主站点同步，而另一个实例（例如，QA）已在 DR 节点中运行。 

### <a name="architecture-diagram"></a>体系结构关系图  

![HSR 的高可用性和灾难恢复（成本优化）](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 B-HLI/HSR |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo > | team0.tenant | 客户端到 B-HLI/HSR |
| B | 第 II 类 | vlan\<tenantNo + 2 > | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan\<tenantNo + 1 > | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在 DR 站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|/hana/shared/QA-SID | 适用于 QA SID 的 HANA 安装 | 
|/hana/data/QA-SID/mnt00001 | 为 QA SID 安装数据文件 | 
|/hana/log/QA-SID/mnt00001 | 问答 SID 的日志文件安装 |
|/hana/logbackups/QA-SID | QA SID 的重做日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 在灾难恢复站点：在 DR B-HLI 单位为生产 HANA 实例安装配置（标记为 "生产 DR 实例"）卷和装入点。 
- 在 DR 站点上：为 qa 实例安装配置了用于 QA 的数据、日志备份、日志和共享卷（标记为 "QA 实例安装"）。
- 主站点节点通过使用 HANA 系统复制与 DR 节点同步。 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用于将 ExpressRoute 线路链接在一起，以在区域网络之间建立专用网络。

## <a name="scale-out-with-dr-using-hsr"></a>使用 HSR 通过 DR 扩展
 
此拓扑支持使用 DR 的横向扩展中的多个节点。 可在有或没有备用节点的情况下请求此拓扑。 主站点节点通过使用 HANA 系统复制（async mode）与 DR 站点节点同步。


### <a name="architecture-diagram"></a>体系结构关系图  

[使用 HSR 通过 DR 实现 ![扩展](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 用例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 B-HLI/HSR |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点通信 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan\<tenantNo > | team0.tenant | 客户端到 B-HLI/HSR |
| B | 第 II 类 | vlan\<tenantNo + 2 > | team0.tenant+2 | 节点到节点通信 |
| C | 第 II 类 | vlan\<tenantNo + 1 > | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan\<tenantNo + 3 > | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配置：

| 装入点 | 用例 | 
| --- | --- |
|**在主节点上**|
|/hana/shared | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |
|**在 DR 节点上**|
|/hana/shared | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的重做日志 |


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 在灾难恢复站点：在 DR B-HLI 单元中为生产 HANA 实例安装配置卷和装入点。 
- 主站点节点通过使用 HANA 系统复制与 DR 节点同步。 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)用于将 ExpressRoute 线路链接在一起，以在区域网络之间建立专用网络。


## <a name="next-steps"></a>后续步骤
- 适用于 HANA 大型实例的[基础结构和连接](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- HANA 大型实例的[高可用性和灾难恢复](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
