---
title: "Azure Service Fabric 平台级别监视 | Microsoft Docs"
description: "了解用于监视和诊断 Azure Service Fabric 群集的平台级别事件和日志。"
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
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: 2e320339f60b593c1cff68ca047c95f9cb7b33e2
ms.contentlocale: zh-cn
ms.lasthandoff: 07/20/2017

---

# <a name="platform-level-event-and-log-generation"></a>平台级别事件和日志生成

## <a name="monitoring-the-cluster"></a>监视群集

在平台级别进行监视以确定硬件和群集的运行情况是否符合预期是非常重要的。 在硬件发生故障期间，Service Fabric 可保持应用程序运行，但用户仍需要诊断错误是在应用程序中还是底层基础结构中发生。 还应该监视群集以便更好地规划容量，帮助决定添加或删除硬件。

Service Fabric 提供了五个现成的不同日志通道，可以生成以下事件：

* 操作通道：由 Service Fabric 和群集执行的高级操作，包括出现节点事件、部署新应用程序或 SF 升级回滚等。
* 客户信息渠道：运行状况报告和负载均衡决策
* [Reliable Services 事件](service-fabric-reliable-services-diagnostics.md)：特定于编程模型的事件
* [Reliable Actors 事件](service-fabric-reliable-actors-diagnostics.md)：特定于编程模型事件和性能计数器
* 支持日志：Service Fabric 生成的系统日志，仅当我们提供支持时使用

这些不同的通道涵盖了大部分推荐的平台级别日志记录。 若要改进平台级别日志记录，建议更好地了解运行状况模型和添加自定义运行状况报表，并添加自定义性能计数器，以实时了解服务和应用程序对群集的影响。

### <a name="azure-service-fabric-health-and-load-reporting"></a>Azure Service Fabric 运行状况和负载报告

