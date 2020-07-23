---
title: 在 H 系列 VM 上进行高性能计算 - Azure 虚拟机
description: 了解针对 HPC 优化的 H 系列 VM 的特性和功能。
author: vermagit
ms.author: amverma
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.openlocfilehash: b3d5d003db89a11a013c3236a3afbe03ffe68557
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "76271021"
---
# <a name="high-performance-computing-on-h-series-vms"></a>在 H 系列 VM 上进行的高性能计算

使用 HB 系列和 HC 系列 VM 上的高性能计算 (HPC)，可以为 Azure 上的任何 VM 实现最优化的 HPC 性能。 HPC 优化的 VM 用于解决某些最难的数学问题，例如流体动力学、石油和天然气模拟以及天气建模。

本文介绍 HB 系列和 HC 系列 VM 的一些重要功能、为何这些 VM 在 HPC 方案中性能良好，以及如何入门。

## <a name="features-and-capabilities"></a>特性和功能

HB 系列和 HC 系列 VM 旨在提供最佳 HPC 性能、消息传递接口 (MPI) 可伸缩性，以及适用于 HPC 工作负荷的成本效率。

### <a name="message-passing-interface"></a>消息传递接口

HB 系列和 HC 系列支持几乎所有 MPI 类型和版本。 部分受支持的最常用 MPI 类型如下：OpenMPI、MVAPICH2、平台 MPI、Intel MPI 以及所有远程直接内存访问 (RDMA) 谓词。 有关详细信息，请参阅[设置适用于 HPC 的消息传递接口](setup-mpi.md)。

### <a name="rdma-and-infiniband"></a>RDMA 和 InfiniBand

在 HB 系列和 HC 系列 VM 上，RDMA 接口是标准接口。 支持 RDMA 的实例在 InfiniBand 网络上通信，以适用于 HB 系列和 HC 系列虚拟机的增强数据速率 (EDR) 运行。 支持 RDMA 的实例可以提高某些 MPI 应用程序的可伸缩性和性能。

支持 HB 系列和 HC 系列 VM 的 InfiniBand 配置是非阻塞性胖树，使用的小直径旨在实现一致的 RDMA 性能。

请参阅[启用 InfiniBand](enable-infiniband.md)，详细了解如何在 HB 系列或 HC 系列 VM 上设置 InfiniBand。

## <a name="get-started"></a>入门

首先，决定要使用哪个 H 系列 VM。 若要详细了解 HPC 优化的 VM，请参阅 [HB 系列概述](hb-series-overview.md)和 [HC 系列概述](hc-series-overview.md)。 如需规格，请参阅[高性能计算 VM 大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)。

为应用程序选择并创建 VM 以后，需启用 InfiniBand 来配置它。 若要了解如何在 Windows 和 Linux VM 上启用 InfiniBand，请参阅[启用 InfiniBand](enable-infiniband.md)。

HPC 工作负荷的关键组件是 MPI。 HB 系列和 HC 系列支持几乎所有 MPI 类型和版本。 有关详细信息，请参阅[设置适用于 HPC 的消息传递接口](setup-mpi.md)。

选择 VM 系列并设置 Infiniband 和 MPI 以后，即可开始构建 HPC 工作负荷。

## <a name="next-steps"></a>后续步骤

- 查看 [HB 系列概述](hb-series-overview.md)和 [HC 系列概述](hc-series-overview.md)，了解关键差异和规格。

- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)。
