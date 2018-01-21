---
title: "Azure 服务总线端到端跟踪和诊断 | Microsoft Docs"
description: "服务总线客户端诊断和端到端跟踪概述"
services: service-bus-messaging
documentationcenter: 
author: lmolkova
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: lmolkova
ms.openlocfilehash: 847056acd2d97391782dcac1874a2739b7f5825c
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2018
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>通过服务总线消息传递进行分布式跟踪和关联

微服务开发中的常见问题之一是通过处理流程所涉及到的所有服务跟踪来自客户端的操作的能力。 此能力对于进行调试、性能分析、A/B 测试和其他典型诊断方案相当有用。
此问题的一个组成部分是跟踪工作的逻辑片段。 这包括消息处理结果和延迟，以及外部依赖项调用。 另一个组成部分是这些诊断事件在进程边界外部的关联。

当生成者通过队列发送消息时，此活动通常发生在其他某个逻辑操作的范围内，并由其他客户端或服务启动。 当使用者收到消息时，会继续相同的操作。 生成者与使用者（以及其他处理该操作的服务）也许会发出遥测事件，以跟踪操作流和结果。 若要将此类事件相关联并以端到端的方式跟踪操作，报告遥测数据的每个服务必须为每个事件提供跟踪上下文戳记。

Microsoft Azure 服务总线消息传递已定义生成者与使用者应该用来传递此类跟踪上下文的有效负载属性。
该协议基于 [HTTP 关联协议](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)。

| 属性名称        | 说明                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | 生成者针对队列发出的外部调用的唯一标识符。 请参阅 [HTTP 协议中的 Request-Id](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) 了解事实依据、注意事项和格式 |
|  Correlation-Context | 操作上下文，将传播到操作处理流程涉及到的所有服务。 有关详细信息，请参阅 [HTTP 协议中的 Correlation-Context](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-auto-tracing"></a>服务总线 .NET 客户端自动跟踪

从版本 3.0.0 开始，[适用于 .NET 的 Microsoft Azure 服务总线客户端](/dotnet/api/microsoft.azure.servicebus.queueclient)提供可由跟踪系统或客户端代码片段挂接的跟踪检测点。
使用检测可以从客户端跟踪对服务总线消息传递服务发出的所有调用。 如果消息处理是通过[消息处理程序模式](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler)完成的，则还会检测消息处理

### <a name="tracking-with-azure-application-insights"></a>使用 Azure Application Insights 进行跟踪

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) 提供丰富的性能监视功能，包括自动请求和依赖项跟踪。

请根据项目类型安装 Application Insights SDK：
- [ASP.NET](../application-insights/app-insights-asp-net.md) 2.5-beta2 或更高版本
- [ASP.NET Core](../application-insights/app-insights-asp-net-core.md) 2.2.0-beta2 或更高版本。
这些链接提供了有关安装 SDK、创建资源和配置 SDK（如果需要）的详细信息。 针对非 ASP.NET 应用程序，请参阅[适用于控制台应用程序的 Azure Application Insights](../application-insights/application-insights-console.md) 一文。

如果使用[消息处理程序模式](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler)来处理消息，则无需执行其他操作，系统会自动跟踪由服务所完成的所有服务总线调用，并将其与其他遥测项关联。 否则，请参考以下示例手动进行消息处理跟踪。

#### <a name="trace-message-processing"></a>跟踪消息处理

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you are using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

在本示例中，系统针对每个已处理的消息报告 `RequestTelemetry`，并提供时间戳、持续时间和结果（成功）。 遥测功能也会提供一组关联属性。
在消息处理期间报告的嵌套跟踪和异常也带有关联属性的戳记，代表它们是 `RequestTelemetry` 的“子级”。

如果在消息处理期间对支持的外部组件发出调用，则会自动跟踪和关联这些调用。 请参阅[使用 Application Insights .NET SDK 跟踪自定义操作](../application-insights/application-insights-custom-operations-tracking.md)来了解手动跟踪和关联。

