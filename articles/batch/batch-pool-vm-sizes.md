---
title: 选择池的 VM 大小
description: 如何选择 Azure Batch 池中计算节点的可用 VM 大小
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: seodec18
ms.openlocfilehash: 2819bb5e4000f18653e47b616a551d69ec525d2c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91271301"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>选择 Azure Batch 池中计算节点的 VM 大小

为 Azure Batch 池选择节点大小时，可以在 Azure 中提供的几乎所有 VM 大小中进行选择。 Azure 针对不同工作负荷，为 Linux 和 Windows VM 提供一系列大小。

选择 VM 大小时有几个例外和限制：

* Batch 不支持某些 VM 系列或 VM 大小。
* 某些 VM 大小受到限制，需要专门启用才能进行分配。

## <a name="supported-vm-series-and-sizes"></a>支持的 VM 系列和大小

### <a name="pools-in-virtual-machine-configuration"></a>虚拟机配置中的池

虚拟机配置中的 Batch 池支持几乎所有的 VM 大小（[Linux](../virtual-machines/linux/sizes.md)、[Windows](../virtual-machines/windows/sizes.md)）。 请参阅下表以了解有关支持的大小和限制的详细信息。

| VM 系列  | 支持的大小 |
|------------|---------|
| 基本 A | 除 Basic_A0 (A0) 之外的所有大小 |
| A | 除 Standard_A0 之外的所有大小 |
| Av2 | 所有大小 |
| B | 无 |
| DC | 无 |
| Dv2, DSv2 | 所有大小 |
| Dv3, Dsv3 | 所有大小 |
| Dav4<sup>1</sup> | 所有大小 |
| Dasv4<sup>1</sup> | 所有大小 |
| Ddv4、Ddsv4 |  所有大小 |
| Ev3, Esv3 | 除 E64is_v3 之外的所有大小 |
| Eav4<sup>1</sup> | 所有大小 |
| Easv4<sup>1</sup> | 所有大小 |
| Edv4, Edsv4 |  所有大小 |
| F, Fs | 所有大小 |
| Fsv2 | 所有大小 |
| G, Gs | 所有大小 |
| H | 所有大小 |
| HB<sup>1</sup> | 所有大小 |
| HBv2<sup>1</sup> | 所有大小 |
| HC<sup>1</sup> | 所有大小 |
| Ls | 所有大小 |
| Lsv2<sup>1</sup> | 所有大小 |
| M<sup>1</sup> | 所有大小 |
| Mv2<sup>1、2</sup> | 所有大小 |
| NC | 所有大小 |
| NCv2<sup>1</sup> | 所有大小 |
| NCv3<sup>1</sup> | 所有大小 |
| ND<sup>1</sup> | 所有大小 |
| NDv2<sup>1</sup> | 无（尚不可用） |
| NV | 所有大小 |
| NVv3<sup>1</sup> | 所有大小 |
| NVv4 | 无（尚不可用） |
| SAP HANA | 无 |

<sup>1</sup> 可以在虚拟机配置的批处理池中分配这些 VM 系列，但必须创建新的 Batch 帐户并请求特定的 [配额增加](batch-quota-limit.md#increase-a-quota)。 Batch 帐户完全支持每个 VM 系列的 vCPU 配额后，将取消此限制。

<sup>2</sup> 这些 VM 系列仅适用于第2代 vm 映像。

### <a name="using-generation-2-vm-images"></a>使用第2代 VM 映像
某些 VM 系列（如 [Mv2](../virtual-machines/mv2-series.md)）只能与 [第2代 vm 映像](../virtual-machines/generation-2.md)一起使用。 使用 ["imageReference"](/rest/api/batchservice/pool/add#imagereference) 配置的 "sku" 属性，按任何 VM 映像指定第2代 vm 映像;"sku" 字符串具有后缀，如 "-g2" 或 "-gen2"。 若要获取 Batch 支持的 VM 映像的列表（包括第2代映像），请使用 ["列出支持的映像"](/rest/api/batchservice/account/listsupportedimages) API、 [PowerShell](/powershell/module/az.batch/get-azbatchsupportedimage)或 [Azure CLI](/cli/azure/batch/pool/supported-images)。

### <a name="pools-in-cloud-service-configuration"></a>云服务配置中的池

云服务配置中的 Batch 池支持所有[云服务的 VM 大小](../cloud-services/cloud-services-sizes-specs.md)，但以下项**除外**：

| VM 系列  | 不支持的大小 |
|------------|-------------------|
| A 系列   | 超小       |
| Av2 系列 | Standard_A1_v2、Standard_A2_v2、Standard_A2m_v2 |

## <a name="size-considerations"></a>大小注意事项

* **应用程序要求** - 请考虑要在节点上运行的应用程序的特征和要求。 考虑应用程序是否是多线程的以及其消耗的内存量等因素有助于确定最合适且经济高效的节点大小。 对于多实例 [MPI 工作负荷](batch-mpi.md)或 CUDA 应用程序，请分别考虑使用专用 [HPC](../virtual-machines/sizes-hpc.md) VM 大小或[启用 GPU](../virtual-machines/sizes-gpu.md) 的 VM 大小。 （请参阅[在 Batch 池中使用支持 RDMA 或启用 GPU 的实例](batch-pool-compute-intensive-sizes.md)。）

* **每个节点的任务数** - 通常，选择节点大小时会假设一个任务要在节点上运行一次。 但是，在作业执行期间，让多个任务（因此有多个应用程序实例）在计算节点上[并行运行](batch-parallel-node-tasks.md)可能是很有利的。 在此情况下，往往会选择多核节点大小，以满足更高的并行任务执行需求。

* **不同任务的负载级别** - 池中的所有节点都是相同大小。 如果打算运行具有不同系统要求和/或负载级别的应用程序，建议使用不同的池。

* **区域可用性** - 某个 VM 系列或大小在创建 Batch 帐户的区域中可能无法使用。 若要检查大小是否可用，请参阅[可用产品（按区域）](https://azure.microsoft.com/regions/services/)。

* **配额** - Batch 帐户中的[核心配额](batch-quota-limit.md#resource-quotas)会限制可添加到 Batch 池的给定大小的节点数。 若要请求增加配额，请参阅[此文](batch-quota-limit.md#increase-a-quota)。 

* **池配置** - 通常，与云服务配置相比，在虚拟机配置中创建池时有更多 VM 大小选项。

## <a name="next-steps"></a>后续步骤

* 了解 [Batch 服务工作流和主要资源](batch-service-workflow-features.md)，例如池、节点、作业和任务。
* 有关使用计算密集型 VM 大小的信息，请参阅[在 Batch 池中使用支持 RDMA 或启用 GPU 的实例](batch-pool-compute-intensive-sizes.md)。
