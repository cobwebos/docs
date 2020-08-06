---
title: 部署 Azure Monitor
description: 描述用于监视 Azure 订阅中的所有资源的 Azure Monitor 完全实现所需的不同步骤。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 34a048c702b62caeecaf21e710a9dcd9156e4aea
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2020
ms.locfileid: "87801447"
---
# <a name="deploy-azure-monitor"></a>部署 Azure Monitor
启用 Azure Monitor 来监视所有 Azure 资源是配置 Azure Monitor 组件和配置 Azure 资源的组合，以便为要收集的 Azure Monitor 生成监视数据。 本文介绍使用常见配置监视 Azure 订阅中的所有资源时，Azure Monitor 的完整实现所需的不同步骤。 为每个步骤提供了基本说明，其中包含有关详细配置要求的其他文档的链接。

> [!IMPORTANT]
> Azure Monitor 的功能及其配置将因你的业务要求与启用的功能的成本进行平衡而有所不同。 下面的每个步骤都将确定是否存在潜在费用，并应在继续执行该步骤之前评估这些成本。 请参阅[Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)了解完整的定价详细信息。

## <a name="configuration-goals"></a>配置目标
Azure Monitor 的完整实现的目标是收集所有云资源和应用程序的所有可用数据，并根据该数据在 Azure Monitor 中启用尽可能多的功能。

Azure Monitor 收集的数据将发送到[Azure Monitor 指标](platform/data-platform-metrics.md)或[Azure Monitor 日志](platform/data-platform-logs.md)。 每个存储不同种类的数据，并启用不同类型的分析和警报。 有关不同警报类型的说明，请参阅[比较 Azure Monitor 度量值和日志](platform/data-platform.md)，以比较 Microsoft Azure 中的两个和[警报概述](platform/alerts-overview.md)。 

某些数据可以发送到指标和日志，以便使用不同的功能。 在这些情况下，可能需要单独配置每个。 例如，Azure 资源自动将指标数据发送到指标，这些指标支持指标资源管理器和指标警报。 您必须为每个资源创建诊断设置，以便将同一指标数据发送到日志，这使您可以使用 Log Analytics 分析其他日志数据的性能趋势。 以下各节标识了数据的发送位置，并包含将数据发送到所有可能的位置所需的每个步骤。

你可能有其他要求，例如监视 Azure 之外的资源并在 Azure Monitor 之外发送数据。 如本文中所述的功能的附加配置，可以实现这些要求。 有关其他配置选项，请访问每个步骤中的文档链接。

## <a name="collect-data-from-azure-resources"></a>从 Azure 资源收集数据

> [!NOTE]
> 有关使用 Azure Monitor 监视虚拟机的完整指南，请参阅[使用 Azure Monitor 监视 Azure 资源](insights/monitor-azure-resource.md)。

一些 Azure 资源的监视是自动提供的，无需进行任何配置，而必须执行配置步骤来收集其他监视数据。 下表说明了从 Azure 资源收集所有可用数据所需的配置步骤，包括将步骤数据发送到 Azure Monitor 度量值和 Azure Monitor 日志。 以下部分详细介绍了每个步骤。

