---
title: SAP HANA Azure 虚拟机存储配置 |微软文档
description: 已部署 SAP HANA 的 VM 的存储建议。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c4e8b544ea3daeb23b22f3864beb21ba9d3f342f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255611"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 虚拟机存储配置

Azure 提供不同类型的存储，适用于运行 SAP HANA 的 Azure VM。 **SAP HANA 认证的 Azure 存储类型**，可考虑用于 SAP HANA 部署列表，如下所示： 

- Azure 高级 SSD  
- [超级磁盘](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/) 

要了解这些磁盘类型，请参阅文章["选择磁盘类型](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)"

Azure 针对 Azure 标准和高级存储上的 VHD 提供两种部署方法。 如果总体方案允许，请利用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)部署。 

有关存储类型的列表及其 IOPS 和存储吞吐量方面的 SLA，请查看[有关托管磁盘的 Azure 文档](https://azure.microsoft.com/pricing/details/managed-disks/)。

> [!IMPORTANT]
> 与选择的 Azure 存储类型无关，SAP 需要支持用于该存储的文件系统，用于特定的操作系统和 DBMS。 [SAP 支持说明#405827](https://launchpad.support.sap.com/#/notes/405827)列出支持用于不同操作系统和数据库（包括 SAP HANA）的文件系统。 这适用于 SAP HANA 可能访问的所有卷，用于读取和写入任何任务。 具体使用 Azure 上的 NFS 进行 SAP HANA，NFS 版本的其他限制适用于本文后面所述 


不同存储类型的最低 SAP HANA 认证条件为： 

- Azure 高级 SSD - /hana/log 需要使用 Azure[写入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)进行缓存。 /hana/数据卷可以放置在高级 SSD 上，无需 Azure 写入加速器或超磁盘
- Azure 超级磁盘至少适用于 /hana/日志卷。 /hana/数据卷可以放置在高级 SSD 上，无需 Azure 写入加速器，也可以用于更快地重新启动超级磁盘
- **NFS v4.1**卷在 Azure NetApp 文件之上的 /hana/log**和**/hana/数据。 /hana/shared 的卷可以使用 NFS v3 或 NFS v4.1 协议。 NFS v4.1 协议对于 /hana/数据和/哈纳/日志卷是必需的。

可以组合某些存储类型。 例如，可以将 /hana/数据放在高级存储上，/hana/log 可以放在超磁盘存储上，以获得所需的低延迟。 但是，如果您使用基于 ANF 的 NFS v4.1 卷进行 /hana/数据，则需要使用基于 ANF 的 /hana/log 的另一个 NFS v4.1 卷。 **不支持**将 NFS 用于 ANF 的顶部，用于其中一个卷（如 /hana/数据）和 Azure 高级存储或超磁盘，用于其他卷（如 /hana/log）。

在本地世界中，您很少关心 I/O 子系统及其功能。 原因在于，设备供应商会确保满足 SAP HANA 的最低存储要求。 在自行构建 Azure 基础结构时，应了解这些 SAP 发布的一些要求。 SAP 需要的某些最低吞吐量特征导致需要：

- 在 **/hana/log**上启用读取/写入，该日志的读取/写入为 250 MB/秒，尺寸为 1 MB I/O
- 为 16 MB 和 64 MB I/O 大小的 /hana/data 启用至少 400 MB/秒的读取活动****
- 为 16 MB 和 64 MB I/O 大小的 /hana/data 启用至少 250MB/秒的写入活动****

如果低存储延迟对于 DBMS 系统至关重要（即使对于 SAP HANA 等 DBMS 系统也是如此），请将数据保留在内存中。 存储中的关键路径通常围绕 DBMS 系统的事务日志写入。 同时，在故障恢复之后写入保存点或加载内存中数据的操作可能也很关键。 因此，**必须**利用 Azure 高级磁盘进行 **/hana/数据和** **/hana/log**卷。 为了达到 SAP 所需的最低 **/hana/log** 和 **/hana/data** 吞吐量，需要使用 MDADM 或 LVM 基于多个 Azure 高级存储磁盘构建 RAID 0。 将 RAID 卷用作 **/hana/data** 和 **/hana/log** 卷。 

**建议：作为 RAID 0 的条带大小，建议使用：**

- 256 KB 用于 **/哈纳/数据**
- 32 KB 的 /hana/log****

> [!IMPORTANT]
> /hana/data 的条带大小从之前要求 64 KB 或 128 KB 的建议更改为 256 KB，具体取决于具有较新 Linux 版本的客户体验。 256 KB 的大小提供了略佳的性能

> [!NOTE]
> 不需要使用 RAID 卷配置任何冗余级别，因为 Azure 高级和标准存储保留 VHD 的三个映像。 使用 RAID 卷的原因仅仅是为了配置可提供足够 I/O 吞吐量的卷。

在 RAID 下面累积多个 Azure VHD 可以提高 IOPS 和存储吞吐量。 因此，如果使用 3 倍的 P30 Azure 高级存储磁盘构建 RAID 0，则单个 Azure 高级存储 P30 磁盘的 IOPS 和存储吞吐量会提高 3 倍。

在确定 VM 大小或决定 VM 时，还要考虑总体 VM I/O 吞吐量。 [内存优化虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)一文中记录了总体 VM 存储吞吐量。

## <a name="linux-io-scheduler-mode"></a>Linux I/O 计划程序模式
Linux 提供多种不同的 I/O 计划模式。 通过 Linux 供应商和 SAP 的常见建议是重新配置磁盘卷的 I/O 调度程序模式，从**mq 截止时间**或**kyber**模式到**noop（** 非多队列）或**无**（多队列）模式。 详细信息在[SAP 说明#1984787](https://launchpad.support.sap.com/#/notes/1984787)中引用。 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>适用于 Azure M 系列虚拟机的高级存储和 Azure 写入加速器的解决方案
Azure 写入加速器是一种功能，仅供 Azure M 系列 VM 使用。 顾名思义，该功能的目的是改善针对 Azure 高级存储执行的写入操作的 I/O 延迟。 在 SAP HANA 中，预期只能对 **/hana/log** 卷使用写入加速器。 因此 **，/hana/数据和** **/hana/log**是单独的卷，Azure 写入加速器仅支持 **/hana/log**卷。 

> [!IMPORTANT]
> 使用 Azure 高级存储时，必须使用 Azure[写入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)或 /hana/log 卷。 写入加速器仅适用于高级存储和 M 系列和 Mv2 系列 VM。

以下缓存建议假设列出的 SAP HANA 的 I/O 特征如下所示：

- 几乎没有任何针对 HANA 数据文件的读取工作负荷。 重启 HANA 实例后或者在将数据载入 HANA 时出现的大型 I/O 例外。 执行 HANA 数据库备份后，也可能出现大量针对数据文件的读取 I/O。 因此，大多数情况下读取缓存没有意义，因为在大多数情况下，需要完全读取所有数据文件卷。
- HANA 写入点和 HANA 故障恢复会产生大量针对数据文件的写入。 写入保存点是异步进行的，不阻止任何用户事务。 在故障恢复期间写入数据对性能要严格要求，以便系统再次快速响应。 但是，故障恢复更确切地说应该是异常情况
- 几乎不会从 HANA 重做文件进行任何读取。 执行事务日志备份、故障恢复或在 HANA 实例重启阶段出现的大型 I/O 例外。  
- 针对 SAP HANA 重做日志文件的主要负载是写入负载。 根据工作负载的性质，I/O 可以小至 4 KB，也可达到 1 MB 或更大。 针对 SAP HANA 重做文件的写入延迟对性能要很高要求。
- 需要以一种可靠的方式在磁盘上保留所有写入

**建议：由于 SAP HANA 观察到的 I/O 模式，使用 Azure 高级存储的不同卷的缓存设置应类似：**

- **/hana/data** - 无缓存
- **/hana/log** - 无缓存 - M-和 Mv2 系列例外，其中应启用写入加速器而不进行读取缓存。 
- **/哈纳/共享**- 读取缓存

### <a name="production-recommended-storage-solution"></a>生产推荐的存储解决方案

> [!IMPORTANT]
> Azure M 系列虚拟机的 SAP HANA 认证要求中规定，Azure 写入加速器只能用于 **/hana/log** 卷。 因此，在 Azure M 系列虚拟机上的生产场景 SAP HANA 部署中，应该配置 **/hana/log** 卷使用的 Azure 写入加速器。  

> [!NOTE]
> 对于生产场景，请在 [IAAS 的 SAP 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中查看特定的 VM 类型是否受 SAP HANA 的支持。

这些建议通常超出了本文前面所述的 SAP 最低要求。 列出的建议是 SAP 建议的大小建议与不同 VM 类型提供的最大存储吞吐量之间的折衷。

**建议：生产方案的建议配置如下所示：**

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data | /hana/log | /hana/shared | /root 卷 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/秒 | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/秒 | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/秒 | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 吉布 | 1000 MB/秒 | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 吉布 | 1000 MB/秒 | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 吉布 | 2000 MB/秒 | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 吉布 | 2000 MB/秒 | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

检查不同建议的卷的存储吞吐量是否符合要运行的工作负荷。 如果工作负荷要求对 **/hana/data** 和 **/hana/log** 使用更高规格的卷，则需要增加 Azure 高级存储 VHD。 使用比列出的多 VHD 的卷进行大小调整会增加 Azure 虚拟机类型限制范围内的 IOPS 和 I/O 吞吐量。

只能配合 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)使用 Azure 写入加速器。 因此，至少需要将构成 **/hana/log** 卷的 Azure 高级存储磁盘部署为托管磁盘。

Azure 写入加速器在每个 VM 中支持的 Azure 高级存储 VHD 数目有限制。 当前限制为：

- 16 VHD 用于 M128xx 和 M416xx VM
- 8 VHD 用于 M64xx 和 M208xx VM
- M32xx VM 限制为 4 个 VHD

在[写入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)一文中可以找到有关如何启用 Azure 写入加速器的更多详细说明。

此文中还介绍了 Azure 写入加速器的详细信息和限制。

**建议：您需要对形成 /hana/log 卷的磁盘使用写入加速器**


### <a name="cost-conscious-azure-storage-configuration"></a>高性价比的 Azure 存储配置
下表显示了客户也用于在 Azure VM 上托管 SAP HANA 的 VM 类型的配置。 可能有一些 VM 类型可能不符合 SAP HANA 的所有最低存储条件，或者 SAP 未正式支持 SAP HANA。 但到目前为止，这些 VM 在非生产场景中的表现似乎不错。 **/hana/数据和** **/hana/log**组合为一个卷。 因此，Azure 写入加速器的使用可能成为 IOPS 方面的限制。

> [!NOTE]
> 对于 SAP 支持的方案，检查 SAP[在 IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)的 SAP 文档中是否支持 SAP HANA 的特定 VM 类型。

> [!NOTE]
> 与以前关于成本意识解决方案的建议的一个变化是，从[Azure 标准硬盘磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)转向性能更好、更可靠的[Azure 标准 SSD 磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)

这些建议通常超出了本文前面所述的 SAP 最低要求。 列出的建议是 SAP 建议的大小建议与不同 VM 类型提供的最大存储吞吐量之间的折衷。

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data and /hana/log<br /> 使用 LVM 或 MDADM 进行条带化 | /hana/shared | /root 卷 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1，200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2，000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1，000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1，000 吉布 | 1，000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1，750 吉布 | 1，000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2，000 吉布 | 2，000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3，800 吉布 | 2，000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2，850 吉布 | 1，000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5，700 吉布 | 1，000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5，700 吉布 | 2，000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 吉布 | 2，000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


根据 [SAP TDI 存储白皮书](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，对于采用 3 x P20 配置的较小 VM 类型，建议的磁盘空间大于建议的卷空间。 但是，表中显示的选项旨在为 SAP HANA 提供足够的磁盘吞吐量。 如果您需要更改 **/hana/备份**卷（用于保留表示内存容量两倍的备份的大小），请随时进行调整。   
检查不同建议的卷的存储吞吐量是否符合要运行的工作负荷。 如果工作负荷要求对 **/hana/data** 和 **/hana/log** 使用更高规格的卷，则需要增加 Azure 高级存储 VHD。 使用比列出的多 VHD 的卷进行大小调整会增加 Azure 虚拟机类型限制范围内的 IOPS 和 I/O 吞吐量。 

> [!NOTE]
> 上述配置并不会受益于 [Azure 虚拟机单一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)，因为此配置混合使用了 Azure 高级存储和 Azure 标准存储。 但是，这种选择可以优化成本。 需要为上面列作 Azure 标准存储 (Sxx) 的所有磁盘选择高级存储，才能使 VM 配置符合 Azure 的单一 VM SLA。


> [!NOTE]
> 所述磁盘配置建议针对 SAP 向其基础设施提供商表达的最低要求。 在实际的客户部署和工作负载场景中，遇到了这些建议仍未提供足够功能的情况。 这些情况可能是客户在 HANA 重启后需要更快地重新加载数据，或者备份配置需要更高的存储带宽。 其他情况包括 **/hana/log**，其中 5000 IOPS 不足以满足特定工作负荷。 因此，请将这些建议作为起点，并根据工作负载的要求进行调整。
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>适用于 SAP HANA 的 Azure 超磁盘存储配置
微软正在推出一种新的 Azure 存储类型，称为[Azure 超磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)。 到目前为止提供的 Azure 存储与 Ultra 磁盘之间的显著区别是磁盘功能不再绑定到磁盘大小。 作为客户，您可以为 Ultra 磁盘定义这些功能：

- 磁盘大小范围从 4 GiB 到 65，536 GiB
- IOPS 范围从 100 IOPS 到 160K IOPS（最大值也取决于 VM 类型）
- 存储吞吐量从 300 MB/秒到 2，000 MB/秒

Ultra 磁盘使您能够定义满足大小、IOPS 和磁盘吞吐量范围的单个磁盘。 而不是在 Azure 高级存储之上使用 LVM 或 MDADM 等逻辑卷管理器来构造满足 IOPS 和存储吞吐量要求的卷。 您可以在超磁盘和高级存储之间运行配置组合。 因此，您可以将 Ultra 磁盘的使用限制为性能关键/哈纳/数据和 /hana/log 卷，并覆盖使用 Azure 高级存储的其他卷

与高级存储相比，Ultra 磁盘的其他优点可能是更好的读取延迟。 当您希望减少 HANA 启动时间和数据后续加载到内存中时，更快的读取延迟可能具有优势。 当 HANA 编写存储点时，也可以感受到超磁盘存储的优势。 由于 /hana/数据的高级存储磁盘通常不写入加速器缓存，因此与 Ultra 磁盘相比，高级存储上对 /hana/数据的写入延迟更高。 可以预期，使用 Ultra 磁盘的存储点写入在 Ultra 磁盘上性能更好。

> [!IMPORTANT]
> 超级磁盘尚未存在于所有 Azure 区域中，并且还不支持下面列出的所有 VM 类型。 有关 Ultra 磁盘可用以及支持哪些 VM 系列的详细信息，请查看["Azure 中有哪些磁盘类型可用？](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>使用纯超磁盘配置生产推荐的存储解决方案
在此配置中，您可以单独保留 /hana/数据和 /hana/log 卷。 建议的值派生自 SAP 必须根据[SAP TDI 存储白皮书](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)中的建议对 SAP HANA 和存储配置的 VM 类型进行验证的 KPI。

这些建议通常超出了本文前面所述的 SAP 最低要求。 列出的建议是 SAP 建议的大小建议与不同 VM 类型提供的最大存储吞吐量之间的折衷。

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /哈纳/数据量 | /哈纳/数据 I/O 吞吐量 | /哈纳/数据 IOPS | /哈纳/日志卷 | /哈纳/日志 I/O 吞吐量 | /哈纳/日志 IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1，200 MB/s | 600 GB | 700 MBps | 7,500 | 512 GB | 500 MBps  | 2,000 |
| M32ts | 192 GiB | 500 MB/秒 | 250 GB | 400 MBps | 7,500 | 256 GB | 250 MBps  | 2,000 |
| M32ls | 256 GiB | 500 MB/秒 | 300 GB | 400 MBps | 7,500 | 256 GB | 250 MBps  | 2,000 |
| M64ls | 512 GiB | 1，000 MB/s | 600 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M64s | 1，000 吉布 | 1，000 MB/s |  1，200 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M64ms | 1，750 吉布 | 1，000 MB/s | 2，100 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M128s | 2，000 吉布 | 2，000 MB/s |2，400 GB | 1，200 MBps |9,000 | 512 GB | 800 MBps  | 3,000 | 
| M128ms | 3，800 吉布 | 2，000 MB/s | 4，800 GB | 1200 MBps |9,000 | 512 GB | 800 MBps  | 3,000 | 
| M208s_v2 | 2，850 吉布 | 1，000 MB/s | 3，500 GB | 1，000 MBps | 9,000 | 512 GB | 400 MBps  | 2,500 | 
| M208ms_v2 | 5，700 吉布 | 1，000 MB/s | 7，200 GB | 1，000 MBps | 9,000 | 512 GB | 400 MBps  | 2,500 | 
| M416s_v2 | 5，700 吉布 | 2，000 MB/s | 7，200 GB | 1，500 MBps | 9,000 | 512 GB | 800 MBps  | 3,000 | 
| M416ms_v2 | 11，400 吉布 | 2，000 MB/s | 14，400 GB | 1，500 MBps | 9,000 | 512 GB | 800 MBps  | 3,000 |   

列出的值旨在作为起点，需要根据实际需求进行评估。 Azure Ultra 磁盘的优点是可以调整 IOPS 和吞吐量的值，而无需关闭 VM 或停止应用于系统的工作负载。   

> [!NOTE]
> 到目前为止，使用 Ultra 磁盘存储的存储快照不可用。 这将阻止使用 Azure 备份服务使用 VM 快照

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>具有纯超磁盘配置的注重成本的存储解决方案
在此配置中，您在同一磁盘上的 /hana/数据和 /hana/log 卷。 建议的值派生自 SAP 必须根据[SAP TDI 存储白皮书](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)中的建议对 SAP HANA 和存储配置的 VM 类型进行验证的 KPI。 

这些建议通常超出了本文前面所述的 SAP 最低要求。 列出的建议是 SAP 建议的大小建议与不同 VM 类型提供的最大存储吞吐量之间的折衷。

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /哈纳/数据和 /日志的卷 | /hana/数据和日志 I/O 吞吐量 | /哈纳/数据和日志 IOPS |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1，200 MB/s | 1，200 GB | 1，200 MBps | 9,500 | 
| M32ts | 192 GiB | 500 MB/秒 | 512 GB | 400 MBps | 9,500 | 
| M32ls | 256 GiB | 500 MB/秒 | 600 GB | 400 MBps | 9,500 | 
| M64ls | 512 GiB | 1，000 MB/s | 1，100 GB | 900 MBps | 10,000 | 
| M64s | 1，000 吉布 | 1，000 MB/s |  1,700 GB | 900 MBps | 10,000 | 
| M64ms | 1，750 吉布 | 1，000 MB/s | 2，600 GB | 900 MBps | 10,000 | 
| M128s | 2，000 吉布 | 2，000 MB/s |2，900 GB | 1，800 MBps |12,000 | 
| M128ms | 3，800 吉布 | 2，000 MB/s | 5，300 GB | 1，800 MBps |12,000 |  
| M208s_v2 | 2，850 吉布 | 1，000 MB/s | 4，000 GB | 900 MBps | 10,000 |  
| M208ms_v2 | 5，700 吉布 | 1，000 MB/s | 7，700 GB | 900 MBps | 10,000 | 
| M416s_v2 | 5，700 吉布 | 2，000 MB/s | 7，700 GB | 1，800MBps | 12,000 |  
| M416ms_v2 | 11，400 吉布 | 2，000 MB/s | 15，000 GB | 1，800 MBps | 12,000 |    

列出的值旨在作为起点，需要根据实际需求进行评估。 Azure Ultra 磁盘的优点是可以调整 IOPS 和吞吐量的值，而无需关闭 VM 或停止应用于系统的工作负载。  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Azure NetApp 文件上的 NFS v4.1 卷
Azure NetApp 文件提供本机 NFS 共享，可用于 /hana/共享、//哈纳/数据和 /hana/log 卷。 对 /hana/数据和 /hana/log 卷使用基于 ANF 的 NFS 共享需要使用 v4.1 NFS 协议。 在基于 ANF 时，不支持使用 /hana/数据和 /hana/log 卷的 NFS 协议 v3。 

> [!IMPORTANT]
> 不支持在 Azure NetApp 文件上实现的 NFS v3 协议用于 /hana/数据和 /hana/log。 **not** 从功能角度来看，NFS 4.1 对于 /hana/数据和 /hana/log 卷是必需的。 而对于 /hana/共享卷，可以从功能角度使用 NFS v3 或 NFS v4.1 协议。

### <a name="important-considerations"></a>重要注意事项
在考虑 SAP Netweaver 和 SAP HANA 的 Azure NetApp 文件时，请注意以下重要注意事项：

- 最小容量池为 4 TiB。  
- 最小体积大小为 100 GiB
- Azure NetApp 文件和将装载 Azure NetApp 文件卷的所有虚拟机必须位于同一 Azure 虚拟网络或同一区域中的[对等虚拟网络中](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。  
- 所选虚拟网络必须具有委派给 Azure NetApp 文件的子网。
- Azure NetApp 卷的吞吐量是卷配额和服务级别的函数，如[Azure NetApp 文件的服务级别](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 调整 HANA Azure NetApp 卷的量量时，请确保生成的吞吐量满足 HANA 系统要求。  
- Azure NetApp 文件提供[导出策略](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)：您可以控制允许的客户端、访问类型（读取&写入、仅读取等）。 
- Azure NetApp 文件功能尚未了解区域。 当前，Azure NetApp 文件功能未部署在 Azure 区域中的所有可用性区域中。 请注意某些 Azure 区域的潜在延迟影响。  
- 将虚拟机部署在靠近 Azure NetApp 存储的位置以降低延迟非常重要。 
- 虚拟机上的<b>sid</b>dm 的用户 ID 和`sapsys`虚拟机上的组 ID 必须与 Azure NetApp 文件中的配置匹配。 

> [!IMPORTANT]
> 对于 SAP HANA 工作负载，低延迟至关重要。 与 Microsoft 代表合作，确保虚拟机和 Azure NetApp 文件卷在接近的地方部署。  

> [!IMPORTANT]
> 如果<b>sid</b>adm 的用户 ID 与虚拟机和 Azure `sapsys` NetApp 配置之间的组 ID 不匹配，则安装在虚拟机上的 Azure NetApp 卷上的文件权限将显示为`nobody`。 在[将新系统输入](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)Azure NetApp 文件时，请确保为`sapsys`sid <b>adm</b>指定正确的用户 ID 和 组的 ID。

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp 文件上的 HANA 数据库的尺寸

Azure NetApp 卷的吞吐量是卷大小和服务级别的函数，如[Azure NetApp 文件的服务级别](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 

在 Azure 中为 SAP 设计基础结构时，应了解 SAP 的一些最低存储吞吐量要求，这转化为以下最低吞吐量特征：

- 在 /hana/log 上启用读取/写入，该日志的读取/写入为 250 MB/秒，尺寸为 1 MB I/O  
- 为 16 MB 和 64 MB I/O 大小的 /hana/data 启用至少 400 MB/秒的读取活动  
- 为 16 MB 和 64 MB I/O 大小的 /hana/data 启用至少 250MB/秒的写入活动  

[Azure NetApp 文件吞吐量限制](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)每 1 TiB 的卷配额是：
- 高级存储层 - 64 MiB/s  
- 超存储层 - 128 MiB/s  

> [!IMPORTANT]
> 与您在单个 NFS 卷上部署的容量无关，吞吐量预计将在虚拟机中的使用者利用的 1.2-1.4 GB/sec 带宽范围内稳定下来。 这与 ANF 产品提供的基础体系结构和 NFS 的相关 Linux 会话限制有关。 在文章["Azure NetApp 文件的性能基准测试结果](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-benchmarks)"中记录的性能和吞吐量编号是针对具有多个客户端 VM 的一个共享 NFS 卷进行的，因此具有多个会话。 这种情况不同于我们在 SAP 中测量的方案。 我们根据 NFS 卷测量单个 VM 的吞吐量。 托管在 ANF 上。

为了满足 SAP 数据和日志的最低吞吐量要求，并根据 的`/hana/shared`准则，建议的大小如下所示：

| 数据量(Volume) | 大小<br /> 高级存储层 | 大小<br /> 超存储层 | 支持的 NFS 协议 |
| --- | --- | --- |
| /哈纳/日志/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/shared | 每个 4 个辅助节点的最大最大（512 GB、1xRAM） | 每个 4 个辅助节点的最大最大（512 GB、1xRAM） | v3 或 v4.1 |


> [!NOTE]
> 此处列出的 Azure NetApp 文件大小调整建议旨在满足 SAP 对其基础结构提供商表示的最低要求。 在实际的客户部署和工作负载方案中，这可能是不够的。 将这些建议作为起点，并根据特定工作负载的要求进行调整。  

因此，您可以考虑为已列出的超磁盘存储的 ANF 卷部署类似的吞吐量。 还要考虑为不同的 VM SKU 列出的大小的大小，就像 Ultra 磁盘表中所做的那样。

> [!TIP]
> 您可以动态调整 Azure NetApp 文件卷的大小，而无需卷`unmount`、停止虚拟机或停止 SAP HANA。 这允许灵活性，以满足您的应用程序的预期和不可预见的吞吐量需求。

有关如何使用 ANF 中托管的 NFS v4.1 卷使用备用节点部署 SAP HANA 横向扩展配置的文档在[SAP HANA 横向扩展中发布，在 Azure VM 上使用在 SUSE Linux 企业服务器上具有 Azure NetApp 文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)。


## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅：

- [SAP HANA Azure 虚拟机的高可用性指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)。
