---
title: Lsv2 系列 - Azure 虚拟机
description: Lsv2 系列 VM 的规范。
author: sasha-melamed
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: cedb5899b392cb111f9c1bb76949e940ef837252
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284806"
---
# <a name="lsv2-series"></a>Lsv2 系列

Lsv2 系列具有高吞吐量、低延迟、在 [AMD EPYC<sup>TM</sup>7551 处理器](https://www.amd.com/en/products/epyc-7000-series)上运行的直接映射的本地 NVMe 存储，且所有核心提升 2.55GHz，最大提升 3.0GHz。 Lsv2 系列 VM 提供同时进行的多线程配置中的 8 到 80 个 vCPU。  每个 vCPU 有 8 GiB 内存，每 8 个 vCPU 有一个 1.92TB 的 NVMe SSD M.2 设备，并且在 L80s v2 上可以使用最多 19.2TB (10x1.92TB)。

> [!NOTE]
> 优化 Lsv2 系列 VM 以使用直接连接到 VM 的节点上的本地磁盘，而不是使用持久数据磁盘。 这可实现工作负载的更大的 IOPS/吞吐量。 Lsv2 和 Ls 系列不支持通过创建本地缓存来增加持久数据磁盘可实现的 IOPS。
>
> 本地磁盘的高吞吐量和 IOPS 使得 Lsv2 系列 VM 非常适用于 NoSQL 存储（例如 Apache Cassandra 和 MongoDB）。这些存储将数据复制到多个 VM 上，以便在单个 VM 发生故障时实现持久性。
>
> 若要了解详细信息，请参阅：针对 [Windows](../virtual-machines/windows/storage-performance.md) 或 [Linux](../virtual-machines/linux/storage-performance.md) 优化 Lsv2 系列虚拟机上的性能。  

ACU：150-175

突发：支持

高级存储：支持

高级存储缓存：不支持

实时迁移：不支持

内存保留更新：不支持

| 大小 | vCPU | 内存 (GiB) | 临时磁盘<sup>1</sup> (GiB) | NVMe 磁盘<sup>2</sup> | NVMe 磁盘吞吐量<sup>3</sup>（读取 IOPS/MBps） | 非缓存数据磁盘吞吐量 (IOPS/MBps)<sup>4</sup> | 最大突发非缓存数据磁盘吞吐量 (IOPS/MBps)<sup>5</sup>| 最大数据磁盘 | 最大 NIC 数 | 预期网络带宽（Mbps） |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1.92 TB  | 400000/2000  | 8000/160   | 8000/1280 | 16 | 2 | 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1.92 TB  | 800000/4000  | 16000/320  | 16000/1280 | 32 | 4 | 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1.92 TB  | 1.5M/8000    | 32000/640  | 32000/1280 | 32 | 8 | 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x1.92 TB  | 2.2M/14000   | 48000/960  | 48000/2000 | 32 | 8 | 16000 + |
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1.92 TB  | 2.9M/16000   | 64000/1280 | 64000/2000 | 32 | 8 | 16000 + |
| Standard_L80s_v2<sup>6</sup> | 80 | 640 | 800 | 10x1.92TB | 3.8M/20000 | 80000/1400 | 80000/2000 | 32 | 8 | 16000 + |

<sup>1</sup> Lsv2 系列 VM 具有标准的基于 SCSI 的临时资源磁盘，用于 OS 分页/交换文件的使用（对于 Windows，为 D:；对于 Linux，为 /dev/sdb）。 此磁盘为每 8 个 vCPU 提供 80 GiB 的存储空间、4000 IOPS 和 80 MBps 的传输速率（例如，Standard_L80s_v2 在 40000 IOPS 和 800 MBps 下提供 800 GiB）。 这确保 NVMe 驱动器可以完全专用于应用程序的使用。 此磁盘是临时的，停止/解除分配时将丢失所有数据。

<sup>2</sup> 本地 NVMe 磁盘是临时的，如果停止/解除分配 VM，这些磁盘上的数据都将丢失。

<sup>3</sup> Hyper-V NVMe Direct 技术提供对安全映射到来宾 VM 空间的本地 NVMe 驱动器的无限制访问。  实现最大性能需要使用最新的 WS2019 版本或者 Azure 市场上的 Ubuntu 18.04 或 16.04。  写入性能因 IO 大小、驱动器负载和容量利用率而异。

<sup>4</sup> Lsv2 系列 VM 不为数据磁盘提供主机缓存，因为这不会让 Lsv2 工作负荷受益。  然而，Lsv2 VM 可以调整 Azure 的临时 VM OS 磁盘选项（最多 30 GiB）。

<sup>5</sup> Lsv2 系列 VM 可以通过[突发](linux/disk-bursting.md)方式提高其磁盘性能，一次长达 30 分钟。 

<sup>6</sup> 具有 64 个以上 vCPU 的 VM 需要以下受支持的来宾操作系统之一：

- Windows Server 2016 或更高版本
- Ubuntu 16.04 LTS 或更高版本，带 Azure 优化内核（4.15 内核或更高版本）
- SLES 12 SP2 或更高版本
- RHEL 或 CentOS 版本6.7 到6.10，其中安装了 Microsoft 提供的 .LIS 包4.3.1 （或更高版本）
- RHEL 或 CentOS 版本7.3，其中安装了 Microsoft 提供的 .LIS 包4.2.1 （或更高版本）
- RHEL 或 CentOS 版本7.6 或更高版本
- UEK4 或更高版本的 Oracle Linux
- Debian 9，带有向后移植内核 Debian 10 或更高版本
- 带有 4.14 内核或更高版本的 CoreOS

## <a name="size-table-definitions"></a>大小表定义

- 存储容量的单位为 GiB 或 1024^3 字节。 比较以 GB（1000^3 字节）为单位的磁盘和以 GiB（1024^3 字节）为单位的磁盘时，请记住以 GiB 为单位的容量数显得更小。 例如，1023 GiB = 1098.4 GB
- 磁盘吞吐量的单位为每秒输入/输出操作数 (IOPS) 和 Mbps，其中 Mbps = 10^6 字节/秒。
- 若要获得 VM 的最佳性能，应将数据磁盘数限制为每 vCPU 2 个磁盘。
- **预期的网络带宽**是指跨所有 NIC [为每个 VM 类型分配的最大聚合带宽](../virtual-network/virtual-machine-network-throughput.md)，适用于所有目标。 不保证能够达到上述预期上限，但目的是提供相关指导，以便为目标应用程序选择适当的 VM 类型。 实际的网络性能将取决于许多因素，包括网络拥塞、应用程序负载和网络设置。 有关如何优化网络吞吐量的信息，请参阅[为 Windows 和 Linux 优化网络吞吐量](../virtual-network/virtual-network-optimize-network-bandwidth.md)。 若要在 Linux 或 Windows 中达到预期的网络性能，可能需要选择特定版本，或者需要优化 VM。 有关详细信息，请参阅[如何针对虚拟机吞吐量进行可靠的测试](../virtual-network/virtual-network-bandwidth-testing.md)。


## <a name="other-sizes-and-information"></a>其他大小和信息

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

定价计算器：[定价计算器](https://azure.microsoft.com/pricing/calculator/)

有关磁盘类型的详细信息：[磁盘类型](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types#ultra-ssd-preview/)


## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
