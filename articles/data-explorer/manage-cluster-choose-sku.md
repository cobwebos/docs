---
title: 为 Azure 数据资源管理器群集选择正确的 VM SKU
description: 本文介绍如何为 Azure 数据资源管理器群集选择最佳 SKU 大小。
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 2d078f9715a0cfa171f0c88776a4ab78c15215a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561844"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>为 Azure 数据资源管理器群集选择正确的 VM SKU 

为某个不断变化的工作负荷创建新群集或优化群集时，Azure 数据资源管理器会提供多个虚拟机 (VM) SKU 供你选择。 提供的 VM 由我们精选，可为任何工作负荷提供最佳性价比。 

数据管理群集的大小和 VM SKU 完全由 Azure 数据资源管理器服务进行管理。 它们由引擎的 VM 大小和引入工作负荷等因素决定。 

随时可以通过[纵向扩展群集](manage-cluster-vertical-scaling.md)来更改引擎群集的 VM SKU。 最好是从适合初始方案的最小 SKU 大小开始。 请注意，使用新的 VM SKU 重新创建群集时，纵向扩展群集会导致最长 30 分钟的停机。

> [!TIP]
> 计算[保留实例 （RI）](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances)适用于 Azure 数据资源管理器群集。  

本文介绍各个 VM SKU 选项，并提供技术详细信息来帮助你做出最佳选择。

## <a name="select-a-cluster-type"></a>选择群集类型

Azure 数据资源管理器提供两种类型的群集：

* **生产**： 生产群集包含两个节点的引擎和数据管理群集，并在 Azure 数据资源管理器[SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)下运行。

* **开发/测试（无 SLA）：** 开发/测试群集具有引擎群集的单个 D11 v2 节点和数据管理群集的单个 D1 节点。 此群集类型是成本最低的配置，因为它的实例计数较小，且不收取引擎标记费用。 此群集配置不附带 SLA，因为它不提供冗余。

## <a name="sku-types"></a>SKU 类型

创建 Azure 数据资源管理器群集时，请根据计划的工作负荷选择最佳的 VM SKU。** 可从以下两个 Azure 数据资源管理器 SKU 系列中选择：

* **D v2**： D SKU 经过计算优化，有两种类型：
    * VM 本身
    * 与高级存储磁盘捆绑在一起的 VM

* **LS**： L SKU 是存储优化的。 它的 SSD 大小比价格相似的 D SKU 大得多。

下表描述了可用 SKU 类型的主要差别：
 
| 特性 | D SKU | L SKU |
|---|---|---
|**小型 SKU**|最小大小为 D11，提供两个核心|最小尺寸为 L4，带四个内核 |
|**可用性**|在所有区域可用（DS + PS 版本的可用性限制更高）|可在少数区域提供 |
|**每核心每&nbsp;GB 缓存成本**|D SKU 的成本较高，DS + PS 版本的成本较低|使用即用即付选项的最低值 |
|**预留实例 （RI） 定价**|高折扣（三年承诺&nbsp;超过 55%）|折扣更低（三年&nbsp;承诺为 20%） |  

## <a name="select-your-cluster-vm"></a>选择群集 VM 

若要选择群集 VM，请[配置纵向缩放](manage-cluster-vertical-scaling.md#configure-vertical-scaling)。 

有各种 VM SKU 选项可供选择，因此可以根据方案的性能和热缓存要求优化成本。 
* 如果需要为高查询量实现最佳性能，理想的 SKU 是计算优化版本。 
* 如果需要以较低的查询负载查询大量数据，则存储优化的 SKU 可帮助降低成本，同时仍可提供出色的性能。

由于小型 SKU 的每群集实例数受限制，因此最好是使用 RAM 较大的大型 VM。 某些对 RAM 资源需求更高的查询类型（例如使用 `joins` 的查询）需要更多的 RAM。 因此，在考虑缩放选项时，我们建议纵向扩展到更大的 SKU，而不要通过添加更多的实例进行横向扩展。

## <a name="vm-options"></a>VM 选项

下表描述了 Azure 数据资源管理器群集 VM 的技术规格：

|**名称**| **类别** | **SSD 大小** | **核心** | **Ram** | **高级存储磁盘 (1&nbsp;TB)**| **每个群集的最小实例计数** | **每个群集的最大实例计数**
|---|---|---|---|---|---|---|---
|D11 v2| 计算优化 | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1 | 8（开发/测试 SKU 除外，其计数为 1）
|D12 v2| 计算优化 | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|D13 v2| 计算优化 | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1,000
|D14 v2| 计算优化 | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1,000
|DS13 v2 + 1&nbsp;TB&nbsp;PS| 存储优化 | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1,000
|DS13 v2 + 2&nbsp;TB&nbsp;PS| 存储优化 | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 + 3&nbsp;TB&nbsp;PS| 存储优化 | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 + 4&nbsp;TB&nbsp;PS| 存储优化 | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1,000
|L4s v1| 存储优化 | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8s v1| 存储优化 | 1.3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1,000
|L16s_1| 存储优化 | 2.6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1,000

* 可以使用 Azure 数据资源管理器 [ListSkus API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet) 查看每个区域的已更新 VM SKU 列表。 
* 详细了解[各种 SKU](/azure/virtual-machines/windows/sizes)。 

## <a name="next-steps"></a>后续步骤

* 随时可以根据不同的需求，通过更改 VM SKU 来[纵向扩展或缩减](manage-cluster-vertical-scaling.md)引擎群集。 

* 可以根据不断变化的需求，[横向缩减或扩展](manage-cluster-horizontal-scaling.md)引擎群集的大小以改变容量。

