---
title: Azure Monitor 中的数据的来源 | Microsoft Docs
description: 介绍用于监视 Azure 资源及其上运行的应用程序的运行状况和性能的数据。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/23/2019
ms.openlocfilehash: b93bf4f67c2581b7cac476b83fc130bf344cfa83
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476737"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Azure Monitor 的监视数据源
Azure Monitor 基于包含[日志](data-platform.md)和[指标](data-platform-logs.md)的[通用监视数据平台](data-platform-metrics.md)。 将数据收集到此平台后，可以使用 Azure Monitor 中的一组通用工具统一分析来自多个资源的数据。 还可以将监视数据发送到其他位置以支持特定的方案，某些资源可能会将数据写入到其他位置，然后可以在日志或指标中收集数据。

本文介绍 Azure Monitor 收集的不同监视数据源，以及 Azure 资源创建的监视数据。 本文还提供了有关在不同位置收集这些数据所需的配置的详细信息的链接。

## <a name="application-tiers"></a>应用程序层

Azure 应用程序中的监视数据源可以组织为层，最高的层是应用程序本身，较低的层是 Azure 平台的组件。 从每个层访问数据的方法各不相同。 下表汇总了应用层，后续部分将介绍每个层中的监视数据源。 有关每个数据位置的说明以及如何访问其中的数据，请参阅 [Azure 中的监视数据位置](data-locations.md)。


![监视层](../media/overview/overview.png)


### <a name="azure"></a>Azure
下表概述了特定于 Azure 的应用层。 单击链接可在后续部分查看有关每个层的更多详细信息。

