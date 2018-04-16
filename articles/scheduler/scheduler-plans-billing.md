---
title: Azure 计划程序中的计划和计费方式
description: Azure 计划程序中的计划和计费方式
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 13a2be8c-dc14-46cc-ab7d-5075bfd4d724
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: b25e97b0f0d0b6f63134a774856eb7ec8f77b679
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="plans-and-billing-in-azure-scheduler"></a>Azure 计划程序中的计划和计费方式
## <a name="job-collection-plans"></a>作业集合计划
作业集合是 Azure 计划程序中的计费实体。 作业集合包含许多作业，并附带三种计划（标准、P10 高级和 P20 高级），下面将对此进行介绍。

| **作业集合计划** | **每个作业集合的作业数上限** | **最大重复次数** | **每个订阅的作业集合数上限** | **限制** |
|:--- |:--- |:--- |:--- |:--- |
| **标准** |每个作业集合 50 个作业 |每分钟一次。 执行作业的频率不能超过每分钟一次 |一个订阅最多允许 100 个标准作业集合 |访问计划程序的完整功能集 |
| **P10 高级** |每个作业集合 50 个作业 |每分钟一次。 执行作业的频率不能超过每分钟一次 |一个订阅最多允许 10,000 个 P10 高级作业集合。 有关详细信息，<a href="mailto:wapteams@microsoft.com">请联系我们</a>。 |访问计划程序的完整功能集 |
| **P20 高级** |每个作业集合 1,000 个作业 |每分钟一次。 执行作业的频率不能超过每分钟一次 |一个订阅最多允许 10,000 个 P20 高级作业集合。 有关详细信息，<a href="mailto:wapteams@microsoft.com">请联系我们</a>。 |访问计划程序的完整功能集 |

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>升级和降级作业集合计划
随时可以在标准、P10 高级和 P20 高级计划之间升级或降级作业集合计划。

## <a name="billing-and-azure-plans"></a>计费和 Azure 计划
如果标准作业集合数超过 100 个（10 个标准计费单位），则最好是将所有作业集合纳入高级计划。

如果有一个标准作业集合和一个高级作业集合，则根据一个标准计费单位*和*一个高级计费单位计费。 计划程序服务根据设置为标准或高级的基于活动作业集合计费；接下来的两个部分将进一步对此做出解释。

## <a name="standard-billable-units"></a>标准计费单位
一个标准计费单位最多可以包含 10 个标准作业集合。 由于每个标准作业集合最多可以包含 50 个作业，因此一个标准计费单位允许订阅最多包含 500 个作业 – 每月最多可以执行近 2200 万次作业。

如果有 1 到 10 个标准作业集合，会按照 1 个标准计费单位向你计费。 如果有 11 到 20 个标准作业集合，会按照 2 个标准计费单位向你计费。 如果有 21 到 30 个标准作业集合，会按照 3 个标准计费单位向你计费，依次类推。

## <a name="p10-premium-billable-units"></a>P10 高级计费单位
一个 P10 高级计费单位最多可以包含 10,000 个 P10 高级作业集合。 由于每个 P10 高级作业集合最多可以包含 50 个作业，因此一个高级计费单位允许订阅最多包含 500,000 个作业 – 每月最多可以执行近 220 亿次作业。

如果有 1 到 10,000 个高级作业集合，会按照 1 个 P10 高级计费单位计费。 如果有 10,001 到 20,000 个高级作业集合，会按照 2 个 P10 高级计费单位计费，依此类推。

因此，P10 高级作业集合的功能与标准作业集合相同，但是，这个价格段适合应用程序需要许多作业集合的情况。

## <a name="p20-premium-billable-units"></a>P20 高级计费单位
一个 P20 高级计费单位最多可以包含 5,000 个 P20 高级作业集合。 由于每个 P20 高级作业集合最多可以包含 1,000 个作业，因此一个高级计费单位允许订阅最多包含 5,000,000 个作业 – 每月最多可以执行近 220 亿次作业。

P20 高级作业集合提供的功能与 P10 高级作业集合相同，但每个作业集合支持更大数量的作业，并且支持的作业总数也更多，可实现更大的可伸缩性。

## <a name="billing-and-active-status"></a>计费和活动状态
作业集合始终处于活动状态，除非整个订阅由于计费问题而进入某种临时禁用状态。 要确保作业集合不被计费，只能将它设置为*免费*计划，或者删除该作业集合。

尽管用户可以在单个操作中禁用某个作业集合中的所有作业，但这不会更改作业集合的计费状态：作业集合*仍然*计费。 同样，空作业集合被视为活动状态，因此会被计费。

## <a name="pricing"></a>定价
有关定价详情，请参阅[计划程序定价](https://azure.microsoft.com/pricing/details/scheduler/)。

## <a name="see-also"></a>另请参阅
 [计划程序是什么？](scheduler-intro.md)

 [Azure 计划程序的概念、术语和实体层次结构](scheduler-concepts-terms.md)

 [开始在 Azure 门户中使用计划程序](scheduler-get-started-portal.md)

 [Azure 计划程序 REST API 参考](https://msdn.microsoft.com/library/mt629143)

 [Azure 计划程序 PowerShell cmdlet 参考](scheduler-powershell-reference.md)

 [Azure 计划程序的高可用性和可靠性](scheduler-high-availability-reliability.md)

 [Azure 计划程序的限制、默认值和错误代码](scheduler-limits-defaults-errors.md)

 [Azure 计划程序出站身份验证](scheduler-outbound-authentication.md)

