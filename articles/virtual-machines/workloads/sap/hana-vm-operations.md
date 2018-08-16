---
title: Azure 上的 SAP HANA 基础结构配置和操作 | Microsoft Docs
description: Azure 虚拟机上部署的 SAP HANA 系统的操作指南。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: juergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2018
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 98c7bd5daf3b84499e8e31c0a7a2da612834b83e
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521976"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Azure 上的 SAP HANA 基础结构配置和操作
本文档提供有关配置 Azure 基础结构以及操作 Azure 本机虚拟机 (VM) 上部署的 SAP HANA 系统的指导。 本文档还包含有关 M128s VM SKU 的 SAP HANA 横向扩展的配置信息。 本文档并不旨在取代标准 SAP 文档，后者包括以下内容：

- [SAP 管理指南](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP 安装指南](https://service.sap.com/instguides)
- [SAP 说明](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>先决条件
要使用本指南，需要具备以下 Azure 组件的基础知识：

- [Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure 网络和虚拟网络](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure 存储](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

若要了解有关 Azure 上的 SAP NetWeaver 和其他 SAP 组件的详细信息，请参阅 [Azure 文档](https://docs.microsoft.com/azure/)的 [Azure 上的 SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) 部分。

## <a name="basic-setup-considerations"></a>基本设置注意事项
以下各节介绍了在 Azure VM 上部署 SAP HANA 系统的基本设置注意事项。

### <a name="connect-into-azure-virtual-machines"></a>连接到 Azure 虚拟机
如 [Azure 虚拟机规划指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中所述，可通过两种基本方法连接到 Azure VM：

- 通过跳接 VM 或运行 SAP HANA 的 VM 上的 Internet 和公共终结点进行连接。
- 通过 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) 或 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 进行连接。

生产方案需要通过 VPN 或 ExpressRoute 建立的站点到站点连接。 对于送入使用 SAP 软件的生产方案的非生产方案，也需要此类型的连接。 下图显示了跨站点连接的示例：

![跨站点连接](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>选择 Azure VM 类型
[IAAS 的 SAP 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中列出了可用于生产方案的 Azure VM 类型。 对于非生产方案，可使用更广泛的本机 Azure VM 类型。

>[!NOTE]
> 对于非生产方案，请使用 [SAP 说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533) 中列出的 VM 类型。 若要将 Azure VM 用于生产场景，请在 SAP 发布的[已认证 IaaS 平台列表](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中查看已通过 SAP HANA 认证的 VM。

通过使用以下方法在 Azure 中部署 VM：

- Azure 门户。
- Azure PowerShell cmdlet。
- Azure CLI。

还可通过 [SAP 云平台](https://cal.sap.com/)在 Azure VM 服务上部署整个已安装的 SAP HANA 平台。 [在 Azure 上部署 SAP S/4HANA 或 BW/4HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) 中介绍了安装过程，也可以使用[此处](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)所述的自动化功能完成安装。

### <a name="choose-azure-storage-type"></a>选择 Azure 存储类型
对于正在运行 SAP HANA 的 Azure VM，Azure 提供两种适用的存储类型：

- [Azure 标准存储](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure 高级存储](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure 针对 Azure 标准和高级存储上的 VHD 提供两种部署方法。 如果总体方案允许，请利用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)部署。

有关存储类型的列表及其 IOPS 和存储吞吐量方面的 SLA，请查看[有关托管磁盘的 Azure 文档](https://azure.microsoft.com/pricing/details/managed-disks/)。

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>配置 Azure 虚拟机的存储

自从购买本地用的 SAP HANA 设备以来，我们从未考虑过 I/O 子系统及其功能， 因为设备供应商会确保满足 SAP HANA 的最低存储要求。 在自行构建 Azure 基础结构时，我们也应该注意其中的一些要求。 此外，应了解后续部分中建议的配置要求。 配置用于运行 SAP HANA 的虚拟机时，也应该这样做。 为了满足某些要求，需要做到以下几点：

- 在最小大小为 1 MB I/O、速率为 250 MB/秒的 /hana/log 上启用读/写卷
- 为 16 MB 和 64 MB I/O 大小的 /hana/data 启用至少 400 MB/秒的读取活动
- 为 16 MB 和 64 MB I/O 大小的 /hana/data 启用至少 250MB/秒的写入活动

如果低存储延迟对于 DBMS 系统至关重要（即使对于 SAP HANA 等 DBMS 系统也是如此），请将数据保留在内存中。 存储中的关键路径通常围绕 DBMS 系统的事务日志写入。 同时，在故障恢复之后写入保存点或加载内存中数据的操作可能也很关键。 因此，必须对 **/hana/data** 和 **/hana/log** 卷使用 Azure 高级磁盘。 为了达到 SAP 所需的最低 **/hana/log** 和 **/hana/data** 吞吐量，需要使用 MDADM 或 LVM 基于多个 Azure 高级存储磁盘构建 RAID 0。 将 RAID 卷用作 **/hana/data** 和 **/hana/log** 卷。 对于 RAID 0 的条带大小，建议使用：

- 64 KB 或 128 KB 的 /hana/data
- 32 KB 的 /hana/log

> [!NOTE]
> 不需要使用 RAID 卷配置任何冗余级别，因为 Azure 高级和标准存储保留 VHD 的三个映像。 使用 RAID 卷的原因仅仅是为了配置可提供足够 I/O 吞吐量的卷。

在 RAID 下面累积多个 Azure VHD 可以提高 IOPS 和存储吞吐量。 因此，如果使用 3 倍的 P30 Azure 高级存储磁盘构建 RAID 0，则单个 Azure 高级存储 P30 磁盘的 IOPS 和存储吞吐量会提高 3 倍。

以下缓存建议假设列出的 SAP HANA 的 I/O 特征如下所示：

- 几乎没有任何针对 HANA 数据文件的读取工作负荷。 重启 HANA 实例后或者在将数据载入 HANA 时出现的大型 I/O 例外。 执行 HANA 数据库备份后，也可能出现大量针对数据文件的读取 I/O。 因此，大多数情况下读取缓存没有意义，因为在大多数情况下，需要完全读取所有数据文件卷。
- HANA 写入点和 HANA 故障恢复会产生大量针对数据文件的写入。 写入保存点是异步进行的，不阻止任何用户事务。 在故障恢复期间写入数据对性能要严格要求，以便系统再次快速响应。 但是，故障恢复更确切地说应该是异常情况
- 几乎不会从 HANA 重做文件进行任何读取。 执行事务日志备份、故障恢复或在 HANA 实例重启阶段出现的大型 I/O 例外。  
- 针对 SAP HANA 重做日志文件的主要负载是写入负载。 根据工作负载的性质，I/O 可以小至 4 KB，也可达到 1 MB 或更大。 针对 SAP HANA 重做文件的写入延迟对性能要很高要求。
- 需要以一种可靠的方式在磁盘上保留所有写入

对于这些观察到的 SAP HANA 的 I/O 模式，使用 Azure 高级存储的不同卷的缓存应设置为如下形式：

- **/hana/data** - 无缓存
- **/hana/log** - 无缓存 - M 系列例外（详见本文稍后部分）
- **/hana/shared** - 读取缓存


在确定 VM 大小或决定 VM 时，还要考虑总体 VM I/O 吞吐量。 [内存优化虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)一文中记录了总体 VM 存储吞吐量。

#### <a name="cost-conscious-azure-storage-configuration"></a>高性价比的 Azure 存储配置
下表显示了客户通常用于在 Azure VM 上托管 SAP HANA 的 VM 类型的配置。 其中可能有些 VM 类型无法满足 SAP HANA 的所有最低标准。 但到目前为止，这些 VM 在非生产场景中的表现似乎不错。 

> [!NOTE]
> 对于生产场景，请在 [IAAS 的 SAP 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中查看特定的 VM 类型是否受 SAP HANA 的支持。


| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data and /hana/log<br /> 使用 LVM 或 MDADM 进行条带化 | /hana/shared | /root 卷 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 GiB | 1200 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2000 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GiB | 1000 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1000 GiB | 1000 MB/秒 | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 GiB | 1000 MB/秒 | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 GiB | 2000 MB/秒 | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


根据 [SAP TDI 存储白皮书](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，对于采用 3 x P20 配置的较小 VM 类型，建议的磁盘空间大于建议的卷空间。 但是，表中显示的选项旨在为 SAP HANA 提供足够的磁盘吞吐量。 如果需要改用 **/hana/backup** 卷（该卷的大小适合保留相当于两倍内存卷的备份），请任意调整配置。   
检查建议的不同卷的存储吞吐量是否满足所要运行的工作负荷。 如果工作负荷要求对 **/hana/data** 和 **/hana/log** 使用更高规格的卷，则需要增加 Azure 高级存储 VHD。 对于包含的 VHD 数目比所列数目更多的卷，调整其大小会在 Azure 虚拟机类型的限制范围内增大 IOPS 和 I/O 吞吐量。 

> [!NOTE]
> 上述配置并不会受益于 [Azure 虚拟机单一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)，因为此配置混合使用了 Azure 高级存储和 Azure 标准存储。 但是，这种选择可以优化成本。


#### <a name="azure-storage-configuration-to-benefit-for-meeting-single-vm-sla"></a>有利于满足单一 VM SLA 的 Azure 存储配置
如果想要受益于 [Azure 虚拟机单一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)，需要专门使用 Azure 高级存储 VHD。

> [!NOTE]
> 对于生产场景，请在 [IAAS 的 SAP 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中查看特定的 VM 类型是否受 SAP HANA 的支持。

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data and /hana/log<br /> 使用 LVM 或 MDADM 进行条带化 | /hana/shared | /root 卷 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E16v3 | 128 GiB | 384 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E32v3 | 256 GiB | 768 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| E64v3 | 443 GiB | 1200 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| GS5 | 448 GiB | 2000 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| M64ls | 512 GiB | 1000 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M64s | 1000 GiB | 1000 MB/秒 | 2 x P30 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/秒 | 3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/秒 | 5 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |


根据 [SAP TDI 存储白皮书](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，对于采用 3 x P20 配置的较小 VM 类型，建议的磁盘空间大于建议的卷空间。 但是，表中显示的选项旨在为 SAP HANA 提供足够的磁盘吞吐量。 如果需要改用 **/hana/backup** 卷（该卷的大小适合保留相当于两倍内存卷的备份），请任意调整配置。  
检查建议的不同卷的存储吞吐量是否满足所要运行的工作负荷。 如果工作负荷要求对 **/hana/data** 和 **/hana/log** 使用更高规格的卷，则需要增加 Azure 高级存储 VHD。 对于包含的 VHD 数目比所列数目更多的卷，调整其大小会在 Azure 虚拟机类型的限制范围内增大 IOPS 和 I/O 吞吐量。 


#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>对 Azure M 系列虚拟机使用包含 Azure 写入加速器的存储解决方案
Azure 写入加速器是专门针对 M 系列 VM 推出的一项功能。 顾名思义，该功能的目的是改善针对 Azure 高级存储执行的写入操作的 I/O 延迟。 在 SAP HANA 中，预期只能对 **/hana/log** 卷使用写入加速器。 因此，到目前为止所述的配置都需要更改。 主要更改是在 **/hana/data** 与 **/hana/log** 之间做出划分，以便仅针对 **/hana/log** 卷使用 Azure 写入加速器。 

> [!IMPORTANT]
> Azure M 系列虚拟机的 SAP HANA 认证要求中规定，Azure 写入加速器只能用于 **/hana/log** 卷。 因此，在 Azure M 系列虚拟机上的生产场景 SAP HANA 部署中，应该配置 **/hana/log** 卷使用的 Azure 写入加速器。  

> [!NOTE]
> 对于生产场景，请在 [IAAS 的 SAP 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中查看特定的 VM 类型是否受 SAP HANA 的支持。

建议的配置如下：

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data | /hana/log | /hana/shared | /root 卷 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/秒 | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/秒 | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/秒 | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

检查建议的不同卷的存储吞吐量是否满足所要运行的工作负荷。 如果工作负荷要求对 **/hana/data** 和 **/hana/log** 使用更高规格的卷，则需要增加 Azure 高级存储 VHD。 对于包含的 VHD 数目比所列数目更多的卷，调整其大小会在 Azure 虚拟机类型的限制范围内增大 IOPS 和 I/O 吞吐量。

只能配合 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)使用 Azure 写入加速器。 因此，至少需要将构成 **/hana/log** 卷的 Azure 高级存储磁盘部署为托管磁盘。

Azure 写入加速器在每个 VM 中支持的 Azure 高级存储 VHD 数目有限制。 当前限制为：

- M128xx VM 限制为 16 个 VHD
- M64xx VM 限制为 8 个 VHD
- M32xx VM 限制为 4 个 VHD

在[写入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)一文中可以找到有关如何启用 Azure 写入加速器的更多详细说明。

此文中还介绍了 Azure 写入加速器的详细信息和限制。



### <a name="set-up-azure-virtual-networks"></a>设置 Azure 虚拟网络
当通过 VPN 或 ExpressRoute 与 Azure 建立站点到站点连接时，必须至少有一个 Azure 虚拟网络已通过虚拟网关连接到 VPN 或 ExpressRoute 线路。 在简单部署中，也可以将虚拟网关部署在托管 SAP HANA 实例的 Azure 虚拟网络 (VNet) 的子网中。 若要安装 SAP HANA，需要在 Azure 虚拟网络中另外创建两个子网。 一个子网托管 VM 以运行 SAP HANA 实例。 另一子网运行 Jumpbox 或管理 VM，以托管 SAP HANA Studio、其他管理软件或应用程序软件。

安装 VM 以运行 SAP HANA 时，VM 需要：

- 已安装两个虚拟 NIC：其中一个 NIC 连接到管理子网，另一个从本地或其他网络连接到 Azure VM 中的 SAP HANA 实例。
- 为两个虚拟 NIC 部署静态专用 IP 地址。

但是，对于持久性部署，需要在 Azure 中创建虚拟数据中心网络体系结构。 此体系结构建议在独立的 Azure VNet 中隔离连接到本地的 Azure VNet 网关。 此独立 VNet 应托管发往本地或 Internet 的所有流量。 使用此方法可以部署软件用于审核和记录进入此独立中心 VNet 中 Azure 虚拟数据中心的流量。 因此，有一个 VNet 托管传入和传出 Azure 部署的流量相关的所有软件和配置。

文章 [Azure 虚拟数据中心：网络透视](https://docs.microsoft.com/azure/networking/networking-virtual-datacenter)和 [Azure 虚拟数据中心和企业控制平面](https://docs.microsoft.com/azure/architecture/vdc/)提供了有关虚拟数据中心方法和相关Azure VNet 设计的详细信息。


>[!NOTE]
>使用 [Azure VNet 对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)在中心 VNet 与分支 VNet 之间传送的流量会产生额外的[成本](https://azure.microsoft.com/pricing/details/virtual-network/)。 鉴于这些成本，可能需要考虑在运行严格的中心和分支网络设计，以及在运行多个连接到“分支”的 [Azure ExpressRoute 网关](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)以绕过 VNet 对等互连之间做出取舍。 但是，Azure ExpressRoute 网关也会造成额外的[成本](https://azure.microsoft.com/pricing/details/vpn-gateway/)。 此外，用于网络流量日志记录、审核和监视的第三方软件也会产生额外的成本。 根据在一端通过 VNet 对等互连进行数据交换产生的成本，以及附加 Azure ExpressRoute 网关和附加软件许可证产生的成本，可以决定使用子网作为隔离单元而不使用 VNet，在一个 VNet 中进行微型分段。


有关分配 IP 地址的不同方法的概述，请参阅 [Azure 中的 IP 地址类型和分配方法](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)。 

对于运行 SAP HANA 的 VM，应该使用分配的静态 IP 地址。 原因是 HANA 的某些配置属性引用 IP 地址。

[Azure 网络安全组 (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 用于定向路由到 SAP HANA 实例或 Jumpbox 的流量。 NSG 与最终的[应用程序安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)关联到 SAP HANA 子网和管理子网。

下图显示了遵循中心和分支 VNet 体系结构的 SAP HANA 的大致部署架构概况：

![SAP HANA 的大致部署架构](media/hana-vm-operations/hana-simple-networking.PNG)

若要在不建立站点到站点连接的情况下在 Azure 中部署 SAP HANA，仍需在公共 Internet 中屏蔽 SAP HANA 实例，并将其隐藏在转发代理的后面。 在此基本方案中，部署依赖于 Azure 内置的 DNS 服务来解析主机名。 对于使用面向公众的 IP 地址的更复杂部署，Azure 内置的 DNS 服务尤为重要。 使用 Azure NSG 和 [Azure NVA](https://azure.microsoft.com/solutions/network-appliances/) 来控制与监视从 Internet 到 Azure 中 Azure VNet 体系结构的路由。 下图显示了在不建立站点到站点连接的情况下用于在中心和分支 VNet 体系结构中部署 SAP HANA 的大致架构：
  
![未建立站点到站点连接的大致 SAP HANA 部署架构](media/hana-vm-operations/hana-simple-networking2.PNG)
 

可以在[部署高可用性网络虚拟设备](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)一文中找到有关如何在没有中心和分支 VNet 体系结构的情况下，使用 Azure NVA 控制和监视来自 Internet 的访问的说明。


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>为 SAP HANA 横向扩展配置 Azure 基础结构
Microsoft 提供一个已通过 SAP HANA 横向扩展配置认证的 M 系列 VM SKU。 VM 类型 M128s 经认证最多可横向扩展到 16 个节点。 有关 Azure VM 上的 SAP HANA 横向扩展认证的更改，请查看[认证的 IaaS 平台列表](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。

用于在 Azure VM 中部署横向扩展配置的最低 OS 版本为：

- SUSE Linux 12 SP3
- Red hat Linux 7.4

16 节点横向缩放认证

- 一个节点是主节点
- 最多 15 个节点是工作节点

>[!NOTE]
>在 Azure VM 横向扩展部署中无法使用备用节点
>

无法配置备用节点的原因有两个：

- Azure 目前没有本机 NFS 服务。 因此需要借助第三方功能配置 NFS 共享。
- 使用 Azure 上部署的解决方案，没有任何第三方 NFS 配置能够满足 SAP HANA 的存储延迟条件。

因此，无法共享 **/hana/data** 和 **/hana/log** 卷。 无法共享单个节点的这些卷会阻止在横向扩展配置中使用 SAP HANA 备用节点。

因此，横向扩展配置中单个节点的基本设计如下所示：

![单个节点的横向扩展基本设计](media/hana-vm-operations/scale-out-basics.PNG)

SAP HANA 横向扩展的 VM 节点基本配置如下所示：

- 对于 **/hana/shared**，基于 SUSE Linux 12 SP3 构建高可用性 NFS 群集。 此群集托管横向扩展配置以及 SAP NetWeaver 或 BW/4HANA 中心服务的 **/hana/shared** NFS 共享。 [SUSE Linux Enterprise Server 中 Azure VM 上的 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)一文阐述了如何构建此类配置
- 其他所有磁盘卷**不会**在不同的节点之间共享，并且**不是**基于 NFS。 此文档进一步提供了有关包含非共享 **/hana/data** 和 **/hana/log** 的横向扩展 HANA 安装的安装配置与步骤。

>[!NOTE]
>图中显示的高可用性 NFS 群集目前仅受 SUSE Linux 的支持。 以后会通告对基于 Red Hat 的高可用性 NFS 解决方案的支持。

调整节点卷大小的过程与纵向扩展相同，但 **/hana/shared** 除外。 对于 M128s VM SKU，建议的大小和类型如下：

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data | /hana/log | /root 卷 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


检查建议的不同卷的存储吞吐量是否满足所要运行的工作负荷。 如果工作负荷要求对 **/hana/data** 和 **/hana/log** 使用更高规格的卷，则需要增加 Azure 高级存储 VHD。 对于包含的 VHD 数目比所列数目更多的卷，调整其大小会在 Azure 虚拟机类型的限制范围内增大 IOPS 和 I/O 吞吐量。 另外，将 Azure 写入加速器应用到构成 **/hana/log** 卷的磁盘。
 
文档 [SAP HANA TDI 存储要求](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)中指定了一个公式，该公式将横向扩展的 **/hana/shared** 卷的大小定义为每 4 个工作节点中单个工作节点的内存大小。

假设我们使用内存大约为 2 TB 且通过 SAP HANA 横向扩展认证的 M128s Azure VM，SAP 建议概括如下：

- 1 个主节点和最多 4 个工作节点，/hana/shared 卷的大小需是 2 TB。 
- 1 个主节点和 5 到 8 个工作节点，**/hana/shared** 的大小应是 4 TB。 
- 1 个主节点和 9 到 12 个工作节点，**/hana/shared** 的大小需是 6 TB。 
- 1 个主节点和 12 到 15 个工作节点，需要提供 8 TB 大小的 **/hana/shared** 卷。

横向扩展 SAP HANA VM 的单节点配置示意图中显示的另一项重要设计是 VNet，最好采用子网配置。 SAP 强烈建议将面向客户端/应用程序的流量与 HANA 节点之间的通信进行隔离。 如图所示，可以通过将两个不同的 vNIC 附加到 VM 来实现此目的。 这两个 vNIC 位于不同的子网中，并使用两个不同的 IP 地址。 然后，使用 NSG 或用户定义的路由通过路由规则控制流量流。

具体而言，在 Azure 中，没有任何方法可以在特定的 vNIC 上强制实施服务质量和配额。 因此，通过面向客户端/应用程序的流量与节点内部通信的隔离，没有任何机会使一个流量流优先于另一个流量流。 隔离保持为一种安全度量，用于屏蔽横向扩展配置的节点内部通信。  

>[!IMPORTANT]
>SAP 强烈建议根据本文档中所述，对客户端/应用程序端和节点内部流量进行网络流量隔离。 因此，强烈建议部署最后一幅示意图中所示的体系结构。
>

从网络角度看，最低要求的网络体系结构如下所示：

![单个节点的横向扩展基本设计](media/hana-vm-operations/scale-out-networking-overview.PNG)

目前支持的限制是在一个主节点的基础上额外添加 15 个工作节点。

从存储角度看，存储体系结构如下所示：


![单个节点的横向扩展基本设计](media/hana-vm-operations/scale-out-storage-overview.PNG)

**/hana/shared** 卷位于高可用性 NFS 共享配置中。 其他所有驱动器“本地”装载到各个 VM。 

### <a name="highly-available-nfs-share"></a>高可用性 NFS 共享
高可用性 NFS 群集目前仅适用于 SUSE Linux。 文档 [SUSE Linux Enterprise Server 上 Azure VM 中的 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)介绍了设置方法。 如果不与 Azure VNet 外部的、运行 SAP HANA 实例的其他任何 HANA 配置共享 NFS 群集，请将群集安装在同一 VNet 中。 请将群集安装在其自身的子网中，并确保不是所有任意流量都可以访问该子网， 而是将发往该子网的流量限制为向 **/hana/shared** 卷执行流量的 VM 的 IP 地址。

对于应该路由 **/hana/shared** 流量的 HANA 横向扩展 VM 的 vNIC，建议为：

- 由于发往 **/hana/shared** 的流量大小适中，因此请在最低配置中通过分配到客户端网络的 vNIC 路由流量
- 最后，对于发往 **/hana/shared** 的流量，请在部署 SAP HANA 横向扩展配置的 VNet 中部署第三个子网，并分配该子网中托管的第三个 vNIC。 针对发往 NFS 共享的流量使用第三个 vNIC 和关联的 IP 地址。 然后，可以应用单独的访问和路由规则。

>[!IMPORTANT]
>无论在何种情况下，都不可以通过 [NVA](https://azure.microsoft.com/solutions/network-appliances/) 或类似虚拟设备来路由以横向扩展部署的 SAP HANA 所在 VM 与高可用性 NFS 之间的网络流量。 而 Azure NSG 并不属于此类设备。 检查路由规则，以确保从运行 SAP HANA 的 VM 访问高可用性 NFS 共享时，绕过 NVA 或类似虚拟设备。
> 

如果想要在 SAP HANA 配置之间共享高可用性 NFS 群集，请将所有这些 HANA 配置移到同一 VNet 中。 
 

### <a name="installing-sap-hana-scale-out-n-azure"></a>在 Azure 中安装 SAP HANA 横向扩展
安装横向扩展 SAP 配置时，需要执行以下大致步骤：

- 部署新的或改编现有的 Azure VNet 基础结构
- 使用 Azure 托管的高级存储卷部署新 VM
- 部署新的或改编现有的高可用性 NFS 群集
- 改编网络路由，确保不会通过 [NVA](https://azure.microsoft.com/solutions/network-appliances/) 路由 VM 之间的节点内部通信。 对于 VM 与高可用性 NFS 群集之间的流量采用相同的方式。
- 安装 SAP HANA 主节点。
- 编辑 SAP HANA 主节点的配置参数
- 继续安装 SAP HANA 工作节点

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>安装采用横向扩展配置的 SAP HANA
部署 Azure VM 基础结构并完成其他所有准备工作后，需要通过以下步骤安装 SAP HANA 横向扩展配置：

- 根据 SAP 文档安装 SAP HANA 主节点
- **安装完成后，需要更改 global.ini 文件，并将参数“basepath_shared = no”添加到 global.ini**。 此参数使 SAP HANA 能够以横向扩展配置运行，且无需在节点之间使用“共享”的 **/hana/data** 和 **/hana/log** 卷。 [SAP 说明 #2080991](https://launchpad.support.sap.com/#/notes/2080991) 中提供了详细信息。
- 更改 global.ini 参数后，重启 SAP HANA 实例
- 添加其他工作节点。 另请参阅 <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>。 在安装期间或之后，使用 local hdblcm 等命令为 SAP HANA 节点内部通信指定内部网络。 有关更详细的文档，请参阅 [SAP 说明 #2183363](https://launchpad.support.sap.com/#/notes/2183363)。 

完成此设置例程后，安装的横向扩展配置将使用非共享磁盘来运行 **/hana/data** 和 **/hana/log**。 **/hana/shared** 卷将放置在高可用性 NFS 共享上。


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>适用于 Azure 虚拟机的 SAP HANA Dynamic Tiering 2.0

除 Azure M 系列 VM 上的 SAP HANA 认证之外，Microsoft Azure 还支持 SAP HANA Dynamic Tiering 2.0（请参阅 SAP HANA Dynamic Tiering 文档链接进行详细了解）。 虽然安装该产品或运行该产品没有任何区别（例如，通过 Azure 虚拟机中的 SAP HANA），但要获得 Azure 上的官方支持，有几点是必不可少的。 这些要点如下所述。 在整篇文章中，使用缩写“DT 2.0”代替 Dynamic Tiering 2.0 全名。

SAP BW 或 S4HANA 不支持 SAP HANA Dynamic Tiering 2.0。 现在的主要用例是本机 HANA 应用程序。


### <a name="overview"></a>概述

下图概述了有关 Microsoft Azure 支持的 DT 2.0。 还有一套强制性要求，必须遵循符合官方认证：

- DT 2.0 必须安装在专用 Azure 虚拟机上。 它可能无法在运行 SAP HANA 的同一 VM 上运行
- SAP HANA 和 DT 2.0 VM 必须部署在同一 Azure Vnet 中
- 在启用 Azure 加速网络的情况下，才能部署 SAP HANA 和 DT 2.0 VM
- DT 2.0 虚拟机的存储类型必须是 Azure 高级存储
- 多个 Azure 磁盘必须附加到 DT 2.0 VM
- 必须通过跨 Azure 磁盘的带区来创建软件 RAID /带区卷（通过 lvm 或 mdadm）

后续部分会对此进行详细说明。

![SAP HANA DT 2.0 体系结构概述](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>适用于 SAP HANA DT 2.0 的专用 Azure VM

在 Azure IaaS 上，DT 2.0 仅在专用 VM 上受支持。 不允许在运行 HANA 实例的同一 Azure VM 上运行 DT 2.0。 最初可以使用两种 VM 类型来运行 SAP HANA DT 2.0：

M64-32ms 和 E32sv3 

请在[此处](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)查看 VM 类型说明

鉴于 DT 2.0 的基本理念，卸载“warm”数据以节省成本，最好使用相应的 VM 大小。 但对于可能的组合，并没有严格的规定。 这取决于特定的客户工作负载。

推荐的配置是：

| SAP HANA VM 类型 | DT 2.0 VM 类型 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


SAP HANA 认证 M 系列 VM 与受支持的 DT 2.0 Vm (M64-32ms, E32sv3) 的所有组合都是可能的。


### <a name="azure-networking-and-sap-hana-dt-20"></a>Azure 网络和 SAP HANA DT 2.0

如果要在专用 VM 上安装 DT 2.0，DT 2.0 VM 和 SAP HANA VM 之间的网络吞吐量必须至少为 10 Gb。 因此，必须将所有 VM 放在相同的 Azure Vnet 中，并启用 Azure 加速网络。

有关 Azure 加速网络的更多信息，请参阅[此处](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)

### <a name="vm-storage-for-sap-hana-dt-20"></a>适用于 SAP HANA DT 2.0 的 VM 存储

根据 DT 2.0 最佳做法指南，每个物理核心的磁盘 IO 吞吐量最低应为 50 MB/秒。 查看 DT 2.0 支持的两种 Azure VM 类型的规范，其中之一将看到 VM 的最大磁盘 IO 吞吐量限制：

- E32sv3：768 MB/秒（未缓存）表示每个物理核心的比率为 48 MB /秒
- M64-32ms：1000 MB/秒（未缓存）表示每个物理核心的比率为 62.5 MB /秒

必须将多个 Azure 磁盘连接到 DT 2.0 VM 并在 OS 级别创建软件 RAID（带区），以实现每个 VM 的磁盘吞吐量的最大限制。 在这方面，单个 Azure 磁盘无法提供该吞吐量以达到最大 VM 限制。 Azure 高级存储是运行 DT 2.0 的必需条件。 

- 有关可用的 Azure 磁盘类型的详细信息，请参阅[此处](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)
- 有关通过 mdadm 创建软件 RAID 的详细信息，请参阅[此处](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
- 有关配置 LVM 以创建最大吞吐量的带区卷的详细信息，请参阅[此处](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)

根据大小要求，可使用不同的选项来达到 VM 的最大吞吐量。 以下是针对每个 DT 2.0 VM 类型以实现 VM 的吞吐量上限可能的数据卷磁盘配置。 E32sv3 VM 应视为较小工作负载的入门级别。 如果它的速度不够快，可能需要将 VM 的大小调整为 M64-32ms。
由于 M64-32ms VM 具有大量内存，因此 IO 负载可能无法达到限制，尤其是对于读取密集型工作负载而言。 因此，根据客户特定的工作负载，带区集中的磁盘可能很少就足够了。 但为了安全起见，请选择下面的磁盘配置以保证最大吞吐量：


| VM SKU | 磁盘配置 1 | 磁盘配置 2 | 磁盘配置 3 | 磁盘配置 4 | 磁盘配置 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3.5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2.5 TB | 6 x P15 -> 1.5 TB | 


特别是在工作负载读取密集的情况下，它可以提高 IO 性能，以打开 Azure 主机缓存“只读”，如数据库软件数据卷所建议的那样。 而对于事务日志，Azure 主机磁盘缓存必须是“无”。 

关于日志卷的大小，推荐的起始点是数据大小的 15％。 可使用不同的 Azure 磁盘类型来完成日志卷的创建，具体取决于成本和吞吐量要求。 此外，对于日志卷，应首先高吞吐量，如果是 M64-32ms，强烈建议打开写入加速器（这是 SAP HANA 必需的）。 这为事务日志提供了最佳磁盘写入延迟（仅适用于 M 系列）。 有一些事项需要注意，比如每个 VM 类型的最大磁盘数。 关于 WA 的更多信息，请参阅[此处](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)


下面是一些有关调整日志卷大小的示例：

| 数据卷的大小和磁盘类型 | 日志卷和磁盘类型配置 1 | 日志卷和磁盘类型配置 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2.5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1.5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


与 SAP HANA 横向扩展类似，必须在 SAP HANA VM 和 DT 2.0 VM 之间共享 /hana/shared 目录。 建议使用与专用 VM 的 SAP HANA 横向扩展相同的体系结构，这些 VM 可充当高可用性 NFS 服务器。 为了提供共享的备份卷，可以使用完全相同的设计。 但是，是否必须使用 HA，或者只需使用具有足够存储容量的专用 VM 作为备份服务器就足够了，这些都可由客户决定。



### <a name="links-to-dt-20-documentation"></a>DT 2.0 文档的链接 

- [SAP HANA Dynamic Tiering 安装和更新指南](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [SAP HANA Dynamic Tiering 教程和资源](https://www.sap.com/developer/topics/hana-dynamic-tiering.html)
- [SAP HANA Dynamic Tiering PoC](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2.0 SPS 02 Dynamic Tiering 增强功能](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>在 Azure VM 上部署 SAP HANA 的操作
以下各节介绍了在 Azure VM 上部署 SAP HANA 系统的一些相关操作。

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Azure VM 上的备份和还原操作
以下文档介绍如何备份和还原 SAP HANA 部署：

- [SAP HANA 备份概述](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA 文件级备份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA 存储快照基准](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>启动并重启包含 SAP HANA 的 VM
Azure 公有云的一个突出特性是只需为计算时间付费。 例如，当关闭正在运行 SAP HANA 的 VM 时，只需为此期间的存储成本付费。 在初始部署中为 VM 指定静态 IP 地址时，可利用另一特性。 重启具有 SAP HANA 的 VM 时，VM 使用其先前的 IP 地址重启。 


### <a name="use-saprouter-for-sap-remote-support"></a>使用 SAProuter 实现 SAP 远程支持
如果已在本地位置与 Azure 之间建立了站点到站点的连接，并且正在运行 SAP 组件，那么可能已在运行 SAProuter。 在此情况下，请通过完成以下操作实现远程支持：

- 在 SAProuter 配置中保留托管 SAP HANA 的 VM 的专用和静态 IP 地址。
- 配置托管 HANA VM 的子网的 NSG，允许流量通过 TCP/IP 端口 3299。

如果通过 Internet 连接到 Azure，且包含 SAP HANA 的 VM 没有 SAP 路由器，则需安装该组件。 在管理子网中某个独立的 VM 上安装 SAProuter。 下图显示了在不建立站点到站点连接且不包含 SAProuter 的情况下部署 SAP HANA 的大致架构：

![未建立站点到站点连接且不包含 SAProuter 的大致 SAP HANA 部署架构](media/hana-vm-operations/hana-simple-networking3.PNG)

确保在独立的 VM 中安装 SAProuter，而不是在 Jumpbox VM 中安装。 该独立 VM 必须具有静态 IP 地址。 要将 SAProuter 连接到 SAP 托管的 SAProuter，请联系 SAP 获取 IP 地址。 （由 SAP 托管的 SAProuter 与在 VM 上安装的 SAProuter 实例相对应。）使用 SAP 提供的 IP 地址配置 SAProuter 实例。 在配置设置中，唯一必需的端口是 TCP 端口 3299。

有关如何通过 SAPRouter 设置和维护远程支持连接的详细信息，请参阅 [SAP 文档](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)。

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Azure 本机 VM 上的 SAP HANA 的高可用性
如果正在运行 SUSE Linux 12 SP1 或更高版本，可以建立包含 STONITH 设备的 Pacemaker 群集。 可以使用这些设备设置一个可将同步复制与 HANA 系统复制和自动故障转移配合使用的 SAP HANA 配置。 有关设置过程的详细信息，请参阅 [Azure 虚拟机的 SAP HANA 的高可用性指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)。
