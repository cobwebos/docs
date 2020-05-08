---
title: 使用 Azure Application Insights .NET SDK 跟踪自定义操作
description: 使用 Azure Application Insights .NET SDK 跟踪自定义操作
ms.topic: conceptual
ms.date: 11/26/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 316c1b7ea32f661b009bfee7a89cb7e5ed082f3b
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690865"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>使用 Application Insights .NET SDK 跟踪自定义操作

Azure Application Insights SDK 自动跟踪传入的 HTTP 请求和对依赖服务的调用，比如 HTTP 请求和 SQL 查询。 通过跟踪和关联请求及依赖项，可了解由微服务组成的应用程序对这些微服务的整体响应能力和可靠性。 

有一类应用程序模式一般不受支持。 正确监视此类模式需要手动代码检测。 本文介绍几种可能需要手动检测的模式，比如自定义队列处理和运行长时间运行的后台任务。

本文档将指导用户如何使用 Application Insights SDK 跟踪自定义操作。 本文档面向：

- 适用于 .NET 的 Application Insights（也称为 Base SDK）版本 2.4+。
- 适用于 Web 应用程序的 Application Insights（运行 ASP.NET）版本 2.4+。
- 适用于 ASP.NET Core 的 Application Insights 版本 2.1+。

## <a name="overview"></a>概述
操作是由应用程序运行的一项逻辑工作。 它具有名称、开始时间、持续时间、结果和执行上下文（如用户名、属性和结果）。 如果操作 A 由操作 B 启动，则操作 B 设置为 A 的父级。一个操作只能有一个父操作，但可以有许多子操作。 有关操作和遥测关联的详细信息，请参阅 [Azure Application Insights 遥测关联](correlation.md)。

在 Application Insights.NET SDK 中，操作由抽象类 [OperationTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) 及其后代 [RequestTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) 和 [DependencyTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs) 描述。

## <a name="incoming-operations-tracking"></a>传入操作跟踪 
Application Insights Web SDK 自动收集 ASP.NET 应用程序（在 IIS 管道中运行）和所有 ASP.NET Core 应用程序的 HTTP 请求。 有其他平台和框架的社区支持解决方案。 但是，如果应用程序不受任何标准或社区支持解决方案的支持，则可手动进行检测。

需要自定义跟踪的另一个示例是，从队列接收项目的辅助角色。 对于某些队列，向队列添加消息的调用将作为依赖项进行跟踪。 但是，不会自动收集描述消息处理的高级操作。

我们来了解可以跟踪此类操作的方式。

大致而言，此任务旨在创建 `RequestTelemetry` 并设置已知的属性。 在操作完成后，可跟踪遥测数据。 以下示例演示了此任务。

