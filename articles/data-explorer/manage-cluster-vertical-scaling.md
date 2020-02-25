---
title: 管理群集垂直缩放（向上缩放），以匹配 Azure 数据资源管理器中的需求
description: 本文介绍了根据不断变化的需求扩展和缩减 Azure 数据资源管理器群集的步骤。
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 95275598febae2b6b0355a7bc3e512490dae500d
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560433"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>管理 Azure 数据资源管理器中的群集垂直缩放（扩大）以适应不断变化的需求

调整好群集的大小对于 Azure 数据资源管理器的性能至关重要。 静态群集大小可能导致利用不充分或利用过度，二者都不理想。

由于群集上的需求无法通过绝对准确性预测，更好的方法是*缩放*群集，添加和删除具有更改需求的容量和 CPU 资源。 

有两个用于缩放 Azure 数据资源管理器群集的工作流：

* [水平缩放](manage-cluster-horizontal-scaling.md)，也称为放大和缩小。
* 垂直缩放，也称为向上和向下缩放。

本文介绍垂直缩放工作流：

## <a name="configure-vertical-scaling"></a>配置垂直缩放

1. 在 Azure 门户中转到 Azure 数据资源管理器群集资源。 在 "**设置**" 下，选择 "**纵向扩展**"。

1. 在 "**向上扩展**" 窗口中，你将看到群集的可用 sku 列表。 例如，在下图中，只有四个 Sku 可用。

    ![纵向扩展](media/manage-cluster-vertical-scaling/scale-up.png)

    Sku 处于禁用状态，因为它们是当前 SKU，或在群集所在的区域中不可用。

1. 若要更改 SKU，请选择新的 SKU，并单击 "**选择**"。

> [!NOTE]
> * 垂直缩放过程可能需要几分钟的时间，在此期间，你的群集将被挂起。 
> * 缩小会损害群集性能。
> * 价格是群集虚拟机和 Azure 数据资源管理器服务成本的估计值。 不包括其他成本。 有关完整定价信息，请参阅 Azure 数据资源管理器[cost 估计器](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html)页面获取估计和 azure 数据资源管理器[定价页](https://azure.microsoft.com/pricing/details/data-explorer/)。

你现在已为 Azure 数据资源管理器群集配置了垂直缩放。 添加另一个用于水平缩放的规则。 如果需要有关群集缩放问题的帮助，请在 Azure 门户中[打开支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

## <a name="next-steps"></a>后续步骤

* [管理群集水平缩放](manage-cluster-horizontal-scaling.md)，以根据你指定的指标动态地横向扩展实例计数。

* 按照本文所述监视资源使用情况：[监视 Azure 数据资源管理器性能、运行状况和使用指标](using-metrics.md)。

