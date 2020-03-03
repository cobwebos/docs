---
title: Azure Linux VM 大小 - 前几代 | Microsoft Docs
description: 列出 Azure 中适用于 Linux 虚拟机的前几代大小。 针对此系列中的大小列出了 vCPU、数据磁盘和 NIC 的数量，以及存储吞吐量和网络带宽。
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: jonbeck
ms.openlocfilehash: e0abae8ada603e4e6b98ab0dfe9d794186e00000
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226776"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>前几代虚拟机大小

本部分提供了以前代的虚拟机大小的信息。 这些大小仍可使用，但有新的大小可供使用。

## <a name="f-series"></a>F 系列

F 系列基于 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 处理器，该处理器使用 Intel Turbo Boost 技术 2.0，可实现高达 3.1 GHz 的时钟速度。 此 CPU 性能与 Dv2 系列的 VM 相同。  

对于需要更快的 CPU，但是每个 vCPU 不需要太多内存或临时存储的工作负荷来说，F 系列 VM 是一个极佳选择。  诸如分析、游戏服务器、Web 服务器和批处理等工作负荷从 F 系列的优势中获益。

ACU：210 - 250

高级存储：不支持

高级存储缓存：不支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量： IOPS/读取 MBps/写入 MBps | 最大数据磁盘/吞吐量： IOPS | 最大 Nic 数/预期网络带宽（Mbps） |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2  | 16  | 3000/46/23    | 4/4x500   | 2/750   |
| Standard_F2  | 2  | 4  | 32  | 6000/93/46    | 8/8x500   | 2/1500  |
| Standard_F4  | 4  | 8  | 64  | 12000/187/93  | 16/16x500 | 4/3000  |
| Standard_F8  | 8  | 16 | 128 | 24000/375/187 | 32/32x500 | 8/6000  |
| Standard_F16 | 16 | 32 | 256 | 48000/750/375 | 64/64x500 | 8/12000 |

## <a name="fs-series-sup1sup"></a>Fs 系列 <sup>1</sup>

Fs 系列具有 F 系列的所有优势（在高级存储的基础上）。

ACU：210 - 250

高级存储：支持

高级存储缓存：支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 缓存和临时存储的最大吞吐量： IOPS/MBps （GiB 中的缓存大小） | 最大非缓存磁盘吞吐量： IOPS/MBps | 最大 Nic 数/预期网络带宽（Mbps） |
|---|---|---|---|---|---|---|---|
| Standard_F1s  | 1  | 2  | 4  | 4  | 4000/32 （12）    | 3200/48   | 2/750   |
| Standard_F2s  | 2  | 4  | 8  | 8  | 8000/64 （24）    | 6400/96   | 2/1500  |
| Standard_F4s  | 4  | 8  | 16 | 16 | 16000/128 （48）  | 12800/192 | 4/3000  |
| Standard_F8s  | 8  | 16 | 32 | 32 | 32000/256 （96）  | 25600/384 | 8/6000  |
| Standard_F16s | 16 | 32 | 64 | 64 | 64000/512 （192） | 51200/768 | 8/12000 |

MBps = 每秒 10^6 字节，GiB = 1024^3 字节。

<sup>1</sup> Fs 系列 VM 可能的最大磁盘吞吐量（IOPS 或 MBps）可能受限于附加磁盘的数量、大小和条带化。  有关详细信息，请参阅为[Windows](windows/premium-storage-performance.md)或[Linux](linux/premium-storage-performance.md)设计高性能。  

## <a name="nvv2-series"></a>NVv2 系列

**较新的大小建议**： [NVv3 系列](nvv3-series.md)

