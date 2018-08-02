---
title: 收集 Azure 中的监视数据 | Microsoft Docs
description: 概述从 Azure 中的应用程序和服务处收集的监视数据以及用于分析这些数据的工具。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: bwren
ms.openlocfilehash: efbf0907f3ed75957159c38a536bd31e88a0dbb3
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213293"
---
# <a name="collecting-monitoring-data-in-azure"></a>收集 Azure 中的监视数据
本文概述从 Azure 中的应用程序和服务处收集的监视数据以及用于分析这些数据的工具。 

## <a name="types-of-monitoring-data"></a>监视数据的类型
所有监视数据均分为两个基本类型：指标或日志。 每种类型都有独特的特征，并且有最适合的特定方案，如下所述。

### <a name="metrics"></a>度量值
指标是数字值，用于描述系统某些方面在特定时间点的情况。 指标包含独特的数据，包括值本身、收集值的时间、值代表的度量的类型，以及与值关联的特定资源。 不管值是否变化，指标都按固定的时间间隔进行收集。 例如，可以每分钟从虚拟机收集一次处理器利用率，也可以每 10 分钟收集一次登录到应用程序的用户数。

指标是轻型的，可以支持近实时方案。 指标特别适用于警报，因为指标可以频繁采样，而警报则可以使用相对简单的逻辑快速触发。 例如，可以在某个指标超出阈值时触发警报，也可以在两个指标的值的差异达到特定值时触发警报。

通常情况下，单个指标本身提供的信息很少， 其提供的单个值没有任何上下文，仅仅是与某个简单的阈值进行比较。 但是，如果能够与其他用于确定模式和趋势的指标组合使用，或者与提供特定值上下文的日志组合使用，则单个指标会很有用。 例如，不能通过应用程序在给定时间的具体用户数来判断应用程序的运行状况。 但是，如果同一指标的多个值指示用户数下降，则说明可能存在问题。 如果应用程序引发的异常过多，而且某个单独的指标也指示存在这种情况，则可能说明是应用程序问题导致用户数下降。 分析应用程序创建的表明其特定组件存在故障的事件，也许就可以确定根本原因。

基于日志的警报不像基于指标的警报那样响应迅速，但可以包括更复杂的逻辑。 如果某个查询能够对多个源的数据进行复杂的分析，则可根据该查询的结果创建警报。

### <a name="logs"></a>日志
日志包含不同类型的已经整理成记录的数据，每种类型都有不同的属性集。 日志可能包含数字值（例如指标），但通常包含带详细说明的文本数据。 日志不同于指标之处还在于，日志有结构差异，且通常不按固定时间间隔收集。

常见的日志项目类型是事件。 事件只是偶尔收集，因为事件是由应用程序或服务创建的，通常包含足够的信息，其本身提供的上下文已经很完整。  例如，事件可能会指示特定资源已创建或修改、新主机开始响应流量增高的情况，或者在应用程序中检测到了错误。

如果需要将多个源的数据组合起来进行复杂的分析并推断一段时间的趋势，则日志特别有用。 考虑到数据的格式可能有差异，应用程序可以使用所需结构创建自定义日志。 甚至可以在日志中复制指标，以便将其与其他监视数据组合起来，进行趋势推断和其他数据分析。


## <a name="monitoring-tools-in-azure"></a>Azure 中的监视工具
可以使用以下部分介绍的多个源来收集和分析 Azure 中的监视数据。

### <a name="azure-metrics"></a>Azure 指标
来自 Azure 资源和应用程序的指标会收集到 Azure 指标中。 指标数据集成到 Azure 门户的特定 Azure 资源（例如虚拟机）的页面中，此类页面包含所选计算机的 CPU 和网络利用率等指标的图形。 也可通过[指标资源管理器](../monitoring-and-diagnostics/monitoring-metric-charts.md)来分析指标数据，该管理器可以根据一段时间内多个指标的值来绘制图表。  可以通过交互方式查看图表，也可以将其固定到某个仪表板，与其他可视化效果一起查看。 也可通过 [Azure 监视 REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md) 检索指标。

若要详细了解不同类型的 Azure 资源收集的指标数据，可参阅 [Azure 中监视数据的源](monitoring-data-sources.md)。 

![指标资源管理器](media/monitoring-data-collection/metrics-explorer.png)


### <a name="azure-activity-log"></a>Azure 活动日志 
[Azure 活动日志](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)存储有关 Azure 服务配置和运行状况的日志。 可以使用活动日志资源管理器在 Azure 门户中查看这些日志，但通常会将这些日志[复制到 Log Analytics](../log-analytics/log-analytics-activity.md) 中，与其他日志数据一起进行分析。

