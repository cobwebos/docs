---
title: HB 系列 VM 概述 - Azure 虚拟机 |微软文档
description: 了解 Azure 中 HB 系列 VM 大小的预览支持。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: 62e4d3dbd7357f8c98df3307c1c8fe52cbed1c5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707778"
---
# <a name="hb-series-virtual-machines-overview"></a>HB 系列虚拟机概述

在 AMD EPYC 上最大化高性能计算 （HPC） 应用程序性能需要深思熟虑的内存位置和过程放置方法。 下面我们概述了 AMD EPYC 体系结构以及我们在 Azure 上为 HPC 应用程序实现该体系结构。 我们将使用术语"pNUMA"来指物理 NUMA 域，使用"vNUMA"来引用虚拟化的 NUMA 域。

从物理上讲，HB 系列是 2 + 32 核 EPYC 7551 CPU，共 64 个物理内核。 这 64 个内核分为 16 个 pNUMA 域（每个套接字 8 个），每个域是 4 个内核，称为"CPU 复合体"（或"CCX"）。 每个 CCX 都有自己的 L3 缓存，这是操作系统如何看到 pNUMA/vNUMA 边界。 一对相邻的 CCX 共享对物理 DRAM 的两个通道（HB 系列服务器中 32 GB 的 DRAM）的访问。

为了为 Azure 虚拟机管理程序提供在不干扰 VM 的情况下运行的空间，我们保留物理 pNUMA 域 0（第一个 CCX）。 然后，我们为 VM 分配 pNUMA 域 1-15（剩余的 CCX 单元）。 VM 将看到：

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`每个 VM 的核心

VM 本身不知道 pNUMA 0 没有给出它。 VM 将 pNUMA 1-15 理解为 vNUMA 0-14，在 vSocket 0 上使用 7 vNUMA，在 vSocket 1 上使用 8 个 vNUMA。 虽然这是不对称的，但操作系统应启动并正常运行。 在本指南的后面部分，我们指导如何最好地运行 MPI 应用程序在此不对称的 NUMA 布局上。

进程固定将在 HB 系列 VM 上工作，因为我们将基础芯片作为正样公开给来宾 VM。 我们强烈建议进行流程固定，以实现最佳性能和一致性。

有关[AMD EPYC 架构](https://bit.ly/2Epv3kC)和[多芯片架构](https://bit.ly/2GpQIMb)LinkedIn的更多了解。 有关详细信息，请参阅[AMD EPYC 处理器的 HPC 调谐指南](https://bit.ly/2T3AWZ9)。

下图显示了为 Azure 虚拟机管理程序和 HB 系列 VM 保留的内核的分离。

![为 Azure 虚拟机管理程序和 HB 系列 VM 保留的内核分离](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>硬件规格

| HW 规格                | HB 系列 VM                     |
|----------------------------------|----------------------------------|
| 核心数                            | 60 （SMT 禁用）                |
| CPU                              | AMD EPYC 7551*                   |
| CPU 频率（非 AVX）          | ±2.55 GHz（单芯+所有内核）   |
| 内存                           | 4 GB/核心（共 240 个）            |
| 本地磁盘                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR 梅拉诺克斯连接X-5* |
| 网络                          | 50 Gb 以太网（40 Gb 可用） Azure 第二代智能 NIC* |

## <a name="software-specifications"></a>软件规格

| 软件规格           |HB 系列 VM           |
|-----------------------------|-----------------------|
| 最大 MPI 作业大小            | 6000 个内核（100 个虚拟机规模集） 12000 个内核（200 个虚拟机规模集）  |
| MPI 支持                 | MVAPICH2， OpenMPI， MPICH， 平台 MPI， 英特尔 MPI  |
| 其他框架       | 统一通信 X， 交换， PGAS |
| Azure 存储支持       | Std = 高级（最多 4 个磁盘） |
| 对 SRIOV RDMA 的操作系统支持   | CentOS/RHEL 7.6°， SLES 12 SP4+， WinServer 2016+  |
| Azure 循环云支持    | 是                         |
| Azure 批处理支持         | 是                         |

## <a name="next-steps"></a>后续步骤

* 详细了解 Azure 中[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)和[Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc)的 HPC VM 大小。

* 在 Azure 中了解有关[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)的更多详细信息。
