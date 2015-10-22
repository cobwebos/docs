<properties
   pageTitle="有状态的 Reliable Service 诊断"
   description="有状态 Reliable Services 的诊断功能"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.date="09/03/2015"
   wacn.date=""/>

# 有状态 Reliable Services 的诊断功能
有状态 Reliable Services StatefulServiceBase 类会发出 [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 事件，它们可以用于调试服务、提供对运行时运行方式的深入了解以及帮助进行故障排除。

## EventSource 事件
有状态 Reliable Service StatefulServiceBase 类的 EventSource 名称是“Microsoft-ServiceFabric-Services”。当[在 Visual Studio 中调试](/documentation/articles/service-fabric-debugging-your-application)服务时，来自此事件源的事件在[](/documentation/articles/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally#view-service-fabric-system-events-in-visual-studio)“诊断事件”窗口中显示。

Service Fabric 还提供将这些事件定向到 [Application Insights](http://azure.microsoft.com/services/application-insights/) 的选项。有关这方面的详细信息，请参阅有关[适用于 Service Fabric 的 Application Insights 设置](/documentation/articles/service-fabric-diagnostics-application-insights-setup)的文章。

可帮助收集和/或查看 EventSource 事件的工具和技术的其他示例有 [PerfView](http://www.microsoft.com/download/details.aspx?id=28567)、[Microsoft Azure 诊断](/documentation/articles/cloud-services-dotnet-diagnostics)和 [Microsoft TraceEvent 库](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)。

## 事件

|事件名称|事件 ID|级别|事件说明|
|----------|--------|-----|-----------------|
|StatefulRunAsyncInvocation|1|信息性|在服务 RunAsync 任务启动时发出|
|StatefulRunAsyncCancellation|2|信息性|在服务 RunAsync 任务取消时发出|
|StatefulRunAsyncCompletion|3|信息性|在服务 RunAsync 任务完成时发出|
|StatefulRunAsyncSlowCancellation|4|警告|在服务 RunAsync 任务完成取消所用时间过长时发出|
|StatefulRunAsyncFailure|5|错误|在服务 RunAsync 任务引发异常时发出|

## 解释事件

StatefulRunAsyncInvocation、StatefulRunAsyncCompletion 和 StatefulRunAsyncCancellation 事件可用于供服务编写者了解服务的生命周期以及服务启动、取消和完成时的计时。在调试服务问题或了解服务生命周期时，这可能会十分有用。

服务编写者应请密切注意 StatefulRunAsyncSlowCancellation 和 StatefulRunAsyncFailure 事件，因为它们指示与服务有关的问题。

每当服务 RunAsync() 任务引发异常时，都会发出 StatefulRunAsyncFailure。引发的异常通常指示服务中存在错误或 bug。此外，异常会导致服务失败，并因此移动到另一个节点，这可能是成本高昂的操作，并且在移动服务时延迟传入请求。服务编写者应确定异常的原因并在可能时缓解它。

每当 RunAsync 任务的取消请求花费的时间超过 4 秒时，都会发出 StatefulRunAsyncSlowCancellation。当服务花费过长时间完成取消时，它会影响在另一个节点快速重启服务的能力，并可能会影响服务的总体可用性。

<!---HONumber=74-->