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
ms.date: 01/10/2018
ms.author: dekapur
ms.openlocfilehash: 1da2fab92c6fd51c5fc18589da5f8bbec2929503
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>对 Azure Service Fabric 进行监视和诊断

本文概述如何为 Service Fabric 群集中运行的应用程序设置监视和诊断。 在任何云环境中开发、测试和部署工作负荷时，监视和诊断至关重要。 使用监视可以跟踪应用程序的使用方式、资源利用率，以及群集的总体运行状况。 可以使用此信息来诊断和更正群集中的任何问题，帮助避免将来发生此类问题。 

监视和诊断的主要目标是：
* 检测和诊断基础结构问题
* 检测应用程序问题
* 了解资源消耗
* 跟踪应用程序、服务和基础结构的性能

在 Service Fabric 群集中，监视和诊断数据来自三个级别：应用程序、平台（群集）和基础结构。 
* [应用程序级别](service-fabric-diagnostics-event-generation-app.md)包括有关应用程序性能的数据，以及添加的其他任何自定义日志数据。 从应用程序本身的角度讲，应用程序监视数据最终应该传入 Application Insights (AI)。 可以通过 AI SDK、EventFlow 或所选的其他管道传输这些数据。
* [平台级别](service-fabric-diagnostics-event-generation-infra.md)包括针对群集执行的操作所生成的事件，这些事件与群集及其上运行的应用程序的管理相关。 应该使用 Service Fabric 分析解决方案将平台监视数据发送到 OMS Log Analytics，以帮助可视化传入事件。 通常会通过 Windows 或 Linux Azure 诊断扩展将此数据发送到存储帐户，供 OMS Log Analytics 访问。 
* 基础结构级别侧重于[性能监视](service-fabric-diagnostics-event-generation-perf.md)，它分析关键指标和性能计数器以确定资源利用率和负载。 可以使用代理实现性能监视 - 我们建议使用 OMS (Microsoft Monitoring) 代理，使性能数据最终传入平台事件所在的同一位置，从而在关联整个群集中发生的更改时获得更好的诊断体验。 

