---
title: Azure SAP HANA（大型实例）的支持方案 | Microsoft Docs
description: Azure SAP HANA（大型实例）的支持方案及其体系结构详细信息
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8927b2a32956f73e75ac7b157ebad6bf6596ea88
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063623"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>HANA 大型实例的支持方案
本文档介绍 HANA 大型实例 (HLI) 的支持方案及其体系结构详细信息。

>[!NOTE]
>如果此处未提及你所需的方案，请与 Microsoft 服务管理团队联系以评估需求。
在继续进行 HLI 单元预配之前，请使用 SAP 或服务实现合作伙伴验证设计。

## <a name="terms-and-definitions"></a>术语和定义
我们来一起了解本文档中使用的术语和定义。

- SID：HANA 系统的系统标识符。
- HLI：Hana 大型实例。
- DR：灾难恢复站点。
- 常规 DR：一种系统设置，具有仅用于 DR 的专用资源。
- 多用途 DR：DR 站点上的系统，配置为使用非生产环境和生产实例（已配置为用于 DR 事件）。 
- 单个 SID：安装了一个实例的系统。
- 多个 SID：配置了多个实例的系统。 也称为 MCOS 环境。


## <a name="overview"></a>概述
HANA 大型实例支持多种体系结构，可满足你的业务需求。 以下列表包含方案及其配置详细信息。 

派生体系结构设计仅基于对基础结构的考虑，必须咨询 SAP 或实现合作伙伴才能进行 HANA 部署。 如果未列出你的方案，请联系 Microsoft 客户团队，以查看体系结构并为你派生解决方案。

这些体系结构完全符合 TDI（定制数据集成）设计，并且受 SAP 支持。

本文档详细介绍每个受支持体系结构中的两个组件：

- 以太网
- 存储

### <a name="ethernet"></a>以太网

配置的每台服务器都已预配了以太网集。 以下是每个 HLI 单元上配置的以太网的详细信息。

- **A**：用于客户端访问。
- **B**：用于节点到节点的通信。 所有服务器上均进行此配置（不论所请求的拓扑为何），但仅用于横向扩展方案。
- **C**：此接口用于建立节点到存储的连接。
- **D**：此接口用于建立节点到 ISCSI 设备的连接，以进行 STONITH 设置。 仅在请求 HSR 设置时配置此接口。  

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | STONITH |
| A | 第 II 类 | vlan<tenantNo> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan<tenantNo+2> | team0.tenant+2 | 节点到节点 |
| C | 第 II 类 | vlan<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan<tenantNo+3> | team0.tenant+3 | STONITH |

基于 HLI 单元上配置的接口使用拓扑。 例如，接口“B”设置为用于节点到节点的通信，这在配置横向扩展拓扑时非常有用。 在单节点扩展配置的情况下，不使用此接口。 查看所需的方案（本文档的后文）以获取有关接口使用情况的详细信息。 

如果需要，可自行定义其他 NIC 卡。 但是，现有 NIC 上的配置不能更改。

>[!NOTE]
>仍可找到其他接口 - 物理接口或绑定接口。 应根据用例考虑上面提到的接口，其余部分可忽略/不进行调整。

分配有两个 IP 地址的单元的分布形式应如下所示：

应为以太网“A”分配一个位于提交给 Microsoft 的服务器 IP 池地址范围以外的 IP 地址。 此 IP 地址用于在 OS 的 /etc/hosts 中进行维护。

应为以太网“B”分配一个用来与 NFS 通信的 IP 地址。 因此，**不**需要在 etc/hosts 中维护这些地址即可在租户中允许实例间的通信流量。

对于 HANA 系统复制或 HANA 横向扩展部署用例，不适合使用分配有两个 IP 地址的刀片式服务器配置。 如果只分配了两个 IP 地址，且希望部署此类配置，请联系 Azure 上的 SAP HANA 服务管理部门，让其在第三个 VLAN 中分配第三个 IP 地址。 对于在三个 NIC 端口上分配了三个 IP 地址的 HANA 大型实例单元，请注意以下使用规则：

