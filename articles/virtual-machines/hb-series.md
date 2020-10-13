---
title: HB 系列
description: HB-ACCT-WC 系列 Vm 的规格。
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: f3fbc1ad3f9e7f3f3c9d2667ae9c3f566325aa51
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931693"
---
# <a name="hb-series"></a>HB 系列

HB-ACCT-WC 系列 Vm 针对内存带宽驱动的应用程序进行了优化，如流体 dynamics、显式有限元素分析和天气建模。 HB-ACCT-WC Vm 功能 60 AMD EPYC 7551 处理器核心，每个 CPU 核心 4 GB RAM，无并发多线程处理。 HB-ACCT-WC VM 提供高达 260 GB/秒的内存带宽。

HB-ACCT-WC 系列 Vm 功能 100 Gb/秒 Mellanox EDR。 这些 Vm 连接在一个非阻止 fat 树中，用于实现优化和一致的 RDMA 性能。 这些 Vm 支持自适应路由，而动态连接传输 (DCT，) 的附加到标准 RC 和 UD 传输。 这些功能增强了应用程序的性能、可伸缩性和一致性，并且强烈建议使用这些功能。

[ACU](acu.md)：199-216<br>
[高级存储](premium-storage-performance.md)：受支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 生成支持](generation-2.md)：第1代和第2代<br>
<br>

| 大小 | vCPU | 处理器 | 内存 (GiB) | 内存带宽 GB/秒 | 基本 CPU 频率 (GHz)  | 所有核心频率 (GHz，峰值)  | 单核频率 (GHz，峰值)  | RDMA 性能 (Gb/s)  | MPI 支持 | 临时存储 (GiB) | 最大数据磁盘数 | 最大以太网 Vnic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | All | 700 | 4 | 8 |

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
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告以及一些 HPC 示例和结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
