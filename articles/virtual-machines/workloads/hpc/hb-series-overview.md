---
title: HB-ACCT-WC 系列 VM 概述-Azure 虚拟机 |Microsoft Docs
description: 了解 Azure 中 HB-ACCT-WC 系列 VM 大小的预览支持。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "67707778"
---
# <a name="hb-series-virtual-machines-overview"></a>HB-ACCT-WC 系列虚拟机概述

最大程度地提高 AMD EPYC 上的高性能计算（HPC）应用程序性能需要一种非常接近的内存区域和进程放置。 下面概述了 AMD EPYC 体系结构，以及如何在 Azure 上实现 HPC 应用程序。 我们将使用术语 "pNUMA" 引用物理 NUMA 域，使用 "vNUMA" 来引用虚拟化 NUMA 域。

从物理上讲，HB-ACCT-WC 系列是 2 * 32 核心 EPYC 7551 Cpu，共64个物理内核。 这些64内核分为16个 pNUMA 域（每个插槽8个），每个域都是四个核心并且称为 "CPU 复杂" （或 "CCX"）。 每个 CCX 都有其自己的 L3 缓存，这是操作系统将如何看到 pNUMA/vNUMA 边界的。 相邻的一对 CCXs 共享对 HB-ACCT-WC 系列服务器中的两个物理 DRAM 通道（32 GB）的访问。

若要为 Azure 虚拟机监控程序提供空间来操作而不干扰 VM，我们保留了物理 pNUMA 域0（第一个 CCX）。 然后为 VM 分配 pNUMA 域1-15 （剩余的 CCX 单位）。 VM 将会看到：

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`每个 VM 的内核数

VM 本身并不知道未向其提供 pNUMA 0。 VM 将 pNUMA 1-15 视为 vNUMA 0-14，在 vSocket 0 上有 7 vNUMA，在 VNUMA 1 上了解 8 VSocket。 尽管这是不对称的，你的操作系统仍应启动并正常运行。 稍后在本指南中，我们将指示如何最好地在此非对称 NUMA 布局上运行 MPI 应用程序。

进程固定适用于 HB-ACCT-WC 系列 Vm，因为我们会向来宾 VM 公开基础硅。 强烈建议通过处理固定来实现最佳性能和一致性。

请参阅 LinkedIn 上的[EPYC 体系结构](https://bit.ly/2Epv3kC)和[多芯片体系](https://bit.ly/2GpQIMb)结构。 有关更多详细信息，请参阅[AMD EPYC 处理器的 HPC 优化指南](https://bit.ly/2T3AWZ9)。

下图显示了为 Azure 虚拟机监控程序和 HB-ACCT-WC 系列 VM 保留的内核的隔离。

![为 Azure 虚拟机监控程序和 HB-ACCT-WC 系列 VM 保留的内核的隔离](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>硬件规格

| HW 规范                | HB-ACCT-WC 系列 VM                     |
|----------------------------------|----------------------------------|
| 核心数                            | 60（SMT 已禁用）                |
| CPU                              | AMD EPYC 7551 *                   |
| CPU 频率（非 AVX）          | 约 2.55 GHz （单个 + 所有内核）   |
| 内存                           | 4 GB/内核（总共240）            |
| 本地磁盘                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 * * |
| 网络                          | 50 Gb 以太网（40 Gb 可用） Azure 第二代 SmartNIC * * * |

## <a name="software-specifications"></a>软件规范

| SW 规范           |HB-ACCT-WC 系列 VM           |
|-----------------------------|-----------------------|
| 最大 MPI 作业大小            | 6000个内核（100个虚拟机规模集）12000个内核（200虚拟机规模集）  |
| MPI 支持                 | MVAPICH2，OpenMPI，MPICH，平台 MPI，Intel MPI  |
| 其他框架       | 统一通信 X、libfabric、PGAS |
| Azure 存储支持       | Std + Premium （最多4个磁盘） |
| SRIOV RDMA 的操作系统支持   | CentOS/RHEL 7.6 +、SLES 12 SP4 +、WinServer 2016 +  |
| Azure CycleCloud 支持    | 是                         |
| Azure Batch 支持         | 是                         |

## <a name="next-steps"></a>后续步骤

* 详细了解适用于 Azure 中[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)和[WINDOWS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc)的 HPC VM 大小。

* 详细了解 Azure 中的[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) 。
