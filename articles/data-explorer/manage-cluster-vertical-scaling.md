---
title: 管理群集垂直扩展（向上扩展），以匹配 Azure 数据资源管理器中的需求
description: 本文介绍根据不断变化的需求对 Azure 数据资源管理器群集进行纵向扩展和纵向缩减的步骤。
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 95275598febae2b6b0355a7bc3e512490dae500d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560433"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>在 Azure 数据资源管理器中管理纵向扩展（缩减）群集以适应不断变化的需求

调整好群集的大小对于 Azure 数据资源管理器的性能至关重要。 静态群集大小可能导致利用不充分或利用过度，二者都不理想。

由于无法绝对准确地预测群集的需求，更好的方法是对群集进行*缩放*，根据不断变化的需求添加或删除容量和 CPU 资源。 

有两个工作流可用于缩放 Azure 数据资源管理器群集：

* [水平缩放](manage-cluster-horizontal-scaling.md)，也称为缩减和横向扩展。
* 垂直缩放，也称为纵向扩展和缩减。

本文介绍垂直缩放工作流：

## <a name="configure-vertical-scaling"></a>配置垂直缩放

1. 在 Azure 门户中，转到 Azure 数据资源管理器群集资源。 在“设置”下选择“纵向扩展”。********

1. 在“纵向扩展”**** 窗口中，你将看到群集的可用 SKU 列表。 例如，在下图中，只有四个 SKU 可用。

    ![纵向扩展](media/manage-cluster-vertical-scaling/scale-up.png)

    SKU 被禁用是因为它们是当前的 SKU，或者在群集所在区域不可用。

1. 若要更改 SKU，请选择一个新 SKU，然后单击“选择”****。

> [!NOTE]
> * 垂直缩放过程可能需要几分钟时间，在此期间群集将被挂起。 
> * 缩减可能会损害群集性能。
> * 价格是对群集的虚拟机和 Azure 数据资源管理器服务成本的估算值。 不包括其他成本。 有关估算值，请参阅 Azure 数据资源管理器[成本估算器](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html)页；有关完整定价信息，请参阅 Azure 数据资源管理器[定价页](https://azure.microsoft.com/pricing/details/data-explorer/)。

现在已为 Azure 数据资源管理器群集配置了垂直缩放。 添加另一个水平缩放规则。 如果在解决群集缩放问题时需要帮助，请在 Azure 门户中[提交支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

## <a name="next-steps"></a>后续步骤

* [管理群集水平缩放](manage-cluster-horizontal-scaling.md)以根据指定的指标动态横向扩展实例计数。

* 通过按照本文监视资源使用情况：监视[Azure 数据资源管理器的性能、运行状况以及指标的使用](using-metrics.md)。

