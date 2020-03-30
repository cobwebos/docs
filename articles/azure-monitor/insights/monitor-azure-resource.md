---
title: 使用 Azure 监视器监视 Azure 资源 |微软文档
description: 介绍如何使用 Azure 监视器从 Azure 中的资源收集和分析监视数据。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 01d188e0e39888297ff8d6a57129a3a17e1654fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249264"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>使用 Azure 监视器监视 Azure 资源
当依赖于 Azure 资源的关键应用程序和业务流程时，需要监视这些资源的可用性、性能和操作。 本文介绍了 Azure 资源生成的监视数据，以及如何使用 Azure 监视器的功能来分析和警报此数据。

> [!IMPORTANT]
> 本文适用于 Azure 中使用 Azure 监视器的所有服务。 计算资源（包括 VM 和应用服务）生成此处描述的相同监视数据，但还有一个来宾操作系统，该操作系统也可能生成日志和指标。 有关如何收集和分析此数据的详细信息，请参阅这些服务的监视文档。

## <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？
Azure 监视器是 Azure 中的完整堆栈监视服务，它提供了一整套功能来监视 Azure 资源，以及其他云和本地的资源。 [Azure 监视器数据平台](../platform/data-platform.md)将数据收集到[日志](../platform/data-platform-logs.md)和[指标中，](../platform/data-platform-metrics.md)可以使用以下各节所述的一组完整的监视工具一起分析这些数据。

