---
title: 利用 Azure Monitor 监视 Azure 资源 |Microsoft Docs
description: 介绍如何使用 Azure Monitor 从 Azure 中的资源收集和分析监视数据。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 01d188e0e39888297ff8d6a57129a3a17e1654fe
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654913"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>利用 Azure Monitor 监视 Azure 资源
如果你有依赖于 Azure 资源的关键应用程序和业务流程，则需要监视这些资源的可用性、性能和操作。 本文介绍 Azure 资源生成的监视数据，以及如何使用 Azure Monitor 的功能对此数据进行分析和发出警报。

> [!IMPORTANT]
> 本文适用于 Azure 中使用 Azure Monitor 的所有服务。 计算资源（包括 Vm 和应用服务）在此处生成相同的监视数据，但也有可能生成日志和指标的来宾操作系统。 有关如何收集和分析此数据的详细信息，请参阅这些服务的监视文档。

## <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？
Azure Monitor 是 Azure 中的一个完整 stack 监视服务，它提供了一组完整的功能来监视 Azure 资源以及其他云和本地的资源。 [Azure Monitor 数据平台](../platform/data-platform.md)会将数据收集到[日志](../platform/data-platform-logs.md)和[指标](../platform/data-platform-metrics.md)中，可以使用一组完整的监视工具将数据组合到一起，如以下各节所述。