### <a name="http-request-in-owin-self-hosted-app"></a>Owin 自托管应用中的 HTTP 请求
在此示例中，跟踪上下文根据 [HTTP 关联协议](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)进行传播。 用户应该会收到此处所述的标头。

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    // you may create a new TelemetryConfiguration instance, reuse one you already have
    // or fetch the instance created by Application Insights SDK.
    private readonly TelemetryConfiguration telemetryConfiguration = TelemetryConfiguration.CreateDefault();
    private readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
    
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
尽管存在用于相关的[W3C 跟踪上下文](https://www.w3.org/TR/trace-context/)和[http 协议](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)来通过 http 请求传递关联详细信息，但每个队列协议都必须定义如何沿队列消息传递相同的详细信息。 某些队列协议（如 AMQP）允许传递附加元数据，而另一些队列协议（如 Azure 存储队列）需要将上下文编码为消息有效负载。

> [!NOTE]
> * **使用 HTTP 的队列尚不支持跨组件跟踪**，如果生产者和使用者将遥测发送到不同的 Application Insights 资源，则“事务诊断体验”和“应用程序映射”将显示事务和端到端映射。 对于队列，尚不支持此项。 

### <a name="service-bus-queue"></a>服务总线队列
Application Insights 使用新的[适用于 .NET 的 Microsoft Azure 服务总线客户端](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) 3.0.0 版及更高版本跟踪服务总线消息传送调用。
如果使用[消息处理程序模式](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler)来处理消息，则无需执行其他操作，系统会自动跟踪由服务所完成的所有服务总线调用，并将其与其他遥测项关联。 如果手动处理消息，请参阅[使用 Microsoft Application Insights 跟踪的 Service Bus 客户端](../../service-bus-messaging/service-bus-end-to-end-tracing.md)。

如果使用 [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) 包，请进一步阅读 - 以下示例演示当服务总线队列使用 AMQP 协议且 Application Insights 不自动跟踪队列操作时，如何跟踪（和关联）对服务总线的调用。
在消息属性中传递关联标识符。

#### <a name="enqueue"></a>排队

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    
    operation.Telemetry.Type = "Azure Service Bus";
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
```csharp
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };

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
以下示例显示如何跟踪 [Azure 存储队列](../../storage/queues/storage-dotnet-how-to-use-queues.md)操作，并将生成者、使用者和 Azure 存储之间的遥测相关联。 

存储队列具有一个 HTTP API。 用于 HTTP 请求的 Application Insights Dependency Collector 会跟踪对该队列的所有调用。
它在 ASP.NET 和 ASP.NET Core 应用程序上默认配置。使用其他类型的应用程序时，可参阅[控制台应用程序文档](../../azure-monitor/app/console.md)

用户可能还想将 Application Insights 操作 ID 与存储请求 ID 相关联。 有关如何设置与获取存储请求客户端和服务器请求 ID 的信息，请参阅[对 Azure 存储进行监视、诊断和故障排除](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing)。

#### <a name="enqueue"></a>排队
由于存储队列支持 HTTP API，因此 Application Insights 会自动跟踪队列的所有操作。 在多数情况下，此检测已足够。 但是，为了将使用者跟踪与生成者跟踪相关联，必须传递某些关联上下文，方法类似于 HTTP 关联协议中所执行的操作。 

此示例演示如何跟踪 `Enqueue` 操作。 可以：

 - **关联重试（如果有）** ：它们都有一个共同的父级，即 `Enqueue` 操作。 否则，它们都作为传入请求的子级进行跟踪。 如果有多个对队列的逻辑请求，可能很难发现导致重试的调用。
 - **关联存储日志（如果需要）** ：它们与 Application Insights 遥测相关联。

`Enqueue` 操作是某个父操作（例如，传入 HTTP 请求）的子级。 HTTP 依赖项调用是 `Enqueue` 操作的子级以及传入请求的孙级：

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
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
- 将 `yourActivity.Id` 串行化到消息有效负载，而不是 `operation.Telemetry.Id`。 还可以使用 `Activity.Current.Id`。


#### <a name="dequeue"></a>取消排队
与 `Enqueue` 类似，Application Insights 自动跟踪对存储队列的实际 HTTP 请求。 但是，`Enqueue` 操作可能发生在父上下文中，例如传入请求上下文。 Application Insights SDK 自动将此类操作（及其 HTTP 部分）与父请求和同一范围内报告的其他遥测相关联。

`Dequeue` 操作比较棘手。 Application Insights SDK 自动跟踪 HTTP 请求。 但是，在分析消息之前，它并不知道关联上下文。 不可能将获取消息的 HTTP 请求与遥测的其余部分相关联，特别是当收到多个消息时。

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("dequeue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Dequeue " + queue.Name;
    
    try
    {
        var message = await queue.GetMessageAsync();
    }
    catch (StorageException e)
    {
        operation.telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.telemetry.Success = false;
        operation.telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }

    return null;
}
```

#### <a name="process"></a>进程

在以下示例中，通过类似于跟踪传入 HTTP 请求的方式跟踪传入消息：

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };
    
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

检测消息删除时，请务必设置操作（关联）标识符。 或者，可以使用 `Activity` API。 这样就无需在遥测项目上设置操作标识符，因为 Application Insights SDK 会为用户完成：

- 从队列中获取项目后，创建新的 `Activity`。
- 使用 `Activity.SetParentId(message.ParentId)` 关联使用者日志和生产者日志。
- 启动 `Activity`。
- 使用 `Start/StopOperation` 帮助程序跟踪取消排队、处理和删除操作。 从同一个异步控制流（执行上下文）执行。 这样，它们就能正确关联。
- 停止 `Activity`。
- 使用 `Start/StopOperation` 或手动调用 `Track` 遥测。

### <a name="dependency-types"></a>依赖项类型

Application Insights 使用依赖关系类型自定义 UI 体验。 对于队列，它识别出以下可改善`DependencyTelemetry`事务诊断体验[的 ](/azure/azure-monitor/app/transaction-diagnostics) 类型：
- `Azure queue` 适用于 Azure 存储队列
- `Azure Event Hubs` 适用于 Azure 事件中心
- `Azure Service Bus` 适用于 Azure 服务总线

### <a name="batch-processing"></a>批处理
有些队列允许对一个请求的多条消息取消排队。 这类消息的处理可能彼此独立，而且属于不同的逻辑操作。 无法将 `Dequeue` 操作与正在处理的特定消息相关联。

