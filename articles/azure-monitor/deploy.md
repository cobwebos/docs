---
title: 部署 Azure Monitor
description: 描述如何通过所需的不同步骤来完全实现 Azure Monitor，以便监视 Azure 订阅中的所有资源。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 0a5c788b4429b5048a1b94fa8adfb2d9367982da
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90033466"
---
# <a name="deploy-azure-monitor"></a>部署 Azure Monitor
启用 Azure Monitor 来监视所有 Azure 资源时，既要配置 Azure Monitor 组件，也要配置 Azure 资源，这样才能生成供 Azure Monitor 收集的监视数据。 本文描述如何通过所需的不同步骤来完全实现使用常用配置的 Azure Monitor，以便监视 Azure 订阅中的所有资源。 本文为每个步骤提供了基本说明，其中包含介绍详细配置要求的其他文档的链接。

> [!IMPORTANT]
> Azure Monitor 的功能及其配置各不相同，具体取决于你的业务要求与所启用功能的成本，二者必须平衡。 下面的每个步骤都会说明是否存在潜在成本，你应该在继续执行该步骤之前评估这些成本。 请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)，了解完整的定价详情。

## <a name="configuration-goals"></a>配置目标
完全实现 Azure Monitor 的目标是收集所有云资源和应用程序的所有可用数据，并根据该数据在 Azure Monitor 中启用尽可能多的功能。

Azure Monitor 收集的数据会发送到 [Azure Monitor 指标](platform/data-platform-metrics.md)或 [Azure Monitor 日志](platform/data-platform-logs.md)。 每个指标/日志存储不同类型的数据，并启用不同类型的分析和警报。 有关不同警报类型的说明，请参阅 [比较 Azure Monitor 度量值和日志](platform/data-platform.md) ，以比较 Microsoft Azure 中的两个和 [警报概述](platform/alerts-overview.md) 。 

某些数据可以同时发送到指标和日志，以便通过不同的功能来利用该数据。 在这些情况下，可能需要单独进行配置。 例如，Azure 资源会自动将指标数据发送到指标，该数据支持指标资源管理器和指标警报。 你必须为每个资源创建诊断设置，以便将该指标数据发送到日志，这样就可以使用 Log Analytics 分析其他日志数据的性能趋势。 以下部分标识了数据发送到的位置，并提供了将数据发送到所有可能的位置所需的每个步骤。

你可能有其他要求，例如监视 Azure 之外的资源，以及在 Azure Monitor 之外发送数据。 对本文所述的功能进行其他配置即可实现此类要求。 如需其他配置选项，请访问每个步骤中的文档链接。

## <a name="collect-data-from-azure-resources"></a>收集来自 Azure 资源的数据

> [!NOTE]
> 有关使用 Azure Monitor 监视虚拟机的完整指南，请参阅[使用 Azure Monitor 监视 Azure 资源](insights/monitor-azure-resource.md)。

对 Azure 资源进行的某些监视是自动提供的，无需进行配置；但若要收集其他监视数据，则必须执行配置步骤。 下表说明了从 Azure 资源收集所有可用数据所需的配置步骤，包括通过哪个步骤将数据发送到 Azure Monitor 指标和 Azure Monitor 日志。 以下部分更深入地详述了每个步骤。

