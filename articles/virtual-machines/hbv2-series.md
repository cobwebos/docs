---
title: HBv2 系列-Azure 虚拟机
description: HBv2 系列 Vm 的规格。
author: vermagit
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: b7655f4cb13e80d6262c3d4871c1ab69f4a6a051
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905461"
---
# <a name="hbv2-series"></a>HBv2 系列

HBv2 系列 Vm 针对内存带宽驱动的应用程序进行了优化，例如流体 dynamics、有限元素分析和容器模拟。 HBv2 Vm 功能 120 AMD EPYC 7742 处理器核心，每个 CPU 核心 4 GB RAM，无并发多线程处理。 每个 HBv2 VM 提供高达 340 GB/秒的内存带宽和最多4个兆次的 FP64 计算。

高级存储：支持

实时迁移：不支持

内存保留更新：不支持

| 大小 | vCPU | 处理器 | 内存 (GB) | 内存带宽 GB/秒 | 基本 CPU 频率 (GHz)  | 所有核心频率 (GHz，峰值)  | 单核频率 (GHz，峰值)  | RDMA 性能 (Gb/s)  | MPI 支持 | 临时存储 (GB)  | 最大数据磁盘数 | 最大以太网 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | 全部 | 480 + 960 | 8 | 1 |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

- 详细了解如何在[HPC 工作负荷](./workloads/hpc/overview.md)中[配置 Vm](./workloads/hpc/configure.md)、[启用无限](./workloads/hpc/enable-infiniband.md)，为 Azure[设置 MPI](./workloads/hpc/setup-mpi.md)和优化 hpc 应用程序。
- 阅读有关[Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)的最新公告和一些 HPC 示例和结果。
- 有关运行 HPC 工作负荷的更高层次结构视图，请参阅[Azure 上的高性能计算 (HPC) ](/azure/architecture/topics/high-performance-computing/)。
- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
