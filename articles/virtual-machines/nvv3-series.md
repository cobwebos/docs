---
title: NVv3 系列-Azure 虚拟机
description: NVv3 系列 Vm 的规格。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: a353abe8cdedb01138c8b633750b90bd9870e5d2
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666873"
---
# <a name="nvv3-series"></a>NVv3 系列

NVv3 系列虚拟机由[NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 和 nvidia GRID 技术提供支持，其中包含 intel E5-2690 V4 （Broadwell） Cpu 和 Intel 超线程技术。 此类虚拟机面向 GPU 加速图形应用程序和虚拟桌面，客户希望利用这些应用和桌面直观呈现数据、模拟要查看的结果、处理 CAD 或渲染和流式处理内容。 此外，这些虚拟机还能运行编码和渲染等单精度工作负荷。 NVv3 虚拟机支持高级存储，并在与其前置 NV 系列进行比较时提供系统内存（RAM）的两倍。  

NVv3 实例中的每个 GPU 都附带了网格许可证。 使用此许可证，可以灵活地将 NV 实例用作单个用户的虚拟工作站，或将 25 个并发用户都连接到用于虚拟应用程序方案的 VM。

高级存储缓存：支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大非缓存磁盘吞吐量： IOPS/MBps | 最大 NIC 数 | 虚拟工作站 | 虚拟应用程序 |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_NV12s_v3 |12 | 112 | 320  | 1 | 8  | 12 | 20000/200 | 4 | 1 | 25  |
| Standard_NV24s_v3 |24 | 224 | 640  | 2 | 16 | 24 | 40000/400 | 8 | 2 | 50  |
| Standard_NV48s_v3 |48 | 448 | 1280 | 4 | 32 | 32 | 80000/800 | 8 | 4 | 100 |

1 GPU = 半块 M60 卡。

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