| 层 | 说明 | 收集方法 |
|:---|:---|:---|
| [Azure 租户](#azure-tenant) | 有关租户级 Azure 服务（例如 Azure Active Directory）操作的数据。 | 在门户中查看 AAD 数据，或者配置为使用租户诊断设置将数据收集到 Azure Monitor。 |
| [Azure 订阅](#azure-subscription) | 与 Azure 订阅中的跨资源服务运行状况和管理（例如资源管理器和服务运行状况）相关的数据。 | 在门户中查看数据，或者配置为使用日志配置文件将数据收集到 Azure Monitor。 |
| [Azure 资源](#azure-resources) |  有关每个 Azure 资源的操作和性能的数据。 | 自动收集的指标，在指标资源管理器中查看。<br>配置用于在 Azure Monitor 中收集日志的诊断设置。<br>可用于对特定资源类型进行更详细监视的监视解决方案和见解。 |

### <a name="azure-other-cloud-or-on-premises"></a>Azure、其他云或本地 
下表概述了可部署在 Azure、其他云中或本地的应用层。 单击链接可在后续部分查看有关每个层的更多详细信息。

| 层 | 说明 | 收集方法 |
|:---|:---|:---|
| [操作系统（来宾）](#operating-system-guest) | 有关计算资源上的操作系统的数据。 | 安装 Log Analytics 代理以将客户端数据源收集到 Azure Monitor，并安装依赖项代理以收集支持用于 VM 的 Azure Monitor 的依赖项。<br>对于 Azure 虚拟机，请安装 Azure 诊断扩展以将日志和指标收集到 Azure Monitor。 |
| [应用程序代码](#application-code) | 有关实际应用程序和代码的性能与功能，包括性能跟踪、应用程序日志和用户遥测数据。 | 检测代码以将数据收集到 Application Insights 中。 |
| [自定义源](#custom-sources) | 来自外部服务或者其他组件或设备的数据。 | 从任何 REST 客户端将日志或指标数据收集到 Azure Monitor 中。 |

## <a name="azure-tenant"></a>Azure 租户
与你的 Azure 租户相关的遥测数据是从租户级服务（例如 Azure Active Directory）中收集的。

![Azure 租户集合](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory 审核日志
[Azure Active Directory 报告](../../active-directory/reports-monitoring/overview-reports.md)包含特定租户中的登录活动历史记录和更改审核日志。 

| 目标 | 说明 | 参考 |
|:---|:---|:---|
| Azure Monitor 日志 | 配置要在 Azure Monitor 中收集 Azure AD 日志，以便用其他监视数据对其进行分析。 | [将 Azure AD 日志与 Azure Monitor 日志集成（预览版）](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure 存储 | 将 Azure AD 日志导出到 Azure 存储以进行存档。 | [教程：将 Azure AD 日志存档到 Azure 存储帐户（预览）](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| 事件中心 | 使用事件中心将 Azure AD 日志流式传输到其他位置。 | [教程：将 Azure Active Directory 日志流式传输到 Azure 事件中心（预览版）](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。 |



## <a name="azure-subscription"></a>Azure 订阅
与 Azure 订阅的运行状况和操作相关的遥测数据。

![Azure 订阅](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure 活动日志 
[Azure 活动日志](activity-logs-overview.md)包含服务运行状况记录，以及对 Azure 订阅中的资源所做的配置更改的记录。 活动日志可供所有 Azure 资源使用，代表其外部视图。

| 目标 | 说明 | 参考 |
|:---|:---|
| 活动日志 | 活动日志将收集到其自身的数据存储中，可以通过 Azure Monitor 菜单查看这些日志，或使用它来创建活动日志警报。 | [在 Azure 门户中查询活动日志](activity-log-view.md#azure-portal) |
| Azure Monitor 日志 | 配置 Azure Monitor 日志来收集活动日志，以结合其他监视数据对其进行分析。 | [收集和分析 Azure Monitor 的 Log Analytics 工作区中的 Azure 活动日志](activity-log-collect.md) |
| Azure 存储 | 将活动日志导出到 Azure 存储进行存档。 | [存档活动日志](activity-log-export.md#archive-activity-log)  |
| 事件中心 | 使用事件中心将活动日志流式传输到其他位置 | [将活动日志流式传输到事件中心](activity-log-export.md#stream-activity-log-to-event-hub) |

### <a name="azure-service-health"></a>Azure 服务运行状况
[Azure 服务运行状况](../../service-health/service-health-overview.md)提供订阅中应用程序和资源所依赖的 Azure 服务的运行状况信息。

| 目标 | 说明 | 参考 |
|:---|:---|:---|
| 活动日志<br>Azure Monitor 日志 | 服务运行状况记录存储在 Azure 活动日志中，因此，可以在 Azure 门户中查看这些记录，或者像处理活动日志时一样对这些记录执行任何其他活动。 | [使用 Azure 门户查看服务运行状况通知](service-notifications.md) |


## <a name="azure-resources"></a>Azure 资源
指标和资源日志提供有关 Azure 资源的_内部_操作的信息。 这些日志适用于大多数 Azure 服务，监视解决方案和见解将收集特定服务的其他数据。

![Azure 资源收集](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>平台指标 
大多数 Azure 服务会将[平台指标](data-platform-metrics.md)直接发送到指标数据库，这些指标反映了其性能和运行情况。 具体[指标因资源类型而异](metrics-supported.md)。 

| 目标 | 说明 | 参考 |
|:---|:---|:---|
| Azure Monitor 指标 | 平台指标将写入到 Azure Monitor 指标数据库，无需进行任何配置。 从指标资源管理器访问平台指标。  | [Azure 指标资源管理器入门](metrics-getting-started.md)<br>[Azure Monitor 支持的指标](metrics-supported.md) |
| Azure Monitor 日志 | 将平台指标复制到日志中，以使用 Log Analytics 进行趋势分析和其他分析。 | [将 Azure 诊断定向到 Log Analytics](resource-logs-collect-workspace.md) |
| 事件中心 | 使用事件中心将指标流式传输到其他位置。 |[将 Azure 监视数据流式传输到事件中心以便外部工具使用](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>资源日志
[资源日志](resource-logs-overview.md)可让你深入了解 Azure 资源的_内部_操作。  资源日志是自动创建的，但您必须创建诊断设置，以便为每个资源指定收集的目标。

资源日志的配置要求和内容因资源类型而异，但并不是所有服务都创建它们。 有关每个服务的详细信息，请参阅[Azure 资源日志支持的服务、架构和类别](diagnostic-logs-schema.md)，并链接到详细的配置过程。 如果该服务未在本文中列出，则该服务当前不会创建资源日志。

| 目标 | 说明 | 参考 |
|:---|:---|:---|
| Azure Monitor 日志 | 将资源日志发送到 Azure Monitor 日志，以便与其他收集的日志数据进行分析。 | [在 Azure Monitor 中 Log Analytics 工作区收集 Azure 资源日志](resource-logs-collect-storage.md) |
| 存储 | 将资源日志发送到 Azure 存储以进行存档。 | [存档 Azure 资源日志](resource-logs-collect-workspace.md) |
| 事件中心 | 使用事件中心将资源日志流式传输到其他位置。 |[将 Azure 资源日志流式传输到事件中心](resource-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>操作系统（来宾）
Azure 中的、其他云中的以及本地的计算资源都有要监视的来宾操作系统。 安装一个或多个代理后，可将来宾发出的遥测数据收集到 Azure Monitor 中，以使用与 Azure 服务本身相同的监视工具对其进行分析。

![Azure 计算资源收集](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Azure 诊断扩展
启用适用于 Azure 虚拟机的 Azure 诊断扩展可从 Azure 计算资源（包括 Azure 云服务（经典）Web 角色与辅助角色、虚拟机、虚拟机规模集及 Service Fabric）的来宾操作系统收集日志和指标。

| 目标 | 说明 | 参考 |
|:---|:---|:---|
| 存储 | 启用诊断扩展后，默认情况下，它会将数据写入存储帐户。 | [在 Azure 存储中存储和查看诊断数据](diagnostics-extension-to-storage.md) |
| Azure Monitor 指标 | 配置用于收集性能计数器的诊断扩展时，这些计数器将写入到 Azure Monitor 指标数据库。 | [使用 Windows 虚拟机的资源管理器模板将来宾 OS 指标发送到 Azure Monitor 指标存储](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Application Insights 日志 | 从支持应用程序的计算资源收集日志和性能计数器，以结合其他应用程序数据对其进行分析。 | [将云服务、虚拟机或 Service Fabric 诊断数据发送到 Application Insights](diagnostics-extension-to-application-insights.md) |
| 事件中心 | 配置诊断扩展，以使用事件中心将数据流式传输到其他位置。  | [使用事件中心流式处理热路径中的 Azure 诊断数据](diagnostics-extension-stream-event-hubs.md) |

### <a name="log-analytics-agent"></a>Log Analytics 代理 
安装 Log Analytics 代理，以便对 Windows 或 Linux 虚拟机进行全面的监视和管理。 虚拟机可以在 Azure 中运行，也可以在其他云中或本地运行。

| 目标 | 说明 | 参考 |
|:---|:---|:---|
| Azure Monitor 日志 | Log Analytics 代理可以直接连接到 Azure Monitor，也可以通过 System Center Operations Manager 进行连接，可用于从配置的数据源收集数据，或者从监视解决方案收集数据，以便提供在虚拟机上运行的应用程序的更多见解。 | [Azure Monitor 中的代理数据源](agent-data-sources.md)<br>[将 Operations Manager 连接到 Azure Monitor](om-agents.md) |


### <a name="azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor 
[用于 VM 的 Azure Monitor](../insights/vminsights-overview.md) 为虚拟机提供自定义的监视体验，对 Azure Monitor 核心功能做了补充，包括服务状态和 VM 运行状况的监视。 它要求在 Windows 和 Linux 虚拟机上安装 Dependency Agent，并与 Log Analytics 代理集成，以收集有关虚拟机上运行的进程和外部进程依赖项的发现数据。

| 目标 | 说明 | 参考 |
|:---|:---|:---|
| Azure Monitor 日志 | 存储有关代理中的进程和依赖项的数据。 | [使用用于 VM 的 Azure Monitor（预览版）映射了解应用程序组件](../insights/vminsights-maps.md) |
| VM 存储 | 用于 VM 的 Azure Monitor 在自定义位置存储运行状态信息。 这仅适用于 Azure 门户以及 [Azure 资源运行状况 REST API](/rest/api/resourcehealth/) 中的用于 VM 的 Azure Monitor。 | [了解 Azure 虚拟机的运行状况](../insights/vminsights-health.md)<br>[Azure 资源运行状况 REST API](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>应用程序代码
Azure Monitor 中的详细应用程序监视是通过 [Application Insights](https://docs.microsoft.com/azure/application-insights/) 完成的，该服务可从各种平台上运行的应用程序收集数据。 此应用程序可以在 Azure 中运行，也可以在其他云中或本地运行。

![应用程序数据收集](media/data-sources/applications.png)


### <a name="application-data"></a>应用程序数据
通过安装检测包为应用程序启用 Application Insights 后，它就会收集与应用程序的性能和运行相关的指标和日志。 Application Insights 将它收集的数据存储在其他数据源使用的同一 Azure Monitor 数据平台中。 它包括各种用于分析此数据的工具，但是也可使用指标资源管理器和 Log Analytics 之类的工具分析来自其他源的数据。

| 目标 | 说明 | 参考 |
|:---|:---|:---|
| Azure Monitor 日志 | 有关应用程序的运行数据，包括页面查看次数、应用程序请求数、异常数和跟踪数。 | [在 Azure Monitor 中分析日志数据](../log-query/log-query-overview.md) |
|                    | 应用程序组件之间的依赖关系信息，用于支持应用程序映射和遥测关联。 | [Application Insights 中的遥测关联](../app/correlation.md) <br> [应用程序映射](../app/app-map.md) |
|            | 可用性测试（从公共 Internet 上的不同位置测试应用程序的可用性和响应能力）的结果。 | [监视任何网站的可用性和响应能力](../app/monitor-web-app-availability.md) |
| Azure Monitor 指标 | Application Insights 将描述应用程序的性能和运行情况的指标以及在应用程序中定义的自定义指标收集到 Azure Monitor 指标数据库中。 | [Application Insights 中基于日志的指标和预先聚合的指标](../app/pre-aggregated-metrics-log-metrics.md)<br>[用于处理自定义事件和指标的 Application Insights API](../app/api-custom-events-metrics.md) |
| Azure 存储 | 将应用程序数据发送到 Azure 存储进行存档。 | [从 Application Insights 导出遥测数据](../app/export-telemetry.md) |
|            | 可用性测试的详细信息存储在 Azure 存储中。 可使用 Azure 门户中 Application Insights 将其下载到本地进行分析。 可用性测试的结果存储在 Azure Monitor 日志中。 | [监视任何网站的可用性和响应能力](../app/monitor-web-app-availability.md) |
|            | Profiler 跟踪数据存储在 Azure 存储中。 可使用 Azure 门户中 Application Insights 将其下载到本地进行分析。  | [使用 Application Insights 探查 Azure 中的生产应用程序](../app/profiler-overview.md) 
|            | 针对一部分异常捕获的调试快照数据存储在 Azure 存储中。 可使用 Azure 门户中 Application Insights 将其下载到本地进行分析。  | [快照的工作原理](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>监视解决方案和见解
[监视解决方案](../insights/solutions.md)和[见解](../insights/insights-overview.md)通过收集数据来提供针对特定服务或应用程序的更多操作见解。 它们可以解决不同应用层甚至多个层中的资源需求。

### <a name="monitoring-solutions"></a>监视解决方案

| 目标 | 说明 | 参考
|:---|:---|:---|
| Azure Monitor 日志 | 监视解决方案将数据收集到 Azure Monitor 日志中，以便在其中使用查询语言或通常包括在解决方案中的[视图](view-designer.md)对这些数据进行分析。 | [Azure 中的监视解决方案的数据收集详细信息](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>用于容器的 Azure Monitor
[容器 Azure Monitor](../insights/container-insights-overview.md)提供[Azure KUBERNETES Service （AKS）](/azure/aks/)的自定义监视体验。 它会收集有关这些资源的其他数据，如下表中所述。

| 目标 | 说明 | 参考 |
|:---|:---|:---|
| Azure Monitor 日志 | 存储 AKS 的监视数据，包括库存、日志和事件。 指标数据还会存储在“日志”中，这样就可以在门户中利用其分析功能。 | [使用适用于容器的 Azure Monitor 了解 AKS 群集性能](../insights/container-insights-analyze.md) |
| Azure Monitor 指标 | 指标数据将存储在指标数据库中，以驱动可视化和警报。 | [在指标资源管理器中查看容器指标](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes 服务 | 提供对门户中的 Azure Kubernetes 服务（AKS）容器日志（stdout/stderror）、事件和 pod 指标的直接访问。 | [如何实时查看 Kubernetes 日志、事件和 pod 指标](../insights/container-insights-livedata-overview.md) |

### <a name="azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor
[用于 VM 的 Azure Monitor](../insights/vminsights-overview.md) 提供自定义的体验用于监视虚拟机。 前面的[操作系统（来宾）](#operating-system-guest)部分介绍了用于 VM 的 Azure Monitor 收集的数据。

## <a name="custom-sources"></a>自定义源
除了应用程序的标准层之外，还可能需要监视具有不能与其他数据源一起收集的遥测数据的其他资源。 对于这些资源，可使用 Azure Monitor API 将此数据写入到指标或日志。

![自定义收集](media/data-sources/custom.png)

| 目标 | 方法 | 说明 | 参考 |
|:---|:---|:---|:---|
| Azure Monitor 日志 | 数据收集器 API | 从任何 REST 客户端收集日志数据，并将其存储在 Log Analytics 工作区中。 | [使用 HTTP 数据收集器 API（公共预览版）将日志数据发送到 Azure Monitor](data-collector-api.md) |
| Azure Monitor 指标 | 自定义指标 API | 从任何 REST 客户端收集指标数据，并将其存储在 Azure Monitor 指标数据库中。 | [使用 REST API 将 Azure 资源的自定义指标发送到 Azure Monitor 指标存储](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>其他服务
Azure 中的其他服务将数据写入到 Azure Monitor 数据平台。 这样，你便可以结合 Azure Monitor 收集的数据来分析这些服务收集的数据，并利用相同的分析和可视化工具。

| 服务 | 目标 | 说明 | 参考 |
|:---|:---|:---|:---|
| [Azure 安全中心](/azure/security-center/) | Azure Monitor 日志 | Azure 安全中心将它收集的安全数据存储在 Log Analytics 工作区中，可以结合 Azure Monitor 收集的其他日志数据对其进行分析。  | [Azure 安全中心中的数据收集](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](/azure/sentinel/) | Azure Monitor 日志 | Azure Sentinel 将它从不同数据源收集的数据存储在 Log Analytics 工作区中，可以结合 Azure Monitor 收集的其他日志数据对其进行分析。  | [连接数据源](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Monitor 收集的监视数据的类型](data-platform.md)以及如何查看和分析此数据。
- 列出 [Azure 资源存储数据的不同位置](data-locations.md)以及数据的访问方式。 