每条消息都应在自己的异步控制流中处理。 有关详细信息，请参阅[传出依赖项跟踪](#outgoing-dependencies-tracking)部分。

## <a name="long-running-background-tasks"></a>长时间运行后台任务

某些应用程序可能因用户请求而启动长时间运行的操作。 从跟踪/检测的角度来看，它与请求或依赖项检测没有区别： 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>(taskName);
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

在此示例中，`telemetryClient.StartOperation` 创建 `DependencyTelemetry` 并填充相关上下文。 假设有一个父操作，它是由计划操作的传入请求创建的。 只要在与传入请求相同的异步控制流中启动 `BackgroundTask`，它就会与该父操作相关联。 `BackgroundTask` 和所有嵌套的遥测项自动与引发此项的请求相关联，即使请求结束也一样。

从不含与之关联的任何操作 (`Activity`) 的后台线程启动任务时，`BackgroundTask` 没有任何父级。 但是，它可以具有嵌套操作。 从任务报告的所有遥测项与 `DependencyTelemetry` 中创建的 `BackgroundTask` 相关联。

## <a name="outgoing-dependencies-tracking"></a>传出依赖项跟踪
用户可以跟踪自己的依赖项类型或不受 Application Insights 支持的操作。

服务总线队列或 Azure 存储队列中的 `Enqueue` 方法可作为此类自定义跟踪的示例。

自定义依赖项跟踪的常规方法是：

- 调用 `TelemetryClient.StartOperation`（扩展）方法，该方法填充关联所需的 `DependencyTelemetry` 属性和某些其他属性（开始时间戳、持续时间）。
- 在 `DependencyTelemetry` 上设置其他自定义属性，比如名称和所需的任何其他上下文。
- 进行依赖项调用，并等它完成。
- 完成后，使用 `StopOperation` 停止操作。
- 处理异常。

```csharp
public async Task RunMyTaskAsync()
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1"))
    {
        try 
        {
            var myTask = await StartMyTaskAsync();
            // Update status code and success as appropriate.
        }
        catch(...) 
        {
            // Update status code and success as appropriate.
        }
    }
}
```

释放操作会导致操作停止，因此你可以执行此操作而不用调用 `StopOperation`。

*警告*：在某些情况下，未处理的异常可能会[阻止](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally)调用 `finally`，因此无法跟踪操作。

### <a name="parallel-operations-processing-and-tracking"></a>并行处理和跟踪操作

`StopOperation` 仅停止已启动的操作。 如果当前运行的操作与要停止的操作不匹配，`StopOperation` 不执行任何操作。 如果在同一执行上下文中并行启动多个操作，则可能发生这种情况：

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// FAILURE!!! This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

请确保始终在同一`StartOperation`异步**方法中调用**  和处理操作，以隔离并行运行的操作。 如果操作是同步的（或非异步的），请包装过程并使用 `Task.Run` 跟踪：

```csharp
public void RunMyTask(string name)
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>(name))
    {
        Process();
        // Update status code and success as appropriate.
    }
}

public async Task RunAllTasks()
{
    var task1 = Task.Run(() => RunMyTask("task 1"));
    var task2 = Task.Run(() => RunMyTask("task 2"));
    
    await Task.WhenAll(task1, task2);
}
```

## <a name="applicationinsights-operations-vs-systemdiagnosticsactivity"></a>ApplicationInsights 操作与 System.Diagnostics.Activity
`System.Diagnostics.Activity` 表示分布式跟踪上下文，可供框架和库用于在进程内外创建和传播上下文，并关联遥测项。 活动与 `System.Diagnostics.DiagnosticSource` 配合使用，后者是框架/库之间的通知机制，用于通知有趣的事件（传入或传出请求、异常等）。

活动是 Application Insights 中的“一类公民”，自动依赖项和请求集合特别依赖它们和 `DiagnosticSource` 事件。 如果在应用程序中创建活动，则不会创建 Application Insights 遥测。 Application Insights 需要接收 DiagnosticSource 事件并了解事件名称和有效负载，然后才能将活动转换为遥测。

每项 Application Insights 操作（请求或依赖）都涉及 `Activity` - 调用 `StartOperation` 时，它会在下面创建活动。 `StartOperation` 是建议的方法，用于手动跟踪请求或依赖项遥测，并确保一切都已关联。

## <a name="next-steps"></a>后续步骤

- 了解 Application Insights 中的[遥测关联](correlation.md)基础知识。
- 查看相关数据如何支持[事务诊断体验](../../azure-monitor/app/transaction-diagnostics.md)和[应用程序映射](../../azure-monitor/app/app-map.md)。
- 有关 Application Insights 的类型和数据模型，请参阅[数据模型](../../azure-monitor/app/data-model.md)。
- 向 Application Insights 报告自定义[事件和指标](../../azure-monitor/app/api-custom-events-metrics.md)。
- 查看上下文属性集合的标准[配置](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)。
- 查看 [System.Diagnostics.Activity 用户指南](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)，了解如何关联遥测。
