---
title: 使用 Azure Monitor 监视 Azure 资源 | Microsoft Docs
description: 介绍如何使用 Azure Monitor 从 Azure 中的资源收集和分析监视数据。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 8072dec6b5f5dbd35c86573dd43d3925a271bc43
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849665"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>使用 Azure Monitor 监视 Azure 资源
如果你有关键应用程序和业务流程依赖于 Azure 资源，则需要监视这些资源的可用性、性能和操作。 本文描述了 Azure 资源生成的监视数据，介绍如何使用 Azure Monitor 的功能对此数据分析和发出警报。

> [!IMPORTANT]
> 本文适用于 Azure 中使用 Azure Monitor 的所有服务。 计算资源（包括虚拟机和应用服务）如此处所述会生成相同的监视数据，但也有可能生成日志和指标的来宾操作系统。 要详细了解如何收集和分析此数据，请参阅这些服务的监视文档。

## <a name="what-is-azure-monitor"></a>什么是 Azure Monitor？
Azure Monitor 是 Azure 中的一项完整堆栈监视服务，它提供了一组完整的功能来监视 Azure 资源以及其他云中和本地的资源。 [Azure Monitor 数据平台](../platform/data-platform.md)会将数据收集到[日志](../platform/data-platform-logs.md)和[指标](../platform/data-platform-metrics.md)中，在这里使用一组完整的监视工具对数据进行整体分析，如以下各部分所述。