[ ![部署 Azure 资源监视](media/deploy/deploy-azure-resources.png) ](media/deploy/deploy-azure-resources.png#lightbox)

### <a name="no-configuration"></a>无配置
在创建 Azure 订阅时，无需进行任何配置即可启用 Azure Monitor 的以下功能。 此监视功能不会产生相关的费用。

[Azure Active Directory 日志](../active-directory/reports-monitoring/overview-reports.md) - 提供登录活动的租户级历史记录，以及在 Azure Active Directory 中所做更改的审核日志。 请参阅 [Azure Active Directory 门户中的审核活动报表](../active-directory/reports-monitoring/concept-audit-logs.md)和 [Azure Active Directory 门户中的登录活动报表](../active-directory/reports-monitoring/concept-sign-ins.md)，详细了解 Azure Active Directory 日志以及在 Azure 门户中查看它们的方法。

[Azure 日志](platform/platform-logs-overview.md) - 方便用户深入了解管理组以及 Azure 中发生的订阅级别事件。 当你创建新的 Azure 资源、修改资源或执行重要活动时，事件会自动写入活动日志。 你可以在 Azure 门户中查看事件，并创建在发生特定事件时会触发的活动日志警报。 若要详细了解活动日志以及在 Azure 门户查看活动日志的方法，请参阅 [Azure 活动日志](platform/activity-log.md)。

[平台指标](platform/metrics-supported.md) - 从 Azure 服务自动收集到 [Azure Monitor 指标](platform/data-platform-metrics.md)中。 此数据通常显示在 Azure 门户中针对不同服务的“概览”页上。 请参阅 [Azure 指标资源管理器入门](platform/metrics-getting-started.md)，详细了解如何在 Azure 门户中分析平台指标。 


### <a name="create-log-analytics-workspace"></a>创建 Log Analytics 工作区
你需要至少一个 Log Analytics 工作区来启用 [Azure Monitor 日志](platform/data-platform-logs.md)，这样才能收集各种数据，例如来自 Azure 资源的日志、来自 Azure 虚拟机的来宾操作系统的数据，以及用于获取大多数 Azure Monitor 见解的数据。 其他服务（如 Azure Sentinel 和 Azure 安全中心）也使用 Log Analytics 工作区，该工作区可以与用于 Azure Monitor 的工作区相同。 一开始可以使用单个工作区来支持此监视，但若要了解何时使用多个工作区，请参阅[设计 Azure Monitor 日志部署](platform/design-logs-deployment.md)。

创建 Log Analytics 工作区不会产生任何费用，但在配置要收集到其中的数据后，可能会产生费用。 有关详细信息，请参阅[使用 Azure Monitor 日志管理使用情况和成本](platform/manage-cost-storage.md)。  

请参阅[在 Azure 门户中创建 Log Analytics 工作区](learn/quick-create-workspace.md)，了解如何创建初始 Log Analytics 工作区。 请参阅[管理对 Azure Monitor 中的日志数据和工作区的访问](platform/manage-access.md)，了解如何配置访问权限。 

### <a name="create-diagnostic-setting-to-collect-tenant-and-subscription-logs"></a>创建诊断设置以收集租户和订阅日志
虽然针对租户的 [Azure Active Directory 日志](../active-directory/reports-monitoring/overview-reports.md)和针对订阅的[活动日志](platform/platform-logs-overview.md)是自动收集的，但将其发送到 Log Analytics 工作区可让你使用 Log Analytics 中的日志查询将这些事件和其他日志数据一起进行分析。 这样，你还可以创建日志查询警报，这是对 Azure Active Directory 日志发出警报并提供比活动日志警报更复杂的逻辑的唯一方式。

没有将活动日志发送到工作区的费用，但有针对 Azure Active Directory 日志的数据引入和保留费用。 

请参阅将 [Azure AD 日志与 Azure Monitor 日志集成](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) ，并 [创建诊断设置以将平台日志和指标发送到不同的目标](platform/diagnostic-settings.md) ，以创建租户和订阅的诊断设置，以便将日志条目发送到 Log Analytics 工作区。 

### <a name="create-diagnostic-setting-to-collect-resource-logs-and-platform-metrics"></a>创建诊断设置以收集资源日志和平台指标
Azure 中的资源会自动生成[资源日志](platform/platform-logs-overview.md)，这些日志提供资源中执行的操作的详细信息。 但与平台指标不同的是，你需要配置要收集的资源日志。 请创建诊断设置以将其发送到 Log Analytics 工作区，以便将其与 Azure Monitor 日志所使用的其他数据组合在一起。 同一诊断设置还可用于将大多数资源的平台指标发送到同一工作区，这样你就可以使用日志查询和其他收集的数据来分析指标数据。

该收集操作需付费，因此请在对大量资源进行实施之前参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。 另请参阅[使用 Azure Monitor 日志管理使用情况和成本](platform/manage-cost-storage.md)，详细了解如何优化日志收集成本。

请参阅[创建诊断设置以收集 Azure 中的资源日志和指标](platform/diagnostic-settings.md#create-in-azure-portal)，了解如何创建 Azure 资源的诊断设置。 由于需要为每个 Azure 资源创建诊断设置，因此请参阅[大规模部署 Azure Monitor](deploy-scale.md)，详细了解如何使用 [Azure Policy](../governance/policy/overview.md) 在每次创建 Azure 资源时自动创建设置。

### <a name="enable-insights-and-solutions"></a>启用见解和解决方案
见解和解决方案为特定服务或解决方案提供专用监视。 见解使用较新的 Azure Monitor 功能（例如工作簿），因此，如果见解适用于服务，则应使用见解。 它们在每个 Azure 订阅中自动提供，但可能需要某些配置才能完全发挥作用。 它们通常会使用之前配置的平台指标和资源日志，并可收集其他数据。

解决方案必须添加到每个订阅，必须以独占方式处理 Azure Monitor 日志中的数据，可以收集额外的日志数据。

解决方案或见解不收费，但其收集的数据可能收费。

请参阅 [Azure Monitor 监视哪些内容？](monitor-reference.md)，获取 Azure Monitor 中提供的见解和解决方案的列表。 请参阅每个见解和解决方案的相关文档，了解独特的配置或定价信息。 

## <a name="collect-data-from-virtual-machines"></a>从虚拟机收集数据

> [!NOTE]
> 有关使用 Azure Monitor 监视虚拟机的完整指南，请参阅 [使用 Azure Monitor 监视 Azure 虚拟机](insights/monitor-vm-azure.md) 。 

虚拟机生成的数据与其他 Azure 资源类似，但你需要一个代理来从来宾操作系统收集数据。 请参阅 [Azure Monitor 代理概述](platform/agents-overview.md) ，比较 Azure Monitor 使用的代理。 

[用于 VM 的 Azure Monitor](insights/vminsights-overview.md) 使用 Log Analytics 代理和依赖项代理从虚拟机的来宾操作系统中收集数据，因此你可以将这些代理作为此见解的实现的一部分进行部署。 这会为使用它的其他服务（例如 Azure 安全中心）启用 Log Analytics 代理。


[![部署 AZURE VM ](media/deploy/deploy-azure-vm.png)](media/deploy/deploy-azure-vm.png#lightbox)


### <a name="configure-workspace-for-azure-monitor-for-vms"></a>为用于 VM 的 Azure Monitor 配置工作区
用于 VM 的 Azure Monitor 需要一个 Log Analytics 工作区，该工作区通常与从其他 Azure 资源中收集数据时所创建的工作区相同。 启用任何虚拟机之前，必须将用于 VM 的 Azure Monitor 所需的解决方案添加到工作区。

若要详细了解如何配置用于 VM 的 Azure Monitor 的 Log Analytics 工作区，请参阅 [配置用于 VM 的 Azure Monitor Log Analytics 工作区](insights/vminsights-configure-workspace.md) 。

### <a name="enable-azure-monitor-for-vms-on-each-virtual-machine"></a>启用每个虚拟机上的用于 VM 的 Azure Monitor
配置工作区后，可以通过安装 Log Analytics 代理和依赖项代理来启用每个虚拟机。 有多种方法可安装这些代理，其中包括 Azure 策略，允许你在创建每个虚拟机时自动配置这些代理。 用于 VM 的 Azure Monitor 收集的性能数据和进程详细信息存储在 Azure Monitor 日志中。

请参阅 [启用用于 VM 的 Azure Monitor 概述](insights/vminsights-enable-overview.md) ，了解如何将代理部署到你的虚拟机，并使其能够进行监视。

### <a name="configure-workspace-to-collect-events"></a>配置工作区以收集事件
用于 VM 的 Azure Monitor 将从每个虚拟机的来宾操作系统中收集性能数据以及进程的详细信息和依赖项。 Log Analytics 代理还可以从来宾收集日志，包括来自 Windows 的事件日志和 Linux 中的 syslog。 它从其连接到的 Log Analytics 工作区中检索这些日志的配置。 只需配置工作区一次，每次代理连接时，它将下载任何配置更改。 

有关配置 Log Analytics 工作区以从代理虚拟机收集额外数据的详细信息，请参阅 [中的代理数据源 Azure Monitor](platform/agent-data-sources.md) 。

> [!NOTE]
> 你还可以配置工作区以收集性能计数器，但这很可能是由用于 VM 的 Azure Monitor 收集的性能数据冗余的。 工作区收集的性能数据将存储在 *Perf* 表中，而用于 VM 的 Azure Monitor 收集的性能数据将存储在 *InsightsMetrics* 表中。 仅当需要用于 VM 的 Azure Monitor 尚未收集的计数器时，才在工作区中配置性能收集。

### <a name="diagnostic-extension-and-telegraf-agent"></a>诊断扩展和 Telegraf 代理
用于 VM 的 Azure Monitor 使用将性能数据发送到 Log Analytics 工作区但不 Azure Monitor 度量值的 Log Analytics 代理。 将此数据发送到度量值后，可以使用指标资源管理器分析该数据，并将其用于指标警报。 这需要 Windows 上的诊断扩展和 Linux 上的 Telegraf 代理。

有关安装和配置这些代理的详细信息，请参阅 [安装和配置 Windows Azure 诊断扩展 (WAD) ](platform/diagnostics-extension-windows-install.md) 和 [使用 InfluxData Telegraf 代理收集 Linux VM 的自定义指标](platform/collect-custom-metrics-linux-telegraf.md) 。


## <a name="monitor-applications"></a>监视应用程序
Azure Monitor 使用 [Application Insights](app/app-insights-overview.md) 来监视自定义应用程序，你必须为要监视的每个应用程序配置 Application Insights。 配置过程因所监视的应用程序的类型和要执行的监视类型而异。 Application Insights 收集的数据存储在 Azure Monitor 指标、Azure Monitor 日志和 Azure blob 存储中，具体取决于功能。 性能数据同时存储在 Azure Monitor 指标和 Azure Monitor 日志中，无需其他配置。

### <a name="create-an-application-resource"></a>创建应用程序资源
必须在 Application Insights 中为要监视的每个应用程序创建一个资源。 Application Insights 收集的日志数据存储在基于工作区的应用程序 Azure Monitor 日志中。 经典应用程序的日志数据存储在 Log Analytics 的工作区中，如 [数据结构](platform/data-platform-logs.md#structure-of-data)中所述。

 创建应用程序时，必须选择是使用经典还是基于工作区。 请参阅 [创建 Application Insights 资源](app/create-new-resource.md) 来创建经典应用程序。 请参阅 [基于工作区的 Application Insights 资源 (预览) ](app/create-workspace-resource.md) 以创建基于工作区的应用程序。

### <a name="configure-codeless-or-code-based-monitoring"></a>配置无代码监视或基于代码的监视
若要为应用程序启用监视功能，你必须决定是使用无代码监视还是基于代码的监视。 配置过程取决于此决定以及要监视的应用程序的类型。

**无代码监视**最容易实现，可在代码开发后进行配置。 它不要求对代码进行更新。 请参阅以下资源，详细了解如何根据应用程序来启用监视。

- [托管在 Azure Web 应用上的应用程序](app/azure-web-apps.md)
- [Java 应用程序](app/java-in-process-agent.md)
- [在 Azure VM 或 Azure 虚拟机规模集的 IIS 中托管的 ASP.NET 应用程序](app/azure-vm-vmss-apps.md)
- [在 VM 的 IIS 中托管的 ASP.NET 应用程序](app/monitor-performance-live-website-now.md)


**基于代码的监视**可以更好地进行自定义，并且可以收集其他遥测数据，但需要在 Application Insights SDK NuGet 包上向代码添加依赖项。 请参阅以下资源，详细了解如何根据应用程序来启用监视。

- [ASP.NET 应用程序](app/asp-net.md)
- [ASP.NET Core 应用程序](app/asp-net-core.md)
- [.NET 控制台应用程序](app/console.md)
- [Java](app/java-get-started.md)
- [Node.js](app/nodejs.md)
- [Python](app/opencensus-python.md)
- [其他平台](app/platforms.md)

### <a name="configure-availability-testing"></a>配置可用性测试
Application Insights 中的可用性测试是重复测试，可以在全球各地定期监视应用程序的可用性和响应能力。 可以免费创建一项简单的 ping 测试，也可以创建一个 Web 请求序列来模拟具有关联成本的用户交易。 

请参阅[监视任意网站的可用性](app/monitor-web-app-availability.md)以获取不同类型测试的摘要，并详细了解如何创建它们。

### <a name="configure-profiler"></a>配置探查器
Application Insights 中的探查器针对 .NET 应用程序提供性能跟踪。 它有助于识别在处理特定 Web 请求时花费时间最长的“热”代码路径。 配置探查器的过程因应用程序类型而异。 

请参阅[使用 Application Insights 探查 Azure 中的生产应用程序](app/profiler-overview.md)，详细了解如何配置探查器。

### <a name="configure-snapshot-debugger"></a>配置 Snapshot Debugger
Application Insights 中的 Snapshot Debugger 可以监视来自 .NET 应用程序的异常遥测数据，并收集常出现的异常的快照，为诊断生产中的问题提供所需信息。 配置 Snapshot Debugger 的过程因应用程序类型而异。 

请参阅 [.NET 应用中发生异常时的调试快照](app/snapshot-debugger.md)，详细了解如何配置 Snapshot Debugger。


## <a name="visualize-data"></a>可视化数据
见解和解决方案会包括其自己的工作簿和视图，用于分析其数据。 除此之外，你还可以创建自己的[可视化效果](visualizations.md)，其中包括用于 Azure Monitor 数据和仪表板的工作簿，以将 Azure Monitor 数据与 Azure 中其他服务的数据合并在一起。


### <a name="create-workbooks"></a>创建工作簿
Azure Monitor 中的[工作簿](platform/workbooks-overview.md)用于在 Azure 门户中创建丰富的视觉对象报表。 可以组合使用 Azure Monitor 指标和 Azure Monitor 日志中的不同数据集，以创建统一的交互式体验。 可以在 Azure Monitor 菜单的“工作簿”选项卡中访问工作簿库。 

请参阅 [Azure Monitor 工作簿](platform/workbooks-overview.md)，详细了解如何创建自定义工作簿。

### <a name="create-dashboards"></a>创建仪表板
[Azure 仪表板](../azure-portal/azure-portal-dashboards.md)是适用于 Azure 的主要仪表板技术，允许将 Azure Monitor 数据与其他服务的数据结合使用，以便在 Azure 基础结构上提供单个窗格。 请参阅[创建和共享 Log Analytics 数据的仪表板](learn/tutorial-logs-dashboards.md)，详细了解如何创建包含 Azure Monitor 日志数据的仪表板 

请参阅[使用 Azure Application Insights 创建自定义 KPI 仪表板](learn/tutorial-app-dashboards.md)，详细了解如何创建包含 Application Insights 数据的仪表板。 

## <a name="alerts"></a>警报
Azure Monitor 中的警报会主动通知你在监视数据中发现的重要数据或模式。 某些见解会在没有配置的情况下生成警报。 对于其他情况，你需要创建[警报规则](platform/alerts-overview.md)，其中包括要分析的数据、关于何时生成警报的条件，以及操作组（用于定义生成警报时要执行的操作）。 


### <a name="create-action-groups"></a>创建操作组
[操作组](platform/action-groups.md)是警报规则所使用的一系列通知首选项，用来确定触发警报时要执行的操作。 操作示例包括发送邮件或短信、调用 Webhook 或将数据发送到 ITSM 工具。 一个警报规则至少需要一个操作组，而一个操作组则可由多个警报规则使用。

请参阅[在 Azure 门户中创建和管理操作组](platform/action-groups.md)，详细了解创建操作组的方法以及操作组可以包含的不同操作的说明。


### <a name="create-alert-rules"></a>创建警报规则
有多种类型的警报规则由其使用的数据类型定义。 每种都有不同的功能和不同的成本。 应遵循的基本策略是使用能够提供所需逻辑但成本最低的警报规则类型。

- [活动日志规则](platform/activity-log-alerts.md)。 创建一个警报来响应与指定条件匹配的新活动日志事件。 这些警报不收费，因此应该是你的第一选择。 请参阅[使用 Azure Monitor 创建、查看和管理活动日志警报](platform/alerts-activity-log.md)，详细了解如何创建活动日志警报。
- [指标警报规则](platform/alerts-metric-overview.md)。 创建一个警报来响应一个或多个超出阈值的指标值。 指标警报是有状态的，这意味着：当值低于阈值时警报会自动关闭，并且仅在状态更改时发出通知。 指标警报会产生成本，但该成本明显小于日志警报。 请参阅[使用 Azure Monitor 创建、查看和管理指标警报](platform/alerts-metric.md)，详细了解如何创建指标警报。
- [日志警报规则](platform/alerts-unified-log.md)。 当计划查询的结果与指定的条件匹配时创建警报。 此类规则是开销最大的警报规则，但允许最复杂的条件。 请参阅[使用 Azure Monitor 创建、查看和管理日志警报](platform/alerts-log.md)，详细了解如何创建日志查询警报。
- [应用程序警报](app/monitor-web-app-availability.md)允许对 Web 应用程序执行主动的性能和可用性测试。 你可以免费进行一次简单的 ping 测试，但更复杂的测试会收费。 请参阅[监视任意网站的可用性](app/monitor-web-app-availability.md)以获取不同测试的说明，并详细了解如何创建它们。


## <a name="next-steps"></a>后续步骤

- 请参阅[使用 Azure Policy 大规模部署 Azure Monitor](deploy-scale.md)。