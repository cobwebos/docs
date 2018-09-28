---
title: Azure Application Insights 中的分布式跟踪 | Microsoft Docs
description: 介绍 Microsoft 如何在 OpenCensus 项目中通过本地转发器和合作伙伴关系提供对分布式跟踪的支持
services: application-insights
keywords: ''
author: nikmd23
ms.author: nimolnar
ms.reviewer: mbullwin
ms.date: 09/17/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 47ea4fadba50fdbbd6d83531bd4eb40fc581d2ad
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46950193"
---
# <a name="what-is-distributed-tracing"></a>什么是分布式跟踪？

现代云和微服务体系结构的出现宣告了一种简单且可独立部署的服务的诞生，它可以提高可用性和吞吐量，同时还能降低成本。 但是，虽然这些转变使那些单个的服务作为整体更易于理解，但也使整个系统更加难以推断和调试。

在整体式体系结构中，我们已习惯于通过调用堆栈进行调试。 调用堆栈是很好的工具，可以显示执行流（方法 A 调用了方法 B，方法 B 调用了方法 C），并可显示每个这样的调用的详细信息和参数。 这适用于在单个进程上运行的庞大单体结构或服务。但是，如果调用跨进程边界，而不仅仅是本地堆栈上的某个引用，我们该如何调试？ 

这时候就需要使用分布式跟踪。  

分布式跟踪就是适合现代云和微服务体系结构的调用堆栈，但是添加了简单的性能探查器。 在 Azure Monitor 中，我们提供的两种体验适合使用分布式跟踪数据。 第一种是[事务诊断](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics)视图，这相当于一个增加了时间维度的调用堆栈。 事务诊断视图适用于查看单个事务/请求，并且可以按照单个请求来查找可靠性问题和性能瓶颈的根本原因。

Azure Monitor 还提供[应用程序映射](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)视图，该视图聚合了许多事务，可以通过拓扑视图的方式来显示系统交互情况，以及平均性能和错误率。 

## <a name="how-to-enable-distributed-tracing"></a>如何启用分布式跟踪

在应用程序中跨服务启用分布式跟踪很简单，只需根据实现服务时采用的语言为每个服务添加适当的 SDK 或库即可。

## <a name="enabling-via-application-insights-sdks"></a>通过 Application Insights SDK 启用

适用于 .NET、.NET Core、Java、Node.js 和 JavaScript 的 Application Insights SDK 都以原生方式支持分布式跟踪。 每个 Application Insights SDK 的安装和配置说明见下：

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)

安装并配置适当的 Application Insights SDK 以后，系统就会通过 SDK 依赖项自动收集器自动收集常用框架、库和技术的跟踪信息。 [依赖项自动收集文档](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies)中提供支持的技术的完整列表。

 另外，任何技术都可以通过在 [TeleletryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) 上调用 [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) 手动进行跟踪。

## <a name="enable-via-opencensus"></a>通过 OpenCensus 启用

除了 Application Insights SDK，Application Insights 还可以通过 [OpenCensus](https://opencensus.io/) 来支持分布式跟踪。 OpenCensus 是库的单发行版，开源且不局限于供应商，可以针对服务进行指标收集和分布式跟踪。 它还允许开源社区针对 Redis、Memcached 或 MongoDB 之类的常用技术启用分布式跟踪。 [Microsoft 与多个其他的监视项目和云项目合作伙伴进行 OpenCensus 协作](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/)。

若要通过 OpenCensus 向应用程序添加分布式跟踪功能，请先[安装并配置 Application Insights 本地转发器](./opencensus-local-forwarder.md)。 然后对 OpenCensus 进行配置，以便通过本地转发器路由分布式跟踪数据。 [Python](./opencensus-python.md) 和 [Go](./opencensus-go.md) 均受支持。

OpenCensus 网站保留了 [Python](https://opencensus.io/api/python/trace/usage.html) 和 [Go](https://godoc.org/go.opencensus.io) 的 API 参考文档，此外还有各种不同的 OpenCensus 使用指南。 

## <a name="next-steps"></a>后续步骤

* [OpenCensus Python 使用指南](https://opencensus.io/api/python/trace/usage.html)
* [应用程序映射](./app-insights-app-map.md)
* [端到端性能监视](./app-insights-tutorial-performance.md)