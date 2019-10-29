---
title: 在 Azure Monitor 中监视使用情况和预估成本
description: Azure Monitor 中的使用情况和预估成本页的使用过程概述
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 48abf95e65b6185f5c95a1f5d942091ed0f33122
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044198"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>在 Azure Monitor 中监视使用情况和预估成本

> [!NOTE]
> 本文介绍如何在多个 Azure 监视功能中查看使用情况和估计成本。 Azure Monitor 的特定组件的相关文章包括：
> - [使用 Azure Monitor 日志管理使用情况和成本](manage-cost-storage.md)介绍了如何通过更改数据保持期来控制成本，以及如何分析和警报数据的使用情况。
> - [管理 Application Insights 的使用情况和成本](../../azure-monitor/app/pricing.md)介绍如何分析 Application Insights 中的数据使用情况。

## <a name="azure-monitor-pricing-model"></a>Azure Monitor 定价模型

基本 Azure Monitor 计费模型是一种基于云的、基于消耗的定价（"即用即付"）。 仅为所用部分付费。 定价详细信息适用于[警报、指标、通知](https://azure.microsoft.com/pricing/details/monitor/)、 [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)和[Application Insights](https://azure.microsoft.com/pricing/details/application-insights/)。 

除了用于日志数据的即用即付模型外，Log Analytics 还提供了容量预留，这使你可以将其与即用即付价格相比最多保存25%。 产能预留价格使你可以购买起价 100 GB/天的保留。 将按现用现付费率对超出预订级别的任何使用量进行计费。 [了解](https://azure.microsoft.com/pricing/details/monitor/)有关容量保留定价的详细信息。

某些客户将有权访问[旧式 Log Analytics 定价](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)层和[旧的企业 Application Insights 定价层](https://docs.microsoft.com/azure/azure-monitor/app/pricing#legacy-enterprise-per-node-pricing-tier)。 

## <a name="understanding-your-azure-monitor-costs"></a>了解 Azure Monitor 成本

了解成本有两个阶段。 第一种方法是考虑将 Azure Monitor 用作监视解决方案。 

### <a name="estimating-the-costs-to-manage-your-environment"></a>估计管理环境的成本

如果尚未使用 Azure Monitor 日志，可以使用[Azure Monitor 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=monitor)来估计使用 Azure Monitor 的成本。 首先在搜索框中输入 "Azure Monitor"，然后单击生成的 Azure Monitor 磁贴。 向下滚动到 "Azure Monitor"，然后从 "类型" 下拉列表中选择其中一个选项：

- 指标查询和警报  
- Log Analytics
- Application Insights

在上述每种情况下，定价计算器都可帮助你根据预期利用率估计可能的成本。

例如，通过 Log Analytics 可以输入要从每个 VM 收集的 Vm 数和 GB 数据。 通常，从典型的 Azure VM 引入的数据月份是 1 GB 到 3 GB。 如果已在评估 Azure Monitor 的日志，则可以使用自己的环境中的数据统计信息。 请参阅下面有关如何确定[受监视 vm 的数目](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data)以及[工作区引入的数据量](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)。

与 Application Insights 同样，如果启用 "基于应用程序活动的估计数据量" 功能，则可以提供有关应用程序的输入（每月的请求数和每月的页面视图，以防您收集客户端遥测），然后，计算器会告诉您类似应用程序收集的中间值和90% 的数据量。 这些应用程序在 Application Insights 配置范围内（例如，某些应用程序具有默认采样、有些采样没有采样等），因此您仍然可以使用采样来减少在中间级别下引入的数据量。 但这是一种开始了解其他同类客户看到的情况。 [详细了解](https://docs.microsoft.com/azure/azure-monitor/app/pricing#estimating-the-costs-to-manage-your-application)如何估算 Application Insights 的成本。

### <a name="understanding-your-usage-and-estimated-costs"></a>了解你的使用情况和估计成本

使用 Azure Monitor 了解和跟踪使用情况很重要，并且有一组丰富的工具可帮助实现此功能。 

Azure 在[Azure 成本管理 + 计费](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json)中心提供了大量有用的功能。 打开**Azure 成本管理 + 计费**中心后，单击 "**成本管理**"，然后选择[范围](https://docs.microsoft.com/azure/cost-management/understand-work-scopes)（要调查的资源集）。 

然后，若要查看最近30天的 Azure Monitor 成本，请单击 "**每日成本**" 磁贴，选择 "相对日期" 下的 "过去30天"，然后添加一个选择服务名称的筛选器：

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. 见解与分析

这将生成如下所示的视图：

![Azure 成本管理屏幕截图](./media/usage-estimated-costs/010.png)

从这里，您可以从累积成本汇总中深化，以获取 "按资源成本" 视图中的更详细信息。 在当前定价层中，将根据 Log Analytics 或 Application Insights 中的一组计量器对 Azure 日志数据收费。 若要将成本与 Log Analytics 或 Application Insights 使用量隔离开来，你可以在**资源类型**上添加筛选器。 若要查看所有 Application Insights 开销，请将资源类型筛选为 "microsoft.operationalinsights/components"，并为 Log Analytics 成本筛选资源类型。 

通过[从 Azure 门户下载您的使用](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)情况，可以获得更多详细信息。 在下载的电子表格中，可以查看每天每个 Azure 资源的使用情况。 在此 Excel 电子表格中，可以通过第一次筛选 "计量类别" 列以显示 "Application Insights" 和 "Log Analytics" 来查找 Application Insights 资源的用法，然后在 "Instance ID" 列中添加筛选器，这是 "containsmicrosoft insights/组件 "。  由于所有 Azure Monitor 组件都有一个登录后端，因此，大多数 Application Insights 使用情况都是在计量器类别为 Log Analytics 的情况下报告的。  对于旧定价层和多步骤 web 测试 Application Insights 资源，将使用 Application Insights 的计量类别进行报告。  使用情况显示在 "已消耗数量" 列中，每个条目的单位显示在 "度量单位" 列中。  更多详细信息可帮助你[了解 Microsoft Azure 帐单](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)。 

> [!NOTE]
> 使用**Azure 成本管理 + 计费**中心中的**成本管理**是广泛了解监视成本的首选方法。  [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs)和[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/pricing#understand-your-usage-and-estimate-costs)的**使用情况和估计成本**经验为 Azure Monitor 的每个部分提供更深入的见解。

查看 Azure Monitor 使用情况的另一种方法是监视集线器中的 "**使用情况和估计成本**" 页。 这会显示核心监视功能的使用情况，例如[警报、指标、通知](https://azure.microsoft.com/pricing/details/monitor/)、 [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)和[Azure 应用程序见解](https://azure.microsoft.com/pricing/details/application-insights/)。 对于使用 2018 年 4 月之前提供的定价计划的客户，这还包括通过见解和分析套餐购买的 Log Analytics 使用情况功能。

在此页上，用户可以查看过去 31 天的资源使用情况（按订阅聚合）。 `Drill-ins` 显示31天内的使用趋势。 需要聚合大量的数据才能进行此估算，因此请耐心等待页面加载。

以下示例显示正在监视使用情况，以及最终的预估成本：

![使用情况和预估成本门户屏幕截图](./media/usage-estimated-costs/001.png)

选择“每月使用情况”列中的链接会打开一个图表，其中显示过去 31 天的使用趋势： 

![“每个节点包含”条形图屏幕截图](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Operations Management Suite 订阅权利

购买了 Microsoft Operations Management Suite E1 和 E2 的客户有资格享受 [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) 和 [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) 的每节点数据引入权利。 若要在给定的订阅中享受 Log Analytics 工作区或 Application Insights 资源的这些权利： 

- Log Analytics 工作区应使用“按节点 (OMS)”定价层。
- Application Insights 资源应使用 "企业" 定价层。

根据你的组织购买的套件的节点数，将某些订阅转移到即用即付（每 GB）定价层可能会很有利，但这需要仔细考虑。

> [!WARNING]
> 如果你的组织具有当前 Microsoft Operations Management Suite E1 和 E2，通常最好将 Log Analytics 工作区保存在 "每节点（OMS）" 定价层和 "企业" 定价层中的 Application Insights 资源。 
>
