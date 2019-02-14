---
title: 根据不断变化的需求缩放 Azure 数据资源管理器群集
description: 本文介绍如何根据不断变化的需求采用相关步骤对 Azure 数据资源管理器群集进行横向缩放。
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 213a49d87d5e9f801bb17604a322c231a4e3dee2
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735310"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>根据不断变化的需求管理群集纵向扩展

调整好群集的大小对于 Azure 数据资源管理器的性能至关重要。 但是，对群集的需求不能 100% 准确地进行预测。 静态群集大小可能导致利用不充分或利用过度，二者都不理想。 更好的方法是对群集进行*缩放*，根据不断变化的需求来添加或删除容量和 CPU。 本文介绍如何管理群集纵向扩展。

导航到群集，在“设置”下选择“纵向扩展”。

系统随后会提供可用 SKU 的列表。 可从已启用的卡列表中进行选择。 对于下图中的实例，仅可从 D14_vs 中选择一个 SKU。

![纵向扩展](media/manage-cluster-scale-up/scale-up.png)

D13_v2 被禁用是因为这是群集的当前 SKU。 L8 和 L16 被禁用是因为它们在群集所在的区域不可用。

若要更改 SKU，只需单击想要使用的 SKU，然后单击“选择”按钮。

[!NOTE] 纵向扩展过程可能需要几分钟时间，在此期间群集将被挂起。 请注意，缩减可能会损害群集性能。

如果在解决群集缩放问题时需要帮助，请在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)中提交支持请求。