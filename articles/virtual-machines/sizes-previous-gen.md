---
title: Azure VM 大小-上一代 |Microsoft Docs
description: 列出 Azure 中可用的前几代虚拟机大小。 针对此系列中的大小列出了 vCPU、数据磁盘和 NIC 的数量，以及存储吞吐量和网络带宽。
services: virtual-machines
ms.subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/02/2020
ms.author: jushiman
ms.openlocfilehash: 05da5a21fb558b7d936b7569a69578eda06e11cf
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875268"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>前几代虚拟机大小

本部分提供了有关先前几代虚拟机大小的信息。 这些大小仍可使用，但有新的大小可供使用。

## <a name="f-series"></a>F 系列

F 系列基于 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 处理器，该处理器使用 Intel Turbo Boost 技术 2.0，可实现高达 3.1 GHz 的时钟速度。 此 CPU 性能与 Dv2 系列的 VM 相同。  

对于需要更快的 CPU，但是每个 vCPU 不需要太多内存或临时存储的工作负荷来说，F 系列 VM 是一个极佳选择。  诸如分析、游戏服务器、Web 服务器和批处理等工作负荷从 F 系列的优势中获益。

ACU：210 - 250

高级存储：不支持

高级存储缓存：不支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量：IOPS/读取 MBps/写入 MBps | 最大数据磁盘数/吞吐量：IOPS | 最大 NIC 数/预期网络带宽 (Mbps) |
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

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数/预期网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F1s  | 1  | 2  | 4  | 4  | 4000/32 (12)    | 3200/48   | 2/750   |
| Standard_F2s  | 2  | 4  | 8  | 8  | 8000/64 (24)    | 6400/96   | 2/1500  |
| Standard_F4s  | 4  | 8  | 16 | 16 | 16000/128 (48)  | 12800/192 | 4/3000  |
| Standard_F8s  | 8  | 16 | 32 | 32 | 32000/256 (96)  | 25600/384 | 8/6000  |
| Standard_F16s | 16 | 32 | 64 | 64 | 64000/512 (192) | 51200/768 | 8/12000 |

Mbps = 每秒 10^6 字节，GiB = 1024^3 字节。

<sup>1</sup> Fs 系列 VM 可能的最大磁盘吞吐量（IOPS 或 Mbps）可能受限于附加磁盘的数量、大小和条带化。  有关详细信息，请参阅 [高性能设计](premium-storage-performance.md)。

<br>



### <a name="basic-a"></a>基本 A  

**更新的大小建议**：[Av2 系列](av2-series.md)

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

**更新的大小建议**：[Av2 系列](av2-series.md)

ACU：50-100

高级存储：不支持

高级存储缓存：不支持

| 大小 | vCPU | 内存:GiB | 临时存储 (HDD)：GiB | 最大数据磁盘数 | 数据磁盘最大吞吐量：IOPS | 最大 NIC 数/预期网络带宽 (Mbps) |
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

**更新的大小建议**：[Av2 系列](av2-series.md)

ACU：225

高级存储：不支持

高级存储缓存：不支持

A8-A11 和 H 系列大小也称为 *计算密集型实例*。 运行这些大小的硬件专为计算密集型和网络密集型应用程序而设计和优化，包括高性能计算 (HPC) 群集应用程序、建模和模拟。 A8-A11 系列使用 Intel Xeon E5-2670 @ 2.6 GHZ，H 系列使用 Intel Xeon E5-2667 v3 @ 3.2 GHz。  

| 大小 | vCPU | 内存:GiB | 临时存储 (HDD)：GiB | 最大数据磁盘数 | 数据磁盘最大吞吐量：IOPS | 最大 NIC 数|
|---|---|---|---|---|---|---|
| Standard_A8 &nbsp; <sup>1</sup> | 8  | 56  | 382 | 32 | 32x500 | 2 |
| Standard_A9 &nbsp; <sup>1</sup> | 16 | 112 | 382 | 64 | 64x500 | 4 |
| Standard_A10 | 8  | 56  | 382 | 32 | 32x500 | 2 |
| Standard_A11 | 16 | 112 | 382 | 64 | 64x500 | 4 |