- [可对 Azure Monitor 指标执行哪些操作？](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [可对 Azure Monitor 日志执行哪些操作？](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

创建 Azure 资源后，Azure 监视器即启用并开始收集指标和活动日志，您可以在[Azure 门户中查看和分析](#monitoring-in-the-azure-portal)这些指标和活动日志。 通过某些配置，您可以收集其他监视数据并启用其他功能。 有关任何配置要求的详细信息，请参阅下面的[监视数据](#monitoring-data)。


## <a name="costs-associated-with-monitoring"></a>与监视相关的成本
分析默认情况下收集的监视数据无需任何成本。 其中包括：

- 收集平台指标，并使用指标资源管理器对其进行分析。
- 收集活动日志并在 Azure 门户中分析它。
- 创建活动日志警报规则。

收集和导出日志和指标没有 Azure 监视器成本，但可能存在与目标相关的成本：

- 在日志分析工作区中收集日志和指标时，与数据引入和保留相关的成本。 请参阅[日志分析的 Azure 监视器定价](https://azure.microsoft.com/pricing/details/monitor/)。
- 向 Azure 存储帐户收集日志和指标时与数据存储相关的成本。 请参阅[Blob 存储的 Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。
- 将日志和指标转发到 Azure 事件中心时与事件中心流相关的成本。 请参阅[Azure 事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。

可能存在与以下内容关联的 Azure 监视器成本。 请参阅[Azure 监视器定价](https://azure.microsoft.com/pricing/details/monitor/)：

- 运行日志查询。
- 创建指标或日志查询警报规则。
- 从任何警报规则发送通知。
- 通过 API 访问指标。

## <a name="monitoring-data"></a>监视数据
Azure 中的资源生成[日志](../platform/data-platform-logs.md)和[指标](../platform/data-platform-metrics.md)，显示下图。 有关每个 Azure 服务生成的特定数据及其提供的任何其他解决方案或见解，请参阅文档。

![概述](media/monitor-azure-resource/logs-metrics.png)



- [平台指标](../platform/data-platform-metrics.md)- 定期自动收集的数字值，并描述特定时间资源的某些方面。 
- [资源日志](../platform/platform-logs-overview.md)- 提供对在 Azure 资源（数据平面）中执行的操作的见解，例如从密钥保管库获取机密或向数据库发出请求。 资源日志的内容和结构因 Azure 服务和资源类型而异。
- [活动日志](../platform/platform-logs-overview.md)- 从外部（管理平面）提供对订阅中每个 Azure 资源的操作的见解，例如创建新资源或启动虚拟机。 此信息包括订阅中资源上对资源采取的内容、操作和时间。


## <a name="configuration-requirements"></a>配置要求

### <a name="configure-monitoring"></a>配置监视
某些监视数据是自动收集的，但您可能需要根据您的要求执行某些配置。 有关每种监视数据类型的特定信息，请参阅以下信息。

- [平台指标](../platform/data-platform-metrics.md)- 平台指标自动收集到[Azure 监视器指标中](../platform/data-platform-metrics.md)，无需配置。 创建诊断设置以将条目发送到 Azure 监视器日志或将其转发到 Azure 外部。
- [资源日志](../platform/platform-logs-overview.md)- 资源日志由 Azure 资源自动生成，但无需诊断设置即可收集。  创建诊断设置以将条目发送到 Azure 监视器日志或将其转发到 Azure 外部。
- [活动日志](../platform/platform-logs-overview.md)- 活动日志会自动收集，无需配置，可以在 Azure 门户中查看。 创建诊断设置以将它们复制到 Azure 监视器日志或将其转发到 Azure 之外。

### <a name="log-analytics-workspace"></a>Log Analytics 工作区
将数据收集到 Azure 监视器日志需要日志分析工作区。 您可以通过创建新工作区快速开始监视服务，但使用从其他服务收集数据的工作区可能具有价值。 有关创建工作区和[设计 Azure 监视器日志部署](../platform/design-logs-deployment.md)以帮助确定满足需求的最佳工作区设计的详细信息，请参阅[在 Azure 门户中创建日志分析工作区](../learn/quick-create-workspace.md)。 如果在组织中使用现有工作区，则需要适当的权限，如[管理 Azure 监视器 中的日志数据和工作区的访问](../platform/manage-access.md)中所述。 





## <a name="diagnostic-settings"></a>诊断设置
诊断设置定义应发送特定资源的资源日志和指标的位置。 可能的目的地是：

- [日志分析工作区](../platform/resource-logs-collect-workspace.md)，允许您使用强大的日志查询使用 Azure 监视器收集的其他监视数据分析数据，并利用其他 Azure 监视器功能（如日志警报和可视化）。 
- [事件中心](../platform/resource-logs-stream-event-hubs.md)将数据流式传输到外部系统，如第三方 SIEM 和其他日志分析解决方案。 
- [Azure 存储帐户](../platform/resource-logs-collect-storage.md)，可用于审核、静态分析或备份。

按照["创建诊断设置"中的过程在 Azure 中收集平台日志和指标](../platform/diagnostic-settings.md)，通过 Azure 门户创建和管理诊断设置。 请参阅[使用资源管理器模板在 Azure 中创建诊断设置](../platform/diagnostic-settings-template.md)，以便在模板中定义它们，并在创建资源时启用对资源的完整监视。


## <a name="monitoring-in-the-azure-portal"></a>在 Azure 门户中监视
 可以从 Azure 门户中的资源菜单访问大多数 Azure 资源的监视数据。 这将允许您使用标准 Azure 监视器工具访问单个资源的数据。 某些 Azure 服务将提供不同的选项，因此应参考该服务的文档以获取其他信息。 使用**Azure 监视器**菜单分析来自所有受监视资源的数据。 

### <a name="overview"></a>概述
许多服务的“概述”页中会包含监视数据，以提供关于操作的快速概览****。 这通常会基于 Azure Monitor 指标中存储的平台指标子集。 其他监视选项通常可在服务的**监视**部分提供。 来访问这些模板。

![概述页](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>洞察和解决方案 
某些服务将提供超出 Azure 监视器标准功能的工具。 [Insights](../insights/insights-overview.md)提供基于 Azure 监视器数据平台和标准功能构建的自定义监视体验。 [解决方案](../insights/solutions.md)提供基于 Azure 监视器日志构建的预定义监视逻辑。 

如果服务具有 Azure 监视器见解，则可以从每个资源菜单中的 **"监视"** 访问它。 从**Azure 监视器**菜单访问所有见解和解决方案。

![洞察力](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>指标
使用[指标资源管理器](../platform/metrics-getting-started.md)分析 Azure 门户中的指标，这些指标资源管理器可从大多数服务的 **"指标"** 菜单项中获取。 此工具允许您使用单个指标或组合多个指标来识别相关性和趋势。 

- 有关使用指标资源管理器的基础知识，请参阅[使用 Azure 指标资源管理器入门](../platform/metrics-getting-started.md)。
- 有关指标资源管理器的高级功能（如使用多个指标和应用筛选器和应用拆分），请参阅[Azure 指标资源管理器的高级功能](../platform/metrics-charts.md)。

![指标](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>活动日志 
在 Azure 门户中查看活动日志中的条目，将初始筛选器设置为当前资源。 将活动日志复制到日志分析工作区以访问它，以在日志查询和工作簿中使用它。 

- 有关查看活动日志和使用各种方法检索条目的详细信息，请参阅[查看和检索 Azure 活动日志事件](../platform/activity-log-view.md)。
- 有关记录的特定事件，请参阅 Azure 服务的文档。

![活动日志](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure Monitor 日志
Azure 监视器日志使用强大的查询工具整合来自多个服务和其他数据源的日志和指标进行分析。 如上所述，创建诊断设置，将平台指标、活动日志和资源日志收集到 Azure 监视器中的日志分析工作区中。

[日志分析](../log-query/get-started-portal.md)允许您使用[日志查询](../log-query/log-query-overview.md)，这是 Azure 监视器的一个强大功能，允许您使用功能齐全的查询语言对日志数据执行高级分析。 在 **"监视"** 菜单中打开**日志**分析，以便 Azure 资源使用资源作为[查询范围](../log-query/scope.md#query-scope)处理日志查询。 这允许您仅分析多个表中的数据，以便进行该资源。 使用 Azure 监视器菜单中的**日志**访问所有资源的日志。 

- 有关使用用于编写日志查询的查询语言的教程，请参阅[Azure 监视器中的日志查询](../log-query/get-started-queries.md)入门。
- 有关如何在 Azure 监视器日志中收集资源日志的信息以及如何在查询中访问这些资源日志的详细信息，请参阅[在 Azure 监视器中的日志分析工作区中收集 Azure 资源日志](../platform/resource-logs-collect-workspace.md)。
- 有关资源日志数据在 Azure 监视器日志中的结构方式的说明，请参阅[收集模式](../platform/resource-logs-collect-workspace.md#resource-log-collection-mode)。
- 有关 Azure 监视器日志表的详细信息，请参阅每个 Azure 服务的文档。

![日志](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>从命令行进行监视
可以使用[Azure PowerShell](/powershell/azure/)或[Azure 命令行接口](/cli/azure/)访问从资源收集的监视数据，也可以包含在脚本中。 

- 有关从 CLI 访问指标数据的[CLI 指标引用](/cli/azure/monitor/metrics)，请参阅 CLI 指标引用。
- 有关使用 CLI 的日志查询访问 Azure 监视器日志数据的[CLI 日志分析参考](/cli/azure/ext/log-analytics/monitor/log-analytics)。
- 有关从 Azure PowerShell 访问指标数据的[Azure PowerShell 指标引用](/powershell/module/azurerm.insights/get-azurermmetric)。这些指标来自 Azure PowerShell。
- 有关使用 Azure PowerShell 的日志查询访问 Azure 监视器日志数据的[Azure PowerShell 日志查询引用，请参阅 Azure PowerShell 日志查询。](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery)

## <a name="monitoring-from-rest-api"></a>从 REST API 进行监视
包括使用 REST API 在自定义应用程序中从资源中收集的监视数据。

- 有关从 Azure 监视器 REST API 访问指标的详细信息，请参阅[Azure 监视 REST API 演练](../platform/rest-api-walkthrough.md)。
- 有关使用 Azure PowerShell 的日志查询访问 Azure 监视器日志数据的信息，请参阅[Azure 日志分析 REST API。](https://dev.loganalytics.io/)

## <a name="alerts"></a>警报
[警报](../platform/alerts-overview.md)主动通知您，并在监视数据中找到重要条件时可能采取行动。 创建警报规则，定义警报的目标、是否创建警报的条件以及响应时要执行的任何操作。

不同类型的监视数据用于不同类型的警报规则。

- [活动日志警报](../platform/alerts-activity-log.md)- 在活动日志中创建符合特定条件的活动日志中创建条目时创建警报。 这允许您在创建特定类型的资源或配置更改失败时通知您。
- [指标警报](../platform/alerts-metric.md)- 当指标值超过特定阈值时创建警报。 指标警报比其他警报响应更快，并且可以在问题得到纠正时自动解决。
- [日志查询警报](../platform/alerts-log.md)- 定期运行日志查询，并在发现特定条件时创建警报。 这允许您对多组数据和 执行复杂的分析。

使用资源菜单中的**警报**来查看警报并管理该资源的警报规则。 只有活动日志警报和指标警报使用单个 Azure 资源作为目标。 日志查询警报使用日志分析工作区作为目标，并且基于可以访问该工作区中存储的任何日志的查询。 使用 Azure 监视器菜单查看和管理所有资源的警报和管理日志查询警报规则。

- 有关创建警报规则的详细信息，请参阅上述不同类型的警报的文章。
- 有关创建操作组以管理警报响应的详细信息，请参阅[Azure 门户中创建和管理操作组](../platform/action-groups.md)的详细信息。



## <a name="next-steps"></a>后续步骤

* 有关不同 Azure 服务的资源日志的详细信息，请参阅[Azure 资源日志的支持服务、架构和类别](../platform/diagnostic-logs-schema.md)。  
