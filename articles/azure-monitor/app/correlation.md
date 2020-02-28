---
title: Azure Application Insights 遥测关联 | Microsoft Docs
description: Application Insights 遥测关联
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 06897fffda490cdfcbb2a9cf6f55c7945e8afda0
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672049"
---
# <a name="telemetry-correlation-in-application-insights"></a>Application Insights 中的遥测关联

在微服务的世界中，每次逻辑操作都需要在服务的不同组件中完成工作。 您可以使用[Application Insights](../../azure-monitor/app/app-insights-overview.md)分别监视其中的每个组件。 Application Insights 支持分布式遥测关联，可用来检测哪个组件要对故障或性能下降问题负责。

本文介绍了 Application Insights 用于关联由多个组件发送的遥测的数据模型。 其中阐述了 context-propagation 技术和协议， 还介绍了如何实现不同语言和平台上的相关策略。

## <a name="data-model-for-telemetry-correlation"></a>遥测关联的数据模型

Application Insights 定义了用于分配遥测关联的[数据模型](../../azure-monitor/app/data-model.md)。 若要将遥测与逻辑操作相关联，每个遥测项都有一个名为 `operation_Id`的上下文字段。 此标识符由分布式跟踪中的每个遥测项共享。 因此，即使丢失单个层的遥测数据，仍可以关联其他组件报告的遥测数据。

分布式逻辑操作通常包含一组较小的操作，这些操作是由某个组件处理的请求。 这些操作由[请求遥测](../../azure-monitor/app/data-model-request-telemetry.md)定义。 每个请求遥测项都有自己的 `id`，可在全局范围内对其进行标识。 与请求关联的所有遥测项（如跟踪和异常）应将 `operation_parentId` 设置为 `id`的请求的值。

每个传出操作（例如，对另一个组件的 HTTP 调用）是由[依赖项遥测](../../azure-monitor/app/data-model-dependency-telemetry.md)表示的。 依赖项遥测还定义其自己的 `id`，这是全局唯一的。 此依赖项调用发起的请求遥测将此 `id` 用作其 `operation_parentId`。

可以结合 `operation_Id` 使用 `operation_parentId`、`request.id` 和 `dependency.id`，生成分布式逻辑操作的视图。 这些字段还定义了遥测调用的因果关系顺序。

在微服务环境中，来自组件的跟踪可能会进入不同的存储项。 每个组件可能在 Application Insights 中具有其自身的检测密钥。 若要获取逻辑操作的遥测数据，请 Application Insights 查询每个存储项的数据。 当存储项的数量很大时，你需要在下一步查找的位置提示。 Application Insights 数据模型定义了以下两个字段来解决此问题：`request.source` 和 `dependency.target`。 第一个字段标识启动依赖关系请求的组件。 第二个字段标识哪个组件返回依赖项调用的响应。

## <a name="example"></a>示例

接下来举例说明。 名为 "股票价格" 的应用程序通过使用名为 "股票" 的外部 API 显示股票的当前市场价格。 股票价格应用程序有一个名为 "股票" 的页面，客户端 web 浏览器使用 `GET /Home/Stock`打开该页面。 应用程序使用 HTTP 调用 `GET /api/stock/value`来查询 Stock API。

可以运行一个查询来分析生成的遥测数据：

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

在结果中可以看到，所有遥测项共享根 `operation_Id`。 从页面进行 Ajax 调用时，会将一个新的唯一 ID （`qJSXU`）分配给依赖项遥测，并使用 pageView 的 ID 作为 `operation_ParentId`。 接着，服务器请求将 Ajax ID 用作 `operation_ParentId`。

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock page                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| 请求    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

对外部服务发出 `GET /api/stock/value` 调用时，您需要知道该服务器的标识，以便您可以相应地设置 `dependency.target` 字段。 如果外部服务不支持监视，则会将 `target` 设置为服务的主机名（例如 `stock-prices-api.com`）。 但是，如果服务通过返回预定义的 HTTP 标头来标识自身，`target` 包含允许 Application Insights 通过从该服务查询遥测数据来生成分布式跟踪的服务标识。

## <a name="correlation-headers"></a>关联标头

