---
title: Azure Monitor 中的数据的来源 | Microsoft Docs
description: 介绍用于监视 Azure 资源及其上运行的应用程序的运行状况和性能的数据。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: bwren
ms.openlocfilehash: b77fb3ab5651147c59b9f0afd22a2d6d0159c90e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66357497"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>有关 Azure Monitor 监视数据源
Azure 监视器基于[常见的监视数据平台](data-platform.md)，其中包含[日志](data-platform-logs.md)并[指标](data-platform-metrics.md)。 数据收集到此平台允许将数据从多个资源一起使用 Azure Monitor 中的一组通用的工具进行分析。 监视数据可能也发送到其他位置来支持特定的方案，并将一些资源可能写入其他位置，才能将其收集到日志或指标。

本文介绍不同的监视除了创建 Azure 资源的监视数据由 Azure Monitor 收集的数据源。 提供了指向配置到不同位置收集此数据所需的详细信息的链接。

## <a name="application-tiers"></a>应用程序层

监视从 Azure 应用程序的数据源可以按层、 最高的层，在进行应用程序本身和较低的层正在 Azure 平台的组件。 从每个层访问数据的方法而异。 下表总结了应用程序层和以下各节中提供的监视每个层中的数据源。 请参阅[监视 Azure 中的数据位置](data-locations.md)有关每个数据位置和如何访问其数据的说明。


![监视层](../media/overview/overview.png)


### <a name="azure"></a>Azure
下表简要介绍特定于 Azure 的应用程序层。 以下链接进一步详细介绍了以下各节中的每个。

