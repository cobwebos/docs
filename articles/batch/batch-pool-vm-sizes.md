---
title: 选择池的 VM 大小 - Azure Batch | Microsoft Docs
description: 如何选择 Azure Batch 池中计算节点的可用 VM 大小
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/01/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: f894a7438c56c3830eb62c516447195f10fc3b76
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094671"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>选择 Azure Batch 池中计算节点的 VM 大小

为 Azure Batch 池选择节点大小时，可以在 Azure 中提供的几乎所有 VM 大小中进行选择。 Azure 针对不同工作负荷，为 Linux 和 Windows VM 提供一系列大小。

选择 VM 大小时有几个例外和限制：

* Batch 不支持某些 VM 系列或 VM 大小。
* 某些 VM 大小受到限制，需要专门启用才能进行分配。

## <a name="supported-vm-series-and-sizes"></a>支持的 VM 系列和大小

### <a name="pools-in-virtual-machine-configuration"></a>虚拟机配置中的池

虚拟机配置中的 Batch 池支持几乎所有的 VM 大小（[Linux](../virtual-machines/linux/sizes.md)、[Windows](../virtual-machines/windows/sizes.md)）。 请查看下表以了解有关支持的大小和限制的详细信息。

不保证支持未列出的任何促销版或预览版 VM 大小。

| VM 系列  | 支持的大小 | Batch 帐户池分配模式<sup>1</sup> |
|------------|---------|-----------------|
| 基本 A 系列 | 除 Basic_A0 (A0) 外的所有大小 | 任意 |
| A 系列 | 除 Standard_A0 外的所有大小 | 任意 |
| Av2 系列 | 所有大小 | 任意 |
| B 系列 | 无 | 不可用 |
| DC 系列 | None | 不可用 |
| Dv2、Dsv2 系列 | 所有大小 | 任意 |
| Dv3、Dsv3 系列 | 所有大小 | 任意 |
| [内存优化大小](../virtual-machines/linux/sizes-memory.md) | 无 | 不可用 |
| Fsv2 系列 | 所有大小 | 任意 |
| H 系列 | 所有大小 | 任意 |
| Hb 系列<sup>2</sup> | 所有大小 | 任意 |
| Hc 系列<sup>2</sup> | 所有大小 | 任意 |
| Ls 系列 | 所有大小 | 任意 |
| Lsv2 系列 | 无 | 不可用 |
| M 系列 | Standard_M64ms（仅限低优先级）、Standard_M128s（仅限低优先级） | 任意 |  
| NCv2 系列<sup>2</sup> | 所有大小 | 任意 |
| NCv3 系列<sup>2</sup> | 所有大小 | 任意 |
| ND 系列<sup>2</sup> | 所有大小 | 任意 |
| NDv2 系列 | 所有大小 | “用户订阅”模式 |
| NV 系列 | 所有大小 | 任意 |
| NVv3 系列 | 无 | 不可用 |
| SAP HANA | 无 | 不可用 |

<sup>1</sup> 一些较新的 VM 系列最初会部分受支持。 在“池分配模式”设置为“用户订阅”的情况下，可以按 Batch 帐户分配这些 VM 系列。 有关 Batch 帐户配置的详细信息，请参阅[管理 Batch 帐户](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode)。 若要了解如何为“用户订阅”Batch 帐户请求部分受支持的这些 VM 系列的配额，请参阅[配额和限制](batch-quota-limit.md)。  

<sup>2</sup> 这些 VM 大小可以在虚拟机配置的 Batch 池中进行分配，但是，你必须提出特定的[配额增加](batch-quota-limit.md#increase-a-quota)请求。

### <a name="pools-in-cloud-service-configuration"></a>云服务配置中的池

云服务配置中的 Batch 池支持所有[云服务的 VM 大小](../cloud-services/cloud-services-sizes-specs.md)，但以下项**除外**：

| VM 系列  | 不支持的大小 |
|------------|-------------------|
| A 系列   | 超小       |
| Av2 系列 | Standard_A1_v2、Standard_A2_v2、Standard_A2m_v2 |

## <a name="size-considerations"></a>大小注意事项

* **应用程序要求** - 请考虑要在节点上运行的应用程序的特征和要求。 考虑应用程序是否是多线程的以及其消耗的内存量等因素有助于确定最合适且经济高效的节点大小。 对于多实例 [MPI 工作负荷](batch-mpi.md)或 CUDA 应用程序，请分别考虑使用专用 [HPC](../virtual-machines/linux/sizes-hpc.md) VM 大小或[启用 GPU](../virtual-machines/linux/sizes-gpu.md) 的 VM 大小。 （请参阅[在 Batch 池中使用支持 RDMA 或启用 GPU 的实例](batch-pool-compute-intensive-sizes.md)。）

* **每个节点的任务数** - 通常，选择节点大小时会假设一个任务要在节点上运行一次。 但是，在作业执行期间，让多个任务（因此有多个应用程序实例）在计算节点上[并行运行](batch-parallel-node-tasks.md)可能是很有利的。 在此情况下，往往会选择多核节点大小，以满足更高的并行任务执行需求。

* **不同任务的负载级别** - 池中的所有节点都是相同大小。 如果打算运行具有不同系统要求和/或负载级别的应用程序，建议使用不同的池。

* **区域可用性** - 某个 VM 系列或大小在创建 Batch 帐户的区域中可能无法使用。 若要检查大小是否可用，请参阅[可用产品（按区域）](https://azure.microsoft.com/regions/services/)。

* **配额** - Batch 帐户中的[核心配额](batch-quota-limit.md#resource-quotas)会限制可添加到 Batch 池的给定大小的节点数。 若要请求增加配额，请参阅[此文](batch-quota-limit.md#increase-a-quota)。 

* **池配置** - 通常，与云服务配置相比，在虚拟机配置中创建池时有更多 VM 大小选项。

## <a name="next-steps"></a>后续步骤

* 有关 Batch 深入概述的信息，请参阅[使用 Batch 开发大规模并行计算解决方案](batch-api-basics.md)。
* 有关使用计算密集型 VM 大小的信息，请参阅[在 Batch 池中使用支持 RDMA 或启用 GPU 的实例](batch-pool-compute-intensive-sizes.md)。
