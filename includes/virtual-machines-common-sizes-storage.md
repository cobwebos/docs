---
title: include 文件
description: include 文件
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: b4de9efbe85d5ab497bccd1742df23ddc1b3af43
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354653"
---
存储优化 VM 大小可提供较高的磁盘吞吐量和 IO，非常适合用于大数据、SQL 和 NoSQL 数据库、数据仓库和大型事务数据库。  示例包括 Cassandra、MongoDB、Cloudera 和 Redis。 本文介绍关于 vCPU、数据磁盘和 NIC 的数量以及每个优化大小的本地存储吞吐量和网络带宽的信息。

Lsv2 系列具有高吞吐量、低延迟、在 [AMD EPYC &trade; 7551 处理器](https://www.amd.com/en/products/epyc-7000-series)上运行的直接映射的本地 NVMe 存储，且所有核心提升 2.55GHz，最大提升 3.0GHz。 Lsv2 系列 VM 提供同时进行的多线程配置中的 8 到 80 个 vCPU。  每个 vCPU 有 8 GiB 内存，每 8 个 vCPU 有一个 1.92TB 的 NVMe SSD M.2 设备，并且在 L80s v2 上可以使用最多 19.2TB (10x1.92TB)。

Ls-系列提供多达 32 个 vCPU，使用的是 [Intel® Xeon® 处理器 E5 v3 系列](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html)。 Ls 系列与 G/GS-系列拥有相同的 CPU 性能，并且每个 vCPU 附带 8 GiB 内存。

> [!NOTE]
> 优化 Lsv2 系列 VM 以使用直接附加到 VM 的节点上的本地磁盘，而不是使用持久性数据磁盘。  这可实现工作负载的更大的 IOPS/吞吐量。  Lsv2 和 Ls 系列不支持创建本地缓存以增加持久数据磁盘可实现的 IOPS。 本地磁盘的高吞吐量和 IOPS 使得 Lsv2 和 Ls 系列 VM 非常适用于 NoSQL 存储（例如 Apache Cassandra 和 MongoDB），这些存储跨多个 VM 复制数据，以便在单个 VM 发生故障时实现持久性。 

## <a name="lsv2-series"></a>Lsv2 系列
ACU：150-175

高级存储：支持

高级存储缓存：不支持

| 大小          | vCPU | 内存 (GiB) | 临时磁盘<sup>1</sup> (GiB) | NVMe 磁盘 | NVMe 磁盘吞吐量<sup>2</sup>（读取 IOPS / MBps） | 主机缓存大小<sup>3</sup> | 最大数据磁盘 | 最大 NIC 数/预期网络带宽 (MBps) | 
|---------------|-----------|-------------|--------------------------|----------------|---------------------------------------------------|-------------------------------------------|------------------------------|------------------------------| 
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1.92 TB  | 340,000 / 2,000 | 不适用 | 16 | 2 / 3,200  | 
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1.92 TB  | 680,000 / 4,500 | 不适用 | 32 | 4 / 6,400  | 
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1.92 TB  | 1.4M / 9,000    | 不适用 | 32 | 8 / 12,800 | 
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1.92 TB  | 2.7M / 18,000   | 不适用 | 32 | 8 / 25,600 |
| Standard_L80s_v2  | 80 | 640 | 800 | 10x1.92TB   | 3.4M / 22,000   | 不适用 | 32 | 8 / 32,000 |
 
<sup>1</sup> Lsv2 系列 VM 具有标准的基于 SCSI 的临时资源磁盘，用于 OS 分页/交换文件的使用（对于 Windows，为 D:；对于 Linux，为 /dev/sdb）。 此磁盘为每 8 个 vCPU 提供 80 GiB 的存储空间、4000 IOPS 和 80 MBps 的传输速率（例如，Standard_L80s_v2 在 40000 IOPS 和 800 MBps 下提供 800 GiB）。 这确保 NVMe 驱动器可以完全专用于应用程序的使用。

<sup>2</sup> HYPER-V NVMe Direct 技术提供对安全映射到来宾 VM 空间的 NVMe 驱动器的无限制访问。  实现最大性能需要使用最新的 WS2019 版本或者 Azure 市场上的 Ubuntu 18.04 或 16.04。  写入性能因 IO 大小、驱动器负载和容量利用率而异。

<sup>3</sup> Lsv2 系列 VM 不为数据磁盘提供主机缓存，因为这不利于 Lsv2 工作负载。  然而，Lsv2 VM 可以调整 Azure 的临时 VM OS 磁盘选项（最多 30 GiB）。 



## <a name="ls-series"></a>Ls 系列
ACU：180-240

高级存储：支持

高级存储缓存：不支持
 
| 大小          | vCPU | 内存 (GiB) | 临时存储 (GiB) | 最大数据磁盘数 | 临时存储的最大吞吐量 (IOPS / MBps) | 非缓存磁盘的最大吞吐量 (IOPS / MBps) | 最大 NIC 数/预期网络带宽 (MBps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20,000 / 200 | 5,000 / 125  | 2 / 4,000  | 
| Standard_L8s   | 8  | 64  | 1,388 | 32 | 40,000 / 400 | 10,000 / 250 | 4 / 8,000  | 
| Standard_L16s  | 16 | 128 | 2,807 | 64 | 80,000 / 800 | 20,000 / 500 | 8 / 16,000 | 
| Standard_L32s <sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40,000 / 1,000     | 8 / 20,000 | 
 

Ls 系列 VM 可能的最大磁盘吞吐量可能受限于任何附加磁盘的数量、大小和条带化。 有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../articles/virtual-machines/windows/premium-storage.md)。

<sup>1</sup> 实例对于专用于单个客户的硬件独立。

## <a name="size-table-definitions"></a>大小表定义

- 存储容量的单位为 GiB 或 1024^3 字节。 比较以 GB（1000^3 字节）为单位的磁盘和以 GiB（1024^3 字节）为单位的磁盘时，请记住以 GiB 为单位的容量数显得更小。 例如，1023 GiB = 1098.4 GB
- 磁盘吞吐量的单位为每秒输入/输出操作数 (IOPS) 和 MBps，其中 MBps = 10^6 字节/秒。
- 若要获得 VM 的最佳性能，应将数据磁盘数限制为每 vCPU 2 个磁盘。
- **预期的网络带宽**是指跨所有 NIC [为每个 VM 类型分配的最大聚合带宽](../articles/virtual-network/virtual-machine-network-throughput.md)，适用于所有目标。 不保证能够达到上述预期上限，但目的是提供相关指导，以便为目标应用程序选择适当的 VM 类型。 实际的网络性能将取决于许多因素，包括网络拥塞、应用程序负载和网络设置。 有关如何优化网络吞吐量的信息，请参阅[为 Windows 和 Linux 优化网络吞吐量](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)。 若要在 Linux 或 Windows 中达到预期的网络性能，可能需要选择特定的版本，或者需要优化 VM。 有关详细信息，请参阅[如何针对虚拟机吞吐量进行可靠的测试](../articles/virtual-network/virtual-network-bandwidth-testing.md)。
