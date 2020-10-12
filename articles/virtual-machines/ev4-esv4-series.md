---
title: Ev4 和 Esv4 系列 - Azure 虚拟机
description: Ev4 和 Esv4 系列 VM 的规范。
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: 2c6540513c46307e4bd51b163dd77984395006c5
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945424"
---
# <a name="ev4-and-esv4-series"></a>Ev4 和 Esv4 系列

Ev4 和 Esv4 系列在采用超线程配置的 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 处理器上运行，非常适合各种内存密集型企业应用程序，并且配备高达 504 GiB 的 RAM。 它具有 3.4 GHz 的所有核心 Turbo 时钟速度。

> [!NOTE]
> 有关常见问题解答，请参阅[无本地临时磁盘的 Azure VM 规格](azure-vms-no-temp-disk.md)。

## <a name="ev4-series"></a>Ev4 系列

Ev4 系列规格在 Intel Xeon&reg; Platinum 8272CL (Cascade Lake) 处理器上运行。 Ev4 系列实例非常适合内存密集型企业应用程序。 Ev4 系列 VM 采用 Intel&reg; 超线程技术。

远程数据磁盘存储与虚拟机分开计费。 要使用高级存储磁盘，请使用 Esv4 规格。 Esv4 系列规格的定价和计费标准与 Ev4 系列相同。

[ACU](acu.md)： 195-210<br>
[高级存储](premium-storage-performance.md)：不支持<br>
[高级存储缓存](premium-storage-performance.md)：不支持<br>
[实时迁移](maintenance-and-updates.md)：支持<br>
[内存保留更新](maintenance-and-updates.md)：受支持<br>
[VM 生成支持](generation-2.md)：第1代<br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大 NIC 数|预期的网络带宽 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_E2_v4  | 2 | 16   | 仅限远程存储 | 4 | 2|1000  |
| Standard_E4_v4  | 4 | 32  | 仅限远程存储 | 8 | 2|2000  |
| Standard_E8_v4  | 8 | 64 | 仅限远程存储 | 16 | 4|4000 |
| Standard_E16_v4 | 16 | 128 | 仅限远程存储 | 32 | 8|8000 |
| Standard_E20_v4 | 20 | 160 | 仅限远程存储 | 32 | 8|10000 |
| Standard_E32_v4 | 32 | 256 | 仅限远程存储 | 32 | 8|16000 |
| Standard_E48_v4 | 48 | 384 | 仅限远程存储 | 32 | 8|24000 |
| Standard_E64_v4 | 64 | 504 | 仅限远程存储 | 32| 8|30000 |


## <a name="esv4-series"></a>Esv4 系列

Esv4 系列规格在 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 处理器上运行。 Esv4 系列实例非常适合于内存密集型企业应用程序。 Esv4 系列 VM 采用 Intel&reg; 超线程技术。 远程数据磁盘存储与虚拟机分开计费。

[ACU](acu.md)：195-210<br>
[高级存储](premium-storage-performance.md)：受支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：支持<br>
[内存保留更新](maintenance-and-updates.md)：受支持<br>
[VM 生成支持](generation-2.md)：第1代<br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数|预期的网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | 仅限远程存储 | 4 | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | 仅限远程存储 | 8 | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | 仅限远程存储 | 16 | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | 仅限远程存储 | 32 | 25600/384 | 8|8000 |
| Standard_E20s_v4 | 20 | 160 | 仅限远程存储 | 32 | 32000/480  | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | 仅限远程存储 | 32 | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | 仅限远程存储 | 32 | 76800/1152 | 8|24000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| 仅限远程存储 | 32 | 80000/1200 | 8|30000 |

<sup>1</sup> [受约束的可用核心规格](./constrained-vcpu.md)。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>其他大小和信息

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

定价计算器：[定价计算器](https://azure.microsoft.com/pricing/calculator/)

有关磁盘类型的详细信息：[磁盘类型](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
