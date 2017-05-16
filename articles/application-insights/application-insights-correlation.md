---
title: "Azure Application Insights 遥测关联 | Microsoft 文档"
description: "Application Insights 遥测关联"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: c48dc5cb5dd6dfa09ff9718e78f8d560886851be
ms.contentlocale: zh-cn
ms.lasthandoff: 04/27/2017


---
# <a name="telemetry-correlation-in-application-insights"></a>Application Insights 中的遥测关联

在微服务的世界中，每次逻辑操作都需要在服务的不同组件中完成工作。 这些组件都可由 [Application Insights](app-insights-overview.md) 单独监视。 Web 应用组件与身份验证提供程序组件通信以验证用户凭据，并与 API 组件通信以获取要可视化的数据。 而 API 组件可从其他服务查询数据，还可使用缓存提供程序组件，并通知有关此调用的计费组件。 Application Insights 支持分布式遥测关联。 它允许检测哪个组件要对故障或性能下降问题负责。

本文介绍了 Application Insights 用于关联由多个组件发送的遥测的数据模型。 其中阐述了上下文传播技术和协议， 以及如何在不同的语言和平台上实现相关的概念。

## <a name="telemetry-correlation-data-model"></a>遥测关联数据模型

Application Insights 定了义用于分配遥测关联的[数据模型](application-insights-data-model.md)。 若要将遥测与逻辑操作关联，每个遥测项都应包含名为 `operation_Id` 的上下文字段。 此标识符由分布式跟踪中的每个遥测项共享。 因此，即使单个层失去了遥测功能，也仍可关联其他组件报告的遥测。

分布式逻辑操作通常由一系列小规模操作（某个组件处理的请求）构成。 这些操作由[请求遥测](application-insights-data-model-request-telemetry.md)定义。 每个请求遥测具有自身的 `id`，用于对自身进行唯一全局标识。 与此请求关联的所有遥测（跟踪、异常等）应将 `operation_parentId` 设置为请求 `id` 的值。

每个传出操作（例如，对另一个组件的 http 调用）由[依赖项遥测](application-insights-data-model-dependency-telemetry.md)表示。 依赖项遥测也定义了自身的全局唯一的 `id`。 此依赖项调用发起的请求遥测将此 ID 用作 `operation_parentId`。

可以结合 `dependency.id` 使用 `operation_Id`、`operation_parentId` 和 `request.id` 生成分布式逻辑操作的视图。 这些字段还定义了遥测调用的因果关系顺序。

在微服务环境中，来自组件的跟踪可能会进入不同的存储。 每个组件可能在 Application Insights 中具有其自身的检测密钥。 若要获取逻辑操作的遥测数据，需要查询每个存储中的数据。 如果存储数目极大，你需要提示后续查找位置。

Application Insights 数据模型定义了以下两个字段来解决此问题：`request.source` 和 `dependency.target`。 第一个字段定义发起依赖项请求的组件，第二个字段定义哪个组件返回依赖项调用的响应。


## <a name="example"></a>示例

我们以 STOCKS API 应用程序为例。该应用程序使用名为 STOCKS API 的外部 API 显示某只股票的当前市价。 STOCK PRICES 应用程序具有由客户端 Web 浏览器使用 `GET /Home/Stock` 打开的 `Stock page` 页。 应用程序通过使用 HTTP 调用 `GET /api/stock/value` 查询 STOCK API。

可以运行一个查询来分析生成的遥测数据：

