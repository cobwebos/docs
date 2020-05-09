---
title: SAP HANA Azure 虚拟机存储配置 |Microsoft Docs
description: 部署了 SAP HANA 的 VM 的存储建议。
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
ms.openlocfilehash: b0d8228586c0e20e4314331339aa2f2c46a38c9a
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792150"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 虚拟机存储配置

Azure 提供了不同类型的存储，适用于运行 SAP HANA 的 Azure Vm。 可按如下所示 SAP HANA 部署列表考虑**SAP HANA 认证的 Azure 存储类型**： 

- Azure 高级 SSD  
- [超级磁盘](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/) 

若要了解这些磁盘类型，请参阅[选择磁盘类型一](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)文

Azure 针对 Azure 标准和高级存储上的 VHD 提供两种部署方法。 如果总体方案允许，请利用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)部署。 

有关存储类型的列表及其 IOPS 和存储吞吐量方面的 SLA，请查看[有关托管磁盘的 Azure 文档](https://azure.microsoft.com/pricing/details/managed-disks/)。

> [!IMPORTANT]
> 独立于所选的 Azure 存储类型，特定操作系统和 DBMS 需要 SAP 支持用于该存储的文件系统。 [SAP 支持说明 #405827](https://launchpad.support.sap.com/#/notes/405827)列出了不同操作系统和数据库支持的文件系统，包括 SAP HANA。 这适用于所有卷 SAP HANA 可以访问，以便读取和写入任何任务。 在 Azure 上专门使用 NFS 进行 SAP HANA，NFS 版本的其他限制将按本文后面的说明进行。 


不同存储类型的最低 SAP HANA 认证条件如下： 

- Azure 高级 SSD-/hana/log 需要通过 Azure[写入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)缓存。 /Hana/data 卷可以在没有 Azure 写入加速器的情况下或在超磁盘上高级 SSD
- Azure Ultra 磁盘至少适用于/hana/log 卷。 可以在没有 Azure 写入加速器或的情况下将/hana/data 卷置于高级 SSD
- 适用于/hana/log**和**/Hana/data. 的 Azure NetApp 文件之上的**NFS** v2.0 卷 /Hana/shared 的数量可以使用 NFS v3 或 NFS v2.0 协议。 NFS v2.0 协议对于/hana/data 和/hana/日志卷是必需的。

某些存储类型可以组合在一起。 例如，可以将/hana/data 放在高级存储上，而/hana/log 可放置在超小型磁盘存储上，以便达到所需的低延迟。 但是，如果使用基于和 for/hana/data 的 NFS v2.0，则需要使用基于和 for/hana/log 的另一个 NFS v2.0 卷 **不支持**在和上将 NFS 用于某个卷（如/hana/data）和 Azure 高级存储或超磁盘（如/hana/log）。

在本地环境中，很少需要关心 i/o 子系统及其功能。 原因在于，设备供应商会确保满足 SAP HANA 的最低存储要求。 当你自行构建 Azure 基础结构时，你应该了解其中一些 SAP 颁发的要求。 SAP 所需的某些最小吞吐量特征导致需要：

- 启用读/写， **/hana/log**为 250 mb/秒，大小为 1 mb i/o
- 为 16 MB 和 64 MB I/O 大小的 /hana/data 启用至少 400 MB/秒的读取活动****
- 为 16 MB 和 64 MB I/O 大小的 /hana/data 启用至少 250MB/秒的写入活动****

如果低存储延迟对于 DBMS 系统至关重要（即使对于 SAP HANA 等 DBMS 系统也是如此），请将数据保留在内存中。 存储中的关键路径通常围绕 DBMS 系统的事务日志写入。 同时，在故障恢复之后写入保存点或加载内存中数据的操作可能也很关键。 因此，**必须**对 **/hana/data**和 **/Hana/log**卷使用 Azure 高级磁盘。 为了达到 SAP 所需的最低 **/hana/log** 和 **/hana/data** 吞吐量，需要使用 MDADM 或 LVM 基于多个 Azure 高级存储磁盘构建 RAID 0。 将 RAID 卷用作 **/hana/data** 和 **/hana/log** 卷。 

**建议：作为 RAID 0 的条带大小，建议使用：**

- **/hana/data** 256 KB
- 32 KB 的 /hana/log****

> [!IMPORTANT]
> /Hana/data 的条带大小已从较早的建议中更改为 64 KB 或 128 KB 256，并基于具有更多最新 Linux 版本的客户体验。 大小 256 KB 提供略微更好的性能

> [!NOTE]
> 不需要使用 RAID 卷配置任何冗余级别，因为 Azure 高级和标准存储保留 VHD 的三个映像。 使用 RAID 卷的原因仅仅是为了配置可提供足够 I/O 吞吐量的卷。

在 RAID 下面累积多个 Azure VHD 可以提高 IOPS 和存储吞吐量。 因此，如果使用 3 倍的 P30 Azure 高级存储磁盘构建 RAID 0，则单个 Azure 高级存储 P30 磁盘的 IOPS 和存储吞吐量会提高 3 倍。

在确定 VM 大小或决定 VM 时，还要考虑总体 VM I/O 吞吐量。 [内存优化虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)一文中记录了总体 VM 存储吞吐量。

## <a name="linux-io-scheduler-mode"></a>Linux I/O 计划程序模式
Linux 提供多种不同的 I/O 计划模式。 通过 Linux 供应商和 SAP 的常见建议是将磁盘卷的 i/o 调度器模式从**mq 截止时间**或**kyber**模式重新配置为**noop** （非 multiqueue）或**none** （multiqueue）模式。 详细信息在[SAP 说明 #1984787](https://launchpad.support.sap.com/#/notes/1984787)中引用。 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>适用于 Azure M 系列虚拟机的高级存储和 Azure 写入加速器的解决方案
Azure 写入加速器是一项仅适用于 Azure M 系列 Vm 的功能。 顾名思义，该功能的目的是改善针对 Azure 高级存储执行的写入操作的 I/O 延迟。 在 SAP HANA 中，预期只能对 **/hana/log** 卷使用写入加速器。 因此， **/hana/data**和 **/hana/log**是单独的卷，其 Azure 写入加速器仅支持 **/hana/log**卷。 

> [!IMPORTANT]
> 使用 Azure 高级存储时，Azure[写入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)或/hana/log 卷的使用是必需的。 写入加速器仅适用于高级存储和 M 系列和 Mv2 系列 Vm。

以下缓存建议假设列出的 SAP HANA 的 I/O 特征如下所示：

- 几乎没有任何针对 HANA 数据文件的读取工作负荷。 重启 HANA 实例后或者在将数据载入 HANA 时出现的大型 I/O 例外。 执行 HANA 数据库备份后，也可能出现大量针对数据文件的读取 I/O。 因此，大多数情况下读取缓存没有意义，因为在大多数情况下，需要完全读取所有数据文件卷。
- HANA 写入点和 HANA 故障恢复会产生大量针对数据文件的写入。 写入保存点是异步进行的，不阻止任何用户事务。 在故障恢复期间写入数据对性能要严格要求，以便系统再次快速响应。 但是，故障恢复更确切地说应该是异常情况
- 几乎不会从 HANA 重做文件进行任何读取。 执行事务日志备份、故障恢复或在 HANA 实例重启阶段出现的大型 I/O 例外。  
- 针对 SAP HANA 重做日志文件的主要负载是写入负载。 根据工作负载的性质，I/O 可以小至 4 KB，也可达到 1 MB 或更大。 针对 SAP HANA 重做文件的写入延迟对性能要很高要求。
- 需要以一种可靠的方式在磁盘上保留所有写入

**建议：通过 SAP HANA 观察到的 i/o 模式，使用 Azure 高级存储的不同卷的缓存应该设置如下：**

- **/hana/data** - 无缓存
- **/hana/log** -无需在未读缓存的情况下启用写入加速器的 M 和 Mv2 系列的缓存例外。 
- **/hana/shared** -读取缓存

### <a name="production-recommended-storage-solution"></a>生产推荐的存储解决方案

> [!IMPORTANT]
> Azure M 系列虚拟机的 SAP HANA 认证要求中规定，Azure 写入加速器只能用于 **/hana/log** 卷。 因此，在 Azure M 系列虚拟机上的生产场景 SAP HANA 部署中，应该配置 **/hana/log** 卷使用的 Azure 写入加速器。  

> [!NOTE]
> 对于生产场景，请在 [IAAS 的 SAP 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中查看特定的 VM 类型是否受 SAP HANA 的支持。

建议经常超出 SAP 最低要求，如本文前面所述。 列出的建议是 SAP 大小建议和不同 VM 类型提供的最大存储吞吐量之间的折衷。

**建议：生产方案的推荐配置如下所示：**

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data | /hana/log | /hana/shared | /root 卷 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/秒 | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/秒 | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/秒 | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/秒 | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/秒 | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/秒 | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/秒 | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

检查不同建议的卷的存储吞吐量是否符合你要运行的工作负荷。 如果工作负荷要求对 **/hana/data** 和 **/hana/log** 使用更高规格的卷，则需要增加 Azure 高级存储 VHD。 使用比列出的 Vhd 更多的卷调整容量可在 Azure 虚拟机类型限制范围内提高 IOPS 和 i/o 吞吐量。

只能配合 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)使用 Azure 写入加速器。 因此，至少需要将构成 **/hana/log** 卷的 Azure 高级存储磁盘部署为托管磁盘。

Azure 写入加速器在每个 VM 中支持的 Azure 高级存储 VHD 数目有限制。 当前限制为：

- 适用于 M128xx 和 M416xx VM 的16个 Vhd
- 8个 Vhd，适用于 M64xx 和 M208xx VM
- M32xx VM 限制为 4 个 VHD

在[写入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)一文中可以找到有关如何启用 Azure 写入加速器的更多详细说明。

此文中还介绍了 Azure 写入加速器的详细信息和限制。

**建议：对于构成/hana/log 卷的磁盘，需要使用写入加速器**


### <a name="cost-conscious-azure-storage-configuration"></a>高性价比的 Azure 存储配置
下表显示了客户用于在 Azure Vm 上托管 SAP HANA 的 VM 类型的配置。 可能有一些 VM 类型可能无法满足 SAP HANA 的所有最小存储条件，也可能不是 SAP SAP HANA 正式支持。 但到目前为止，这些 VM 在非生产场景中的表现似乎不错。 **/Hana/data**和 **/hana/log**组合到一个卷中。 因此，Azure 写入加速器的使用可能会对 IOPS 产生限制。

> [!NOTE]
> 对于 SAP 支持的方案，请检查[IAAS 的 sap 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中的 sap SAP HANA 是否支持某个 VM 类型。

> [!NOTE]
> 对于成本意识解决方案，以前的建议的更改是从[Azure 标准 HDD 磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)转移到更好的性能和更可靠的[Azure 标准 SSD 磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)

建议经常超出 SAP 最低要求，如本文前面所述。 列出的建议是 SAP 大小建议和不同 VM 类型提供的最大存储吞吐量之间的折衷。

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data and /hana/log<br /> 使用 LVM 或 MDADM 进行条带化 | /hana/shared | /root 卷 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1200 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2000 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1000 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1000 GiB | 1000 MB/秒 | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1750 GiB | 1000 MB/秒 | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3800 GiB | 2000 MB/秒 | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2850 GiB | 1000 MB/秒 | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5700 GiB | 1000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5700 GiB | 2000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2000 MB/秒 | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


根据 [SAP TDI 存储白皮书](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，对于采用 3 x P20 配置的较小 VM 类型，建议的磁盘空间大于建议的卷空间。 但是，表中显示的选项旨在为 SAP HANA 提供足够的磁盘吞吐量。 如果需要对 **/hana/backup**卷进行更改（这是为了保持两次内存量的备份的大小），则可随意进行调整。   
检查不同建议的卷的存储吞吐量是否符合你要运行的工作负荷。 如果工作负荷要求对 **/hana/data** 和 **/hana/log** 使用更高规格的卷，则需要增加 Azure 高级存储 VHD。 使用比列出的 Vhd 更多的卷调整容量可在 Azure 虚拟机类型限制范围内提高 IOPS 和 i/o 吞吐量。 

> [!NOTE]
> 上述配置并不会受益于 [Azure 虚拟机单一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)，因为此配置混合使用了 Azure 高级存储和 Azure 标准存储。 但是，这种选择可以优化成本。 需要为上面列作 Azure 标准存储 (Sxx) 的所有磁盘选择高级存储，才能使 VM 配置符合 Azure 的单一 VM SLA。


> [!NOTE]
> 所述磁盘配置建议针对 SAP 向其基础设施提供商表达的最低要求。 在实际的客户部署和工作负载场景中，遇到了这些建议仍未提供足够功能的情况。 这些情况可能是客户在 HANA 重启后需要更快地重新加载数据，或者备份配置需要更高的存储带宽。 其他情况包括 **/hana/log**，其中 5000 IOPS 不足以满足特定工作负荷。 因此，请将这些建议作为起点，并根据工作负载的要求进行调整。
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>适用于 SAP HANA 的 Azure Ultra 磁盘存储配置
Microsoft 正在推出一种新的 Azure 存储类型（称为[Azure Ultra 磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)）。 目前为止，Azure 存储空间和超磁盘之间的重要区别在于磁盘功能不再绑定到磁盘大小。 作为客户，你可以为超磁盘定义以下功能：

- 磁盘大小，范围从 4 GiB 到 65536 GiB
- IOPS 范围为 100 IOPS 到 160K IOPS （最大值取决于 VM 类型）
- 从 300 MB/秒到 2000 MB/秒的存储吞吐量

超磁盘允许定义满足大小、IOPS 和磁盘吞吐量范围的单个磁盘。 不要使用逻辑卷管理器（例如，在 Azure 高级存储上使用 LVM 或 MDADM）来构建满足 IOPS 和存储吞吐量要求的卷。 可以在超磁盘与高级存储之间运行配置混合。 因此，你可以将超磁盘的使用限制为性能关键的/hana/data 和/hana/log 卷，并覆盖包含 Azure 高级存储的其他卷

与高级存储相比，Ultra 磁盘的其他优点可能是更好的读取延迟。 如果要减少 HANA 启动时间和将后续数据加载到内存中，则读取延迟越快。 当 HANA 编写保存点时，还可以感觉到 Ultra 磁盘存储的优点。 由于/hana/data 的高级存储磁盘通常不写入加速器缓存，因此，与超磁盘相比，高级存储上的/hana/data 的写入延迟会更高。 可能会出现这样的情况：在超磁盘上，用超高磁盘写入的保存点性能会更好。

> [!IMPORTANT]
> 所有 Azure 区域中都尚未提供 Ultra 磁盘，还不支持下面列出的所有 VM 类型。 有关可用的超磁盘和受支持的 VM 系列的详细信息，请参阅文章[Azure 中有哪些磁盘类型可用？](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)。

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>使用纯超高磁盘配置的生产推荐存储解决方案
在此配置中，你需要单独保留/hana/data 和/hana/log 卷。 建议的值派生于 SAP 必须根据[SAP TDI 存储白皮书](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)中的建议认证 SAP HANA 和存储配置的 VM 类型。

建议经常超出 SAP 最低要求，如本文前面所述。 列出的建议是 SAP 大小建议和不同 VM 类型提供的最大存储吞吐量之间的折衷。

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data 卷 | /hana/data i/o 吞吐量 | /hana/data IOPS | /hana/log 卷 | /hana/log i/o 吞吐量 | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1200 MB/秒 | 600 GB | 700 MBps | 7,500 | 512 GB | 500 MBps  | 2,000 |
| M32ts | 192 GiB | 500 MB/秒 | 250 GB | 400 MBps | 7,500 | 256 GB | 250 MBps  | 2,000 |
| M32ls | 256 GiB | 500 MB/秒 | 300 GB | 400 MBps | 7,500 | 256 GB | 250 MBps  | 2,000 |
| M64ls | 512 GiB | 1000 MB/秒 | 600 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M64s | 1000 GiB | 1000 MB/秒 |  1200 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M64ms | 1750 GiB | 1000 MB/秒 | 2100 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M128s | 2000 GiB | 2000 MB/秒 |2400 GB | 1200 MBps |9,000 | 512 GB | 800 MBps  | 3,000 | 
| M128ms | 3800 GiB | 2000 MB/秒 | 4800 GB | 1200 MBps |9,000 | 512 GB | 800 MBps  | 3,000 | 
| M208s_v2 | 2850 GiB | 1000 MB/秒 | 3500 GB | 1000 MBps | 9,000 | 512 GB | 400 MBps  | 2,500 | 
| M208ms_v2 | 5700 GiB | 1000 MB/秒 | 7200 GB | 1000 MBps | 9,000 | 512 GB | 400 MBps  | 2,500 | 
| M416s_v2 | 5700 GiB | 2000 MB/秒 | 7200 GB | 1500 MBps | 9,000 | 512 GB | 800 MBps  | 3,000 | 
| M416ms_v2 | 11400 GiB | 2000 MB/秒 | 14400 GB | 1500 MBps | 9,000 | 512 GB | 800 MBps  | 3,000 |   

列出的值旨在作为起点，需要根据实际需求进行评估。 Azure Ultra 磁盘的优点是，可以对 IOPS 和吞吐量的值进行调整，而无需关闭 VM 或停止应用到系统的工作负载。   

> [!NOTE]
> 到目前为止，使用超高磁盘存储的存储快照不可用。 这会阻止使用 Azure 备份服务的 VM 快照

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>具有纯超高磁盘配置的注重成本的存储解决方案
在此配置中，/hana/data 和/hana/log 卷位于同一磁盘上。 建议的值派生于 SAP 必须根据[SAP TDI 存储白皮书](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)中的建议认证 SAP HANA 和存储配置的 VM 类型。 

建议经常超出 SAP 最低要求，如本文前面所述。 列出的建议是 SAP 大小建议和不同 VM 类型提供的最大存储吞吐量之间的折衷。

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /Hana/data 和/log 的卷 | /hana/data 和日志 i/o 吞吐量 | /hana/data 和日志 IOPS |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1200 MB/秒 | 1200 GB | 1200 MBps | 9500 | 
| M32ts | 192 GiB | 500 MB/秒 | 512 GB | 400 MBps | 9500 | 
| M32ls | 256 GiB | 500 MB/秒 | 600 GB | 400 MBps | 9500 | 
| M64ls | 512 GiB | 1000 MB/秒 | 1100 GB | 900 MBps | 10,000 | 
| M64s | 1000 GiB | 1000 MB/秒 |  1,700 GB | 900 MBps | 10,000 | 
| M64ms | 1750 GiB | 1000 MB/秒 | 2600 GB | 900 MBps | 10,000 | 
| M128s | 2000 GiB | 2000 MB/秒 |2900 GB | 1800 MBps |12,000 | 
| M128ms | 3800 GiB | 2000 MB/秒 | 5300 GB | 1800 MBps |12,000 |  
| M208s_v2 | 2850 GiB | 1000 MB/秒 | 4000 GB | 900 MBps | 10,000 |  
| M208ms_v2 | 5700 GiB | 1000 MB/秒 | 7700 GB | 900 MBps | 10,000 | 
| M416s_v2 | 5700 GiB | 2000 MB/秒 | 7700 GB | 1，800MBps | 12,000 |  
| M416ms_v2 | 11400 GiB | 2000 MB/秒 | 15000 GB | 1800 MBps | 12,000 |    

列出的值旨在作为起点，需要根据实际需求进行评估。 Azure Ultra 磁盘的优点是，可以对 IOPS 和吞吐量的值进行调整，而无需关闭 VM 或停止应用到系统的工作负载。  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Azure NetApp 文件上的 NFS v2.0 卷
Azure NetApp 文件提供可用于/hana/shared、/hana/data 和/hana/log 卷的本机 NFS 共享。 对/hana/data 和/hana/log 卷使用基于和的 NFS 共享需要使用版本 4.1 NFS 协议。 如果在和上基于共享，则不支持使用 NFS 协议 v3 作为/hana/data 和/hana/log 卷。 

> [!IMPORTANT]
> **不**支持在 Azure NetApp 文件上实现的 NFS v3 协议用于/hana/data 和/hana/log 从功能角度来看，NFS 4.1 对于/hana/data 和/hana/log 卷是必需的。 但对于/hana/shared 卷，NFS v3 或 NFS v2.0 协议可从功能角度来使用。

### <a name="important-considerations"></a>重要注意事项
考虑用于 SAP Netweaver 和 SAP HANA 的 Azure NetApp 文件时，请注意以下重要事项：

- 最小容量池为 4 TiB。  
- 最小卷大小为 100 GiB
- Azure NetApp 文件以及将在其中装入 Azure NetApp 文件的所有虚拟机都必须位于同一个 Azure 虚拟网络中，或位于同一区域中的[对等互连虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。  
- 所选虚拟网络必须具有子网，并委托给 Azure NetApp 文件。
- Azure NetApp 卷的吞吐量是卷配额和服务级别的功能，如[Azure NetApp 文件的服务级别](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 调整 HANA Azure NetApp 卷的大小时，请确保生成的吞吐量符合 HANA 系统要求。  
- Azure NetApp 文件提供[导出策略](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)：可控制允许的客户端、访问类型（读取&写入、只读等）。 
- Azure NetApp 文件功能尚不能识别区域。 目前，azure NetApp 文件功能未部署在 Azure 区域中的所有可用性区域中。 请注意某些 Azure 区域中潜在的延迟影响。  
- 将部署的虚拟机接近于 Azure NetApp 存储非常重要，以实现低延迟。 
- 虚拟机`sapsys`上<b>Sid</b>adm 和组 id 的用户 Id 必须与 Azure NetApp 文件中的配置相匹配。 

> [!IMPORTANT]
> 对于 SAP HANA 工作负荷，低延迟是至关重要的。 与你的 Microsoft 代表合作，以确保虚拟机和 Azure NetApp 文件卷在接近邻近性时进行部署。  

> [!IMPORTANT]
> 如果<b>sid</b>Adm 的用户 id 与虚拟机和 Azure NetApp 配置`sapsys`之间的组 id 不匹配，则在虚拟机上装载的 Azure netapp 卷上的文件权限将显示为。 `nobody` [在将新系统](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)加入 Azure NetApp 文件时<b>sid</b> `sapsys`，请确保为 sid adm 和组 ID 指定了正确的用户 id。

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>在 Azure NetApp 文件上调整 HANA 数据库的大小

Azure NetApp 卷的吞吐量是卷大小和服务级别的一项功能，如[Azure Netapp 文件的服务级别](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 

在 Azure 中设计 SAP 基础结构时，应注意到 SAP 的一些最低存储吞吐量要求，这些要求转换为的最小吞吐量特征：

- 启用读/写，/hana/log 为 250 MB/秒，大小为 1 MB i/o  
- 为 16 MB 和 64 MB I/O 大小的 /hana/data 启用至少 400 MB/秒的读取活动  
- 为 16 MB 和 64 MB I/O 大小的 /hana/data 启用至少 250MB/秒的写入活动  

每 1 TiB 的卷配额的[Azure NetApp 文件吞吐量限制](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)如下：
- 高级存储层-64 MiB/秒  
- 超存储层-128 MiB/秒  

> [!IMPORTANT]
> 不依赖于在单个 NFS 卷上部署的容量（吞吐量）应达到平稳虚拟机中的使用者所利用的 1.2-1.4 GB/秒带宽。 这与和产品/服务的基础体系结构以及有关 NFS 的相关 Linux 会话限制相关。 针对[Azure NetApp 文件的性能基准测试结果](https://docs.microsoft.com/azure/azure-netapp-files/performance-benchmarks-linux)一文中所述的性能和吞吐量数字是针对包含多个客户端 vm 的一个共享 NFS 卷执行的，因此具有多个会话。 这种情况不同于我们在 SAP 中衡量的方案。 从单个 VM 对 NFS 卷衡量吞吐量。 托管在和上。

为了满足数据和日志的 SAP 最小吞吐量要求，以及根据的准则`/hana/shared`，建议的大小如下所示：

| 数据量(Volume) | 大小<br /> 高级存储层 | 大小<br /> 超存储层 | 支持的 NFS 协议 |
| --- | --- | --- |
| /hana/log | 4 TiB | 2 TiB | 4。1 |
| /hana/data | 6.3 TiB | 3.2 TiB | 4。1 |
| /hana/shared | 每4个辅助角色节点最大（512 GB，1xRAM） | 每4个辅助角色节点最大（512 GB，1xRAM） | v3 或4。1 |


> [!NOTE]
> 此处所述的 Azure NetApp 文件大小建议旨在满足 SAP 向其基础结构提供商提供的最低要求。 在实际的客户部署和工作负载情况下，这可能不够。 使用这些建议作为起点并根据具体工作负载的要求进行调整。  

因此，可以考虑为已列出的和卷部署类似的吞吐量。 还应考虑为不同 VM Sku 的卷列出的大小，就像在 "超小型磁盘" 表中所做的那样。

> [!TIP]
> 你可以动态地重新调整 Azure NetApp 文件卷的大小，而无`unmount`需卷、停止虚拟机或停止 SAP HANA。 这样就可以灵活地满足应用程序预期和无法预料的吞吐量需求。

有关如何使用在和中托管的[Azure 虚拟机上的备用节点在 Azure vm 上使用备用节点](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)部署具有备用节点的 SAP HANA 横向扩展配置的文档 SAP HANA，请参阅 SUSE Linux Enterprise Server 上的 Azure NetApp 文件。


## <a name="next-steps"></a>后续步骤
有关详细信息，请参见:

- [SAP HANA Azure 虚拟机的高可用性指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)。
