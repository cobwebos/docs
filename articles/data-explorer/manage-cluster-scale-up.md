---
title: 根据不断变化的需求缩放 Azure 数据资源管理器群集
description: 本文介绍纵向扩展和缩减根据不断变化的需求的 Azure 数据资源管理器群集的步骤。
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: a74c529fc3543d5cbdcf009a5b7736309e15569e
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961697"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>管理群集纵向扩展以适应不断变化的需求

调整好群集的大小对于 Azure 数据资源管理器的性能至关重要。 但是，需在群集上的不能预测绝对精确。 静态群集大小可能会导致过度或使用，这两者都不是理想之选。

更好的方法是向*规模*群集，添加和删除容量和 CPU 资源与不断变化的需求。 有两个工作流进行缩放： 纵向扩展和横向扩展。本文介绍如何管理群集向上扩展。

1. 请转到你的群集。 下**设置**，选择**纵向扩展**。

    要显示可用 Sku 的列表。 例如，在下图中，只需四个 Sku 为可用。

    ![纵向扩展](media/manage-cluster-scale-up/scale-up.png)

    Sku 被禁用，因为它们是当前的 SKU，或双方无法在群集所在的区域中。

1. 若要更改你的 SKU，选择您想和选择的 SKU**选择**按钮。

> [!NOTE]
> 向上缩放过程可能需要几分钟，并且在该时间段会挂起你的群集。 请注意，缩减可能会损害群集性能。

现在已完成在 Azure 数据资源管理器群集的纵向扩展或缩减操作。 此外可以[管理群集横向扩展](manage-cluster-scale-out.md)动态扩展实例数基于你指定的度量值的扩展。

如果您需要使用群集缩放问题的帮助[建立支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)在 Azure 门户中。