| 层 | 描述 | 收集方法 |
|:---|:---|:---|
| [Azure Tenant](#azure-tenant) | 有关租户级 Azure 服务（例如 Azure Active Directory）操作的数据。 | 在门户中查看 AAD 数据或配置到 Azure Monitor 使用租户的诊断设置的集合。 |
| [Azure 订阅](#azure-subscription) | 与运行状况和管理 Azure 资源管理器和服务运行状况等订阅中跨资源服务相关的数据。 | 在门户中查看或配置为使用日志配置文件的 Azure Monitor 的集合。 |
| [Azure 资源](#azure-resources) |  有关操作和性能的每个 Azure 资源的数据。 | 在指标资源管理器中自动，查看收集的指标。<br>诊断设置配置为收集 Azure Monitor 中的日志。<br>监视解决方案和见解可用于对特定资源类型的更详细监视。 |

### <a name="azure-other-cloud-or-on-premises"></a>Azure、 其他云或本地 
下表简要介绍可能在 Azure、 另一个云或在本地的应用程序层。 以下链接进一步详细介绍了以下各节中的每个。

| 层 | 描述 | 收集方法 |
|:---|:---|:---|
| [操作系统 （来宾）](#operating-system-guest) | 有关计算资源上的操作系统的数据。 | 安装 Log Analytics 代理来收集客户端数据源将数据置于 Azure Monitor 和依赖关系代理以收集有关在 Vm 支持 Azure Monitor 的依赖项。<br>对于 Azure 虚拟机，安装 Azure 诊断扩展到 Azure Monitor 收集日志和指标。 |
| [应用程序代码](#application-code) | 有关性能和功能的实际的应用程序和代码，包括性能跟踪、 应用程序日志和用户遥测数据的数据。 | 检测代码到 Application Insights 收集数据。 |
| [自定义源](#custom-sources) | 从外部服务或其他组件或设备的数据。 | 从任何 REST 客户端到 Azure Monitor 收集日志或指标数据。 |

## <a name="azure-tenant"></a>Azure 租户
与你的 Azure 租户相关的遥测数据是从租户级服务（例如 Azure Active Directory）中收集的。

![Azure 租户集合](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory 审核日志
[Azure Active Directory 报告](../../active-directory/reports-monitoring/overview-reports.md)包含特定租户中的登录活动历史记录和更改审核日志。 

| 目标 | 描述 | 参考 |
|:---|:---|:---|
| Azure Monitor 日志 | 配置 Azure AD 日志收集在 Azure Monitor，与其他监视数据对其进行分析。 | [将 Azure AD 日志集成与 Azure Monitor 日志 （预览版）](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure 存储 | 将 Azure AD 日志导出到 Azure 存储用于存档。 | [教程：存档 Azure AD 的日志传输到 Azure 存储帐户 （预览版）](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| 事件中心 | Stream Azure AD 会记录到事件中心使用的其他位置。 | [教程：Stream 到 Azure 事件中心 （预览版） 的 Azure Active Directory 日志](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。 |



## <a name="azure-subscription"></a>Azure 订阅
与运行状况和操作的 Azure 订阅相关的遥测数据。

![Azure 订阅](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure 活动日志 
[Azure 活动日志](activity-logs-overview.md)包括服务运行状况记录以及了解对你的 Azure 订阅中的资源进行任何配置更改的记录。 活动日志可供所有 Azure 资源使用，代表其外部视图。 

| 目标 | 描述 | 参考 |
|:---|:---|
| 活动日志 | 活动日志收集到其自己数据存储区可以查看从 Azure Monitor 菜单或用于创建活动日志警报。 | [在 Azure 门户中的活动日志中查询](activity-log-view.md#azure-portal) |
| Azure Monitor 日志 | 配置 Azure Monitor 日志收集活动日志以对其进行分析与其他监视数据。 | [收集和分析 Azure Monitor 中的 Log Analytics 工作区中的 Azure 活动日志](activity-log-collect.md) |
| Azure 存储 | 将活动日志导出到 Azure 存储用于存档。 | [存档活动日志](activity-log-export.md#archive-activity-log)  |
| 事件中心 | Stream 到其他位置使用事件中心的活动日志 | [Stream 活动记录到事件中心](activity-log-export.md#stream-activity-log-to-event-hub)。 |

### <a name="azure-service-health"></a>Azure 服务运行状况
[Azure 服务运行状况](../../service-health/service-health-overview.md)提供订阅中应用程序和资源所依赖的 Azure 服务的运行状况信息。

| 目标 | 描述 | 参考 |
|:---|:---|:---|
| 活动日志<br>Azure Monitor 日志 | 服务运行状况记录存储在 Azure 活动日志，以便您可以在 Azure 门户中查看它们，或执行您可以使用活动日志执行的任何其他活动。 | [使用 Azure 门户查看服务运行状况通知](service-notifications.md) |


## <a name="azure-resources"></a>Azure 资源
指标和资源级诊断日志提供  Azure 资源的内部操作的信息。 这些是可用于大多数 Azure 服务，并监视解决方案和见解收集的特定服务的其他数据。

![Azure 资源收集](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>平台指标 
大多数 Azure 服务将发送[平台指标](data-platform-metrics.md)它反映它们的性能和直接对度量值的数据库的操作。 具体[指标因资源类型而异](metrics-supported.md)。 

| 目标 | 描述 | 参考 |
|:---|:---|:---|
| Azure Monitor 指标 | 平台度量值将写入到 Azure Monitor 指标数据库而无需配置。 从指标资源管理器访问平台指标。  | [Azure 指标资源管理器入门](metrics-getting-started.md)<br>[Azure Monitor 支持的指标](metrics-supported.md) |
| Azure Monitor 日志 | 将平台指标的趋势和其他分析使用 Log Analytics 复制到日志中。 | [Azure 诊断定向到 Log Analytics](collect-azure-metrics-logs.md#azure-diagnostics-direct-to-log-analytics) |
| 事件中心 | 使用事件中心的其他位置的 Stream 指标。 |[将 Azure 监视数据流式传输到事件中心以便外部工具使用](stream-monitoring-data-event-hubs.md) |

### <a name="diagnostic-logs"></a>诊断日志
[诊断日志](diagnostic-logs-overview.md)提供深入_内部_Azure 资源的操作。  默认情况下未启用诊断日志。 必须启用它们，并为每个资源指定目标。 

配置要求和诊断日志的内容因资源类型，并不是所有服务尚未都创建诊断日志。 请参阅[支持的 Azure 诊断日志服务、 架构和类别](diagnostic-logs-schema.md)有关每个服务和指向详细的配置过程的详细信息。 如果该服务不会列出此文章介绍了，则该服务当前不会写入诊断日志。

| 目标 | 描述 | 参考 |
|:---|:---|:---|
| Azure Monitor 日志 | 将诊断日志发送给 Azure Monitor 日志与其他收集日志数据的分析。 一些资源可以直接写入到 Azure Monitor 时其他人正在导入到 Log Analytics 工作区之前写入到存储帐户。 | [Stream Azure 诊断日志到 Azure Monitor 中的 Log Analytics 工作区](diagnostic-logs-stream-log-store.md)<br>[使用 Azure 门户从 Azure 存储中收集日志](azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage)  |
| 存储 | 发送诊断日志传输到 Azure 存储用于存档。 | [存档 Azure 诊断日志](archive-diagnostic-logs.md) |
| 事件中心 | Stream 诊断日志到使用事件中心的其他位置。 |[Stream 到事件中心的 Azure 诊断日志](diagnostic-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>操作系统 （来宾）
Azure 中的、其他云中的以及本地的计算资源都有要监视的来宾操作系统。 一个或多个代理的安装后，您可以到 Azure Monitor 与 Azure 服务本身相同的监视工具使用分析从来宾操作系统收集遥测数据。

![Azure 计算资源收集](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Azure 诊断扩展
为 Azure 虚拟机可以启用 Azure 诊断扩展收集日志和指标来自来宾操作系统的 Azure 计算资源，包括 Azure 云服务 （经典） Web 和辅助角色，虚拟机，虚拟机规模集和 Service Fabric。

| 目标 | 描述 | 参考 |
|:---|:---|:---|
| 存储 | 时启用诊断扩展，它将写入到存储帐户，默认情况下。 | [在 Azure 存储中存储和查看诊断数据](diagnostics-extension-to-storage.md) |
| Azure Monitor 指标 | 在配置诊断扩展收集性能计数器时，写入到 Azure Monitor 指标数据库时。 | [发送到 Azure Monitor 指标的来宾 OS 指标存储为 Windows 虚拟机使用资源管理器模板](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Application Insights 日志 | 从支持与其他应用程序数据分析应用程序的计算资源中收集日志和性能计数器。 | [将云服务、 虚拟机或 Service Fabric 诊断数据发送到 Application Insights](diagnostics-extension-to-application-insights.md) |
| 事件中心 | 配置诊断扩展来流式传输到其他位置使用事件中心的数据。  | [使用事件中心的流式处理热路径中的 Azure 诊断数据](diagnostics-extension-stream-event-hubs.md) |

### <a name="log-analytics-agent"></a>Log Analytics 代理 
安装适用于全面的监视和管理 Windows 或 Linux 虚拟机的 Log Analytics 代理。 虚拟机可以在 Azure 中运行，也可以在其他云中或本地运行。

| 目标 | 描述 | 参考 |
|:---|:---|:---|
| Azure Monitor 日志 | 连接到 Azure Log Analytics 代理直接或通过 System Center Operations Manager 监视，并允许您从你配置的数据源或无法监视解决方案，提供更深入了解应用程序收集数据虚拟机上运行。 | [Azure Monitor 中的代理数据源](agent-data-sources.md)<br>[将 Operations Manager 连接到 Azure Monitor](om-agents.md) |


### <a name="azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor 
[适用于 Vm 的 azure 监视器](../insights/vminsights-overview.md)为虚拟机提供功能更多核心 Azure 监视器功能，包括服务状态和 VM 运行状况提供的自定义的监视体验。 它需要与 Log Analytics 代理来收集有关虚拟机和外部进程依赖项上运行的进程中发现的数据集成，Windows 和 Linux 虚拟机上的依赖关系代理。

| 目标 | 描述 | 参考 |
|:---|:---|:---|
| Azure Monitor 日志 | 将有关进程和依赖项的数据存储在代理上。 | [使用 Azure Monitor 的 Vm （预览版） 将地图以了解应用程序组件](../insights/vminsights-maps.md) |
| VM 存储 | Azure 监视的 Vm 将运行状况状态信息存储在自定义位置。 这是仅供除了在 Azure 门户中的 Vm 的 Azure Monitor [Azure 资源运行状况 REST API](/rest/api/resourcehealth/)。 | [了解 Azure 虚拟机的运行状况](../insights/vminsights-health.md)<br>[Azure 资源运行状况 REST API](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>应用程序代码
在 Azure Monitor 监视详细的应用程序通过[Application Insights](https://docs.microsoft.com/azure/application-insights/)将从各种平台上运行的应用程序收集数据。 此应用程序可以在 Azure 中运行，也可以在其他云中或本地运行。

![应用程序数据收集](media/data-sources/applications.png)


### <a name="application-data"></a>应用程序数据
通过安装检测包为应用程序启用 Application Insights 后，它就会收集与应用程序的性能和运行相关的指标和日志。 Application Insights 将收集的数据存储在由其他数据源使用的相同 Azure Monitor 数据平台。 它包括大量工具用于分析此数据，但你也可以使用指标资源管理器和 Log Analytics 等工具其他来源的数据与分析它。

| 目标 | 描述 | 参考 |
|:---|:---|:---|
| Azure Monitor 日志 | 有关包括页面视图、 应用程序请求、 异常和跟踪应用程序的操作数据。 | [分析 Azure Monitor 中的日志数据](../log-query/log-query-overview.md) |
|                    | 应用程序组件来支持应用程序映射和遥测关联之间的依赖关系信息。 | [Application Insights 中的遥测关联](../app/correlation.md) <br> [应用程序映射](../app/app-map.md) |
|            | 测试可用性和响应能力的应用程序从公共 Internet 上的不同位置的可用性测试的结果。 | [监视任何网站的可用性和响应能力](../app/monitor-web-app-availability.md) |
| Azure Monitor 指标 | Application Insights 收集指标的性能和操作的应用程序除了在 Azure Monitor 指标数据库应用程序中定义的自定义指标的描述。 | [Application Insights 中的基于日志的和预聚合指标](../app/pre-aggregated-metrics-log-metrics.md)<br>[用于自定义事件和指标的 application Insights API](../app/api-custom-events-metrics.md) |
| Azure 存储 | 将应用程序数据发送到 Azure 存储，用于存档。 | [从 Application Insights 导出遥测数据](../app/export-telemetry.md) |
|            | 可用性测试的详细信息存储在 Azure 存储中。 使用 Azure 门户中 Application Insights 下载进行本地分析。 可用性测试的结果存储在 Azure Monitor 日志。 | [监视任何网站的可用性和响应能力](../app/monitor-web-app-availability.md) |
|            | Profiler 跟踪数据存储在 Azure 存储。 使用 Azure 门户中 Application Insights 下载进行本地分析。  | [分析生产应用程序在 Azure 中使用 Application Insights](../app/profiler-overview.md) 
|            | 调试的快照捕获的异常的子集的数据存储在 Azure 存储。 使用 Azure 门户中 Application Insights 下载进行本地分析。  | [快照的工作原理](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>监视解决方案和见解
[监视解决方案](../insights/solutions.md)并[Insights](../insights/insights-overview.md)收集数据，以提供更深入了解特定服务或应用程序的操作。 它们可能会解决不同应用程序层和甚至多个层中的资源。

### <a name="monitoring-solutions"></a>监视解决方案

| 目标 | 描述 | 参考
|:---|:---|:---|
| Azure Monitor 日志 | 监视解决方案数据收集到 Azure Monitor 日志位置可能会使用查询语言分析或[视图](view-designer.md)通常包含在解决方案中。 | [用于监视 Azure 中的解决方案的数据集合详细信息](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>用于容器的 Azure Monitor
[用于容器的 azure Monitor](../insights/container-insights-overview.md)提供的自定义监视体验[Azure Kubernetes 服务 (AKS)](/azure/aks/)。 它收集有关下表中描述这些资源的其他数据。

| 目标 | 描述 | 参考 |
|:---|:---|:---|
| Azure Monitor 日志 | 适用于 AKS 包括清单、 日志和事件监视数据存储。 若要利用其在门户中的分析功能，指标数据也存储在日志中。 | [使用适用于容器的 Azure Monitor 了解 AKS 群集性能](../insights/container-insights-analyze.md) |
| Azure Monitor 指标 | 指标数据存储在驱动器可视化和警报的指标数据库。 | [查看容器指标在指标资源管理器](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes 服务 | 为了提供近乎实时体验，用于容器的 Azure Monitor 显示在 Azure 门户中直接从 Azure Kubernetes 服务的数据。 | [如何使用用于容器的 Azure Monitor（预览版）实时查看容器日志](../insights/container-insights-live-logs.md) |

### <a name="azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor
[适用于 Vm 的 azure 监视器](../insights/vminsights-overview.md)提供用于监视虚拟机的自定义的体验。 Vm 的 Azure Monitor 收集的数据的说明包含在[操作系统 （来宾）](#operating-system-guest)上面一节。

## <a name="custom-sources"></a>自定义源
除了应用程序的标准层之外，还可能需要监视具有不能与其他数据源一起收集的遥测数据的其他资源。 对于这些资源，此数据写入指标或使用 Azure Monitor API 的日志。

![自定义集合](media/data-sources/custom.png)

| 目标 | 方法 | 描述 | 参考 |
|:---|:---|:---|:---|
| Azure Monitor 日志 | 数据收集器 API | 从任何 REST 客户端收集日志数据并将存储在 Log Analytics 工作区。 | [将日志数据发送到 Azure Monitor 使用 HTTP 数据收集器 API （公共预览版）](data-collector-api.md) |
| Azure Monitor 指标 | 自定义指标 API | 从任何 REST 客户端和 Azure Monitor 指标数据库中的存储区中收集指标数据。 | [使用 REST API 将在 Azure 资源的自定义指标发送到 Azure Monitor 指标存储](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>其他服务
在 Azure 中的其他服务将数据写入到 Azure Monitor 数据平台。 这允许您分析这些服务使用由 Azure Monitor 收集的数据收集的数据，并利用相同的分析和可视化工具。

| 服务 | 目标 | 描述 | 参考 |
|:---|:---|:---|:---|
| [Azure 安全中心](/azure/security-center/) | Azure Monitor 日志 | Azure 安全中心将存储在 Log Analytics 工作区，这使它可以分析与 Azure Monitor 收集其他日志数据中收集的安全数据。  | [Azure 安全中心中的数据收集](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](/azure/sentinel/) | Azure Monitor 日志 | Azure Sentinel 存储来自不同数据源中的 Log Analytics 工作区，这使它可以与 Azure Monitor 收集其他日志数据进行分析收集的数据。  | [连接数据源](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Monitor 收集的监视数据的类型](data-platform.md)以及如何查看和分析此数据。
- 列表[Azure 资源在其中存储数据的不同位置](data-locations.md)以及如何访问它。 
