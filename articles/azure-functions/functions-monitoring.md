---
title: "监视 Azure Functions"
description: "了解如何将 Azure Application Insights 和 Azure Functions 结合使用来监视函数执行。"
services: functions
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构"
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/15/2017
ms.author: tdykstra
ms.openlocfilehash: 1a8158dd60b6e2eb15a16bf3efb60ef30d602fd6
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2017
---
# <a name="monitor-azure-functions"></a>监视 Azure Functions

## <a name="overview"></a>概述 

[Azure Functions](functions-overview.md) 提供与 [Azure Application Insights](../application-insights/app-insights-overview.md) 的内置集成来监视函数。 本文演示如何配置 Functions 来将遥测数据发送到 Application Insights。

![Application Insights 指标资源管理器](media/functions-monitoring/metrics-explorer.png)

Functions 还具有不使用 Application Insights 的内置监视。 我们建议使用 Application Insights，因为它提供更多的数据和更好的方式来分析数据。 有关内置监视的信息，请参阅[本文的上一部分](#monitoring-without-application-insights)。

## <a name="enable-application-insights-integration"></a>启用 Application Insights 集成

对于将数据发送到 Application Insights 的函数应用，它需要知道 Application Insights 实例的检测密钥。 在 [Azure 门户](https://portal.azure.com)中建立该连接的方法有两种：

* [在创建函数应用时创建连接的 Application Insights 实例](#new-function-app)。
* [将 Application Insights 实例连接到现有的函数应用](#existing-function-app)。
 
### <a name="new-function-app"></a>新建函数应用

在 Function App 的“创建”页上启用 Application Insights：

1. 将 Application Insights开关设置为“打开”。

2. 选择一个 Application Insights 位置。

   ![在创建函数应用时启用 Application Insights](media/functions-monitoring/enable-ai-new-function-app.png)

### <a name="existing-function-app"></a>现有的函数应用

获取检测密钥并将其保存在函数应用中：

1. 创建 Application Insights 实例。 将应用程序类型设置为“常规”。

   ![创建 Application Insights 实例，类型为“常规”](media/functions-monitoring/ai-general.png)

2. 从 Application Insights 实例的“Essentials”页复制检测密钥。 将鼠标悬停在显示的键值的末尾以获取“单击以复制”按钮。

   ![复制 Application Insights 检测密钥](media/functions-monitoring/copy-ai-key.png)

1. 在函数应用的“应用程序设置”页上，单击“添加新设置”[添加应用设置](functions-how-to-use-azure-function-app-settings.md#settings)。 将新设置命名为 APPINSIGHTS_INSTRUMENTATIONKEY，并粘贴复制的检测密钥。

   ![将检测密钥添加到应用设置](media/functions-monitoring/add-ai-key.png)

1. 单击“保存” 。

## <a name="view-telemetry-data"></a>查看遥测数据

若要从门户中的函数应用导航到连接的 Application Insights 实例，请在函数应用的“概述”页上选择“Application Insights”链接。

有关如何使用 Application Insights 的信息，请参阅 [Application Insights 文档](https://docs.microsoft.com/azure/application-insights/)。 本部分介绍如何在 Application Insights 中查看数据的一些示例。 如果已经熟悉 Application Insights，则可以直接转到[有关配置和自定义遥测数据的部分](#configure-categories-and-log-levels)。

在[指标资源管理器](../application-insights/app-insights-metrics-explorer.md)中，可以基于指标（如函数调用次数、执行时间和成功率）来创建图表和警报。

![指标资源管理器](media/functions-monitoring/metrics-explorer.png)

在[失败](../application-insights/app-insights-asp-net-exceptions.md)选项卡上，可以基于函数失败和服务器异常来创建图表和警报。 操作名称是函数名称。 不显示依赖项中的失败，除非为依赖项实现了[自定义遥测](#custom-telemetry-in-c-functions)。

![失败数](media/functions-monitoring/failures.png)

在[性能](../application-insights/app-insights-performance-counters.md)选项卡上，可以分析性能问题。

![性能](media/functions-monitoring/performance.png)

“服务器”选项卡显示资源利用率和每个服务器的吞吐量。 在函数阻碍基础资源的调试方案下，此数据非常有用。 服务器被称为云角色实例。

![服务器](media/functions-monitoring/servers.png)

[实时指标流](../application-insights/app-insights-live-stream.md)选项卡显示指标数据，因为该数据是实时创建的。

![实时流](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>查询遥测数据

借助 [Application Insights Analytics](../application-insights/app-insights-analytics.md) 可以访问数据库中以表形式存储的所有遥测数据。 Analytics 提供了一种用于提取、处理和可视化数据的查询语言。

![选择 Analytics](media/functions-monitoring/select-analytics.png)

![Analytics 示例](media/functions-monitoring/analytics-traces.png)

下面是一个查询示例。 本示例显示过去 30 分钟内每个辅助角色的请求分发。

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

可用的表会显示在左侧窗格的“架构”选项卡中。 可以在下表中找到由函数调用生成的数据：

* **traces** - 由运行时和函数代码创建的日志。
* **requests** - 一个请求用于一个函数调用。
* **exceptions** - 由运行时引发的任何异常。
* **customMetrics** - 成功和失败调用的计数、成功率、持续时间。
* **customEvents** - 由运行时跟踪的事件，例如：触发函数的 HTTP 请求。
* **performanceCounters** - 有关运行函数的服务器的性能的信息。

其他表适用于可用性测试以及客户端/浏览器遥测。 可以实现自定义遥测以向其中添加数据。

在每个表内，一些函数特定的数据位于 `customDimensions` 字段。  例如，以下查询检索所有具有日志级别 `Error` 的跟踪。

```
traces 
| where customDimensions.LogLevel == "Error"
```

运行时提供 `customDimensions.LogLevel` 和 `customDimensions.Category`。 可以在日志中提供在函数代码中编写的其他字段。 请参阅本文后面部分中的[结构化日志记录](#structured-logging)。

## <a name="configure-categories-and-log-levels"></a>配置类别和日志级别

无需进行任何自定义配置即可使用 Application Insights，但默认配置可能会导致大量数据。 如果使用的是 Visual Studio Azure 订阅，可能会达到 Application Insights 的数据上限。 本文的剩余部分将演示如何配置和自定义函数发送到 Application Insights 的数据。

### <a name="categories"></a>Categories

对于每个日志，Azure Functions 记录器都包含一个类别。 类别指示运行时代码或函数代码的哪个部分编写日志。 

Functions 运行时创建具有以“Host”开头的类别的日志。 例如，“函数已启动”、“函数已执行”和“函数已完成”日志具有类别“Host.Executor”。 

如果在函数代码中编写日志，则其类别为“Function”。

### <a name="log-levels"></a>日志级别

此外，对于每个日志，Azure Functions 记录器都会包含一个日志级别。 [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) 是一个枚举，整数代码指示相对重要性：

|LogLevel    |代码|
|------------|---|
|跟踪       | 0 |
|调试       | 1 |
|信息 | #N/A |
|警告     | 3 |
|错误       | 4 |
|严重    | 5 |
|无        | 6 |

日志级别 `None` 将在下一节中进行介绍。 

### <a name="configure-logging-in-hostjson"></a>配置 Host.json 中的日志记录

Host.json 文件配置函数应用发送到 Application Insights 的日志记录数量。 对于每个类别，均可以指示要发送的最小日志级别。 下面是一个示例：

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

此示例设置以下规则：

1. 对于类别为“Host.Results”或“Function”的日志，仅向 Application Insights 发送 `Error` 级别及更高级别。 `Warning` 级别及以下级别的日志将被忽略。
2. 对于类别为“Host.Aggregator”的日志， 仅向 Application Insights 发送 `Information` 级别及更高级别。 `Debug` 级别及以下级别的日志将被忽略。
3. 对于所有其他日志，仅向 Application Insights 发送 `Information` 级别及更高级别。

host.json 中的类别值控制所有以相同值开头的类别的日志记录。 例如，host.json 中的“Host”控制“Host.General”、“Host.Executor”、“Host.Results”等的日志记录。

如果 host.json 包含以相同字符串开头的多个类别，则先匹配较长的类别。 例如，假设想要让运行时中除“Host.Aggregator”之外的所有内容都在 `Error` 级别记录，而“Host.Aggregator”在 `Information` 级别记录：

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

若要禁止类别的所有日志，可以使用日志级别 `None`。 没有使用该类别编写的日志，该类别上也没有日志级别。

以下各部分描述了运行时创建的日志的主要类别。 

### <a name="category-hostresults"></a>类别 Host.Results

这些日志在 Application Insights 中显示为“requests”。 它们指示函数的成功或失败。

![请求图表](media/functions-monitoring/requests-chart.png)

所有这些日志在 `Information` 级别编写，因此，如果在 `Warning` 或更高级别进行筛选，将看不到任何此类数据。

### <a name="category-hostaggregator"></a>类别 Host.Aggregator

这些日志在一段[可配置](#configure-the-aggregator)的时间内提供函数调用的计数和平均值。 默认时段为 30 秒或 1,000 个结果，以先满足的条件为准。 

日志位于 Application Insights 中的 **customMetrics** 表内。 示例包括运行数、成功率和持续时间。

![customMetrics 查询](media/functions-monitoring/custom-metrics-query.png)

所有这些日志在 `Information` 级别编写，因此，如果在 `Warning` 或更高级别进行筛选，将看不到任何此类数据。

### <a name="other-categories"></a>其他类别

除了已经列出的类别，其余类别的所有日志在 Application Insights 的 **traces** 表中提供。

![traces 查询](media/functions-monitoring/analytics-traces.png)

由 Functions 运行时编写以“Host”开头的类别的所有日志。 “函数已启动”和“函数已完成”日志的类别为“Host.Executor”。 对于成功运行，这些日志是 `Information` 级别；异常在 `Error` 级别进行记录。 运行时还创建 `Warning` 级别日志，例如：已发送到病毒邮件队列的队列邮件。

由函数代码编写的日志类别为“Function”，并且可以是任何日志级别。

## <a name="configure-the-aggregator"></a>配置聚合器

如前一部分中所述，运行时聚合一段时间内有关函数执行的数据。 默认时段为 30 秒或 1,000 次运行，以先满足的条件为准。 可以在 host.json 文件中配置此设置。  下面是一个示例：

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>配置采样

Application Insights 具有[采样](../application-insights/app-insights-sampling.md)功能，可以防止在峰值负载时生成太多的遥测数据。 当遥测项的数目超过指定的速率时，Application Insights 开始随机忽略某些传入项。 可以在 host.json 中配置采样。  下面是一个示例：

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

## <a name="write-logs-in-c-functions"></a>在 C# 函数中编写日志

可以在 Application Insights 中显示为 traces 的函数代码中编写日志。

### <a name="ilogger"></a>ILogger

在函数中使用 [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) 参数，而不是 `TraceWriter` 参数。 通过使用 `TraceWriter` 创建的日志会转到 Application Insights，但借助 `ILogger` 可执行[结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。

使用 `ILogger` 对象，可以调用 `Log<level>` [ILogger 上的扩展方法](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions#Methods_)来创建日志。 例如，下面的代码编写类别为“Function”的 `Information` 日志。

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>结构化日志记录

占位符的顺序（而不是其名称）确定日志消息中使用的参数。 例如，假设有以下代码：

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

如果保留相同的消息字符串并颠倒参数的顺序，则生成的消息文本将在错误的位置生成值。

以这种方式处理占位符，以便可以执行结构化日志记录。 Application Insights 存储除了消息字符串之外的参数名称值对。 结果是消息参数变为可以查询的字段。

例如，如果记录器方法调用类似于前面的示例，则可以查询字段 `customDimensions.prop__rowKey`。 添加 `prop__` 前缀以确保运行时添加的字段和函数代码添加的字段之间没有冲突。

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

### <a name="logging-custom-metrics"></a>记录自定义指标  

在 C# 脚本函数中，可以使用 `ILogger` 上的 `LogMetric` 扩展方法来在 Application Insights 中创建自定义指标。 下面是示例方法调用：

```csharp
logger.LogMetric("TestMetric", 1234); 
```

此代码是一种替代方法，使用[适用于 .NET 的 Application Insights API](#custom-telemetry-in-c-functions) 调用 `TrackMetric`。

## <a name="write-logs-in-javascript-functions"></a>在 JavaScript 函数中写入日志

在 Node.js 函数中，使用 `context.log` 编写日志。 结构化日志记录未启用。

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="logging-custom-metrics"></a>记录自定义指标  

在 Node.js 函数中，可以使用 `context.log.metric` 方法来在 Application Insights 中创建自定义指标。 下面是示例方法调用：

```javascript
context.log.metric("TestMetric", 1234); 
```

此代码是一种替代方法，使用[适用于 Application Insights 的 Node.js SDK](#custom-telemetry-in-javascript-functions) 调用 `trackMetric`。

## <a name="custom-telemetry-in-c-functions"></a>C# 函数中的自定义遥测

可以使用 [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) NuGet 程序包将自定义遥测数据发送到 Application Insights。

下面是一个 C# 代码的示例，使用[自定义遥测 API](../application-insights/app-insights-api-custom-events-metrics.md)。 示例针对的是 .NET 类库，但对于 C# 脚本，Application Insights 代码是相同的。

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
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

        private static TelemetryClient telemetry = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;

            telemetry.Context.Operation.Id = context.InvocationId.ToString();
            telemetry.Context.Operation.Name = "cs-http";
            if (!String.IsNullOrEmpty(name))
            {
                telemetry.Context.User.Id = name;
            }
            telemetry.TrackEvent("Function called");
            telemetry.TrackMetric("Test Metric", DateTime.Now.Millisecond);
            telemetry.TrackDependency("Test Dependency", 
                "swapi.co/api/planets/1/", 
                start, DateTime.UtcNow - start, true);

            return name == null
                ? req.CreateResponse(HttpStatusCode.BadRequest, 
                    "Please pass a name on the query string or in the request body")
                : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
        }
    }
}
```

请勿调用 `TrackRequest` 或 `StartOperation<RequestTelemetry>`，因为你将看到函数调用的重复请求。  Functions 运行时自动跟踪请求。

在每次启动函数时，将 `telemetry.Context.Operation.Id` 设置为调用 ID。 这样就可以为给定的函数调用关联所有遥测项。

```cs
telemetry.Context.Operation.Id = context.InvocationId.ToString();
```

## <a name="custom-telemetry-in-javascript-functions"></a>JavaScript 函数中的自定义遥测

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

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

`tagOverrides` 参数将 `operation_Id` 设置为函数的调用 ID。 通过此设置，可为给定的函数调用关联所有自动生成的遥测和自定义遥测。

## <a name="known-issues"></a>已知问题

### <a name="dependencies"></a>依赖项

函数与其他服务之间的依赖关系不会自动显示，但可以编写自定义代码来显示依赖项。 [C# 自定义遥测部分](#custom-telemetry-in-c-functions)中的示例代码显示相关方式。 该示例代码会导致 Application Insights 中出现如下所示的应用程序映射：

![应用程序映射](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>报告问题

若要报告 Functions 中的 Application Insights 集成问题，或提出建议或请求，请[在 GitHub 中创建问题](https://github.com/Azure/Azure-Functions/issues/new)。

## <a name="monitoring-without-application-insights"></a>不使用 Application Insights 进行监视

我们建议使用 Application Insights 监视函数，因为它提供更多的数据和更好的方式来分析数据。 但还可以在 Azure 门户页面中为函数应用查找遥测和日志记录数据。 

为函数选择“监视”选项卡，获取函数执行的列表。 选择某个函数执行可查看持续时间、输入数据、错误和关联的日志文件。

> [!IMPORTANT]
> 对 Azure Functions 使用[消耗托管计划](functions-overview.md#pricing)时，Function App 中的“监视”磁贴不显示任何数据。 这是因为平台动态地缩放和管理计算实例。 这些指标在消耗计划上没有意义。

### <a name="real-time-monitoring"></a>实时监视

实时监视可通过在函数“监视”选项卡上单击“实时事件流”实现。实时事件流在新浏览器选项卡的图表中显示

> [!NOTE]
> 存在一个已知问题，可能会导致数据无法进行填充。 可能需要关闭包含实时事件流的浏览器选项卡，并再次单击“实时事件流”，让它可以正确填充事件流数据。 

这些统计信息是实时的，但执行数据的实际作图可能会有约 10 秒钟的延迟。

### <a name="monitor-log-files-from-a-command-line"></a>从命令行监视日志文件

可在本地工作站上使用 Azure 命令行接口 (CLI) 1.0 或 PowerShell 将日志文件流式传输到命令行会话。

### <a name="monitor-function-app-log-files-with-the-azure-cli-10"></a>使用 Azure CLI 1.0 监视函数应用日志文件

若要开始，请[安装 Azure CLI 1.0](../cli-install-nodejs.md)，并[登录到 Azure](/cli/azure/authenticate-azure-cli)。

使用以下命令来启用经典服务管理模式，选择订阅，然后流式传输日志文件：

```
azure config mode asm
azure account list
azure account set <subscriptionNameOrId>
azure site log tail -v <function app name>
```

### <a name="monitor-function-app-log-files-with-powershell"></a>使用 PowerShell 监视函数应用日志文件

若要开始，请[安装和配置 Azure PowerShell](/powershell/azure/overview)。

使用以下命令来添加 Azure 帐户，选择订阅，然后流式传输日志文件：

```
PS C:\> Add-AzureAccount
PS C:\> Get-AzureSubscription
PS C:\> Get-AzureSubscription -SubscriptionName "MyFunctionAppSubscription" | Select-AzureSubscription
PS C:\> Get-AzureWebSiteLog -Name MyFunctionApp -Tail
```

有关详细信息，请参阅[如何：流式传输 Web 应用的日志](../app-service/web-sites-enable-diagnostic-log.md#streamlogs)。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解有关 Application Insights 的详细信息](https://docs.microsoft.com/azure/application-insights/)

> [!div class="nextstepaction"]
> [了解有关 Functions 使用的日志记录框架的详细信息](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)
