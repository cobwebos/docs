---
title: HBv2 系列 - Azure 虚拟机
description: HBv2 系列 VM 的规格。
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: eea649610ca53ccbb98b5ca361555280dcd3dafe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164791"
---
# <a name="hbv2-series"></a>HBv2 系列

HBv2 系列 VM 针对由内存带宽驱动的应用进行了优化，例如流体动力学、有限元分析和储层仿真。 HBv2 VM 具有 120 个 AMD EPYC 7742 处理器内核、每个 CPU 内核 4 GB 的 RAM，并且没有同时进行多线程。 每个 HBv2 VM 提供高达 340 GB/秒的内存带宽，以及高达 4 TBFLOPS 的 FP64 计算。

高级存储：支持

实时迁移：不支持

内存保留更新：不支持

| 大小 | vCPU | 处理器 | 内存 (GB) | 内存带宽 GB/s | 基本 CPU 频率 （GHz） | 全核频率（GHz，峰值） | 单核频率（GHz，峰值） | RDMA 性能（Gb/s） | MPI 支持 | 临时存储 （GB） | 最大数据磁盘数 | 最大以太网 NIC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | All | 480 × 960 | 8 | 1 |


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