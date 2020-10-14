---
title: 在支持 InfiniBand 的 H 系列和 N 系列 VM 上进行高性能计算 - Azure 虚拟机
description: 了解针对 HPC 优化的支持 InfiniBand 的 H 系列和 N 系列 VM 的特性和功能。
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms-subservice: hpc
ms.topic: overview
ms.date: 07/29/2020
ms.reviewer: cynthn
ms.openlocfilehash: e2c3a1559b4a6642f17a2a998e68543a519b1225
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996727"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>在支持 InfiniBand 的 H 系列和 N 系列 VM 上进行高性能计算

Azure 的支持 InfiniBand 的 H 系列和 N 系列 VM 旨在为各种实际 HPC 和 AI 工作负载提供领先的性能、消息传递接口 (MPI) 可伸缩性和成本效益。 这些高性能计算 (HPC) 优化 VM 用于解决科学和工程方面的一些计算密集程度最高的问题（如：流体动力学、地球建模、天气模拟等）。

这些文章介绍如何在 Azure 上开始使用支持 InfiniBand 的 H 系列和 N 系列 VM，以及在 VM 上开始使用 HPC 和 AI 工作负载进行优化配置来实现可伸缩性。

## <a name="features-and-capabilities"></a>特性和功能

支持 InfiniBand 的 H 系列和 N 系列 VM 旨在为 HPC 工作负载提供最佳 HPC 性能、MPI 可伸缩性和成本效益。 请参阅 [H 系列](../../sizes-hpc.md)和 [N 系列](../../sizes-gpu.md) VM，以详细了解 VM 的特性和功能。

### <a name="rdma-and-infiniband"></a>RDMA 和 InfiniBand

[支持 RDMA 的](../../sizes-hpc.md#rdma-capable-instances) [H 系列](../../sizes-hpc.md)和 [N 系列](../../sizes-gpu.md) VM 通过低延迟的高带宽 InfiniBand 网络进行通信。 此类互连上的 RDMA 功能对于提高分布式节点 HPC 和 AI 工作负载的可伸缩性和性能至关重要。 支持 InfiniBand 的 H 系列和 N 系列 VM 采用非阻塞性胖树进行连接，使用的小直径旨在实现一致的优化 RDMA 性能。
请参阅[启用 InfiniBand](enable-infiniband.md)，详细了解如何在支持 InfiniBand 的 VM 上设置 InfiniBand。

### <a name="message-passing-interface"></a>消息传递接口

支持 SR-IOV 的 H 系列和 N 系列几乎支持所有 MPI 库和版本。 部分受支持的最常用 MPI 库如下：Intel MPI、OpenMPI、MPICH、MVAPICH2、平台 MPI 以及所有远程直接内存访问 (RDMA) 谓词。
请参阅[设置 MPI](setup-mpi.md)，详细了解如何安装各种受支持的 MPI 库及其最佳配置。

## <a name="get-started"></a>入门

第一步是基于 VM 规范和 [RDMA 功能](../../sizes-hpc.md#rdma-capable-instances)选择对工作负载最佳的 [H 系列](../../sizes-hpc.md)和 [N 系列](../../sizes-gpu.md) VM 类型。
其次，通过启用 InfiniBand 来配置 VM。 可通过各种方法实现此目的，包括使用内嵌驱动程序的优化 VM 映像；有关详细信息，请参阅[针对 Linux 进行优化](configure.md)和[启用 InfiniBand](enable-infiniband.md)。
第三，对于分布式节点工作负载，选择和配置 MPI 至关重要。 有关详细信息，请参阅[设置 MPI](setup-mpi.md)。
第四，为了提高性能和可伸缩性，请按照特定于 VM 系列的指导（如 [HB 系列概述](hb-series-overview.md)和 [HC 系列概述](hc-series-overview.md)）对工作负载进行优化配置。

## <a name="next-steps"></a>后续步骤

- 了解如何[配置和优化](configure.md)支持 InfiniBand 的 [H 系列](../../sizes-hpc.md)和 [N 系列](../../sizes-gpu.md) VM。
- 查看 [HB 系列概述](hb-series-overview.md)和 [HC 系列概述](hc-series-overview.md)，以了解如何对工作负载进行优化配置以提高性能和可伸缩性。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告以及一些 HPC 示例和结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
