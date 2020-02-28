---
title: NDv2 系列-Azure 虚拟机
description: NDv2 系列 Vm 的规格。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 2d38b69e468f766d21a38bda1c2da3daae50d900
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663702"
---
# <a name="updated-ndv2-series-preview"></a>更新的 NDv2 系列（预览）

NDv2 系列虚拟机是 GPU 系列的新补充，旨在满足最苛刻的 GPU 加速 AI、机器学习、模拟和 HPC 工作负荷的需求。

NDv2 由8个 NVIDIA Tesla V100 NVLINK 连接的 Gpu 提供支持，其中每个 Gpu 都具有 32 GB 的 GPU 内存。 每个 NDv2 VM 还具有40非超线程的 Intel 至强白金8168（Skylake）核心和 672 GiB 的系统内存。

NDv2 实例可为使用 CUDA GPU 优化计算内核的 HPC 和 AI 工作负荷提供卓越的性能，并提供许多支持 GPU 加速的 AI、ML 和分析工具，如 TensorFlow、Pytorch、Caffe、RAPIDS 和其他协作.

严重而言，NDv2 是为计算强度向上扩展（为每个 VM 提供8个 Gpu）和向外扩展（跨多个 vm 工作）的。 NDv2 系列现在支持100千兆位的 ""，它类似于 HB-ACCT-WC 系列 HPC VM 提供的功能，从而为并行方案（包括 AI 和 ML 的分布式培训）提供高性能群集。 此后端网络支持所有主要的不受支持的协议（包括 NVIDIA 的 NCCL2 库使用的协议），从而允许对 Gpu 进行无缝群集。


> [!NOTE]
> 在 ND40rs_v2 VM 上[启用 "允许](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband)" 时，请使用 4.7-1.0.0.1 Mellanox OFED 驱动程序。
>
> 由于 GPU 内存增加，新的 ND40rs_v2 VM 需要使用[第2代 vm](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2)和 marketplace 映像。 
>
> [注册以请求提前访问 NDv2 虚拟机预览版。](https://aka.ms/AzureNDrv2Preview)
>
> 请注意：具有 16 GB 的每个 GPU 内存的 ND40s_v2 不再可供预览，已被更新的 ND40rs_v2 取代。

<br>

高级存储：支持

高级存储缓存：支持

不受支持：支持

| 大小 | vCPU | 内存：GiB | 临时存储（SSD）： GiB | GPU | GPU 内存： GiB | 最大数据磁盘数 | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大网络带宽 | 最大 NIC 数 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB （NVLink） | 16 | 32 | 80000/800 | 24000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

若要利用 Azure N 系列 Vm 的 GPU 功能，必须安装 NVIDIA GPU 驱动程序。

[NVIDIA GPU 驱动程序扩展](./extensions/hpccompute-gpu-windows.md)可在 N 系列 VM 上安装适当的 NVIDIA CUDA 或 GRID 驱动程序。 请使用 Azure 门户或工具（例如 Azure PowerShell 或 Azure 资源管理器模板）安装或管理该扩展。 有关受支持的操作系统和部署步骤，请参阅 [NVIDIA GPU 驱动程序扩展文档](./extensions/hpccompute-gpu-windows.md)。 有关 VM 扩展的常规信息，请参阅 [Azure 虚拟机扩展和功能](/.extensions/overview.md)。

如果选择手动安装 NVIDIA GPU 驱动程序，请参阅适用于[Windows 的 n 系列 gpu 驱动程序设置](./windows/n-series-driver-setup.md)或适用于[Linux 的 n 系列 gpu 驱动程序设置](./linux/n-series-driver-setup.md)，以支持操作系统、驱动程序、安装和验证步骤。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。
