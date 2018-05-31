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
ms.openlocfilehash: 6cc35697573ae2997f289f67c7867d9c522149be
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203771"
---
# <a name="monitoring-usage-and-estimated-costs"></a>监视使用情况和预估成本

在 Azure 门户的 Monitor 中心，“使用情况和预估成本”页说明[警报、指标和通知](https://azure.microsoft.com/pricing/details/monitor/)、[Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 及 [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) 等核心监视功能的使用情况。 对于使用 2018 年 4 月之前提供的定价计划的客户，这还包括通过见解和分析产品/服务购买的 Log Analytics 使用情况功能。

在此页上，用户可以查看过去 31 天的资源使用情况（按订阅聚合）。 “钻取”显示的是 31 天的使用趋势。 需要聚合大量的数据才能进行此估算，因此请耐心等待页面加载。

以下示例显示正在监视使用情况，以及最终的预估成本：

![使用情况和预估成本门户屏幕截图](./media/monitoring-usage-and-estimated-costs/001.png)

选择“每月使用情况”列中的链接会打开一个图表，其中显示过去 31 天的使用趋势：

![“每个节点包含”条形图屏幕截图](./media/monitoring-usage-and-estimated-costs/002.png)

下面是另一个类似的使用情况和成本摘要。 此示例显示 2018 年 4 月新推出的基于消耗的定价模型中的订阅。 请注意，没有基于节点的计费。 Log Analytics 和 Application Insights 的数据引入与保留期现在按新的通用计量值报告。

![使用情况和预估成本门户屏幕截图 - 2018 年 4 月定价](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>新定价模型

2018 年 4 月[发布了新的监控定价模型](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)。  此模型采用适合云的基于消耗的定价。 客户只需为使用的资源付费，且无需基于节点做出承诺。 我们已提供适用于[警报、指标和通知](https://azure.microsoft.com/pricing/details/monitor/)、[Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 及 [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) 的新定价模型的详细信息。 

对于在 2018 年 4 月 2 日之后加入 Log Analytics 或 Application Insights 的客户，新定价模型是唯一选项。 对于已使用这些服务的客户，可以选择性地转移到新定价模型。

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>评估新定价模型的影响
新定价模型对每个客户造成的影响因客户的监视使用模式而异。 在 2018 年 4 月 2 日之前使用过 Log Analytics 或 Application Insights 的客户可以通过 Azure Monitor 中的“使用情况和预估成本”页来评估改用新定价模型后的成本变化。 可以通过该页将订阅移到新模型中。 对于大多数客户来说，新定价模型是有利的。 对于数据使用量大的客户或所在区域成本较高的客户来说，情况可能不是这样。

若要查看在“使用情况和预估成本”页上选择的订阅的预估成本，请选择页面顶部附近的蓝色横幅。 最好是一次针对一个订阅执行此操作，因为只能这样采用新定价模型。

![在新定价模型中监视“使用情况和预估成本”的屏幕截图](./media/monitoring-usage-and-estimated-costs/004.png)

新页面显示的版本与前一页类似，只是横幅是绿色的：

![在当前定价模型中监视“使用情况和预估成本”的屏幕截图](./media/monitoring-usage-and-estimated-costs/005.png)

该页还显示了对应于新定价模型的另一组计量。 以下列表是一个示例：

- 见解和分析\每个节点的超额
- 见解和分析\每个节点包含
- Application Insights\基本超额数据
- Application Insights\已包含的数据

新定价模型不提供基于节点的“包含数据”分配。 因此，这些数据引入计量合并为称作“共享服务\数据引入”的新通用数据引入计量。 

在成本较高的区域，引入到 Log Analytics 或 Application Insights 的数据还有一个变化。 这些高成本区域的数据在显示时会使用新的区域计量。 一个例子是**数据引入（美国中西部）**。

> [!NOTE]
> 每订阅估计成本未纳入到 Operations Management Suite (OMS) 订阅的帐户级每节点权利中。 请咨询客户代表，以更深入地探讨这种情况下的新定价模型。

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>新定价模型和 Operations Management Suite 订阅权利

购买了 Microsoft Operations Management Suite E1 和 E2 的客户有资格享受 [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) 和 [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing#the-price-plans) 的每节点数据引入权利。 若要在给定的订阅中享受 Log Analytics 工作区或 Application Insights 资源的这些权利： 

- 该订阅的定价模型必须保持为 2018 年 4 月之前的定价模型。
- Log Analytics 工作区应使用“按节点 (OMS)”定价层。
- Application Insights 资源应使用“企业”定价计划。

根据组织所购买套件的节点数，将某些订阅转移到新定价模型可能是有利的，但这需要仔细考虑。 通常情况下，建议保持为上面所述的 2018 年 4 月之前的定价模型即可。

> [!WARNING]
> 如果组织已购买 Microsoft Operations Management Suite E1 和 E2，通常最好将订阅保持为 2018 年 4 月之前的定价模型。 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>转移到新定价模型时的变化

新定价模型将 Log Analytics 和 Application Insights 定价选项简化为仅包含一个单层（或计划）。 将订阅转移到新定价模型会：

- 将每个 Log Analytics 的定价层更改为新的“按 GB”层（在 Azure 资源管理器中称为“按 GB 2018”）
- 将“企业”计划中的任何 Application Insights 资源更改为“基本”计划。

成本估算显示了这些变化造成的影响。

> [!WARNING]
> 要注意的重要一点是，如果使用 Azure 资源管理器或 PowerShell 在订阅中部署 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) 或 [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell)，则你已转移到新的定价模型。 如果为 Log Analytics 指定“按 GB 2018”以外的定价层/计划，或为 Application Insights 指定“基本”以外的定价层/计划，此部署不会由于指定了无效定价层/计划而失败，相反，此部署会成功，但它将仅使用有效的定价层/计划（这不适用于生成无效定价层消息的 Log Analytics 免费层）。
>

## <a name="moving-to-the-new-pricing-model"></a>转移到新定价模型

如果决定对某个订阅采用新定价模型，请选择“使用情况和预估成本”页顶部的“定价模型选择”选项：

![在新定价模型中监视“使用情况和预估成本”的屏幕截图](./media/monitoring-usage-and-estimated-costs/006.png)

此时会打开“定价模型选择”页。 该页显示一个列表，其中包含在上一页查看的每个订阅。

![“定价模型选择”屏幕截图](./media/monitoring-usage-and-estimated-costs/007.png)

若要将订阅移到新定价模型，只需选中相应的复选框，然后选择“保存”即可。 可以相同的方式移回到旧定价模型。 请记住，更改定价模型需要订阅所有者或参与者权限。
