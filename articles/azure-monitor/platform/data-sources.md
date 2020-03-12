---
title: Azure Monitor 中的数据的来源 | Microsoft Docs
description: 介绍用于监视 Azure 资源及其上运行的应用程序的运行状况和性能的数据。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/19/2019
ms.openlocfilehash: 2a5d1178bd6dbd6f7cfdd2ec2af17b78836a38d7
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096730"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Azure Monitor 的监视数据源
Azure Monitor 基于包含[日志](data-platform-logs.md)和[指标](data-platform-metrics.md)的[常见监视数据平台](data-platform.md)。 通过将数据收集到此平台，可以使用 Azure Monitor 中的一组常用工具来一起分析多个资源中的数据。 还可以将监视数据发送到其他位置以支持特定方案，并且某些资源可能会写入到其他位置，然后才能将这些数据收集到日志或指标中。

本文介绍 Azure Monitor 收集的监视数据的不同源以及 Azure 资源创建的监视数据。 提供了有关将此数据收集到不同位置所需的配置的详细信息的链接。

## <a name="application-tiers"></a>应用程序层

可以将 Azure 应用程序中的监视数据源分成不同的层级，最高的层是应用程序本身，较低的层是 Azure 平台的组成部分。 从每个层访问数据的方法有所不同。 下表中总结了应用程序层，每个层中的监视数据源将在以下各节中提供。 有关每个数据位置的说明以及如何访问数据，请参阅[监视 Azure 中的数据位置](data-locations.md)。


![监视层](../media/overview/overview.png)


### <a name="azure"></a>Azure
下表简要介绍了特定于 Azure 的应用程序层。 进一步了解以下各部分中的每个链接。

