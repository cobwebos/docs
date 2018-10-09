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
ms.openlocfilehash: 961f82cd4970abfdd11a30b2847a14f8ff1880b0
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454453"
---
存储优化 VM 大小可提供较高的磁盘吞吐量和 IO，非常适合用于大数据、SQL 和 NoSQL 数据库。 本文介绍了此分组中各个大小的 vCPU 数、数据磁盘数、NIC 数、存储吞吐量及网络带宽的相关信息。 

Ls-系列提供多达 32 个 vCPU，使用的是 [Intel® Xeon® 处理器 E5 v3 系列](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html)。 Ls 系列与 G/GS-系列拥有相同的 CPU 性能，并且每个 vCPU 附带 8 GiB 内存。  Ls 系列 VM 十分适合需要低延迟、高吞吐量和大量本地磁盘存储的应用程序。 

示例用例包括 NoSQL 数据库（如 Cassandra、MongoDB、Cloudera 和 Redis）、数据仓库和大型事务数据库。

> [!NOTE]
> Ls 系列已针对附加到 VM 计算机的临时磁盘的使用（而不是持久数据磁盘的使用）进行了优化。 临时磁盘的高吞吐量和 IOPS 使得 Ls 系列非常适用于 NoSQL 存储（例如 Apache Cassandra 和 MongoDB），这些存储跨多个 VM 复制数据，以便在单个 VM 发生故障时实现持久性。 Ls 系列不支持创建本地缓存以增加持久数据磁盘可实现的 IOPS。

## <a name="ls-series"></a>Ls 系列

ACU：180-240

高级存储：支持

高级存储缓存：不支持
 
| 大小          | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 临时存储的最大吞吐量：IOPS/MBps | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大 NIC 数/预期网络带宽 (MBps) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4    | 32   | 678   | 16    | 20,000 / 200   | 5,000 / 125        | 2 / 4,000  | 
| Standard_L8s   | 8    | 64   | 1,388 | 32   | 40,000 / 400   | 10,000 / 250       | 4 / 8,000  | 
| Standard_L16s  | 16   | 128  | 2,807 | 64   | 80,000 / 800   | 20,000 / 500       | 8 / 16,000 | 
| Standard_L32s <sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40,000 / 1,000     | 8 / 20,000 | 
 

Ls 系列 VM 可能的最大磁盘吞吐量可能受限于任何附加磁盘的数量、大小和条带化。 有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../articles/virtual-machines/windows/premium-storage.md)。

<sup>1</sup> 实例对于专用于单个客户的硬件独立。

## <a name="size-table-definitions"></a>大小表定义

- 存储容量的单位为 GiB 或 1024^3 字节。 比较以 GB（1000^3 字节）为单位的磁盘和以 GiB（1024^3 字节）为单位的磁盘时，请记住以 GiB 为单位的容量数显得更小。 例如，1023 GiB = 1098.4 GB
- 磁盘吞吐量的单位为每秒输入/输出操作数 (IOPS) 和 MBps，其中 MBps = 10^6 字节/秒。
- 若要获得 VM 的最佳性能，应将数据磁盘数限制为每 vCPU 2 个磁盘。
- **预期的网络带宽**是指跨所有 NIC [为每个 VM 类型分配的最大聚合带宽](../articles/virtual-network/virtual-machine-network-throughput.md)，适用于所有目标。 不保证能够达到上述预期上限，但目的是提供相关指导，以便为目标应用程序选择适当的 VM 类型。 实际的网络性能将取决于许多因素，包括网络拥塞、应用程序负载和网络设置。 有关如何优化网络吞吐量的信息，请参阅[为 Windows 和 Linux 优化网络吞吐量](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)。 若要在 Linux 或 Windows 中达到预期的网络性能，可能需要选择特定的版本，或者需要优化 VM。 有关详细信息，请参阅[如何针对虚拟机吞吐量进行可靠的测试](../articles/virtual-network/virtual-network-bandwidth-testing.md)。