[![部署 Azure 资源监视 ](media/deploy/deploy-azure-resources.png)](media/deploy/deploy-azure-resources.png#lightbox)

### <a name="no-configuration"></a>无配置
在创建 Azure 订阅时，无需进行任何配置即可启用 Azure Monitor 的以下功能。 此监视不会产生费用。

[Azure Active Directory 日志](../active-directory/reports-monitoring/overview-reports.md)-提供登录活动的租户级历史记录和 Azure Active Directory 中所做更改的审核跟踪。 有关 Azure Active Directory 日志的详细信息以及如何在 Azure 门户中查看它们的详细信息，请参阅[Azure Active Directory 门户中的审核活动报告](../active-directory/reports-monitoring/concept-audit-logs.md)和[Azure Active Directory 门户中的登录活动报告](../active-directory/reports-monitoring/concept-sign-ins.md)。

[活动日志](platform/platform-logs-overview.md)-可深入了解 Azure 中发生的管理组和订阅级别事件。 当你创建新的 Azure 资源、修改资源或执行重要活动时，事件将自动写入活动日志。 你可以查看 Azure 门户中的事件，并在创建特定事件时创建活动日志警报。 有关活动日志的详细信息以及如何在 Azure 门户中查看该日志的详细信息，请参阅[Azure 活动日志](platform/activity-log.md)。

[平台指标](platform/metrics-supported.md)-从 Azure 服务自动收集到[Azure Monitor 指标](platform/data-platform-metrics.md)。 此数据通常显示在不同服务的 Azure 门户中的 "**概述**" 页上。 有关分析 Azure 门户中的平台指标的详细信息，请参阅[Azure 指标资源管理器](platform/metrics-getting-started.md)入门。 


### <a name="create-log-analytics-workspace"></a>创建 Log Analytics 工作区
你需要至少一个 Log Analytics 工作区来启用[Azure Monitor 日志](platform/data-platform-logs.md)，这是将此类数据收集为来自 azure 资源的日志、从 azure 虚拟机的来宾操作系统收集数据和用于大多数 Azure Monitor insights 所必需的。 其他服务（如 Azure Sentinel 和 Azure 安全中心）也使用 Log Analytics 工作区，并与用于 Azure Monitor 的工作区相同。 你可以开始使用单个工作区来支持此监视，但请参阅[设计你的 Azure Monitor 日志部署](platform/design-logs-deployment.md)以获取有关何时使用多个工作区的指导。

创建 Log Analytics 工作区不会产生任何费用，但在配置要收集到数据的数据时，可能会产生费用。 有关详细信息，请参阅[使用 Azure Monitor 日志管理使用情况和成本](platform/manage-cost-storage.md)。  

请参阅[在 Azure 门户中创建 Log Analytics 工作区](learn/quick-create-workspace.md)以创建初始 Log Analytics 工作区。 若要配置访问权限，请参阅[管理对 Azure Monitor 日志数据和工作区的访问权限](platform/manage-access.md)。 

### <a name="create-diagnostic-setting-to-collect-tenant-and-subscription-logs"></a>创建诊断设置以收集租户和订阅日志
尽管租户的[Azure Active Directory 日志](../active-directory/reports-monitoring/overview-reports.md)和订阅的[活动日志](platform/platform-logs-overview.md)会自动收集，但将其发送到 Log Analytics 工作区后，你可以在 Log Analytics 中使用日志查询分析这些事件和其他日志数据。 这还允许您创建日志查询警报，这是警报 Azure Active Directory 日志的唯一方法，并提供更复杂的逻辑，而不是活动日志警报。

将活动日志发送到工作区不会产生费用，但 Azure Active Directory 日志中存在数据引入和保留费用。 

请参阅将[Azure AD 日志与 Azure Monitor 日志集成](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)，并[创建诊断设置以将平台日志和指标发送到不同的目标](platform/diagnostic-settings.md)，以创建租户和订阅的诊断设置，以便将日志条目发送到 Log Analytics 工作区。 

### <a name="create-diagnostic-setting-to-collect-resource-logs-and-platform-metrics"></a>创建诊断设置以收集资源日志和平台指标
Azure 中的资源会自动生成[资源日志](platform/platform-logs-overview.md)，以提供资源中执行的操作的详细信息。 但与平台指标不同，你需要配置要收集的资源日志。 创建诊断设置以将其发送到 Log Analytics 工作区，以将它们与 Azure Monitor 日志使用的其他数据组合在一起。 同一诊断设置还可用于将大多数资源的平台指标发送到相同的工作区，这使你可以使用日志查询和其他收集的数据分析指标数据。

此集合需要支付费用，因此在实现大量资源之前，请参考[Azure Monitor 的定价](https://azure.microsoft.com/pricing/details/monitor/)。 有关优化日志收集成本的详细信息，另请参阅[使用 Azure Monitor 日志管理使用情况和成本](platform/manage-cost-storage.md)。

请参阅[创建诊断设置以收集 azure 中的资源日志和指标](platform/diagnostic-settings.md#create-in-azure-portal)，以创建 azure 资源的诊断设置。 由于需要为每个 Azure 资源创建诊断设置，有关使用[azure 策略](../governance/policy/overview.md)的详细信息，请参阅[部署 Azure Monitor 规模](deploy-scale.md)，以便在每次创建 azure 资源时自动创建设置。

### <a name="enable-insights-and-solutions"></a>启用见解和解决方案
见解和解决方案为特定服务或解决方案提供专用监视。 Insights 使用较新的 Azure Monitor 功能，如工作簿，因此，如果你的服务可用，则应使用见解。 它们可在每个 Azure 订阅中自动使用，但可能需要某些配置才能获得完整功能。 它们通常将使用之前配置的平台指标和资源日志，并可以收集其他数据。

必须将解决方案添加到每个订阅，并以独占方式处理 Azure Monitor 日志中的数据，并可能收集额外的日志数据。

解决方案或见解没有成本，但你可能会对收集的任何数据收费。

有关 Azure Monitor 中可用见解和解决方案的列表，请参阅[Azure Monitor 监视的内容](monitor-reference.md)。 请参阅每个的相关文档，了解唯一的配置或定价信息。 

## <a name="collect-data-from-virtual-machines"></a>从虚拟机收集数据

> [!NOTE]
> 有关使用 Azure Monitor 监视虚拟机的完整指南，请参阅[使用 Azure Monitor 监视 Azure 虚拟机](insights/monitor-vm-azure.md)。 

虚拟机生成的数据与其他 Azure 资源类似，但你需要一个代理来从来宾操作系统收集数据。 请参阅[Azure Monitor 代理概述](platform/agents-overview.md)，比较 Azure Monitor 使用的代理。 

[用于 VM 的 Azure Monitor](insights/vminsights-overview.md)使用 Log Analytics 代理和依赖项代理从虚拟机的来宾操作系统中收集数据，因此你可以将这些代理作为此见解的实现的一部分进行部署。 这会为使用它的其他服务（例如 Azure 安全中心）启用 Log Analytics 代理。


[![部署 AZURE VM ](media/deploy/deploy-azure-vm.png)](media/deploy/deploy-azure-vm.png#lightbox)


### <a name="configure-workspace-for-azure-monitor-for-vms"></a>为用于 VM 的 Azure Monitor 配置工作区
用于 VM 的 Azure Monitor 需要一个 Log Analytics 工作区，该工作区通常与从其他 Azure 资源中收集数据时所创建的工作区相同。 启用任何虚拟机之前，必须将用于 VM 的 Azure Monitor 所需的解决方案添加到工作区。

若要详细了解如何配置用于 VM 的 Azure Monitor 的 Log Analytics 工作区，请参阅[配置用于 VM 的 Azure Monitor Log Analytics 工作区](insights/vminsights-configure-workspace.md)。

### <a name="enable-azure-monitor-for-vms-on-each-virtual-machine"></a>启用每个虚拟机上的用于 VM 的 Azure Monitor
配置工作区后，可以通过安装 Log Analytics 代理和依赖项代理来启用每个虚拟机。 有多种方法可安装这些代理，其中包括 Azure 策略，允许你在创建每个虚拟机时自动配置这些代理。 用于 VM 的 Azure Monitor 收集的性能数据和进程详细信息存储在 Azure Monitor 日志中。

请参阅[启用用于 VM 的 Azure Monitor 概述](insights/vminsights-enable-overview.md)，了解如何将代理部署到你的虚拟机，并使其能够进行监视。

### <a name="configure-workspace-to-collect-events"></a>配置工作区以收集事件
用于 VM 的 Azure Monitor 将从每个虚拟机的来宾操作系统中收集性能数据以及进程的详细信息和依赖项。 Log Analytics 代理还可以从来宾收集日志，包括来自 Windows 的事件日志和 Linux 中的 syslog。 它从其连接到的 Log Analytics 工作区中检索这些日志的配置。 只需配置工作区一次，每次代理连接时，它将下载任何配置更改。 

有关配置 Log Analytics 工作区以从代理虚拟机收集额外数据的详细信息，请参阅[中的代理数据源 Azure Monitor](platform/agent-data-sources.md) 。

> [!NOTE]
> 你还可以配置工作区以收集性能计数器，但这很可能是由用于 VM 的 Azure Monitor 收集的性能数据冗余的。 工作区收集的性能数据将存储在*Perf*表中，而用于 VM 的 Azure Monitor 收集的性能数据将存储在*InsightsMetrics*表中。 仅当需要用于 VM 的 Azure Monitor 尚未收集的计数器时，才在工作区中配置性能收集。

### <a name="diagnostic-extension-and-telegraf-agent"></a>诊断扩展和 Telegraf 代理
用于 VM 的 Azure Monitor 使用将性能数据发送到 Log Analytics 工作区但不 Azure Monitor 度量值的 Log Analytics 代理。 将此数据发送到度量值后，可以使用指标资源管理器分析该数据，并将其用于指标警报。 这需要 Windows 上的诊断扩展和 Linux 上的 Telegraf 代理。

有关安装和配置这些代理的详细信息，请参阅[安装和配置 Windows Azure 诊断扩展 (WAD) ](platform/diagnostics-extension-windows-install.md)和[使用 InfluxData Telegraf 代理收集 Linux VM 的自定义指标](platform/collect-custom-metrics-linux-telegraf.md)。


## <a name="monitor-applications"></a>监视应用程序
Azure Monitor 使用[Application Insights](app/app-insights-overview.md)监视自定义应用程序，必须为要监视的每个应用程序配置该应用程序。 根据所监视的应用程序的类型和要执行的监视类型，配置过程会有所不同。 Application Insights 收集的数据存储在 Azure Monitor 指标、Azure Monitor 日志和 Azure blob 存储中，具体取决于功能。 性能数据同时存储在 Azure Monitor 度量值和 Azure Monitor 日志中，无需其他配置。

### <a name="create-an-application-resource"></a>创建应用程序资源
必须为要监视的每个应用程序在 Application Insights 中创建一个资源。 Application Insights 收集的日志数据存储在 Azure Monitor 日志中，但与 Log Analytics 工作区不同，如[Azure Monitor 日志中的数据如何结构化？中](platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured)所述。 目前在预览版中，可以将应用程序数据直接存储在包含其他数据的 Log Analytics 工作区中。 这简化了配置并允许应用程序利用 Log Analytics 工作区的所有功能。

 创建应用程序时，必须选择是使用经典 (预览) 还是基于工作区的预览。 请参阅[创建 Application Insights 资源](app/create-new-resource.md)来创建经典应用程序。 请参阅[基于工作区的 Application Insights 资源 (预览) ](app/create-workspace-resource.md)以创建基于工作区的应用程序。

### <a name="configure-codeless-or-code-based-monitoring"></a>配置无代码置备或基于代码的监视
若要为应用程序启用监视，必须确定是使用无代码置备还是基于代码的监视。 根据此决定以及要监视的应用程序类型，配置过程会有所不同。

**无代码置备监视**最容易实现，并且可在代码开发后进行配置。 不需要对代码进行任何更新。 有关启用监视的详细信息，请参阅以下资源。

- [托管在 Azure Web 应用上的应用程序](app/azure-web-apps.md)
- [Java 应用程序](app/java-in-process-agent.md)
- [在 Azure VM 或 Azure 虚拟机规模集的 IIS 中托管的 ASP.NET 应用程序](app/azure-vm-vmss-apps.md)
- [在 VM 的 IIS 中托管的 ASP.NET 应用程序](app/monitor-performance-live-website-now.md)


**基于代码的监视**更易于自定义并收集更多遥测数据，但它需要将依赖项添加到 Application Insights SDK NuGet 包上的代码中。 有关启用监视的详细信息，请参阅以下资源。

- [ASP.NET 应用程序](app/asp-net.md)
- [ASP.NET Core 应用程序](app/asp-net-core.md)
- [.NET 控制台应用程序](app/console.md)
- [Java](app/java-get-started.md)
- [Node.js](app/nodejs.md)
- [Python](app/opencensus-python.md)
- [其他平台](app/platforms.md)

### <a name="configure-availability-testing"></a>配置可用性测试
Application Insights 中的可用性测试是重复测试，它们定期监视应用程序的可用性和响应能力，从世界各地。 可以免费创建一个简单的 ping 测试，也可以创建一个 web 请求序列来模拟具有关联成本的用户交易。 

请参阅[监视任何网站的可用性](app/monitor-web-app-availability.md)，了解不同类型的测试的摘要和有关创建它们的详细信息。

### <a name="configure-profiler"></a>配置探查器
Application Insights 中的探查器提供 .NET 应用程序的性能跟踪。 它帮助你确定处理特定 web 请求所用时间最长的 "热" 代码路径。 配置探查器的过程因应用程序类型而异。 

有关配置 Profiler 的详细信息，请参阅[通过 Application Insights 分析 Azure 中的生产应用程序](app/profiler-overview.md)。

### <a name="configure-snapshot-debugger"></a>配置 Snapshot Debugger
Snapshot Debugger 在 Application Insights 中，可以从 .NET 应用程序中监视异常遥测，并收集你的最常见异常的快照，以便你可以获得诊断生产中的问题所需的信息。 配置 Snapshot Debugger 的过程因应用程序类型而异。 

有关配置 Snapshot Debugger 的详细信息，请参阅[在 .net 应用中的异常调试快照](app/snapshot-debugger.md)。


## <a name="visualize-data"></a>可视化数据
见解和解决方案将包括自己的工作簿和视图，用于分析数据。 除此之外，还可以创建自己的[可视化效果](visualizations.md)，包括用于 Azure Monitor 数据和仪表板的工作簿，将 Azure Monitor 数据与 Azure 中的其他服务的数据相结合。


### <a name="create-workbooks"></a>创建工作簿
Azure Monitor 中的[工作簿](platform/workbooks-overview.md)允许您在 Azure 门户中创建丰富的视觉对象报表。 可以将不同的数据集与 Azure Monitor 度量值和 Azure Monitor 日志相结合，以创建统一的交互式体验。 您可以在 "Azure Monitor" 菜单的 "**工作簿**" 选项卡中访问工作簿的库。 

有关创建自定义工作簿的详细信息，请参阅[Azure Monitor 工作簿](platform/workbooks-overview.md)。

### <a name="create-dashboards"></a>创建仪表板
[Azure 仪表板](../azure-portal/azure-portal-dashboards.md)是适用于 azure 的主要仪表板建设技术，使你可以将 Azure Monitor 数据与其他服务中的数据合并，以便在 Azure 基础结构上提供单个窗格。 有关创建包含 Azure Monitor 日志数据的仪表板的详细信息，请参阅[创建和共享 Log Analytics 数据的仪表板](learn/tutorial-logs-dashboards.md)。 

有关创建包含 Application Insights 数据的仪表板的详细信息，请参阅[使用 Azure 应用程序 Insights 创建自定义 KPI 仪表板](learn/tutorial-app-dashboards.md)。 

## <a name="alerts"></a>警报
Azure Monitor 中的警报会主动通知你在监视数据中发现的重要数据或模式。 某些见解将生成不带配置的警报。 对于其他方案，你需要创建包括要分析的数据的[警报规则](platform/alerts-overview.md)以及用于生成警报的时间的条件，以及定义生成警报时要执行的操作的操作组。 


### <a name="create-action-groups"></a>创建操作组
[操作组](platform/action-groups.md)是警报规则使用的一系列通知首选项，用来确定触发警报时要执行的操作。 操作的示例包括发送邮件或文本、调用 webhook 或将数据发送到 ITSM 工具。 每个警报规则至少需要一个操作组，并且一个操作组可以由多个警报规则使用。

有关创建操作组的详细信息，请参阅[创建和管理 Azure 门户操作](platform/action-groups.md)组和可包括的不同操作的说明。


### <a name="create-alert-rules"></a>创建警报规则
有多种类型的警报规则由它们使用的数据类型定义。 每个都有不同的功能和不同的成本。 应遵循的基本策略是将警报规则类型与提供所需逻辑的最低开销一起使用。

- [活动日志规则](platform/activity-log-alerts.md)。 创建一个警报以响应与指定条件相匹配的新的活动日志事件。 这些警报不会产生任何费用，因此应该是第一种选择。 若要详细了解如何创建活动日志警报，请参阅[创建、查看和管理活动日志 Azure Monitor 警报](platform/alerts-activity-log.md)。
- [指标警报规则](platform/alerts-metric-overview.md)。 创建一个警报，以响应超出阈值的一个或多个指标值。 指标警报是有状态的，这意味着当该值低于阈值时，警报将自动关闭，并且仅在状态更改时发出通知。 指标警报会产生成本，但这种情况明显小于日志警报。 有关创建指标警报的详细信息，请参阅[使用 Azure Monitor 创建、查看和管理指标警报](platform/alerts-metric.md)。
- [日志警报规则](platform/alerts-unified-log.md)。 当计划查询的结果与指定的条件匹配时，创建警报。 它们是最昂贵的警报规则，但它们允许最复杂的条件。 有关创建日志查询警报的详细信息，请参阅[使用 Azure Monitor 创建、查看和管理日志警报](platform/alerts-log.md)。
- 利用[应用程序警报](app/monitor-web-app-availability.md)，你可以对 web 应用程序执行主动性能和可用性测试。 你可以无需进行一次简单的 ping 测试，但是，更复杂的测试会产生成本。 请参阅[监视任何网站的可用性](app/monitor-web-app-availability.md)，了解有关不同测试的说明以及有关创建这些测试的详细信息。


## <a name="next-steps"></a>后续步骤

- 请参阅[使用 Azure 策略大规模部署 Azure Monitor](deploy-scale.md)。