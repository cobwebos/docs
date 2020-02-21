---
title: HB-ACCT-WC 系列-Azure 虚拟机
description: HB-ACCT-WC 系列 Vm 的规格。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 124bdecac900655ed8f6ad0aaaa51fadf342f2c6
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492833"
---
# <a name="hb-series"></a>HB 系列

HB-ACCT-WC 系列 Vm 针对内存带宽驱动的应用程序进行了优化，如流体 dynamics、显式有限元素分析和天气建模。 HB-ACCT-WC Vm 功能 60 AMD EPYC 7551 处理器核心，每个 CPU 核心 4 GB RAM，无并发多线程处理。 HB-ACCT-WC VM 提供高达 260 GB/秒的内存带宽。

ACU：199-216

高级存储：支持

高级存储缓存：支持

| 大小 | vCPU | 处理器 | 内存 (GB) | 内存带宽 GB/秒 | 基本 CPU 频率（GHz） | 所有核心频率（GHz、峰值） | 单核频率（GHz、峰值） | RDMA 性能（Gb/秒） | MPI 支持 | 临时存储（GB） | 最大数据磁盘数 | 最大以太网 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | All | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。