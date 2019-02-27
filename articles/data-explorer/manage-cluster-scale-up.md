---
title: 根据不断变化的需求缩放 Azure 数据资源管理器群集
description: 本文介绍根据不断变化的需求对 Azure 数据资源管理器群集进行纵向扩展和横向扩展的步骤。
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: bc3f97c798f5e040908e8103c00d3f015f8c824d
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415328"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>管理群集纵向扩展以适应不断变化的需求

调整好群集的大小对于 Azure 数据资源管理器的性能至关重要。 但是，对群集的需求不能 100% 准确地进行预测。 静态群集大小可能导致利用不充分或利用过度，二者都不理想。 更好的方法是对群集进行*缩放*，根据不断变化的需求来添加或删除容量和 CPU。 有两个工作流可用于缩放、纵向扩展和横向扩展。本文介绍如何管理群集纵向扩展。

1. 导航到群集，在“设置”下选择“纵向扩展”。

    此时提供可用 SKU 的列表。 例如，下图中仅有一个可用的 SKU：D14_V2。

    ![纵向扩展](media/manage-cluster-scale-up/scale-up.png)

    D13_V2 被禁用是因为其是群集的当前 SKU。 L8 和 L16 被禁用是因为它们在群集所在的区域不可用。

1. 若要更改 SKU，请选择想要的 SKU 并按下“选择”按钮。

> [!NOTE]
> 纵向扩展过程可能需要几分钟时间，在此期间群集将被挂起。 请注意，缩减可能会损害群集性能。

现在已为 Azure 数据资源管理器群集执行了纵向扩展或横向扩展操作。 还可以执行[群集横向扩展](manage-cluster-scale-out.md)（也称为自动缩放），以根据指定的指标动态缩放。

如果在解决群集缩放问题时需要帮助，请在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)中提交支持请求。
