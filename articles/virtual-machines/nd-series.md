---
title: ND 系列-Azure 虚拟机
description: ND 系列 Vm 的规格。
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 9acf92649d5b44e277d3ae3d277c9b1112fdcdd0
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273894"
---
# <a name="nd-series"></a>ND 系列

ND 系列虚拟机是针对 AI 和深度学习工作负荷设计的 GPU 系列的新成员。 它们在训练和推理方面性能卓越。 ND 实例由[NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) Gpu 和 Intel 2690 V4 （Broadwell） cpu 提供支持。 这些实例可以针对单精度浮点运算和利用 Microsoft 认知工具包、TensorFlow、Caffe 及其他框架的 AI 工作负荷提供卓越的性能。 ND 系列还提供了更大的 GPU 内存大小（24 GB），能够适应更大的神经网络模型。 与 NC 系列一样，ND 系列可通过 RDMA 和 InfiniBand 连接提供含辅助型低延迟、高吞吐量网络的配置，以便可运行跨多个 GPU 的大规模训练作业。

高级存储：支持

高级存储缓存：支持

实时迁移：不支持

内存保留更新：不支持

> [!IMPORTANT]
> 对于此 VM 系列，订阅中每个区域的 vCPU （核心）配额最初设置为0。 在[可用区域](https://azure.microsoft.com/regions/services/)中请求此系列的[vCPU 配额增加](../azure-supportability/resource-manager-core-quotas-request.md)。
>
| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大非缓存磁盘吞吐量： IOPS/MBps | 最大 NIC 数 |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = 一个 P40 卡。

\* 支持 RDMA [!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

若要利用 Azure N 系列 Vm 的 GPU 功能，必须安装 NVIDIA GPU 驱动程序。

[NVIDIA GPU 驱动程序扩展](./extensions/hpccompute-gpu-windows.md)可在 N 系列 VM 上安装适当的 NVIDIA CUDA 或 GRID 驱动程序。 请使用 Azure 门户或工具（例如 Azure PowerShell 或 Azure 资源管理器模板）安装或管理该扩展。 有关受支持的操作系统和部署步骤，请参阅 [NVIDIA GPU 驱动程序扩展文档](./extensions/hpccompute-gpu-windows.md)。 有关 VM 扩展的常规信息，请参阅 [Azure 虚拟机扩展和功能](./extensions/overview.md)。

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
