---
title: "使用 Azure Application Insights .NET SDK 跟踪自定义操作 | Microsoft Docs"
description: "使用 Azure Application Insights .NET SDK 跟踪自定义操作"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/30/2017
ms.author: sergkanz
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: b31d38fe2f7060597956a1ee9c66f43ce39d7240
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---

# <a name="track-custom-operations-with-application-insights-net-sdk"></a>使用 Application Insights .NET SDK 跟踪自定义操作

Azure Application Insights SDK 自动跟踪传入的 HTTP 请求和对依赖服务的调用，比如 HTTP 请求和 SQL 查询。 通过跟踪和关联请求及依赖项，可了解由微服务组成的应用程序对这些微服务的整体响应能力和可靠性。 

有一类应用程序模式一般不受支持。 正确监视此类模式需要手动代码检测。 本文介绍几种可能需要手动检测的模式，比如自定义队列处理和运行长时间运行的后台任务。

本文档将指导用户如何使用 Application Insights SDK 跟踪自定义操作。 本文档面向：

- 适用于 .NET 的 Application Insights（也称为 Base SDK）版本 2.4+。
- 适用于 Web 应用程序的 Application Insights（运行 ASP.NET）版本 2.4+。
- 适用于 ASP.NET Core 的 Application Insights 版本 2.1+。

## <a name="overview"></a>概述
操作是由应用程序运行的一项逻辑工作。 它具有名称、开始时间、持续时间、结果和执行上下文（如用户名、属性和结果）。 如果操作 A 由操作 B 启动，则操作 B 设置为 A 的父级。一个操作只能有一个父操作，但可以有许多子操作。 有关操作和遥测关联的详细信息，请参阅 [Azure Application Insights 遥测关联](application-insights-correlation.md)。

在 Application Insights.NET SDK 中，操作由抽象类 [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/Extensibility/Implementation/OperationTelemetry.cs) 及其后代 [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/RequestTelemetry.cs) 和 [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/DependencyTelemetry.cs) 描述。

## <a name="incoming-operations-tracking"></a>传入操作跟踪 
Application Insights Web SDK 自动收集 ASP.NET 应用程序（在 IIS 管道中运行）和所有 ASP.NET Core 应用程序的 HTTP 请求。 有其他平台和框架的社区支持解决方案。 但是，如果应用程序不受任何标准或社区支持解决方案的支持，则可手动进行检测。

需要自定义跟踪的另一个示例是，从队列接收项目的辅助角色。 对于某些队列，向队列添加消息的调用将作为依赖项进行跟踪。 但是，不会自动收集描述消息处理的高级操作。

我们来看看如何跟踪此类操作。

大致而言，此任务旨在创建 `RequestTelemetry` 并设置已知的属性。 在操作完成后，可跟踪遥测数据。 以下示例演示了此任务。

### <a name="http-request-in-owin-self-hosted-app"></a>Owin 自托管应用中的 HTTP 请求
在此示例中，我们将遵循 [HTTP Protocol for Correlation](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)（HTTP 关联协议）。 用户应该会收到此处所述的标头。

