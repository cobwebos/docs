---
title: H 系列-Azure 虚拟机
description: H 系列 Vm 的规格。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/04/2020
ms.author: lahugh
ms.openlocfilehash: 7b739e8a282fdd462ee6b465371bc04a9172ab46
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493067"
---
# <a name="h-series"></a>H 系列

H 系列 Vm 针对按高 CPU 频率驱动的应用程序和每个核心要求提供较大内存的优化。 H 系列 Vm 功能8或 16 Intel 至强 E5 2667 v3 处理器核心，每个 CPU 核心最多 14 GB RAM，无超线程。 H 系列功能 56 Gb/秒，FDR 不会阻止 fat 树配置，以实现一致的 RDMA 性能。 H 系列 Vm 支持 Intel MPI 1.x 和 MS-CHAP。

ACU：290-300

高级存储：不支持

高级存储缓存：不支持

| 大小 | vCPU | 处理器 | 内存 (GB) | 内存带宽 GB/秒 | 基本 CPU 频率（GHz） | 所有核心频率（GHz、峰值） | 单核频率（GHz、峰值） | RDMA 性能（Gb/秒） | MPI 支持 | 临时存储（GB） | 最大数据磁盘数 | 最大以太网 Nic |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel 至强 E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 1.x，MS-MPI | 1000 | 32 | 2 |
| Standard_H16  | 16 | Intel 至强 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 1.x，MS-MPI | 2000 | 64 | 4 |
| Standard_H8m  | 8  | Intel 至强 E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 1.x，MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel 至强 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 1.x，MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel 至强 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 1.x，MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel 至强 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 1.x，MS-MPI | 2000 | 64 | 4 |

<sup>1</sup>对于 MPI 应用程序，专用 RDMA 后端网络是通过 FDR 无限网络启用的。

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