![诊断概述图表](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="monitoring-scenarios"></a>监视方案

本部分介绍有关监视 Service Fabric 群集的重要方案 - 应用程序、群集、性能和运行状况监视。 其中将会讨论每种方案的意图和总体方法。 可以在[最佳做法 - 监视和诊断](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)中找到有关这些方案以及适用于 Azure 资源的其他一般性监视建议的更多详细信息。 

这些方案也松散映射到前面所述的三个级别的监视和诊断数据，也就是说，要在群集中适当处理每种方案，应该获取来自相应级别的监视和诊断数据。 但运行状况监视方案例外，因为它跨越整个群集以及群集中运行的任何组件。

## <a name="application-monitoring"></a>应用程序监视
应用程序监视跟踪所构建应用程序的功能和组件的使用方式。 监视应用程序可以确保捕获影响用户的问题。 监视应用程序可能对以下工作有帮助：
* 确定应用程序负载和用户流量 - 是否需要缩放服务来满足用户需求，或解决应用程序中的潜在瓶颈？
* 识别服务通信和群集远程控制的问题
* 判断用户正在使用应用程序做些什么 - 检测应用程序可能有助于将来的功能开发，以及更好地诊断应用错误

对于 Service Fabric 中应用程序级别的监视，我们建议使用 Application Insights (AI)。 AI 与 Service Fabric 的集成包括 Visual Studio 和 Azure 门户的工具体验，可以在 AI 仪表板中了解 Service Fabric 服务上下文和远程控制，从而获得现成的全面日志记录体验。 尽管使用 AI 时系统会自动创建和收集许多日志，但我们建议将更多的自定义日志添加到应用程序，并在 AI 中将这些日志与系统提供的日志一同显示，以创建更丰富的诊断体验来处理将来出现的问题。 在[使用 Application Insights 执行事件分析](service-fabric-diagnostics-event-analysis-appinsights.md)中详细了解如何将 AI 与 Service Fabric 配合使用。 

若要开始检测代码以监视应用程序，请转到[应用程序级别事件和日志生成](service-fabric-diagnostics-event-generation-app.md)。 

### <a name="monitoring-application-availability"></a>监视应用程序可用性
有时，群集中的所有组件看上去都在按预期运行并报告为正常，但应用程序不可访问或不可连接。 尽管这种情况不很常见，但我们必须知道在发生此情况时应如何尽快予以解决。 可用性监视在很大程度上涉及到跟踪系统组件的可用性，以了解系统的总体“运行时间”。 在群集中，我们注重从应用程序的层面保持可用性 - 平台会尽力确保应用程序生命周期管理方案不会导致停机。 尽管如此，群集中的各种问题仍可能影响应用程序的运行时间，在这种情况下，应用程序所有者通常需要立即了解问题所在。 我们建议在群集中连同其他所有应用程序一起部署一个监视器。 此类监视器的用途是根据设置的时间间隔检查应用程序的相应终结点，并报告这些终结点可供访问。 为实现此目的，也可以使用某个外部服务终结点按指定的时间间隔 ping 该终结点并返回报告。

## <a name="cluster-monitoring"></a>群集监视
若要确保平台及其上运行的所有工作负荷按预期运行，监视 Service Fabric 群集至关重要。 Service Fabric 的目标之一是确保在发生硬件故障之前应用程序可保持运行。 为了实现此目的，可以通过平台的系统服务功能检测基础结构问题，并快速将工作负荷故障转移到群集中的其他节点。 但在此特殊情况下，系统服务本身出现问题该怎么办？ 或者，在尝试移动工作负荷时，如果违反服务位置的规则该怎么办？ 监视群集可以随时了解群集中发生的活动，帮助有效诊断和解决问题。 需要分析的一些重要问题包括：
* 定位好应用程序并均衡群集中的负载后，Service Fabric 的行为方式是否符合预期？ 
* 对于确认有问题的群集，是否采取了相应的措施来修改其配置，这些措施是否发挥了预期的作用？ 缩放群集时，这一点尤为重要。
* Service Fabric 是否能够正确处理群集中的数据和服务间的通信？

Service Fabric 通过操作和数据与消息传递通道现成地提供整套事件。 在 Windows 中，这些事件以单个 ETW 提供程序的形式提供，其中包含一组相关的 `logLevelKeywordFilters`，用于在不同的通道之间进行选择。 在 Linux 中，所有平台事件通过 LTTng 传入一个表中，可在其中按需筛选事件。 

这些通道包含组织有序的结构化事件用于更好地了解群集的状态。 创建群集时默认会启用“诊断”并设置一个 Azure 存储表，来自这些通道的事件将发送到其中，方便将来进行查询。 可以在[平台级别事件和日志生成](service-fabric-diagnostics-event-generation-infra.md)中详细了解如何监视群集。 我们建议使用 OMS Log Analytics 监视群集。 OMS Log Analytics 提供了 Service Fabric 分析，这是一个特定于 Service Fabric 的解决方案 ，它提供自定义仪表板用于监视 Service Fabric 群集，以及查询群集的事件和设置警报。 [使用 OMS 执行事件分析](service-fabric-diagnostics-event-analysis-oms.md)中提供了相关的详细信息。 

### <a name="watchdogs"></a>监视器
监视器通常是一个独立的服务，可以监视各个服务的运行状况和负载、ping 终结点，以及报告群集中任何组件的运行状况。 这有助于防止出现基于单个服务的视图所检测不到的错误。 监视器也是一个托管代码的好选择，在此无需用户交互即可执行补救措施（例如每隔特定时间就清理一次存储中的日志文件）。 可在[此处](https://github.com/Azure-Samples/service-fabric-watchdog-service)获取监视软件服务实现示例。

## <a name="performance-monitoring"></a>性能监视
监视底层基础结构是了解群集状态和资源利用率的重要组成部分。 测量系统性能的过程取决于多种因素，其中的每种因素通常是通过关键性能指标 (KPI) 测量的。 可将 Service Fabric 相关的 KPI 作为性能计数器映射到可以从群集中的节点收集的指标。
这些 KPI 有助于：
* 了解资源利用率和负载 - 实现缩放群集或优化服务进程的目的
* 预测基础结构问题 - 许多问题在发生之前，都会出现性能骤然发生变化（下降）的前兆，因此，我们可以使用网络 I/O 和 CPU 利用率等 KPI 来预测和诊断基础结构问题

可以在[性能指标](service-fabric-diagnostics-event-generation-perf.md)中找到应在基础结构级别收集的性能计数器列表。 

对于应用程序级别的性能监视，Service Fabric 为 Reliable Services 和 Reliable Actors 编程模型提供了一组性能计数器。 如果使用其中的任一模型，这些性能计数器可以提供 KPI 来帮助确保执行组件正常启动和停止，或者以足够快的速度处理可靠服务请求。 有关这些 KPI 的详细信息，请参阅 [Reliable Services 远程处理的监视](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters)和 [Reliable Actors 的性能监视](service-fabric-reliable-actors-diagnostics.md#performance-counters)。 除此之外，如果已在应用程序中进行相应的配置，Application Insights 还会提供它所要收集的一组性能指标。

使用 OMS 代理可以收集相应的性能计数器，并在 OMS Log Analytics 中查看这些 KPI。 也可以使用 Azure 诊断代理扩展（或其他任何类似代理）来收集和存储可供分析的指标。 

## <a name="health-monitoring"></a>运行状况监视
Service Fabric 平台包含运行状况模型，针对群集中的实体状态提供可扩展的运行状况报告。 每个节点、应用程序、服务、分区、副本或实例都具有持续可更新的运行状况。 运行状况可能是“正常”、“警告”或“错误”。 可以根据群集中的问题，针对针对每个实体发出的运行状况报告更改运行状况。 可以随时按如下所示在 Service Fabric Explorer (SFX) 中检查实体的运行状况，或者通过平台的运行状况 API 查询。 还可以通过添加自己的运行状况报告或使用运行状况 API 来自定义运行状况报告和修改实体的运行状况。 可以在 [Service Fabric 运行状况监视简介](service-fabric-health-introduction.md)中找到有关运行状况模型的详细信息。

![SFX 运行状况仪表板](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

除了在 SFX 中查看最新的运行状况报告以外，还能以事件的形式查看每份报告。 可以通过操作通道收集运行状况事件（请参阅[使用 Azure 诊断聚合事件](service-fabric-diagnostics-event-aggregation-wad.md#collect-health-and-load-events)），这些事件存储在 OMS Log Analytics 中，供将来进行警报和查询。 这有助于检测可能影响应用程序可用性的问题，因此，我们建议针对相应的故障情景设置警报（通过 OMS 创建自定义警报）。

## <a name="monitoring-workflow"></a>监视工作流 

监视和诊断的整体工作流程分为三个步骤：

1. **事件生成**：包括基础结构、平台（群集）和应用程序级别的事件（日志、跟踪、自定义事件）
2. **事件聚合**：此阶段实际上通过管道（包括 Azure 诊断扩展或 EventFlow）将事件发送到某个工具中，然后可在该工具中分析这些事件
3. **分析**：必须可在某个工具中访问事件以进行分析 - 可视化、查询、警报，等等

多个产品可用于以上三个方面，你也可以为每个方面选择不同的技术。 请务必确保各种技术协同工作，为群集提供端到端的监视解决方案。

## <a name="event-generation"></a>事件生成

监视和诊断工作流的第一步是设置事件与日志数据的创建和生成过程。 这些事件、日志和性能计数器在所有三个级别发出：应用程序级别（添加到群集中部署的应用和服务的所有检测项）、平台级别（根据 Service Fabric 的操作从群集发出的事件）和基础结构级别（来自每个节点的性能指标）。 在其中每个级别收集的诊断数据可自定义，不过，Service Fabric 确实会启用一些默认检测。 

请参阅[平台级事件](service-fabric-diagnostics-event-generation-infra.md)和[应用程序级事件](service-fabric-diagnostics-event-generation-app.md)，了解所含内容以及如何添加其他检测。 此时必须做出的重要决策是如何检测应用程序。 对于 .NET 应用程序，我们建议使用 ILogger，但也可以浏览 EventSource、Serilog 和其他类似的库。 对于 Java，我们建议使用 Log4j。 此外，可以根据应用程序的性质使用其他多种选项。 请尽情探索最适合具体用例的选项，或选择最习惯使用的选项。 

在决定要使用哪个日志记录提供程序后，需要确保日志能够正常聚合和存储。

## <a name="event-aggregation"></a>事件聚合

收集应用程序和群集生成的日志和事件时，我们通常建议使用 [Azure 诊断扩展](service-fabric-diagnostics-event-aggregation-wad.md)（更类似于基于代理的日志收集）或 [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)（进程中日志收集）。 如果使用 Application Insights 并以 .NET 或 Java 进行开发，则我们建议使用 Application Insights SDK 而不是 EventFlow。

尽管使用其中一种工具就能完成类似的工作，但在大多数情况下，将 Azure 诊断扩展代理与进程中收集管道（AI SDK 或 EventFlow）结合使用可以创建更可靠的综合性监视工作流。 可以使用 Azure 诊断扩展代理作为平台级事件的路径，同时对应用程序级日志使用 AI SDK 或 EventFlow（进程中收集）。 

如果只想使用其中的一种工具，请记住一些要点。
* 如果日志源和目标集不经常更改，并且在源和目标之间存在简单映射，则对于 Service Fabric 服务，使用 Azure 诊断扩展收集应用程序日志是一个不错的选择。 因为在资源管理器层进行 Azure 诊断配置，所以对配置进行大量更改需要更新整个群集。 这意味着最终的效率比使用 AI SDK 或 EventFlow 更低。
* 使用 EventFlow 可让服务将其日志直接发送到分析和可视化平台和/或存储。 其他库（ILogger、Serilog 等）也可以实现相同的效果，但 EventFlow 的优势在于它专用于进程内日志收集，并且支持 Service Fabric 服务。 在配置和部署方便性方面，这往往能够提供多种优势，同时，为支持不同的日志记录库和分析工具提供更高的灵活性。 

## <a name="event-analysis"></a>事件分析

关于对监视和诊断数据进行分析和可视化，市场上有几个优秀的平台。 我们建议使用 [OMS](service-fabric-diagnostics-event-analysis-oms.md) 和 [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) 这两个平台，因为它们与 Service Fabric 集成。 此外，还应考虑 [Elastic Stack](https://www.elastic.co/products)（尤其是考虑在脱机环境中运行群集时）、[Splunk](https://www.splunk.com/) 或其他任何偏好的平台。 

选择平台时应该考虑的关键点包括：用户界面和查询选项的舒适度、实现数据可视化效果和创建易读仪表板的能力、平台提供的用于增强监视功能的其他工具（例如自动警报）等。

除选择的平台以外，将 Service Fabric 群集设置为 Azure 资源时，还可以访问 Azure 为计算机性能监视提供的现成功能。

### <a name="azure-monitor"></a>Azure 监视器

可以使用 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) 来监视构建 Service Fabric 群集时所在的许多 Azure 资源。 系统会自动收集[虚拟机规模集](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)和每个[虚拟机](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines)的一组指标并将其显示在 Azure 门户中。 若要在 Azure 门户中查看收集的信息，请选择包含 Service Fabric 群集的资源组。 然后，选择要查看的虚拟机规模集。 在左侧导航栏中的“监视”部分，选择“指标”查看值的图表。

![Azure 门户视图：收集的指标信息](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

若要自定义图表，请遵照 [Metrics in Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)（Microsoft Azure 中的指标）中的说明。 还可以根据 [Create alerts in Azure Monitor for Azure services](../monitoring-and-diagnostics/monitoring-overview-alerts.md)（在 Azure Monitor 中为 Azure 服务创建警报）中所述，基于这些指标创建警报。 

## <a name="next-steps"></a>后续步骤

* 在[平台级别事件和日志生成](service-fabric-diagnostics-event-generation-infra.md)中详细了解如何监视平台以及 Service Fabric 提供的事件
* 若要开始检测应用程序，请参阅[应用程序级别事件和日志生成](service-fabric-diagnostics-event-generation-app.md)
* 通过[在 Service Fabric 上监视和诊断 ASP.NET Core 应用程序](service-fabric-tutorial-monitoring-aspnet.md)，完成为应用程序设置 AI 的步骤
* 了解如何为监视容器设置 OMS Log Analytics - [在 Azure Service Fabric 上监视和诊断 Windows 容器](service-fabric-tutorial-monitoring-wincontainers.md)

