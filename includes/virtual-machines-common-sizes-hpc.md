---
title: include 文件
description: include 文件
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 489ac7fa37c10a27de971151f0be35c647d2186f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74116596"
---
Azure HPC 优化虚拟机（Vm）旨在为各种真实的应用程序提供领先的性能、MPI 可伸缩性和成本效益。
 
## <a name="infiniband-networking-for-large-scale-hpc"></a>大规模 HPC 的无限网络连接
HBv2 Vm 功能 200 Gb/秒 Mellanox HDR 不会有功能，同时，HB-ACCT-WC 和 HC Vm 功能 100 Gb/秒 Mellanox EDR。 其中每个 VM 类型都连接在一个非阻塞 fat 树中，以实现优化和一致的 RDMA 性能。

HBv2 Vm 支持自适应路由和动态连接传输（DCT，附加到标准 RC 和 UD 传输）。 这些功能增强了应用程序的性能、可伸缩性和一致性，并且强烈建议使用这些功能。  

HBv2、HB-ACCT-WC 和 HC Vm 支持标准 Mellanox/OFED 驱动程序。 同样，支持所有 RDMA 谓词和 MPI 类型。 其中每个 VM 类型还支持基于硬件的集体卸载，以提高应用程序性能。
 
原始 H 系列 Vm 功能 56 Gb/秒，FDR 的带宽。 若要利用 H 系列上的未受支持的接口，客户必须使用 Azure Marketplace 中特定于此 VM 类型的官方支持映像进行部署。 


## <a name="hbv2-series"></a>HBv2 系列
HBv2 系列 Vm 针对内存带宽驱动的应用程序进行了优化，例如流体 dynamics、有限元素分析和容器模拟。 HBv2 Vm 功能 120 AMD EPYC 7742 处理器核心，每个 CPU 核心 4 GB RAM，无并发多线程处理。 每个 HBv2 VM 提供高达 340 GB/秒的内存带宽和最多4个兆次的 FP64 计算。 

高级存储：支持

| 大小 | vCPU | 处理器 | 内存 (GB) | 内存带宽 GB/秒 | 基本 CPU 频率（GHz） | 所有核心频率（GHz、峰值） | 单核频率（GHz、峰值） | RDMA 性能（Gb/秒） | MPI 支持 | 临时存储（GB） | 最大数据磁盘数 | 最大以太网 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs | 120 | AMD EPYC 7742 | 480 | 350 | 2.45 | 2.45 | 3.4 | 200 | All | 480 + 960 | 8 | 1 |

<br>

## <a name="hb-series"></a>HB 系列
HB 系列 VM 针对内存带宽驱动的应用进行了优化，例如流体动力学、显式有限元素分析和天气建模等应用。 HB-ACCT-WC Vm 功能 60 AMD EPYC 7551 处理器核心，每个 CPU 核心 4 GB RAM，无并发多线程处理。 HB-ACCT-WC VM 提供高达 260 GB/秒的内存带宽。  

ACU：199-216

高级存储：支持

高级存储缓存：支持

| 大小 | vCPU | 处理器 | 内存 (GB) | 内存带宽 GB/秒 | 基本 CPU 频率（GHz） | 所有核心频率（GHz、峰值） | 单核频率（GHz、峰值） | RDMA 性能（Gb/秒） | MPI 支持 | 临时存储（GB） | 最大数据磁盘数 | 最大以太网 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | All | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>HC 系列
HC 系列 Vm 针对密集计算驱动的应用程序进行了优化，如隐式有限元素分析、分子 dynamics 和计算化学。 HB VM 具有 44 个 Intel Xeon Platinum 8168 处理器核心、每 CPU 核心 8 GB RAM，无超线程。 Intel 强白金平台支持 Intel 丰富的软件工具生态系统，如 Intel 数学内核库。 

ACU：297-315

高级存储：支持

高级存储缓存：支持


| 大小 | vCPU | 处理器 | 内存 (GB) | 内存带宽 GB/秒 | 基本 CPU 频率（GHz） | 所有核心频率（GHz、峰值） | 单核频率（GHz、峰值） | RDMA 性能（Gb/秒） | MPI 支持 | 临时存储（GB） | 最大数据磁盘数 | 最大以太网 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel 强白金8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | All | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>H 系列
H 系列 Vm 针对按高 CPU 频率驱动的应用程序和每个核心要求提供较大内存的优化。 H 系列 Vm 功能8或 16 Intel 至强 E5 2667 v3 处理器核心，每个 CPU 核心最多 14 GB RAM，无超线程。 H 系列 VM 提供高达 80 GB/秒的内存带宽。

ACU：290-300

高级存储：不支持

高级存储缓存：不支持

| 大小 | vCPU | 处理器 | 内存 (GB) | 内存带宽 GB/秒 | 基本 CPU 频率（GHz） | 所有核心频率（GHz、峰值） | 单核频率（GHz、峰值） | RDMA 性能（Gb/秒） | MPI 支持 | 临时存储（GB） | 最大数据磁盘数 | 最大以太网 Nic |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel 至强 E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 1.x，MS-MPI | 1000 | 32 | 2 |
| Standard_H16 | 16 | Intel 至强 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 |  - | Intel 1.x，MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel 至强 E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 1.x，MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel 至强 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 1.x，MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel 至强 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 1.x，MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel 至强 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 1.x，MS-MPI | 2000 | 64 | 4 |

<sup>1</sup>对于 MPI 应用程序，专用 RDMA 后端网络是通过 FDR 无限网络启用的。

<br>
