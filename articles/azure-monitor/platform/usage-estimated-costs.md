---
title: 在 Azure Monitor 中监视使用情况和预估成本
description: Azure Monitor 中的使用情况和预估成本页的使用过程概述
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 7117e7287f601b306893cb02dc5d7599d7c6224d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60453745"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>在 Azure Monitor 中监视使用情况和预估成本

> [!NOTE]
> 本文介绍如何针对不同的定价模型查看多个 Azure 监视功能的使用情况和估算成本。  相关信息请参阅以下文章。
> - [通过在 Log Analytics 中控制数据量和保留期管理成本](manage-cost-storage.md)介绍如何通过更改数据保留期来控制成本。
> - [在 Log Analytics 中分析数据使用情况](../../azure-monitor/platform/data-usage.md)介绍了如何根据数据使用情况进行分析和警告。
> - [在 Application Insights 中管理定价和数据量](../../azure-monitor/app/pricing.md)介绍如何在 Application Insights 中分析数据使用情况。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

在 Azure 门户的 Monitor 中心，“使用情况和预估成本”页说明[警报、指标和通知](https://azure.microsoft.com/pricing/details/monitor/)、[Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 及 [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) 等核心监视功能的使用情况。 对于使用 2018 年 4 月之前提供的定价计划的客户，这还包括通过见解和分析产品/服务购买的 Log Analytics 使用情况功能。

在此页上，用户可以查看过去 31 天的资源使用情况（按订阅聚合）。 “钻取”显示的是 31 天的使用趋势。 需要聚合大量的数据才能进行此估算，因此请耐心等待页面加载。

以下示例显示正在监视使用情况，以及最终的预估成本：

![使用情况和预估成本门户屏幕截图](./media/usage-estimated-costs/001.png)

选择“每月使用情况”列中的链接会打开一个图表，其中显示过去 31 天的使用趋势：

![“每个节点包含”条形图屏幕截图](./media/usage-estimated-costs/002.png)

下面是另一个类似的使用情况和成本摘要。 此示例显示 2018 年 4 月新推出的基于消耗的定价模型中的订阅。 请注意，没有基于节点的计费。 Log Analytics 和 Application Insights 的数据引入与保留期现在按新的通用计量值报告。

![使用情况和预估成本门户屏幕截图 - 2018 年 4 月定价](./media/usage-estimated-costs/003.png)

## <a name="new-pricing-model"></a>新定价模型

2018 年 4 月[发布了新的监控定价模型](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)。  此模型采用适合云的基于消耗的定价。 客户只需为使用的资源付费，且无需基于节点做出承诺。 我们已提供适用于[警报、指标和通知](https://azure.microsoft.com/pricing/details/monitor/)、[Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 及 [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) 的新定价模型的详细信息。 

对于在 2018 年 4 月 2 日之后加入 Log Analytics 或 Application Insights 的客户，新定价模型是唯一选项。 对于已使用这些服务的客户，可以选择性地转移到新定价模型。

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>评估新定价模型的影响
新定价模型对每个客户造成的影响因客户的监视使用模式而异。 在 2018 年 4 月 2 日之前使用过 Log Analytics 或 Application Insights 的客户可以通过 Azure Monitor 中的“使用情况和预估成本”页来评估改用新定价模型后的成本变化。 可以通过该页将订阅移到新模型中。 对于大多数客户来说，新定价模型是有利的。 对于数据使用量大的客户或所在区域成本较高的客户来说，情况可能不是这样。

若要查看在“使用情况和预估成本”页上选择的订阅的预估成本，请选择页面顶部附近的蓝色横幅。 最好是一次针对一个订阅执行此操作，因为只能这样采用新定价模型。

![在新定价模型中监视“使用情况和预估成本”的屏幕截图](./media/usage-estimated-costs/004.png)

新页面显示的版本与前一页类似，只是横幅是绿色的：

![在当前定价模型中监视“使用情况和预估成本”的屏幕截图](./media/usage-estimated-costs/005.png)

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

购买了 Microsoft Operations Management Suite E1 和 E2 的客户有资格享受 [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) 和 [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) 的每节点数据引入权利。 若要在给定的订阅中享受 Log Analytics 工作区或 Application Insights 资源的这些权利： 

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

如果您已决定采用新定价模型为给定的订阅，请转到每个 Application Insights 资源，打开**使用情况和预估的成本**并确保它在基本定价级别，并转到每个 Log Analytics工作区中，打开每个**定价层**页上，并将更改为**按 GB (2018)** 定价层。 

> [!NOTE]
> 所有 Application Insights 资源和 Log Analytics 工作区在给定订阅中的都采用的最新定价模型的要求现在已删除，从而允许更多灵活性和更轻松的配置。 

## <a name="automate-moving-to-the-new-pricing-model"></a>自动转移到新定价模型

如上所述，不再需要监视的所有资源在订阅中的新定价模型将在移动到同一时间，并因此``migratetonewpricingmodel``操作将不会再有任何效果。 现在您可以 Application Insights 资源和 Log Analytics 工作区单独移动到最新的定价层。  

自动执行此更改记录以供使用的 Application Insights[集 AzureRmApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/azurerm.applicationinsights/set-azurermapplicationinsightspricingplan)与``-PricingPlan "Basic"``和 Log Analytics 使用[集 AzureRmOperationalInsightsWorkspace](https://docs.microsoft.com/powershell/module/AzureRM.OperationalInsights/Set-AzureRmOperationalInsightsWorkspace)与``-sku "PerGB2018"``。 