- [您可以对 Azure Monitor 指标执行哪些操作？](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [您可以对 Azure Monitor 日志执行哪些操作？](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

一旦创建了 Azure 资源，Azure Monitor 就会启用，并开始收集可[在 Azure 门户中查看和分析](#monitoring-in-the-azure-portal)的指标和活动日志。 对于某些配置，可以收集其他监视数据并启用其他功能。 有关配置要求的详细信息，请参阅下面的[监视数据](#monitoring-data)。


## <a name="costs-associated-with-monitoring"></a>与监视相关的成本
分析默认情况下收集的监视数据不收取任何费用。 其中包括：

- 收集平台指标，并通过指标资源管理器对其进行分析。
- 收集活动日志，并在 Azure 门户中对其进行分析。
- 创建活动日志警报规则。

收集和导出日志和指标没有 Azure Monitor 开销，但可能存在与目标相关的相关成本：

- Log Analytics 工作区收集日志和指标时，与数据引入和保留相关的成本。 请参阅[Log Analytics Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。
- 将日志和指标收集到 Azure 存储帐户时，与数据存储关联的成本。 [有关 blob 存储，请参阅 Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。
- 将日志和指标转发到 Azure 事件中心时，与事件中心流式传输相关的成本。 请参阅[Azure 事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。

可能存在与以下相关的 Azure Monitor 成本。 请参阅[Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)：

- 运行日志查询。
- 创建指标或日志查询警报规则。
- 从任何警报规则发送通知。
- 通过 API 访问指标。

## <a name="monitoring-data"></a>监视数据
Azure 中的资源生成的[日志](../platform/data-platform-logs.md)和[指标](../platform/data-platform-metrics.md)如下图所示。 请参阅每个 Azure 服务的文档以了解其所生成的特定数据以及提供的任何其他解决方案或见解。

![概述](media/monitor-azure-resource/logs-metrics.png)



- [平台指标](../platform/data-platform-metrics.md)-自动收集的数值，在特定时间自动收集并描述资源的某个方面。 
- [资源日志](../platform/platform-logs-overview.md)-提供对 Azure 资源（数据平面）中执行的操作的深入了解，例如从 Key Vault 获取机密或向数据库发出请求。 资源日志的内容和结构因 Azure 服务和资源类型而异。
- [活动日志](../platform/platform-logs-overview.md)-提供对外部（管理平面）订阅中每个 Azure 资源的操作的见解，例如创建新资源或启动虚拟机。 这是有关对订阅中的资源执行的任何写入操作（PUT、POST、DELETE）的信息。


## <a name="configuration-requirements"></a>配置要求

### <a name="configure-monitoring"></a>配置监视
某些监视数据是自动收集的，但您可能需要根据您的要求执行一些配置。 有关每种类型的监视数据的特定信息，请参阅下面的信息。

- [平台指标](../platform/data-platform-metrics.md)-平台指标会自动收集到[Azure Monitor 的指标](../platform/data-platform-metrics.md)中，无需进行任何配置。 创建诊断设置以将条目发送到 Azure Monitor 日志，或将其转发到 Azure 之外。
- [资源日志](../platform/platform-logs-overview.md)-资源日志由 Azure 资源自动生成，但不收集诊断设置。  创建诊断设置以将条目发送到 Azure Monitor 日志，或将其转发到 Azure 之外。
- [活动日志](../platform/platform-logs-overview.md)-自动收集活动日志，无需任何配置，可以在 Azure 门户中查看。 创建诊断设置以将它们复制到 Azure Monitor 日志，或将其转发到 Azure 之外。

### <a name="log-analytics-workspace"></a>Log Analytics 工作区
将数据收集到 Azure Monitor 日志需要 Log Analytics 工作区。 您可以通过创建新的工作区来快速开始监视服务，但在使用从其他服务收集数据的工作区中，可能有一些值。 有关创建工作区和[设计 Azure Monitor 日志部署](../platform/design-logs-deployment.md)的详细信息，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../learn/quick-create-workspace.md)，以帮助确定最佳的工作区设计以满足你的要求。 如果你在组织中使用现有的工作区，则需要如[管理 Azure Monitor 中的日志数据和工作区的访问](../platform/manage-access.md)中所述的相应权限。 





## <a name="diagnostic-settings"></a>诊断设置
诊断设置定义应将特定资源的资源日志和指标发送到的位置。 可能的目标是：

- [Log Analytics 工作区](../platform/resource-logs-collect-workspace.md)，通过该工作区，你可以使用通过功能强大的日志查询 Azure Monitor 收集的其他监视数据分析数据，还可以利用其他 Azure Monitor 功能，如日志警报和可视化效果。 
- 用于将数据流式传输到外部系统（如第三方 Siem 和其他 log analytics 解决方案）的[事件中心](../platform/resource-logs-stream-event-hubs.md)。 
- 适用于审核、静态分析或备份的[Azure 存储帐户](../platform/resource-logs-collect-storage.md)。

按照创建诊断设置中的过程在[Azure 中收集平台日志和指标](../platform/diagnostic-settings.md)，通过 Azure 门户创建和管理诊断设置。 请参阅在[Azure 中使用资源管理器模板创建诊断设置](../platform/diagnostic-settings-template.md)，以便在模板中定义这些设置，并在创建资源时对其启用完全监视。


## <a name="monitoring-in-the-azure-portal"></a>在 Azure 门户中监视
 可以从 Azure 门户中的资源菜单访问大多数 Azure 资源的监视数据。 这样，你就可以使用标准 Azure Monitor 工具访问单个资源的数据。 某些 Azure 服务将提供不同的选项，因此，你应参考该服务的文档以了解其他信息。 使用 " **Azure Monitor** " 菜单可分析所有被监视资源中的数据。 

### <a name="overview"></a>概述
许多服务的“概述”页中会包含监视数据，以提供关于操作的快速概览。 这通常会基于 Azure Monitor 指标中存储的平台指标子集。 其他监视选项通常会出现在服务的 "**监视**" 部分中。 菜单中）。

![概述页](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>见解和解决方案 
某些服务将提供超出 Azure Monitor 标准功能的工具。 [Insights](../insights/insights-overview.md)提供基于 Azure Monitor 数据平台和标准功能构建的自定义监视体验。 [解决方案](../insights/solutions.md)提供基于 Azure Monitor 日志构建的预定义监视逻辑。 

如果服务有 Azure Monitor 的见解，则可以从每个资源的菜单中的 "**监视**" 访问该服务。 从 " **Azure Monitor** " 菜单访问所有见解和解决方案。

![洞察力](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>度量值
使用[指标资源管理器](../platform/metrics-getting-started.md)分析 Azure 门户中的度量值，可通过大多数服务的 "**指标**" 菜单项获得。 此工具允许你使用单个度量值或将多个度量值组合在一起，以确定相关与趋势。 

- 有关使用指标资源管理器的基本知识，请参阅[Azure 指标资源管理器](../platform/metrics-getting-started.md)入门。
- 请参阅[Azure 指标资源管理器的高级功能](../platform/metrics-charts.md)，了解指标资源管理器的高级功能，如使用多个度量值以及应用筛选器和拆分。

![度量值](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>活动日志 
查看 Azure 门户中的活动日志中的条目，并将初始筛选器设置为当前资源。 将活动日志复制到 Log Analytics 工作区以访问该工作区，以便在日志查询和工作簿中使用它。 

- 有关查看活动日志和使用各种方法检索条目的详细信息，请参阅[查看和检索 Azure 活动日志事件](../platform/activity-log-view.md)。
- 有关所记录的特定事件，请参阅 Azure 服务的文档。

![活动日志](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure Monitor 日志
Azure Monitor 日志合并了来自多个服务和其他数据源的日志和指标，以便使用强大的查询工具进行分析。 如上所述，创建诊断设置以将平台指标、活动日志和资源日志收集到 Azure Monitor 中的 Log Analytics 工作区中。

[Log Analytics](../log-query/get-started-portal.md)允许你使用[日志查询](../log-query/log-query-overview.md)，这是一项功能强大 Azure Monitor 的功能，可让你使用功能完备的查询语言对日志数据进行高级分析。 在 Azure 资源的 "**监视**" 菜单中打开 "**日志**Log Analytics，以便使用资源作为[查询范围](../log-query/scope.md#query-scope)来处理日志查询。 这样，便可以在多个表中分析仅用于该资源的数据。 使用 "Azure Monitor" 菜单中的 "**日志**" 来访问所有资源的日志。 

- 有关使用用于写入日志查询的查询语言的教程，请参阅[Azure Monitor 中的日志查询入门](../log-query/get-started-queries.md)。
- 有关如何在 Azure Monitor 日志中收集资源日志的信息以及如何在查询中访问资源日志的详细信息，请参阅在[Azure Monitor 中收集 Azure 资源日志 Log Analytics 工作区](../platform/resource-logs-collect-workspace.md)。
- 有关如何在 Azure Monitor 日志中构建资源日志数据的说明，请参阅[收集模式](../platform/resource-logs-collect-workspace.md#resource-log-collection-mode)。
- 有关 Azure Monitor 日志中的表的详细信息，请参阅每个 Azure 服务的文档。

![日志](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>通过命令行监视
你可以从命令行访问从资源收集的监视数据，或使用[Azure PowerShell](/powershell/azure/)或[Azure 命令行接口](/cli/azure/)将其包含在脚本中。 

- 请参阅[cli 指标参考](/cli/azure/monitor/metrics)，了解如何访问 cli 中的指标数据。
- 请参阅[cli Log Analytics 参考](/cli/azure/ext/log-analytics/monitor/log-analytics)，了解如何使用 cli 中的日志查询访问 Azure Monitor 记录数据。
- 请参阅[Azure PowerShell 指标参考](/powershell/module/azurerm.insights/get-azurermmetric)，了解如何从 Azure PowerShell 访问指标数据。
- 请参阅[Azure PowerShell 日志查询参考](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery)，了解如何使用 Azure PowerShell 的日志查询访问 Azure Monitor 记录数据。

## <a name="monitoring-from-rest-api"></a>REST API 中的监视
包括使用 REST API 从自定义应用程序中的资源收集的监视数据。

- 有关从 Azure Monitor REST API 访问指标的详细信息，请参阅[Azure Monitoring REST API 演练](../platform/rest-api-walkthrough.md)。
- 请参阅[Azure Log Analytics REST API](https://dev.loganalytics.io/) ，了解如何使用 Azure PowerShell 中的日志查询访问 Azure Monitor 日志数据。

## <a name="alerts"></a>警报
当在监视数据中发现重要条件时，[警报](../platform/alerts-overview.md)会主动通知你并可能采取措施。 您将创建一个警报规则，该规则定义警报的目标、是否创建警报的条件，以及要执行的任何响应的操作。

不同类型的监视数据用于不同种类的警报规则。

- [活动日志警报](../platform/alerts-activity-log.md)-在与特定条件匹配的活动日志中创建项时创建警报。 这允许您在创建特定类型的资源或配置更改失败时得到通知。
- [指标警报](../platform/alerts-metric.md)-如果指标值超过特定阈值，则创建警报。 与其他警报相比，指标警报的响应能力更高，并可以在问题解决时自动解决。
- [日志查询警报](../platform/alerts-log.md)-定期运行日志查询并在找到特定条件时创建警报。 这使您可以跨多个数据集和执行复杂的分析。

使用资源菜单中的**警报**来查看警报并管理该资源的警报规则。 仅活动日志警报和指标警报使用单个 Azure 资源作为目标。 日志查询警报将 Log Analytics 工作区用作目标，并基于可访问存储在该工作区中的任何日志的查询。 使用 "Azure Monitor" 菜单查看和管理所有资源的警报和 "管理日志查询" 警报规则。

- 有关创建警报规则的详细信息，请参阅上述不同类型警报的文章。
- 有关创建操作组的详细信息，请参阅[在 Azure 门户中创建和管理操作组](../platform/action-groups.md)，以管理对警报的响应。



## <a name="next-steps"></a>后续步骤

* 有关不同 Azure 服务的资源日志的详细信息，请参阅[Azure 资源日志支持的服务、架构和类别](../platform/diagnostic-logs-schema.md)。  
