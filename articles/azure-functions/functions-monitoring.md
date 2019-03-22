---
title: 监视 Azure Functions
description: 了解如何使用 Azure Functions 使用 Azure Application Insights 监视函数执行。
services: functions
author: ggailey777
manager: jeconnoc
keywords: Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: glenga
ms.openlocfilehash: e9e47eff3df941b0c1437083dc7440fab4091418
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317062"
---
# <a name="monitor-azure-functions"></a>监视 Azure Functions

[Azure Functions](functions-overview.md)提供了与内置集成[Azure Application Insights](../azure-monitor/app/app-insights-overview.md)来监视函数。 本文介绍如何配置 Azure Functions 将由系统生成日志文件发送到 Application Insights。

![Application Insights 指标资源管理器](media/functions-monitoring/metrics-explorer.png)

Azure Functions 还具有[内置的监视不使用 Application Insights](#monitoring-without-application-insights)。 我们建议使用 Application Insights，因为它提供更多的数据和更好的方式来分析数据。

## <a name="application-insights-pricing-and-limits"></a>Application Insights 定价和限制

可以免费试用 Application Insights 与函数应用的集成。 没有每日限制可以免费处理多少数据。 可能会达到此限制在测试期间。 当你达到每日限制时，Azure 会提供门户和电子邮件通知。 如果您错过这些警报并达到的限制，新的日志不会显示在 Application Insights 查询中。 请注意以避免不必要的故障排除时间的限制。 有关详细信息，请参阅[在 Application Insights 中管理定价和数据量](../azure-monitor/app/pricing.md)。

## <a name="enable-application-insights-integration"></a>启用 Application Insights 集成

对于将数据发送到 Application Insights 的函数应用，它需要知道 Application Insights 资源的检测密钥。 该密钥必须位于名为 **APPINSIGHTS_INSTRUMENTATIONKEY** 的应用设置中。

可以在 [Azure 门户](https://portal.azure.com)中设置此连接：

* [自动连接新的 function app](#new-function-app)
* [手动连接 Application Insights 资源](#manually-connect-an-app-insights-resource)

### <a name="new-function-app"></a>新建函数应用
<!-- Add a transitional sentence to introduce the procedure. -->

1. 转到函数应用的“创建”页。

1. 将 Application Insights开关设置为“打开”。

1. 选择一个 Application Insights 位置。 选择靠近函数应用的区域和中的区域[Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)你想要将数据存储。

   ![在创建函数应用时启用 Application Insights](media/functions-monitoring/enable-ai-new-function-app.png)

1. 输入其他所需的信息，并选择“创建”。

下一步是[禁用内置日志记录](#disable-built-in-logging)。


<a id="manually-connect-an-app-insights-resource"></a>
### <a name="application-insights-resource"></a>Application Insights 资源 
<!-- Add a transitional sentence to introduce the procedure. -->

1. 创建 Application Insights 资源。 将应用程序类型设置为“常规”。

   ![创建常规类型的 Application Insights 资源](media/functions-monitoring/ai-general.png)

1. 从 Application Insights 资源的“概要”页复制检测密钥。 指向要获取的显示值的结束**单击此项可复制**按钮。

   ![复制 Application Insights 检测密钥](media/functions-monitoring/copy-ai-key.png)

1. 函数应用中**应用程序设置**页上，[添加应用设置](functions-how-to-use-azure-function-app-settings.md#settings)通过选择**添加新设置**。 将新设置命名**APPINSIGHTS_INSTRUMENTATIONKEY**并粘贴复制的检测密钥。

   ![将检测密钥添加到应用设置](media/functions-monitoring/add-ai-key.png)

1. 选择“保存”。

<!-- Before the next H2 heading, add transitional sentences to summarize why the procedures were necessary. -->

## <a name="disable-built-in-logging"></a>禁用内置日志记录

如果启用 Application Insights 时，会禁用[使用 Azure 存储的内置日志记录](#logging-to-storage)。 内置日志记录对于使用轻工作负荷测试非常有用，但并不是为了高负载生产环境中使用。 对于生产监视，我们建议使用 Application Insights。 如果在生产环境中使用内置日志记录，则日志记录记录可能不完整由于 Azure 存储限制。

若要禁用内置日志记录，请删除 `AzureWebJobsDashboard` 应用设置。 有关如何在 Azure 门户中删除应用设置的信息，请参阅[如何管理函数应用](functions-how-to-use-azure-function-app-settings.md#settings)的“应用程序设置”部分。 删除应用设置之前，请确保没有现有函数相同的函数应用中使用 Azure 存储触发器或绑定设置。

## <a name="view-telemetry-in-monitor-tab"></a>在“监视”选项卡中查看遥测数据

如前面各节中所示设置 Application Insights 集成后，可以查看遥测数据**监视器**选项卡。

1. 在函数应用页中，选择已配置 Application Insights 后，具有至少一次运行的函数。 然后选择**监视器**选项卡。

   ![选择“监视”选项卡](media/functions-monitoring/monitor-tab.png)

1. 选择**刷新**定期，直至函数调用的列表显示。

   它可以需要长达五分钟出现遥测客户端的数据传输到服务器进行批处理的列表。 (不适用于延迟[实时指标 Stream](../azure-monitor/app/live-stream.md)。 加载页面时，该服务会连接到 Functions 主机，因此，日志会直接流向页面。）

   ![调用列表](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. 若要查看特定函数调用的日志，选择该调用对应的“日期”列链接。

   ![调用详细信息链接](media/functions-monitoring/invocation-details-link-ai.png)

   该调用的日志记录输出显示在新页中。

   ![调用详细信息](media/functions-monitoring/invocation-details-ai.png)

这两个页面 （调用列表和调用详细信息） 链接到 Application Insights Analytics 查询可检索的数据：

![在 Application Insights 中运行](media/functions-monitoring/run-in-ai.png)

![Application Insights Analytics 调用列表](media/functions-monitoring/ai-analytics-invocation-list.png)

从这些查询，可以看到，该调用列表将限制到最后一个 30 天。 此列表显示不超过 20 个行 (`where timestamp > ago(30d) | take 20`)。 调用详细信息列表为过去 30 天没有限制。

有关详细信息，请参阅本文稍后的[查询遥测数据](#query-telemetry-data)。

## <a name="view-telemetry-in-application-insights"></a>在 Application Insights 中查看遥测

若要从 Azure 门户中的函数应用中打开 Application Insights，请转到 function app**概述**页。 下**配置功能**，选择**Application Insights**。

![从函数应用概述页中打开 Application Insights](media/functions-monitoring/ai-link.png)

有关如何使用 Application Insights 的信息，请参阅 [Application Insights 文档](https://docs.microsoft.com/azure/application-insights/)。 本部分介绍如何在 Application Insights 中查看数据的一些示例。 如果您已经熟悉 Application Insights，你可以直接转到[有关如何配置和自定义遥测数据的部分](#configure-categories-and-log-levels)。

在中[指标资源管理器](../azure-monitor/app/metrics-explorer.md)，可以创建图表和基于指标的警报。 度量值包括函数调用、 执行时间和成功率的数。

![指标资源管理器](media/functions-monitoring/metrics-explorer.png)

在[失败](../azure-monitor/app/asp-net-exceptions.md)选项卡上，可以基于函数失败和服务器异常来创建图表和警报。 操作名称是函数名称。 除非您实现依赖项中的失败不会显示[自定义遥测](#custom-telemetry-in-c-functions)依赖项。

![失败数](media/functions-monitoring/failures.png)

在[性能](../azure-monitor/app/performance-counters.md)选项卡上，可以分析性能问题。

![性能](media/functions-monitoring/performance.png)

“服务器”选项卡显示资源利用率和每个服务器的吞吐量。 在函数阻碍基础资源的调试方案下，此数据非常有用。 服务器被称为云角色实例。

![服务器](media/functions-monitoring/servers.png)

[实时指标 Stream](../azure-monitor/app/live-stream.md)选项卡将显示在真实时间中创建度量值数据。

![实时流](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>查询遥测数据

[Application Insights 分析](../azure-monitor/app/analytics.md)，可以访问数据库的表中的窗体中的所有遥测数据。 Analytics 提供了一种用于提取、处理和可视化数据的查询语言。

![选择 Analytics](media/functions-monitoring/select-analytics.png)

![Analytics 示例](media/functions-monitoring/analytics-traces.png)

下面是一个查询示例，它显示过去 30 分钟内每个辅助角色的请求的分布。

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

可用的表所示**架构**在左侧选项卡。 可以在下表中找到由函数调用生成的数据：

* **跟踪**:创建由运行时和函数代码的日志。
* **请求**:每个函数调用的一个请求。
* **异常**:由运行时引发的任何异常。
* **customMetrics**:成功和失败的调用、 成功率和持续时间的计数。
* **customEvents**:事件跟踪的运行时，例如：触发函数的 HTTP 请求。
* **performanceCounters**:有关性能的服务器上运行函数的信息。

其他表适用于可用性测试，以及客户端和浏览器遥测。 可以实现自定义遥测以向其中添加数据。

在每个表内，一些函数特定的数据位于 `customDimensions` 字段。  例如，以下查询检索所有具有日志级别 `Error` 的跟踪。

```
traces 
| where customDimensions.LogLevel == "Error"
```

运行时提供`customDimensions.LogLevel`和`customDimensions.Category`字段。 您可以在函数代码中编写的日志中的其他字段。 请参阅本文后面部分中的[结构化日志记录](#structured-logging)。

## <a name="configure-categories-and-log-levels"></a>配置类别和日志级别

无需进行任何自定义配置，可以使用 Application Insights。 默认配置可能会导致大量数据。 如果使用的是 Visual Studio Azure 订阅，可能会达到 Application Insights 的数据上限。 稍后在本文中，您将了解如何配置和自定义函数发送到 Application Insights 的数据。

### <a name="categories"></a>类别

对于每个日志，Azure Functions 记录器都包含一个类别。 类别指示运行时代码或函数代码的哪个部分编写日志。 

Functions 运行时创建日志具有类别开头"主机。 "函数已启动，""函数已执行"和"函数已完成"日志具有类别"Host.Executor"。 

如果在函数代码中编写日志，其类别为"函数"。

### <a name="log-levels"></a>日志级别

此外包括 Azure Functions 记录器*日志级别*与每个日志。 [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) 是一个枚举，整数代码指示相对重要性：

|LogLevel    |代码|
|------------|---|
|跟踪       | 0 |
|调试       | 第 |
|信息 | 2 |
|警告     | 3 |
|错误       | 4 |
|严重    | 5 |
|无        | 6 |

日志级别 `None` 将在下一节中进行介绍。 

### <a name="log-configuration-in-hostjson"></a>在 host.json 中的日志配置

[Host.json](functions-host-json.md) 文件配置函数应用发送到 Application Insights 的日志记录数量。 对于每个类别，均可以指示要发送的最小日志级别。 有两个示例： 第一个示例针对[Functions 版本 2.x 运行时](functions-versions.md#version-2x)(.NET Core) 和第二个示例适用于 1.x 版运行时。

### <a name="version-2x"></a>版本 2.x

v2.x 运行时使用 [.NET Core 日志记录筛选器层次结构](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering)。 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>版本 1.x

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

此示例设置以下规则：

* 具有类别的日志`Host.Results`或`Function`，仅发送`Error`级别及更高版本到 Application Insights。 `Warning` 级别及以下级别的日志将被忽略。
* 对于 `Host.Aggregator` 类别的日志，将所有日志发送到 Application Insights。 `Trace` 日志级别与某些记录器称为 `Verbose` 的日志级别相同，但在 [host.json](functions-host-json.md) 文件中请使用 `Trace`。
* 对于所有其他日志，仅向 Application Insights 发送 `Information` 级别及更高级别。

[host.json](functions-host-json.md) 中的类别值控制所有以相同值开头的类别的日志记录。 `Host` 在中[host.json](functions-host-json.md)控制日志记录`Host.General`， `Host.Executor`， `Host.Results`，依次类推。

如果 [host.json](functions-host-json.md) 包含以相同字符串开头的多个类别，则先匹配较长的类别。 假设你想从运行时除外`Host.Aggregator`进行记录`Error`级别，但您想`Host.Aggregator`在`Information`级别：

### <a name="version-2x"></a>版本 2.x 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>版本 1.x 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

若要禁止类别的所有日志，可以使用日志级别 `None`。 无日志写入与该类别和它上面没有日志级别。

以下各部分描述了运行时创建的日志的主要类别。 

### <a name="category-hostresults"></a>类别 Host.Results

这些日志在 Application Insights 中显示为“requests”。 它们指示函数的成功或失败。

![请求图表](media/functions-monitoring/requests-chart.png)

所有这些日志写入在`Information`级别。 如果在筛选`Warning`或更高版本，不会看到任何此类数据。

### <a name="category-hostaggregator"></a>类别 Host.Aggregator

这些日志在一段[可配置](#configure-the-aggregator)的时间内提供函数调用的计数和平均值。 默认时段为 30 秒或 1,000 个结果，以先满足的条件为准。 

日志位于 Application Insights 中的 **customMetrics** 表内。 示例包括运行、 成功率和持续时间数。

![customMetrics 查询](media/functions-monitoring/custom-metrics-query.png)

所有这些日志写入在`Information`级别。 如果在筛选`Warning`或更高版本，不会看到任何此类数据。

### <a name="other-categories"></a>其他类别

除了已经列出的类别，其余类别的所有日志在 Application Insights 的 **traces** 表中提供。

![traces 查询](media/functions-monitoring/analytics-traces.png)

使用类别的开头的所有日志`Host`编写的 Functions 运行时。 "函数已启动"和"函数已完成"日志具有类别`Host.Executor`。 对于成功运行，这些日志是`Information`级别。 在记录异常`Error`级别。 运行时还创建 `Warning` 级别日志，例如：已发送到病毒邮件队列的队列邮件。

由函数代码编写的日志具有类别`Function`，可以是任何日志级别。

## <a name="configure-the-aggregator"></a>配置聚合器

如前一部分中所述，运行时聚合一段时间内有关函数执行的数据。 默认时段为 30 秒或 1,000 次运行，以先满足的条件为准。 可以在 [host.json](functions-host-json.md) 文件中配置此设置。  下面是一个示例：

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>配置采样

Application Insights 具有[采样](../azure-monitor/app/sampling.md)功能，可以防止在峰值负载时生成太多的遥测数据。 当传入遥测的速率超过指定的阈值时，Application Insights 开始随机忽略某些传入项。 项 / 秒的最大数目的默认设置为 5。 可以在 [host.json](functions-host-json.md) 中配置采样。  下面是一个示例：

### <a name="version-2x"></a>版本 2.x 

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 5
      }
    }
  }
}
```

### <a name="version-1x"></a>版本 1.x 

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

> [!NOTE]
> [采样](../azure-monitor/app/sampling.md)是默认启用的。 如果您看起来会丢失数据，您可能需要调整采样设置以适合特定监视方案。

## <a name="write-logs-in-c-functions"></a>在 C# 函数中编写日志

可以在 Application Insights 中显示为 traces 的函数代码中编写日志。

### <a name="ilogger"></a>ILogger

在函数中使用 [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) 参数，而不是 `TraceWriter` 参数。 通过使用创建的日志`TraceWriter`转到 Application Insights 中，但`ILogger`使你能够实现[结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。

使用 `ILogger` 对象，可以调用 `Log<level>`ILogger 上的 [ 扩展方法](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods)来创建日志。 下面的代码编写`Information`日志类别为"函数"。

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>结构化日志记录

占位符的顺序（而不是其名称）确定日志消息中使用的参数。 假设有以下代码：

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

如果保留相同的消息字符串并颠倒参数的顺序，则生成的消息文本将在错误的位置生成值。

以这种方式处理占位符，以便可以执行结构化日志记录。 Application Insights 存储参数名称 / 值对和消息字符串。 结果是消息参数变为可以查询的字段。

如果记录器方法调用类似于前面的示例，您可以查询该字段`customDimensions.prop__rowKey`。 `prop__`以确保在运行时将添加和字段在函数代码的字段之间没有冲突添加添加前缀。

此外，可以通过引用字段 `customDimensions.prop__{OriginalFormat}` 查询原始消息字符串。  

下面是 `customDimensions` 数据的示例 JSON 表示形式：

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>自定义指标日志记录

在 C# 脚本函数中，可以使用 `ILogger` 上的 `LogMetric` 扩展方法来在 Application Insights 中创建自定义指标。 下面是示例方法调用：

```csharp
logger.LogMetric("TestMetric", 1234);
```

此代码是调用的替代方法`TrackMetric`通过使用[适用于.NET 的 Application Insights API](#custom-telemetry-in-c-functions)。

## <a name="write-logs-in-javascript-functions"></a>在 JavaScript 函数中写入日志

在 Node.js 函数中，使用 `context.log` 编写日志。 结构化日志记录未启用。

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>自定义指标日志记录

运行时[版本 1.x](functions-versions.md#creating-1x-apps) Functions 运行时，可以使用 Node.js 函数中`context.log.metric`方法来在 Application Insights 中创建自定义指标。 此方法当前不支持在版本 2.x。 下面是示例方法调用：

```javascript
context.log.metric("TestMetric", 1234);
```

此代码是调用的替代方法`trackMetric`通过使用[Application Insights Node.js SDK](#custom-telemetry-in-javascript-functions)。

## <a name="log-custom-telemetry-in-c-functions"></a>登录自定义遥测C#函数

可以使用 [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) NuGet 程序包将自定义遥测数据发送到 Application Insights。 以下 C# 示例使用[自定义遥测 API](../azure-monitor/app/api-custom-events-metrics.md)。 示例针对的是 .NET 类库，但对于 C# 脚本，Application Insights 代码是相同的。

### <a name="version-2x"></a>版本 2.x

版本 2.x 运行时使用 Application Insights 中的较新功能自动将遥测与当前操作进行关联。 无需手动设置该操作`Id`， `ParentId`，或`Name`字段。

```cs
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        // In Functions v2, TelemetryConfiguration.Active is initialized with the InstrumentationKey
        // from APPINSIGHTS_INSTRUMENTATIONKEY. Creating a default TelemetryClient like this will 
        // automatically use that key for all telemetry. It will also enable telemetry correlation
        // with the current operation.
        // If you require a custom TelemetryConfiguration, create it initially with
        // TelemetryConfiguration.CreateDefault() to include this automatic correlation.
        private static TelemetryClient telemetryClient = new TelemetryClient();

        [FunctionName("HttpTrigger2")]
        public static Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            telemetryClient.TrackMetric(metric);

            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

### <a name="version-1x"></a>版本 1.x

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

不要调用`TrackRequest`或`StartOperation<RequestTelemetry>`因为您将看到函数调用的重复请求。  Functions 运行时自动跟踪请求。

不要设置 `telemetryClient.Context.Operation.Id`。 许多函数同时运行时，此全局设置会导致不正确的关联。 请改为创建新的遥测实例（`DependencyTelemetry`、`EventTelemetry`）并修改其 `Context` 属性。 然后将遥测实例传入到 `TelemetryClient` 的相应 `Track` 方法（`TrackDependency()`、`TrackEvent()`）。 此方法可确保遥测具有当前函数调用的正确的相关详细信息。

## <a name="log-custom-telemetry-in-javascript-functions"></a>在 JavaScript 函数中的日志自定义遥测

[Application Insights Node.js SDK](https://www.npmjs.com/package/applicationinsights) 当前为 beta 版本。 下面是一些可将自定义遥测发送到 Application Insights 的示例代码：

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    context.done();
};
```

`tagOverrides`参数集`operation_Id`到函数的调用 id。 通过此设置，可为给定的函数调用关联所有自动生成的遥测和自定义遥测。

## <a name="known-issues"></a>已知问题
<!-- Add a transitional sentence to introduce the section. -->

### <a name="dependencies"></a>依赖项

该函数所拥有的其他服务的依赖项不会自动显示。 可以编写自定义代码来显示依赖项。 有关示例，请参阅中的示例代码[C#自定义遥测部分](#custom-telemetry-in-c-functions)。 示例代码会导致*应用程序映射*在 Application Insights 中看起来像以下映像：

![应用程序映射](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>报告问题

若要报告 Functions 中的 Application Insights 集成问题，或提出建议或请求，请[在 GitHub 中创建问题](https://github.com/Azure/Azure-Functions/issues/new)。

## <a name="monitor-without-application-insights"></a>不使用 Application Insights 监视器

我们建议使用 Application Insights 监视函数。 它提供更多数据和更好的方式来分析数据。 但如果您愿意使用 Azure 存储的内置日志记录系统，可以继续使用该方法。

### <a name="azure-storage-account-for-logging"></a>日志记录的 azure 存储帐户

内置日志记录使用 `AzureWebJobsDashboard` 应用设置中的连接字符串所指定的存储帐户。 在函数应用页中，选择某一函数，然后选择**监视器**选项卡，然后选择要将它保存在**经典视图**。

![切换到经典视图](media/functions-monitoring/switch-to-classic-view.png)

获取函数执行列表。 选择某个函数执行可查看持续时间、输入数据、错误和关联的日志文件。

如果启用了 Application Insights，则可以返回到使用内置日志记录。 手动禁用 Application Insights，然后选择**监视器**选项卡。若要禁用 Application Insights 集成，请删除`APPINSIGHTS_INSTRUMENTATIONKEY`应用设置。

即使“监视”选项卡显示 Application Insights 数据，但如果未[禁用内置日志记录](#disable-built-in-logging)，也仍会看到文件系统中的日志数据。 在存储资源中，请转到**文件**，并选择该函数的文件服务。 然后转到**LogFiles** > **应用程序** > **函数** > **函数** > **your_function**若要查看日志文件。

### <a name="real-time-monitoring"></a>实时监视

您可以流式传输到命令行会话本地工作站上的日志文件。 使用[Azure 命令行界面 (CLI)](/cli/azure/install-azure-cli)或[Azure PowerShell](/powershell/azure/overview)。  

对于 Azure CLI 中，使用以下命令登录，请选择订阅并流式传输日志文件：

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <resource group name> --name <function app name>
```

对于 Azure PowerShell，使用以下命令添加 Azure 帐户，选择订阅并流式传输日志文件：

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <function app name> -Tail
```

有关详细信息，请参阅[如何流式传输日志](../app-service/troubleshoot-diagnostic-logs.md#streamlogs)。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core 日志记录](/aspnet/core/fundamentals/logging/)
