---
title: 从 Azure 或独立群集中的 .NET Service Fabric 应用生成日志事件
description: 了解如何向 Azure 群集或独立群集中托管的 .NET Service Fabric 应用程序添加日志记录。
services: service-fabric
documentationcenter: .net
author: thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/27/2018
ms.author: adegeo
ms.openlocfilehash: 7c1a2330b9eb595d05df31aa8511720911dfee97
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="add-logging-to-your-service-fabric-application"></a>向 Service Fabric 应用程序添加日志记录

应用程序必须提供足够的信息，以便在出现问题时对其进行取证式的调试。 日志记录是可以添加到 Service Fabric 应用程序的最重要功能之一。 发生故障时，正确的日志记录可以为调查故障提供有效方法。 通过分析日志模式，可以找到提高应用程序的性能或设计的方法。 本文档演示几个不同的日志记录选项。

## <a name="eventflow"></a>EventFlow

[EventFlow 库](https://github.com/Azure/diagnostics-eventflow)套件允许应用程序定义要收集哪些诊断数据，以及应将结果输出到的位置。 诊断数据可以是从性能计数器到应用程序跟踪的任何内容。 它与应用程序在同一进程中运行，因此可以最大程度地减少通信开销。 有关 EventFlow 和 Service Fabric 的详细信息，请参阅[使用 EventFlow 进行Azure Service Fabric 事件聚合](service-fabric-diagnostics-event-aggregation-eventflow.md)。

### <a name="using-structured-eventsource-events"></a>使用结构化 EventSource 事件

按用例定义消息事件可以在事件上下文中为有关事件的数据打包。 还可以根据指定事件属性的名称或值更轻松地进行搜索和筛选。 将检测输出结构化可使其变得更易于读取，但需要花费更多的精力和时间来为每个用例定义事件。 

某些事件定义可在整个应用程序中共享。 例如，可在应用程序中的多个服务之间重复使用方法启动或停止事件。 特定于域的服务（例如订单系统）可以包含 **CreateOrder** 事件，该事件包含自身的唯一事件。 这种方法可能会生成大量的事件，并可能需要在项目团队之间协调标识符。 

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The instance constructor is private to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceTypeRegisteredEventId = 3;
    [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
    public void ServiceTypeRegistered(int hostProcessId, string serviceType)
    {
        WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
    }

    // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceHostInitializationFailedEventId = 4;
    [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
    public void ServiceHostInitializationFailed(string exception)
    {
        WriteEvent(ServiceHostInitializationFailedEventId, exception);
    }

    ...

```

### <a name="using-eventsource-generically"></a>一般情况下使用 EventSource

由于特定的事件可能难以定义，许多用户都会使用一组常用的参数来定义少量的事件，这些参数通常以字符串的形式输出其信息。 因此，大部分结构化信息已丢失，使结果搜索和筛选变得更加困难。 使用此方法时，会定义一些通常对应于日志记录级别的事件。 以下代码片段定义调试和错误消息：

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The Instance constructor is private, to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    private const int DebugEventId = 10;
    [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
    public void Debug(string msg)
    {
        WriteEvent(DebugEventId, msg);
    }

    private const int ErrorEventId = 11;
    [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
    public void Error(string error, string msg)
    {
        WriteEvent(ErrorEventId, error, msg);
    }

    ...

```

也可以混合使用结构化检测和常规检测方法。 结构化检测用于报告错误和指标。 常规事件可用于详细日志记录，工程师在排除故障时可以使用这些日志信息。

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

ASP.NET Core 日志记录（[Microsoft.Extensions.Logging NuGet 包](https://www.nuget.org/packages/Microsoft.Extensions.Logging)）是一个日志记录框架，可为应用程序提供标准日志记录 API。 可以将对其他日志记录后端的支持插入到 ASP.NET Core 日志记录。 这可以为处理应用程序中的日志记录提供各种支持，而无需更改大量代码。

1. 将 **Microsoft.Extensions.Logging** NuGet 包添加到要检测的项目。 此外，还添加任何提供程序包。 有关详细信息，请参阅 [Logging in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)（ASP.NET Core 中的日志记录）。
2. 在服务文件中添加一条针对 **Microsoft.Extensions.Logging** 的 **using** 指令。
3. 在服务类中定义一个专用变量。

   ```csharp
   private ILogger _logger = null;
   ```

4. 在服务类的构造函数中添加此代码：

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. 开始检测方法中的代码。 下面提供了几个示例：

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>使用其他日志记录提供程序

某些第三方提供程序（包括 [Serilog](https://serilog.net/)、[NLog](http://nlog-project.org/) 和 [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging)）可以使用上一部分所述的方法。 可将其中的每个提供程序插入 ASP.NET Core 日志记录，也可以单独使用。 Serilog 中的某个功能可以扩充记录器发出的所有消息。 此功能对服务名称、类型和分区信息的输出可能很有作用。 若要在 ASP.NET Core 基础结构中使用此功能，请执行以下步骤：

1. 将 **Serilog**、**Serilog.Extensions.Logging**、**Serilog.Sinks.Literate** 和 **Serilog.Sinks.Observable** NuGet 包添加到项目。 
2. 创建 `LoggerConfiguration` 和记录器实例。

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. 将 `Serilog.ILogger` 参数添加到服务构造函数并传递新建的记录器。

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. 在服务构造函数中，为 **ServiceTypeName**、**ServiceName**、**PartitionId** 和 **InstanceId** 创建属性扩充器。

   ```csharp
   public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
       : base(context)
   {
       PropertyEnricher[] properties = new PropertyEnricher[]
       {
           new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
           new PropertyEnricher("ServiceName", context.ServiceName),
           new PropertyEnricher("PartitionId", context.PartitionId),
           new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
       };

       serilog.ForContext(properties);

       _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
   }
   ```

5. 可以像在不使用 Serilog 的情况下运行 ASP.NET Core 一样检测代码。

   >[!NOTE]
   >建议不要在前面的示例中使用静态 `Log.Logger`。 Service Fabric 可在单个进程中托管同一服务类型的多个实例。 如果使用静态 `Log.Logger`，属性扩充器的最后一个写入者会显示所有正在运行的实例的值。 这是 _logger 变量为何是服务类的专用成员变量的原因之一。 另外，必须将 `_logger` 提供给可跨服务使用的通用代码使用。

## <a name="next-steps"></a>后续步骤

- 阅读有关 [Service Fabric 中的应用程序监视](service-fabric-diagnostics-event-generation-app.md)的详细信息。
- 阅读使用 [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) 和 [Windows Azure 诊断](service-fabric-diagnostics-event-aggregation-wad.md)进行日志记录的相关信息。










