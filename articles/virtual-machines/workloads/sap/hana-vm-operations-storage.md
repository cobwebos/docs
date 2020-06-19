---
title: SAP HANA Azure 虚拟机存储配置 | Microsoft Docs
description: 针对已部署 SAP HANA 的 VM 的存储建议。
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
ms.date: 05/19/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa3096f43952c047620b310412b27c434fc5fb06
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682597"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 虚拟机存储配置

对于正在运行 SAP HANA 的 Azure VM，Azure 提供其他适用的存储类型。 下面列出了一些可用于 SAP HANA 部署的 SAP HANA 认证的 Azure 存储类型： 

- Azure 高级 SSD  
- [超级磁盘](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/) 

若要了解这些磁盘类型，请参阅[选择磁盘类型](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)一文

Azure 针对 Azure 标准和高级存储上的 VHD 提供两种部署方法。 如果总体方案允许，请利用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)部署。 

有关存储类型的列表及其 IOPS 和存储吞吐量方面的 SLA，请查看[有关托管磁盘的 Azure 文档](https://azure.microsoft.com/pricing/details/managed-disks/)。

> [!IMPORTANT]
> 无论选择何种 Azure 存储类型，对于特定操作系统和 DBMS，都需要 SAP 支持该存储所使用的文件系统。 [SAP 支持说明 #405827](https://launchpad.support.sap.com/#/notes/405827) 列出了不同操作系统和数据库的支持文件系统，包括 SAP HANA。 这适用于 SAP HANA 可能访问的所有卷（以便为任何任务进行读取和写入）。 若为 SAP HANA 专门使用 Azure 上的 NFS，NFS 版本的其他限制将按本文后面的说明适用 


不同存储类型的最低 SAP HANA 认证条件为： 

- Azure 高级 SSD - /hana/log 需要使用 Azure [写入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)缓存。 /hana/data 卷可以放置在高级 SSD 上，无需 Azure 写入加速器或位于超级磁盘
- 至少将 Azure 超级磁盘用于 /hana/log 卷。 /hana/data 卷可以放置在高级 SSD 上（无需 Azure 写入加速器），也可以放置在超级磁盘上（旨在以更快的速度重启）
- 对于 /hana/log 和 /hana/data，需要基于 Azure NetApp 文件的 NFS v4.1 卷 。 /hana/shared 的卷可以使用 NFS v3 或 NFS v4.1 协议。 对于 /hana/data and/hana/log 卷，必须使用 NFS v4.1 协议。

可以组合某些存储类型。 例如，可以将 /hana/data 放在高级存储上，同时 /hana/log 可放置在超级磁盘存储上，以获得所需的低延迟。 但是，如果为 /hana/data 使用基于 ANF 的 NFS v4.1 卷，则需要为 /hana/log 使用另一个基于 ANF 的 NFS v4.1 卷。 不支持将基于 ANF 的 NFS 用于其中的一个卷（如 /hana/data）并将 Azure 高级存储或超级磁盘用于另一个卷（如 /hana/log）。

在本地环境中，很少需要考虑 I/O 子系统及其功能。 原因在于，设备供应商会确保满足 SAP HANA 的最低存储要求。 在自行构建 Azure 基础结构时，你应该注意其中的一些 SAP 颁发的要求。 因 SAP 需要的某些最低吞吐量特征，而需要执行以下操作：

- 在 I/O 大小为 1 MB、速率为 250 MB/秒的 /hana/log 上启用读/写
- 为 16 MB 和 64 MB I/O 大小的 /hana/data 启用至少 400 MB/秒的读取活动
- 为 16 MB 和 64 MB I/O 大小的 /hana/data 启用至少 250MB/秒的写入活动

如果低存储延迟对于 DBMS 系统至关重要（即使对于 SAP HANA 等 DBMS 系统也是如此），请将数据保留在内存中。 存储中的关键路径通常围绕 DBMS 系统的事务日志写入。 同时，在故障恢复之后写入保存点或加载内存中数据的操作可能也很关键。 因此，必须对 /hana/data 和 /hana/log 卷使用 Azure 高级磁盘  。 为了达到 SAP 所需的最低 **/hana/log** 和 **/hana/data** 吞吐量，需要使用 MDADM 或 LVM 基于多个 Azure 高级存储磁盘构建 RAID 0。 将 RAID 卷用作 **/hana/data** 和 **/hana/log** 卷。 

> [!IMPORTANT]
>不得 /data、/log 和 /shared 这三个 SAP HANA FileSystems 放入默认卷或根卷组中。  强烈建议遵循 Linux 供应商指南，该指南通常用于为 /data、/log 和 /shared 创建各自的卷组。

建议：对于 RAID 0 的条带大小，建议使用：

- 256 KB 的 /hana/data
- 32 KB 的 /hana/log

> [!IMPORTANT]
> 根据在较新 Linux 版本方面的客户体验，/hana/data 的条带大小已从之前要求 64 KB 或 128 KB 这一建议更改为 256 KB。 256 KB 的大小提供的性能略佳

> [!NOTE]
> 不需要使用 RAID 卷配置任何冗余级别，因为 Azure 高级和标准存储保留 VHD 的三个映像。 使用 RAID 卷的原因仅仅是为了配置可提供足够 I/O 吞吐量的卷。

在 RAID 下面累积多个 Azure VHD 可以提高 IOPS 和存储吞吐量。 因此，如果使用 3 倍的 P30 Azure 高级存储磁盘构建 RAID 0，则单个 Azure 高级存储 P30 磁盘的 IOPS 和存储吞吐量会提高 3 倍。

在确定 VM 大小或决定 VM 时，还要考虑总体 VM I/O 吞吐量。 [内存优化虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)一文中记录了总体 VM 存储吞吐量。

## <a name="linux-io-scheduler-mode"></a>Linux I/O 计划程序模式
Linux 提供多种不同的 I/O 计划模式。 Linux 供应商和 SAP 的常见建议是重新配置磁盘卷的 I/O 调度程序模式，即从“mq-deadline”或“kyber”模式配置为“noop”(non-multiqueue) 或“none”(multiqueue) 模式   。 请参考 [SAP 说明 #1984787](https://launchpad.support.sap.com/#/notes/1984787) 中的详细信息。 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>对 Azure M 系列虚拟机使用包含高级存储和 Azure 写入加速器的解决方案
Azure 写入加速器是可用于 Azure M 系列 VM 的一项功能。 顾名思义，该功能的目的是改善针对 Azure 高级存储执行的写入操作的 I/O 延迟。 在 SAP HANA 中，预期只能对 **/hana/log** 卷使用写入加速器。 因此，/hana/data 和 /hana/log 是单独的卷，其中 Azure 写入加速器仅支持 /hana/log 卷  。 

> [!IMPORTANT]
> 使用 Azure 高级存储时，必须使用 Azure [写入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)或 /hana/log 卷。 写入加速器仅适用于高级存储以及 M 系列和 Mv2 系列 VM。

以下缓存建议假设列出的 SAP HANA 的 I/O 特征如下所示：

- 几乎没有任何针对 HANA 数据文件的读取工作负荷。 重启 HANA 实例后或者在将数据载入 HANA 时出现的大型 I/O 例外。 执行 HANA 数据库备份后，也可能出现大量针对数据文件的读取 I/O。 因此，大多数情况下读取缓存没有意义，因为在大多数情况下，需要完全读取所有数据文件卷。
- HANA 写入点和 HANA 故障恢复会产生大量针对数据文件的写入。 写入保存点是异步进行的，不阻止任何用户事务。 在故障恢复期间写入数据对性能要严格要求，以便系统再次快速响应。 但是，故障恢复更确切地说应该是异常情况
- 几乎不会从 HANA 重做文件进行任何读取。 执行事务日志备份、故障恢复或在 HANA 实例重启阶段出现的大型 I/O 例外。  
- 针对 SAP HANA 重做日志文件的主要负载是写入负载。 根据工作负载的性质，I/O 可以小至 4 KB，也可达到 1 MB 或更大。 针对 SAP HANA 重做文件的写入延迟对性能要很高要求。
- 需要以一种可靠的方式在磁盘上保留所有写入

建议：对于这些观察到的 SAP HANA 的 I/O 模式，使用 Azure 高级存储的不同卷的缓存应设置为如下形式：

- **/hana/data** - 无缓存
- **/hana/log** - 无缓存；M- 和 Mv2 系列除外，这两个系列应启用写入加速器，但不读取缓存。 
- **/hana/shared** - 读取缓存

### <a name="production-recommended-storage-solution"></a>生产推荐的存储解决方案

> [!IMPORTANT]
> Azure M 系列虚拟机的 SAP HANA 认证要求中规定，Azure 写入加速器只能用于 **/hana/log** 卷。 因此，在 Azure M 系列虚拟机上的生产场景 SAP HANA 部署中，应该配置 **/hana/log** 卷使用的 Azure 写入加速器。  

> [!NOTE]
> 对于生产场景，请在 [IAAS 的 SAP 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中查看特定的 VM 类型是否受 SAP HANA 的支持。

这些建议通常超出了本文前面所述的 SAP 最低要求。 列出的建议是 SAP 提供的大小建议与不同 VM 类型提供的最大存储吞吐量之间的折衷方案。

建议：**建议用于生产场景的配置如下所示：**

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

检查建议的不同卷的存储吞吐量是否满足所要运行的工作负载。 如果工作负荷要求对 **/hana/data** 和 **/hana/log** 使用更高规格的卷，则需要增加 Azure 高级存储 VHD。 对于包含的 VHD 数目比所列数目更多的卷，调整其大小会在 Azure 虚拟机类型的限制范围内增大 IOPS 和 I/O 吞吐量。

只能配合 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)使用 Azure 写入加速器。 因此，至少需要将构成 **/hana/log** 卷的 Azure 高级存储磁盘部署为托管磁盘。

Azure 写入加速器在每个 VM 中支持的 Azure 高级存储 VHD 数目有限制。 当前限制为：

- M128xx 和 M416xx VM 限制为 16 个 VHD
- M64xx 和 M208xx VM 限制为 8 个 VHD
- M32xx VM 限制为 4 个 VHD

在[写入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)一文中可以找到有关如何启用 Azure 写入加速器的更多详细说明。

此文中还介绍了 Azure 写入加速器的详细信息和限制。

建议：**需要对形成 /hana/log 卷的磁盘使用写入加速器**


### <a name="cost-conscious-azure-storage-configuration"></a>高性价比的 Azure 存储配置
下表显示了客户还用于在 Azure VM 上托管 SAP HANA 的 VM 类型的配置。 其中可能有些 VM 类型无法满足 SAP HANA 的所有最低存储准则，或者不受 SAP HANA 的正式支持。 但到目前为止，这些 VM 在非生产场景中的表现似乎不错。 /hana/data 和 /hana/log 合并为一个卷 。 因此，Azure 写入加速器的使用可能成为 IOPS 方面的限制。

> [!NOTE]
> 对于支持 SAP 的场景，请在 [IAAS 的 SAP 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中查看特定的 VM 类型是否受 SAP HANA 的支持。

> [!NOTE]
> 为实现高性价比的解决方案而从之前的建议进行一项更改，即从 [Azure 标准 HDD 磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)转变为性能更好、更可靠的 [Azure 标准 SSD 磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)

这些建议通常超出了本文前面所述的 SAP 最低要求。 列出的建议是 SAP 提供的大小建议与不同 VM 类型提供的最大存储吞吐量之间的折衷方案。

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data and /hana/log<br /> 使用 LVM 或 MDADM 进行条带化 | /hana/shared | /root 卷 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1,200 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2,000 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1,000 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1,000 GiB | 1,000 MB/秒 | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1,750 GiB | 1,000 MB/秒 | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2,000 GiB | 2,000 MB/秒 |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3,800 GiB | 2,000 MB/秒 | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2,000 MB/秒 | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


根据 [SAP TDI 存储白皮书](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，对于采用 3 x P20 配置的较小 VM 类型，建议的磁盘空间大于建议的卷空间。 但是，表中显示的选项旨在为 SAP HANA 提供足够的磁盘吞吐量。 如果需要改用 **/hana/backup** 卷（该卷的大小适合保留相当于两倍内存卷的备份），请任意调整配置。   
检查建议的不同卷的存储吞吐量是否满足所要运行的工作负载。 如果工作负荷要求对 **/hana/data** 和 **/hana/log** 使用更高规格的卷，则需要增加 Azure 高级存储 VHD。 对于包含的 VHD 数目比所列数目更多的卷，调整其大小会在 Azure 虚拟机类型的限制范围内增大 IOPS 和 I/O 吞吐量。 

> [!NOTE]
> 上述配置并不会受益于 [Azure 虚拟机单一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)，因为此配置混合使用了 Azure 高级存储和 Azure 标准存储。 但是，这种选择可以优化成本。 需要为上面列作 Azure 标准存储 (Sxx) 的所有磁盘选择高级存储，才能使 VM 配置符合 Azure 的单一 VM SLA。


> [!NOTE]
> 所述磁盘配置建议针对 SAP 向其基础设施提供商表达的最低要求。 在实际的客户部署和工作负载场景中，遇到了这些建议仍未提供足够功能的情况。 这些情况可能是客户在 HANA 重启后需要更快地重新加载数据，或者备份配置需要更高的存储带宽。 其他情况包括 **/hana/log**，其中 5000 IOPS 不足以满足特定工作负荷。 因此，请将这些建议作为起点，并根据工作负载的要求进行调整。
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>适用于 SAP HANA 的 Azure 超级磁盘存储配置
Microsoft 正推出一种新 Azure 存储类型，名为 [Azure 超级磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)。 目前提供的 Azure 存储与超级磁盘之间的显著区别在于，磁盘功能不再受到磁盘大小的约束。 作为客户，你可以为超级磁盘定义以下功能：

- 磁盘大小范围（从 4 GiB 到 65,536 GiB）
- IOPS 范围（从 100 IOPS 到 160K IOPS，最大值也取决于 VM 类型）
- 存储吞吐量（从 300 MB/秒到 2,000 MB/秒）

超级磁盘使你能够定义一个磁盘来满足大小、IOPS 和磁盘吞吐量范围。 无需使用基于 Azure 高级存储的 LVM 或 MDADM 等逻辑卷管理器来构造可满足 IOPS 和存储吞吐量要求的卷。 可以在超级磁盘和高级存储之间运行配置组合。 因此，你可以将超级磁盘的使用限制为性能关键型 /hana/data 和 /hana/log 卷，并覆盖使用 Azure 高级存储的其他卷

与高级存储相比，超级磁盘的其他优点可能是读取延迟更佳。 如果希望减少 HANA 启动时间以及向内存中后续加载数据，速度较快的读取延迟可能会有优势。 如果 HANA 在编写保存点，超级磁盘存储的优势也可以体现出来。 由于 /hana/data 的高级存储磁盘通常不通过写入加速器缓存，因此与超级磁盘相比，高级存储上对 /hana/data 的写入延迟更高。 预期情况可能是这样：使用超级磁盘编写的保存点在超级磁盘上的性能更好。

> [!IMPORTANT]
> 超级磁盘并非在所有 Azure 区域都提供，并且还尚不完全支持下面列出的 VM 类型。 有关超级磁盘的可用区域以及受支持的 VM 系列的详细信息，请参阅文章 [Azure 中提供哪些可用磁盘类型？](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)。

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>生产推荐的使用纯超级磁盘配置的存储解决方案
在此配置中，可以单独保留 /hana/data 和 /hana/log 卷。 建议的值派生自 KPI，即 SAP 必须根据 [SAP TDI 存储白皮书](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)中建议的对 SAP HANA 和存储配置的 VM 类型进行认证。

这些建议通常超出了本文前面所述的 SAP 最低要求。 列出的建议是 SAP 提供的大小建议与不同 VM 类型提供的最大存储吞吐量之间的折衷方案。

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data 卷 | /hana/data I/O 吞吐量 | /hana/data IOPS | /hana/log 卷 | /hana/log I/O 吞吐量 | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1,200 MB/秒 | 600 GB | 700 MBps | 7,500 | 512 GB | 500 MBps  | 2,000 |
| M32ts | 192 GiB | 500 MB/秒 | 250 GB | 400 MBps | 7,500 | 256 GB | 250 MBps  | 2,000 |
| M32ls | 256 GiB | 500 MB/秒 | 300 GB | 400 MBps | 7,500 | 256 GB | 250 MBps  | 2,000 |
| M64ls | 512 GiB | 1,000 MB/秒 | 600 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M64s | 1,000 GiB | 1,000 MB/秒 |  1,200 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M64ms | 1,750 GiB | 1,000 MB/秒 | 2,100 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M128s | 2,000 GiB | 2,000 MB/秒 |2,400 GB | 1,200 MBps |9,000 | 512 GB | 800 MBps  | 3,000 | 
| M128ms | 3,800 GiB | 2,000 MB/秒 | 4,800 GB | 1200 MBps |9,000 | 512 GB | 800 MBps  | 3,000 | 
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 3,500 GB | 1,000 MBps | 9,000 | 512 GB | 400 MBps  | 2,500 | 
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 7,200 GB | 1,000 MBps | 9,000 | 512 GB | 400 MBps  | 2,500 | 
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 7,200 GB | 1,500 MBps | 9,000 | 512 GB | 800 MBps  | 3,000 | 
| M416ms_v2 | 11,400 GiB | 2,000 MB/秒 | 14,400 GB | 1,500 MBps | 9,000 | 512 GB | 800 MBps  | 3,000 |   

列出的值旨在作为起点，并需要根据实际需求进行评估。 Azure 超级磁盘的优点是可以调整 IOPS 和吞吐量的值，而无需关闭 VM 或停止应用于系统的工作负载。   

> [!NOTE]
> 到目前为止，超级磁盘存储的存储快照不可用。 这会阻止 Azure 备份服务对 VM 快照的使用

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>使用纯超级磁盘配置的高性价比存储解决方案
在此配置中，可以将 /hana/data 和 /hana/log 卷保留在同一磁盘上。 建议的值派生自 KPI，即 SAP 必须根据 [SAP TDI 存储白皮书](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)中建议的对 SAP HANA 和存储配置的 VM 类型进行认证。 

这些建议通常超出了本文前面所述的 SAP 最低要求。 列出的建议是 SAP 提供的大小建议与不同 VM 类型提供的最大存储吞吐量之间的折衷方案。

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data 和 /log 卷 | /hana/data 和 log I/O 吞吐量 | /hana/data 和 log IOPS |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1,200 MB/秒 | 1,200 GB | 1,200 MBps | 9,500 | 
| M32ts | 192 GiB | 500 MB/秒 | 512 GB | 400 MBps | 9,500 | 
| M32ls | 256 GiB | 500 MB/秒 | 600 GB | 400 MBps | 9,500 | 
| M64ls | 512 GiB | 1,000 MB/秒 | 1,100 GB | 900 MBps | 10,000 | 
| M64s | 1,000 GiB | 1,000 MB/秒 |  1,700 GB | 900 MBps | 10,000 | 
| M64ms | 1,750 GiB | 1,000 MB/秒 | 2,600 GB | 900 MBps | 10,000 | 
| M128s | 2,000 GiB | 2,000 MB/秒 |2,900 GB | 1,800 MBps |12,000 | 
| M128ms | 3,800 GiB | 2,000 MB/秒 | 5,300 GB | 1,800 MBps |12,000 |  
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 4,000 GB | 900 MBps | 10,000 |  
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 7,700 GB | 900 MBps | 10,000 | 
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 7,700 GB | 1,800MBps | 12,000 |  
| M416ms_v2 | 11,400 GiB | 2,000 MB/秒 | 15,000 GB | 1,800 MBps | 12,000 |    

列出的值旨在作为起点，并需要根据实际需求进行评估。 Azure 超级磁盘的优点是可以调整 IOPS 和吞吐量的值，而无需关闭 VM 或停止应用于系统的工作负载。  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Azure NetApp 文件上的 NFS v4.1 卷
Azure NetApp 文件提供原生 NFS 共享，可用于 /hana/shared、/hana/data 和 /hana/log 卷。 对 /hana/data 和 /hana/log 卷使用基于 ANF 的 NFS 共享需要使用 v4.1 NFS 协议。 当共享基于 ANF 时，NFS 协议 v3 不支持使用 /hana/data和 /hana/log 卷。 

> [!IMPORTANT]
> 在 Azure NetApp 文件上实现的 NFS v3 协议不支持用于 /hana/data 和 /hana/log。 从功能角度来看，对于 /hana/data 和 /hana/log 卷，NFS 4.1 是必需的。 而对于 /hana/shared 卷，则可以从功能角度使用 NFS v3 或 NFS v4.1 协议。

### <a name="important-considerations"></a>重要注意事项
在考虑将 Azure NetApp 文件用于 SAP Netweaver 和 SAP HANA 时，请注意以下重要注意事项：

- 最小容量池是 4 TiB。  
- 最小卷大小为 100 GiB
- Azure NetApp 文件和所有虚拟机（将装载 Azure NetApp 文件卷）必须位于同一 Azure 虚拟网络中或同一区域中的[对等虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)中。  
- 所选的虚拟网络必须具有一个委托给 Azure NetApp 文件的子网。
- Azure NetApp 卷的吞吐量是卷配额和服务级别的函数，如 [Azure NetApp 文件服务级别](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 调整 HANA Azure NetApp 卷的大小时，请确保生成的吞吐量满足 HANA 系统要求。  
- Azure NetApp 文件提供[导出策略](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)：你可以对允许的客户端、访问类型（读写、只读等）进行控制。 
- Azure NetApp 文件功能尚没有区域感知性。 当前，Azure NetApp 文件功能未部署在 Azure 区域中的所有可用性区域中。 请注意某些 Azure 区域的潜在延迟影响。  
- 将虚拟机部署在靠近 Azure NetApp 存储的位置以降低延迟，这一点非常重要。 
- 虚拟机上 sidadm 的用户 ID 和 `sapsys` 的组 ID 必须与 Azure NetApp 文件中的配置相匹配<b></b>。 

> [!IMPORTANT]
> 对于 SAP HANA 工作负载，低延迟至关重要。 与 Microsoft 代表合作，确保虚拟机和 Azure NetApp 文件卷在邻近的地方部署。  

> [!IMPORTANT]
> 如果在虚拟机和 Azure NetApp 配置之间，sidadm 的用户 ID 和 `sapsys` 组 ID 不匹配，虚拟机上装载的 Azure NetApp 卷上的文件权限将显示为 `nobody`<b></b>。 在[载入新系统](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)到 Azure NetApp 文件时，请确保指定的 sidadm 的用户 ID 和 `sapsys` 的组 ID<b></b> 准确无误。

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>调整 Azure NetApp 文件上的 HANA 数据库的大小

Azure NetApp 卷的吞吐量是卷大小和服务级别的函数，如 [Azure NetApp 文件服务级别](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 

在 Azure 中为 SAP 设计基础结构时，应了解 SAP 的一些最低存储吞吐量要求，这可转化为以下最低吞吐量特征：

- 在 I/O 大小为 1 MB、速率为 250 MB/秒的 /hana/log 上启用读/写  
- 为 16 MB 和 64 MB I/O 大小的 /hana/data 启用至少 400 MB/秒的读取活动  
- 为 16 MB 和 64 MB I/O 大小的 /hana/data 启用至少 250MB/秒的写入活动  

每 1 TiB 的卷配额的 [Azure NetApp 文件吞吐量限制](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)为：
- 高级存储层 - 64 MiB/秒  
- 超级存储层 - 128 MiB/秒  

> [!IMPORTANT]
> 无论在单个 NFS 卷上部署的容量如何，预计吞吐量都将 1.2-1.4 GB/秒的带宽范围（由虚拟机中的使用者利用）内稳定下来。 这与 ANF 产品/服务的基础体系结构以及围绕 NFS 的相关 Linux 会话限制有关。 [Azure NetApp 文件的性能基准测试结果](https://docs.microsoft.com/azure/azure-netapp-files/performance-benchmarks-linux)一文中记录的性能和吞吐量数针对具有多个客户端 VM 的一个共享 NFS 卷而执行，因此具有多个会话。 这种情况不同于我们在 SAP 中测量的情况。 我们针对 NFS 卷衡量单个 VM 的吞吐量的位置。 托管在 ANF 上。

为了满足 SAP 数据和日志的最低吞吐量要求，并根据 `/hana/shared` 准则，建议的大小如下所示：

| 数据量(Volume) | 大小<br /> 高级存储层 | 大小<br /> 超级存储层 | 支持的 NFS 协议 |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/shared | 最大（512 GB，1xRAM），每 4 个辅助角色节点 | 最大（512 GB，1xRAM），每 4 个辅助角色节点 | v3 或 v4.1 |


> [!NOTE]
> 此处所述 Azure NetApp 文件大小调整建议的目的在于满足 SAP 向其基础设施提供商表达的最低要求。 在实际客户部署和工作负载场景中，这可能还不够。 请将这些建议作为起点，并根据具体工作负载的要求进行调整。  

因此，可以考虑对已为超级磁盘存储列出的 ANF 卷部署类似的吞吐量。 同时请考虑为不同 VM SKU 的卷列出的大小情况，正如超级磁盘表中所做的那样。

> [!TIP]
> 可以动态调整 Azure NetApp 文件卷的大小，而无需 `unmount` 卷、停止虚拟机或停止 SAP HANA。 这具有灵活性，可同时满足应用程序的预期和不可预见的吞吐量需求。

有关如何使用 ANF 中托管的 NFS v4.1 卷部署带备用节点的 SAP HANA 横向扩展配置的文档，已在以下位置发布：[使用 SUSE Linux Enterprise Server 上的 Azure NetApp 文件在 Azure VM 上进行带备用节点的 SAP HANA 横向扩展](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)。


## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅：

- [Azure 虚拟机的 SAP HANA 高可用性指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)。
