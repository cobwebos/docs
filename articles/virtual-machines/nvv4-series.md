---
title: NVv4 系列
description: NVv4 系列 Vm 的规格。
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 21d44044250bacc95c4dbb0dde147e87f2fed6a5
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91652731"
---
# <a name="nvv4-series"></a>NVv4 系列 

NVv4 系列虚拟机由 [Amd Radeon INSTINCT MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU 和 Amd EPYC 7V12 （ (罗马) cpu）提供支持。 借助 NVv4 系列，Azure 引入了包含部分 Gpu 的虚拟机。 从 GPU 的 1/8 开始，为 GPU 加速图形应用程序和虚拟桌面选取适当大小的虚拟机，并将 2 GiB 帧缓冲到带有 16 GiB 帧缓冲区的完整 GPU。 NVv4 虚拟机当前仅支持 Windows 来宾操作系统。

<br>

[ACU](acu.md)：230-260<br>
[高级存储](premium-storage-performance.md)：受支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 生成支持](generation-2.md)：第1代<br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大 NIC 数/预期网络带宽 (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 / 1000 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 / 2000 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 / 4000 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 / 8000 |

<sup>1</sup> NVv4 系列 VM 功能 AMD 并发多线程技术

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

若要利用运行 Windows 的 Azure NVv4 系列 Vm 的 GPU 功能，必须安装 AMD GPU 驱动程序。

若要手动安装 AMD GPU 驱动程序，请参阅适用于 [Windows 的 N 系列 AMD gpu 驱动程序设置](./windows/n-series-amd-driver-setup.md) 以获取支持的操作系统、驱动程序、安装和验证步骤。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
