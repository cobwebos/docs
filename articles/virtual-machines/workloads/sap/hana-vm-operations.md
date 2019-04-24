---
title: Azure 上的 SAP HANA 基础结构配置和操作 | Microsoft Docs
description: Azure 虚拟机上部署的 SAP HANA 系统的操作指南。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: acccc553c5b63b2acd0f9793b0397b25145449dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60477374"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Azure 上的 SAP HANA 基础结构配置和操作
本文指导如何配置 Azure 基础结构和操作 Azure 本机虚拟机 (Vm) 部署的 SAP HANA 系统。 本文还包含 M128s VM SKU 的 SAP HANA 横向扩展的配置信息。 本文并不旨在取代标准 SAP 文档，其中包括以下内容：

- [SAP 管理指南](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP 安装指南](https://service.sap.com/instguides)
- [SAP 说明](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>必备组件
要使用本指南，需要具备以下 Azure 组件的基础知识：

- [Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure 网络和虚拟网络](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure 存储](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

若要了解有关 Azure 上的 SAP NetWeaver 和其他 SAP 组件的详细信息，请参阅 [Azure 文档](https://docs.microsoft.com/azure/)的 [Azure 上的 SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) 部分。

## <a name="basic-setup-considerations"></a>基本设置注意事项
以下各节介绍了在 Azure VM 上部署 SAP HANA 系统的基本设置注意事项。

### <a name="connect-to-azure-virtual-machines"></a>连接到 Azure 虚拟机
如中所述[Azure 虚拟机规划指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)，两种基本方法可用来连接到 Azure Vm:

- 连接通过 internet 和公共终结点或 JumpBox VM 上运行 SAP HANA 的 VM。
- 通过 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) 或 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 进行连接。

生产方案需要通过 VPN 或 ExpressRoute 建立的站点到站点连接。 对于送入使用 SAP 软件的位置的生产方案的非生产方案也需要此类型的连接。 下图显示了跨站点连接的示例：

![跨站点连接](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>选择 Azure VM 类型
Azure VM 类型以用于生产方案所示[IAAS 的 SAP 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)。 对于非生产方案提供了广泛的本机 Azure VM 类型。

>[!NOTE]
> 对于非生产方案，使用中列出的 VM 类型[SAP 说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533)。 对于生产方案使用 Azure Vm 后，请检查 SAP HANA 认证虚拟机在 sap 发布[认证 IaaS 平台列表](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。

若要部署在 Azure 中的 Vm，请使用：

- Azure 门户。
- Azure PowerShell cmdlet。
- Azure CLI。

你还可以部署通过在 Azure VM 服务上完成的已安装的 SAP HANA 平台[SAP 云平台](https://cal.sap.com/)。 有关安装过程的信息，请参阅[部署 SAP S/4HANA 或 Azure 上的 BW/4HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)。 有关的自动化的信息，请参阅[此 GitHub 文章](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)。

### <a name="choose-an-azure-storage-type"></a>选择 Azure 存储类型
Azure 提供了适用于运行 SAP HANA 的 Azure Vm 的两种类型的存储：  

- 标准硬盘驱动器 (Hdd)
- 高级固态硬盘 (Ssd)

若要了解有关这些磁盘类型的信息，请参阅[选择磁盘类型](../../windows/disks-types.md)。

Azure 提供两种部署方法的 Vhd 在 Azure 标准存储和高级存储上。 如果总体方案允许，请利用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)部署。

有关存储类型及其 IOPS 和存储吞吐量的 Sla 的列表，请参阅[有关托管磁盘的 Azure 文档](https://azure.microsoft.com/pricing/details/managed-disks/)。

### <a name="configure-the-storage-for-azure-virtual-machines"></a>配置 Azure 虚拟机的存储

您可能永远不会关心 I/O 子系统和及其功能因为设备供应商已确保已针对 SAP HANA 满足最低存储要求。 您自己构建的 Azure 基础结构，需要注意的一些这些要求。 您还应了解以下各节中建议的配置要求。 想在其中配置虚拟机的情况下为 SAP HANA 上运行，您可能需要：

- 启用最少为 1 MB 的 I/O 大小为 250 MB/秒的 /hana/log 上的读/写卷。
- 启用至少 400MB/秒为 16 MB 到 64 MB 的 I/O 大小的 /hana/data 的读取的活动。
- 启用至少 250MB/秒 16 MB 到 64 MB 的 I/O 大小的 /hana/data 的写入活动。

低存储延迟对于 DBMS 系统至关重要，即使 DBMS，SAP HANA 等保留内存中数据也是如此。 存储中的关键路径通常围绕 DBMS 系统的事务日志写入。 但是，写入保存点或加载内存中数据后还崩溃恢复可能非常重要之类的操作。 

对 /hana/data 和 /hana/log 卷使用 Azure 高级磁盘是必需的。 若要实现的 /hana/log 和 /hana/data 作为 sap 所需的最小吞吐量，请使用 mdadm 或逻辑卷管理器 (LVM) 通过多个 Azure 高级存储磁盘构建 RAID 0。 此外使用 RAID 卷作为 /hana/data 和 /hana/log 卷。 我们建议使用 RAID 0 以下的条带大小：

- 64 KB 或者/hana/数据为 128 KB
- 32 KB/hana/log

> [!NOTE]
> 不需要使用 RAID 卷，因为 Azure 高级和标准存储保留 VHD 的三个映像配置任何冗余级别。 使用 RAID 卷是纯粹配置卷提供足够 I/O 吞吐量。

多个 RAID 下面的 Azure Vhd 是从 IOPS 和存储吞吐量端积累的。 如果您把 3 x P30 Azure 高级存储磁盘的 RAID 0，它提供三次的 IOPS 和三次单个 Azure 高级存储 P30 磁盘的存储吞吐量。

以下缓存建议的 SAP HANA 假定 I/O 特征：

- 没有针对 HANA 数据文件几乎不需要任何读取工作负荷。 HANA 实例，或者在数据加载到 HANA 时的重启后，例外情况是大型 I/o。 执行 HANA 数据库备份后，也可能出现大量针对数据文件的读取 I/O。 结果是，读取缓存没有意义，因为，在大多数情况下，所有数据文件必须读取卷完全。
- HANA 写入点和 HANA 故障恢复会产生大量针对数据文件的写入。 写入保存点是异步的不会阻止任何用户事务。 在故障恢复期间写入数据对性能要严格要求，以便系统再次快速响应。 崩溃恢复应而不是发生异常情况。
- 几乎不会从 HANA 重做文件进行任何读取。 例外是大型 I/o，当您执行事务日志备份、 崩溃恢复或 HANA 实例的重新启动阶段。  
- 针对 SAP HANA 重做日志文件的主要负载是写入。 根据工作负荷的性质，你可以 I/o 最小可为 4 KB，或在其他情况下，I/o 的 1 MB 或更多的大小。 针对 SAP HANA 重做文件的写入延迟对性能要很高要求。
- 必须以可靠方式的磁盘上保留所有写入。

由于这些观察到的 I/O 模式由 SAP HANA，而使用到 Azure 高级存储的不同卷将缓存设置：

- **/ hana/数据**:无缓存。
- **/ hana/log**:不缓存，与 M 系列 Vm （请参阅本文中的更多更高版本） 的异常。
- **hana/共享**:读取缓存。


此外，请注意大小或决定 VM 时的总体 VM I/O 吞吐量。 中记录了虚拟机存储吞吐量总体[内存优化的虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)。

#### <a name="linux-io-scheduler-mode"></a>Linux I/O 计划程序模式
Linux 提供多种不同的 I/O 计划模式。 从 Linux 供应商和 SAP 常见的建议是设置离开磁盘卷的 I/O 计划程序模式**cfq**模式设置为**noop**模式。 有关详细信息，请参阅[SAP 说明 #1984798](https://launchpad.support.sap.com/#/notes/1984787)。 


#### <a name="storage-solution-with-write-accelerator-for-azure-m-series-virtual-machines"></a>使用 azure 写入加速器的存储解决方案 M 系列虚拟机
 写入加速器目前发布了一项 Azure 功能，以独占方式推出的 Azure M 系列 Vm。 功能的目的是改善针对 Azure 高级存储的写入 I/O 延迟。 在 SAP HANA 中，预期只能对 /hana/log 卷使用写入加速器。 出于此原因，必须更改到目前为止所示的配置。 主要更改是 /hana/data 和 /hana/log 之间做出划分，以便对仅 /hana/log 卷使用写入加速器。 

> [!IMPORTANT]
> 适用于 Azure 的 SAP HANA 认证 M 系列虚拟机是独占的写入加速器/hana/日志卷。 因此，在 Azure 上生产场景 SAP HANA 部署 M 系列虚拟机应为其配置的用于写入加速器日志卷。

> [!NOTE]
> 对于生产场景，请在 [IAAS 的 SAP 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中查看特定的 VM 类型是否受 SAP HANA 的支持。

下表显示了建议的配置。

| VM SKU | RAM | 最大 VM I/O<br /> throughput | /hana/data | /hana/log | /hana/shared | /root 卷 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1,000 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1,000 GiB | 1,000 MB/秒 | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1,750 GiB | 1,000 MB/秒 | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2,000 GiB | 2,000 MB/秒 |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3,800 GiB | 2,000 MB/秒 | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

检查建议的不同卷的存储吞吐量是否满足你想要运行的工作负荷。 如果工作负荷要求较高的量 /hana/data 和 /hana/log，增加 Azure 高级存储 Vhd 的数目。 调整大小与比列出增加 IOPS 更多 Vhd 的卷和 Azure 虚拟机类型的限制范围内的 I/O 吞吐量。

只能配合 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)使用写入加速器。 至少，必须将构成 /hana/log 卷的 Azure 高级存储磁盘部署为托管磁盘。

有了 Azure 高级存储每个写入加速器可以支持的 VM 的 Vhd 的限制。 当前限制为：

- M128xx 为 16 个 Vhd VM。
- M64xx 为 8 个 VM。
- 4 个 Vhd M32xx VM。

有关如何启用写入加速器的详细信息，请参阅[写入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)。

有关详细信息和限制写入加速器，请参阅相同的文档。


#### <a name="cost-conscious-azure-storage-configuration"></a>节约成本的统一的 Azure 存储配置
下表显示了客户通常用于在 Azure VM 上托管 SAP HANA 的 VM 类型的配置。 可能有一些不满足所有最低标准的 SAP HANA 的 VM 类型。 此外可能有一些不正式支持的 SAP HANA 的 SAP 的 VM 类型。 到目前为止，这些 Vm 已正常执行，对于非生产方案。 

> [!NOTE]
> 对于生产场景，请在 [IAAS 的 SAP 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中查看特定的 VM 类型是否受 SAP HANA 的支持。


| VM SKU | RAM | 最大 VM I/O<br /> throughput | /hana/data and /hana/log<br /> 使用 LVM 或 mdadm 的条带化 | /hana/shared | /root 卷 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 432 GiB | 1200 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2,000 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GiB | 1,000 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1,000 GiB | 1,000 MB/秒 | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1,750 GiB | 1,000 MB/秒 | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2,000 GiB | 2,000 MB/秒 |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3,800 GiB | 2,000 MB/秒 | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


对于较小的 VM 类型使用 3 个 x P20 过大的卷中给定的空间建议方面建议的磁盘[SAP TDI 存储白皮书](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)。 对表中显示所选的 SAP HANA 提供足够的磁盘吞吐量。 如果需要更改 **/hana/备份**卷已调整大小以保留两倍内存卷的备份，可随意进行调整。 

检查建议的不同卷的存储吞吐量是否满足你想要运行的工作负荷。 如果工作负荷要求较高的量 /hana/data 和 /hana/log，增加 Azure 高级存储 Vhd 的数目。 调整大小与比列出增加 IOPS 更多 Vhd 的卷和 Azure 虚拟机类型的限制范围内的 I/O 吞吐量。 

> [!NOTE]
> 以前的配置未能从中获益[Azure 虚拟机单一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)因为它使用混合使用 Azure 高级存储和 Azure 标准存储。 选择所选内容的目的是优化成本。 作为 Azure 标准存储 (Sxx) 若要使 VM 配置符合 Azure 的单一 VM SLA 列出表中选择所有磁盘的高级的存储。


> [!NOTE]
> 磁盘配置建议针对 SAP 使其基础结构提供商的最低要求。 在实际客户部署和工作负荷方案中，这些建议并未提供足够的功能，在某些情况下。 例如，客户 HANA 对重新启动后，需要更快地重新加载的数据或备份配置所需存储到更高的带宽。 其他情况下包括的 /hana/log 5,000 IOPS 没有足够的特定工作负荷。 使用这些建议作为起点，并根据工作负荷的要求进行调整。
>  

### <a name="set-up-azure-virtual-networks"></a>设置 Azure 虚拟网络
如果已通过站点到站点连接到 Azure 通过 VPN 或 Azure ExpressRoute，您必须至少一个 Azure 虚拟网络通过 VPN 或 ExpressRoute 线路的虚拟网关连接。 在简单部署中，可以过托管 SAP HANA 实例的 Azure 虚拟网络的子网中部署虚拟网关。 

若要安装 SAP HANA，请创建 Azure 虚拟网络中两个附加的子网。 一个子网托管 VM 以运行 SAP HANA 实例。 另一个子网运行 JumpBox 或管理虚拟机托管 SAP HANA Studio、 其他管理软件或您的应用程序软件。

> [!IMPORTANT]
> 配置[Azure 网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)在 SAP 应用程序和 DBMS 层中的 SAP NetWeaver-之间的通信路径，Hybris-或 S/4HANA 的基于 SAP 系统不受支持。 此限制是出于功能和性能的原因。 SAP 应用程序层和 DBMS 层之间的通信路径必须是直接的。 限制不包括[应用程序安全组 (ASG) 和网络安全组 (NSG) 规则](https://docs.microsoft.com/azure/virtual-network/security-overview)如果这些 ASG 和 NSG 规则允许直接通信路径。 
>
> 网络虚拟设备不支持的其他方案中是： 
>
> - 表示 Linux Pacemaker 的 Azure Vm 之间的通信路径群集节点和 SBD 设备，如中所述[针对 SAP 应用程序的 SUSE Linux Enterprise Server 上的 Azure Vm 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)。
> - Azure Vm 和 Windows Server 横向扩展文件服务器之间的通信路径设置中所述的最多[在 Azure 中使用的文件共享在 Windows 故障转移群集上群集 SAP ASCS/SCS 实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)。 
>
> 通信路径中的网络虚拟设备可以轻松地两倍的两个通信合作伙伴之间的网络延迟。 它们还可以限制在 SAP 应用程序层和 DBMS 层之间的关键路径中的吞吐量。 在某些客户方案中，网络虚拟设备可能会导致 Pacemaker Linux 群集失败。 这些是到其 SBD 设备通过网络虚拟设备通信 Linux Pacemaker 群集节点之间的通信的情况。  
> 

> [!IMPORTANT]
> 另一种设计的*不*支持为到不同 Azure 虚拟网络都不是 SAP 应用程序层和 DBMS 层的分离[对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)彼此。 我们建议通过使用不同的 Azure 虚拟网络中而不是 Azure 虚拟网络的子网隔离的 SAP 应用程序层和 DBMS 层。 
>
>如果你决定不按照建议操作，而是将两个层隔离到不同虚拟网络，必须为两个虚拟网络[对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。 
>
> 请注意，网络两个之间的流量[对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)Azure 虚拟网络可能会发生传输成本。 SAP 应用程序层和 DBMS 层之间交换包含数万亿字节的大数据量。 如果 SAP 应用程序层和 DBMS 层与两个对等互连的 Azure 虚拟网络之间，可能会累积大量成本。 

安装 VM 以运行 SAP HANA 时，VM 需要：

- 安装两个虚拟 Nic。 一个 NIC 连接到管理子网。 另一个 NIC 从本地网络或其他网络连接到 Azure VM 中的 SAP HANA 实例。
- 为两个虚拟 NIC 部署静态专用 IP 地址。

> [!NOTE]
> 将通过 Azure 的静态 IP 地址分配意味着若要将其分配给单独的虚拟 Nic。 不要将来宾 OS 中的静态 IP 地址分配给虚拟 nic。 某些 Azure 服务，例如 Azure 备份依赖于这一事实，在最主要的虚拟 NIC 设置 DHCP 而不属于静态 IP 地址。 有关详细信息，请参阅[排查 Azure 虚拟机备份](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)。 若要将多个静态 IP 地址分配到 VM，请为 VM 分配多个虚拟 Nic。
>
>

对于持久的部署，在 Azure 中创建的虚拟数据中心网络体系结构。 此体系结构建议的分离到单独的 Azure 虚拟网络连接到本地的 Azure 虚拟网络网关。 此单独的虚拟网络托管离开到的本地或 internet 的所有流量。 通过使用这种方法，可以部署软件审核和日志中此单独的中心虚拟网络在 Azure 中输入虚拟数据中心的流量。 这样一来，您有一个虚拟网络托管所有的软件和配置与 ingoing 和传出流量发送到 Azure 部署相关联。


虚拟数据中心的方法和相关的 Azure 虚拟网络设计的详细信息，请参阅： 

- [Azure 虚拟数据中心：网络角度来看](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)。
- [Azure 虚拟数据中心和企业控制平面](https://docs.microsoft.com/azure/architecture/vdc/)。


>[!NOTE]
>使用中心虚拟网络和辐射虚拟网络之间流动的通信量[Azure 虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)可能会发生其他[成本](https://azure.microsoft.com/pricing/details/virtual-network/)。 根据这些成本，您可能需要进行运行严格的中心辐射型网络设计和运行多个之间取得平衡[Azure ExpressRoute 网关](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)您连接到辐射以绕过虚拟网络对等互连。 
>
> Azure ExpressRoute 网关引入额外[成本](https://azure.microsoft.com/pricing/details/vpn-gateway/)过。 您还可能会遇到第三方软件，用于记录、 审核和监视网络流量的其他的费用。 请考虑数据交换创建的其他 ExpressRoute 网关和软件许可证的成本与对等互连的虚拟网络的成本。 而不是虚拟网络的隔离单元作为使用子网，你可能决定在一个虚拟网络中的微分段。


有关可用于分配 IP 地址的不同方法的概述，请参阅[IP 地址类型和在 Azure 中的分配方法](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)。 

对于运行 SAP HANA 的 Vm，请使用分配的静态 IP 地址。 原因是 HANA 的某些配置属性引用 IP 地址。

[Azure Nsg](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)用于定向路由到 SAP HANA 实例或 JumpBox 的流量。 Nsg，并最终[应用程序安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)与 SAP HANA 子网和管理子网相关联。

下图显示的遵循中心辐射型虚拟网络体系结构的 SAP HANA 的大致部署架构的概述：

![SAP HANA 的大致部署架构](media/hana-vm-operations/hana-simple-networking.PNG)

部署，而站点到站点连接不在 Azure 中的 SAP HANA，SAP HANA 实例从公共 internet 进行防护和隐藏在正向代理。 在此基本方案中，部署依赖于 Azure 内置的 DNS 服务来解析主机名。 对于使用面向公众的 IP 地址的更复杂部署，Azure 内置的 DNS 服务尤为重要。 使用 Azure Nsg 并[Azure 网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)监视路由从 internet 到在 Azure 中应用 Azure 虚拟网络体系结构。 

下图显示了如何将 SAP HANA 部署而无需在一个中心辐射型虚拟网络体系结构中的站点到站点连接的大致架构：
  
![未建立站点到站点连接的大致 SAP HANA 部署架构](media/hana-vm-operations/hana-simple-networking2.PNG)
 

有关如何从中心辐射型虚拟网络体系结构无 internet 访问控制和监视使用 Azure 网络虚拟设备的另一个说明，请参阅[部署高度可用的网络虚拟设备](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configure-azure-infrastructure-for-sap-hana-scale-out"></a>配置 SAP HANA 横向扩展的 Azure 基础结构
Microsoft 有一个经过认证的 SAP HANA 横向扩展配置的 M 系列 VM SKU。 VM 类型 M128s 是经过认证的最多 16 节点的扩展。 有关在 Azure Vm 上的 SAP HANA 横向扩展认证中的更改，请参阅[认证 IaaS 平台列表](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。

用于部署 Azure Vm 中的横向扩展配置的最低 OS 版本是：

- SUSE Linux 12 SP3。
- Red Hat Linux 7.4。

16 节点向外缩放认证：

- 一个节点是主节点。
- 最多 15 个节点作为辅助角色节点。

>[!NOTE]
>在 Azure VM 扩展部署中，不能使用备用节点。
>

有两个原因，为什么不能配置备用节点：

- Azure 目前没有本机 NFS 服务。 因此，必须使用第三方功能的帮助配置 NFS 共享。
- 没有任何第三方 NFS 配置可以与他们在 Azure 上部署的解决方案的 SAP HANA 满足存储延迟条件。

因此，不能共享 /hana/data 和 /hana/log 卷。 不共享单个节点的这些卷会阻止使用横向扩展配置中的 SAP HANA 备用节点。

下图显示单个节点的基本设计了横向扩展配置中：

![单个节点的横向扩展基本设计](media/hana-vm-operations/scale-out-basics.PNG)

SAP HANA 横向扩展的 VM 节点基本配置如下所示：

- /Hana/shared，为您构建基于 SUSE Linux 12 SP3 的高度可用的 NFS 群集。 此群集将托管横向扩展配置和 SAP NetWeaver 或 BW/4HANA 中心服务的 /hana/shared NFS 的共享。 有关如何构建此类配置的信息，请参阅[SUSE Linux Enterprise Server 上的 Azure Vm 上的 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)。
- 所有其他磁盘卷不在不同节点之间共享，而不基于 NFS。 在本文后面提供了安装配置和使用非共享的 /hana/data 和 /hana/log 的横向扩展 HANA 安装的步骤。

>[!NOTE]
>图中显示的高可用性 NFS 群集目前仅受 SUSE Linux 的支持。 以后会通告对基于 Red Hat 的高可用性 NFS 解决方案的支持。

调整大小的卷的节点是向上扩展，除了 /hana/shared 的一样。 下表显示 M128s VM sku 的建议的大小和类型。

| VM SKU | RAM | 最大 VM I/O<br /> throughput | /hana/data | /hana/log | /root 卷 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2,000 GiB | 2,000 MB/秒 |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


检查建议的不同卷的存储吞吐量是否满足你想要运行的工作负荷。 如果工作负荷要求较高的量 /hana/data 和 /hana/log，增加 Azure 高级存储 Vhd 的数目。 调整大小与比列出增加 IOPS 更多 Vhd 的卷和 Azure 虚拟机类型的限制范围内的 I/O 吞吐量。 也适用于构成 /hana/log 卷的磁盘的写入加速器。
 

有关用于定义为横向扩展 hana/共享卷的大小作为每四个辅助角色节点的单个辅助角色节点的内存大小的公式，请参阅[SAP HANA TDI 存储要求](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)。

假设要在 SAP HANA 横向扩展认证的 M128s Azure VM 约 2 tb 的内存，SAP 建议将概括为：

- 1 个主节点和多达四个辅助角色节点，/hana/shared 卷的大小为 2 TB。
- 对于一个主节点和 5 到 8 个辅助角色节点，/hana/shared 卷的大小为 4 TB。
- 对于一个主节点和 9 到 12 个辅助角色节点，/hana/shared 卷的大小为 6 TB。
- 对于一个主节点和 12 到 15 辅助角色节点，/hana/shared 卷的大小是 8 TB。

向外缩放 SAP HANA VM 的单节点配置的图形中显示的其他重要的设计是使用子网配置的虚拟网络。 SAP 强烈建议从 HANA 节点之间的通信的客户端和应用程序面向流量分离。 

要实现此目标，请将两个不同的虚拟 Nic 附加到 VM，如图所示。 这两个虚拟 Nic 位于不同子网，并有两个不同的 IP 地址。 然后，你通过使用 Nsg 或用户定义的路由控制的流量使用路由规则。

特别是在 Azure 中，没有方法和方法，以强制实施质量的服务和特定的虚拟 Nic 上的配额。 因此，面向于客户端和应用程序和在节点内部通信的分离未打开任何优先于另一个流量流的机会。 相反，分离保持中屏蔽横向扩展配置的内节点通信的安全性的度量值。  

>[!IMPORTANT]
>SAP 强烈建议您分离到客户端和应用程序端和在节点内部流量在这篇文章中所述的网络流量。 我们建议你落实到位，一种体系结构中的上一个图形所示。
>

下图显示了从网络的角度来看的最低要求的网络体系结构：

![单个节点的横向扩展基本设计](media/hana-vm-operations/scale-out-networking-overview.PNG)

到目前为止支持的限制是除了一个主节点的 15 个辅助角色节点。

下图显示了从存储角度的存储体系结构：


![单个节点的横向扩展基本设计](media/hana-vm-operations/scale-out-storage-overview.PNG)

/Hana/shared 卷位于高度可用的 NFS 共享配置。 所有其他驱动器会本地装载到单个 Vm。 

### <a name="highly-available-nfs-share"></a>高可用性 NFS 共享
到目前为止，高度可用的 NFS 群集使用 SUSE Linux 仅。 安装程序的信息，请参阅[SUSE Linux Enterprise Server 上的 Azure Vm 上的 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)。 如果不具有运行 SAP HANA 实例的 Azure 虚拟网络外部的任何其他 HANA 配置共享的 NFS 群集，请将其安装在同一虚拟网络。 将其安装在自己的子网，并确保不是所有的任意流量可以访问子网。 相反，限制为执行到 /hana/shared 卷流量的 IP 地址的 VM 到该子网的流量。

与将 /hana/shared 流量路由的 HANA 横向扩展 VM 的虚拟 NIC 相关，则推荐是：

- 因为流向 /hana/shared 为中等，路由通过分配给客户端网络中的最小配置的虚拟 NIC。
- 最终，到 hana/共享的流量，将部署在其中部署 SAP HANA 横向扩展配置的虚拟网络中的第三个子网。 分配托管该子网中的第三个虚拟 NIC。 到 NFS 共享的流量使用第三个虚拟 NIC 和关联的 IP 地址。 然后，可以应用单独的访问和路由规则。

>[!IMPORTANT]
>可能会通过路由之间具有以向外缩放的方式部署 SAP HANA 的 Vm 和任何情况下都高度可用的 NFS 的网络流量[网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)或类似的虚拟设备。 Azure Nsg 是没有此类设备。 检查从运行的 SAP HANA 的 Vm 共享你的路由规则，以确保访问高度可用的 NFS 时转向网络虚拟设备或类似的虚拟设备。
> 

如果你想要共享的 SAP HANA 配置之间高度可用的 NFS 群集，请将所有这些 HANA 配置移到同一虚拟网络。 
 

### <a name="install-an-sap-hana-scale-out-in-azure"></a>在 Azure 中安装 SAP HANA 横向扩展
若要安装扩展 SAP 配置，请按照下列常规步骤：

- 部署新的或新的 Azure 虚拟网络基础结构。
- 使用 Azure 托管的高级存储卷部署新的 Vm。
- 部署新的或调整现有高度可用的 NFS 群集。
- 调整网络路由，以确保，例如，在节点内部 Vm 之间的通信不通过路由[网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)。 同样适用于虚拟机和高度可用的 NFS 群集之间的流量。
- 安装 SAP HANA 主节点。
- 调整 SAP HANA 主控节点的配置的参数。
- 继续进行安装的 SAP HANA 辅助节点。

#### <a name="install-sap-hana-in-a-scale-out-configuration"></a>横向扩展配置中安装 SAP HANA
部署 Azure VM 基础结构，并完成所有其他准备工作。 现在，若要安装 SAP HANA 横向扩展配置，请按照下列步骤：

- 安装 SAP HANA 主控节点根据 SAP 的文档。
- *安装之后，更改 global.ini 文件并添加参数 basepath_shared = no 到 global.ini*。 此参数使 SAP HANA 横向扩展而无需共享的 /hana/data 和 /hana/log 卷中运行的节点之间。 有关详细信息，请参阅[SAP 说明 #2080991](https://launchpad.support.sap.com/#/notes/2080991)。
- 更改 global.ini 参数后，重新启动 SAP HANA 实例。
- 添加其他工作节点。 有关详细信息，请参阅[SAP HANA 管理指南](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html)。 指定在安装期间或之后通过使用，例如，本地 hdblcm SAP HANA 节点间通信的内部网络。 有关详细信息，请参阅[SAP 说明 #2183363](https://launchpad.support.sap.com/#/notes/2183363)。 

以下这个安装例程，您安装的横向扩展配置使用非共享的磁盘运行 /hana/data 和 /hana/log。 Hana/共享卷放置在高可用性 NFS 共享。


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>Azure 虚拟机的 SAP HANA 动态分层 2.0

除了 Azure M 系列 Vm 上的 SAP HANA 认证，Microsoft Azure 上也支持 SAP HANA 动态分层 2.0 (DT 2.0)。 有关 SAP HANA 动态分层文档的链接，请参阅"链接到 DT 2.0 文档"更高版本中的这篇文章。 在安装此产品或操作系统，例如，通过 SAP HANA 考核中心内为 Azure VM 中的没有区别，但几个重要事项是强制性的 Azure 上的官方支持。 以下各节所述这些关键点。 

SAP HANA DT 2.0 不支持 SAP BW 或 S4HANA。 主要用例现在是本机 HANA 应用程序。


### <a name="overview"></a>概述

下图概述了 DT 2.0 支持的 Microsoft Azure 上。 请按照这些强制性要求，以符合官方认证：

- DT 2.0 必须安装在专用 Azure 虚拟机上。 它可能无法运行 SAP HANA 在其中运行在同一 VM 上。
- 必须在相同的 Azure 虚拟网络中部署 SAP HANA 和 DT 2.0 虚拟机。
- 必须启用加速网络 Azure 部署的 SAP HANA 和 DT 2.0 虚拟机。
- DT 2.0 虚拟机的存储类型必须是 Azure 高级存储。
- 多个 Azure 磁盘必须附加到 DT 2.0 VM。
- 创建软件 RAID 或条带化的卷，通过 LVM 或 mdadm，所需的所有 Azure 磁盘使用条带化。

以下部分提供了详细说明。

![SAP HANA DT 2.0 体系结构概述](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>适用于 SAP HANA DT 2.0 的专用 Azure VM

Azure IaaS 上的专用 VM 上仅支持 DT 2.0。 DT 2.0 不允许其中 HANA 实例运行在同一个 Azure VM 上运行。 最初，两个 VM 类型可用于运行 SAP HANA DT 2.0:

- M64-32ms 
- E32sv3 

VM 类型说明，请参阅[内存优化的虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)。

提供有关卸载暖数据以节约成本 DT 2.0 的基本理念最好使用相应的 VM 大小。 没有为可能的组合严格的规则。 这取决于特定的客户工作负载。

下表显示了建议的配置。

| SAP HANA VM 类型 | DT 2.0 VM 类型 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


使用受支持的 DT 2.0 Vm，如 M64 32ms 和 E32sv3，SAP HANA 认证 M 系列 Vm 的所有组合都都有可能。


### <a name="azure-networking-and-sap-hana-dt-20"></a>Azure 网络和 SAP HANA DT 2.0

在专用 VM 上安装 DT 2.0 需要 DT 2.0 VM 和 SAP HANA VM 具有 10 GB 最小值之间的网络吞吐量。 因此，它是，必须将置于同一 Azure 虚拟网络中的所有 Vm 并启用 Azure 加速网络。

有关 Azure 加速网络的详细信息，请参阅[创建具有加速网络的 Linux 虚拟机](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)。

### <a name="vm-storage-for-sap-hana-dt-20"></a>SAP HANA DT 2.0 VM 存储

DT 2.0 的最佳实践指南，根据最小的磁盘 IO 吞吐量为每个物理核心 50 MB/秒。 查看支持 DT 2.0 的两个 Azure VM 类型的规范的最大磁盘 VM 的 IO 吞吐量限制是：

- **E32sv3**: 768 MB/秒，非缓存，这意味着每个物理核心 48 MB/秒的比率
- **M64 32ms**:1,000 MB/秒，非缓存，这意味着每个物理核心 62.5 MB/秒的比率

将多个 Azure 磁盘附加到 DT 2.0 VM，并通过使用在 OS 级别条带化以实现每个 VM 的磁盘吞吐量的最大限制创建一个软件 RAID 是必需的。 单个 Azure 磁盘不能提供的吞吐量才能达到最大 VM 限制。 Azure 高级存储是强制性要求运行 DT 2.0。 

- 有关可用的 Azure 磁盘类型的详细信息，请参阅[适用于 Azure IaaS Windows Vm 选择磁盘类型](../../windows/disks-types.md)。
- 有关如何创建通过 mdadm 软件 RAID 的详细信息，请参阅[Linux 上配置软件 RAID](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)。
- 有关如何配置 LVM 创建条带化的卷的最大吞吐量的详细信息，请参阅[在 Azure 中 Linux VM 上配置 LVM](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)。

根据大小要求有不同的选项来访问 VM 的最大吞吐量。 以下是针对每个 DT 2.0 VM 类型以实现 VM 的吞吐量上限可能的数据卷磁盘配置。 E32sv3 VM 被视为一种较小的工作负荷的条目级别。 如果不足够快，则可能有必要调整 M64 32ms 到 VM 的大小。

因为 M64 32ms VM 有大量的内存，IO 负载可能无法达到的限制，尤其是对于读取密集型工作负荷。 更少的磁盘中条带集可能是足够具体取决于特定于客户的工作负荷。 为了安全起见，以下的磁盘配置选择以保证最大吞吐量。


| VM SKU | 磁盘配置 1 | 磁盘配置 2 | 磁盘配置 3 | 磁盘配置 4 | 磁盘配置 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3.5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2.5 TB | 6 x P15 -> 1.5 TB | 


尤其是如果工作负荷是读取密集型的则数据库软件的数据卷的建议开启 Azure 主机缓存"只读"设置可能会大大提高 IO 性能。 对于事务日志的 Azure 主机磁盘缓存必须是"none。 

日志卷的大小，我们建议的起始点是 15%的数据大小的启发式方法。 若要创建日志卷，请使用不同的 Azure 磁盘类型，具体取决于成本和吞吐量要求。 日志卷，高 I/O 吞吐量是必需的。 

如果使用的 VM 类型 M64 32ms，我们建议您启用[写入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)。 Write Accelerator 是一项 Azure 功能，提供最佳的磁盘写入延迟的事务日志。 它是仅适用于 M 系列。 有一些事项需要注意，如每个 VM 类型的磁盘的最大数目。 写入加速器的详细信息，请参阅[启用写入加速器](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)。


下表显示了几个示例来帮助你调整日志卷的大小。

| 数据卷的大小和磁盘类型 | 日志卷和磁盘类型配置 1 | 日志卷和磁盘类型配置 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2.5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1.5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


类似于 SAP HANA 横向扩展，在 /hana/shared 目录必须共享 SAP HANA VM 和 DT 2.0 VM 之间。 我们建议使用专用的 Vm，为高度可用的 NFS 服务器使用相同的体系结构与 SAP HANA 横向扩展。 若要提供共享的备份卷，请使用完全相同的设计。 但它将由您来决定是否需要高可用性，或只需使用具有足够的存储容量的专用的机充当备份服务器。



### <a name="links-to-dt-20-documentation"></a>DT 2.0 文档的链接 

- [SAP HANA 动态分层安装和更新指南](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [SAP HANA 动态分层教程和资源](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA 动态分层概念证明](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2.0 SPS 02 Dynamic Tiering 增强功能](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>在 Azure VM 上部署 SAP HANA 的操作
以下各节介绍了在 Azure VM 上部署 SAP HANA 系统的一些相关操作。

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Azure VM 上的备份和还原操作
以下文档介绍如何备份和还原 SAP HANA 部署：

- [SAP HANA 备份概述](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA 文件级备份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA 存储快照基准](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>启动并重启包含 SAP HANA 的 VM
Azure 公有云的一个突出特性是只需为计算时间付费。 例如，在关闭正在运行的 SAP HANA 的 VM，则计费仅对存储成本在该时间段。 在初始部署中为 VM 指定静态 IP 地址时，可利用另一特性。 重启具有 SAP HANA 的 VM 时，VM 使用其先前的 IP 地址重启。 


### <a name="use-saprouter-for-sap-remote-support"></a>使用 SAProuter 实现 SAP 远程支持
如果必须在本地位置与 Azure 之间的站点到站点连接，并且正在运行 SAP 组件，您可能已在运行 SAProuter。 在这种情况下，请执行以下步骤实现远程支持：

- 在 SAProuter 配置中保留托管 SAP HANA 的 VM 的专用和静态 IP 地址。
- 配置托管 HANA VM 的子网的 NSG，允许流量通过 TCP/IP 端口 3299。

如果你连接到 Azure 通过 internet 和包含 SAP HANA 的 vm 没有 SAP 路由器，安装该组件。 在管理子网中某个独立的 VM 中安装 SAProuter。 

下图显示了在不建立站点到站点连接且不包含 SAProuter 的情况下部署 SAP HANA 的大致架构：

![未建立站点到站点连接且不包含 SAProuter 的大致 SAP HANA 部署架构](media/hana-vm-operations/hana-simple-networking3.PNG)

请务必在单独的 VM 并不在 JumpBox VM 中安装 SAProuter。 该独立 VM 必须具有静态 IP 地址。 若要将 SAProuter 连接到 SAP 托管的 SAProuter，请联系 SAP 获取 IP 地址。 由 SAP 托管的 SAProuter 是在 VM 上安装的 SAProuter 实例相对应。 使用 SAP 提供的 IP 地址配置 SAProuter 实例。 在配置设置中，唯一必需的端口是 TCP 端口 3299。

有关如何设置和维护远程支持通过 SAProuter 连接的详细信息，请参阅[SAP 文档](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)。

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Azure 本机 Vm 上的 SAP HANA 的高可用性
如果您运行 SUSE Linux Enterprise Server for SAP Applications 12 SP1 或更高版本，可以建立包含 STONITH 设备的 Pacemaker 群集。 使用这些设备使用设置使用同步复制与 HANA 系统复制和自动故障转移的 SAP HANA 配置。 有关设置过程的详细信息，请参阅[的 Azure 虚拟机的 SAP HANA 高可用性指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)。