- 应为以太网“A”分配一个位于提交给 Microsoft 的服务器 IP 池地址范围以外的 IP 地址。 因此，此 IP 地址不会用于在 OS 的 /etc/hosts 中进行维护。

- 应为以太网“B”分配一个用来与 NFS 存储通信的 IP 地址。 因此，不应在 etc/hosts 中维护此类地址。

- 应在 etc/hosts 中以独占方式维护 以太网“C”，以便在不同的实例之间通信。 在横向扩展 HANA 配置中，也需要将这些 IP 地址作为 HANA 用于节点间配置的 IP 地址进行维护。

- 应以独占方式使用以太网“D”访问 pacemaker 的 STONITH 设备。 配置 HANA 系统复制 (HSR) 并且希望在使用基于 SBD 的设备的操作系统上实现自动故障转移时，这是必需条件。


### <a name="storage"></a>存储
根据请求的拓扑结构预配置存储。 卷大小和装入点有所不同，具体取决于配置的服务器、SKU 和拓扑的数量。 查看你所需的方案（本文档的后文）以获取详细信息。 如果需要更多存储空间，可按一个 TB 增量购买。

>[!NOTE]
>mountpoint /usr/sap/<SID> 是 /hana/shared 装入点的符号链接。


## <a name="supported-scenarios"></a>支持的方案

在体系结构关系图中，图形使用以下表示法：

![Legends.PNG](media/hana-supported-scenario/Legends.PNG)

以下是受支持的方案列表：

1. 具有一个 SID 的单节点
2. 单节点 MCOS
3. 具有 DR（常规）的单节点
4. 具有 DR（多用途）的单节点
5. 具有 STONITH 的 HSR
6. 具有 DR（常规/多用途）的 HSR 
7. 主机自动故障转移 (1+1) 
8. 使用备用横向扩展
9. 不使用备用横向扩展
10. 利用 DR 横向扩展



## <a name="1-single-node-with-one-sid"></a>1.具有一个 SID 的单节点

此拓扑支持具有一个 SID 的纵向扩展配置中的一个节点。

### <a name="architecture-diagram"></a>体系结构关系图  

