---
title: 在 Azure Monitor 中监视使用情况和预估成本 | Microsoft Docs
description: Azure Monitor 中的使用情况和预估成本页的使用过程概述
author: dalekoetke
manager: carmonmills
editor: mrbullwinkle
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: Dale.Koetke;mbullwin
ms.openlocfilehash: ce295c449b01de4fa99df9198805a6b0727c0d18
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="monitoring-usage-and-estimated-costs"></a>监视使用情况和预估成本

在 Azure 门户的 Monitor 中心，“使用情况和预估成本”页旨在帮助了解[警报、指标和通知](https://azure.microsoft.com/pricing/details/monitor/)、[Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 及 [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) 等核心监视功能的使用情况。 对于使用 2018 年 4 月之前提供的定价计划的客户，这还包括通过见解和分析产品/服务购买的 Log Analytics 使用情况功能。

在此页上，用户可以查看这些资源在过去 31 天的使用情况、按订阅聚合的数据，并可以深入到此时间段内的使用趋势。 需要提取大量的数据才能生成此预估信息，因此，请耐心等候页面加载。
以下示例显示正在监视使用情况，以及最终的预估成本：

![使用情况和预估成本门户屏幕截图](./media/monitoring-usage-and-estimated-costs/001.png)

单击“每月使用情况”列中的链接会打开一个图表，其中显示过去 31 天的使用趋势：

![“每个节点包含”(671.47 GB) 的屏幕截图](./media/monitoring-usage-and-estimated-costs/002.png)

下面是另一个类似的使用情况和成本摘要，本例适用于 2018 年 4 月新推出的基于消耗的定价模型中的订阅。 可以看到，现在不再提供任何基于节点的计费，并且 Log Analytics 和 Application Insights 的数据引入与保留期按新的通用计量值报告。

![使用情况和预估成本门户屏幕截图](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>新定价模型

2018 年 4 月发布了新的监视定价模型。  此模型采用适合云的基于消耗的定价。 客户只需为使用的资源付费，且无需基于节点做出承诺。 我们已提供适用于[警报、指标和通知](https://azure.microsoft.com/pricing/details/monitor/)、[Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 及 [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) 的新定价模型的详细信息。

对于在 2018 年 4 月 2 日之后加入 Log Analytics 或 Application Insights 的客户，新定价模型是唯一选项。 对于已使用这些服务的客户，可以选择性地转移到新定价模型。

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>评估新定价模型的影响

新定价模型根据每个客户的监视使用模式造成不同的影响。 在 2018 年 4 月 2 日之前已使用 Log Analytics 或 Application Insights 的客户，可以通过 Azure Monitor 中的“使用情况和预估成本”页来评估任何成本变化（如果已转移到新定价模型），并将订阅转移到新定价模型。 对于大多数客户而言，新定价模型非常有利，但对于数据使用模式超高或者位于较高成本区域中的客户，情况可能不是这样。

若要查看在“使用情况和预估成本”页上选择的订阅的预估成本，请单击页面顶部附近的蓝色横幅。 最好是一次针对一个订阅执行此操作，因为只能在一个订阅级别采用新定价模型。

![定价模型选择屏幕截图](./media/monitoring-usage-and-estimated-costs/004.png)

现在将会看到此页的类似版本，其中包含绿色横幅：

![定价模型选择屏幕截图](./media/monitoring-usage-and-estimated-costs/005.png)

此处显示了一组不同的计量器 – 这些计量器对应于新定价模型。 例如，

1. 见解和分析\每个节点的超额
2. 见解和分析\每个节点包含
3. Application Insights\基本超额数据
4. Application Insights\已包含的数据

等数据引入计量器已合并为称作“共享服务\数据引入”的新通用数据引入计量器，因为新定价模型不提供每节点包括的数据权利。

会看到的另一处变化是，引入到成本较高区域中 Log Analytics 或 Application Insights 的数据附带一个新的区域计量器以正确反映此情况，例如“数据引入(美国中西部)”。

> [!NOTE]
> 每订阅估计成本未纳入到 Operations Management Suite (OMS) 订阅的帐户级每节点权利中。 请咨询客户代表，以更深入地探讨这种情况下的新定价模型。

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>新定价模型和 Operations Management Suite 订阅权利

购买了 Microsoft Operations Management Suite E1 和 E2 的客户有资格享受 [Log Analytics](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite) 和 [Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-pricing#the-price-plans) 的每节点数据引入权利。 若要在给定的订阅中享受 Log Analytics 工作区或 Application Insights 资源的这些权利，该订阅的定价模型必须保持为 2018 年 4 月之前的定价模型，该定价模型中提供了 Log Analytics“每节点 (OMS)”定价层和 Application Insights“企业”定价计划。 取决于你的组织购买的套件的节点数，将某些订阅转移到新定价模型可能还是有利的，但这需要仔细考虑。 

## <a name="changes-when-moving-to-the-new-pricing-model"></a>转移到新定价模型时的变化

将订阅移到新定价模型会将每个 Log Analytics 的定价层更改为新的“按 GB”层（在 Azure 资源管理器中称作“pergb2018”）。 这种转移还会将企业计划中的所有 Application Insights 资源更改到“基本”计划。 这些变化造成的影响会显示在上述预估成本中。 

## <a name="moving-to-the-new-pricing-model"></a>转移到新定价模型

如果决定对某个订阅采用新定价模型，请单击“使用情况和预估成本”页顶部的“定价模型选择”选项：

![在新定价模型中监视“使用情况和预估成本”屏幕截图](./media/monitoring-usage-and-estimated-costs/006.png)

这会打开“定价模型选择”页，其中列出了在前一页中查看的每个订阅：

![“定价模型选择”屏幕截图](./media/monitoring-usage-and-estimated-costs/007.png)

若要将订阅移到新定价模型，只需选中相应的复选框并单击“保存”。  可以相同的方式移回到旧定价模型。 请记住，更改定价模型需要订阅所有者或参与者权限。
