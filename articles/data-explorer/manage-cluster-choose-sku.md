---
title: 为 Azure 数据资源管理器群集选择正确的 VM SKU
description: 本文介绍如何为 Azure 数据资源管理器群集选择最佳 SKU 大小。
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: b0cf6eab86b0b932e44b6824305c23df01f35808
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383818"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>为 Azure 数据资源管理器群集选择正确的 VM SKU 

Azure 数据资源管理器有多个 VM Sku, 可以在创建新群集时选择, 也可以针对不断变化的工作负荷优化群集。 已仔细选择了 Vm, 以允许为任何工作负荷提供最高的成本。 

数据管理群集的大小和 VM SKU 完全由 Azure 数据资源管理器服务管理。 它由各种因素确定, 如引擎的 VM 大小和引入工作负荷。 

可以通过[扩展群集](manage-cluster-vertical-scaling.md), 随时更改引擎群集的 VM SKU。 因此, 最好从符合初始方案的最小 SKU 大小开始。 请记住, 增加群集将导致停机时间最长为30分钟, 同时将群集重新创建为新的 VM SKU。

> [!TIP]
> 计算[RI (保留实例)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances)适用于 Azure 数据资源管理器群集。  

本文介绍了不同的 VM SKU 选项, 并提供可帮助你做出最佳选择的技术详细信息。

## <a name="select-the-cluster-type"></a>选择群集类型

Azure 数据资源管理器提供两种类型的群集:

* **生产**:生产群集包含两个用于引擎和数据管理群集的节点, 并在 Azure 数据资源管理器[SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)下运行。

* **开发/测试 (无 SLA)** :开发/测试群集为引擎群集提供单个 D11_v2 节点, 并为数据管理群集提供单个 D1 节点。 此群集类型是最低的成本配置, 因为实例计数较低, 无引擎标记费用。 没有针对此群集配置的 SLA, 因为它缺少冗余性。

## <a name="sku-types"></a>SKU 类型

创建 Azure 数据资源管理器群集时, 为计划的工作负荷选择*最佳*VM SKU。 Azure 数据资源管理器有两个 SKU 系列可供选择:

* **D_V2**:D SKU 经过优化, 提供两种风格。
    * VM 本身
    * 与高级存储磁盘捆绑在一起的 VM

* **LS**:L SKU 存储优化。 与类似的定价**D** SKU 相比, 它的 SSD 大小要大得多。

下表提供了可用 SKU 类型的主要区别:
 
|**属性** | **D SKU** | **L SKU**
|---|---|---
|**小型 Sku**|最小大小为11个, 具有两个内核|最小大小为 "L4", 具有四个核心
|**可用性**|在所有区域中可用 (DS + PS 版本具有更有限的可用性)|在几个区域中提供
|**每个内核的每 GB 缓存成本**|带有 D SKU 的高版本, DS + PS 版本低|最便宜的 "即用*即付*" 选项
|**RI (预订实例) 定价**|高折扣 (超过 55% 表示三年承诺)|更低的折扣 (3 年承诺的 20%)  

## <a name="select-your-cluster-vm"></a>选择群集 VM 

若要选择群集 VM, 请[配置垂直缩放](manage-cluster-vertical-scaling.md#configure-vertical-scaling)。 

利用不同的 VM SKU 选项, 你可以针对所需方案, 为所需的性能和热缓存要求优化成本。 如果该方案需要高查询量的最佳性能, 应优化理想的 SKU。 另一方面, 如果方案需要查询负载相对较低的大量数据, 则存储优化的 SKU 会降低成本, 同时仍能提供出色的性能。

小型 Sku 的每个群集的实例数受到限制, 因此最好使用具有更大 RAM 的更大 Vm。 对于对 RAM 资源施加更多需求的某些查询类型 (例如, 使用`joins`的查询), ram 大小是必需的。 因此, 在考虑缩放选项时, 建议通过添加更多实例来扩展到更大的 SKU。

## <a name="vm-options"></a>VM 选项

下表提供了 Azure 数据资源管理器群集 Vm 的技术规格:

|**名称**| **类别** | **SSD 大小** | **核心数** | **RAM** | **高级存储磁盘 (1 TB)**| **每个群集的最小实例计数** | **每个群集的最大实例计数**
|---|---|---|---|---|---|---|---
|D11_v2| 计算优化 | 75 GB    | 2 | 14 GB | 0 | 1 | 8 (开发/测试 SKU 除外, 其中为 1)
|D12_v2| 计算优化 | 150 GB   | 4 | 28 GB | 0 | 2 | 16
|D13_v2| 计算优化 | 307 GB   | 8 | 56 GB | 0 | 2 | 1,000
|D14_v2| 计算优化 | 614 GB   | 16| 112 GB | 0 | 2 | 1,000
|DS13_v2 + 1TB PS| 优化存储 | 1 TB | 8 | 56 GB | 1 | 2 | 1,000
|DS13_v2 + 2TB PS| 优化存储 | 2 TB | 8 | 56 GB | 2 | 2 | 1,000
|DS14_v2 + 3TB PS| 优化存储 | 3 TB | 16 | 112 GB | 2 | 2 | 1,000
|DS14_v2 + 4TB PS| 优化存储 | 4 TB | 16 | 112 GB | 4 | 2 | 1,000
|L4s_v1| 优化存储 | 650 GB | 4 | 32 GB | 0 | 2 | 16
|L8s_v1| 优化存储 | 1.3 TB | 8 | 64 GB | 0 | 2 | 1,000
|L16s_1| 优化存储 | 2.6 TB | 16| 128 GB | 0 | 2 | 1,000

* 使用 Azure 数据资源管理器[LISTSKUS API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet)查看每个区域的已更新 VM SKU 列表。 
* 了解有关[不同计算 sku](/azure/virtual-machines/windows/sizes-compute)的详细信息。 

## <a name="next-steps"></a>后续步骤

* 可以通过更改 VM SKU 来实现不同需求, 随时[扩展或缩减](manage-cluster-vertical-scaling.md)引擎群集。 

* 可以放大[和缩小](manage-cluster-horizontal-scaling.md)引擎群集的大小, 以改变需求。

