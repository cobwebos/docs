---
title: 适用于 SAP 工作负荷的 Azure 存储类型
description: 规划 SAP 工作负荷的 Azure 存储类型
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/23/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 668f8ffdc4b797219dc1f3c23fecb858d8f706ad
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510855"
---
# <a name="azure-storage-types-for-sap-workload"></a>适用于 SAP 工作负载的 Azure 存储类型
Azure 有许多不同的存储类型，这些类型在功能、吞吐量、延迟和价格方面有所不同。 某些存储类型不是或不可用于 SAP 方案。 但对于特定的 SAP 工作负荷方案，多个 Azure 存储类型非常适合或进行了优化。 特别是对于 SAP HANA，某些 Azure 存储类型已通过 SAP HANA 使用进行了认证。 在本文档中，我们将浏览不同类型的存储，并介绍 SAP 工作负荷和 SAP 组件的功能和可用性。

介绍了本文中使用的单位。 公有云供应商迁移到使用 GiB ([Gibibyte](https://en.wikipedia.org/wiki/Gibibyte)) 或 TiB ([Tebibyte](https://en.wikipedia.org/wiki/Tebibyte) 作为大小单位，而不是 gb 或 tb。 因此，所有 Azure 文档和 prizing 都使用这些单元。  在整个文档中，我们会专门引用这些大小的 MiB、GiB 和 TiB 单元。 你可能需要计划 MB、GB 和 TB。 因此，如果需要为 400 MiB/秒吞吐量调整大小，而不是 250 MiB/秒的吞吐量，请注意计算中的一些细微差别。

## <a name="microsoft-azure-storage-resiliency"></a>Microsoft Azure 存储复原

Microsoft Azure 存储标准 HDD、标准 SSD、Azure 高级存储和超磁盘，可在三个不同的存储节点上的三个副本中保留基本 VHD (，其中包含 OS) 和 VM 附加数据磁盘或 Vhd。 故障转移到另一个副本，并在发生存储节点故障时对新副本进行种子设定是透明的。 作为此冗余的结果， **无** 需在多个 Azure 磁盘上使用任何类型的存储冗余层。 这称为本地冗余存储 (LRS)。 对于 Azure 中的这些存储类型，LRS 是默认值。 [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/) 提供足够的冗余来实现与其他本机 Azure 存储相同的 sla。

还有其他几种冗余方法，这一文章适用于 Azure 提供的一些不同存储类型的 [Azure 存储复制](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) 。 

### <a name="azure-managed-disks"></a>Azure 托管磁盘

托管磁盘是 Azure 资源管理器中的一种资源类型，可用于替代 Azure 存储帐户中存储的 Vhd。 托管磁盘会自动与它们附加到的虚拟机的 [可用性集] [虚拟机-管理可用性] 一致，因此可以提高虚拟机和虚拟机上运行的服务的可用性。 有关详细信息，请阅读[概述文章](../../windows/managed-disks-overview.md)。

与复原相关，此示例演示了托管磁盘的优点：

- 正在 Azure 可用性集中为 SAP 系统部署两个 DBMS Vm 
- Azure 在部署 Vm 时，将在不同的存储群集中放置包含操作系统映像的磁盘。 这可以避免这两个 Vm 受到单个 Azure 存储群集问题的影响
- 当你创建新的托管磁盘，并将其分配给这些 Vm 来存储数据库的数据文件和日志文件时，这两个 Vm 的新磁盘也部署在单独的存储群集中，因此，第一个 VM 的所有磁盘都不会与第二个 VM 的磁盘共享存储群集

在客户定义的存储帐户中部署不带托管磁盘的情况下，磁盘分配是任意的，并不知道出于复原目的，在 AvSet 中部署 Vm。

> [!NOTE]
> 从此原因和通过托管磁盘专门提供的其他一些改进，我们要求使用 azure 数据块存储的虚拟机的新部署 (所有 Azure 存储空间（Azure NetApp 文件除外），) 需要将 Azure 托管磁盘用于基本 VHD/OS 磁盘、包含 SAP 数据库文件的数据磁盘。 独立于是否通过可用性集部署 Vm，无论是在可用性区域还是独立于集和区域。 用于存储备份的磁盘不一定需要为托管磁盘。

> [!NOTE]
> Azure 托管磁盘仅 (LRS) 提供本地冗余。 


## <a name="storage-scenarios-with-sap-workloads"></a>SAP 工作负荷的存储方案
在 Azure 中部署的堆栈的各种组件的 SAP 工作负荷中需要保留的存储。 这些方案最少列出如下：

- 永久保存 VM 的基本 VHD，其中包含操作系统以及在该磁盘上安装的其他软件。 此磁盘/VHD 是 VM 的根。 对它所做的任何更改都需要保留。 因此，下次停止并重新启动 VM 时，所有更改都将保留。 尤其是在 VM 通过 Azure 部署到另一个主机上（而不是最初运行的主机）的情况下
- 持久化数据磁盘。 这些磁盘是附加到的 Vhd，用于存储应用程序数据。 此应用程序数据可以是数据库、备份文件或软件安装的数据和日志/重做文件。 表示包含操作系统的基本 VHD 之外的任何磁盘
- 包含 NetWeaver 或 S/4HANA 的全局传输目录的文件共享或共享磁盘。 这些共享的内容由多个 Vm 中运行的软件使用，或用于构建高可用性故障转移群集方案
- EDI 进程的/sapmnt 目录或通用文件共享，或类似。 这些共享的内容由多个 Vm 中运行的软件使用，或用于构建高可用性故障转移群集方案

在接下来的几个部分中，我们讨论了适用于上述四种方案的不同 Azure 存储类型及其可用性。 有关如何使用不同的 Azure 存储类型的一般分类说明，请参见 [Azure 中的可用磁盘类型](../../linux/disks-types.md)一文。 针对 SAP 工作负荷使用不同 Azure 存储类型的建议并不 majorly。

有关 S/4HANA 的 SAP NetWeaver/应用程序层对 Azure 存储类型的支持限制，请阅读 [sap 支持说明 2015553](https://launchpad.support.sap.com/#/notes/2015553) ，了解 SAP HANA 认证和受支持的 azure 存储类型 [SAP HANA azure 虚拟机存储配置](./hana-vm-operations-storage.md)。

描述不同 Azure 存储类型的部分将为你介绍使用 SAP 支持的存储的限制和可能性的更多背景。 

## <a name="storage-recommendations-for-sap-storage-scenarios"></a>SAP 存储方案的存储建议
在深入了解详细信息之前，我们将介绍文档开头部分的摘要和建议。 而特定类型的 Azure 存储的详细信息则在文档的此部分之后。 汇总表中 SAP 存储方案的存储建议，如下所示：

| 使用方案 | 标准 HDD | 标准 SSD | 高级存储 | 超级磁盘 | Azure NetApp 文件 |
| --- | --- | --- | --- | --- | --- |
| OS 磁盘 | 不适用 |  适用于非生产) 的受限 ( | 建议 | 不可能 | 不可能 |
| 全局传输目录 | 不支持 | 不支持 | 建议 | 建议 | 建议 |
| /sapmnt | 不适用 | 适用于非生产) 的受限 ( | 建议 | 建议 | 建议 |
| DBMS 数据卷 SAP HANA M/Mv2 VM 系列 | 不支持 | 不支持 | 建议 | 建议 | 推荐<sup>2</sup> |
| DBMS 日志卷 SAP HANA M/Mv2 VM 系列 | 不支持 | 不支持 | 建议<sup>1</sup> | 建议 | 推荐<sup>2</sup> | 
| DBMS 数据卷 SAP HANA Esv3/Edsv4 VM 系列 | 不支持 | 不支持 | 建议 | 建议 | 推荐<sup>2</sup> |
| DBMS 日志卷 SAP HANA Esv3/Edsv4 VM 系列 | 不支持 | 不支持 | 不支持 | 建议 | 推荐<sup>2</sup> | 
| DBMS 数据量非 HANA | 不受支持 | 适用于非生产) 的受限 ( | 建议 | 建议 | 不受支持 |
| DBMS 日志量非 HANA M/Mv2 VM 系列 | 不受支持 | 适用于非生产) 的受限 ( | 建议<sup>1</sup> | 建议 | 不受支持 |
| DBMS 日志卷非 HANA 非 Mv2/虚拟机系列 | 不受支持 | 适用于非生产) 的受限 ( | 适用于最多中等工作负荷 | 建议 | 不受支持 |


<sup>1</sup> 使用 [Azure 写入加速器](../../how-to-enable-write-accelerator.md) 适用于使用和的 Mv2 VM 系列的日志/重做日志卷 <sup>2</sup> 使用时，需要/hana/data 和/hana/log 和 

不同存储类型列表中的预期特征，例如：

| 使用方案 | 标准 HDD | 标准 SSD | 高级存储 | 超级磁盘 | Azure NetApp 文件 |
| --- | --- | --- | --- | --- | --- |
| 吞吐量/IOPS SLA | 否 | 否 | 是 | 是 | 是 |
| 延迟读取次数 | high | 中到高 | low | sub 毫秒 | sub 毫秒 |
| 延迟写入 | high | 中到高  | 低 (sub 毫秒<sup>1</sup>)  | sub 毫秒 | sub 毫秒 |
| 支持 HANA | 否 | 否 | 是<sup>1</sup> | 是 | 是 |
| 可能的磁盘快照 | 是 | 是 | 是 | 否 | 是 |
| 使用可用性集时分配不同存储群集上的磁盘 | 通过托管磁盘 | 通过托管磁盘 | 通过托管磁盘 | 使用可用性集部署的 Vm 不支持磁盘类型 | 无<sup>3</sup> |
| 与可用性区域对齐 | 是 | 是 | 是 | 是 | 需要 Microsoft 的参与 |
| 区域性冗余 | 不适用于托管磁盘 | 不适用于托管磁盘 | 不适用于托管磁盘 | 否 | 否 |
| 异地冗余 | 不适用于托管磁盘 | 不适用于托管磁盘 | 否 | 否 | 否 |


<sup>1</sup> 使用 [Azure 写入加速器](../../how-to-enable-write-accelerator.md) FOR M/Mv2 VM 系列的日志/重做日志卷的使用情况

<sup>2</sup> 成本取决于预配的 IOPS 和吞吐量

<sup>3</sup> 创建不同的和容量池并不保证容量池部署到不同的存储单元上


> [!IMPORTANT]
> 若要使用 Azure NetApp 文件 (和) 实现小于1毫秒的 i/o 延迟，你需要与 Microsoft 协作，以根据和安排 Vm 和 NFS 共享之间的正确位置。 目前没有任何机制可以在部署的 VM 和和上托管的 NFS 卷之间提供自动邻近性。 如果不同的 Azure 区域设置不同，则添加的网络延迟可能会导致 i/o 滞后时间超过1毫秒（如果 VM 和 NFS 共享未分配到邻近性）。


> [!IMPORTANT]
> 目前没有提供任何基于 Azure 块存储的托管磁盘，或 Azure NetApp 文件提供任何区域或地理冗余。 因此，你需要确保高可用性和灾难恢复体系结构不依赖于这些托管磁盘、NFS 或 SMB 共享的任何类型的 Azure 本机存储复制。


## <a name="azure-premium-storage"></a>Azure 高级存储
Azure 高级 SSD 存储已引入，旨在提供：

* 低 i/o 延迟
* IOPS 和吞吐量 Sla
* I/o 延迟的可变性更少

这种类型的存储面向 DBMS 工作负荷，需要低单位数毫秒延迟的存储流量，在 Azure 高级存储的情况下，针对 IOPS 和吞吐量开销的 Sla 并不是存储在此类磁盘中的实际数据量，而是此类磁盘的大小类别，与存储在磁盘中的数据量无关。 你还可以在高级存储上创建不直接映射到 [高级 SSD](../../linux/disks-types.md#premium-ssd)文章所示大小类别中的磁盘。 本文的结论如下：

- 存储按范围组织。 例如，513 GiB 到 1024 GiB 容量范围内的磁盘共用相同的功能和每月的成本
- 每个 GiB 的 IOPS 不会跨大小类别跟踪。 低于 32 GiB 的较小磁盘具有更高的每 GiB IOPS 速率。 对于超过 32 GiB 到 1024 GiB 的磁盘，每个 GiB 的 IOPS 速率介于每个 GiB 的 4-5 IOPS 之间。 对于容量高达 32767 GiB 的大型磁盘，每个 GiB 的 IOPS 速率将低于1
- 此存储的 i/o 吞吐量并非线性，大小为磁盘类别。 对于较小的磁盘，例如 65 GiB 和 128 GiB 容量之间的类别，吞吐量是围绕 780KB/GiB。 对于极大型磁盘（如 32767 GiB 磁盘），吞吐量约为 28KB/GiB
- 在不更改磁盘容量的情况下，不能更改 IOPS 和吞吐量 Sla

Azure 具有99.9% 的单一实例 VM SLA，与 Azure 高级存储或 Azure 超磁盘存储的使用相关。 Sla 中记录了 [虚拟机](https://azure.microsoft.com/support/legal/sla/virtual-machines/)的 sla。 为了遵从此单 VM SLA，基本 VHD 磁盘以及 **所有** 附加的磁盘都需要是 azure 高级存储或 azure Ultra 磁盘存储。

SAP 工作负荷的功能矩阵如下所示：

| 功能| 评论| 备注/链接 | 
| --- | --- | --- | 
| OS 基本 VHD | 适合 | 所有系统 |
| 数据磁盘 | 适合 | 所有系统- [专门用于 SAP HANA](../../how-to-enable-write-accelerator.md) |
| SAP 全局传输目录 | YES | [支持](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 适合 | 所有系统 |
| 备份存储 | 适合 | 用于短期存储备份 |
| 共享/共享磁盘 | 不可用 | 需要 Azure 高级文件或第三方 |
| 复原 | LRS | 磁盘无可用的 GRS 或 ZRS |
| 延迟 | 低到中 | - |
| IOPS SLA | YES | - |
| 从线性到容量的 IOPS | 方括号中的半线性  | [托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/) |
| 每个磁盘的最大 IOPS | 20000 [依赖磁盘大小](https://azure.microsoft.com/pricing/details/managed-disks/) | 还要考虑 [VM 限制](../../sizes.md) |
| 吞吐量 SLA | YES | - |
| 吞吐量线性到容量 | 方括号中的半线性 | [托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA 认证 | YES | [专门为 SAP HANA](../../how-to-enable-write-accelerator.md) |
| 可能的磁盘快照 | YES | - |
| 可能的 Azure 备份 VM 快照 | YES | [写入加速器](../../how-to-enable-write-accelerator.md)缓存磁盘除外  |
| 成本 | MEDIUM | - |

Azure 高级存储不满足 SAP HANA 存储延迟 Kpi，其中包含 Azure 高级存储提供的通用缓存类型。 为了满足 SAP HANA 日志写入的存储延迟 Kpi，需要使用 Azure 写入加速器缓存，如 [启用写入加速器](../../how-to-enable-write-accelerator.md)一文中所述。 Azure 写入加速器受益于所有其他 DBMS 系统的事务日志写入和重做日志写入。 因此，建议在所有 SAP DBMS 部署中使用它。 对于 SAP HANA，Azure 写入加速器与 Azure 高级存储结合使用是必需的。



**摘要：** Azure 高级存储是建议用于 SAP 工作负荷的 Azure 存储类型之一。 此建议适用于非生产系统以及生产系统。 Azure 高级存储适合处理数据库工作负荷。 Azure 写入加速器的使用将显著提高 Azure 高级磁盘的写入延迟。 但是，对于具有高 IOPS 和吞吐率的 DBMS 系统，需要过度预配存储容量，或者需要使用 Linux 中的 Windows 存储空间或逻辑卷管理器等功能构建带区的条带集，这些条带集可为你提供所需的容量，同时还能获得最大的成本效益。


### <a name="azure-burst-functionality-for-premium-storage"></a>适用于高级存储的 Azure 猝发功能
对于容量较小或等于 512 GiB 的 Azure 高级存储磁盘，提供了突发功能。 磁盘突发如何工作的确切方式在 " [磁盘突发](../../linux/disk-bursting.md)" 一文中进行了介绍。 当您阅读本文时，您将了解在以下情况下，当 i/o 工作负荷低于磁盘的名义 IOPS 和吞吐量时，所产生的的 IOPS 和吞吐量的概念 (有关名义吞吐量的详细信息，请参阅 [托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)) 。 你将在当前使用情况与磁盘的名义值之间累计 IOPS 和吞吐量的增量。 猝发次数限制为最多30分钟。

可以在其中规划此突发功能的理想情况可能是包含不同 DBMS 的数据文件的卷或磁盘。 需要对这些卷执行 i/o 工作负荷，尤其是对于小到中等范围的系统，应如下所示：

- 从低到中等的读取工作负荷，因为数据理想的缓存在内存中，
- 数据库检查点或定期发出的保存点触发的突发写入数
- 在不通过存储快照执行备份的情况下，在连续流中读取的备份工作负荷
- 对于 SAP HANA，在实例重新启动后将数据加载到内存中

尤其是在工作负荷仅每秒处理几百个事务的小型 DBMS 系统上，此类突发功能对于存储事务或重做日志的磁盘或卷也很有用。 针对此类磁盘的预期工作负载如下所示：

- 由于应用程序发出的每个提交都可能触发 i/o 操作，因此，对依赖于工作负荷的磁盘的常规写入以及工作负荷的性质
- 在操作任务的情况下提高吞吐量的工作负荷，如创建或重新生成索引
- 执行事务日志或重做日志备份时，请阅读猝发


## <a name="azure-ultra-disk"></a>Azure Ultra 磁盘
Azure 超级磁盘为 Azure IaaS VM 提供高吞吐量、高 IOPS 和一贯低延迟的磁盘存储。 超磁盘的其他好处包括能够动态更改磁盘的 IOPS 和吞吐量以及工作负荷，而无需重新启动虚拟机 (VM) 。 超磁盘适用于数据密集型工作负荷，如 SAP DBMS 工作负荷。 超磁盘只能用作数据磁盘，不能用作存储操作系统的基本 VHD 磁盘。 建议将 Azure 高级存储用作基于 VHD 磁盘的使用。

创建超磁盘时，可以定义三个维度：

- 磁盘的容量。 范围是从 4 GiB 到 65536 GiB
- 磁盘的预配 IOPS。 不同的最大值适用于磁盘容量。 阅读有关更多详细 [信息的详细](../../linux/disks-types.md#ultra-disk) 信息
- 预配的存储带宽。 不同的最大带宽适用于磁盘的容量。 阅读有关更多详细 [信息的详细](../../linux/disks-types.md#ultra-disk) 信息

单个磁盘的开销取决于你可以为特定磁盘单独定义的三个维度。 


SAP 工作负荷的功能矩阵如下所示：

| 功能| 评论| 备注/链接 | 
| --- | --- | --- | 
| OS 基本 VHD | 不起作用 | - |
| 数据磁盘 | 适合 | 所有系统  |
| SAP 全局传输目录 | YES | [支持](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 适合 | 所有系统 |
| 备份存储 | 适合 | 用于短期存储备份 |
| 共享/共享磁盘 | 不可用 | 需要第三方 |
| 复原 | LRS | 磁盘无可用的 GRS 或 ZRS |
| 延迟 | 非常低 | - |
| IOPS SLA | YES | - |
| 从线性到容量的 IOPS | 方括号中的半线性  | [托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/) |
| 每个磁盘的最大 IOPS | 1200至160000 | 磁盘容量依赖 |
| 吞吐量 SLA | YES | - |
| 吞吐量线性到容量 | 方括号中的半线性 | [托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA 认证 | YES | - |
| 可能的磁盘快照 | 是 | - |
| 可能的 Azure 备份 VM 快照 | 是 | - |
| 成本 | 高于高级存储 | - |



**摘要：** Azure ultra 磁盘是适用于各种 SAP 工作负荷的低延迟的存储。 到目前为止，超磁盘只能与通过可用性区域 (区域部署) 部署的 Vm 组合使用。 超磁盘目前不支持存储快照。 对于所有其他存储，超磁盘不能用于基本 VHD 磁盘。 超磁盘非常适合于 i/o 工作负荷很大程度地波动的情况，并且你想要将部署的存储吞吐量或 IOPS 调整到存储工作负荷模式，而不是调整带宽和 IOPS 的最大使用量。


## <a name="azure-netapp-files-anf"></a>Azure NetApp 文件 (和) 
[Azure NetApp 文件](https://azure.microsoft.com/services/netapp/) 是 Microsoft 与 NetApp 之间协作的结果，旨在提供高性能的 AZURE 本机 NFS 和 SMB 共享。 重点是提供高带宽和低延迟存储，以实现 DBMS 部署方案，并且随着时间的推移，还可以通过 Azure 同时启用 NetApp 存储的典型操作功能。 NFS/SMB 共享以三个不同的服务级别提供，可区分存储吞吐量和价格。 [Azure NetApp 文件的服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)一文中介绍了服务级别。 对于不同类型的 SAP 工作负荷，强烈建议使用以下服务级别：

- SAP DBMS 工作负荷：性能，理想的超
- SAPMNT 共享：性能，理想的超
- 全局传输目录：性能，理想情况

> [!NOTE]
> 最小预配大小为4个称为容量池的 TiB 单位。 然后，在此容量池中创建卷。 而可以生成的最小卷为 100 GiB。 可以在 TiB 步骤中扩展容量池。 有关定价，请查看[Azure NetApp 文件定价](https://azure.microsoft.com/pricing/details/netapp/)一文

对于多个 SAP 工作负荷方案，当前支持和存储：

- 为 SAP 的全局传输目录提供 SMB 或 NFS 共享
- 如中所述，在高可用性方案中 sapmnt 共享：
    - [适用于 Windows 上的 Azure 虚拟机上的 SAP NetWeaver 高可用性，适用于 SAP 应用程序 (SMB) 的 Azure Vm](./high-availability-guide-windows-netapp-files-smb.md)
    - [带有适用于 SAP 应用程序的 Azure NetApp 文件的 SUSE Linux Enterprise Server 上 Azure VM 上的 SAP NetWeaver 的高可用性](./high-availability-guide-suse-netapp-files.md)
    - [适用于 sap NetWeaver 的 azure 虚拟机高可用性，适用于 SAP 应用程序的 Azure NetApp 文件 Red Hat Enterprise Linux](./high-availability-guide-rhel-netapp-files.md)
- 在[Azure 虚拟机存储配置 SAP HANA](./hana-vm-operations-storage.md)一文中所述，使用/hana/data 和/hana/log 卷和/或 nfs/hana/shared 卷的 nfs v2.0 共享和卷 SAP HANA 部署

> [!NOTE]
> 基于 NFS 或 SMB 共享的 Azure NetApp 文件不支持其他 DBMS 工作负荷。 如果要更改，将提供更新和更改。

在 Azure 高级存储中，如果需要遵守吞吐量中的一些最小数值，每 GB 的固定或线性吞吐量大小可能是一个问题。 这就是 SAP HANA 的情况。 借助和，此问题可能会比 Azure 高级磁盘更明显。 对于 Azure 高级磁盘，可以使用多个较小的磁盘，其中每个 GiB 都有相对较高的吞吐量，条带化可降低成本，并具有更高的容量。 此类条带化不适用于 NFS 或和上托管的 SMB 共享。 此限制导致了 overcapacity 的部署，如下所示：

- 例如，若要在和上托管的 NFS 卷上实现 250 MiB/秒的吞吐量，则需要部署超服务级别的 1.95 TiB 容量。 
- 若要实现 400 MiB/秒，需要部署 3.125 TiB 容量。 但你可能需要过度预配容量来实现卷所需的吞吐量。 容量的过度预配会影响较小 HANA 实例的定价。 
- 在 SAP/sapmnt 目录的和上使用 NFS 的空间中，你通常会使用 Azure NetApp 文件强制执行的最小容量为 100 GiB 到 150 GiB。 不过，客户体验表明，使用超高服务) 级别的 12.8 MiB/sec (的相关吞吐量可能不足，并且可能会对 SAP 系统的稳定性产生负面影响。 在这种情况下，客户可以通过增加/sapmnt 卷的数量来避免出现问题，因此，该卷提供了更多的吞吐量。

SAP 工作负荷的功能矩阵如下所示：

| 功能| 评论| 备注/链接 | 
| --- | --- | --- | 
| OS 基本 VHD | 不起作用 | - |
| 数据磁盘 | 适合 | 仅 SAP HANA  |
| SAP 全局传输目录 | YES | SMB 和 NFS |
| SAP sapmnt | 适合 | 仅适用于 Windows 的所有系统 SMB (仅) 或 NFS (Linux)  |
| 备份存储 | 适合 | - |
| 共享/共享磁盘 | YES | SMB 3.0、NFS v3 和 NFS v2。0 |
| 复原 | LRS | 磁盘无可用的 GRS 或 ZRS |
| 延迟 | 非常低 | - |
| IOPS SLA | YES | - |
| 从线性到容量的 IOPS | 严格线性  | 依赖于 [服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| 吞吐量 SLA | YES | - |
| 吞吐量线性到容量 | 方括号中的半线性 | 依赖于 [服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| HANA 认证 | YES | - |
| 可能的磁盘快照 | YES | - |
| 可能的 Azure 备份 VM 快照 | 是 | - |
| 成本 | 高于高级存储 | - |


和存储的其他内置功能：

- 执行卷快照的功能
- 从快照克隆和卷
- 从快照还原卷 (快照恢复) 

**摘要**： Azure NetApp 文件是用于部署 NFS 和 SMB 卷或共享的 HANA 认证低延迟存储。 存储提供了三种不同的服务级别，以线性方式为卷的每个 GiB 容量提供不同的吞吐量和 IOPS。 和存储支持使用备用节点部署 SAP HANA 扩展方案。 存储适合为/sapmnt 或 SAP 全局传输目录提供文件共享。 和存储提供了可用作本机 NetApp 功能的功能可用性。  



## <a name="azure-standard-ssd-storage"></a>Azure 标准 SSD 存储
与 Azure 标准 HDD 存储相比，Azure 标准 SSD 存储提供更好的可用性、一致性、可靠性和延迟。 它针对需要在低 IOPS 级别上具有一致性能的工作负荷进行了优化。 此存储是用于具有低 IOPS 和吞吐量要求的非生产 SAP 系统的最小存储空间。 SAP 工作负荷的功能矩阵如下所示：

| 功能| 评论| 备注/链接 | 
| --- | --- | --- | 
| OS 基本 VHD | 限制合适 | 非生产系统 |
| 数据磁盘 | 限制合适 | 某些非生产系统的 IOPS 和延迟需求较低 |
| SAP 全局传输目录 | 是 | 不支持 |
| SAP sapmnt | 限制合适 | 非生产系统 |
| 备份存储 | 适合 | - |
| 共享/共享磁盘 | 不可用 | 需要第三方 |
| 复原 | LRS、GRS | 磁盘无可用 ZRS |
| 延迟 | high | SAP 全局传输目录或生产系统的过高 |
| IOPS SLA | 是 | - |
| 每个磁盘的最大 IOPS | 500 | 与磁盘大小无关 |
| 吞吐量 SLA | 是 | - |
| HANA 认证 | 是 | - |
| 可能的磁盘快照 | YES | - |
| 可能的 Azure 备份 VM 快照 | YES | - |
| 成本 | LOW | - |



**摘要：** Azure 标准 SSD 存储是针对基本 VHD 的非生产 Vm 的最小建议，最小值是相对延迟不区分、/或低 IOPS 和吞吐率的最终 DBMS 部署。 此 Azure 存储类型不受支持，无法再托管 SAP 全局传输目录。 



## <a name="azure-standard-hdd-storage"></a>Azure 标准 HDD 存储
在2014年，azure 基础结构为 SAP NetWeaver 工作负荷认证时，Azure 标准 HDD 存储是唯一的存储类型。 在2014年，Azure 虚拟机的存储吞吐量较小且低。 因此，此存储类型能够满足需求。 存储非常适合于延迟的不区分工作负荷，在 SAP 空间中几乎不会遇到。 随着 Azure Vm 的增长吞吐量和这些 Vm 产生的工作负荷增加，此存储类型不会考虑到 SAP 方案的使用量。 SAP 工作负荷的功能矩阵如下所示：

| 功能| 评论| 备注/链接 | 
| --- | --- | --- | 
| OS 基本 VHD | 不适用 | - |
| 数据磁盘 | 不适用 | - |
| SAP 全局传输目录 | 是 | 不支持 |
| SAP sapmnt | 是 | 不支持 |
| 备份存储 | 适合 | - |
| 共享/共享磁盘 | 不可用 | 需要 Azure 文件或第三方 |
| 复原 | LRS、GRS | 磁盘无可用 ZRS |
| 延迟 | high | DBMS 使用情况、SAP 全局传输目录或 sapmnt/saploc 太高 |
| IOPS SLA | 是 | - |
| 每个磁盘的最大 IOPS | 500 | 与磁盘大小无关 |
| 吞吐量 SLA | 是 | - |
| HANA 认证 | 是 | - |
| 可能的磁盘快照 | YES | - |
| 可能的 Azure 备份 VM 快照 | YES | - |
| 成本 | LOW | - |



**摘要：** 标准 HDD 是只应用于存储 SAP 备份的 Azure 存储类型。 它仅应用作（而非非活动系统）的基本 VHD，如用于在此处查找数据的已停用系统。 但没有活动的开发、QA 或生产 Vm 应基于该存储。 数据库文件是否在该存储中托管


## <a name="azure-vm-limits-in-storage-traffic"></a>存储流量中的 Azure VM 限制
与本地方案相反，你选择的单个 VM 类型在存储带宽中扮演着重要的角色。 对于不同的存储类型，您需要考虑：

| 存储类型| Linux | Windows | 注释 |
| --- | --- | --- | --- |
| 标准 HDD | [Azure 中 Linux Vm 的大小](../../sizes.md) | [Azure 中 Windows Vm 的大小](../../sizes.md) | 可能很难触摸中型或大型 Vm 的存储限制 |
| 标准 SSD | [Azure 中 Linux Vm 的大小](../../sizes.md) | [Azure 中 Windows Vm 的大小](../../sizes.md) | 可能很难触摸中型或大型 Vm 的存储限制 |
| 高级存储 | [Azure 中 Linux Vm 的大小](../../sizes.md) | [Azure 中 Windows Vm 的大小](../../sizes.md) | 易于访问的 IOPS 或存储吞吐量 VM 限制与存储配置 |
| 超磁盘存储 | [Azure 中 Linux Vm 的大小](../../sizes.md) | [Azure 中 Windows Vm 的大小](../../sizes.md) | 易于访问的 IOPS 或存储吞吐量 VM 限制与存储配置 |
| Azure NetApp 文件 | [Azure 中 Linux Vm 的大小](../../sizes.md) | [Azure 中 Windows Vm 的大小](../../sizes.md) | 存储流量使用网络吞吐量带宽而不是存储带宽！ |

作为限制，你可以注意：

- VM 越小，可以附加的磁盘就越少。 这不适用于和。 由于你装载了 NFS 或 SMB 共享，因此你不会遇到与要附加的共享卷数的限制
- Vm 的 i/o 吞吐量和 IOPS 限制可能会超出高级存储磁盘和超磁盘
- 使用和，共享卷的流量占用了 VM 的网络带宽，而不是存储带宽
- 使用双数字 TiB 容量空间中的大型 NFS 卷，从单个 VM 访问此类卷的吞吐量将基于与共享卷交互的单个会话的 Linux 限制进行达到平稳。 

随着 SAP 系统生命周期中的 Azure Vm 大小的增加，你应该评估新的和更大的 VM 类型的 IOPS 和存储吞吐量限制。 在某些情况下，将存储配置调整为 Azure VM 的新功能也可能有意义。 


## <a name="striping-or-not-striping"></a>条带或未条带化
将多个 Azure 磁盘的条带集创建到一个更大的卷中，可以将各个磁盘的 IOPS 和吞吐量累积到一个卷中。 它仅用于 Azure 标准存储和 Azure 高级存储。 Azure Ultra 磁盘：可在其中配置与磁盘容量无关的吞吐量和 IOPS，无需使用条带集。 不能对基于 NFS 或 SMB 的共享卷进行条带化。 由于 Azure 高级存储吞吐量和 IOPS 的非线性性质，你可以预配更小的容量，该容量与大型单个 Azure 高级存储磁盘的 IOPS 和吞吐量相同。 这是使用 Azure 高级存储以较低成本实现较高吞吐量或 IOPS 的方法。 例如：

- 跨两个 P15 高级存储磁盘条带化，可获得吞吐量 
- 250 MiB/秒。此类卷将具有 512 GiB 的容量。 如果希望有单个磁盘提供每秒 250 MiB 吞吐量，则需要选择具有2个 TiB 容量的 P40 磁盘。 
- 或者，你可以通过将四个 P10 高级存储磁盘条带化，将四个高级存储磁盘与 512 GiB 的总体容量分隔，来实现 400 MiB/秒的吞吐量。 如果要使用每秒至少具有 500 MiB 吞吐量的单个磁盘，则需要选择具有8个 TiB 的 P60 高级存储磁盘。 由于成本计算或高级存储的容量接近线性，因此可以通过使用条带化来节约成本。

某些规则需要遵循条带化：

- 不应使用 VM 中配置的存储，因为 Azure 存储空间使数据保持冗余
- 应用条带集的磁盘大小必须相同

跨多个较小的磁盘进行条带化是使用 Azure 高级存储实现良好性价比的最佳方式。 了解条带化有一些额外的部署和管理开销。

有关特定的条带大小建议，请参阅不同 DBMS 的文档，例如 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)。




## <a name="next-steps"></a>后续步骤
阅读以下文章：

- [部署适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 的注意事项](./dbms_guide_general.md)
- [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)
 