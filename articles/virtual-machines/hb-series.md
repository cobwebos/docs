---
title: HB 系列 - Azure 虚拟机
description: HB 系列 VM 的规格。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 600f10e81742e9bb66c800b747fd7b2dc062754d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164825"
---
# <a name="hb-series"></a>HB 系列

HB 系列 VM 针对由内存带宽驱动的应用进行了优化，例如流体动力学、显式有限元分析和天气建模。 HB VM 具有 60 个 AMD EPYC 7551 处理器内核，每个 CPU 内核 4 GB 的 RAM，并且没有同时进行多线程。 HB VM 提供高达 260 GB/秒的内存带宽。

ACU： 199-216

高级存储：支持

高级存储缓存：支持

实时迁移：不支持

内存保留更新：不支持

| 大小 | vCPU | 处理器 | 内存 (GB) | 内存带宽 GB/s | 基本 CPU 频率 （GHz） | 全核频率（GHz，峰值） | 单核频率（GHz，峰值） | RDMA 性能（Gb/s） | MPI 支持 | 临时存储 （GB） | 最大数据磁盘数 | 最大以太网 NIC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | All | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。