```
(requests | union dependencies | union pageViews) 
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

在结果视图中可以看到，所有遥测项共享根 `operation_Id`。 从该页面发出 ajax 调用后，会将新的唯一 ID `qJSXU` 分配给依赖项遥测，并将 pageView 的 ID 用作 `operation_ParentId`。 接着，服务器请求将 ajax 的 ID 用作 `operation_ParentId`，等等。

| itemType   | name                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock 页面                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| 请求    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

现在，在对外部服务发出 `GET /api/stock/value` 调用时，你希望知道该服务器的标识。 因此，可以相应地设置 `dependency.target` 字段。 如果外部服务不支持监视 - `target` 将设置为服务的主机名，例如 `stock-prices-api.com`。 但是，如果该服务通过返回预定义的 HTTP 标头来标识自身 - `target` 将包含服务标识，使 Application Insights 能够通过查询该服务中的遥测数据来生成分布式跟踪。 

## <a name="correlation-headers"></a>关联标头

我们正在开发[关联 HTTP 协议](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v1.md)的 RFC 提案。 此提案定义两个标头：

- `Request-Id` 承载调用的全局唯一 ID
- `Correlation-Context` 承载分布式跟踪属性的名称值对集合

该标准还定义了 `Request-Id` 生成项的两个架构：平面和分层。 使用平面架构时，将为 `Correlation-Context` 集合定义一个已知的 `Id` 键。

Application Insights 为关联 HTTP 协议定义了[扩展](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md)。 它使用 `Request-Context` 名称值对来传播直接调用方或被调用方使用的属性集合。 Application Insights SDK 使用此标头设置 `dependency.target` 和 `request.source` 字段。

## <a name="open-tracing-and-application-insights"></a>开放跟踪和 Application Insights

[开放跟踪](http://opentracing.io/) 和 Application Insights 数据模型如下所示 

- `request`、`pageView` 映射到采用 `span.kind = server` 设置的 **Span**
- `dependency` 映射到采用 `span.kind = client` 设置的 **Span**
- `request` 和 `dependency` 的 `id` 映射到 **Span.Id**
- `operation_Id` 映射到 **TraceId**
- `operation_ParentId` 映射到 `ChileOf` 类型的 **Reference**

有关 Application Insights 的类型和数据模型，请参阅[数据模型](application-insights-data-model.md)。

有关开放跟踪概念的定义，请参阅[规范](https://github.com/opentracing/specification/blob/master/specification.md)和 [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md)。


## <a name="telemetry-correlation-in-net"></a>.NET 中的遥测关联

.NET 至今已定义了多种方式用于关联遥测和诊断日志。 它提供了用于跟踪 [LogicalOperationStack 和 ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx) 的 `System.Diagnostics.CorrelationManager`。 `System.Diagnostics.Tracing.EventSource` 和 Windows ETW 定义了方法 [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx)。 `ILogger` 使用[日志范围](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes)。 WCF 和 Http 将“当前”上下文传播关联到一起。

但是，这些方法并未实现自动分布式跟踪支持。 `DiagnosticsSource` 是支持跨计算机自动关联的一种方式。 .NET 库支持诊断源，并允许通过 http 等传输方法自动跨计算机传播关联上下文。

诊断源中的[指南活动](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)解释了跟踪活动的基础知识。 

ASP.NET Core 2.0 支持提取 Http 标头和启动新的活动。 

从版本 `<fill in>` 开始，`System.Net.HttpClient` 支持自动注入关联 Http 标头和以活动形式跟踪 http 调用。

ASP.NET Classic 有一个新的 Http 模块 [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/)。 此模块使用 DiagnosticsSource 实现遥测关联。 它会基于传入的请求标头启动活动。 它还会关联不同请求处理阶段的遥测， 即使每个 IIS 处理阶段在不同的管理线程上运行，它也能做到这一点。

从版本 `2.4.0-beta1` 开始，Application Insights SDK 使用 DiagnosticsSource 和 Activity 收集遥测数据并将其与当前活动相关联。 

## <a name="next-steps"></a>后续步骤

- [编写自定义遥测](app-insights-api-custom-events-metrics.md)
- 在 Application Insights 中载入微服务的所有组件。 查看[支持的平台](app-insights-platforms.md)。
- 有关 Application Insights 的类型和数据模型，请参阅[数据模型](application-insights-data-model.md)。
- 了解如何[扩展和筛选遥测](app-insights-api-filtering-sampling.md)。