NVv2 系列虚拟机采用 Intel Broadwell CPU，由 [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 和 NVIDIA GRID 技术提供支持。 此类虚拟机面向 GPU 加速图形应用程序和虚拟桌面，客户希望利用这些应用和桌面直观呈现数据、模拟要查看的结果、处理 CAD 或渲染和流式处理内容。 此外，这些虚拟机还能运行编码和渲染等单精度工作负荷。 NVv2 虚拟机支持高级存储，且与之前的 NV 系列相比，系统内存 (RAM) 翻了一番。  

NVv2 实例中的每个 GPU 都带有 GRID 许可证。 使用此许可证，可以灵活地将 NV 实例用作单个用户的虚拟工作站，或将 25 个并发用户都连接到用于虚拟应用程序方案的 VM。

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大 NIC 数 | 虚拟工作站 | 虚拟应用程序 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1280 | 4 | 32 | 32 | 8 | 4 | 100 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="older-generations-of-virtual-machine-sizes"></a>较旧的虚拟机大小代

本部分提供有关较旧代虚拟机大小的信息。 仍支持这些大小，但不会获得额外的容量。 有一些较新的或备用的大小已正式发布。 请参阅[Azure 中 Linux 虚拟机的大小](linux/sizes.md)，以选择最适合你需求的 VM 大小。  

有关调整 Windows VM 大小的详细信息，请参阅[调整 LINUX vm 的大小](linux/change-vm-size.md)。  

<br>

### <a name="basic-a"></a>基本 A  

**较新的大小建议**： [Av2 系列](av2-series.md)

高级存储：不支持

高级存储缓存：不支持

基本层大小主要用于开发工作负荷，以及其他不需要负载均衡、自动缩放或内存密集型虚拟机的应用程序。

| 大小 – 大小\名称 | vCPU | 内存|NIC 数（最大值）| 最大临时磁盘大小 | 最大 数据磁盘（每个 1023 GB）| 最大 IOPS（每个磁盘 300 次） |
|---|---|---|---|---|---|---|
| A0\Basic_A0 | 1 | 768 MB  | 2 | 20 GB  | 1  | 1x300  |
| A1\Basic_A1 | 1 | 1.75 GB | 2 | 40 GB  | 2  | 2x300  |
| A2\Basic_A2 | 2 | 3.5 GB  | 2 | 60 GB  | 4  | 4x300  |
| A3\Basic_A3 | 4 | 7 GB    | 2 | 120 GB | 8  | 8x300  |
| A4\Basic_A4 | 8 | 14 GB   | 2 | 240 GB | 16 | 16x300 |

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>使用 CLI 和 PowerShell 的标准 A0 - A4

在经典部署模型中，CLI 和 PowerShell 中的一些 VM 大小名称略有不同：

* Standard_A0 是特小型
* Standard_A1 是小型
* Standard_A2 是中型
* Standard_A3 是大型
* Standard_A4 是超大型

### <a name="a-series"></a>A 系列  

**较新的大小建议**： [Av2 系列](av2-series.md)

ACU：50-100

高级存储：不支持

高级存储缓存：不支持

| 大小 | vCPU | 内存：GiB | 临时存储 (HDD)：GiB | 最大数据磁盘数 | 数据磁盘最大吞吐量：IOPS | 最大 Nic 数/预期网络带宽（Mbps） |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> | 1 | 0.768 | 20 | 1 | 1x500 | 2/100 |
| Standard_A1 | 1 | 1.75 | 70  | 2  | 2x500  | 2/500  |
| Standard_A2 | 2 | 3.5  | 135 | 4  | 4x500  | 2/500  |
| Standard_A3 | 4 | 7    | 285 | 8  | 8x500  | 2/1000 |
| Standard_A4 | 8 | 14   | 605 | 16 | 16x500 | 4/2000 |
| Standard_A5 | 2 | 14   | 135 | 4  | 4x500  | 2/500  |
| Standard_A6 | 4 | 28   | 285 | 8  | 8x500  | 2/1000 |
| Standard_A7 | 8 | 56   | 605 | 16 | 16x500 | 4/2000 |

<sup>1</sup> A0 大小在物理硬件上过度订阅。 仅针对此特定大小，其他客户部署可能影响正在运行的工作负荷的性能。 以下概述的相对性能为预期的基准，受限于近似变化性的 15%。

<br>

### <a name="a-series---compute-intensive-instances"></a>A 系列 - 计算密集型实例  

**较新的大小建议**： [Av2 系列](av2-series.md)

ACU：225

高级存储：不支持

高级存储缓存：不支持

A8-A11 和 H 系列大小也称为“计算密集型实例”。 运行这些大小的硬件专为计算密集型和网络密集型应用程序而设计和优化，包括高性能计算 (HPC) 群集应用程序、建模和模拟。 A8-A11 系列使用 Intel Xeon E5-2670 @ 2.6 GHZ，H 系列使用 Intel Xeon E5-2667 v3 @ 3.2 GHz。  

| 大小 | vCPU | 内存：GiB | 临时存储 (HDD)：GiB | 最大数据磁盘数 | 数据磁盘最大吞吐量：IOPS | 最大 NIC 数|
|---|---|---|---|---|---|---|
| Standard_A8&nbsp;<sup>1</sup> | 8  | 56  | 382 | 32 | 32x500 | 2 |
| Standard_A9&nbsp;<sup>1</sup> | 16 | 112 | 382 | 64 | 64x500 | 4 |
| Standard_A10 | 8  | 56  | 382 | 32 | 32x500 | 2 |
| Standard_A11 | 16 | 112 | 382 | 64 | 64x500 | 4 |

<sup>1</sup>对于 MPI 应用程序来说，专用 RDMA 后端网络是通过 FDR InfiniBand 网络启用的，后者可以提供相当低的延迟和高带宽。  

> [!NOTE]
> A8 – A11 Vm 计划于3/2021 停用。 有关详细信息，请参阅[HPC 迁移指南](https://azure.microsoft.com/resources/hpc-migration-guide/)。

<br>

### <a name="d-series"></a>D 系列  

**较新的大小建议**： [Dv3 系列](dv3-dsv3-series.md)

ACU： 160-250 <sup>1</sup>

高级存储：不支持

高级存储缓存：不支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量： IOPS/读取 MBps/写入 MBps | 最大数据磁盘/吞吐量： IOPS | 最大 Nic 数/预期网络带宽（Mbps） |
|---|---|---|---|---|---|---|
| Standard_D1  | 1 | 3.5 | 50  | 3000/46/23    | 4/4x500   | 2/500  |
| Standard_D2  | 2 | 7   | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D3  | 4 | 14  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D4  | 8 | 28  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |

<sup>1</sup> VM 系列可以运行于以下 CPU 之一： 2.2 GHz intel 强® E5-2660 v2、2.4 GHz intel 强® e5-2673 V3 （Haswell）或 2.3 GHZ Intel 强® E5-2673 V4 （Broadwell）  

<br>

### <a name="d-series---memory-optimized"></a>D 系列 - 内存优化  

**较新的大小建议**： [Dv3 系列](dv3-dsv3-series.md)

ACU： 160-250 <sup>1</sup>

高级存储：不支持

高级存储缓存：不支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量： IOPS/读取 MBps/写入 MBps | 最大数据磁盘/吞吐量： IOPS | 最大 Nic 数/预期网络带宽（Mbps） |
|---|---|---|---|---|---|---|
| Standard_D11 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D12 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D13 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |
| Standard_D14 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/8000 |

<sup>1</sup> VM 系列可以运行于以下 CPU 之一： 2.2 GHz intel 强® E5-2660 v2、2.4 GHz intel 强® e5-2673 V3 （Haswell）或 2.3 GHZ Intel 强® E5-2673 V4 （Broadwell）  

<br>

## <a name="preview-dc-series"></a>预览： DC 系列

高级存储：支持

高级存储缓存：支持

DC 系列使用最新版本的 3.7 GHz Intel 至强 E-2176G 处理器和 SGX 技术，使用 Intel Turbo 提升技术可以达到 4.7 GHz。 

| 大小          | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 缓存和临时存储的最大吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大 NIC 数/预期网络带宽 (MBps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |

> [!IMPORTANT]
>
> DC 系列 Vm 是[第2代 vm](./linux/generation-2.md#creating-a-generation-2-vm) ，仅支持 `Gen2` 映像。


### <a name="ds-series"></a>DS 系列  

**较新的大小建议**： [Dsv3 系列](dv3-dsv3-series.md)

ACU： 160-250 <sup>1</sup>

高级存储：支持

高级存储缓存：支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 缓存和临时存储的最大吞吐量： IOPS/MBps （GiB 中的缓存大小） | 最大非缓存磁盘吞吐量： IOPS/MBps | 最大 Nic 数/预期网络带宽（Mbps） |
|---|---|---|---|---|---|---|---|
| Standard_DS1 | 1 | 3.5 | 7  | 4  | 4000/32 （43）    | 3200/32   | 2/500  |
| Standard_DS2 | 2 | 7   | 14 | 8  | 8000/64 （86）    | 6400/64   | 2/1000 |
| Standard_DS3 | 4 | 14  | 28 | 16 | 16000/128 （172） | 12800/128 | 4/2000 |
| Standard_DS4 | 8 | 28  | 56 | 32 | 32000/256 （344） | 25600/256 | 8/4000 |

<sup>1</sup> VM 系列可以运行于以下 CPU 之一： 2.2 GHz intel 强® E5-2660 v2、2.4 GHz intel 强® e5-2673 V3 （Haswell）或 2.3 GHZ Intel 强® E5-2673 V4 （Broadwell）  

<br>

### <a name="ds-series---memory-optimized"></a>DS 系列 - 内存优化  

**较新的大小建议**： [Dsv3 系列](dv3-dsv3-series.md)

ACU： 160-250 <sup>1，2</sup>

高级存储：支持

高级存储缓存：支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 缓存和临时存储的最大吞吐量： IOPS/MBps （GiB 中的缓存大小） | 最大非缓存磁盘吞吐量： IOPS/MBps | 最大 Nic 数/预期网络带宽（Mbps） |
|---|---|---|---|---|---|---|---|
| Standard_DS11 | 2  | 14  | 28  | 8  | 8000/64 （72）    | 6400/64   | 2/1000 |
| Standard_DS12 | 4  | 28  | 56  | 16 | 16000/128 （144） | 12800/128 | 4/2000 |
| Standard_DS13 | 8  | 56  | 112 | 32 | 32000/256 （288） | 25600/256 | 8/4000 |
| Standard_DS14 | 16 | 112 | 224 | 64 | 64000/512 （576） | 51200/512 | 8/8000 |

<sup>1</sup> DS 系列 VM 可能的最大磁盘吞吐量（IOPS 或 MBps）可能受限于附加磁盘的数量、大小和条带化。  有关详细信息，请参阅为[Windows](windows/premium-storage-performance.md)或[Linux](linux/premium-storage-performance.md)设计高性能。
<sup>2</sup> VM 系列可以运行于以下 CPU 之一： 2.2 GHz intel 强® E5-2660 v2、2.4 GHz intel 强® e5-2673 V3 （Haswell）或 2.3 GHZ Intel 强® E5-2673 V4 （Broadwell）  

<br>

### <a name="ls-series"></a>Ls 系列

Ls-系列提供多达 32 个 vCPU，使用的是 [Intel® Xeon® 处理器 E5 v3 系列](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html)。 Ls 系列与 G/GS-系列拥有相同的 CPU 性能，并且每个 vCPU 附带 8 GiB 内存。

Ls 系列不支持创建本地缓存以增加持久数据磁盘可实现的 IOPS。 本地磁盘的高吞吐量和 IOPS 使 Ls 系列 Vm 非常适合于 NoSQL 存储，例如 Apache Cassandra 和 MongoDB，可跨多个 Vm 复制数据，从而在单个 VM 发生故障时保持持久性。

ACU：180-240

高级存储：支持

高级存储缓存：不支持

| 大小 | vCPU | 内存 (GiB) | 临时存储 (GiB) | 最大数据磁盘数 | 最大临时存储吞吐量（IOPS/MBps） | 最大未缓存磁盘吞吐量（IOPS/MBps） | 最大 Nic 数/预期网络带宽（Mbps） |
|---|---|---|---|---|---|---|---|
| Standard_L4s   | 4  | 32  | 678  | 16 | 20000/200 | 5000/125  | 2/4000  |
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  |
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 |
| Standard_L32s&nbsp;<sup>1</sup> | 32 | 256 | 5630 | 64 | 160000/1600 | 40000/1000 | 8/20000 |

Ls 系列 VM 可能的最大磁盘吞吐量可能受限于任何附加磁盘的数量、大小和条带化。 有关详细信息，请参阅为[Windows](windows/premium-storage-performance.md)或[Linux](linux/premium-storage-performance.md)设计高性能。

<sup>1</sup> 实例对于专用于单个客户的硬件独立。

### <a name="gs-series"></a>GS 系列

ACU：180 - 240 <sup>1</sup>

高级存储：支持

高级存储缓存：支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 缓存和临时存储的最大吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量： IOPS/MBps | 最大 Nic 数/预期网络带宽（Mbps） |
|---|---|---|---|---|---|---|---|
| Standard_GS1 | 2 | 28  | 56  | 8  | 10000/100 （264）  | 5000/ 125  | 2/2000 |
| Standard_GS2 | 4 | 56  | 112 | 16 | 20000/200 （528）  | 10000/ 250 | 2/4000 |
| Standard_GS3 | 8 | 112 | 224 | 32 | 40000/400 （1056） | 20000/ 500 | 4/8000 |
| Standard_GS4&nbsp;<sup>3</sup> | 16 | 224 | 448 | 64 | 80000/800 （2112） | 40000/1000 | 8/16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> | 32 | 448 |896 | 64 |160000/1600 （4224） | 80000/2000 | 8/20000 |

<sup>1</sup> GS 系列 VM 可能的最大磁盘吞吐量（IOPS 或 MBps）可能受限于附加磁盘的数量、大小和条带化。 有关详细信息，请参阅为[Windows](windows/premium-storage-performance.md)或[Linux](linux/premium-storage-performance.md)设计高性能。

<sup>2</sup> 实例对于专用于单个客户的硬件独立。

<sup>3</sup> 受约束的可用核心大小。

<br>

### <a name="g-series"></a>G 系列

ACU：180 - 240

高级存储：不支持

高级存储缓存：不支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量： IOPS/读取 MBps/写入 MBps | 最大数据磁盘/吞吐量： IOPS | 最大 Nic 数/预期网络带宽（Mbps） |
|---|---|---|---|---|---|---|
| Standard_G1  | 2  | 28  | 384  | 6000/93/46    | 8/8x500   | 2/2000  |
| Standard_G2  | 4  | 56  | 768  | 12000/187/93  | 16/16x500 | 2/4000  |
| Standard_G3  | 8  | 112 | 1536 | 24000/375/187 | 32/32x500 | 4/8000  |
| Standard_G4  | 16 | 224 | 3072 | 48000/750/375 | 64/64x500 | 8/16000 |
| Standard_G5&nbsp;<sup>1</sup> | 32 | 448 | 6144 | 96000/1500/750| 64/64x500 | 8/20000 |

<sup>1</sup> 实例对于专用于单个客户的硬件独立。
<br>

## <a name="other-sizes"></a>其他大小

* [常规用途](sizes-general.md)
* [计算优化](sizes-compute.md)
* [内存优化](sizes-memory.md)
* [存储优化](sizes-storage.md)
* [GPU](sizes-gpu.md)
* [高性能计算](sizes-hpc.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。