| 层 | 说明 | 收集方法 |
|:---|:---|:---|
| [Azure 租户](#azure-tenant) | 有关租户级 Azure 服务（例如 Azure Active Directory）操作的数据。 | 使用租户诊断设置在门户中查看 AAD 数据或将集合配置为 Azure Monitor。 |
| [Azure 订阅](#azure-subscription) | 与 Azure 订阅中跨资源服务的运行状况和管理有关的数据，例如资源管理器和服务运行状况。 | 在门户中查看或使用日志配置文件将集合配置为 Azure Monitor。 |
| [Azure 资源](#azure-resources) |  有关每个 Azure 资源的操作和性能的数据。 | 自动收集的指标，指标资源管理器中查看。<br>配置诊断设置以在 Azure Monitor 收集日志。<br>监视解决方案和见解，可用于对特定资源类型进行更详细的监视。 |

### <a name="azure-other-cloud-or-on-premises"></a>Azure、其他云或本地 
下表简要介绍了可能位于 Azure、其他云或本地的应用层。 进一步了解以下各部分中的每个链接。

| 层 | 说明 | 收集方法 |
|:---|:---|:---|
| [操作系统（来宾）](#operating-system-guest) | 有关计算资源上的操作系统的数据。 | 安装 Log Analytics 代理，将客户端数据源收集到 Azure Monitor 和依赖项代理中，以收集支持用于 VM 的 Azure Monitor 的依赖项。<br>对于 Azure 虚拟机，请安装 Azure 诊断扩展，以将日志和指标收集到 Azure Monitor 中。 |
| [应用程序代码](#application-code) | 有关实际应用程序和代码的性能和功能的数据，包括性能跟踪、应用程序日志和用户遥测。 | 检测你的代码以将数据收集到 Application Insights 中。 |
| [自定义源](#custom-sources) | 外部服务或其他组件或设备中的数据。 | 从任何 REST 客户端将日志或指标数据收集到 Azure Monitor。 |

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
与 Azure 订阅的运行状况和操作相关的遥测。

![Azure 订阅](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure 活动日志 
[Azure 活动日志](platform-logs-overview.md)包含服务运行状况记录以及对 Azure 订阅中的资源所做的任何配置更改的记录。 活动日志可供所有 Azure 资源使用，代表其外部视图。

| 目标 | 说明 | 参考 |
|:---|:---|
| 活动日志 | 活动日志将收集到其自己的数据存储中，你可以从 "Azure Monitor" 菜单中查看该日志，或使用创建活动日志警报。 | [在 Azure 门户中查询活动日志](activity-log-view.md#azure-portal) |
| Azure Monitor 日志 | 将 Azure Monitor 日志配置为收集活动日志，以便用其他监视数据对其进行分析。 | [收集和分析 Azure Monitor 中 Log Analytics 工作区中的 Azure 活动日志](activity-log-collect.md) |
| Azure 存储 | 将活动日志导出到 Azure 存储以进行存档。 | [存档活动日志](resource-logs-collect-storage.md)  |
| 事件中心 | 使用事件中心将活动日志流式传输到其他位置 | [将活动日志流式传输到事件中心](resource-logs-stream-event-hubs.md)。 |

### <a name="azure-service-health"></a>Azure 服务运行状况
[Azure 服务运行状况](../../service-health/service-health-overview.md)提供订阅中应用程序和资源所依赖的 Azure 服务的运行状况信息。

| 目标 | 说明 | 参考 |
|:---|:---|:---|
| 活动日志<br>Azure Monitor 日志 | 服务运行状况记录存储在 Azure 活动日志中，因此您可以在 Azure 门户中查看这些记录，也可以执行可以对活动日志执行的任何其他活动。 | [使用 Azure 门户查看服务运行状况通知](service-notifications.md) |


## <a name="azure-resources"></a>Azure 资源
指标和资源日志提供有关 Azure 资源的_内部_操作的信息。 这些服务可用于大多数 Azure 服务，并可用于监视解决方案和见解，从而为特定服务收集额外的数据。

![Azure 资源收集](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>平台指标 
大多数 Azure 服务都将发送[平台指标](data-platform-metrics.md)，以反映其性能和操作直接反映到指标数据库。 具体[指标因资源类型而异](metrics-supported.md)。 

| 目标 | 说明 | 参考 |
|:---|:---|:---|
| Azure Monitor 度量值 | 平台指标将写入 Azure Monitor 指标数据库，而不进行任何配置。 从指标资源管理器访问平台指标。  | [Azure 指标资源管理器入门](metrics-getting-started.md)<br>[Azure Monitor 支持的指标](metrics-supported.md) |
| Azure Monitor 日志 | 使用 Log Analytics 将平台指标复制到日志进行趋势分析和其他分析。 | [Azure 诊断定向到 Log Analytics](resource-logs-collect-workspace.md) |
| 事件中心 | 使用事件中心将指标流式传输到其他位置。 |[将 Azure 监视数据流式传输到事件中心以便外部工具使用](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>资源日志
[资源日志](platform-logs-overview.md)可让你深入了解 Azure 资源的_内部_操作。  资源日志是自动创建的，但您必须创建诊断设置，以便为每个资源指定收集的目标。

资源日志的配置要求和内容因资源类型而异，但并不是所有服务都创建它们。 有关每个服务的详细信息，请参阅[Azure 资源日志支持的服务、架构和类别](diagnostic-logs-schema.md)，并链接到详细的配置过程。 如果该服务未在本文中列出，则该服务当前不会创建资源日志。

| 目标 | 说明 | 参考 |
|:---|:---|:---|
| Azure Monitor 日志 | 将资源日志发送到 Azure Monitor 日志，以便与其他收集的日志数据进行分析。 | [在 Azure Monitor 中 Log Analytics 工作区收集 Azure 资源日志](resource-logs-collect-storage.md) |
| 存储 | 将资源日志发送到 Azure 存储以进行存档。 | [存档 Azure 资源日志](resource-logs-collect-workspace.md) |
| 事件中心 | 使用事件中心将资源日志流式传输到其他位置。 |[将 Azure 资源日志流式传输到事件中心](resource-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>操作系统（来宾）
Azure 中的、其他云中的以及本地的计算资源都有要监视的来宾操作系统。 安装一个或多个代理后，可以将来自来宾的遥测数据收集到 Azure Monitor 中，以便通过与 Azure 服务自身相同的监视工具对其进行分析。

![Azure 计算资源收集](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Azure 诊断扩展
通过为 Azure 虚拟机启用 Azure 诊断扩展，可以从 Azure 计算资源的来宾操作系统（包括 Azure 云服务（经典） Web 和辅助角色、虚拟机、虚拟机）收集日志和指标。规模集和 Service Fabric。

| 目标 | 说明 | 参考 |
|:---|:---|:---|
| 存储 | Azure 诊断扩展始终写入 Azure 存储帐户。 | [安装和配置 Windows Azure 诊断扩展（WAD）](diagnostics-extension-windows-install.md)<br>[使用 Linux 诊断扩展监视指标和日志](../../virtual-machines/extensions/diagnostics-linux.md) |
| Azure Monitor 度量值 | 将诊断扩展配置为收集性能计数器时，它们将写入 Azure Monitor 度量值数据库中。 | [使用 Windows 虚拟机的资源管理器模板将来宾 OS 指标发送到 Azure Monitor 指标存储](collect-custom-metrics-guestos-resource-manager-vm.md) |
| 事件中心 | 将诊断扩展配置为使用事件中心将数据流式传输到其他位置。  | [使用事件中心流式传输 Azure 诊断数据](diagnostics-extension-stream-event-hubs.md)<br>[使用 Linux 诊断扩展监视指标和日志](../../virtual-machines/extensions/diagnostics-linux.md) |
| Application Insights 日志 | 从支持您的应用程序的计算资源收集日志和性能计数器，以便与其他应用程序数据一起进行分析。 | [将云服务、虚拟机或 Service Fabric 诊断数据发送到 Application Insights](diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Log Analytics 代理 
安装 Log Analytics 代理，以全面监视和管理 Windows 或 Linux 虚拟机。 虚拟机可以在 Azure 中运行，也可以在其他云中或本地运行。

| 目标 | 说明 | 参考 |
|:---|:---|:---|
| Azure Monitor 日志 | Log Analytics 代理直接或通过 System Center Operations Manager 连接到 Azure Monitor，并使你能够从你配置的数据源或通过监视解决方案提供应用程序的更多见解的数据源中收集数据正在虚拟机上运行。 | [Azure Monitor 中的代理数据源](agent-data-sources.md)<br>[将 Operations Manager 连接到 Azure Monitor](om-agents.md) |
| VM 存储 | 用于 VM 的 Azure Monitor 使用 Log Analytics 代理在自定义位置中存储 heath 状态信息。 有关详细信息，请参阅下一部分。  |


### <a name="azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor 
[用于 VM 的 Azure Monitor](../insights/vminsights-overview.md)为虚拟机提供自定义的监视体验，提供除核心 Azure Monitor 功能之外的功能，包括服务状态和 VM 运行状况。 它需要 Windows 和 Linux 虚拟机上的 Dependency Agent，这些虚拟机与 Log Analytics 代理集成，以便收集有关虚拟机上运行的进程和外部进程依赖关系的发现数据。

| 目标 | 说明 | 参考 |
|:---|:---|:---|
| Azure Monitor 日志 | 存储有关进程和代理上的依赖项的数据。 | [使用用于 VM 的 Azure Monitor （预览）映射来了解应用程序组件](../insights/vminsights-maps.md) |
| VM 存储 | 用于 VM 的 Azure Monitor 使用 Log Analytics 代理在自定义位置中存储 heath 状态信息。 除了[Azure 资源运行状况 REST API](/rest/api/resourcehealth/)外，此功能仅适用于 Azure 门户中的用于 VM 的 Azure Monitor。 | [了解 Azure 虚拟机的运行状况](../insights/vminsights-health.md)<br>[Azure 资源运行状况 REST API](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>应用程序代码
Azure Monitor 中的详细应用程序监视使用[Application Insights](https://docs.microsoft.com/azure/application-insights/)来收集在各种平台上运行的应用程序的数据。 此应用程序可以在 Azure 中运行，也可以在其他云中或本地运行。

![应用程序数据收集](media/data-sources/applications.png)


### <a name="application-data"></a>应用程序数据
通过安装检测包为应用程序启用 Application Insights 后，它就会收集与应用程序的性能和运行相关的指标和日志。 Application Insights 将它收集的数据存储在其他数据源所使用的同一 Azure Monitor 数据平台中。 它包含大量用于分析此数据的工具，但也可以使用指标资源管理器和 Log Analytics 等工具通过其他源的数据对其进行分析。

| 目标 | 说明 | 参考 |
|:---|:---|:---|
| Azure Monitor 日志 | 有关应用程序的操作数据，包括页面视图、应用程序请求、异常和跟踪。 | [在 Azure Monitor 中分析日志数据](../log-query/log-query-overview.md) |
|                    | 应用程序组件之间的依赖关系信息，以支持应用程序映射和遥测关联。 | [Application Insights 中的遥测关联](../app/correlation.md) <br> [应用程序映射](../app/app-map.md) |
|            | 可用性测试的结果，可测试公共 Internet 上不同位置的应用程序的可用性和响应能力。 | [监视任何网站的可用性和响应能力](../app/monitor-web-app-availability.md) |
| Azure Monitor 度量值 | Application Insights 收集描述应用程序性能和操作的度量值，以及在应用程序中定义到 Azure Monitor 指标数据库的自定义指标。 | [Application Insights 中基于日志和预聚合的度量值](../app/pre-aggregated-metrics-log-metrics.md)<br>[自定义事件和指标的 Application Insights API](../app/api-custom-events-metrics.md) |
| Azure 存储 | 将应用程序数据发送到 Azure 存储以进行存档。 | [从 Application Insights 导出遥测数据](../app/export-telemetry.md) |
|            | 可用性测试的详细信息存储在 Azure 存储中。 使用 Azure 门户中 Application Insights 下载进行本地分析。 可用性测试的结果存储在 Azure Monitor 日志中。 | [监视任何网站的可用性和响应能力](../app/monitor-web-app-availability.md) |
|            | 探查器跟踪数据存储在 Azure 存储中。 使用 Azure 门户中 Application Insights 下载进行本地分析。  | [通过 Application Insights 分析 Azure 中的生产应用程序](../app/profiler-overview.md) 
|            | 为异常子集捕获的调试快照数据存储在 Azure 存储中。 使用 Azure 门户中 Application Insights 下载进行本地分析。  | [快照的工作原理](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>监视解决方案和见解
[监视解决方案](../insights/solutions.md)和[见解](../insights/insights-overview.md)收集数据，以便为特定服务或应用程序的操作提供更深入的见解。 它们可以处理不同应用程序层和多个层中的资源。

### <a name="monitoring-solutions"></a>监视解决方案

| 目标 | 说明 | 参考
|:---|:---|:---|
| Azure Monitor 日志 | 监视解决方案会将数据收集到 Azure Monitor 日志中，可以使用通常包含在解决方案中的查询语言或[视图](view-designer.md)对数据进行分析。 | [Azure 中监视解决方案的数据收集详细信息](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>用于容器的 Azure Monitor
[容器 Azure Monitor](../insights/container-insights-overview.md)提供[Azure KUBERNETES Service （AKS）](/azure/aks/)的自定义监视体验。 它收集有关下表中所述的这些资源的其他数据。

| 目标 | 说明 | 参考 |
|:---|:---|:---|
| Azure Monitor 日志 | 存储 AKS 的监视数据，包括清单、日志和事件。 指标数据也存储在日志中，以便在门户中利用其分析功能。 | [使用适用于容器的 Azure Monitor 了解 AKS 群集性能](../insights/container-insights-analyze.md) |
| Azure Monitor 度量值 | 度量值数据存储在指标数据库中，用于驱动可视化和警报。 | [在指标资源管理器中查看容器指标](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes 服务 | 提供对门户中的 Azure Kubernetes 服务（AKS）容器日志（stdout/stderror）、事件和 pod 指标的直接访问。 | [如何实时查看 Kubernetes 日志、事件和 pod 指标](../insights/container-insights-livedata-overview.md) |

### <a name="azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor
[用于 VM 的 Azure Monitor](../insights/vminsights-overview.md)提供了用于监视虚拟机的自定义体验。 上面的[操作系统（来宾）](#operating-system-guest)部分包含用于 VM 的 Azure Monitor 收集的数据的说明。

## <a name="custom-sources"></a>自定义来源
除了应用程序的标准层之外，还可能需要监视具有不能与其他数据源一起收集的遥测数据的其他资源。 对于这些资源，请使用 Azure Monitor API 将此数据写入指标或日志。

![自定义集合](media/data-sources/custom.png)

| 目标 | 方法 | 说明 | 参考 |
|:---|:---|:---|:---|
| Azure Monitor 日志 | 数据收集器 API | 从任何 REST 客户端收集日志数据，并将其存储在 Log Analytics 工作区中。 | [通过 HTTP 数据收集器 API （公共预览版）将日志数据发送到 Azure Monitor](data-collector-api.md) |
| Azure Monitor 度量值 | 自定义指标 API | 收集任何 REST 客户端中的指标数据，并将其存储在 Azure Monitor 度量值数据库中。 | [使用 REST API 将 Azure 资源的自定义指标发送到 Azure Monitor 指标存储](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>其他服务
Azure 中的其他服务将数据写入 Azure Monitor 数据平台。 这使你可以通过 Azure Monitor 收集的数据分析这些服务收集的数据，并利用相同的分析和可视化工具。

| 服务 | 目标 | 说明 | 参考 |
|:---|:---|:---|:---|
| [Azure 安全中心](/azure/security-center/) | Azure Monitor 日志 | Azure 安全中心会将它收集的安全数据存储在 Log Analytics 工作区中，这允许使用 Azure Monitor 收集的其他日志数据对其进行分析。  | [Azure 安全中心中的数据收集](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](/azure/sentinel/) | Azure Monitor 日志 | Azure Sentinel 将从不同数据源收集的数据存储在 Log Analytics 工作区中，这允许使用 Azure Monitor 收集的其他日志数据对其进行分析。  | [连接数据源](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Monitor 收集的监视数据的类型](data-platform.md)以及如何查看和分析此数据。
- 列出[Azure 资源存储数据的不同位置](data-locations.md)以及如何访问数据。 
