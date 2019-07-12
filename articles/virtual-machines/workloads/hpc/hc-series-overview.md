---
title: HC 系列 VM 概述-Azure 虚拟机 |Microsoft Docs
description: 了解如何在 Azure 中的混合连接系列 VM 大小的预览支持。
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
ms.openlocfilehash: 6cdb539846104f70dabf684925685fb062fea8af
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797559"
---
# <a name="hc-series-virtual-machine-overview"></a>HC 系列虚拟机概述

最大化 Intel Xeon 可扩展处理器上的 HPC 应用程序性能要求此新体系结构上的进程放置到谨慎周密的方法。 在这里，我们将概述我们它的 HPC 应用程序的 Azure 混合连接系列 Vm 上的实现。 我们将使用术语"pNUMA"来指代物理 NUMA 域和"vNUMA"来指代虚拟化 NUMA 域。 同样，我们将使用术语"pCore"来指代物理 CPU 核心，并且"vCore"来指代虚拟化 CPU 核心数。

以物理方式，混合连接服务器为 2 * 24 核 Intel Xeon Platinum 8168 Cpu 48 物理核心总数。 每个 CPU 是单个 pNUMA 域，并具有统一的 DRAM 六个通道的访问权限。 一个 4 倍更大的 L2 缓存比前几代 (256 KB/核心-> 1 MB/core)，同时还可降低相比以前 Intel Cpu 的 L3 缓存中的 Intel Xeon 白金 Cpu 功能 (2.5 MB/核心-> 1.375 MB/核心)。

上述拓扑将传递给 HC 系列虚拟机监控程序配置。 若要提供为 Azure 虚拟机监控程序运行而不会干扰 VM 留出空间，则我们保留 pCores 0-1 和 24-25 (即，每个套接字前 2 个 pCores)。 我们然后 pNUMA 域所有剩余内核数向 VM 分配。 因此，会看到 VM:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` 每个 VM 的内核数

VM 并不知道 pCores 0-1 和 24 25 不提供给它。 因此，它公开每个 vNUMA 好像本机具有 22 个核心。

Intel Xeon 白金、 金牌和 Silver Cpu 还到 CPU 套接字引入内部的通信以及外部的载 2D 网格网络。 我们强烈建议固定为优化性能和一致性的过程。 固定进程将运行 HC 系列 Vm 上，因为作为公开基础硅-是在来宾 VM。 若要了解详细信息，请参阅[Intel Xeon SP 体系结构](https://bit.ly/2RCYkiE)。

下图显示核心职责的划分的 Azure 虚拟机监控程序和 HC 系列 VM 保留。

![为 Azure 虚拟机监控程序和 HC 系列 VM 保留核心的职责划分](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>硬件规格

| 硬件规格          | HC 系列 VM                     |
|----------------------------------|----------------------------------|
| 核心数                            | 40 （禁用超线程）                 |
| CPU                              | Intel Xeon Platinum 8168 *        |
| CPU 频率 (非 AVX)          | 3.7 GHz （单核） 2.7 3.4 GHz （所有内核） |
| 内存                           | 8 GB/core (352 总数)            |
| 本地磁盘                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 * * |
| 网络                          | 50 Gb 以太网 (40 Gb 可用) Azure 第二个常规 SmartNIC * * * |

## <a name="software-specifications"></a>软件详细说明

| 软件详细说明     | HC 系列 VM          |
|-----------------------------|-----------------------|
| 最大 MPI 作业大小            | 4400 核心 （100 虚拟机规模集） 8800 核心 （200 虚拟机规模集） |
| MPI 支持                 | MVAPICH2、 OpenMPI、 MPICH，平台 MPI，Intel MPI  |
| 其他框架       | 统一的通信 X，libfabric，PGAS |
| Azure 存储支持       | 标准 + Premium （最大 4 个磁盘） |
| SRIOV RDMA 的 OS 支持   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+ |
| Azure CycleCloud 支持    | 是                         |
| Azure Batch 支持         | 是                         |

## <a name="next-steps"></a>后续步骤

* 了解有关的 HPC VM 大小的详细信息[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)并[Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc)在 Azure 中。

* 详细了解如何[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)在 Azure 中。