Application Insights 正在转换为[W3C 跟踪上下文](https://w3c.github.io/trace-context/)，该上下文定义：

- `traceparent`：携带调用的全局唯一操作 ID 和唯一标识符。
- `tracestate`：携带系统特定的跟踪上下文。

最新版本的 Application Insights SDK 支持跟踪上下文协议，但你可能需要选择加入它。 （将保留与 Application Insights SDK 支持的以前的相关协议的向后兼容性。）

[相关 HTTP 协议（也称为请求 Id](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)）将被弃用。 此协议定义了两个标头：

- `Request-Id`：携带调用的全局唯一 ID。
- `Correlation-Context`：携带分布式跟踪属性的名称-值对集合。

Application Insights 还定义了相关 HTTP 协议的[扩展](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md)。 它使用 `Request-Context` 名称值对来传播直接调用方或被调用方使用的属性集合。 Application Insights SDK 使用此标头来设置 `dependency.target` 和 `request.source` 字段。

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>启用对经典 ASP.NET 应用的 W3C 分布式跟踪支持
 
  > [!NOTE]
  >  从 `Microsoft.ApplicationInsights.Web` 和 `Microsoft.ApplicationInsights.DependencyCollector`开始，不需要进行任何配置。

W3C 跟踪上下文支持将以向后兼容的方式实现。 相关性应该适用于使用早期版本的 SDK 进行检测的应用程序（无 W3C 支持）。

如果要继续使用旧的 `Request-Id` 协议，则可以使用此配置禁用跟踪上下文：

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

如果你运行的是较旧版本的 SDK，我们建议你对其进行更新或应用以下配置，以启用跟踪上下文。
此功能在 `Microsoft.ApplicationInsights.Web` 和 `Microsoft.ApplicationInsights.DependencyCollector` 包中提供，自 beta1 版开始。
此项默认禁用。 若要启用它，请对 `ApplicationInsights.config`进行以下更改：

- 在 "`RequestTrackingTelemetryModule`" 下，添加 `EnableW3CHeadersExtraction` 元素，并将其值设置为 "`true`"。
- 在 "`DependencyTrackingTelemetryModule`" 下，添加 `EnableW3CHeadersInjection` 元素，并将其值设置为 "`true`"。
- 将 `W3COperationCorrelationTelemetryInitializer` 添加到 `TelemetryInitializers`下。 它将类似于以下示例：

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>启用对 ASP.NET Core 应用的 W3C 分布式跟踪支持

 > [!NOTE]
  > 从 `Microsoft.ApplicationInsights.AspNetCore` 版本2.8.0 开始，不需要进行任何配置。
 
W3C 跟踪上下文支持将以向后兼容的方式实现。 相关性应该适用于使用早期版本的 SDK 进行检测的应用程序（无 W3C 支持）。

如果要继续使用旧的 `Request-Id` 协议，则可以使用此配置禁用跟踪上下文：

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

如果你运行的是较旧版本的 SDK，我们建议你对其进行更新或应用以下配置，以启用跟踪上下文。

此功能在 `Microsoft.ApplicationInsights.AspNetCore` 的版本 2.5.0-beta1 以及 `Microsoft.ApplicationInsights.DependencyCollector` 的版本 2.8.0-beta1 中提供。
此项默认禁用。 若要启用该项，请将 `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` 设置为 `true`：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>启用对 Java 应用的 W3C 分布式跟踪支持

- **传入配置**

  - 对于 Java EE 应用，请将以下内容添加到 Applicationinsights.config 中的 `<TelemetryModules>` 标记中：

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - 对于春季 Boot 应用，请添加以下属性：

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **传出配置**

  将以下代码添加到 AI-Agent.xml：

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > 默认情况下启用后向兼容性模式，并且 `enableW3CBackCompat` 参数是可选的， 仅在要将后向兼容性关闭时使用。
  >
  > 理想情况下，当所有服务都已更新为支持 W3C 协议的较新版 SDK 时，应将该功能关闭。 强烈建议你尽快迁移到这些更新的 SDK。

> [!IMPORTANT]
> 请确保传入和传出配置完全相同。

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>为 Web 应用启用 W3C 分布式跟踪支持

此功能 `Microsoft.ApplicationInsights.JavaScript`。 此项默认禁用。 若要启用它，请使用 `distributedTracingMode` config。提供 AI_AND_W3C 是为了向后兼容由 Application Insights 检测到的任何旧版服务。

- **npm 安装程序（如果使用代码段设置，则忽略）**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **代码段设置（如果使用 npm 安装程序则忽略）**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...other configuration options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing 和 Application Insights

[OpenTracing 数据模型规范](https://opentracing.io/)和 Application Insights 数据模型按以下方式映射：

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`、`PageView`                 | 带 `Span` 的 `span.kind = server`                  |
| `Dependency`                          | 带 `Span` 的 `span.kind = client`                  |
| `Id` 和 `Request` 的 `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` 类型的 `ChildOf`（父级范围）   |

有关详细信息，请参阅[Application Insights 遥测数据模型](../../azure-monitor/app/data-model.md)。

有关 OpenTracing 概念的定义，请参阅 OpenTracing[规范](https://github.com/opentracing/specification/blob/master/specification.md)和[语义约定](https://github.com/opentracing/specification/blob/master/semantic_conventions.md)。

## <a name="telemetry-correlation-in-opencensus-python"></a>OpenCensus Python 中的遥测关联

OpenCensus Python 遵循前面概述的 `OpenTracing` 数据模型规范。 它还支持[W3C 跟踪上下文](https://w3c.github.io/trace-context/)，无需任何配置。

### <a name="incoming-request-correlation"></a>传入请求相关

OpenCensus Python 将 W3C 跟踪上下文标头从传入请求关联到从请求自身生成的范围。 OpenCensus 将通过以下常见的 web 应用程序框架集成来自动执行此操作： Flask、Django 和棱锥。 只需用[正确的格式](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)填充 W3C 跟踪上下文标题，并随请求发送它们。 下面是一个示例 Flask 应用程序，该应用程序演示了这一点：

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

此代码在本地计算机上运行示例 Flask 应用程序，侦听端口 `8080`。 若要关联跟踪上下文，请将请求发送到终结点。 在此示例中，可以使用 `curl` 命令：
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
通过查看[跟踪上下文标题格式](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)，可以派生以下信息：

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

如果查看发送到 Azure Monitor 的请求条目，则可以看到用跟踪标头信息填充的字段。 你可以在 "日志（Analytics）" 下的 "Azure Monitor Application Insights 资源" 中找到此数据。

![请求日志中的遥测数据（Analytics）](./media/opencensus-python/0011-correlation.png)

`id` 字段采用 `<trace-id>.<span-id>`格式，其中 `trace-id` 是从请求传入的跟踪标头获取的，而 `span-id` 是此范围生成的8字节数组。

`operation_ParentId` 字段采用 `<trace-id>.<parent-id>`格式，其中 `trace-id` 和 `parent-id` 都取自请求中传递的跟踪标头。

### <a name="log-correlation"></a>日志关联

OpenCensus Python 使你可以通过将跟踪 ID、跨度 ID 和采样标志添加到日志记录来关联日志。 可以通过安装 OpenCensus[日志记录集成](https://pypi.org/project/opencensus-ext-logging/)来添加这些属性。 以下属性将添加到 Python `LogRecord` 对象： `traceId`、`spanId`和 `traceSampled`。 请注意，这仅对在集成后创建的记录器有效。

下面是演示此内容的示例应用程序：

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
此代码运行时，控制台中将显示以下输出：
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
请注意，在范围内的日志消息中存在 `spanId`。 此 `spanId` 属于名为 `hello`的跨度。

您可以使用 `AzureLogHandler`导出日志数据。 有关详细信息，请参阅[此文章](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs)。

## <a name="telemetry-correlation-in-net"></a>.NET 中的遥测关联

随着时间的推移，.NET 定义了多种方法来关联遥测和诊断日志：

- `System.Diagnostics.CorrelationManager` 允许跟踪[LogicalOperationStack 和 ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx)。
- `System.Diagnostics.Tracing.EventSource` 和 Windows 事件跟踪 (ETW) 定义了 [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) 方法。
- `ILogger` 使用[日志范围](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes)。
- Windows Communication Foundation (WCF) 和 HTTP 将“当前”上下文传播关联到一起。

但这些方法未启用自动分布式跟踪支持。 `DiagnosticSource` 支持自动跨计算机关联。 .NET 库支持 `DiagnosticSource`，并允许通过传输（如 HTTP）自动跨计算机传播相关上下文。

`DiagnosticSource` 中的[活动用户指南](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)介绍了跟踪活动的基本知识。

ASP.NET Core 2.0 支持提取 HTTP 标头和启动新活动。

从版本4.1.0 开始，`System.Net.Http.HttpClient`支持自动注入相关 HTTP 标头，并将 HTTP 调用跟踪为活动。

对于经典 ASP.NET，有一个新的 HTTP 模块[TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/)。 此模块使用 `DiagnosticSource` 实现遥测关联。 它会基于传入的请求标头启动活动。 即使 Internet Information Services （IIS）处理的每个阶段都在不同的托管线程上运行，它也会将遥测与请求处理的不同阶段相关联。

从版本 2.4.0-beta1 开始，Application Insights SDK 使用 `DiagnosticSource` 和 `Activity` 收集遥测数据并将其与当前活动相关联。

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Java SDK 中的遥测关联

[APPLICATION INSIGHTS SDK For Java](../../azure-monitor/app/java-get-started.md)版本2.0.0 或更高版本支持遥测的自动关联。 它会自动填充请求范围内发出的所有遥测（如跟踪、异常和自定义事件） `operation_id`。 如果配置了[JAVA SDK 代理](../../azure-monitor/app/java-agent.md)，它还会通过 HTTP 为服务到服务调用传播相关标头（前面所述）。

> [!NOTE]
> 相关功能仅支持通过 Apache HttpClient 进行的调用。 春季 RestTemplate 和 Feign 都可以在幕后使用 Apache HttpClient。

目前不支持跨消息传送技术（例如 Kafka、RabbitMQ 和 Azure 服务总线）的自动上下文传播。 通过使用 `trackDependency` 和 `trackRequest` 方法，可以手动编写此类方案。 在这些方法中，依赖项遥测表示由制造者排队的消息。 请求表示使用者正在处理的消息。 在这种情况下，`operation_id` 和 `operation_parentId` 都应在消息的属性中传播。

### <a name="telemetry-correlation-in-asynchronous-java-applications"></a>异步 Java 应用程序中的遥测关联

若要了解如何关联异步弹簧启动应用程序中的遥测，请参阅[异步 Java 应用程序中的分布式跟踪](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications)。 本文提供了有关检测弹簧的[ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html)和[ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html)的指导。


<a name="java-role-name"></a>
## <a name="role-name"></a>角色名称

你可能想要自定义组件名称在[应用程序映射](../../azure-monitor/app/app-map.md)中的显示方式。 为此，可以执行以下操作之一，手动设置 `cloud_RoleName`：

- 使用 Application Insights Java SDK 2.5.0 和更高版本时，可以通过将 `<RoleName>` 添加到 Applicationinsights.config 文件来指定 `cloud_RoleName`：

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- 如果将弹簧 Boot 用于 Application Insights 弹簧 Boot Starter，只需在应用程序的 "属性" 文件中设置应用程序的自定义名称：

  `spring.application.name=<name-of-app>`

  弹簧 Boot Starter 会自动将 `cloudRoleName` 分配给为 `spring.application.name` 属性输入的值。

## <a name="next-steps"></a>后续步骤

- 编写[自定义遥测](../../azure-monitor/app/api-custom-events-metrics.md)。
- 有关 ASP.NET Core 和 ASP.NET 中的高级关联方案，请参阅[跟踪自定义操作](custom-operations-tracking.md)。
- 详细了解如何为其他 SDK [设置 cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name)。
- 在 Application Insights 中载入微服务的所有组件。 查看[支持的平台](../../azure-monitor/app/platforms.md)。
- 有关 Application Insights 的类型，请参阅[数据模型](../../azure-monitor/app/data-model.md)。
- 了解如何[扩展和筛选遥测](../../azure-monitor/app/api-filtering-sampling.md)。
- 参阅 [Application Insights 配置参考](configuration-with-applicationinsights-config.md)。
