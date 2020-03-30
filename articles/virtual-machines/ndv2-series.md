---
title: NDv2 系列 - Azure 虚拟机
description: NDv2 系列 VM 的规格。
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 8a2d275501816dd504130b255b73a752c5615f0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247276"
---
# <a name="updated-ndv2-series"></a>更新的 NDv2 系列

NDv2 系列虚拟机是 GPU 系列的新新增功能，专为最苛刻的 GPU 加速 AI、机器学习、仿真和 HPC 工作负载而设计。

NDv2 由 8 个 NVIDIA 特斯拉 V100 NVLINK 连接的 GPU 供电，每个 GPU 内存为 32 GB。 每个 NDv2 VM 还具有 40 个非超线程英特尔至强白金 8168 （天湖）内核和 672 GiB 的系统内存。

NDv2 实例利用 CUDA GPU 优化的计算内核，以及支持 GPU 加速的"开箱即用"的许多 AI、ML 和分析工具，如 TensorFlow、Pytorch、Caffe、RAPIDS 等，为 HPC 和 AI 工作负载提供出色的性能框架。

关键是，NDv2 专为计算密集扩展（每个 VM 占用 8 个 GPU）和横向扩展（利用多个 VM 协同工作）工作负载而构建。 NDv2 系列现在支持 100 千兆英菲尼班 EDR 后端网络，类似于 HB 系列 HPC VM 的后端网络，允许高性能群集用于并行方案，包括 AI 和 ML 的分布式培训。 此后端网络支持所有主要的 InfiniBand 协议，包括 NVIDIA NCCL2 库采用的协议，允许无缝群集 GPU。

> 在ND40rs_v2 VM[上启用 InfiniBand](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband)时，请使用 4.7-1.0.0.1 Mellanox OFED 驱动程序。
>
> 由于 GPU 内存增加，新的ND40rs_v2 VM 需要使用[第 2 代 VM](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2)和市场映像。 
>
> 请注意：具有 16 GB 每 GPU 内存的ND40s_v2不再可用于预览，并已被更新ND40rs_v2取代。

<br>

高级存储：支持

高级存储缓存：支持

实时迁移：不支持

内存保留更新：不支持

英菲尼班：支持

| 大小 | vCPU | 内存：GiB | 临时存储 （SSD）： GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大网络带宽 | 最大 NIC 数 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB （NVLink） | 32 | 32 | 80000 / 800 | 24000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

要利用 Azure N 系列 VM 的 GPU 功能，必须安装 NVIDIA GPU 驱动程序。

[NVIDIA GPU 驱动程序扩展](./extensions/hpccompute-gpu-linux.md)可在 N 系列 VM 上安装适当的 NVIDIA CUDA 或 GRID 驱动程序。 请使用 Azure 门户或工具（例如 Azure PowerShell 或 Azure 资源管理器模板）安装或管理该扩展。 有关 VM 扩展的常规信息，请参阅 [Azure 虚拟机扩展和功能](./extensions/overview.md)。

如果选择手动安装 NVIDIA GPU 驱动程序，请参阅[Linux 的 N 系列 GPU 驱动程序设置](./linux/n-series-driver-setup.md)。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。
