---
title: HC 系列 - Azure 虚拟机
description: HC 系列 VM 的规格。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: cc25fb9b21d535ef07bcfae673be48216427b370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164774"
---
# <a name="hc-series"></a>HC 系列

HC 系列 VM 针对密集计算驱动的应用进行了优化，例如隐式有限元分析、分子动力学和计算化学。 HC VM 具有 44 个英特尔至强白金 8168 处理器内核、每个 CPU 内核 8 GB 的 RAM，并且没有超线程。 英特尔到强白金平台支持英特尔丰富的软件工具生态系统，如英特尔数学内核库。

ACU： 297-315

高级存储：支持

高级存储缓存：支持

实时迁移：不支持

内存保留更新：不支持

| 大小 | vCPU | 处理器 | 内存 (GB) | 内存带宽 GB/s | 基本 CPU 频率 （GHz） | 全核频率（GHz，峰值） | 单核频率（GHz，峰值） | RDMA 性能（Gb/s） | MPI 支持 | 临时存储 （GB） | 最大数据磁盘数 | 最大以太网 NIC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | 英特尔新恩铂金 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | All | 700 | 4 | 1 |

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