- [可对 Azure Monitor 指标执行哪些操作？](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [可以对 Azure Monitor 日志执行哪些操作？](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

创建 Azure 资源后，Azure Monitor 会立即启用并开始收集指标和活动日志，你可在 [Azure 门户中查看和分析](#monitoring-in-the-azure-portal)这些内容。 通过某些配置，你可收集其他监视数据和启用其他功能。 要详细了解任何配置要求，请参阅下面的[监视数据](#monitoring-data)。


## <a name="costs-associated-with-monitoring"></a>与监视相关的成本
分析默认收集的监视数据不会产生任何费用。 其中包括：

- 收集平台指标并使用指标资源管理器对其进行分析。
- 收集活动日志并在 Azure 门户中对其进行分析。
- 创建活动日志警报规则。

收集和导出日志及指标不会产生 Azure Monitor 费用，但可能存在与目标相关的成本，其中包括：

- 在 Log Analytics 工作区中收集日志和指标时，与数据引入和保留相关的成本。 请参阅 [Azure Monitor 对 Log Analytics 的定价](https://azure.microsoft.com/pricing/details/monitor/)。
- 将日志和指标收集放入 Azure 存储帐户时，与数据存储相关的成本。 请参阅 [Azure 存储对 Blob 存储的定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。
- 将日志和指标转发到 Azure 事件中心时，与事件中心流式传输相关的成本。 请参阅 [Azure 事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。

可能存在与以下操作相关的 Azure Monitor 成本。 请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)：

- 运行日志查询。
- 创建指标或日志查询警报规则。
- 基于任何警报规则发送通知。
- 通过 API 访问指标。

## <a name="monitoring-data"></a>监视数据
Azure 中的资源会生成[日志](../platform/data-platform-logs.md)和[指标](../platform/data-platform-metrics.md)，如下图所示。 请参阅每项 Azure 服务的相关文档，了解它们生成的特定数据以及提供的任何其他解决方案或见解。

![概述](media/monitor-azure-resource/logs-metrics.png)



- [平台指标](../platform/data-platform-metrics.md) - 定期自动收集的数值，描述了资源在特定时间的某个方面。 
- [资源日志](../platform/platform-logs-overview.md) - 提供在 Azure 资源内（数据平面）执行的操作的见解，例如从 Key Vault 获取机密或向数据库发出请求。 资源日志的内容和结构因 Azure 服务和资源类型而异。
- [活动日志](../platform/platform-logs-overview.md) - 提供从外部（管理平面）对订阅中的各项 Azure 资源执行操作的见解，例如创建新的资源或启动虚拟机。 该信息显示了对订阅中的资源执行的任何写入操作（PUT、POST、DELETE）的执行内容、执行者和执行时间。


## <a name="configuration-requirements"></a>配置要求

### <a name="configure-monitoring"></a>配置监视
某些监视数据是自动收集的，但可能需要根据你的要求执行一些配置。 有关每种类型的监视数据的特定信息，请参阅下面的信息。

- [平台指标](../platform/data-platform-metrics.md) - 平台指标会自动收集到 [Azure Monitor 指标](../platform/data-platform-metrics.md)中，无需进行任何配置。 可创建诊断设置，将条目发送到 Azure Monitor 日志，或将其转发到 Azure 之外。
- [资源日志](../platform/platform-logs-overview.md) - 资源日志由 Azure 资源自动生成，但如果没有诊断设置，则不会收集。  可创建诊断设置，将条目发送到 Azure Monitor 日志，或将其转发到 Azure 之外。
- [活动日志](../platform/platform-logs-overview.md) - 自动收集活动日志，无需任何配置，可在 Azure 门户中查看。 可创建诊断设置，将它们复制到 Azure Monitor 日志，或将其转发到 Azure 之外。

### <a name="log-analytics-workspace"></a>Log Analytics 工作区
要将数据收集到 Azure Monitor 日志中，需要使用 Log Analytics 工作区。 可通过创建新的工作区来快速开始监视服务，但使用从其他服务收集数据的工作区可能会对你有所帮助。 请参阅[在 Azure 门户中创建 Log Analytics 工作区](../learn/quick-create-workspace.md)，详细了解如何创建工作区和[设计 Azure Monitor 日志部署](../platform/design-logs-deployment.md)，来帮助确定满足你要求的最佳工作区设计。 如果你使用组织中现有的工作区，则需要具备[在 Azure Monitor 中管理对日志数据和工作区的访问](../platform/manage-access.md)中所述的相应权限。 





## <a name="diagnostic-settings"></a>诊断设置
诊断设置会定义特定资源的资源日志和指标应发送到的位置。 可能的目标包括：

- [Log Analytics 工作区](../platform/resource-logs-collect-workspace.md)，通过它可使用功能强大的日志查询借助 Azure Monitor 收集的其他监视数据来分析数据，还可利用日志警报和可视化效果等其他 Azure Monitor 功能。 
- [事件中心](../platform/resource-logs-stream-event-hubs.md)，用于将数据流式传输到外部系统，例如第三方 SIEM 和其他日志分析解决方案。 
- [Azure 存储帐户](../platform/resource-logs-collect-storage.md)，适用于审核、静态分析或备份。

按照[创建诊断设置以收集 Azure 中的平台日志和指标](../platform/diagnostic-settings.md)中的过程，通过 Azure 门户创建和管理诊断设置。 请参阅[使用资源管理器模板在 Azure 中创建诊断设置](../platform/diagnostic-settings-template.md)，在模板中定义这些设置，并在创建资源时对其启用全面监视。


## <a name="monitoring-in-the-azure-portal"></a>在 Azure 门户中监视
 可通过 Azure 门户中的资源菜单访问大多数 Azure 资源的监视数据。 这样，你就可以使用标准 Azure Monitor 工具访问单个资源的数据。 某些 Azure 服务将提供不同的选项，因此应参考相应服务的文档了解其他信息。 使用“Azure Monitor”菜单来分析所有受监视资源中的数据。 

### <a name="overview"></a>概述
许多服务的“概述”页中会包含监视数据，以提供关于操作的快速概览。 这通常会基于 Azure Monitor 指标中存储的平台指标子集。 其他监视选项通常在“服务”>“菜单”的“监视”部分中提供 。

![概述页](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>见解和解决方案 
某些服务将提供超出 Azure Monitor 标准功能的工具。 [见解](../insights/insights-overview.md)提供基于 Azure Monitor 数据平台和标准功能构建的自定义监视体验。 [解决方案](../insights/solutions.md)提供基于 Azure Monitor 日志构建的预定义监视逻辑。 

如果服务有 Azure Monitor 见解，可通过每项资源的菜单中的“监视”访问它。 可通过“Azure Monitor”菜单访问所有见解和解决方案。

![见解](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>指标
在 Azure 门户中使用[指标资源管理器](../platform/metrics-getting-started.md)分析指标，该管理器在大多数服务的“指标”菜单项中提供。 借助此工具，可使用单个指标或组合使用多个指标来确定相关性和趋势。 

- 请参阅 [Azure 指标资源管理器入门](../platform/metrics-getting-started.md)，了解有关使用指标资源管理器的基本信息。
- 请参阅 [Azure 指标资源管理器的高级功能](../platform/metrics-charts.md)，了解指标资源管理器的高级功能，例如使用多个指标以及应用筛选器和拆分。

![指标](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>活动日志 
在 Azure 门户中查看活动日志中的条目，并将初始筛选器设置为当前资源。 将活动日志复制到 Log Analytics 工作区进行访问，以便在日志查询和工作簿中使用它。 

- 请参阅[查看和检索 Azure 活动日志事件](../platform/activity-log-view.md)，详细了解如何使用各种方法查看活动日志和检索条目。
- 请参阅 Azure 服务的文档，了解所记录的特定事件。

![活动日志](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure Monitor 日志
Azure Monitor 日志整合了来自多项服务和其他数据源的日志和指标，以便使用强大的查询工具进行分析。 如上所述，请创建诊断设置，将平台指标、活动日志和资源日志收集放入 Azure Monitor 中的 Log Analytics 工作区。

可通过 [Log Analytics](../log-query/get-started-portal.md) 使用[日志查询](../log-query/log-query-overview.md)，这是 Azure Monitor 的一项强大功能，可使用功能完备的查询语言对日志数据进行高级分析。 从 Azure 资源的“监视”菜单中的“日志”打开 Log Analytics，将资源用作[查询范围](../log-query/scope.md#query-scope)来处理日志查询 。 这样可跨多个表仅分析该资源的数据。 使用 Azure Monitor 菜单中的“日志”来访问所有资源的日志。 

- 请参阅 [Azure Monitor 中的日志查询入门](../log-query/get-started-queries.md)，通过教程了解如何使用编写日志查询时所用的查询语言。
- 请参阅[在 Azure Monitor 的 Log Analytics 工作区中收集 Azure 资源日志](../platform/resource-logs-collect-workspace.md)，了解如何在 Azure Monitor 日志中收集资源日志，以及如何在查询中访问这些日志。
- 请参阅[收集模式](../platform/resource-logs-collect-workspace.md#resource-log-collection-mode)，了解 Azure Monitor 日志中构建资源日志数据的方式。
- 请参阅每项 Azure 服务的相关文档，详细了解 Azure Monitor 日志中的服务表。

![日志](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>通过命令行监视
可通过命令行访问从资源收集的监视数据，也可使用 [Azure PowerShell](/powershell/azure/) 或 [Azure 命令行接口](/cli/azure/)包含在脚本中。 

- 要通过 CLI 访问指标数据，请参阅 [CLI 指标参考](/cli/azure/monitor/metrics)。
- 要通过 CLI 使用日志查询访问 Azure Monitor 日志数据，请参阅 [CLI Log Analytics 参考](/cli/azure/ext/log-analytics/monitor/log-analytics)。
- 要通过 Azure PowerShell 访问指标数据，请参阅 [Azure PowerShell 指标参考](/powershell/module/azurerm.insights/get-azurermmetric)。
- 要通过 Azure PowerShell 使用日志查询访问 Azure Monitor 日志数据，请参阅 [Azure PowerShell 日志查询参考](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery)。

## <a name="monitoring-from-rest-api"></a>通过 REST API 监视
使用 REST API 将从资源收集到的监视数据加入到自定义应用程序中。

- 请参阅 [Azure Monitor REST API 演练](../platform/rest-api-walkthrough.md)，详细了解如何通过 Azure Monitor REST API 访问指标。
- 请参阅 [Azure Log Analytics REST API](https://dev.loganalytics.io/)，了解如何通过 Azure PowerShell 使用日志查询访问 Azure Monitor 日志数据。

## <a name="alerts"></a>警报
在监视数据中发现重要情况时，[警报](../platform/alerts-overview.md)会主动通知你并可能采取措施。 请创建一个警报规则，用于定义警报的目标、确定是否创建警报的条件，以及要执行进行响应的任何操作。

不同类型的警报规则使用不同类型的监视数据。

- [活动日志警报](../platform/alerts-activity-log.md) - 在与特定条件匹配的活动日志中创建条目时创建警报。 这样可在创建特定类型的资源或配置更改失败时收到通知。
- [指标警报](../platform/alerts-metric.md) - 在指标值超过特定阈值时创建警报。 与其他警报相比，指标警报的响应更迅速，且可在问题得到更正时自动解决。
- [日志查询警报](../platform/alerts-log.md) - 定期运行日志查询，并在找到特定条件时创建警报。 通过此规则，可跨多个数据集执行复杂的分析。

使用资源菜单中的“警报”来查看警报并管理该资源的警报规则。 只有活动日志警报和指标警报使用单个 Azure 资源作为目标。 日志查询警报将 Log Analytics 工作区用作目标，并基于可访问该工作区中存储的任何日志的查询。 使用 Azure Monitor 菜单查看和管理所有资源的警报并管理日志查询警报规则。

- 请参阅上述不同类型警报的相关文章，详细了解如何创建警报规则。
- 请参阅[在 Azure 门户中创建和管理操作组](../platform/action-groups.md)，详细了解如何创建允许管理警报响应的操作组。



## <a name="next-steps"></a>后续步骤

* 请参阅 [Azure 资源日志支持的服务、架构和类别](../platform/diagnostic-logs-schema.md)，了解不同 Azure 服务的资源日志。  
