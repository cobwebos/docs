---
title: Azure 中的监视数据的源 | Microsoft Docs
description: 介绍用于监视 Azure 资源及其上运行的应用程序的运行状况和性能的数据。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: bwren
ms.openlocfilehash: 262099bbe45e483efd269445aa8042b30668ebe3
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036517"
---
# <a name="sources-of-monitoring-data-in-azure"></a>Azure 中的监视数据源
本文介绍用于监视 Azure 资源及其上运行的应用程序的运行状况和性能的数据。  使用[收集 Azure 中的监视数据](monitoring-data-collection.md)中介绍的工具收集和分析该数据

Azure 中的监视数据来自多个源，这些源可以整理成层，最高的层是你的应用程序，最低的层是 Azure 平台。 下图对此进行了描述，每个层在以下各部分详细说明。

![监视数据的层](media/monitoring-data-sources/monitoring-tiers.png)


## <a name="azure-platform"></a>Azure 平台
与 Azure 本身的运行状况和操作相关的遥测数据包含 Azure 订阅或租户的操作和管理数据。 它包括存储在 Azure 活动日志中的服务运行状况数据，以及 Azure Active Directory 中的审核日志。

![Azure 集合](media/monitoring-data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Azure 服务运行状况
[Azure 服务运行状况](../monitoring-and-diagnostics/monitoring-service-notifications.md)提供订阅中应用程序和资源所依赖的 Azure 服务的运行状况信息。 可以根据当前的以及预期的可能会影响应用程序的关键问题创建警报，以便到时获得通知。 服务运行状况记录存储在 [Azure 活动日志](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)中，你可以在活动日志资源管理器中查看，并将其复制到 Log Analytics 中。

### <a name="azure-activity-log"></a>Azure 活动日志
[Azure 活动日志](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)包含服务运行状况记录，以及对 Azure 资源所做的配置更改的记录。 活动日志可供所有 Azure 资源使用，代表其外部视图。 活动日志中记录的具体类型在 [Azure 活动日志事件架构](../monitoring-and-diagnostics/monitoring-activity-log-schema.md)中介绍。

可以在 Azure 门户中特定资源的页面上查看其活动日志，或在[活动日志资源管理器](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)中的多个资源处查看日志。 可以将日志项目复制到 Log Analytics 中，与其他监视数据组合在一起，这特别有用。 也可使用[事件中心](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)将其发送到其他位置。


### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory 审核日志
[Azure Active Directory 报告](../active-directory/active-directory-reporting-azure-portal.md)包含特定租户中的登录活动历史记录和更改审核日志。 目前不能将 Azure Active Directory 审核数据与其他监视数据组合在一起，因为该审核数据只能通过 Azure Active Directory 和 [Azure Active Directory 报告 API](../active-directory/active-directory-reporting-api-getting-started-azure-portal.md) 进行访问。


## <a name="azure-services"></a>Azure 服务
指标和资源级诊断日志提供 Azure 资源的内部操作的信息。 这些适用于大多数 Azure 服务。管理解决方案提供特定服务的更多见解。

![Azure 资源收集](media/monitoring-data-sources/azure-resource-collection.png)


### <a name="metrics"></a>度量值
大多数 Azure 服务会生成反映其性能和运行的指标。 具体[指标因资源类型而异](../monitoring-and-diagnostics/monitoring-supported-metrics.md)。  这些指标可以在指标资源管理器中访问，并且可以复制到 Log Analytics 中进行趋势推断和其他分析。


### <a name="resource-diagnostic-logs"></a>资源诊断日志
活动日志提供在 Azure 资源上执行的操作的信息，而资源级[诊断日志](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)则可用于了解资源本身的操作。   这些日志的配置要求和内容[因资源类型而异](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md)。

不能直接在 Azure 门户中查看诊断日志，但可以[将它们发送到 Azure 存储进行存档](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)，然后将它们导出到[事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)以便重定向到其他服务，或者导出[到 Log Analytics](../monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics.md) 进行分析。 某些资源可以直接写入到 Log Analytics 中，而另一些资源则只能先写入到存储帐户，然后再[导入 Log Analytics 中](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage)。

### <a name="management-solutions"></a>管理解决方案
 [管理解决方案](../monitoring/monitoring-solutions.md)通过收集数据来提供特定服务的更多操作见解。 这些解决方案将数据收集到 Log Analytics，以便在其中使用[查询语言](../log-analytics/log-analytics-log-search.md)或通常包括在解决方案中的视图对这些数据进行分析。

## <a name="guest-operating-system"></a>来宾操作系统
除了所有 Azure 服务生成的遥测数据，计算资源还有来宾操作系统需要监视。 安装一个或多个代理以后，即可将来宾的遥测数据收集到与 Azure 服务本身相同的监视工具中。

![Azure 计算资源收集](media/monitoring-data-sources/compute-resource-collection.png)

### <a name="diagnostic-extension"></a>诊断扩展
使用 [Azure 诊断扩展](../monitoring-and-diagnostics/azure-diagnostics.md)，可以从 Azure 计算资源的客户端操作系统收集日志和性能数据。 从客户端收集的指标和日志存储在一个 Azure 存储帐户中，可以[将 Log Analytics 配置为从该帐户导入数据](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage)。  指标资源管理器了解如何从存储帐户读取数据，并会将客户端指标与其他收集的指标包括在一起。


### <a name="log-analytics-agent"></a>Log Analytics 代理
可以将 Log Analytics 代理安装在任何 Windows 或 Linux 虚拟机或物理计算机上。 虚拟机可以在 Azure 中运行，也可以在其他云中或本地运行。  代理可以直接连接到 Log Analytics，也可以通过[连接的 System Center Operations Manager 管理组](../log-analytics/log-analytics-om-agents.md)进行连接，适用于从配置的[数据源](../log-analytics/log-analytics-data-sources.md)收集数据，或者从[管理解决方案](../monitoring/monitoring-solutions.md)收集数据，以便提供在虚拟机上运行的应用程序的更多见解。

### <a name="service-map"></a>服务映射
[服务映射](../operations-management-suite/operations-management-suite-service-map.md)要求在 Windows 和 Linux 虚拟机上有一个依赖项代理。 它可以与 Log Analytics 代理配合使用，用于收集在虚拟机上运行的进程的数据，以及收集外部进程上的依赖项的数据。 它将此数据存储在 Log Analytics 中，并且还包括一个控制台，可以直观地显示所收集的数据以及其他存储在 Log Analytics 中的数据。

## <a name="applications"></a>应用程序
除了可以通过应用程序将遥测数据写入到来宾操作系统中，还可以通过 [Application Insights](https://docs.microsoft.com/azure/application-insights/) 进行详细的应用程序监视。 Application Insights 可以从各种平台上运行的应用程序处收集数据。 此应用程序可以在 Azure 中运行，也可以在其他云中或本地运行。

![应用程序数据收集](media/monitoring-data-sources/application-collection.png)


#### <a name="application-data"></a>应用程序数据
通过安装检测包为应用程序启用 Application Insights 后，它就会收集与应用程序的性能和运行相关的指标 和日志。 这包括有关页面视图、应用程序请求和异常的详细信息。 Application Insights 将收集的数据存储在 Azure 指标和 Log Analytics 中。 它包括各种用于分析此数据的工具，但是也可使用指标资源管理器和日志搜索之类的工具分析来自其他源的数据。

也可使用 Application Insights [创建自定义指标](../application-insights/app-insights-api-custom-events-metrics.md)。  这样就可以定义你自己的逻辑，以便计算出一个数字值，然后将该值与其他可以从指标资源管理器访问的指标一起存储，用于[自动缩放](../monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md)和指标警报。

#### <a name="dependencies"></a>依赖项
若要监视应用程序的不同逻辑操作，必须[跨多个组件收集遥测数据](../application-insights/app-insights-transaction-diagnostics.md)。 Application Insights 支持[分布式遥测关联](../application-insights/application-insights-correlation.md)，该关联用于确定组件之间的依赖关系，这样就可以将它们放在一起进行分析。

#### <a name="availability-tests"></a>可用性测试
使用 Application Insights 中的[可用性测试](../application-insights/app-insights-monitor-web-app-availability.md)，可以从公共 Internet 的不同位置测试应用程序的可用性和响应能力。 可以通过简单的 ping 测试来验证应用程序是否处于活动状态，也可以使用 Visual Studio 创建一个模拟用户方案的 Web 测试。  可用性测试不需应用程序中有任何检测。

## <a name="next-steps"></a>后续步骤

- 详细了解[监视数据的类型以及用于收集和分析监视数据的 Azure 工具的类型](monitoring-data-collection.md)。
