---
title: HB 系列
description: HB-ACCT-WC 系列 Vm 的规格。
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: e735b389b5e223e558736f5eaa3c393f8bc29004
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926257"
---
# <a name="hb-series"></a>HB 系列

HB-ACCT-WC 系列 Vm 针对内存带宽驱动的应用程序进行了优化，如流体 dynamics、显式有限元素分析和天气建模。 HB-ACCT-WC Vm 功能 60 AMD EPYC 7551 处理器核心，每个 CPU 核心 4 GB RAM，无并发多线程处理。 HB-ACCT-WC VM 提供高达 260 GB/秒的内存带宽。

ACU：199-216

高级存储：支持

高级存储缓存：支持

实时迁移：不支持

内存保留更新：不支持

| 大小 | vCPU | 处理器 | 内存 (GB) | 内存带宽 GB/秒 | 基本 CPU 频率 (GHz)  | 所有核心频率 (GHz，峰值)  | 单核频率 (GHz，峰值)  | RDMA 性能 (Gb/s)  | MPI 支持 | 临时存储 (GB)  | 最大数据磁盘数 | 最大以太网 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | 全部 | 700 | 4 | 1 |

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
- 有关运行 HPC 工作负荷的更高层次结构视图，请参阅[Azure 上的高性能计算 (HPC) ](/azure/architecture/topics/high-performance-computing/)。
- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