<sup>1</sup>对于 MPI 应用程序来说，专用 RDMA 后端网络是通过 FDR InfiniBand 网络启用的，后者可以提供相当低的延迟和高带宽。  

> [!NOTE]
> A8 – A11 VM 计划于 2021 年 3 月停用。 强烈建议不要创建任何新的 A8 – A11 Vm。 请将任何现有的 A8 – A11 Vm 迁移到新的、功能强大的高性能计算 VM 大小（如 H、HB-ACCT-WC、HC、HBv2）以及常规用途计算 VM 大小（如 D、E 和 F），以获得更好的性价比。 有关详细信息，请参阅 [HPC 迁移指南](https://azure.microsoft.com/resources/hpc-migration-guide/)。

<br>

### <a name="d-series"></a>D 系列  

**较新的大小建议**： [Dav4 系列](dav4-dasv4-series.md)、 [Dv4 系列](dv4-dsv4-series.md) 和 [Ddv4 系列](ddv4-ddsv4-series.md)

ACU：160-250 <sup>1</sup>

高级存储：不支持

高级存储缓存：不支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量：IOPS/读取 MBps/写入 MBps | 最大数据磁盘数/吞吐量：IOPS | 最大 NIC 数/预期网络带宽 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D1  | 1 | 3.5 | 50  | 3000/46/23    | 4/4x500   | 2/500  |
| Standard_D2  | 2 | 7   | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D3  | 4 | 14  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D4  | 8 | 28  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |

<sup>1</sup> VM 系列可以在下述 CPU 之一上运行：2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 或 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>D 系列 - 内存优化  

**较新的大小建议**： [Dav4 系列](dav4-dasv4-series.md)、 [Dv4 系列](dv4-dsv4-series.md) 和 [Ddv4 系列](ddv4-ddsv4-series.md)

ACU：160-250 <sup>1</sup>

高级存储：不支持

高级存储缓存：不支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量：IOPS/读取 MBps/写入 MBps | 最大数据磁盘数/吞吐量：IOPS | 最大 NIC 数/预期网络带宽 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D11 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D12 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D13 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |
| Standard_D14 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/8000 |

<sup>1</sup> VM 系列可以在下述 CPU 之一上运行：2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 或 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

## <a name="preview-dc-series"></a>预览： DC 系列

**较新的大小建议**： [DCsv2 系列](dcv2-series.md)

高级存储：支持

高级存储缓存：支持

DC 系列使用最新版本的 3.7 GHz Intel 至强 E-2176G 处理器和 SGX 技术，使用 Intel Turbo 提升技术可以达到 4.7 GHz。 

| 大小          | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大 NIC 数/预期网络带宽 (MBps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |

> [!IMPORTANT]
>
> DC 系列 Vm 是 [第2代 vm](./linux/generation-2.md#creating-a-generation-2-vm) ，仅支持 `Gen2` 映像。


### <a name="ds-series"></a>DS 系列  

**较新的大小建议**： [Dasv4 系列](dav4-dasv4-series.md)、 [Dsv4 系列](dv4-dsv4-series.md) 和 [Ddsv4 系列](ddv4-ddsv4-series.md)

ACU：160-250 <sup>1</sup>

高级存储：支持

高级存储缓存：支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数/预期网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1 | 1 | 3.5 | 7  | 4  | 4000/32 (43)    | 3200/32   | 2/500  |
| Standard_DS2 | 2 | 7   | 14 | 8  | 8000/64 (86)    | 6400/64   | 2/1000 |
| Standard_DS3 | 4 | 14  | 28 | 16 | 16000/128 (172) | 12800/128 | 4/2000 |
| Standard_DS4 | 8 | 28  | 56 | 32 | 32000/256 (344) | 25600/256 | 8/4000 |

<sup>1</sup> VM 系列可以在下述 CPU 之一上运行：2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 或 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS 系列 - 内存优化  

**较新的大小建议**： [Dasv4 系列](dav4-dasv4-series.md)、 [Dsv4 系列](dv4-dsv4-series.md) 和 [Ddsv4 系列](ddv4-ddsv4-series.md)

ACU：160-250 <sup>1,2</sup>

高级存储：支持

高级存储缓存：支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数/预期网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS11 | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/64   | 2/1000 |
| Standard_DS12 | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/128 | 4/2000 |
| Standard_DS13 | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/256 | 8/4000 |
| Standard_DS14 | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/512 | 8/8000 |

<sup>1</sup> DS 系列 VM 可能的最大磁盘吞吐量（IOPS 或 MBps）可能受限于附加磁盘的数量、大小和条带化。  有关详细信息，请参阅 [高性能设计](premium-storage-performance.md)。
<sup>2</sup> VM 系列可以在下述 CPU 之一上运行：2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 或 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ls-series"></a>Ls 系列

**较新的大小建议**： [Lsv2 系列](lsv2-series.md)

Ls-系列提供多达 32 个 vCPU，使用的是 [Intel® Xeon® 处理器 E5 v3 系列](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html)。 Ls 系列与 G/GS-系列拥有相同的 CPU 性能，并且每个 vCPU 附带 8 GiB 内存。

Ls 系列不支持创建本地缓存以增加持久数据磁盘可实现的 IOPS。 本地磁盘的高吞吐量和 IOPS 使 Ls 系列 Vm 非常适合于 NoSQL 存储，例如 Apache Cassandra 和 MongoDB，可跨多个 Vm 复制数据，从而在单个 VM 发生故障时保持持久性。

ACU：180-240

高级存储：支持

高级存储缓存：不支持

| 大小 | vCPU | 内存 (GiB) | 临时存储 (GiB) | 最大数据磁盘数 | 最大临时存储吞吐量 (IOPS/MBps)  | 最大未缓存磁盘吞吐量 (IOPS/MBps)  | 最大 NIC 数/预期网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_L4s   | 4  | 32  | 678  | 16 | 20000/200 | 5000/125  | 2/4000  |
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  |
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 |
| Standard_L32s &nbsp; <sup>1</sup> | 32 | 256 | 5630 | 64 | 160000/1600 | 40000/1000 | 8/20000 |

Ls 系列 VM 可能的最大磁盘吞吐量可能受限于任何附加磁盘的数量、大小和条带化。 有关详细信息，请参阅 [高性能设计](premium-storage-performance.md)。

<sup>1</sup> 实例对于专用于单个客户的硬件独立。

### <a name="gs-series"></a>GS 系列

**较新的大小建议**： [Easv4 系列](eav4-easv4-series.md)、 [Esv4 系列](ev4-esv4-series.md)、 [Edsv4 系列](edv4-edsv4-series.md) 和 [M 系列](m-series.md)

ACU：180 - 240 <sup>1</sup>

高级存储：支持

高级存储缓存：支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数/预期网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 | 2 | 28  | 56  | 8  | 10000/100 (264)   | 5000/ 125  | 2/2000 |
| Standard_GS2 | 4 | 56  | 112 | 16 | 20000/200 (528)   | 10000/ 250 | 2/4000 |
| Standard_GS3 | 8 | 112 | 224 | 32 | 40000/400 (1056)  | 20000/ 500 | 4/8000 |
| Standard_GS4&nbsp;<sup>3</sup> | 16 | 224 | 448 | 64 | 80000/800 (2112)  | 40000/1000 | 8/16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> | 32 | 448 |896 | 64 |160000/1600 (4224)  | 80000/2000 | 8/20000 |

<sup>1</sup> GS 系列 VM 可能的最大磁盘吞吐量（IOPS 或 MBps）可能受限于附加磁盘的数量、大小和条带化。 有关详细信息，请参阅 [高性能设计](premium-storage-performance.md)。

<sup>2</sup> 实例对于专用于单个客户的硬件独立。

<sup>3</sup> 受约束的可用核心大小。

<br>

### <a name="g-series"></a>G 系列

**较新的大小建议**： [Eav4 系列](eav4-easv4-series.md)、 [Ev4 系列](ev4-esv4-series.md) 和 [Edv4 系列](edv4-edsv4-series.md) 和 [M 系列](m-series.md)

ACU：180 - 240

高级存储：不支持

高级存储缓存：不支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量：IOPS/读取 MBps/写入 MBps | 最大数据磁盘数/吞吐量：IOPS | 最大 NIC 数/预期网络带宽 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_G1  | 2  | 28  | 384  | 6000/93/46    | 8/8x500   | 2/2000  |
| Standard_G2  | 4  | 56  | 768  | 12000/187/93  | 16/16x500 | 2/4000  |
| Standard_G3  | 8  | 112 | 1536 | 24000/375/187 | 32/32x500 | 4/8000  |
| Standard_G4  | 16 | 224 | 3072 | 48000/750/375 | 64/64x500 | 8/16000 |
| Standard_G5&nbsp;<sup>1</sup> | 32 | 448 | 6144 | 96000/1500/750| 64/64x500 | 8/20000 |

<sup>1</sup> 实例对于专用于单个客户的硬件独立。
<br>

## <a name="nv-series"></a>NV 系列
**较新的大小建议**： [NVv3 系列](nvv3-series.md) 和 [NVv4 系列](nvv4-series.md)

NV 系列虚拟机采用 [NVIDIA Tesla M60 ](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 和 NVIDIA GRID 技术，适用于桌面加速型应用程序和虚拟桌面，方便客户将其数据或模拟可视化。 用户可以在 NV 实例上直观显示其图形密集型工作流以获取高级图形功能，并可额外运行单精度工作负荷，例如编码和渲染。 NV 系列 Vm 还由 Intel 强 2690 v3 (Haswell) Cpu 提供支持。

NV 实例中的每个 GPU 都带有 GRID 许可证。 使用此许可证，可以灵活地将 NV 实例用作单个用户的虚拟工作站，或将 25 个并发用户都连接到用于虚拟应用程序方案的 VM。

高级存储：不支持

高级存储缓存：不支持

实时迁移：不支持

内存保留更新：不支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大 NIC 数 | 虚拟工作站 | 虚拟应用程序 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = 半块 M60 卡。
<br>


## <a name="nvv2-series"></a>NVv2 系列

**较新的大小建议**： [NVv3 系列](nvv3-series.md)

NVv2 系列虚拟机采用 Intel Broadwell CPU，由 [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 和 NVIDIA GRID 技术提供支持。 此类虚拟机面向 GPU 加速图形应用程序和虚拟桌面，客户希望利用这些应用和桌面直观呈现数据、模拟要查看的结果、处理 CAD 或渲染和流式处理内容。 此外，这些虚拟机还能运行编码和渲染等单精度工作负荷。 NVv2 虚拟机支持高级存储，且与之前的 NV 系列相比，系统内存 (RAM) 翻了一番。  

NVv2 实例中的每个 GPU 都带有 GRID 许可证。 使用此许可证，可以灵活地将 NV 实例用作单个用户的虚拟工作站，或将 25 个并发用户都连接到用于虚拟应用程序方案的 VM。

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大 NIC 数 | 虚拟工作站 | 虚拟应用程序 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1280 | 4 | 32 | 32 | 8 | 4 | 100 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

<br>

## <a name="nc-series"></a>NC 系列
**较新的大小建议**： [NC T4 v3 系列](nct4-v3-series.md)

NC 系列 Vm 由 [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) 卡和 Intel 2690 E5-V3 (Haswell) 处理器提供支持。 通过将 CUDA 用于能源勘探应用、碰撞模拟、光纤跟踪渲染、深度学习等领域，用户可以更快地分析数据。 NC24r 配置提供了针对紧密耦合的并行计算工作负荷优化的低延迟、高吞吐量网络接口。

[高级存储](premium-storage-performance.md)：不支持<br>
[高级存储缓存](premium-storage-performance.md)：不支持<br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 生成支持](generation-2.md)：第1代<br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大 NIC 数 |
|---|---|---|---|---|---|---|---|
| Standard_NC6    | 6  | 56  | 340  | 1 | 12 | 24 | 1 |
| Standard_NC12   | 12 | 112 | 680  | 2 | 24 | 48 | 2 |
| Standard_NC24   | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = 半块 K80 卡。

*支持 RDMA


<br>


## <a name="ncv2-series"></a>NCv2 系列
**较新的大小建议**： [NC T4 V3 系列](nct4-v3-series.md) 和 [nc V100 v3 系列](ncv3-series.md)

NCv2 系列 VM 采用 NVIDIA Tesla P100 GPU。 这些 GPU 可提供比 NC 系列高 2 倍以上的计算性能。 客户可将这些更新的 GPU 用于传统的 HPC 工作负荷，例如油藏模拟、DNA 测序、蛋白质分析、Monte Carlo 模拟和其他工作负荷。 除了 Gpu 以外，NCv2 系列 Vm 还由 Intel Broadwell E5-2690 v4 提供支持 () Cpu。

NC24rs v2 配置提供了针对紧密耦合的并行计算工作负荷优化的低延迟、高吞吐量网络接口。

[高级存储](premium-storage-performance.md)：受支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 生成支持](generation-2.md)：第1代和第2代<br>

> 对于此 VM 系列，订阅中的 vCPU（核心）配额最初在每个区域中设置为 0。 在[可用区域](https://azure.microsoft.com/regions/services/)中为此系列[请求 vCPU 配额增加](../azure-portal/supportability/resource-manager-core-quotas-request.md)。
>
| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数 |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v2    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v2   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2* | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = 一个 P100 卡。

*支持 RDMA

<br>

## <a name="nd-series"></a>ND 系列
**较新的大小建议**： [NDv2 系列](ndv2-series.md) 和 [NC V100 v3 系列](ncv3-series.md)

ND 系列虚拟机是针对 AI 和深度学习工作负荷设计的 GPU 系列的新成员。 它们在训练和推理方面性能卓越。 ND 实例由 [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) gpu 和 2690 (Broadwell) cpu 提供支持。 这些实例可以针对单精度浮点运算和利用 Microsoft 认知工具包、TensorFlow、Caffe 及其他框架的 AI 工作负荷提供卓越的性能。 ND 系列还提供了更大的 GPU 内存大小（24 GB），能够适应更大的神经网络模型。 与 NC 系列一样，ND 系列可通过 RDMA 和 InfiniBand 连接提供含辅助型低延迟、高吞吐量网络的配置，以便可运行跨多个 GPU 的大规模训练作业。

[高级存储](premium-storage-performance.md)：受支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 生成支持](generation-2.md)：第1代和第2代<br>

> 对于此 VM 系列，订阅中每个区域的 vCPU (核心) 配额最初设置为0。 在[可用区域](https://azure.microsoft.com/regions/services/)中为此系列[请求 vCPU 配额增加](../azure-portal/supportability/resource-manager-core-quotas-request.md)。
>
| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数 |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 24 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = 一个 P40 卡。

*支持 RDMA

<br>

## <a name="other-sizes"></a>其他大小

* [常规用途](sizes-general.md)
* [计算优化](sizes-compute.md)
* [内存优化](sizes-memory.md)
* [存储优化](sizes-storage.md)
* [GPU](sizes-gpu.md)
* [高性能计算](sizes-hpc.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
