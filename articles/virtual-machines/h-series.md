---
title: H 系列-Azure 虚拟机
description: H 系列 Vm 的规格。
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 656113df50936acf6f9a6b7e9cc8cb01a8aec2a8
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931744"
---
# <a name="h-series"></a>H 系列

H 系列 Vm 针对按高 CPU 频率驱动的应用程序和每个核心要求提供较大内存的优化。 H 系列 Vm 功能8或 16 Intel 至强 E5 2667 v3 处理器核心，每个 CPU 核心最多 14 GB RAM，无超线程。 H 系列功能 56 Gb/秒，FDR 不会阻止 fat 树配置，以实现一致的 RDMA 性能。 H 系列 Vm 目前不支持 SR-IOV，并支持 Intel MPI 1.x 和 MS-CHAP。

[ACU](acu.md)：290-300<br>
[高级存储](premium-storage-performance.md)：不支持<br>
[高级存储缓存](premium-storage-performance.md)：不支持<br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 生成支持](generation-2.md)：第1代<br>
<br>

| 大小 | vCPU | 处理器 | 内存 (GiB) | 内存带宽 GB/秒 | 基本 CPU 频率 (GHz)  | 所有核心频率 (GHz，峰值)  | 单核频率 (GHz，峰值)  | RDMA 性能 (Gb/s)  | MPI 支持 | 临时存储 (GiB) | 最大数据磁盘数 | 磁盘最大吞吐量：IOPS | 最大以太网 Vnic |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel 至强 E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 1.x，MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16  | 16 | Intel 至强 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 1.x，MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | Intel 至强 E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 1.x，MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16m | 16 | Intel 至强 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 1.x，MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel 至强 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 1.x，MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel 至强 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 1.x，MS-MPI | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup> 对于 MPI 应用程序，专用 RDMA 后端网络是通过 FDR 无限网络启用的。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

> [!NOTE]
> 在 [支持 RDMA 的 vm](sizes-hpc.md#rdma-capable-instances)之间，H 系列不启用 sr-iov。 因此，支持的 [Vm 映像](./workloads/hpc/configure.md#vm-images)、未受支持的 [驱动程序](./workloads/hpc/enable-infiniband.md) 要求和受支持的 [MPI 库](./workloads/hpc/setup-mpi.md) 不同于启用 sr-iov 的 vm。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

- 详细了解如何在[HPC 工作负荷](./workloads/hpc/overview.md)中[配置 Vm](./workloads/hpc/configure.md)、[启用无限](./workloads/hpc/enable-infiniband.md)，为 Azure[设置 MPI](./workloads/hpc/setup-mpi.md)和优化 hpc 应用程序。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告以及一些 HPC 示例和结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
