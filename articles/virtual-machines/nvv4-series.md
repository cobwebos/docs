---
title: NVv4 系列-Azure 虚拟机
description: NVv4 系列 Vm 的规格。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 2afec4dc89c8d8892356888ec3404d36ba929d23
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669635"
---
# <a name="nvv4-series-preview"></a>NVv4 系列（预览）

NVv4 系列虚拟机由[Amd Radeon INSTINCT MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU 和 AMD EPYC 7V12 （罗马） cpu 提供支持。 借助 NVv4 系列，Azure 引入了包含部分 Gpu 的虚拟机。 从 GPU 的 1/8 开始，为 GPU 加速图形应用程序和虚拟桌面选取适当大小的虚拟机，并将 2 GiB 帧缓冲到带有 16 GiB 帧缓冲区的完整 GPU。 NVv4 虚拟机当前仅支持 Windows 来宾操作系统。

[注册并在预览期访问这些虚拟机](https://aka.ms/nvv4signup)。
<br>

高级存储：支持

高级存储缓存：支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大 NIC 数 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> NVv4 系列 VM 功能 AMD 并发多线程技术

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

若要利用运行 Windows、NVIDIA 或 AMD GPU 驱动程序的 Azure N 系列 Vm 的 GPU 功能，必须安装。

[NVIDIA GPU 驱动程序扩展](./extensions/hpccompute-gpu-windows.md)在 Windows N 系列 VM 上安装合适的 nvidia CUDA 或网格驱动程序。 请使用 Azure 门户或工具（例如 Azure PowerShell 或 Azure 资源管理器模板）安装或管理该扩展。 有关受支持的操作系统和部署步骤，请参阅 [NVIDIA GPU 驱动程序扩展文档](./extensions/hpccompute-gpu-windows.md)。 有关 VM 扩展的常规信息，请参阅 [Azure 虚拟机扩展和功能](/.extensions/overview.md)。

如果选择手动安装 NVIDIA GPU 驱动程序，请参阅适用于[Windows 的 N 系列 GPU 驱动程序设置](./windows/n-series-driver-setup.md)以获取支持的操作系统、驱动程序、安装和验证步骤。

若要手动安装 AMD GPU 驱动程序，请参阅适用于[Windows 的 N 系列 AMD gpu 驱动程序设置](./windows/n-series-amd-driver-setup.md)以获取支持的操作系统、驱动程序、安装和验证步骤。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。