### <a name="tracking-without-tracing-system"></a>在没有跟踪系统的情况下进行跟踪
如果跟踪系统不支持自动服务总线调用跟踪，可以考虑将此类支持添加到跟踪系统或应用程序中。 本部分介绍服务总线 .NET 客户端发送的诊断事件。  

使用 .NET 跟踪基元 [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) 和 [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) 检测服务总线 .NET 客户端。

`Activity` 充当跟踪上下文，而 `DiagnosticSource` 是通知机制。 

如果没有针对 DiagnosticSource 事件的侦听器，检测将会关闭，以避免产生检测成本。 DiagnosticSource 将所有控制权授予侦听器：
- 侦听器控制要侦听的源和事件
- 侦听器控制事件速率和采样
- 事件连同某个有效负载一起发送，该有效负载可提供完整上下文，使你能够在事件发生期间访问和修改 Message 对象

在继续实现之前，请先通过 [DiagnosticSource 用户指南](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)熟悉相关流程。

让我们在 ASP.NET Core 应用中针对服务总线事件创建一个侦听器，以通过 Microsoft.Extension.Logger 写入日志。
该侦听器使用 [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) 库来订阅 DiagnosticSource（不使用它也能轻松订阅 DiagnosticSource）

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

在此示例中，侦听器记录每个服务总线操作的持续时间、结果、唯一标识符和开始时间。

#### <a name="events"></a>活动

对于每个操作，将发送两个事件：“Start”和“Stop”。 你很有可能只对“Stop”事件感兴趣。 这些事件提供操作的结果，并以 Activity 属性的形式提供开始时间和持续时间。

每个事件有效负载为侦听器提供操作上下文，并复制 API 传入参数和返回值。 “Stop”事件有效负载具有“Start”事件有效负载的所有属性，因此可以完全忽略“Start”事件。

此外，所有事件具有“Entity”和“Endpoint”属性，但下表中省略了这些属性
  * `string Entity` - 实体（队列、主题等）的名称
  * `Uri Endpoint` - 服务总线终结点 URL

每个“Stop”事件具有表示 `TaskStatus` 异步操作已完成的 `Status` 属性，为便于阅读，下表中也省略了该属性。

下面是检测操作的完整列表：

| 操作名称 | 跟踪的 API | 特定的有效负载属性|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | IList<Message> Messages - 正在发送的消息的列表 |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | Message Message - 正在处理的消息<br/>DateTimeOffset ScheduleEnqueueTimeUtc - 已计划消息偏移<br/>long SequenceNumber - 已计划消息的序号（“Stop”事件有效负载） |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | long SequenceNumber - 要取消的消息的序号 | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) |int RequestedMessageCount - 可接收的消息数上限。<br/>IList<Message> Messages - 已接收的消息列表（“Stop”事件有效负载） |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | int FromSequenceNumber - 浏览消息批的起点。<br/>int RequestedMessageCount - 要检索的消息数。<br/>IList<Message> Messages - 已接收的消息列表（“Stop”事件有效负载） |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | IEnumerable<long> SequenceNumbers - 包含要接收的序号的列表。<br/>IList<Message> Messages - 已接收的消息列表（“Stop”事件有效负载） |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | IList<string> LockTokens - 包含要完成的相应消息的锁定标记的列表。|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | string LockToken - 要丢弃的相应消息的锁定标记。 |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | string LockToken - 要推迟的相应消息的锁定标记。 | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | string LockToken - 要加入死信队列的相应消息的锁定标记。 | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | string LockToken - 要续订锁的相应消息的锁定标记。<br/>DateTime LockedUntilUtc - 以 UTC 格式显示的新锁定标记过期日期和时间。 （“Stop”事件有效负载）|
| Microsoft.Azure.ServiceBus.Process | 在 [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) 中提供的消息处理程序 Lambda 函数 | Message Message - 正在处理的消息。 |
| Microsoft.Azure.ServiceBus.ProcessSession | 在 [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) 中提供的消息会话处理程序 lambda 函数 | Message Message - 正在处理的消息。<br/>IMessageSession Session - 正在处理的会话 |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | RuleDescription Rule - 提供要添加的规则的规则说明。 |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | string RuleName - 要删除的规则的名称。 |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | IEnumerable<RuleDescription> Rules- 与订阅关联的所有规则。 （仅限“Stop”有效负载） |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | string SessionId - 消息中的 sessionId。 |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | string SessionId - 消息中的 sessionId。<br/>byte [] State - 会话状态（“Stop”事件有效负载） |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | string SessionId - 消息中的 sessionId。<br/>byte [] State - 会话状态 |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | string SessionId - 消息中的 sessionId。 |
| Microsoft.Azure.ServiceBus.Exception | 任何已检测的 API| Exception Exception - 异常实例 |

