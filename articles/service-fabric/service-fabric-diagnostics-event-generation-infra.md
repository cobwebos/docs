---
title: Azure Service Fabric 平台级别监视
description: 了解用于监视和诊断 Azure Service Fabric 群集的平台级别事件和日志。
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: ce0cd0866cc6daa36d598767a486faeabac8076d
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791810"
---
# <a name="monitoring-the-cluster"></a>监视群集

在群集级别进行监视以确定硬件和群集的运行情况是否符合预期是非常重要的。 在硬件发生故障期间，Service Fabric 可保持应用程序运行，但用户仍需要诊断错误是在应用程序中还是底层基础结构中发生。 还应该监视群集以便更好地规划容量，帮助决定添加或删除硬件。

Service Fabric 通过 EventStore 和各种现成的日志通道将多个结构化平台事件作为 [Service Fabric 事件](service-fabric-diagnostics-events.md)公开。 

在 Windows 上，Service Fabric 事件可通过使用一组相关 `logLevelKeywordFilters`（用于在操作通道与“数据和消息”通道之间进行选择）从单个 ETW 提供程序获得 - 这是我们用来分离出要根据需要筛选的传出 Service Fabric 事件的方式。

* **可操作**：由 Service Fabric 和群集执行的高级操作，包括出现节点事件、部署新应用程序或升级回滚等。可在[此处](service-fabric-diagnostics-event-generation-operational.md)查看事件的完整列表。  

* **可操作 - 详细信息**  
运行状况报告和负载均衡决策。

可通过各种方式访问操作通道，包括 ETW/Windows 事件日志、[EventStore](service-fabric-diagnostics-eventstore.md)（适用于 Windows 群集的 Windows 版本 6.2 及更高版本上可用）。 使用 EventStore 可以按实体（实体包括群集、节点、应用程序、服务、分区、副本和容器）访问群集的事件，并通过 REST API 和 Service Fabric 客户端库将其公开。 使用 EventStore 监视开发/测试群集，并获取生产群集状态的时间点了解。

* **数据和消息**  
消息（当前仅限 ReverseProxy）和数据路径（可靠的服务模型）中生成的关键日志和事件。

* **数据和消息 - 详细信息**  
包含群集中的数据和消息提供的所有非关键日志的详细通道（此通道的事件量非常大）。

除了这些之外，还提供了两个结构化的 EventSource 通道以及为支持目的而收集的日志。

* [Reliable Services 事件](service-fabric-reliable-services-diagnostics.md)  
特定于编程模型的事件。

* [Reliable Actors 事件](service-fabric-reliable-actors-diagnostics.md)  
特定于编程模型的事件和性能计数器。

* 支持日志  
Service Fabric 生成的系统日志，仅供我们提供支持时使用。

这些不同的通道涵盖了大部分推荐的平台级别日志记录。 若要改进平台级别日志记录，建议更好地了解运行状况模型和添加自定义运行状况报表，并添加自定义性能计数器，以实时了解服务和应用程序对群集的影响  。

为了利用这些日志，强烈建议在 Azure 门户中创建群集期间启用“诊断”。 如果开启诊断，部署群集时，Windows Azure 诊断就可确认运行 Operational、Reliable Services 和 Reliable Actors 通道，并按照[通过 Azure 诊断聚合事件](service-fabric-diagnostics-event-aggregation-wad.md)中所述存储数据。

## <a name="azure-service-fabric-health-and-load-reporting"></a>Azure Service Fabric 运行状况和负载报告

Service Fabric 具有自身的运行状况模型，以下文章对此做了详细介绍：

