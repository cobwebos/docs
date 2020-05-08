---
title: Lsv2 系列-Azure 虚拟机
description: Lsv2 系列 Vm 的规格。
services: virtual-machines
author: sasha-melamed
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: bdb9e346b8deea71ef2af9f9f271ffa446be624e
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594332"
---
# <a name="lsv2-series"></a>Lsv2 系列

Lsv2 系列具有高吞吐量、低延迟、在 [AMD EPYC<sup>TM</sup>7551 处理器](https://www.amd.com/en/products/epyc-7000-series)上运行的直接映射的本地 NVMe 存储，且所有核心提升 2.55GHz，最大提升 3.0GHz。 Lsv2 系列 VM 提供同时进行的多线程配置中的 8 到 80 个 vCPU。  每个 vCPU 有 8 GiB 内存，每 8 个 vCPU 有一个 1.92TB 的 NVMe SSD M.2 设备，并且在 L80s v2 上可以使用最多 19.2TB (10x1.92TB)。

> [!NOTE]
> Lsv2 系列 Vm 经过优化，可使用直接连接到 VM 的节点上的本地磁盘，而不是使用持久性数据磁盘。 这可实现工作负载的更大的 IOPS/吞吐量。 Lsv2 和 Ls 系列不支持创建本地缓存，以增加持久数据磁盘可实现的 IOPs。
>
> 本地磁盘的高吞吐量和 IOPs 使 Lsv2 系列虚拟机非常适合于 NoSQL 存储，例如 Apache Cassandra 和 MongoDB，它们可跨多个 Vm 复制数据，从而在单个 VM 发生故障时保持持久性。
>
> 若要了解详细信息，请参阅 Lsv2 系列虚拟机上适用于[Windows](../virtual-machines/windows/storage-performance.md)或[Linux](../virtual-machines/linux/storage-performance.md)的优化性能。  

ACU：150-175

突发：支持

高级存储：支持

高级存储缓存：不支持

实时迁移：不支持

内存保留更新：不支持

| 大小 | vCPU | 内存 (GiB) | 临时磁盘<sup>1</sup> (GiB) | NVMe 磁盘<sup>2</sup> | NVMe 磁盘吞吐量<sup>3</sup> （读取 IOPS/MBps） | 未缓存的数据磁盘吞吐量（IOPs/MBps）<sup>4</sup> | 最大突发未缓存数据磁盘吞吐量（IOPs/MBps）<sup>5</sup>| 最大数据磁盘 | 最大 NIC 数/预期网络带宽 (MBps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1.92 TB  | 400000/2000  | 8000/160   | 8000/1280 | 16 | 2 / 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1.92 TB  | 800000/4000  | 16000/320  | 16000/1280 | 32 | 4 / 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1.92 TB  | 1.5 m/8000    | 32000/640  | 32000/1280 | 32 | 8 / 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6倍 1.92 TB  | 2.2 m/14000   | 48000/960  | 48000/2000 | 32 | 8/16000 + |
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1.92 TB  | 2.9 m/16000   | 64000/1280 | 64000/2000 | 32 | 8/16000 + |
| Standard_L80s_v2<sup>6</sup> | 80 | 640 | 800 | 10x1.92TB | 3.8 m/20000 | 80000/1400 | 80000/2000 | 32 | 8/16000 + |

<sup>1</sup> Lsv2 系列 VM 具有标准的基于 SCSI 的临时资源磁盘，用于 OS 分页/交换文件的使用（对于 Windows，为 D:；对于 Linux，为 /dev/sdb）。 此磁盘为每 8 个 vCPU 提供 80 GiB 的存储空间、4000 IOPS 和 80 MBps 的传输速率（例如，Standard_L80s_v2 在 40000 IOPS 和 800 MBps 下提供 800 GiB）。 这确保 NVMe 驱动器可以完全专用于应用程序的使用。 此磁盘是临时的，停止/解除分配时将丢失所有数据。

<sup>2</sup> 本地 NVMe 磁盘是临时的，如果停止/解除分配 VM，这些磁盘上的数据都将丢失。

<sup>3</sup> Hyper-V NVMe Direct 技术提供对安全映射到来宾 VM 空间的本地 NVMe 驱动器的无限制访问。  实现最大性能需要使用最新的 WS2019 版本或者 Azure 市场上的 Ubuntu 18.04 或 16.04。  写入性能因 IO 大小、驱动器负载和容量利用率而异。

<sup>4</sup> Lsv2 系列 VM 不为数据磁盘提供主机缓存，因为这不会让 Lsv2 工作负荷受益。  然而，Lsv2 VM 可以调整 Azure 的临时 VM OS 磁盘选项（最多 30 GiB）。

<sup>5</sup> Lsv2 系列 vm 一次最多可将磁盘性能[突发](linux/disk-bursting.md)为30分钟。 

<sup>6</sup>个个 vcpu 超过64的 vm 需要以下受支持的来宾操作系统之一：

- Windows Server 2016 或更高版本
- Ubuntu 16.04 LTS 或更高版本，带 Azure 优化内核（4.15 内核或更高版本）
- SLES 12 SP2 或更高版本
- RHEL 或 CentOS 版本6.7 到6.10，其中安装了 Microsoft 提供的 .LIS 包4.3.1 （或更高版本）
- RHEL 或 CentOS 版本7.3，其中安装了 Microsoft 提供的 .LIS 包4.2.1 （或更高版本）
- RHEL 或 CentOS 版本7.6 或更高版本
- UEK4 或更高版本的 Oracle Linux
- Debian 9 precise-backports 内核，Debian 10 或更高版本
- 使用4.14 内核或更高版本的 CoreOS

## <a name="size-table-definitions"></a>大小表定义

- 存储容量的单位为 GiB 或 1024^3 字节。 比较以 GB（1000^3 字节）为单位的磁盘和以 GiB（1024^3 字节）为单位的磁盘时，请记住以 GiB 为单位的容量数显得更小。 例如，1023 GiB = 1098.4 GB
- 磁盘吞吐量的单位为每秒输入/输出操作数 (IOPS) 和 MBps，其中 MBps = 10^6 字节/秒。
- 若要获得 VM 的最佳性能，应将数据磁盘数限制为每 vCPU 2 个磁盘。
- **预期的网络带宽**是指跨所有 NIC [为每个 VM 类型分配的最大聚合带宽](../virtual-network/virtual-machine-network-throughput.md)，适用于所有目标。 不保证能够达到上述预期上限，但目的是提供相关指导，以便为目标应用程序选择适当的 VM 类型。 实际的网络性能将取决于许多因素，包括网络拥塞、应用程序负载和网络设置。 有关如何优化网络吞吐量的信息，请参阅[为 Windows 和 Linux 优化网络吞吐量](../virtual-network/virtual-network-optimize-network-bandwidth.md)。 若要在 Linux 或 Windows 中达到预期的网络性能，可能需要选择特定的版本，或者需要优化 VM。 有关详细信息，请参阅[如何针对虚拟机吞吐量进行可靠的测试](../virtual-network/virtual-network-bandwidth-testing.md)。

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
