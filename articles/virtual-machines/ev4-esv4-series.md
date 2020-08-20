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
ms.openlocfilehash: 94c47369d7b638640fab1971801177103779e896
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648932"
---
# <a name="ev4-and-esv4-series"></a>Ev4 和 Esv4 系列

Ev4 和 Esv4 系列在采用超线程配置的 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 处理器上运行，非常适合各种内存密集型企业应用程序，并且配备高达 504 GiB 的 RAM。

> [!NOTE]
> 有关常见问题解答，请参阅[无本地临时磁盘的 Azure VM 规格](azure-vms-no-temp-disk.md)。

## <a name="ev4-series"></a>Ev4 系列

Ev4 系列规格在 Intel Xeon&reg; Platinum 8272CL (Cascade Lake) 处理器上运行。 Ev4 系列实例非常适合内存密集型企业应用程序。 Ev4 系列 VM 采用 Intel&reg; 超线程技术。

远程数据磁盘存储与虚拟机分开计费。 要使用高级存储磁盘，请使用 Esv4 规格。 Esv4 系列规格的定价和计费标准与 Ev4 系列相同。

> [!IMPORTANT]
> 这些新规格目前仅以公共预览版提供。 可在 [此处](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u)注册这些 Ev4 和 Esv4 系列。 

ACU：195 - 210

高级存储：不支持

高级存储缓存：不支持

实时迁移：支持

内存保留更新：支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大 NIC 数|预期网络带宽 (Mbps)  |
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

> [!IMPORTANT]
> 这些新规格目前仅以公共预览版提供。 可在 [此处](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u)注册这些 Ev4 和 Esv4 系列。 

ACU：195-210

高级存储：支持

高级存储缓存：支持

实时迁移：支持

内存保留更新：支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数|预期网络带宽 (Mbps)  |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | 仅限远程存储 | 4 | 19000/120 (50) | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | 仅限远程存储 | 8 | 38500/242 (100) | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | 仅限远程存储 | 16 | 77000/485 (200) | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | 仅限远程存储 | 32 | 154000/968 (400) | 25600/384 | 8|8000 |
| Standard_E20s_v4 | 20 | 160 | 仅限远程存储 | 32 | 193000/1211 (500) | 32000/480  | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | 仅限远程存储 | 32 | 308000/1936 (800) | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | 仅限远程存储 | 32 | 462000/2904 (1200) | 76800/1152 | 8|24000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| 仅限远程存储 | 32 | 615000/3872 (1600) | 80000/1200 | 8|30000 |

<sup>1</sup> [受约束的可用核心规格](./windows/constrained-vcpu.md)。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>其他大小和信息

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

定价计算器： [定价计算器](https://azure.microsoft.com/pricing/calculator/)

有关磁盘类型的详细信息： [磁盘类型](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。