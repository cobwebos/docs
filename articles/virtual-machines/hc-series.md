---
title: HC 系列-Azure 虚拟机
description: HC 系列 Vm 的规格。
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: b73051ab7cc122f1f371ddac9c77e7243bfa0d73
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87923537"
---
# <a name="hc-series"></a>HC 系列

HC 系列 Vm 针对密集计算驱动的应用程序进行了优化，如隐式有限元素分析、分子 dynamics 和计算化学。 HC Vm 功能 44 Intel 至强白金8168处理器核心，每 CPU 内核 8 GB RAM，无超线程。 Intel 强白金平台支持 Intel 丰富的软件工具生态系统，如 Intel 数学内核库。

ACU：297-315

高级存储：支持

高级存储缓存：支持

实时迁移：不支持

内存保留更新：不支持

| 大小 | vCPU | 处理器 | 内存 (GB) | 内存带宽 GB/秒 | 基本 CPU 频率 (GHz)  | 所有核心频率 (GHz，峰值)  | 单核频率 (GHz，峰值)  | RDMA 性能 (Gb/s)  | MPI 支持 | 临时存储 (GB)  | 最大数据磁盘数 | 最大以太网 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel 强白金8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | 全部 | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

- 详细了解如何[配置 vm](./workloads/hpc/configure.md)、在[hpc 工作负荷](./workloads/hpc/overview.md)下为 Azure 设置不[允许](./workloads/hpc/enable-infiniband.md)的[设置](./workloads/hpc/setup-mpi.md)和优化 hpc 应用程序。
- 阅读有关[Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)的最新公告和一些 HPC 示例和结果。
- 有关运行 HPC 工作负荷的高级体系结构视图，请参阅[Azure 上的高性能计算 (HPC) ](/azure/architecture/topics/high-performance-computing/)。
- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