Service Fabric 具有自身的运行状况模型，以下文章对此做了详细介绍：
- [Service Fabric 运行状况监视简介](service-fabric-health-introduction.md)
- [报告并检查服务运行状况](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [添加自定义 Service Fabric 运行状况报告](service-fabric-report-health.md)
- [查看 Service Fabric 运行状况报告](service-fabric-view-entities-aggregated-health.md)

运行状况监视对于运行服务的多个方面至关重要。 当 Service Fabric 执行命名应用程序升级时，运行状况监视尤为重要。 服务的每个升级域都已升级并且提供给客户使用后，升级域必须先通过运行状况检查，然后部署才能转到下一个升级域。 如果无法实现良好的运行状况，部署将会回滚，使应用程序保持一种已知正常的状态。 尽管在回滚服务之前某些客户可能会受到影响，但大多数客户不会遇到问题。 此外，问题的解决速度相对较快，无需等待操作员的人工操作。 在代码中合并的运行状况检查越多，服务应对部署问题的弹性就越高。

服务运行状况的另一个方面是从服务报告指标。 指标在 Service Fabric 中非常重要，因为它们用于均衡资源使用量。 指标还可用作系统运行状况的指示器。 例如，假设某个应用程序包含许多服务，每个实例报告每秒请求数 (RPS) 指标。 如果一个服务使用的资源比另一个服务要多，Service Fabric 会围绕群集移动服务实例，尽量使资源利用率保持均衡。 有关资源利用的工作原理的详细说明，请参阅 [Manage resource consumption and load in Service Fabric with metrics](service-fabric-cluster-resource-manager-metrics.md)（在 Service Fabric 中使用指标管理资源消耗和负载）。

使用指标还能洞察服务的执行情况。 在不同的时间，都可以使用指标来检查服务是否根据所需的参数运行。 例如，如果趋势表明星期一上午 9 点的平均 RPS 为 1,000，则可以设置一份运行状况报告，在 RPS 低于 500 或高于 1,500 时发出警报。 尽管一切看上去可能正常，但还是值得执行一番检查，确保客户获得优越的体验。 服务可以定义一组可在执行运行状况检查时报告的指标，同时避免对群集的资源均衡造成影响。 为此，可将指标权重设置为零。 建议一开始为所有指标使用零权重，只有在确定指标加权会对群集的资源均衡产生何种影响时，才增大权重。

> [!TIP]
> 不要使用过多的加权指标， 否则可能难以了解服务实例为何会出于均衡目的而被移动， 并且某些指标可能会持续很长时间！

可以指明应用程序的运行状况和性能的任何信息都是指标和运行状况报告的候选项。 CPU 性能计数器可以告知节点的利用情况，但不会指明特定的服务是否正常，因为单个节点上可能运行了多个服务。 但是，RPS、已处理的项数和请求延迟等指标都可以指明特定服务的运行状况。

若要报告运行状况，请使用如下所示的代码：

  ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
  ```

若要报告指标，请使用如下所示的代码：

  ```csharp
    this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
  ```

### <a name="service-fabric-support-logs"></a>Service Fabric 支持日志

如需联系 Microsoft 支持部门来获取 Azure Service Fabric 群集方面的帮助，几乎始终都需要提供支持日志。 如果群集托管在 Azure 中，则会自动配置支持日志，并在创建群集的过程中收集这些日志。 日志存储在群集资源组中的专用存储帐户内。 该存储帐户没有固定的名称，但在其中可以看到以 *fabric* 开头的 Blob 容器和表。 有关为独立群集设置日志收集的信息，请参阅 [Create and manage a standalone Azure Service Fabric cluster](service-fabric-cluster-creation-for-windows-server.md)（创建和管理独立 Azure Service Fabric 群集）以及 [Configuration settings for a standalone Windows cluster](service-fabric-cluster-manifest.md)（Windows 独立群集的配置设置）。 对于独立的 Service Fabric 实例，应该将日志发送到本地文件共享。 **必须**提供这些日志才能获得支持，但是，这些日志只能由 Microsoft 客户支持团队使用。

## <a name="enabling-diagnostics-for-a-cluster"></a>启用群集诊断

为了充分利用这些日志，强烈建议在群集创建过程中启用“诊断”。 如果开启诊断，部署群集时，Microsoft Azure 诊断就可确认运行 Operational、Reliable Services 和 Reliable Actors 通道，并按照此处所述存储数据。

如上图所示，还存在一个用于添加 Application Insights (AppInsights) 检测密钥的可选字段。 如果选择使用 AppInsights 对所有事件进行分析（阅读此处的详细信息），请在此处添加 AppInsights resource instrumentationKey (GUID)。


如果要将容器部署到群集中，通过将此代码添加到“WadCfg > DiagnosticMonitorConfiguration”，启用 WAD 来读取 docker 统计信息：

```json
"DockerSources": {
    "Stats": {
        "enabled": true,
        "sampleRate": "PT1M"
    }
},

```

## <a name="measuring-performance"></a>测量性能

测量群集性能有助于了解它如何处理负载以及如何做出关于缩放群集的决策（请参阅有关[在 Azure 上](service-fabric-cluster-scale-up-down.md)或[在本地](service-fabric-cluster-windows-server-add-remove-nodes.md)缩放群集的详细信息）。 将来分析日志时，性能数据还可用于比较你或你的应用程序和服务可能执行的操作。 

有关使用 Service Fabric 时要收集的性能计数器的列表，请参阅 [Service Fabric 中的性能计数器](service-fabric-diagnostics-event-generation-perf.md)

以下是设置群集收集性能数据的两种常见方式：

* 使用代理：这是从计算机中收集性能的首选方法，因为代理通常有可以收集的可能性能指标列表，并且选择要收集或更改的指标是一个相对简单的过程。 阅读有关[如何配置适用于 Service Fabric 的 OMS](service-fabric-diagnostics-event-analysis-oms.md) 和[设置 OMS Windows 代理](../log-analytics/log-analytics-windows-agents.md)的文章，了解有关 OMS 代理的更多信息，OMS 代理是一个能够读取群集 VM 的性能数据和部署容器的监视代理。

* 配置诊断以将性能计数器写入表中：对于 Azure 上的群集，这意味着更改 Azure 诊断配置以从群就中的 VM 读取适当的性能计数器，如果要部署任何容器，也能使其读取 docker 统计数据。 阅读有关在 Service Fabric 中配置[WAD 中的性能计数器](service-fabric-diagnostics-event-aggregation-wad.md)，设置性能计数器集合。

## <a name="next-steps"></a>后续步骤

需要将日志和事件聚合后，才能将其发送到任何分析平台。 阅读有关 [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) 和 [WAD](service-fabric-diagnostics-event-aggregation-wad.md) 的信息，更好地了解一些推荐选项。