- [Service Fabric 运行状况监视简介](service-fabric-health-introduction.md)
- [报告和检查服务运行状况](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [添加自定义 Service Fabric 运行状况报告](service-fabric-report-health.md)
- [查看 Service Fabric 运行状况报告](service-fabric-view-entities-aggregated-health.md)

运行状况监视对于运行服务的多个方面至关重要，尤其是在应用程序升级期间。 升级服务的每个升级域后，升级域必须在部署转到下一个升级域之前通过运行状况检查。 如果无法实现良好的运行状况，部署会回滚，使应用程序保持一种已知正常的状态。 尽管在回滚服务之前某些客户可能会受到影响，但大多数客户不会遇到问题。 此外，问题的解决速度相对较快，无需等待操作员的人工操作。 在代码中合并的运行状况检查越多，服务应对部署问题的弹性就越高。

服务运行状况的另一个方面是从服务报告指标。 指标在 Service Fabric 中非常重要，因为它们用于均衡资源使用量。 指标还可用作系统运行状况的指示器。 例如，假设某个应用程序包含许多服务，每个实例报告每秒请求数 (RPS) 指标。 如果一个服务使用的资源比另一个服务要多，Service Fabric 会围绕群集移动服务实例，尽量使资源利用率保持均衡。 有关资源利用的工作原理的详细说明，请参阅 [Manage resource consumption and load in Service Fabric with metrics](service-fabric-cluster-resource-manager-metrics.md)（在 Service Fabric 中使用指标管理资源消耗和负载）。

使用指标还能洞察服务的执行情况。 在不同的时间，都可以使用指标来检查服务是否根据所需的参数运行。 例如，如果趋势表明星期一上午 9 点的平均 RPS 为 1,000，则可以设置一份运行状况报告，在 RPS 低于 500 或高于 1,500 时发出警报。 尽管一切看上去可能正常，但还是值得执行一番检查，确保客户获得优越的体验。 服务可以定义一组可在执行运行状况检查时报告的指标，同时避免对群集的资源均衡造成影响。 为此，可将指标权重设置为零。 建议一开始为所有指标使用零权重，只有在确定指标加权会对群集的资源均衡产生何种影响时，才增大权重。

> [!TIP]
> 不要使用过多的加权指标， 否则可能难以了解服务实例为何会出于均衡目的而被移动， 并且某些指标可能会持续很长时间！

可以指明应用程序的运行状况和性能的任何信息都是指标和运行状况报告的候选项。 CPU 性能计数器可以告知节点的利用情况，但不会指明特定的服务是否正常，因为单个节点上可能运行了多个服务  。 但是，RPS、已处理的项数和请求延迟等指标都可以指明特定服务的运行状况。

## <a name="service-fabric-support-logs"></a>Service Fabric 支持日志

如需联系 Microsoft 支持部门来获取 Azure Service Fabric 群集方面的帮助，几乎始终都需要提供支持日志。 如果群集托管在 Azure 中，则会自动配置支持日志，并在创建群集的过程中收集这些日志。 日志存储在群集资源组中的专用存储帐户内。 该存储帐户没有固定的名称，但在其中可以看到以 *fabric* 开头的 Blob 容器和表。 有关为独立群集设置日志收集的信息，请参阅 [Create and manage a standalone Azure Service Fabric cluster](service-fabric-cluster-creation-for-windows-server.md)（创建和管理独立 Azure Service Fabric 群集）以及 [Configuration settings for a standalone Windows cluster](service-fabric-cluster-manifest.md)（Windows 独立群集的配置设置）。 对于独立的 Service Fabric 实例，应该将日志发送到本地文件共享。 **必须**提供这些日志才能获得支持，但是，这些日志只能由 Microsoft 客户支持团队使用。

## <a name="measuring-performance"></a>测量性能

测量群集性能有助于了解它如何处理负载以及如何做出关于缩放群集的决策（请参阅有关[在 Azure 上](service-fabric-cluster-scale-in-out.md)或[在本地](service-fabric-cluster-windows-server-add-remove-nodes.md)缩放群集的详细信息）。 将来分析日志时，性能数据还可用于比较你或你的应用程序和服务可能执行的操作。 

有关使用 Service Fabric 时要收集的性能计数器的列表，请参阅 [Service Fabric 中的性能计数器](service-fabric-diagnostics-event-generation-perf.md)

以下是设置群集收集性能数据的两种常见方式：

* **使用代理**  
这是从计算机中收集性能的首选方法，因为代理通常有可以收集的可能性能指标列表，并且选择要收集或更改的指标是一个相对简单的过程。 有关 Azure Monitor 服务的详细信息，请参阅 Service Fabric 的[Azure Monitor 日志集成](service-fabric-diagnostics-event-analysis-oms.md)中的 Azure Monitor 日志，并[设置 Log Analytics 代理](../log-analytics/log-analytics-windows-agent.md)，以了解有关 Log Analytics 代理的详细信息，这是一个可以获取群集 vm 和部署容器的性能数据的监视代理。

* **性能计数器到 Azure 表存储**  
还可将性能指标发送到与事件相同的表存储。 此操作需要更改 Azure 诊断配置以从群集中的 VM 读取适当的性能计数器，如果要部署任何容器，也能使其读取 Docker 统计数据。 阅读有关在 Service Fabric 中配置[WAD 中的性能计数器](service-fabric-diagnostics-event-aggregation-wad.md)，设置性能计数器集合。

## <a name="next-steps"></a>后续步骤

* 了解 Service Fabric [Azure Monitor 日志集成](service-fabric-diagnostics-event-analysis-oms.md)以收集群集诊断以及创建自定义查询和警报
* 了解 Service Fabric 内置诊断体验：[EventStore](service-fabric-diagnostics-eventstore.md)
* 在 Service Fabric 中演练某些[常见诊断方案](service-fabric-diagnostics-common-scenarios.md)