``` C#
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

HTTP 关联协议还声明 `Correlation-Context` 标头。 但为了简单起见，此处省略了该标头。

## <a name="queue-instrumentation"></a>队列检测
对于 HTTP 通信，我们创建了协议来传递关联详细信息。 某些队列的协议允许将其他元数据与消息一起传递，其他队列的协议则不然。

### <a name="service-bus-queue"></a>服务总线队列
Azure [服务总线队列](../service-bus-messaging/index.md)允许将属性包与消息一起传递。 我们用它来传递关联 ID。

服务总线队列使用基于 TCP 的协议。 Application Insights 不会自动跟踪队列操作，因而需要手动跟踪。 取消排队操作是一种推送样式 API，根本无法跟踪。

#### <a name="enqueue"></a>排队

```C#
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>进程
```C#
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Azure 存储队列
以下示例显示如何跟踪 [Azure 存储队列](../storage/queues/storage-dotnet-how-to-use-queues.md)操作，并将生成者、使用者和 Azure 存储之间的遥测相关联。 

存储队列具有一个 HTTP API。 用于 HTTP 请求的 Application Insights Dependency Collector 会跟踪对该队列的所有调用。
请确保在 `applicationInsights.config` 中有 `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer`。 如果没有，则按 [Azure Application Insights SDK 中的筛选和预处理](app-insights-api-filtering-sampling.md)中所述以编程方式添加。

如果手动配置 Application Insights，请确保创建并初始化 `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`，如下所示：
 
``` C#
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// You can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add a Storage endpoint. Otherwise, you might experience request signature validation issues on the Storage service side.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// Do not forget to dispose of the module during application shutdown.
```

用户可能还想将 Application Insights 操作 ID 与存储请求 ID 相关联。 有关如何设置与获取存储请求客户端和服务器请求 ID 的信息，请参阅[对 Azure 存储进行监视、诊断和故障排除](../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing)。

#### <a name="enqueue"></a>排队
由于存储队列支持 HTTP API，因此 Application Insights 会自动跟踪队列的所有操作。 在多数情况下，此检测已足够。 但是，为了将使用者跟踪与生成者跟踪相关联，必须传递某些关联上下文，方法类似于 HTTP 关联协议中所执行的操作。 

在此示例中，我们跟踪可选的 `Enqueue` 操作。 可以：

 - **关联重试（如果有）**：它们都有一个共同的父级，即 `Enqueue` 操作。 否则，它们都作为传入请求的子级进行跟踪。 如果有多个对队列的逻辑请求，可能很难发现导致重试的调用。
 - **关联存储日志（如果需要）**：它们与 Application Insights 遥测相关联。

`Enqueue` 操作是某个父操作（例如，传入 HTTP 请求）的子级。 HTTP 依赖项调用是 `Enqueue` 操作的子级以及传入请求的孙级：

```C#
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

若要减少应用程序报告的遥测数或者由于其他原因不想跟踪 `Enqueue` 操作，可直接使用 `Activity` API：

- 创建（并启动）新的 `Activity`，而不是启动 Application Insights 操作。 *无*需在其上分配除操作名称以外的任何属性。
- 将 `yourActivity.Id` 串行化到消息有效负载，而不是 `operation.Telemetry.Id`。 也可使用 `Activity.Current.Id`。


#### <a name="dequeue"></a>取消排队
与 `Enqueue` 类似，Application Insights 自动跟踪对存储队列的实际 HTTP 请求。 但是，`Enqueue` 操作可能发生在父上下文中，例如传入请求上下文。 Application Insights SDK 自动将此类操作（及其 HTTP 部分）与父请求和同一范围内报告的其他遥测相关联。

`Dequeue` 操作比较棘手。 Application Insights SDK 自动跟踪 HTTP 请求。 但是，在分析消息之前，它并不知道关联上下文。 因此无法关联 HTTP 请求，获取其他遥测的消息。

在多数情况下，将队列的 HTTP 请求与其他跟踪相关联也是有用处的。 以下示例演示如何执行此操作：

``` C#
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // If there is a message, we want to correlate the Dequeue operation with processing.
            // However, we will only know what correlation ID to use after we get it from the message,
            // so we will report telemetry after we know the IDs.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // Delete the message.
            return payload;
        }
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>进程

在以下示例中，通过类似于跟踪传入 HTTP 请求的方式跟踪传入消息：

```C#
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // It might also make sense to get the name from the message.
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

同样，可以检测其他队列操作。 应该以类似于取消排队操作的方式检测速览操作。 不必检测队列管理操作。 Application Insights 会跟踪 HTTP 之类的操作，在大多数情况下，这就足够了。

