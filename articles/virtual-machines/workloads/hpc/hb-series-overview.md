---
title: HB 系列 VM 概述-Azure 虚拟机 |Microsoft Docs
description: 了解如何在 Azure 中的 HB 系列 VM 大小的预览支持。
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
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707778"
---
# <a name="hb-series-virtual-machines-overview"></a>HB 系列虚拟机概述

最大化 AMD EPYC 上的高性能计算 (HPC) 应用程序性能需要谨慎周密的方法内存位置和过程的位置。 下面我们将概述 AMD EPYC 体系结构和我们的实现它的 Azure 上的 HPC 应用程序。 我们将使用术语"pNUMA"来指代物理 NUMA 域和"vNUMA"来指代虚拟化 NUMA 域。

实际上，HB 系列为 2 * 32 核 EPYC 7551 Cpu 用于 64 个物理核心总数。 这些 64 个内核都划分为 16 个 pNUMA 域 (每个插槽 8)，其中每个为四个内核，称为"CPU 复杂"（或"CCX"）。 每个 CCX 具有其自己的 L3 缓存，这是一种操作系统，将看到 pNUMA/vNUMA 边界的方式。 一对相邻 CCXs 共享对物理 DRAM (32 GB 的 DRAM HB 系列服务器中) 的两个通道的访问。

若要提供为 Azure 虚拟机监控程序运行而不会干扰 VM 留出空间，则我们保留物理 pNUMA 域 0 (第一个 CCX)。 然后，我们会为 VM 分配 pNUMA 域 1-15 （剩余 CCX 单位）。 VM 将会看到：

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` 每个 VM 的内核数

虚拟机，本身并不知道该 pNUMA 0 不提供给它。 VM 作为与 7 vNUMA 上 vSocket 1 上的 0 到 8 vSocket vNUMA vNUMA 0 至 14 日，了解 pNUMA 1-15。 虽然这是对称的您的操作系统应启动并正常运行。 稍后在本指南中，指示如何最大程度上此非对称的 NUMA 布局运行 MPI 应用程序。

固定进程将运行 HB 系列 Vm 上，因为我们公开作为基础硅-是在来宾 VM。 我们强烈建议固定为优化性能和一致性的过程。

在查看更多[AMD EPYC 体系结构](https://bit.ly/2Epv3kC)并[多芯片体系结构](https://bit.ly/2GpQIMb)LinkedIn 上。 有关详细信息，请参阅[HPC AMD EPYC 处理器的优化指南](https://bit.ly/2T3AWZ9)。

下图显示核心职责的划分的 Azure 虚拟机监控程序和 HB 系列 VM 保留。

![为 Azure 虚拟机监控程序和 HB 系列 VM 保留核心的职责划分](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>硬件规格

| 硬件规范                | HB 系列 VM                     |
|----------------------------------|----------------------------------|
| 核心数                            | 60 (SMT 禁用)                |
| CPU                              | AMD EPYC 7551 *                   |
| CPU 频率 (非 AVX)          | ~2.55 GHz （单 + 所有核心）   |
| 内存                           | 4 GB/core (240 总数)            |
| 本地磁盘                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 * * |
| 网络                          | 50 Gb 以太网 (40 Gb 可用) Azure 第二个常规 SmartNIC * * * |

## <a name="software-specifications"></a>软件详细说明

| SW 规范           |HB 系列 VM           |
|-----------------------------|-----------------------|
| 最大 MPI 作业大小            | 6000 个核心 （100 虚拟机规模集） 12000 个核心 （200 虚拟机规模集）  |
| MPI 支持                 | MVAPICH2、 OpenMPI、 MPICH，平台 MPI，Intel MPI  |
| 其他框架       | 统一的通信 X，libfabric，PGAS |
| Azure 存储支持       | 标准 + Premium （最大 4 个磁盘） |
| SRIOV RDMA 的 OS 支持   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Azure CycleCloud 支持    | 是                         |
| Azure Batch 支持         | 是                         |

## <a name="next-steps"></a>后续步骤

* 了解有关的 HPC VM 大小的详细信息[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)并[Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc)在 Azure 中。

* 详细了解如何[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)在 Azure 中。
