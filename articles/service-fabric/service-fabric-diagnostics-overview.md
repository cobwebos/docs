---
title: "Azure Service Fabric 监视和诊断概述 | Microsoft Docs"
description: "了解对 Azure Service Fabric 群集、应用程序和服务的监视和诊断。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2017
ms.author: dekapur
ms.openlocfilehash: 88f4a23f89a1c8fd88db1df3a7ff03ae5df64c0f
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>对 Azure Service Fabric 进行监视和诊断

监视和诊断对任何环境中的开发、测试和部署应用程序和服务都至关重要。 监视和诊断有助于确保应用程序和服务在本地开发环境或生产环境中按预期运行，计划和实现监视和诊断时，Service Fabric 解决方案效果最佳。

监视和诊断的主要目标是：
* 检测和诊断硬件和基础结构问题
* 检测软件和应用问题，减少服务停机时间
* 了解资源消耗情况，帮助推动运营决策
* 优化应用程序、服务和基础结构性能
* 生成业务见解并确定改进区域


监视和诊断的整体工作流程分为三个步骤：

1. **事件生成**：这包括基础结构（群集）级、平台级、应用程序/服务级事件（日志、跟踪、自定义事件）
2. 事件聚合：需要先收集和聚合生成的事件才能显示这些事件
3. 分析：需可视化事件并能够以某种格式访问事件，以便按需进行分析和显示

多个产品可用于以上三个方面，你也可以为每个方面选择不同的技术。 请务必确保各种技术协同工作，为群集提供端到端的监视解决方案。

## <a name="event-generation"></a>事件生成

监视和诊断工作流的第一步是创建和生成事件和日志。 这些事件、日志和跟踪在以下两个级别生成：平台层（包括群集、计算机或 Service Fabric 操作）或应用程序层（向群集中部署的应用和服务添加的任何检测）。 Service Fabric 确实会默认提供一些检测，但两个级别中的事件都可以自定义。

请参阅[平台级事件](service-fabric-diagnostics-event-generation-infra.md)和[应用程序级事件](service-fabric-diagnostics-event-generation-app.md)，了解所含内容以及如何添加其他检测。

在决定要使用哪个日志记录提供程序后，需要确保日志能够正常聚合和存储。

## <a name="event-aggregation"></a>事件聚合

收集应用程序和群集生成的日志和事件时，通常推荐使用 [Azure 诊断](service-fabric-diagnostics-event-aggregation-wad.md)（更类似于基于代理的日志收集）或 [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)（进程内日志收集）。

如果日志源和目标集不经常更改，并且在源和目标之间存在简单映射，则对于 Service Fabric 服务，使用 Azure 诊断扩展收集应用程序日志是一个不错的选择。 因为在资源管理器层进行 Azure 诊断配置，所以对配置进行大量更改需要更新/重新部署群集。 此外，最好确保将日志存储在较持久的位置，以供各种分析平台从该位置访问日志。 这意味着最终管道的效率比使用 EventFlow 等选项的效率低。

使用 [EventFlow](https://github.com/Azure/diagnostics-eventflow) 可以让服务将其日志直接发送给分析和可视化平台和/或存储。 其他库（ILogger、Serilog 等）可以实现相同效果，但 EventFlow 的优势在于它专用于进程内日志收集，并且支持 Service Fabric 服务。 这种方式具有许多潜在优势：

* 易于配置和部署
    * 诊断数据收集配置只是服务配置的一部分。 将其与应用程序的其余部分始终保持“同步”很简单
    * 可轻松基于每个应用程序或每个服务进行配置
    * 通过 EventFlow 配置数据目标只需添加相应的 NuGet 包并更改 eventFlowConfig.json 文件即可
* 灵活性
    * 只要客户端库支持目标数据存储系统，应用程序就可以将数据发送至任何需要的地方。 可以根据需要添加新的目标
    * 可以实现复杂的捕获、筛选和数据聚合规则
* 可访问内部应用程序数据和上下文
    * 应用程序/服务进程内运行的诊断子系统可以轻松地随着上下文信息而扩展跟踪

需要注意的一点是，这两个选项不是相互排斥的。虽然完成类似工作可以只使用其中的一个选项，但也可以同时设置两个选项。 在大部分情况下，将代理与进程内收集结合使用可以实现更可靠的监视工作流。 可以选择 Azure 诊断扩展（代理）作为平台级日志的路径，而对应用程序级日志使用 EventFlow（进程内集合）。 确定最适合自己的方法之后，就需要思考希望如何显示和分析数据。

## <a name="event-analysis"></a>事件分析

关于对监视和诊断数据进行分析和可视化，市场上有几个优秀的平台。 推荐使用 [OMS](service-fabric-diagnostics-event-analysis-oms.md) 和 [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) 这两个平台，因为这两个平台更好地集成了 Service Fabric，但也可以考虑 [Elastic Stack](https://www.elastic.co/products)（尤其是想在离线环境中运行群集时）、[Splunk](https://www.splunk.com/) 和任何你喜欢的平台。

选择平台时应该考虑的关键点包括：用户界面和查询选项的舒适度、实现数据可视化效果和创建易读仪表板的能力、平台提供的用于增强监视功能的其他工具（例如自动警报）等。

除选择的平台之外，将 Service Fabric 群集设置为 Azure 资源时，还可以访问 Azure 对计算机的现有监视，这对特定的性能和指标监视非常有用。

### <a name="azure-monitor"></a>Azure 监视器

可以使用 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) 来监视构建 Service Fabric 群集时所在的许多 Azure 资源。 系统会自动收集[虚拟机规模集](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)和每个[虚拟机](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines)的一组指标并将其显示在 Azure 门户中。 若要在 Azure 门户中查看收集的信息，请选择包含 Service Fabric 群集的资源组。 然后，选择你想要查看虚拟机规模集。 在“监视”部分中，选择“指标”可查看值的图表。

![Azure 门户视图：收集的指标信息](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

若要自定义图表，请遵照 [Metrics in Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)（Microsoft Azure 中的指标）中的说明。 还可以根据 [Create alerts in Azure Monitor for Azure services](../monitoring-and-diagnostics/insights-alerts-portal.md)（在 Azure Monitor 中为 Azure 服务创建警报）中所述，基于这些指标创建警报。 可以根据 [Configure a web hook on an Azure metric alert](../monitoring-and-diagnostics/insights-webhooks-alerts.md)（针对 Azure 指标警报配置 Webhook）中所述，使用 Webhook 将警报发送到通知服务。 Azure Monitor 仅支持一个订阅。 如果需要监视多个订阅，或需要其他功能，Microsoft Operations Management Suite 中的 [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 提供了全方位的 IT 管理解决方案，适用于本地基础结构和基于云的基础结构。 可将来自 Azure 监视的数据直接路由到 Log Analytics，以便在一个位置查看整个环境的指标和日志。

## <a name="next-steps"></a>后续步骤

### <a name="watchdogs"></a>监视器

监视器是一个独立的服务，可以监视各个服务的运行状况和负载，并报告运行状况模型层次结构中任何组件的运行状况。 这有助于防止出现基于单个服务的视图所检测不到的错误。 监视器也是一个托管代码的好选择，在此无需用户交互即可执行补救措施（例如每隔特定时间就清理一次存储中的日志文件）。 可在[此处](https://github.com/Azure-Samples/service-fabric-watchdog-service)获取监视软件服务实现示例。

开始了解如何生成[平台级](service-fabric-diagnostics-event-generation-infra.md)和[应用程序级](service-fabric-diagnostics-event-generation-app.md)事件和日志。