![Single-node-with-one-SID.png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan<tenantNo> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan<tenantNo+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配：

| 装入点 | 使用案例 | 
| --- | --- |
|/hana/shared/SID | HANA 安装 | 
|/hana/data/SID/mnt00001 | 数据文件安装 | 
|/hana/log/SID/mnt00001 | 日志文件安装 | 
|/hana/logbackups/SID | 恢复日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。

## <a name="2-single-node-mcos"></a>2.单节点 MCOS

此拓扑支持具有多个 SID 的纵向扩展配置中的一个节点。

### <a name="architecture-diagram"></a>体系结构关系图  

![single-node-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan<tenantNo> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan<tenantNo+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配：

| 装入点 | 使用案例 | 
| --- | --- |
|/hana/shared/SID1 | SID1 的 HANA 安装 | 
|/hana/data/SID1/mnt00001 | SID1 的数据文件安装 | 
|/hana/log/SID1/mnt00001 | SID1 的日志文件安装 | 
|/hana/logbackups/SID1 | SID1 的恢复日志 |
|/hana/shared/SID2 | SID2 的 HANA 安装 | 
|/hana/data/SID2/mnt00001 | SID2 的数据文件安装 | 
|/hana/log/SID2/mnt00001 | SID2 的日志文件安装 | 
|/hana/logbackups/SID2 | SID2 的恢复日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 卷大小分配取决于内存中的数据库大小。 请参阅[概述和体系结构](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture)部分，了解多 SID 环境支持的内存中数据库大小。

## <a name="3-single-node-with-dr-normal"></a>3.具有 DR（常规）的单节点
 
此拓扑支持具有一个或多个 SID（具有主 SID 的 DR 站点的基于存储的副本）的纵向扩展配置中的一个节点。 在该关系图中，主站点仅显示单个 SID，但也支持多 SID (MCOS)。

### <a name="architecture-diagram"></a>体系结构关系图  

![Single-node-with-dr.png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan<tenantNo> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan<tenantNo+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配：

| 装入点 | 使用案例 | 
| --- | --- |
|/hana/shared/SID | SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | SID 的日志文件安装 | 
|/hana/logbackups/SID | SID 的恢复日志 |


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分配取决于内存中的数据库大小。 请参阅[概述和体系结构](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture)部分，了解多 SID 环境支持的内存中数据库大小。
- 在 DR：在 DR HLI 单元为生产 HANA 实例安装配置卷和装入点（标记为“HANA 安装所需”）。 
- 在 DR：通过生产站点的快照复制数据、日志备份和共享卷（标记为“存储复制”）。 这些卷仅在故障转移期间装载。 有关详细信息，请参阅[灾难恢复故障转移过程](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure)。
- SKU 第 I 类级别的启动卷将复制到 DR 节点。


## <a name="4-single-node-with-dr-multipurpose"></a>4.具有 DR（多用途）的单节点
 
此拓扑支持具有一个或多个 SID（具有主 SID 的 DR 站点的基于存储的副本）的纵向扩展配置中的一个节点。 在该关系图中，主站点仅显示单个 SID，但也支持多 SID (MCOS)。 在 DR 站点，从主站点运行生产操作时，HLI 单元用于 QA 实例。 在 DR 故障转移（或故障转移测试）时，系统会停用 DR 站点上的 QA 实例。

### <a name="architecture-diagram"></a>体系结构关系图  

![single-node-with-dr-multipurpose.png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan<tenantNo> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan<tenantNo+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配：

| 装入点 | 使用案例 | 
| --- | --- |
|**在主站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的恢复日志 |
|**在 DR 站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/shared/QA-SID | QA SID 的 HANA 安装 | 
|/hana/data/QA-SID/mnt00001 | QA SID 的数据文件安装 | 
|/hana/log/QA-SID/mnt00001 | QA SID 的日志文件安装 |
|/hana/logbackups/QA-SID | QA SID 的恢复日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分配取决于内存中的数据库大小。 请参阅[概述和体系结构](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture)部分，了解多 SID 环境支持的内存中数据库大小。
- 在 DR：在 DR HLI 单元为生产 HANA 实例安装配置卷和装入点（标记为“HANA 安装所需”）。 
- 在 DR：通过生产站点的快照复制数据、日志备份和共享卷（标记为“存储复制”）。 这些卷仅在故障转移期间装载。 有关详细信息，请参阅[灾难恢复故障转移过程](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure)。 
- 在 DR：为 QA 实例安装配置 QA 的数据、日志备份、日志、共享卷（标记为“QA 实例安装”）。
- SKU 第 I 类级别的启动卷将复制到 DR 节点。

## <a name="5-hsr-with-stonith"></a>5.具有 STONITH 的 HSR
 
此拓扑支持 HANA 系统复制 (HSR) 配置的两个节点。 

截至目前，SUSE 操作系统仅支持此体系结构。


### <a name="architecture-diagram"></a>体系结构关系图  

![HSR-with-STONITH.png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 用于 STONITH |
| A | 第 II 类 | vlan<tenantNo> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan<tenantNo+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan<tenantNo+3> | team0.tenant+3 | 用于 STONITH |

### <a name="storage"></a>存储
以下装入点已预配：

| 装入点 | 使用案例 | 
| --- | --- |
|**在主节点上**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的恢复日志 |
|**在辅助节点上**|
|/hana/shared/SID | 辅助 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 辅助 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 辅助 SID 的日志文件安装 | 
|/hana/logbackups/SID | 辅助 SID 的恢复日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分配取决于内存中的数据库大小。 请参阅[概述和体系结构](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture)部分，了解多 SID 环境支持的内存中数据库大小。
- STONITH：为 STONITH 设置配置 SBD。 但是，可选择是否使用 STONITH。


## <a name="6-hsr-with-dr"></a>6.具有 DR 的 HSR
 
此拓扑支持 HANA 系统复制 (HSR) 配置的两个节点。 支持常规和多用途 DR。 

在该关系图中，在 DR 站点处展示了多用途方案，从主站点运行生产操作时，HLI 单元用于 QA 实例。 在 DR 故障转移（或故障转移测试）时，系统会停用 DR 站点上的 QA 实例。 



### <a name="architecture-diagram"></a>体系结构关系图  

![HSR-with-DR.png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 已配置但未使用 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 用于 STONITH |
| A | 第 II 类 | vlan<tenantNo> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan<tenantNo+2> | team0.tenant+2 | 已配置但未使用 |
| C | 第 II 类 | vlan<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan<tenantNo+3> | team0.tenant+3 | 用于 STONITH |

### <a name="storage"></a>存储
以下装入点已预配：

| 装入点 | 使用案例 | 
| --- | --- |
|**在主站点的主节点上**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的恢复日志 |
|**在主站点的辅助节点上**|
|/hana/shared/SID | 辅助 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 辅助 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 辅助 SID 的日志文件安装 | 
|/hana/logbackups/SID | 辅助 SID 的恢复日志 |
|**在 DR 站点**|
|/hana/shared/SID | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/shared/QA-SID | QA SID 的 HANA 安装 | 
|/hana/data/QA-SID/mnt00001 | QA SID 的数据文件安装 | 
|/hana/log/QA-SID/mnt00001 | QA SID 的日志文件安装 |
|/hana/logbackups/QA-SID | QA SID 的恢复日志 |

### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 对于 MCOS：卷大小分配取决于内存中的数据库大小。 请参阅[概述和体系结构](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture)部分，了解多 SID 环境支持的内存中数据库大小。
- STONITH：为 STONITH 设置配置 SBD。 但是，可选择是否使用 STONITH。
- 在 DR：主节点和辅助节点复制需要两组存储卷。
- 在 DR：在 DR HLI 单元为生产 HANA 实例安装配置卷和装入点（标记为“HANA 安装所需”）。 
- 在 DR：通过生产站点的快照复制数据、日志备份和共享卷（标记为“存储复制”）。 这些卷仅在故障转移期间装载。 有关详细信息，请参阅[灾难恢复故障转移过程](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure)。 
- 在 DR：为 QA 实例安装配置 QA 的数据、日志备份、日志、共享卷（标记为“QA 实例安装”）。
- SKU 第 I 类级别的启动卷将复制到 DR 节点。


## <a name="7-host-auto-failover-11"></a>7.主机自动故障转移 (1+1)
 
此拓扑支持主机自动故障转移配置中的两个节点。 其中一个节点具有主/辅助角色，另一个节点作为备用节点。 SAP 仅支持将此方案用于 S/4 HANA。 有关详细信息，请参阅 OSS 注意事项“[2408419 - SAP S/4HANA - 多节点支持](https://launchpad.support.sap.com/#/notes/2408419)”。



### <a name="architecture-diagram"></a>体系结构关系图  

![sca](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点通信 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan<tenantNo> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan<tenantNo+2> | team0.tenant+2 | 节点到节点通信 |
| C | 第 II 类 | vlan<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配：

| 装入点 | 使用案例 | 
| --- | --- |
|**在主节点和备用节点上**|
|/hana/shared | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的恢复日志 |



### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
- 在备用节点上：在备用单元上为 HANA 实例安装配置卷和装入点（标记为“HANA 安装所需”）。
 

## <a name="8-scale-out-with-standby"></a>8.使用备用横向扩展
 
此拓扑支持横向扩展配置中的多个节点。 其中一个节点具有主角色，一个或多个节点具有辅助角色，一个或多个节点作为备用节点。 但是，在任何给定时间点只能有一个主节点。


### <a name="architecture-diagram"></a>体系结构关系图  

![scaleout-nm-standby.png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点通信 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan<tenantNo> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan<tenantNo+2> | team0.tenant+2 | 节点到节点通信 |
| C | 第 II 类 | vlan<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配：

| 装入点 | 使用案例 | 
| --- | --- |
|**在主节点、辅助节点和备用节点上**|
|/hana/shared | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的恢复日志 |


## <a name="9-scale-out-without-standby"></a>9.不使用备用横向扩展
 
此拓扑支持横向扩展配置中的多个节点。 其中一个节点具有主角色，一个或多个节点具有辅助角色。 但是，在任何给定时间点只能有一个主节点。


### <a name="architecture-diagram"></a>体系结构关系图  

![scaleout-nm.png](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点通信 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan<tenantNo> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan<tenantNo+2> | team0.tenant+2 | 节点到节点通信 |
| C | 第 II 类 | vlan<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配：

| 装入点 | 使用案例 | 
| --- | --- |
|**在主节点和辅助节点上**|
|/hana/shared | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的恢复日志 |


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。

## <a name="10-scale-out-with-dr"></a>10.利用 DR 横向扩展
 
此拓扑支持使用 DR 进行横向扩展中的多个节点。 支持常规和多用途 DR。 在该关系图中，仅展示单一目的 DR。 可在有或没有备用节点的情况下请求此拓扑。


### <a name="architecture-diagram"></a>体系结构关系图  

![scaleout-with-dr.png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>以太网
以下网络接口已预配：

| NIC 逻辑接口 | SKU 类型 | 带 SUSE 操作系统的名称 | 带 RHEL 操作系统的名称 | 使用案例|
| --- | --- | --- | --- | --- |
| A | 第 I 类 | eth0.tenant | eno1.tenant | 客户端到 HLI |
| B | 第 I 类 | eth2.tenant | eno3.tenant | 节点到节点通信 |
| C | 第 I 类 | eth1.tenant | eno2.tenant | 节点到存储 |
| D | 第 I 类 | eth4.tenant | eno4.tenant | 已配置但未使用 |
| A | 第 II 类 | vlan<tenantNo> | team0.tenant | 客户端到 HLI |
| B | 第 II 类 | vlan<tenantNo+2> | team0.tenant+2 | 节点到节点通信 |
| C | 第 II 类 | vlan<tenantNo+1> | team0.tenant+1 | 节点到存储 |
| D | 第 II 类 | vlan<tenantNo+3> | team0.tenant+3 | 已配置但未使用 |

### <a name="storage"></a>存储
以下装入点已预配：

| 装入点 | 使用案例 | 
| --- | --- |
|**在主节点上**|
|/hana/shared | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 
|/hana/logbackups/SID | 生产 SID 的恢复日志 |
|**在 DR 节点上**|
|/hana/shared | 生产 SID 的 HANA 安装 | 
|/hana/data/SID/mnt00001 | 生产 SID 的数据文件安装 | 
|/hana/log/SID/mnt00001 | 生产 SID 的日志文件安装 | 


### <a name="key-considerations"></a>重要注意事项
- /usr/sap/SID 是 /hana/shared/SID 的符号链接。
-  在 DR：在 DR HLI 单元为生产 HANA 实例安装配置卷和装入点（标记为“HANA 安装所需”）。 
- 在 DR：通过生产站点的快照复制数据、日志备份和共享卷（标记为“存储复制”）。 这些卷仅在故障转移期间装载。 有关详细信息，请参阅[灾难恢复故障转移过程](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure)。 
- SKU 第 I 类级别的启动卷将复制到 DR 节点。


## <a name="next-steps"></a>后续步骤
- 有关 HLI，请参阅[基础结构和连接](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- 有关 HLI，请参阅[高可用性和灾难恢复](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)