可以使用活动日志资源管理器查看按特定条件筛选的活动日志。  大多数资源在其 Azure 门户的菜单中还会有一个活动日志选项，用于显示针对该资源筛选的活动日志资源管理器。 也可通过 [Azure 监视 REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md) 检索活动日志。

![活动日志资源管理器](media/monitoring-data-collection/activity-log-explorer.png)


### <a name="log-analytics"></a>Log Analytics
Log Analytics 提供适合在 Azure 中进行管理的通用数据平台。 它是用于在 Azure 中存储和分析日志的主要服务，可以从各种源（包括虚拟机上的代理、管理解决方案以及不同的 Azure 资源）收集数据。 可以将其他源（包括指标和活动日志）的数据复制到 Log Analytics 中，以便创建一个完整的中心存储库来存储监视数据。

Log Analytics 可以通过丰富的查询语言来分析收集的数据。  可以通过[日志搜索门户](../log-analytics/log-analytics-log-search-portals.md)以交互方式编写和测试查询并分析其结果。 也可[创建视图](../log-analytics/log-analytics-view-designer.md)来可视化日志搜索的结果，或者将查询的结果直接粘贴到 Azure 仪表板中。  管理解决方案可以将日志搜索和视图包括在 Log Analytics 中，以便分析所收集的数据。 其他服务（例如 Application Insights）可以将数据存储在 Log Analytics 中，通过其他工具进行分析。  

![日志](media/monitoring-data-collection/logs.png)

### <a name="application-insights"></a>Application Insights
Application Insights 收集安装在各种平台上的 Web 应用程序的遥测数据。 它将其数据存储在 Azure 指标和 Log Analytics 中，并在用于分析该数据的现有工具的基础上提供各种各样的工具来分析和可视化其数据。 这样你就可以利用各种常见的服务，例如警报、日志搜索以及用于进行其他监视的仪表板。


![App Insights](media/monitoring-data-collection/app-insights.png)

### <a name="service-map"></a>服务映射
服务映射以可视方式呈现虚拟机及其进程和依赖项。 它将大多数此类数据存储在 Log Analytics 中，供用户将其与其他管理数据一起分析。 在对虚拟机进行分析时，服务映射控制台也可从 Log Analytics 检索需要呈现的数据。

![服务映射](media/monitoring-data-collection/service-map.png)


## <a name="transferring-monitoring-data"></a>传输监视数据

### <a name="metrics-to-logs"></a>从指标传输到日志
指标也可以复制到 Log Analytics 中，这样就可以使用后者的丰富查询语言与其他数据类型一起进行复杂的分析。 另外，日志数据的保留时间也长于指标，适合进行一段时间的趋势推断。 将指标或任何其他的性能数据存储在 Log Analytics 中时，该数据就可以充当 日志。 可以使用指标进行近实时分析和警报发送，可以使用日志和其他数据进行趋势推断和分析。

有关如何从 Azure 资源收集指标的指南，可以参阅[收集在 Log Analytics 中使用的 Azure 服务日志和指标](../log-analytics/log-analytics-azure-storage.md)。 有关如何从 Azure PaaS 资源收集资源指标的指南，请参阅[使用 Log Analytics 配置 Azure PaaS 资源指标的收集](../log-analytics/log-analytics-collect-azurepass-posh.md)。

### <a name="logs-to-metrics"></a>从日志传输到指标
如上所述，指标的响应速度快于日志，因此在创建警报时延迟更低，成本也更低。 Log Analytics 收集的很多数字数据本来是适合指标的，但并不存储在 Azure 指标中。 一个常见的示例是从代理和管理解决方案收集的性能数据。 某些这样的数据可以复制到 Azure 指标中，以便通过指标资源管理器发送警报以及进行分析。

有关此功能的说明，请参阅 [Faster Metric Alerts for Logs now in limited public preview](https://azure.microsoft.com/blog/faster-metric-alerts-for-logs-now-in-limited-public-preview/)（针对日志的更快速指标警报现已推出限制性的公开预览版）。 如需支持的值的列表，请参阅[新型指标警报支持的指标和创建方法](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md)。

### <a name="event-hub"></a>事件中心
除了使用 Azure 中的工具来分析监视数据，也可根据需要将这些数据转发给外部工具，例如某个安全信息和事件管理 (SIEM) 产品。 这通常通过 [Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/)来完成。 

若要了解不同类型的监视数据，可参阅[将 Azure 监视数据流式传输到事件中心供外部工具使用](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)。

## <a name="next-steps"></a>后续步骤

- 了解适用于 Azure 中不同资源的[监视数据](monitoring-data-sources.md)。 