检测消息删除时，请务必设置操作（关联）标识符。 或者，可以使用 `Activity` API。 这样就无需在遥测项目上设置操作标识符，因为 Application Insights 会为用户完成：

- 从队列中获取项目后，创建新的 `Activity`。
- 使用 `Activity.SetParentId(message.ParentId)` 关联使用者日志和生成者日志。
- 启动 `Activity`。
- 使用 `Start/StopOperation` 帮助程序跟踪取消排队、处理和删除操作。 从同一个异步控制流（执行上下文）执行。 这样，它们就能正确关联。
- 停止 `Activity`。
- 使用 `Start/StopOperation` 或手动调用 `Track` 遥测。

### <a name="batch-processing"></a>批处理
有些队列允许对一个请求的多条消息取消排队。 这类消息的处理可能彼此独立，而且属于不同的逻辑操作。 在此情况下，不能将 `Dequeue` 操作与特定消息处理相关联。

每条消息都应在自己的异步控制流中处理。 有关详细信息，请参阅[传出依赖项跟踪](#outgoing-dependencies-tracking)部分。

## <a name="long-running-background-tasks"></a>长时间运行后台任务
某些应用程序可能因用户请求而启动长时间运行的操作。 从跟踪/检测的角度来看，它与请求或依赖项检测没有区别： 

``` C#
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<RequestTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

在此示例中，使用 `telemetryClient.StartOperation` 创建 `RequestTelemetry` 并填充相关上下文。 假设有一个父操作，它是由计划操作的传入请求创建的。 只要在与传入请求相同的异步控制流中启动 `BackgroundTask`，它就会与该父操作相关联。 `BackgroundTask` 和所有嵌套的遥测项自动与引发此项的请求相关联，即使请求结束也一样。

从不含与之关联的任何操作 (`Activity`) 的后台线程启动任务时，`BackgroundTask` 没有任何父级。 但是，它可以具有嵌套操作。 从任务报告的所有遥测项与 `BackgroundTask` 中创建的 `RequestTelemetry` 相关联。

## <a name="outgoing-dependencies-tracking"></a>传出依赖项跟踪
用户可以跟踪自己的依赖项类型或不受 Application Insights 支持的操作。

服务总线队列或 Azure 存储队列中的 `Enqueue` 方法可作为此类自定义跟踪的示例。

自定义依赖项跟踪的常规方法是：

- 调用 `TelemetryClient.StartOperation`（扩展）方法，该方法填充关联所需的 `DependencyTelemetry` 属性和某些其他属性（开始时间戳、持续时间）。
- 在 `DependencyTelemetry` 上设置其他自定义属性，比如名称和所需的任何其他上下文。
- 进行依赖项调用，并等它完成。
- 完成后，使用 `StopOperation` 停止操作。
- 处理异常。

`StopOperation` 仅停止已启动的操作。 如果当前运行的操作与要停止的操作不匹配，`StopOperation` 不执行任何操作。 如果在同一执行上下文中并行启动多个操作，则可能发生这种情况：

```C#
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

确保始终调用 `StartOperation` 并在其自身的上下文中运行任务：
```C#
public async Task RunMyTaskAsync()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
    try 
    {
        var myTask = await StartMyTaskAsync();
        // Update status code and success as appropriate.
    }
    catch(...) 
    {
        // Update status code and success as appropriate.
    }
    finally 
    {
        telemetryClient.StopOperation(operation);
    }
}
```

## <a name="next-steps"></a>后续步骤

- 了解 Application Insights 中的[遥测关联](application-insights-correlation.md)基础知识。
- 有关 Application Insights 的类型和数据模型，请参阅[数据模型](application-insights-data-model.md)。
- 向 Application Insights 报告自定义[事件和指标](app-insights-api-custom-events-metrics.md)。
- 查看上下文属性集合的标准[配置](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)。
- 查看 [System.Diagnostics.Activity 用户指南](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)，了解如何关联遥测。

