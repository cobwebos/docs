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
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1b363c9da195794f6356539ffca46101edf431c2
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533873"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 虚拟机存储配置

Azure 提供了不同类型的存储, 适用于运行 SAP HANA 的 Azure Vm。 可被视为 SAP HANA 部署列表的 Azure 存储类型如下所示: 

- 标准 SSD 磁盘驱动器 (SSD)
- 高级固态硬盘 (SSD)
- [超磁盘](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/disks-enable-ultra-ssd) 

若要了解这些磁盘类型, 请参阅[选择磁盘类型一](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)文

Azure 针对 Azure 标准和高级存储上的 VHD 提供两种部署方法。 如果总体方案允许，请利用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)部署。 

有关存储类型的列表及其 IOPS 和存储吞吐量方面的 SLA，请查看[有关托管磁盘的 Azure 文档](https://azure.microsoft.com/pricing/details/managed-disks/)。

**推荐结合 Azure 写入加速器使用 Azure 高级存储, 并使用 Azure 托管磁盘进行部署**

在本地环境中, 很少需要关心 i/o 子系统及其功能。 原因在于，设备供应商会确保满足 SAP HANA 的最低存储要求。 当你自行构建 Azure 基础结构时, 应注意其中的某些要求。 要求的某些最小吞吐量特征是:

- 启用读/写, **/hana/log**为 250 mb/秒, 大小为 1 mb i/o
- 为 16 MB 和 64 MB I/O 大小的 /hana/data 启用至少 400 MB/秒的读取活动
- 为 16 MB 和 64 MB I/O 大小的 /hana/data 启用至少 250MB/秒的写入活动

如果低存储延迟对于 DBMS 系统至关重要（即使对于 SAP HANA 等 DBMS 系统也是如此），请将数据保留在内存中。 存储中的关键路径通常围绕 DBMS 系统的事务日志写入。 同时，在故障恢复之后写入保存点或加载内存中数据的操作可能也很关键。 因此,**必须**对 **/hana/data**和 **/Hana/log**卷使用 Azure 高级磁盘。 为了达到 SAP 所需的最低 **/hana/log** 和 **/hana/data** 吞吐量，需要使用 MDADM 或 LVM 基于多个 Azure 高级存储磁盘构建 RAID 0。 将 RAID 卷用作 **/hana/data** 和 **/hana/log** 卷。 

**推荐作为 RAID 0 的条带大小, 建议使用:**

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

**推荐通过 SAP HANA 这些观察到的 i/o 模式, 使用 Azure 高级存储的不同卷的缓存应该设置如下:**

- **/hana/data** - 无缓存
- **/hana/log** - 无缓存 - M 系列例外（详见本文稍后部分）
- **/hana/shared** - 读取缓存


在确定 VM 大小或决定 VM 时，还要考虑总体 VM I/O 吞吐量。 [内存优化虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)一文中记录了总体 VM 存储吞吐量。

## <a name="linux-io-scheduler-mode"></a>Linux I/O 计划程序模式
Linux 提供多种不同的 I/O 计划模式。 通过 Linux 供应商和 SAP 的常见建议是将磁盘卷的 i/o 调度器模式从**cfq**模式重新配置为**noop**模式。 请参考 [SAP 说明 #1984798](https://launchpad.support.sap.com/#/notes/1984787) 中的详细信息。 


## <a name="production-storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Azure M 系列虚拟机 Azure 写入加速器的生产存储解决方案
Azure 写入加速器是专门针对 Azure M 系列 VM 推出的一项功能。 顾名思义，该功能的目的是改善针对 Azure 高级存储执行的写入操作的 I/O 延迟。 在 SAP HANA 中，预期只能对 **/hana/log** 卷使用写入加速器。 因此, **/hana/data**和 **/hana/log**是单独的卷, 其 Azure 写入加速器仅支持 **/hana/log**卷。 

> [!IMPORTANT]
> Azure M 系列虚拟机的 SAP HANA 认证要求中规定，Azure 写入加速器只能用于 **/hana/log** 卷。 因此，在 Azure M 系列虚拟机上的生产场景 SAP HANA 部署中，应该配置 **/hana/log** 卷使用的 Azure 写入加速器。  

> [!NOTE]
> 对于生产场景，请在 [IAAS 的 SAP 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中查看特定的 VM 类型是否受 SAP HANA 的支持。

**推荐建议用于生产方案的配置如下所示:**

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

Microsoft 还不会向公众提供 M416xx_v2 VM 类型。 检查不同建议的卷的存储吞吐量是否符合你要运行的工作负荷。 如果工作负荷要求对 **/hana/data** 和 **/hana/log** 使用更高规格的卷，则需要增加 Azure 高级存储 VHD。 使用比列出的 Vhd 更多的卷调整容量可在 Azure 虚拟机类型限制范围内提高 IOPS 和 i/o 吞吐量。

只能配合 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)使用 Azure 写入加速器。 因此，至少需要将构成 **/hana/log** 卷的 Azure 高级存储磁盘部署为托管磁盘。

Azure 写入加速器在每个 VM 中支持的 Azure 高级存储 VHD 数目有限制。 当前限制为：

- 适用于 M128xx 和 M416xx VM 的16个 Vhd
- 8个 Vhd, 适用于 M64xx 和 M208xx VM
- M32xx VM 限制为 4 个 VHD

在[写入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)一文中可以找到有关如何启用 Azure 写入加速器的更多详细说明。

此文中还介绍了 Azure 写入加速器的详细信息和限制。

**推荐需要为构成/hana/log 卷的磁盘使用写入加速器**


## <a name="cost-conscious-azure-storage-configuration"></a>高性价比的 Azure 存储配置
下表显示了客户用于在 Azure Vm 上托管 SAP HANA 的 VM 类型的配置。 可能有一些 VM 类型可能无法满足 SAP HANA 的所有最小存储条件, 也可能不是 SAP SAP HANA 正式支持。 但到目前为止，这些 VM 在非生产场景中的表现似乎不错。 **/Hana/data**和 **/hana/log**组合到一个卷中。 因此, Azure 写入加速器的使用可能会对 IOPS 产生限制。

> [!NOTE]
> 对于 SAP 支持的方案, 请检查[IAAS 的 sap 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中的 sap SAP HANA 是否支持某个 VM 类型。

> [!NOTE]
> 对于成本意识解决方案, 以前的建议的更改是从[Azure 标准 HDD 磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)转移到更好的性能和更可靠的[Azure 标准 SSD 磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)


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


Microsoft 还不会向公众提供 M416xx_v2 VM 类型。 根据 [SAP TDI 存储白皮书](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，对于采用 3 x P20 配置的较小 VM 类型，建议的磁盘空间大于建议的卷空间。 但是，表中显示的选项旨在为 SAP HANA 提供足够的磁盘吞吐量。 如果需要改用 **/hana/backup** 卷（该卷的大小适合保留相当于两倍内存卷的备份），请任意调整配置。   
检查不同建议的卷的存储吞吐量是否符合你要运行的工作负荷。 如果工作负荷要求对 **/hana/data** 和 **/hana/log** 使用更高规格的卷，则需要增加 Azure 高级存储 VHD。 使用比列出的 Vhd 更多的卷调整容量可在 Azure 虚拟机类型限制范围内提高 IOPS 和 i/o 吞吐量。 

> [!NOTE]
> 上述配置并不会受益于 [Azure 虚拟机单一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)，因为此配置混合使用了 Azure 高级存储和 Azure 标准存储。 但是，这种选择可以优化成本。 需要为上面列作 Azure 标准存储 (Sxx) 的所有磁盘选择高级存储，才能使 VM 配置符合 Azure 的单一 VM SLA。


> [!NOTE]
> 所述磁盘配置建议针对 SAP 向其基础设施提供商表达的最低要求。 在实际的客户部署和工作负载场景中，遇到了这些建议仍未提供足够功能的情况。 这些情况可能是客户在 HANA 重启后需要更快地重新加载数据，或者备份配置需要更高的存储带宽。 其他情况包括 **/hana/log**，其中 5000 IOPS 不足以满足特定工作负荷。 因此，请将这些建议作为起点，并根据工作负载的要求进行调整。
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>适用于 SAP HANA 的 Azure Ultra 磁盘存储配置
Microsoft 正在推出一种新的 Azure 存储类型 (称为[Azure Ultra 磁盘](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#ultra-disk))。 目前为止, Azure 存储空间和超磁盘之间的最大区别是磁盘功能不再绑定到磁盘大小。 作为客户, 你可以为超磁盘定义以下功能:

- 磁盘大小, 范围从 4 GiB 到 65536 GiB
- IOPS 范围为 100 IOPS 到 160K IOPS (最大值取决于 VM 类型)
- 从 300 MB/秒到 2000 MB/秒的存储吞吐量

超磁盘允许定义满足大小、IOPS 和磁盘吞吐量范围的单个磁盘。 不要使用逻辑卷管理器 (例如, 在 Azure 高级存储上使用 LVM 或 MDADM) 来构建满足 IOPS 和存储吞吐量要求的卷。 可以在超磁盘与高级存储之间运行配置混合。 因此, 你可以将超磁盘的使用限制为性能关键的/hana/data 和/hana/log 卷, 并覆盖包含 Azure 高级存储的其他卷

> [!IMPORTANT]
> 所有 Azure 区域中都尚未提供 Ultra 磁盘, 还不支持所有 VM 类型。 有关可用的超磁盘和受支持的 VM 系列的详细信息, 请参阅文章[Azure 中有哪些磁盘类型可用？](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#ultra-disk)。

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data 卷 | /hana/data i/o 吞吐量 | /hana/data IOPS | /hana/log 卷 | /hana/log i/o 吞吐量 | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1200 MB/秒 | 600 GB | 700 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M32ts | 192 GiB | 500 MB/秒 | 250 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M32ls | 256 GiB | 500 MB/秒 | 300 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M64ls | 512 GiB | 1000 MB/秒 | 600 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64s | 1000 GiB | 1000 MB/秒 |  1200 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64ms | 1750 GiB | 1000 MB/秒 | 2100 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M128s | 2000 GiB | 2000 MB/秒 |2400 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M128ms | 3800 GiB | 2000 MB/秒 | 4800 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M208s_v2 | 2850 GiB | 1000 MB/秒 | 3500 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M208ms_v2 | 5700 GiB | 1000 MB/秒 | 7200 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M416s_v2 | 5700 GiB | 2000 MB/秒 | 7200 GB | 1500M Bps | 9000 | 512 GB | 800 MBps  | 2000 | 
| M416ms_v2 | 11400 GiB | 2000 MB/秒 | 14400 GB | 1500 MBps | 9000 | 512 GB | 800 MBps  | 2000 |   

Microsoft 还不会向公众提供 M416xx_v2 VM 类型。 列出的值旨在作为起点, 需要根据实际需求进行评估。 Azure Ultra 磁盘的优点是, 可以对 IOPS 和吞吐量的值进行调整, 而无需关闭 VM 或停止应用到系统的工作负载。   

> [!NOTE]
> 到目前为止, 使用超高磁盘存储的存储快照不可用。 这会阻止使用 Azure 备份服务的 VM 快照

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅：

- [SAP HANA Azure 虚拟机的高可用性指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)。