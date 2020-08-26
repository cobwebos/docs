---
title: 在 Azure Monitor 中监视使用情况和预估成本
description: Azure Monitor 中的使用情况和预估成本页的使用过程概述
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: eb96537f67c61fb31759da020068f784d0e89993
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323394"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>在 Azure Monitor 中监视使用情况和预估成本

> [!NOTE]
> 本文介绍如何查看多个 Azure 监视功能的使用情况和估算成本。 Azure Monitor 特定组件的相关文章包括：
> - [使用 Azure Monitor 日志管理使用情况和成本](manage-cost-storage.md)介绍了如何通过更改数据保留期来控制成本，以及如何分析数据使用情况并对其发出警报。
> - [管理 Application Insights 的使用情况和成本](../app/pricing.md)介绍了如何在 Application Insights 中分析数据使用情况。

## <a name="azure-monitor-pricing-model"></a>Azure Monitor 定价模型

基本 Azure Monitor 计费模型是云友好的基于消耗量的定价模型（“即用即付”）。 只需为所用的部分付费。 我们已提供适用于[警报、指标和通知](https://azure.microsoft.com/pricing/details/monitor/)、[Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 及 [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) 的定价详细信息。 

除了适用于日志数据的即用即付模型以外，Log Analytics 还提供容量预留，与即用即付价格相比，该模型最多可将成本节省 25%。 产能预留定价使你可以购买起价为 100 GB/天的保留。 超过预留级别的任何用量将按即用即付费率计费。 [详细了解](https://azure.microsoft.com/pricing/details/monitor/)容量预留定价。

某些客户有权访问[传统 Log Analytics 定价层](./manage-cost-storage.md#legacy-pricing-tiers)和[传统 Enterprise Application Insights 定价层](../app/pricing.md#legacy-enterprise-per-node-pricing-tier)。 

## <a name="understanding-your-azure-monitor-costs"></a>了解 Azure Monitor 成本

了解成本的过程分为两个阶段。 第一个阶段是考虑将 Azure Monitor 用作监视解决方案。 

### <a name="estimating-the-costs-to-manage-your-environment"></a>估算环境的管理成本

如果你尚未使用 Azure Monitor 日志，可以使用 [Azure Monitor 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=monitor)来估算 Azure Monitor 的使用成本。 首先在搜索框中输入“Azure Monitor”，然后单击生成的 Azure Monitor 磁贴。 在页面中向下滚动到“Azure Monitor”，然后从“类型”下拉列表中选择一个选项。

- 指标查询和警报  
- Log Analytics
- Application Insights

在上述每种情况下，定价计算器都可帮助你根据预期用量估算可能的成本。

例如，对于 Log Analytics，可以输入 VM 数目，以及要从每个 VM 收集的数据量 (GB)。 通常每月会从一个典型的 Azure VM 引入 1 GB 到 3 GB 数据。 如果已经评估了 Azure Monitor 日志，则可以使用自己环境中的数据统计信息。 请参阅下文来了解如何确定[受监视 VM 数](./manage-cost-storage.md#understanding-nodes-sending-data)和[工作区引入的数据量](./manage-cost-storage.md#understanding-ingested-data-volume)。

类似于 Application Insights，如果启用“基于应用程序活动估算数据量”功能，则可以提供有关应用程序的输入（如果收集客户端遥测数据，请提供每月请求数和页面视图数），然后，计算器会告知类似应用程序收集的中间值和第 90 百分位的数据量。 这些应用程序超过了 Application Insights 的配置范围（例如，有些应用程序采用默认采样，而有些应用程序根本不使用采样，等等），因此，你仍可以通过采样控制，将引入的数据量降到远远低于中位水平。 但这只是了解其他类似客户看到的情况的一个起点。 [详细了解](../app/pricing.md#estimating-the-costs-to-manage-your-application)如何估算 Application Insights 的成本。

### <a name="understanding-your-usage-and-estimated-costs"></a>了解你的使用情况和估计成本

使用 Azure Monitor 了解和跟踪使用情况很重要，并且有一组丰富的工具可帮助实现此功能。 

Azure 在 [Azure 成本管理和计费](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json)中心提供了大量实用功能。 打开**Azure 成本管理 + 计费**中心后，单击 "**成本管理**"，然后选择[范围](../../cost-management-billing/costs/understand-work-scopes.md)（要调查的资源集）。 

然后，若要查看最近30天的 Azure Monitor 成本，请单击 "**每日成本**" 磁贴，选择 "相对日期" 下的 "过去30天"，然后添加一个选择服务名称的筛选器：

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. 见解与分析

这将生成如下所示的视图：

![Azure 成本管理屏幕截图](./media/usage-estimated-costs/010.png)

从这里，您可以从累积成本汇总中深化，以获取 "按资源成本" 视图中的更详细信息。 在当前定价层中，将根据 Log Analytics 或 Application Insights 中的一组计量器对 Azure 日志数据收费。 若要将成本与 Log Analytics 或 Application Insights 使用量隔离开来，你可以在**资源类型**上添加筛选器。 若要查看所有 Application Insights 开销，请将资源类型筛选为 "microsoft.operationalinsights/components"，并为 Log Analytics 成本筛选资源类型。 

通过[从 Azure 门户下载您的使用](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal)情况，可以获得更多详细信息。 在下载的电子表格中，可以查看每天每个 Azure 资源的使用情况。 在此 Excel 电子表格中，可通过以下方式查找 Application Insights 资源的使用情况：先按“计量类别”列进行筛选，以显示“Application Insights”和“Log Analytics”，然后按“实例 ID”列添加一个“包含 microsoft.insights/components”筛选器。  由于所有 Azure Monitor 组件只有一个日志后端，因此，大多数 Application Insights 使用情况都是根据“计量类别”为 Log Analytics 的计量报告的。  只有旧式定价层和多步骤 Web 测试中的 Application Insights 资源才使用计量类别 Application Insights 进行报告。  使用情况显示在“使用的数量”列中，每个条目的单位显示在“度量单位”列中。  另外还提供更多详细信息，可帮助你[了解 Microsoft Azure 帐单](../../cost-management-billing/understand/review-individual-bill.md)。 

> [!NOTE]
> 使用**Azure 成本管理 + 计费**中心中的**成本管理**是广泛了解监视成本的首选方法。  适用于 [Log Analytics](./manage-cost-storage.md#understand-your-usage-and-estimate-costs) 和 [Application Insights](../app/pricing.md#understand-your-usage-and-estimate-costs) 的“使用情况和估算成本”为 Azure Monitor 的上述每个组成部分提供了更深入的见解。****

也可以在“Monitor”中心的“使用情况和估算成本”页中查看 Azure Monitor 的使用情况。  此页会显示[警报、指标和通知](https://azure.microsoft.com/pricing/details/monitor/)、[Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 及 [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) 等核心监视功能的使用情况。 对于使用 2018 年 4 月之前提供的定价计划的客户，这还包括通过见解和分析套餐购买的 Log Analytics 使用情况功能。

在此页上，用户可以查看过去 31 天的资源使用情况（按订阅聚合）。 `Drill-ins` 显示 31 天的使用趋势。 需要聚合大量的数据才能进行此估算，因此请耐心等待页面加载。

以下示例显示正在监视使用情况，以及最终的预估成本：

![使用情况和预估成本门户屏幕截图](./media/usage-estimated-costs/001.png)

选择“每月使用情况”列中的链接会打开一个图表，其中显示过去 31 天的使用趋势： 

![“每个节点包含”条形图屏幕截图](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Operations Management Suite 订阅权利

购买了 Microsoft Operations Management Suite E1 和 E2 的客户有资格享受 [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) 和 [Application Insights](../app/pricing.md) 的每节点数据引入权利。 若要在给定的订阅中享受 Log Analytics 工作区或 Application Insights 资源的这些权利： 

- Log Analytics 工作区应使用“按节点 (OMS)”定价层。
- Application Insights 资源应使用“企业”定价层。

根据组织所购买套件的节点数，将某些订阅转移到即用即付（按 GB）定价层可能是有利的，但这需要仔细考虑。

> [!WARNING]
> 如果组织已购买 Microsoft Operations Management Suite E1 和 E2，通常最好将 Log Analytics 工作区保留在“按节点(OMS)”定价层，将 Application Insights 资源保留在“企业”定价层。 
>

