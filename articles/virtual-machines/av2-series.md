---
title: Av2 系列
description: Av2 系列 VM 的规范。
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 65aab240dd0be97aafa370883aa953f8eb766cf2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650742"
---
# <a name="av2-series"></a>Av2 系列

Av2 系列 VM 可以部署在各种不同的硬件类型和处理器上。 Av2 系列 VM 的 CPU 性能和内存配置非常适合部署和测试等入门级工作负荷。 无论部署在哪个硬件上，都会限制大小以为正在运行的实例提供一致的处理器性能。 若要判断此大小部署所在的物理硬件，请从虚拟机中查询虚拟硬件。 一些示例用例包括开发和测试服务器、低流量 Web 服务器、中小型数据库、概念证明和代码存储库。

[ACU](acu.md)：100<br>
[高级存储](premium-storage-performance.md)：不支持 <br>
[高级存储缓存](premium-storage-performance.md)：不支持 <br>
[实时迁移](maintenance-and-updates.md)：支持 <br>
[内存保留更新](maintenance-and-updates.md)：受支持 <br>
[VM 生成支持](generation-2.md)：第1代 <br>
<br>

| 大小 | vCore | 内存:GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量：IOPS/读取 MBps/写入 MBps | 最大数据磁盘数/吞吐量：IOPS | 最大 NIC 数 | 预期的网络带宽 (Mbps)
|---|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2/2x500   | 2 | 250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |

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
