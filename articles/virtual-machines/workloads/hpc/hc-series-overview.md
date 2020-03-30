---
title: HC 系列 VM 概述 - Azure 虚拟机*微软文档
description: 了解 Azure 中 HC 系列 VM 大小的预览支持。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: a4cd74c9c85ee7413cde9f0fb4cf3ffb54c9b3d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906740"
---
# <a name="hc-series-virtual-machine-overview"></a>HC 系列虚拟机概述

最大化英特尔至强可扩展处理器上的 HPC 应用程序性能需要一种深思熟虑的方法来处理此新体系结构的放置。 在这里，我们概述了我们在适用于 HPC 应用程序的 Azure HC 系列 VM 上实现它。 我们将使用术语"pNUMA"来指物理 NUMA 域，使用"vNUMA"来引用虚拟化的 NUMA 域。 同样，我们将使用术语"pCore"来指物理 CPU 内核，使用"vCore"来指虚拟 CPU 内核。

从物理上讲，HC 服务器是 2 + 24 核英特尔 Xeon 白金 8168 CPU，总共 48 个物理内核。 每个 CPU 都是单个 pNUMA 域，并且对六个 DRAM 通道具有统一访问权限。 英特尔至强白金 CPU 具有比前几代人（256 KB/核心 -1 MB/内核 > 1 MB/内核）的 4 倍的 L2 缓存，同时与以前的英特尔 CPU（2.5 MB/core -> 1.375 MB/core）相比，也减少了 L3 缓存。

上述拓扑也延续到 HC 系列虚拟机管理程序配置。 为了为 Azure 虚拟机管理程序提供在不干扰 VM 的情况下运行的空间，我们保留 pCore0-1 和 24-25（即每个套接字上的前 2 个 pCore）。 然后，我们将所有剩余的内核分配 pNUMA 域到 VM。 因此，VM 将看到：

`(2 vNUMA domains) * (22 cores/vNUMA) = 44`每个 VM 的核心

VM 不知道 pCore0-1 和 24-25 没有提供给它。 因此，它公开每个 vNUMA，就好像它本机有 22 个内核一样。

英特尔至强白金、金和银 CPU 还引入了模上 2D 网状网络，用于在 CPU 插槽内外进行通信。 我们强烈建议进行流程固定，以实现最佳性能和一致性。 进程固定将在 HC 系列 VM 上工作，因为基础芯片以正样公开给来宾 VM。 要了解更多信息，请参阅[英特尔至强 SP 架构](https://bit.ly/2RCYkiE)。

下图显示了为 Azure 虚拟机管理程序和 HC 系列 VM 保留的内核的分离。

![为 Azure 虚拟机管理程序和 HC 系列 VM 保留的内核分离](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>硬件规格

| 硬件规格          | HC 系列 VM                     |
|----------------------------------|----------------------------------|
| 核心数                            | 44 （已禁用 HT）                 |
| CPU                              | 英特尔新恩铂金 8168*        |
| CPU 频率（非 AVX）          | 3.7 GHz（单核），2.7-3.4 GHz（所有内核） |
| 内存                           | 8 GB/核心（共 352 个）            |
| 本地磁盘                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR 梅拉诺克斯连接X-5* |
| 网络                          | 50 Gb 以太网（40 Gb 可用） Azure 第二代智能 NIC* |

## <a name="software-specifications"></a>软件规格

| 软件规格     | HC 系列 VM          |
|-----------------------------|-----------------------|
| 最大 MPI 作业大小            | 13200 个内核（单个 VMSS 中的 300 个 VMM，单位置组=true） |
| MPI 支持                 | MVAPICH2， OpenMPI， MPICH， 平台 MPI， 英特尔 MPI  |
| 其他框架       | 统一通信 X， 交换， PGAS |
| Azure 存储支持       | Std = 高级（最多 4 个磁盘） |
| 对 SRIOV RDMA 的操作系统支持   | CentOS/RHEL 7.6°， SLES 12 SP4+， WinServer 2016+ |
| Azure 循环云支持    | 是                         |
| Azure 批处理支持         | 是                         |

## <a name="next-steps"></a>后续步骤

* 详细了解 Azure 中[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)和[Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc)的 HPC VM 大小。

* 在 Azure 中了解有关[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)的更多详细信息。
