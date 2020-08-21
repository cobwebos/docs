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
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 7c66af5184c4a943fd4b3185a87623112fe0d954
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691235"
---
# <a name="hb-series-virtual-machines-overview"></a>HB-ACCT-WC 系列虚拟机概述

最大程度提高高性能计算 (HPC) 应用程序性能 下面概述了 AMD EPYC 体系结构，以及如何在 Azure 上实现 HPC 应用程序。 我们将使用术语 "pNUMA" 引用物理 NUMA 域，使用 "vNUMA" 来引用虚拟化 NUMA 域。

从物理上讲， [hb-acct-wc 系列](../../hb-series.md) 服务器是 2 * 32 核心 EPYC 7551 cpu，总共64个物理内核。 这些64核心分为16个 pNUMA 域 (每套接字) 8 个，其中每个都是四个核心，称为 "CPU 复杂" (或 "CCX" ) 。 每个 CCX 都有其自己的 L3 缓存，这是操作系统将如何看到 pNUMA/vNUMA 边界的。 一对相邻的 CCXs 共享对 hb-acct-wc 系列服务器) 中的两个 (通道，即系列服务器的物理 DRAM 32 GB。

若要为 Azure 虚拟机监控程序提供空间来操作而不干扰 VM，我们保留了物理 pNUMA 域 0 (第一个 CCX) 。 然后，将 pNUMA 域 1-15 (VM 的剩余 CCX 单位) 。 VM 将会看到：

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` 每个 VM 的内核数

VM 本身并不知道未向其提供 pNUMA 0。 VM 将 pNUMA 1-15 视为 vNUMA 0-14，在 vSocket 0 上有 7 vNUMA，在 VNUMA 1 上了解 8 VSocket。 尽管这是不对称的，你的操作系统仍应启动并正常运行。 稍后在本指南中，我们将指示如何最好地在此非对称 NUMA 布局上运行 MPI 应用程序。

进程固定适用于 HB-ACCT-WC 系列 Vm，因为我们会向来宾 VM 公开基础硅。 强烈建议通过处理固定来实现最佳性能和一致性。

下图显示了为 Azure 虚拟机监控程序和 HB-ACCT-WC 系列 VM 保留的内核的隔离。

![为 Azure 虚拟机监控程序和 HB-ACCT-WC 系列 VM 保留的内核的隔离](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>硬件规格

| 硬件规范                | HB-ACCT-WC 系列 VM                     |
|----------------------------------|----------------------------------|
| 核心数                            | 60 (SMT 禁用)                 |
| CPU                              | AMD EPYC 7551                    |
|  (非 AVX) 的 CPU 频率          | ~ 2.55 GHz (单个 + 所有核心)    |
| 内存                           | 4 GB/核心 (240 GB 总)          |
| 本地磁盘                       | 700 GB SSD                       |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 |
| Network (网络)                          | 50 gb 以太网 (40 Gb 可用) Azure 第二代 SmartNIC |

## <a name="software-specifications"></a>软件规范

| 软件规范           |HB-ACCT-WC 系列 VM           |
|-----------------------------|-----------------------|
| 最大 MPI 作业大小            | 18000核心 (使用 singlePlacementGroup = true 的单个虚拟机规模集中的 300 Vm)   |
| MPI 支持                 | HPC-X，Intel MPI，OpenMPI，MVAPICH2，MPICH，平台 MPI  |
| 其他框架       | 统一通信 X、libfabric、PGAS |
| Azure 存储支持       | 标准磁盘和高级磁盘 (最多4个磁盘)  |
| SRIOV RDMA 的操作系统支持   | CentOS/RHEL 7.6 +、SLES 12 SP4 +、WinServer 2016 +  |
| Orchestrator 支持        | CycleCloud，Batch  |

## <a name="next-steps"></a>后续步骤

- 了解有关 [AMD EPYC 体系结构](https://bit.ly/2Epv3kC) 和 [多芯片体系](https://bit.ly/2GpQIMb)结构的详细信息。 有关更多详细信息，请参阅 [AMD EPYC 处理器的 HPC 优化指南](https://bit.ly/2T3AWZ9)。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告以及一些 HPC 示例和结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
