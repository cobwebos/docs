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
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561844"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>为 Azure 数据资源管理器群集选择正确的 VM SKU 

当你创建新群集或针对不断变化的工作负荷优化群集时，Azure 数据资源管理器提供多个虚拟机（VM） Sku 来进行选择。 已仔细选择了 Vm，可为你的任何工作负荷提供最高的成本。 

数据管理群集的大小和 VM SKU 由 Azure 数据资源管理器服务完全管理。 它们由引擎的 VM 大小和引入工作负载等因素来确定。 

你可以随时通过[扩展群集](manage-cluster-vertical-scaling.md)来更改引擎群集的 VM SKU。 最好从符合初始方案的最小 SKU 大小开始。 请记住，增加群集将导致停机时间最长为30分钟，同时使用新的 VM SKU 重新创建群集。

> [!TIP]
> 计算[预留实例（RI）](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances)适用于 Azure 数据资源管理器群集。  

本文介绍各种 VM SKU 选项，并提供可帮助你做出最佳选择的技术详细信息。

## <a name="select-a-cluster-type"></a>选择群集类型

Azure 数据资源管理器提供两种类型的群集：

* **生产**：生产群集包含两个用于引擎和数据管理群集的节点，并且在 Azure 数据资源管理器[SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)下运行。

* **开发/测试（无 SLA）** ：开发/测试群集为引擎群集提供单个 D11 v2 节点，为数据管理群集提供单个 D1 节点。 此群集类型是最低的成本配置，因为它的实例计数低，无引擎标记费用。 此群集配置没有 SLA，因为它没有冗余性。

## <a name="sku-types"></a>SKU 类型

创建 Azure 数据资源管理器群集时，为计划的工作负荷选择*最佳*VM SKU。 可以从以下两个 Azure 数据资源管理器 SKU 系列中进行选择：

* **D v2**： d SKU 经过计算优化，分为两种风格：
    * VM 本身
    * 与高级存储磁盘捆绑在一起的 VM

* **LS**： L SKU 存储优化。 它的 SSD 大小比价格相同的 D SKU 大得多。

下表介绍了可用 SKU 类型的主要区别：
 
| Attribute | D SKU | L SKU |
|---|---|---
|**小型 Sku**|最小大小为 D11，具有两个内核|最小大小是具有四个内核的 L4 |
|**可用性**|在所有区域中可用（DS + PS 版本具有更有限的可用性）|在几个区域中提供 |
|**每个内核每个&nbsp;GB 缓存的成本**|带有 D SKU 的高版本，DS + PS 版本低|与即用即付选项最低 |
|**预订实例（RI）定价**|高折扣（超过 55&nbsp;% 表示三年承诺）|更低的折扣（3年承诺，20&nbsp;%） |  

## <a name="select-your-cluster-vm"></a>选择群集 VM 

若要选择群集 VM，请[配置垂直缩放](manage-cluster-vertical-scaling.md#configure-vertical-scaling)。 

利用各种 VM SKU 选项，你可以为方案的性能和热缓存要求优化成本。 
* 如果需要高查询量的最佳性能，应计算最佳 SKU。 
* 如果需要在较低的查询负载下查询大量数据，则存储优化的 SKU 有助于降低成本，但仍能提供出色的性能。

由于小型 Sku 的每个群集的实例数受到限制，因此最好使用具有更大 RAM 的更大 Vm。 对于对 RAM 资源施加更多需求的某些查询类型（如使用 `joins`的查询），需要更多的 RAM。 因此，在考虑缩放选项时，我们建议你将扩展到更大的 SKU，而不是通过添加更多实例进行扩展。

## <a name="vm-options"></a>VM 选项

下表介绍了 Azure 数据资源管理器群集 Vm 的技术规范：

|**名称**| **类别** | **SSD 大小** | **核心数** | **RAM** | **高级存储磁盘（1&nbsp;TB）**| **每个群集的最小实例计数** | **每个群集的最大实例计数**
|---|---|---|---|---|---|---|---
|D11 v2| 计算优化 | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1 | 8（开发/测试 SKU 除外，这是1）
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

* 可以使用 Azure 数据资源管理器[LISTSKUS API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet)查看每个区域的已更新 VM SKU 列表。 
* 了解有关[各种 sku](/azure/virtual-machines/windows/sizes)的详细信息。 

## <a name="next-steps"></a>后续步骤

* 根据不同的需要，你可以随时根据不同需求更改 VM SKU，[增加或减少](manage-cluster-vertical-scaling.md)引擎群集。 

* 您可以根据不断变化的需求，根据不断变化的需求，[扩大或](manage-cluster-horizontal-scaling.md)缩小引擎群集的大小。

