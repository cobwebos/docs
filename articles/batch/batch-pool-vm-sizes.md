---
title: 选择池的 VM 大小 - Azure Batch | Microsoft Docs
description: 如何选择 Azure Batch 池中计算节点的可用 VM 大小
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c18190ec5e5d079d51630a976681717a78a46e00
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087044"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>选择 Azure Batch 池中计算节点的 VM 大小

为 Azure Batch 池选择节点大小时，可以在 Azure 中提供的几乎所有 VM 大小中进行选择。 Azure 针对不同工作负荷，为 Linux 和 Windows VM 提供一系列大小。

选择 VM 大小时有几个例外和限制：

* 批处理中不支持某些 VM 系列或 VM 大小。
* 某些 VM 大小受到限制，需要专门启用才能进行分配。

## <a name="supported-vm-series-and-sizes"></a>支持的 VM 系列和大小

### <a name="pools-in-virtual-machine-configuration"></a>虚拟机配置中的池

虚拟机配置中的 Batch 池几乎支持所有 VM 大小（[Linux](../virtual-machines/linux/sizes.md)、 [Windows](../virtual-machines/windows/sizes.md)）。 请参阅下表以了解有关支持的大小和限制的详细信息。

| VM 系列  | 支持的大小 |
|------------|---------|
| 基本 A | 除 Basic_A0 （A0）*以外*的所有大小 |
| A | 除 Standard_A0*以外*的所有大小 |
| Av2 | 所有大小 |
| B | 无 |
| DC | 无 |
| Dv2、DSv2 | 所有大小 |
| Dv3、Dsv3 | 所有大小 |
| Dav4, Dasv4 | 无-尚不可用 |
| Ev3、Esv3 | 除 E64is_v3 和 E64i_v3 之外的所有大小 |
| Eav4, Easv4 | 无-尚不可用 |
| F、Fs | 所有大小 |
| Fsv2 | 所有大小 |
| G，Gs | 所有大小 |
| H | 所有大小 |
| HB-ACCT-WC<sup>1</sup> | 所有大小 |
| HBv2<sup>1</sup> | 所有大小 |
| HC<sup>1</sup> | 所有大小 |
| LS | 所有大小 |
| Lsv2 | 无-尚不可用 |
| M<sup>1</sup> | 除 M64-16ms、M64m、M128、M128m 之外的所有大小 |
| Mv2 | 无-尚不可用 |
| NC | 所有大小 |
| NCv2<sup>1</sup> | 所有大小 |
| NCv3<sup>1</sup> | 所有大小 |
| ND<sup>1</sup> | 所有大小 |
| NDv2<sup>1</sup> | 无-尚不可用 |
| NV | 所有大小 |
| NVv3<sup>1</sup> | 所有大小 |
| NVv4 | 无 |
| SAP HANA | 无 |

<sup>1</sup>可以在虚拟机配置的批处理池中分配这些 VM 大小，但必须创建新的 Batch 帐户并请求特定的[配额增加](batch-quota-limit.md#increase-a-quota)。 当批处理帐户完全支持每个 VM 系列的 vCPU 配额时，将删除此限制。

### <a name="pools-in-cloud-service-configuration"></a>云服务配置中的池

云服务配置中的 Batch 池支持[云服务的所有 VM 大小](../cloud-services/cloud-services-sizes-specs.md) **，如下**所示：

| VM 系列  | 不支持的大小 |
|------------|-------------------|
| A 系列   | 特小型       |
| Av2 系列 | Standard_A1_v2、Standard_A2_v2、Standard_A2m_v2 |

## <a name="size-considerations"></a>大小注意事项

* **应用程序要求** - 请考虑要在节点上运行的应用程序的特征和要求。 考虑应用程序是否是多线程的以及其消耗的内存量等因素有助于确定最合适且经济高效的节点大小。 对于多实例 [MPI 工作负荷](batch-mpi.md)或 CUDA 应用程序，请分别考虑使用专用 [HPC](../virtual-machines/linux/sizes-hpc.md) VM 大小或[启用 GPU](../virtual-machines/linux/sizes-gpu.md) 的 VM 大小。 （请参阅[在 Batch 池中使用支持 RDMA 或启用 GPU 的实例](batch-pool-compute-intensive-sizes.md)。）

* **每个节点的任务数** - 通常，选择节点大小时会假设一个任务要在节点上运行一次。 但是，在作业执行期间，让多个任务（因此有多个应用程序实例）在计算节点上[并行运行](batch-parallel-node-tasks.md)可能是很有利的。 在此情况下，往往会选择多核节点大小，以满足更高的并行任务执行需求。

* **不同任务的负载级别** - 池中的所有节点都是相同大小。 如果打算运行具有不同系统要求和/或负载级别的应用程序，建议使用不同的池。

* **区域可用性**-VM 系列或大小可能不适用于创建 Batch 帐户的区域。 若要检查大小是否可用，请参阅[可用产品（按区域）](https://azure.microsoft.com/regions/services/)。

* **配额** - Batch 帐户中的[核心配额](batch-quota-limit.md#resource-quotas)会限制可添加到 Batch 池的给定大小的节点数。 若要请求增加配额，请参阅[此文](batch-quota-limit.md#increase-a-quota)。 

* **池配置** - 通常，与云服务配置相比，在虚拟机配置中创建池时有更多 VM 大小选项。

## <a name="next-steps"></a>后续步骤

* 有关 Batch 深入概述的信息，请参阅[使用 Batch 开发大规模并行计算解决方案](batch-api-basics.md)。
* 有关使用计算密集型 VM 大小的信息，请参阅[在 Batch 池中使用支持 RDMA 或启用 GPU 的实例](batch-pool-compute-intensive-sizes.md)。
