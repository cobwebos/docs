---
title: Azure VM 大小命名约定
description: 说明用于 Azure VM 大小的命名约定
ms.service: virtual-machines
subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.date: 7/22/2020
ms.author: mimckitt
ms.custom: sttsinar
ms.openlocfilehash: 13894e534dc8d6dd89baf75ea2bd3b6500b718f7
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650955"
---
# <a name="azure-virtual-machine-sizes-naming-conventions"></a>Azure 虚拟机大小命名约定

此页概述了用于 Azure Vm 的命名约定。 Vm 使用这些命名约定来表示不同的功能和规范。

## <a name="naming-convention-explanation"></a>命名约定说明

**[家族]**  + **[子系列 *]**  + **[个 vcpu] 的 [# of]**  + **[加法功能]**  + **[快捷键类型 *]**  + **[版本]**

|值 | 说明|
|---|---|
| 系列 | 指示 VM 系列系列| 
| * 子系列 | 仅用于专用 VM differentiations|
| 个 vcpu 数| 表示 VM 的个 vcpu 数 |
| 附加功能 | 一个或多个小写字母表示附加功能，例如： <br> a = 基于 AMD 的处理器 <br> d =)  (本地临时磁盘的磁盘;这适用于较新的 Azure Vm，请参阅 [Ddv4 和 Ddsv4 系列](./ddv4-ddsv4-series.md) <br> h = 支持休眠 <br> i = 隔离大小 <br> l = 内存不足;内存量要低于内存密集型大小 <br> m = 占用大量内存;特定大小的最大内存量 <br> t = 小内存;特定大小的最小内存量 <br> r = 支持 RDMA <br> s = 支持高级存储，包括 [超级 SSD](./disks-types.md#ultra-disk) (的可能使用情况。注意：没有属性的某些较新大小仍可支持高级存储，例如 M128、m64-16ms 等 ) <br> |
| * 加速器类型 | 表示专用/GPU Sku 中的硬件加速器类型。 只有从第 3 2020 季度启动的新专用/GPU Sku 才能在名称中包含硬件加速器。 |
| 版本 | 表示 VM 系列系列的版本 |

## <a name="example-breakdown"></a>示例细目

**[家族]**  + **[子系列 *]**  + **[个 vcpu] 的 [# of]**  + **[加法功能]**  + **[快捷键类型 *]**  + **[版本]**

### <a name="example-1-m416ms_v2"></a>示例1： M416ms_v2

|值 | 说明|
|---|---|
| 系列 | M | 
| 个 vcpu 数 | 416 |
| 附加功能 | m = 占用大量内存 <br> s = 支持高级存储 |
| 版本 | v2 |

### <a name="example-2-nv16as_v4"></a>示例2： NV16as_v4

|值 | 说明|
|---|---|
| 系列 | N | 
| 子系列 | V |
| 个 vcpu 数 | 16 |
| 附加功能 | a = 基于 AMD 的处理器 <br> s = 支持高级存储 |
| 版本 | v4 |

### <a name="example-3-nc4as_t4_v3"></a>示例3： NC4as_T4_v3

|值 | 说明|
|---|---|
| 系列 | N | 
| 子系列 | C |
| 个 vcpu 数 | 4 |
| 附加功能 | a = 基于 AMD 的处理器 <br> s = 支持高级存储 |
| 加速器类型 | T4 |
| 版本 | v3 |

## <a name="next-steps"></a>后续步骤

详细了解 Azure 中的可用 [VM 大小](./sizes.md) 。 