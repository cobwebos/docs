---
title: HC 系列 VM 概述-Azure 虚拟机 |Microsoft Docs
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
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906740"
---
# <a name="hc-series-virtual-machine-overview"></a>HC 系列虚拟机概述

最大程度地提高 Intel 强处理器上的 HPC 应用程序性能在此新体系结构上处理放置的方法非常好。 在这里，我们将其实现概述为适用于 HPC 应用程序的 Azure HC 系列 Vm。 我们将使用术语 "pNUMA" 引用物理 NUMA 域，使用 "vNUMA" 来引用虚拟化 NUMA 域。 同样，我们将使用术语 "pCore" 来表示物理 CPU 内核，使用 "vCore" 来表示虚拟化的 CPU 内核。

从物理上讲，HC 服务器是 2 * 24 核 Intel 强白金 8168 Cpu，总共48个物理内核。 每个 CPU 都是单个 pNUMA 域，并且具有对两个 DRAM 通道的统一访问。 Intel 强白金 Cpu 具有比之前代（256 KB/核心-> 1 MB/核）更大的 L2 缓存，同时减少了 L3 缓存与以前的 Intel Cpu （2.5 MB/核-> 1.375 MB/内核）的比较。

上述拓扑也会传输到 HC 系列虚拟机监控程序配置。 若要为 Azure 虚拟机监控程序提供空间，而不干扰 VM，我们保留 pCores 0-1 和24-25 （即每个插槽上的前2个 pCores）。 然后，将所有剩余核心的 pNUMA 域分配给 VM。 这样，VM 就会看到：

每个 VM 的 `(2 vNUMA domains) * (22 cores/vNUMA) = 44` 核心数

VM 没有 pCores 0-1 和24-25 的知识。 因此，它公开每个 vNUMA，就像它在本机具有22个核心一样。

Intel （r）白金、黄金和银 Cpu 还引入了一个片上的2D 网格网络，用于在 CPU 插槽内部和外部进行通信。 强烈建议通过处理固定来实现最佳性能和一致性。 进程固定适用于 HC 系列 Vm，因为底层硅按原样公开给来宾 VM。 若要了解详细信息，请参阅[Intel 强 SP 体系结构](https://bit.ly/2RCYkiE)。

下图显示了为 Azure 虚拟机监控程序和 HC 系列 VM 保留的内核的隔离。

![为 Azure 虚拟机监控程序和 HC 系列 VM 保留的内核的隔离](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>硬件规范

| 硬件规范          | HC 系列 VM                     |
|----------------------------------|----------------------------------|
| 双核                            | 44（已禁用 HT）                 |
| CPU                              | Intel 强白金 8168 *        |
| CPU 频率（非 AVX）          | 3.7 GHz （单核），2.7-3.4 GHz （所有内核） |
| 内存                           | 8 GB/内核（总共352）            |
| 本地磁盘                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 * * |
| 网络                          | 50 Gb 以太网（40 Gb 可用） Azure 第二代 SmartNIC * * * |

## <a name="software-specifications"></a>软件规范

| 软件规范     | HC 系列 VM          |
|-----------------------------|-----------------------|
| 最大 MPI 作业大小            | 13200核心（使用 singlePlacementGroup = true 的单个 VMSS 中的 300 Vm） |
| MPI 支持                 | MVAPICH2，OpenMPI，MPICH，平台 MPI，Intel MPI  |
| 其他框架       | 统一通信 X、libfabric、PGAS |
| Azure 存储支持       | Std + Premium （最多4个磁盘） |
| SRIOV RDMA 的操作系统支持   | CentOS/RHEL 7.6 +、SLES 12 SP4 +、WinServer 2016 + |
| Azure CycleCloud 支持    | 用户帐户控制                         |
| Azure Batch 支持         | 用户帐户控制                         |

## <a name="next-steps"></a>后续步骤

* 详细了解适用于 Azure 中[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)和[WINDOWS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc)的 HPC VM 大小。

* 详细了解 Azure 中的[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) 。
