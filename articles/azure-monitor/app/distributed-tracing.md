---
title: Azure Application Insights 中的分布式跟踪 | Microsoft Docs
description: 提供有关 Microsoft 通过我们的合作关系在 OpenCensus 项目中的分布式跟踪支持的信息
ms.topic: conceptual
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 9eb753981821a6b53d81b1d03e65abf68e0064dc
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539986"
---
# <a name="what-is-distributed-tracing"></a>什么是分布式跟踪？

现代云和[微服务](https://azure.com/microservices)体系结构的出现宣告了一种简单且可独立部署的服务的诞生，它可以提高可用性和吞吐量，同时还能降低成本。 但是，虽然这些转变使那些单个服务作为整体更易于理解，但也使整个系统更加难以推断和调试。

在整体体系结构中，我们已习惯于通过调用堆栈进行调试。 调用堆栈是很好的工具，可以显示执行流（方法 A 调用了方法 B，方法 B 调用了方法 C），并可显示每个这样的调用的详细信息和参数。 这适用于在单个进程上运行的庞大单体结构或服务。但是，如果调用跨进程边界，而不仅仅是本地堆栈上的某个引用，我们该如何调试？ 

这时候就需要使用分布式跟踪。  

分布式跟踪就是适合现代云和微服务体系结构的调用堆栈，但是添加了简单的性能探查器。 在 Azure Monitor 中，我们提供的两种体验适合使用分布式跟踪数据。 第一种是[事务诊断](./transaction-diagnostics.md)视图，这相当于一个增加了时间维度的调用堆栈。 事务诊断视图适用于查看单个事务/请求，并且可以按照单个请求来查找可靠性问题和性能瓶颈的根本原因。

Azure Monitor 还提供[应用程序映射](./app-map.md)视图，该视图聚合了许多事务，可以通过拓扑视图的方式来显示系统交互情况，以及平均性能和错误率。 

## <a name="how-to-enable-distributed-tracing"></a>如何启用分布式跟踪

在应用程序中跨服务启用分布式跟踪很简单，只需根据实现服务时采用的语言为每个服务添加适当的代理、SDK 或库即可。

## <a name="enabling-via-application-insights-through-auto-instrumentation-or-sdks"></a>使用自动检测或 SDK 通过 Application Insights 启用

适用于 .NET、.NET Core、Java、Node.js 和 JavaScript 的 Application Insights 代理和/或 SDK 都以原生方式支持分布式跟踪。 每个 Application Insights SDK 的安装和配置说明见下：

* [.NET](../learn/quick-monitor-portal.md)
* [.NET Core](../learn/dotnetcore-quick-start.md)
* [Java](./java-in-process-agent.md)
* [Node.js](../learn/nodejs-quick-start.md)
* [JavaScript](./javascript.md)
* [Python](opencensus-python.md)

安装并配置适当的 Application Insights SDK 以后，系统就会通过 SDK 依赖项自动收集器自动收集常用框架、库和技术的跟踪信息。 [依赖项自动收集文档](./auto-collect-dependencies.md)中提供支持的技术的完整列表。

 另外，任何技术都可以通过在 [TelemetryClient](./api-custom-events-metrics.md) 上调用 [TrackDependency](./api-custom-events-metrics.md) 手动进行跟踪。

## <a name="enable-via-opencensus"></a>通过 OpenCensus 启用

除了 Application Insights SDK，Application Insights 还可以通过 [OpenCensus](https://opencensus.io/) 来支持分布式跟踪。 OpenCensus 是库的单发行版，开源且不局限于供应商，可以针对服务进行指标收集和分布式跟踪。 它还允许开源社区针对 Redis、Memcached 或 MongoDB 之类的常用技术启用分布式跟踪。 [Microsoft 与多个其他的监视项目和云项目合作伙伴进行 OpenCensus 协作](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/)。

[Python](opencensus-python.md) 

OpenCensus 网站保留了 [Python](https://opencensus.io/api/python/trace/usage.html) 和 [Go](https://godoc.org/go.opencensus.io) 的 API 参考文档，此外还有各种不同的 OpenCensus 使用指南。 

## <a name="next-steps"></a>后续步骤

* [OpenCensus Python 使用指南](https://opencensus.io/api/python/trace/usage.html)
* [应用程序映射](./../../azure-monitor/app/app-map.md)
* [端到端性能监视](./../../azure-monitor/learn/tutorial-performance.md)
