---
title: 计划和计费 - Azure 计划程序
description: 了解有关 Azure 计划程序的计划和计费
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 13a2be8c-dc14-46cc-ab7d-5075bfd4d724
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 3a8664497d3d082ec1c7f584188854991e872d50
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344302"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Azure 计划程序的计划和计费

> [!IMPORTANT]
> [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)正在替换即将停用的 Azure 计划程序。 若要计划作业，请[改用 Azure 逻辑应用](../scheduler/migrate-from-scheduler-to-logic-apps.md)。 

## <a name="job-collection-plans"></a>作业集合计划

在 Azure 计划程序中，作业集合包含作业的特定数目。 作业集合是可计费的实体，包含标准版、P10 高级版和 P20 高级版计划，如以下所述： 

| 作业集合计划 | 每个集合的最大作业数 | 最大重复次数 | 每个订阅的最大作业集合数 | 限制 | 
|:--- |:--- |:--- |:--- |:--- |
| **标准** | 每个集合 50 个作业 | 每分钟一次。 不能运行每分钟超过一次的作业。 | 每个 Azure 订阅最多可以有 100 个标准版作业集合。 | 访问计划程序完整功能集 | 
| **P10 高级** | 每个集合 50 个作业 | 每分钟一次。 不能运行每分钟超过一次的作业。 | 每个 Azure 订阅最多可以有 10,000 个 P10 高级版作业集合。 有关更多集合，请<a href="mailto:wapteams@microsoft.com">联系我们</a>。 | 访问计划程序完整功能集 |
| **P20 高级** | 每个集合 1000 个作业数 | 每分钟一次。 不能运行每分钟超过一次的作业。 | 每个 Azure 订阅最多可以有 5,000 个 P20 标准版作业集合。 有关更多集合，请<a href="mailto:wapteams@microsoft.com">联系我们</a>。 | 访问计划程序完整功能集 |
|||||| 

## <a name="pricing"></a>定价

有关定价详情，请参阅[计划程序定价](https://azure.microsoft.com/pricing/details/scheduler/)。

## <a name="upgrade-or-downgrade-plans"></a>升级或降级计划

随时可以在标准版、P10 高级版和 P20 高级版计划之间升级或降级作业集合计划。

## <a name="active-status-and-billing"></a>活动状态和计费

作业集合始终处于活动状态，除非整个 Azure 订阅由于计费问题而进入某种临时禁用状态。 尽管用户可以通过单个操作禁用某个作业集合中的所有作业，但此操作不会更改作业集合的计费状态，所以作业集合仍然计费。 空作业集合被视为活动状态，因此会被计费。

若要确保作业集合不会被计费，必须删除该作业集合。

## <a name="standard-billable-units"></a>标准计费单位

一个标准计费单位最多可以有 10 个标准版作业集合。 由于标准版作业集合中的每个集合最多可以有 50 个作业，因此一个标准计费单位允许 Azure 订阅最多包含 500 个作业，或每月最多可以执行近 2,200 万次作业。 此列表说明了如何基于各种数量的标准版作业集合来计费：

* 对于 1 到 10 个标准版作业集合，会按照一个标准计费单位计费。 

* 对于 11 到 20 个标准版作业集合，会按照两个标准计费单位计费。 

* 对于 21 到 30 个标准版作业集合，会按照三个标准计费单位计费，依次类推。

## <a name="p10-premium-billable-units"></a>P10 高级计费单位

一个 P10 高级计费单位最多可以包含 10,000 个 P10 高级版作业集合。 由于 P10 高级版作业集合中的每个集合最多可以有 50 个作业，因此一个 P10 高级计费单位允许 Azure 订阅最多有 500,000 个作业，或每月最多可以执行近 220 亿次作业。 

P10 高级版作业集合提供与标准版作业集合相同的功能，但为需要多种作业集合的应用提供折扣价，并提供更多的可伸缩性。 此列表说明了如何基于各种数量 P10 高级版作业集合来向你计费：

* 如果有 1 到 10,000 个 P10 高级版作业集合，会按照一个 P10 高级计费单位计费。 

* 如果有 10,001 到 20,000 个 P10 高级版作业集合，会按照 2 个 P10 高级计费单位计费，依此类推。

## <a name="p20-premium-billable-units"></a>P20 高级计费单位

一个 P20 高级计费单位最多可以有 5,000 个 P20 高级版作业集合。 由于 P20 高级版作业集合中的每个集合最多可以有 1,000 个作业，因此一个 P20 高级计费单位允许订阅最多有 5,000,000 个作业，或每月最多可以执行近 220 亿次作业。

P20 高级版作业集合提供的功能与 P10 高级版作业集合相同，但每个作业集合支持更大数量的作业，并且支持的作业总数比 P10 更多，从而提供更大的可伸缩性。

## <a name="plan-comparison"></a>计划比较

* 如果标准作业集合数超过 100 个（10 个标准计费单位），则最好是将所有作业集合纳入高级版计划。

* 如果有一个标准版作业集合和一个高级版作业集合，则根据一个标准计费单位和一个高级计费单位计费。

  计划程序服务根据设置为标准或高级的活动作业集合数计费。

## <a name="see-also"></a>另请参阅

* [什么是 Azure 计划程序？](scheduler-intro.md)
* [Azure 计划程序的概念、术语和实体层次结构](scheduler-concepts-terms.md)
* [Azure 计划程序的限制、默认值和错误代码](scheduler-limits-defaults-errors.md)