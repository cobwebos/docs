---
title: Ev4 和 Esv4 系列-Azure 虚拟机
description: Ev4 和 Esv4 系列 Vm 的规格。
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: ccb6bbcddaa5ce76e9dbb04d737830fbaf5c2d9f
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86134336"
---
# <a name="ev4-and-esv4-series"></a>Ev4 和 Esv4 系列

Ev4 和 Esv4 系列在 &reg; 超线程配置的 Intel 强 &reg; 白金8272CL （级联版）处理器上运行，非常适合于各种内存密集型企业应用程序和功能，最高可达 504GiB RAM。

> [!NOTE]
> 对于常见问题，请参阅[没有本地临时磁盘的 AZURE VM 大小](azure-vms-no-temp-disk.md)。

## <a name="ev4-series"></a>Ev4 系列

Ev4 系列大小在 Intel 至强 &reg; 白金8272CL （Cascade Lake）上运行。 Ev4 系列实例适用于内存密集型企业应用程序。 Ev4 系列 Vm 支持 Intel &reg; 超线程技术。

远程数据磁盘存储与虚拟机分开计费。 若要使用高级存储磁盘，请使用 Esv4 大小。 Esv4 大小的定价和计费标准与 Ev4 系列相同。

> [!IMPORTANT]
> 这些新大小目前仅限公共预览。 可在[此处](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u)注册这些 Ev4 和 Esv4 系列。 

ACU：195 - 210

高级存储：不支持

高级存储缓存：不支持

实时迁移：支持

内存保留更新：支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大 NIC 数/预期网络带宽 (Mbps) |
|---|---|---|---|---|---|
| Standard_E2_v4  | 2 | 16   | 仅远程存储 | 4 | 2/1000  |
| Standard_E4_v4  | 4 | 32  | 仅远程存储 | 8 | 2/2000  |
| Standard_E8_v4  | 8 | 64 | 仅远程存储 | 16 | 4/4000 |
| Standard_E16_v4 | 16 | 128 | 仅远程存储 | 32 | 8/8000 |
| Standard_E20_v4 | 20 | 160 | 仅远程存储 | 32 | 8/10000 |
| Standard_E32_v4 | 32 | 256 | 仅远程存储 | 32 | 8/16000 |
| Standard_E48_v4 | 48 | 384 | 仅远程存储 | 32 | 8/24000 |
| Standard_E64_v4 | 64 | 504 | 仅远程存储 | 32| 8/30000 |


## <a name="esv4-series"></a>Esv4 系列

Esv4 系列大小在 Intel &reg; 至强 &reg; 白金8272CL （Cascade Lake）上运行。 Esv4 系列实例适用于内存密集型企业应用程序。 Evs4 系列 Vm 支持 Intel &reg; 超线程技术。 远程数据磁盘存储与虚拟机分开计费。

> [!IMPORTANT]
> 这些新大小目前仅限公共预览。 可在[此处](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u)注册这些 Ev4 和 Esv4 系列。 

ACU：195-210

高级存储：支持

高级存储缓存：支持

实时迁移：支持

内存保留更新：支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 缓存的最大吞吐量： IOPS/MBps （GiB 中的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数/预期网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | 仅远程存储 | 4 | 19000/120 （50） | 3200/48 | 2/1000  |
| Standard_E4s_v4  | 4 | 32  | 仅远程存储 | 8 | 38500/242 （100） | 6400/96 | 2/2000  |
| Standard_E8s_v4  | 8 | 64  | 仅远程存储 | 16 | 77000/485 （200） | 12800/192 | 4/4000 |
| Standard_E16s_v4 | 16 | 128 | 仅远程存储 | 32 | 154000/968 （400） | 25600/384 | 8/8000 |
| Standard_E20s_v4 | 20 | 160 | 仅远程存储 | 32 | 193000/1211 （500） | 32000/480  | 8/10000 |
| Standard_E32s_v4 | 32 | 256 | 仅远程存储 | 32 | 308000/1936 （800） | 51200/768  | 8/16000 |
| Standard_E48s_v4 | 48 | 384 | 仅远程存储 | 32 | 462000/2904 （1200） | 76800/1152 | 8/24000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| 仅远程存储 | 32 | 615000/3872 （1600） | 80000/1200 | 8/30000 |

<sup>1</sup>个[受约束的可用核心大小](https://docs.microsoft.com/azure/virtual-machines/windows/constrained-vcpu)。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
