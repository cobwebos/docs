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
ms.date: 08/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 074171d658eb4e1e029652c9c0851e082ba043fe
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053433"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 虚拟机存储配置

对于正在运行 SAP HANA 的 Azure VM，Azure 提供其他适用的存储类型。 下面列出了一些可用于 SAP HANA 部署的 SAP HANA 认证的 Azure 存储类型： 

- Azure 高级 SSD 或高级存储 
- [超级磁盘](../../linux/disks-enable-ultra-ssd.md)
- [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/) 

若要了解这些磁盘类型，请参阅[Azure 存储类型 SAP 工作负荷](./planning-guide-storage.md)一文和[选择磁盘类型](../../linux/disks-types.md)

Azure 为 Azure 标准和高级存储上的 Vhd 提供了两种部署方法。 我们希望你能够利用 azure[托管磁盘](https://azure.microsoft.com/services/managed-disks/)进行 azure 块存储部署。 

有关存储类型的列表及其 IOPS 和存储吞吐量方面的 SLA，请查看[有关托管磁盘的 Azure 文档](https://azure.microsoft.com/pricing/details/managed-disks/)。

> [!IMPORTANT]
> 无论选择何种 Azure 存储类型，对于特定操作系统和 DBMS，都需要 SAP 支持该存储所使用的文件系统。 [SAP 支持说明 #405827](https://launchpad.support.sap.com/#/notes/405827) 列出了不同操作系统和数据库的支持文件系统，包括 SAP HANA。 这适用于 SAP HANA 可能访问的所有卷（以便为任何任务进行读取和写入）。 若为 SAP HANA 专门使用 Azure 上的 NFS，NFS 版本的其他限制将按本文后面的说明适用 


不同存储类型的最低 SAP HANA 认证条件为： 

- Azure 高级存储- **/hana/log**需要受 azure[写入加速器](../../linux/how-to-enable-write-accelerator.md)支持。 **/Hana/data**卷可放置在不带 Azure 写入加速器的高级存储或超磁盘上
- Azure Ultra 磁盘至少适用于 **/hana/log**卷。 **/Hana/data**卷可放置在无 Azure 写入加速器的高级存储上，或者用于获取超磁盘的重新启动时间
- **NFS** v2.0 上 **/hana/log 和/Hana/data**的 Azure NetApp 文件的卷。 /Hana/shared 的卷可以使用 NFS v3 或 NFS v2.0 协议

可以组合某些存储类型。 例如，可以将 **/hana/data**放在高级存储上，而 **/hana/log**可放置在超小型磁盘存储上，以便达到所需的低延迟。 如果将基于和的卷用于 **/hana/data**，则 **/hana/log**卷也需要基于和顶层的 NFS。 **不支持**在和上将 NFS 用于某个卷 (例如/hana/data) 、Azure 高级存储或超磁盘 (如 **/hana/log**) ）。

在本地环境中，很少需要考虑 I/O 子系统及其功能。 原因在于，设备供应商会确保满足 SAP HANA 的最低存储要求。 在自行构建 Azure 基础结构时，你应该注意其中的一些 SAP 颁发的要求。 SAP 所建议的一些最小吞吐量特征包括：

- 每秒 **/hana/log** 250 mb/秒，读取/写入大小为 1 mb 的 i/o 大小
- 对于 **/hana/data** ，至少 400 mb/秒的读取活动，适用于 16 mb 和 64 mb i/o 大小
- 对于具有 16 MB 和 64 MB i/o 大小的 **/hana/data** ，至少 250 mb/秒的写入活动

如果低存储延迟对于 DBMS 系统至关重要（即使对于 SAP HANA 等 DBMS 系统也是如此），请将数据保留在内存中。 存储中的关键路径通常围绕 DBMS 系统的事务日志写入。 同时，在故障恢复之后写入保存点或加载内存中数据的操作可能也很关键。 因此，**必须**对 **/hana/data**和 **/hana/log**卷使用 Azure 高级存储、Ultra 磁盘或和。 


选择 HANA 存储配置时，一些指导原则可以按如下方式列出：

- 根据[SAP 工作负荷的 Azure 存储类型](./planning-guide-storage.md)决定存储类型，并[选择磁盘类型](../../linux/disks-types.md)
- 调整或确定 VM 时的总体 VM i/o 吞吐量和 IOPS 限制。 [内存优化虚拟机大小](../../sizes-memory.md)一文中记录了总体 VM 存储吞吐量
- 在确定存储配置时，请尝试将 VM 的总体吞吐量保持在 **/hana/data**卷配置的下方。 写入保存点，SAP HANA 可以是主动的、发出的 i/o。 写入保存点时，可以轻松地将 **/hana/data**卷的吞吐量限制增加到最大值。 如果) 生成 **/hana/data**卷的 (磁盘的吞吐量比 VM 允许的吞吐量更高，则可能会出现以下情况：保存点写入利用的吞吐量与重做日志写入操作的吞吐量要求产生了干扰。 可能影响应用程序吞吐量的情况
- 如果你使用的是 Azure 高级存储，开销最少的配置是使用逻辑卷管理器来构建带区集以生成 **/hana/data**和 **/hana/log**卷

> [!IMPORTANT]
> 存储配置的建议旨在作为开始的指导。 运行工作负荷和分析存储利用率模式，你可能会意识到未利用所有提供的存储带宽或 IOPS。 可以考虑在存储上缩小。 相反，工作负荷可能需要比这些配置所建议的更多的存储吞吐量。 因此，你可能需要部署更多的容量、IOPS 或吞吐量。 在所需的存储容量、需要的存储延迟、所需的存储吞吐量和 IOPS 之间，Azure 提供了足够多的不同存储类型，具有不同的功能和不同的价格，可以为你和 HANA 工作负荷查找并调整到适当的折衷。

## <a name="linux-io-scheduler-mode"></a>Linux I/O 计划程序模式
Linux 提供多种不同的 I/O 计划模式。 Linux 供应商和 SAP 的常见建议是重新配置磁盘卷的 I/O 调度程序模式，即从“mq-deadline”或“kyber”模式配置为“noop”(non-multiqueue) 或“none”(multiqueue) 模式   。 请参考 [SAP 说明 #1984787](https://launchpad.support.sap.com/#/notes/1984787) 中的详细信息。 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>适用于 Azure M 系列虚拟机的高级存储和 Azure 写入加速器的解决方案
Azure 写入加速器是可用于 Azure M 系列 VM 的一项功能。 作为名称状态，该功能的目的是改善针对 Azure 高级存储的写入操作的 i/o 延迟。 在 SAP HANA 中，预期只能对 **/hana/log** 卷使用写入加速器。 因此，/hana/data 和 /hana/log 是单独的卷，其中 Azure 写入加速器仅支持 /hana/log 卷  。 

> [!IMPORTANT]
> 使用 Azure 高级存储时，对 **/hana/log**卷使用 azure[写入加速器](../../linux/how-to-enable-write-accelerator.md)是必需的。 写入加速器仅适用于高级存储和 M 系列和 Mv2 系列 Vm。 写入加速器与其他 Azure VM 系列（如 Esv3 或 Edsv4）不结合工作。

下面列出了适用于 Azure 高级磁盘的缓存建议，这些建议如下所示 SAP HANA：

- 几乎没有任何针对 HANA 数据文件的读取工作负荷。 重启 HANA 实例后或者在将数据载入 HANA 时出现的大型 I/O 例外。 执行 HANA 数据库备份后，也可能出现大量针对数据文件的读取 I/O。 因此，大多数情况下读取缓存没有意义，因为在大多数情况下，需要完全读取所有数据文件卷。 
- HANA 写入点和 HANA 故障恢复会产生大量针对数据文件的写入。 写入保存点是异步进行的，不阻止任何用户事务。 在故障恢复期间写入数据对性能要严格要求，以便系统再次快速响应。 但是，故障恢复更确切地说应该是异常情况
- 几乎不会从 HANA 重做文件进行任何读取。 执行事务日志备份、故障恢复或在 HANA 实例重启阶段出现的大型 I/O 例外。  
- 针对 SAP HANA 重做日志文件的主要负载是写入负载。 根据工作负载的性质，I/O 可以小至 4 KB，也可达到 1 MB 或更大。 针对 SAP HANA 重做文件的写入延迟对性能要很高要求。
- 需要以一种可靠的方式在磁盘上保留所有写入

**建议：通过 SAP HANA 观察到的 i/o 模式，使用 Azure 高级存储的不同卷的缓存应该设置如下：**

- **/hana/data** -无缓存或读缓存
- **/hana/log** - 无缓存；M- 和 Mv2 系列除外，这两个系列应启用写入加速器，但不读取缓存。 
- **/hana/shared** - 读取缓存
- **OS 磁盘**-不要更改 Azure 在创建 VM 时设置的默认缓存


如果使用 LVM 或 mdadm 跨多个 Azure 高级磁盘构建条带集，则需要定义条带大小。 这些大小在 **/hana/data**和 **/hana/log**之间有所不同。 **建议：作为条带大小，建议使用：**

- 256 KB 的 /hana/data
- **/hana/log** 64 KB

> [!NOTE]
> **/Hana/data**的条带大小已从较早的建议中更改为 64 kb 或 128 kb 256，并基于具有更多最新 Linux 版本的客户体验。 256 KB 的大小是提供略微改善的性能。 我们还将 **/hana/log**的条带大小建议从 32 kb 改为 64 kb，以获得具有更大 i/o 大小的足够吞吐量。

> [!NOTE]
> 不需要使用 RAID 卷配置任何冗余级别，因为 Azure 块存储会保留 VHD 的三个映像。 使用带 Azure 高级磁盘的带区集纯粹用于配置提供足够的 IOPS 和/或 i/o 吞吐量的卷。

在条带集下堆积大量 Azure Vhd，可从 IOPS 和存储吞吐量端累积总计。 因此，如果你将条带集放在超过 3 x P30 的 Azure 高级存储磁盘上，则它应为你分配三倍的 IOPS 和单个 Azure 高级存储 P30 磁盘的存储吞吐量三倍。

> [!IMPORTANT]
> 如果使用 LVM 或 mdadm 作为卷管理器跨多个 Azure 高级磁盘创建条带集，则不能将这三个 SAP HANA 文件系统/data sources、/log 和/shared 置于默认或根卷组。 强烈建议遵循 Linux 供应商指南，该指南通常用于为 /data、/log 和 /shared 创建各自的卷组。


### <a name="azure-burst-functionality-for-premium-storage"></a>适用于高级存储的 Azure 猝发功能
对于容量较小或等于 512 GiB 的 Azure 高级存储磁盘，提供了突发功能。 磁盘突发如何工作的确切方式在 "[磁盘突发](../../linux/disk-bursting.md)" 一文中进行了介绍。 当您阅读本文时，您将了解在以下情况下，当 i/o 工作负荷低于磁盘的名义 IOPS 和吞吐量时，所产生的的 IOPS 和吞吐量的概念 (有关名义吞吐量的详细信息，请参阅[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)) 。 你将在当前使用情况与磁盘的名义值之间累计 IOPS 和吞吐量的增量。 猝发次数限制为最多30分钟。

可以在其中规划此突发功能的理想情况可能是包含不同 DBMS 的数据文件的卷或磁盘。 需要对这些卷执行 i/o 工作负荷，尤其是对于小到中等范围的系统，应如下所示：

- 从低到中等的读取工作负荷，因为数据理想的缓存在内存中，
- 数据库检查点或定期发出的保存点触发的突发写入数
- 在不通过存储快照执行备份的情况下，在连续流中读取的备份工作负荷
- 对于 SAP HANA，在实例重新启动后将数据加载到内存中

尤其是在工作负荷仅每秒处理几百个事务的小型 DBMS 系统上，此类突发功能对于存储事务或重做日志的磁盘或卷也很有用。 针对此类磁盘的预期工作负载如下所示：

- 由于应用程序发出的每个提交都可能触发 i/o 操作，因此，对依赖于工作负荷的磁盘的常规写入以及工作负荷的性质
- 在操作任务的情况下提高吞吐量的工作负荷，如创建或重新生成索引
- 执行事务日志或重做日志备份时，请阅读猝发


### <a name="production-recommended-storage-solution-based-on-azure-premium-storage"></a>基于 Azure 高级存储的生产推荐的存储解决方案

> [!IMPORTANT]
> Azure M 系列虚拟机的 SAP HANA 认证要求中规定，Azure 写入加速器只能用于 **/hana/log** 卷。 因此，在 Azure M 系列虚拟机上的生产场景 SAP HANA 部署中，应该配置 **/hana/log** 卷使用的 Azure 写入加速器。  

> [!NOTE]
> 在涉及 Azure 高级存储的方案中，我们将在配置中实现突发功能。 使用任意形状或窗体的存储测试工具时，请牢记[Azure 高级磁盘突发](../../linux/disk-bursting.md)功能的工作方式。 运行通过 SAP HWCCT 或 HCMT 工具传递的存储测试时，我们不希望所有测试都将通过条件，因为某些测试会超出你可以累积的突发富余额度。 尤其是在所有测试都按顺序运行时，无需中断。


> [!NOTE]
> 对于生产场景，请在 [IAAS 的 SAP 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中查看特定的 VM 类型是否受 SAP HANA 的支持。

**建议：用于生产方案的 Azure 高级存储的推荐配置如下所示：**

SAP **/hana/data**卷的配置：

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data | 最大突发吞吐量 | IOPS | 突发 IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 4 x P6 | 680 MBps | 960 | 14,000 |
| M32ls | 256 GiB | 500 MBps | 4 x P6 | 680 MBps | 960 | 14,000 |
| M64ls | 512 GiB | 1,000 MBps | 4 x P10 |  680 MBps | 2,000 | 14,000 |
| M64s | 1,000 GiB | 1,000 MBps | 4 x P15 | 680 MBps | 4400 | 14,000 |
| M64ms | 1,750 GiB | 1,000 MBps | 4 x P20 | 680 MBps | 9200 | 14,000 |  
| M128s | 2,000 GiB | 2000 MBps | 4 x P20 | 680 MBps | 9200| 14,000 | 
| M128ms | 3,800 GiB | 2000 MBps | 4 x P30 | 800 MBps (预配)  | 20,000 | 无突发 | 
| M208s_v2 | 2,850 GiB | 1,000 MBps | 4 x P30 | 800 MBps (预配)  | 20,000| 无突发 | 
| M208ms_v2 | 5,700 GiB | 1,000 MBps | 4 x P40 | 1000 MBps (预配)  | 25,000 | 无突发 |
| M416s_v2 | 5,700 GiB | 2000 MBps | 4 x P40 | 1000 MBps (预配)  | 25,000 | 无突发 |
| M416ms_v2 | 11,400 GiB | 2000 MBps | 4 x P50 | 2000 MBps (预配)  | 25,000 | 无突发 |


对于 **/hana/log**卷。 此配置如下所示：

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | **/hana/log**卷 | 最大突发吞吐量 | IOPS | 突发 IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 3 x P10 | 510 MBps | 1,500 | 10500 | 
| M32ls | 256 GiB | 500 MBps | 3 x P10 | 510 MBps | 1,500 | 10500 | 
| M64ls | 512 GiB | 1,000 MBps | 3 x P10 | 510 MBps | 1,500 | 10500 | 
| M64s | 1,000 GiB | 1,000 MBps | 3 x P15 | 510 MBps | 3300 | 10500 | 
| M64ms | 1,750 GiB | 1,000 MBps | 3 x P15 | 510 MBps | 3300 | 10500 |  
| M128s | 2,000 GiB | 2000 MBps | 3 x P15 | 510 MBps | 3300 | 10500|  
| M128ms | 3,800 GiB | 2000 MBps | 3 x P15 | 510 MBps | 3300 | 10500 | 
| M208s_v2 | 2,850 GiB | 1,000 MBps | 3 x P15 | 510 MBps | 3300 | 10500 |  
| M208ms_v2 | 5,700 GiB | 1,000 MBps | 3 x P15 | 510 MBps | 3300 | 10500 |  
| M416s_v2 | 5,700 GiB | 2000 MBps | 3 x P15 | 510 MBps | 3300 | 10500 |  
| M416ms_v2 | 11,400 GiB | 2000 MBps | 3 x P15 | 510 MBps | 3300 | 10500 | 


对于其他卷，配置如下所示：

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/shared | /root 卷 | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MBps | 1 x P20 | 1 x P6 | 1 x P6 |
| M32ls | 256 GiB | 500 MBps |  1 x P20 | 1 x P6 | 1 x P6 |
| M64ls | 512 GiB | 1000 MBps | 1 x P20 | 1 x P6 | 1 x P6 |
| M64s | 1,000 GiB | 1,000 MBps | 1 x P30 | 1 x P6 | 1 x P6 |
| M64ms | 1,750 GiB | 1,000 MBps | 1 x P30 | 1 x P6 | 1 x P6 | 
| M128s | 2,000 GiB | 2000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M128ms | 3,800 GiB | 2000 MBps | 1 x P30 | 1 x P10 | 1 x P6 |
| M208s_v2 | 2,850 GiB | 1,000 MBps |  1 x P30 | 1 x P10 | 1 x P6 |
| M208ms_v2 | 5,700 GiB | 1,000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M416s_v2 | 5,700 GiB | 2000 MBps |  1 x P30 | 1 x P10 | 1 x P6 | 
| M416ms_v2 | 11,400 GiB | 2000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 


检查建议的不同卷的存储吞吐量是否满足所要运行的工作负载。 如果工作负荷需要更高的 **/hana/data**和 **/hana/log**卷，则需要增加 Azure 高级存储 vhd 的数量。 对于包含的 VHD 数目比所列数目更多的卷，调整其大小会在 Azure 虚拟机类型的限制范围内增大 IOPS 和 I/O 吞吐量。

只能配合 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)使用 Azure 写入加速器。 至少需要将构成 **/hana/log**卷的 Azure 高级存储磁盘部署为托管磁盘。 可在[写入加速器](../../linux/how-to-enable-write-accelerator.md)一文中找到更详细的 Azure 写入加速器说明和限制。

对于 Azure [Esv3](../../ev3-esv3-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#esv3-series)系列和[Edsv4](../../edv4-edsv4-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#edsv4-series)的 HANA 认证的 vm，需要对 **/HANA/DATA**和 **/hana/log**卷进行和。 或者，需要对 **/hana/log**卷使用 azure Ultra 磁盘存储而不是 azure 高级存储。 因此，Azure 高级存储上的 **/hana/data**卷的配置可能如下所示：

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data | 最大突发吞吐量 | IOPS | 突发 IOPS |
| --- | --- | --- | --- | --- | --- | --- |
| E20ds_v4 | 160 GiB | 480 MBps | 3 x P10 | 510 MBps | 1,500 | 10500 |
| E32ds_v4 | 256 GiB | 768 MBps | 3 x P10 |  510 MBps | 1,500 | 10500|
| E48ds_v4 | 384 GiB | 1152 MBps | 3 x P15 |  510 MBps | 3300  | 10500 | 
| E64ds_v4 | 504 GiB | 1,200 MBps | 3 x P15 |  510 MBps | 3300 | 10500 | 
| E64s_v3 | 432 GiB | 1,200 MB/秒 | 3 x P15 |  510 MBps | 3300 | 10500 | 

对于其他卷（包括超磁盘上的 **/hana/log** ），配置可能如下所示：

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/log 卷 | /hana/log I/O 吞吐量 | /hana/log IOPS | /hana/shared | /root 卷 | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MBps | 80 GB | 250 MBps | 1,800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E32ds_v4 | 256 GiB | 768 MBps | 128 GB | 250 MBps | 1,800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E48ds_v4 | 384 GiB | 1152 MBps | 192 GB | 250 MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64ds_v4 | 504 GiB | 1,200 MBps | 256 GB | 250 MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64s_v3 | 432 GiB | 1,200 MBps | 220 GB | 250 MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |


## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>适用于 SAP HANA 的 Azure 超级磁盘存储配置
其他 Azure 存储类型称为[Azure Ultra 磁盘](../../windows/disks-types.md#ultra-disk)。 目前提供的 Azure 存储与超级磁盘之间的显著区别在于，磁盘功能不再受到磁盘大小的约束。 作为客户，你可以为超级磁盘定义以下功能：

- 磁盘大小范围（从 4 GiB 到 65,536 GiB）
- IOPS 范围（从 100 IOPS 到 160K IOPS，最大值也取决于 VM 类型）
- 存储吞吐量（从 300 MB/秒到 2,000 MB/秒）

超级磁盘使你能够定义一个磁盘来满足大小、IOPS 和磁盘吞吐量范围。 不要使用逻辑卷管理器（例如，在 Azure 高级存储上使用 LVM 或 MDADM）来构建满足 IOPS 和存储吞吐量要求的卷。 可以在超磁盘与高级存储之间运行配置混合。 因此，你可以将超磁盘的使用限制为性能关键的 **/hana/data**和 **/hana/log**卷，并覆盖包含 Azure 高级存储的其他卷

与高级存储相比，Ultra 磁盘的其他优点可能是更好的读取延迟。 如果希望减少 HANA 启动时间以及向内存中后续加载数据，速度较快的读取延迟可能会有优势。 如果 HANA 在编写保存点，超级磁盘存储的优势也可以体现出来。 

> [!NOTE]
> 超级磁盘并非在所有 Azure 区域都提供，并且还尚不完全支持下面列出的 VM 类型。 有关超级磁盘的可用区域以及受支持的 VM 系列的详细信息，请参阅文章 [Azure 中提供哪些可用磁盘类型？](../../windows/disks-types.md#ultra-disk)。

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>生产推荐的使用纯超级磁盘配置的存储解决方案
在此配置中，你需要单独保留 **/hana/data**和 **/hana/log**卷。 建议的值派生自 KPI，即 SAP 必须根据 [SAP TDI 存储白皮书](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)中建议的对 SAP HANA 和存储配置的 VM 类型进行认证。

这些建议通常超出了本文前面所述的 SAP 最低要求。 列出的建议是 SAP 提供的大小建议与不同 VM 类型提供的最大存储吞吐量之间的折衷方案。

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data 卷 | /hana/data I/O 吞吐量 | /hana/data IOPS | /hana/log 卷 | /hana/log I/O 吞吐量 | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MB/秒 | 200 GB | 400 MBps | 2,500 | 80 GB | 250 MB | 1,800 |
| E32ds_v4 | 256 GiB | 768 MB/秒 | 300 GB | 400 MBps | 2,500 | 128 GB | 250 MBps | 1,800 |
| E48ds_v4 | 384 GiB | 1152 MB/秒 | 460 GB | 400 MBps | 3,000 | 192 GB | 250 MBps | 1,800 |
| E64ds_v4 | 504 GiB | 1200 MB/秒 | 610 GB | 400 MBps | 3,500 |  256 GB | 250 MBps | 1,800 |
| E64s_v3 | 432 GiB | 1,200 MB/秒 | 610 GB | 400 MBps | 3,500 | 220 GB | 250 MB | 1,800 |
| M32ts | 192 GiB | 500 MB/秒 | 250 GB | 400 MBps | 2,500 | 96 GB | 250 MBps  | 1,800 |
| M32ls | 256 GiB | 500 MB/秒 | 300 GB | 400 MBps | 2,500 | 256 GB | 250 MBps  | 1,800 |
| M64ls | 512 GiB | 1,000 MB/秒 | 620 GB | 400 MBps | 3,500 | 256 GB | 250 MBps  | 1,800 |
| M64s | 1,000 GiB | 1,000 MB/秒 |  1,200 GB | 600 MBps | 5,000 | 512 GB | 250 MBps  | 2,500 |
| M64ms | 1,750 GiB | 1,000 MB/秒 | 2,100 GB | 600 MBps | 5,000 | 512 GB | 250 MBps  | 2,500 |
| M128s | 2,000 GiB | 2,000 MB/秒 |2,400 GB | 750 MBps | 7,000 | 512 GB | 250 MBps  | 2,500 | 
| M128ms | 3,800 GiB | 2,000 MB/秒 | 4,800 GB | 750 MBps |7,000 | 512 GB | 250 MBps  | 2,500 | 
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 3,500 GB | 750 MBps | 7,000 | 512 GB | 250 MBps  | 2,500 | 
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 7,200 GB | 750 MBps | 7,000 | 512 GB | 250 MBps  | 2,500 | 
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 7,200 GB | 1,000 MBps | 9,000 | 512 GB | 400 MBps  | 4,000 | 
| M416ms_v2 | 11,400 GiB | 2,000 MB/秒 | 14,400 GB | 1,500 MBps | 9,000 | 512 GB | 400 MBps  | 4,000 |   

**列出的值旨在作为起点，并需要根据实际需求进行评估。** Azure 超级磁盘的优点是可以调整 IOPS 和吞吐量的值，而无需关闭 VM 或停止应用于系统的工作负载。   

> [!NOTE]
> 到目前为止，超级磁盘存储的存储快照不可用。 这会阻止 Azure 备份服务对 VM 快照的使用


## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Azure NetApp 文件上的 NFS v4.1 卷
Azure NetApp 文件提供可用于 **/hana/shared**、 **/hana/data**和 **/hana/log**卷的本机 NFS 共享。 对 **/hana/data**和 **/hana/log**卷使用基于和的 NFS 共享需要使用版本 4.1 nfs 协议。 如果在和上基于共享，则不支持使用 NFS 协议 v3 作为 **/hana/data**和 **/hana/log**卷。 

> [!IMPORTANT]
> **不**支持在 Azure NetApp 文件上实现的 NFS v3 协议适用于 **/hana/data**和 **/hana/log**。 从功能角度来看，NFS 4.1 对于 **/hana/data**和 **/hana/log**卷是必需的。 但对于 **/hana/shared**卷，nfs V3 或 nfs v2.0 协议可从功能角度来使用。

### <a name="important-considerations"></a>重要注意事项
在考虑将 Azure NetApp 文件用于 SAP Netweaver 和 SAP HANA 时，请注意以下重要注意事项：

- 最小容量池是 4 TiB。  
- 最小卷大小为 100 GiB
- Azure NetApp 文件和所有虚拟机（将装载 Azure NetApp 文件卷）必须位于同一 Azure 虚拟网络中或同一区域中的[对等虚拟网络](../../../virtual-network/virtual-network-peering-overview.md)中。  
- 所选的虚拟网络必须具有一个委托给 Azure NetApp 文件的子网。
- Azure NetApp 卷的吞吐量是卷配额和服务级别的函数，如 [Azure NetApp 文件服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。 调整 HANA Azure NetApp 卷的大小时，请确保生成的吞吐量满足 HANA 系统要求。  
- Azure NetApp 文件提供[导出策略](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)：你可以对允许的客户端、访问类型（读写、只读等）进行控制。 
- Azure NetApp 文件功能尚没有区域感知性。 当前，Azure NetApp 文件功能未部署在 Azure 区域中的所有可用性区域中。 请注意某些 Azure 区域的潜在延迟影响。  
- 将虚拟机部署在靠近 Azure NetApp 存储的位置以降低延迟，这一点非常重要。 
- 虚拟机上 sidadm 的用户 ID 和 `sapsys` 的组 ID 必须与 Azure NetApp 文件中的配置相匹配<b></b>。 

> [!IMPORTANT]
> 对于 SAP HANA 工作负载，低延迟至关重要。 与 Microsoft 代表合作，确保虚拟机和 Azure NetApp 文件卷在邻近的地方部署。  

> [!IMPORTANT]
> 如果在虚拟机和 Azure NetApp 配置之间，sidadm 的用户 ID 和 `sapsys` 组 ID 不匹配，虚拟机上装载的 Azure NetApp 卷上的文件权限将显示为 `nobody`<b></b>。 在[载入新系统](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)到 Azure NetApp 文件时，请确保指定的 sidadm 的用户 ID 和 `sapsys` 的组 ID<b></b> 准确无误。

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>调整 Azure NetApp 文件上的 HANA 数据库的大小

Azure NetApp 卷的吞吐量是卷大小和服务级别的函数，如 [Azure NetApp 文件服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。 

在 Azure 中为 SAP 设计基础结构时，应了解 SAP 的一些最低存储吞吐量要求，这可转化为以下最低吞吐量特征：

- 在 I/O 大小为 1 MB、速率为 250 MB/秒的 /hana/log 上启用读/写  
- 为 16 MB 和 64 MB I/O 大小的 /hana/data 启用至少 400 MB/秒的读取活动  
- 为 16 MB 和 64 MB I/O 大小的 /hana/data 启用至少 250MB/秒的写入活动  

每 1 TiB 的卷配额的 [Azure NetApp 文件吞吐量限制](../../../azure-netapp-files/azure-netapp-files-service-levels.md)为：
- 高级存储层-64 MiB/秒  
- 超级存储层 - 128 MiB/秒  

> [!IMPORTANT]
> 无论在单个 NFS 卷上部署的容量如何，预计吞吐量都将 1.2-1.4 GB/秒的带宽范围（由虚拟机中的使用者利用）内稳定下来。 这与 ANF 产品/服务的基础体系结构以及围绕 NFS 的相关 Linux 会话限制有关。 [Azure NetApp 文件的性能基准测试结果](../../../azure-netapp-files/performance-benchmarks-linux.md)一文中记录的性能和吞吐量数针对具有多个客户端 VM 的一个共享 NFS 卷而执行，因此具有多个会话。 这种情况不同于我们在 SAP 中测量的情况。 我们针对 NFS 卷衡量单个 VM 的吞吐量的位置。 托管在 ANF 上。

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

有关如何使用 ANF 中托管的 NFS v4.1 卷部署带备用节点的 SAP HANA 横向扩展配置的文档，已在以下位置发布：[使用 SUSE Linux Enterprise Server 上的 Azure NetApp 文件在 Azure VM 上进行带备用节点的 SAP HANA 横向扩展](./sap-hana-scale-out-standby-netapp-files-suse.md)。


## <a name="cost-conscious-solution-with-azure-premium-storage"></a>Azure 高级存储的成本意识解决方案
到目前为止，本文档中的 Azure 高级存储解决方案在[Azure M 系列虚拟机的高级存储和 azure 写入加速器的解决方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage#solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines)中进行了介绍，用于 SAP HANA 生产支持的方案。 生产可支持性配置的特征之一是将 SAP HANA 数据和重做日志的卷分离成两个不同的卷。 此类隔离的原因是卷上的工作负荷特征不同。 而且，对于推荐的生产配置，可能需要不同类型的缓存或甚至不同类型的 Azure 块存储。 使用 Azure 块存储目标的生产支持的配置也符合[Azure 虚拟机的单一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 。  对于非生产方案，为生产系统所做的一些注意事项可能不适用于更低端的非生产系统。 因此，可以合并 HANA 数据和日志卷。 尽管最终有一些原因，但最终不满足生产系统所需的某些吞吐量或延迟 Kpi。 降低此类环境成本的另一个方面就是使用[Azure 标准 SSD 存储](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide-storage#azure-standard-ssd-storage)。 尽管选择会使[Azure 虚拟机的单个 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)失效。 

对于此类配置，更便宜的替代方法如下所示：


| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data and /hana/log<br /> 使用 LVM 或 MDADM 进行条带化 | /hana/shared | /root 卷 | /usr/sap | comments |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/秒 | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | 不会达到1ms 存储延迟<sup>1</sup> |
| E16v3 | 128 GiB | 384 MB/秒 | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | VM 类型未通过 HANA 认证 <br /> 不会达到1ms 存储延迟<sup>1</sup> |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 5000<sup>2</sup> |
| E20ds_v4 | 160 GiB | 480 MB/秒 | 4 x P6 | 1 x E15 | 1 x E6 | 1 x E6 | 不会达到1ms 存储延迟<sup>1</sup> |
| E32v3 | 256 GiB | 768 MB/秒 | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | VM 类型未通过 HANA 认证 <br /> 不会达到1ms 存储延迟<sup>1</sup> |
| E32ds_v4 | 256 GiB | 768 MBps | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 不会达到1ms 存储延迟<sup>1</sup> |
| M32ls | 256 GiB | 500 MB/秒 | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 5000<sup>2</sup> |
| E48ds_v4 | 384 GiB | 1152 MBps | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 不会达到1ms 存储延迟<sup>1</sup> |
| E64v3 | 432 GiB | 1,200 MB/秒 | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 不会达到1ms 存储延迟<sup>1</sup> |
| E64ds_v4 | 504 GiB | 1200 MB/秒 |  7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 不会达到1ms 存储延迟<sup>1</sup> |
| M64ls | 512 GiB | 1,000 MB/秒 | 7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 10000<sup>2</sup> |
| M64s | 1,000 GiB | 1,000 MB/秒 | 7 x P15 | 1 x E30 | 1 x E6 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 10000<sup>2</sup> |
| M64ms | 1,750 GiB | 1,000 MB/秒 | 6 x P20 | 1 x E30 | 1 x E6 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 10000<sup>2</sup> |
| M128s | 2,000 GiB | 2,000 MB/秒 |6 x P20 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 20000<sup>2</sup> |
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 10000<sup>2</sup> |
| M128ms | 3,800 GiB | 2,000 MB/秒 | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 20000<sup>2</sup> |
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 10000<sup>2</sup> |
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 20000<sup>2</sup> |
| M416ms_v2 | 11400 GiB | 2,000 MB/秒 | 7 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 20000<sup>2</sup> |


<sup>1</sup> [Azure 写入加速器](../../linux/how-to-enable-write-accelerator.md)不能与 Ev4 和 Ev4 VM 系列一起使用。 由于使用 Azure 高级存储，i/o 延迟不会小于1ms

<sup>2</sup> VM 家族支持[Azure 写入加速器](../../linux/how-to-enable-write-accelerator.md)，但写入加速器的 IOPS 限制可能会限制磁盘配置 iops 功能

在将数据和日志卷与 SAP HANA 进行组合的情况下，生成条带化卷的磁盘不应启用读缓存或读/写缓存。

列出了未通过 SAP 认证的 VM 类型，因此未在所谓的 " [SAP HANA 硬件目录](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)" 中列出。 客户的反馈是，某些非生产任务已成功使用了这些非列出的 VM 类型。


## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅：

- [Azure 虚拟机的 SAP HANA 高可用性指南](./sap-hana-availability-overview.md)。