在每个事件中，可以访问保存当前操作上下文的 `Activity.Current`。

#### <a name="logging-additional-properties"></a>记录其他属性

`Activty.Current` 提供当前操作及其父级的详细上下文。 有关更多详细信息，请参阅 [Activity 文档](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)。
服务总线检测在 `Activity.Current.Tags` 中提供其他信息 - 这些信息包含 `MessageId` 和 `SessionId`（如果已提供）。

跟踪“Receive”、“Peek”和“ReceiveDeferred”事件的活动还可能带有 `RelatedTo` 标记。 该标记包含作为结果收到的消息的 `Diagnostic-Id` 相异列表。
此类操作可能导致收到多个不相关的消息。 此外，当操作启动时，`Diagnostic-Id` 未知，因此，只能使用此标记将“Receive”操作关联到“Process”操作。 此标记在分析性能问题时相当有用，可以检查接收消息所花费的时间。

记录标记的有效方法是循环访问它们，将标记添加到上述示例后，结果如下所示： 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", "{tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>筛选和采样

在某些情况下，我们可能只需记录一部分事件，以减少性能开销或存储耗用量。 可以只记录“Stop”事件（如上述示例所示），或者对特定百分比的事件采样。 
使用 `DiagnosticSource` 可以通过 `IsEnabled` 谓词实现此目的。 有关详细信息，请参阅 [DiagnosticSource 中基于上下文的筛选](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering)。

可以针对单个操作调用 `IsEnabled` 多次，以将性能影响降到最低。

按以下顺序调用 `IsEnabled`：

1. `IsEnabled(<OperationName>, string entity, null)`，例如 `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`。 请注意，末尾没有“Start”或“Stop”。 使用此语句可以筛选出特定的操作或队列。 如果回调返回 `false`，则表示未发送操作的事件

  * 对于“Process”和“ProcessSession”操作，还会收到 `IsEnabled(<OperationName>, string entity, Activity activity)` 回调。 使用此回调可根据 `activity.Id` 或 Tags 属性筛选事件。
  
2. `IsEnabled(<OperationName>.Start)`，例如 `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`。 检查是否应激发“Start”事件。 结果只影响“Start”事件，但其他检测不依赖于它。

“Stop”事件没有 `IsEnabled`。

如果某个操作结果为异常，则会调用 `IsEnabled("Microsoft.Azure.ServiceBus.Exception")`。 只能订阅“Exception”事件并阻止剩余的检测。 在此情况下，仍需处理此类异常。 由于其他检测已禁用，因此跟踪上下文不应会连同消息一起从使用者流向生成者。

也可以使用 `IsEnabled` 来实现采样策略。 基于 `Activity.Id` 或 `Activity.RootId` 的采样可确保在所有尝试都获取一致的采样结果（前提是采样内容由跟踪系统或你自己的代码传播）。

如果同一个源存在多个 `DiagnosticSource` 侦听器，只需其中一个侦听器接受事件便已足够，因此无法保证调用 `IsEnabled`。

## <a name="next-steps"></a>后续步骤

* [服务总线基础知识](service-bus-fundamentals-hybrid-solutions.md)
* [Application Insights 关联](../application-insights/application-insights-correlation.md)
* 参阅 [Application Insights 监视依赖项](../application-insights/app-insights-asp-net-dependencies.md)，了解 REST、SQL 或其他外部资源是否会降低性能。
* [使用 Application Insights .NET SDK 跟踪自定义操作](../application-insights/application-insights-custom-operations-tracking.md)
