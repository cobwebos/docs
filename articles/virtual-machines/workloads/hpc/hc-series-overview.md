---
title: HC 系列 VM 概述-Azure 虚拟机 |Microsoft Docs
description: 了解 Azure 中 HC 系列 VM 大小的预览支持。
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 97e0ae9ea4914cda5d92f5180199a740c02ecdf4
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91994362"
---
# <a name="hc-series-virtual-machine-overview"></a>HC 系列虚拟机概述

最大程度地提高 Intel 强处理器上的 HPC 应用程序性能在此新体系结构上处理放置的方法非常好。 在这里，我们将其实现概述为适用于 HPC 应用程序的 Azure HC 系列 Vm。 我们将使用术语 "pNUMA" 引用物理 NUMA 域，使用 "vNUMA" 来引用虚拟化 NUMA 域。 同样，我们将使用术语 "pCore" 来表示物理 CPU 内核，使用 "vCore" 来表示虚拟化的 CPU 内核。

从物理上讲， [HC 系列](../../hc-series.md) 服务器是 2 * 24 核 Intel 强白金 8168 cpu，总共48个物理内核。 每个 CPU 都是单个 pNUMA 域，并且具有对两个 DRAM 通道的统一访问。 Intel 强白金 Cpu 比之前代 (256 KB/核心-> 1 MB/核心) 多4倍的 L2 缓存，同时还会将 L3 缓存与以前的 Intel Cpu 相比 (2.5 MB/内核 > 1.375 MB/核心) 。

上述拓扑也会传输到 HC 系列虚拟机监控程序配置。 若要为 Azure 虚拟机监控程序提供空间以在不干扰 VM 的情况下运行，我们保留 pCores 0-1 和 24-25 (即每个套接字上的前 2 pCores) 。 然后，将所有剩余核心的 pNUMA 域分配给 VM。 这样，VM 就会看到：

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` 每个 VM 的内核数

VM 没有 pCores 0-1 和24-25 的知识。 因此，它公开每个 vNUMA，就像它在本机具有22个核心一样。

Intel （r）白金、黄金和银 Cpu 还引入了一个片上的2D 网格网络，用于在 CPU 插槽内部和外部进行通信。 强烈建议通过处理固定来实现最佳性能和一致性。 进程固定适用于 HC 系列 Vm，因为底层硅按原样公开给来宾 VM。

下图显示了为 Azure 虚拟机监控程序和 HC 系列 VM 保留的内核的隔离。

![为 Azure 虚拟机监控程序和 HC 系列 VM 保留的内核的隔离](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>硬件规格

| 硬件规范          | HC 系列 VM                     |
|----------------------------------|----------------------------------|
| 核心数                            | 44 (已禁用 HT)                  |
| CPU                              | Intel 强白金8168         |
|  (非 AVX) 的 CPU 频率          | 3.7 GHz (单核) ，2.7-3.4 GHz (所有核心)  |
| 内存                           | 8 GB/核心 (352 总)             |
| 本地磁盘                       | 700 GB SSD                       |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5   |
| 网络                          | 50 gb 以太网 (40 Gb 可用) Azure 第二代 SmartNIC    |

## <a name="software-specifications"></a>软件规范

| 软件规范     |HC 系列 VM           |
|-----------------------------|-----------------------|
| 最大 MPI 作业大小            | 13200核心 (使用 singlePlacementGroup = true 的单个虚拟机规模集中的 300 Vm)   |
| MPI 支持                 | HPC-X，Intel MPI，OpenMPI，MVAPICH2，MPICH，平台 MPI  |
| 其他框架       | 统一通信 X、libfabric、PGAS |
| Azure 存储支持       | 标准磁盘和高级磁盘 (最多4个磁盘)  |
| SRIOV RDMA 的操作系统支持   | CentOS/RHEL 7.6 +、SLES 12 SP4 +、WinServer 2016 +  |
| Orchestrator 支持        | CycleCloud，Batch  |

## <a name="next-steps"></a>后续步骤

- 了解有关 [Intel 强 SP 体系结构](https://bit.ly/2RCYkiE)的详细信息。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告以及一些 HPC 示例和结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
