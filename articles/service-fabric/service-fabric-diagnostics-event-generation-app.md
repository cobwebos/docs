---
title: "Azure Service Fabric 应用程序级别监视 | Microsoft Docs"
description: "了解用于监视和诊断 Azure Service Fabric 群集的应用程序和服务级别事件和日志。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/26/2017
ms.author: dekapur
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 3c472904641108b7383cd0f1416c47460f8de11a
ms.contentlocale: zh-cn
ms.lasthandoff: 06/01/2017

---

# <a name="application-and-service-level-event-and-log-generation"></a>应用程序和服务级别事件和日志生成

## <a name="instrumenting-the-code-with-custom-events"></a>检测带有自定义事件的代码

对于服务监视的其他大多数环节，检测代码是一个基本要求。 我们只能通过检测来判断是否出错以及诊断需要解决哪些问题。 尽管在技术上可将调试器连接到生产服务，但这种做法并不常见。 因此，提供详细的检测数据非常重要。

某些产品可自动检测代码。 尽管这些解决方案能够正常运行，但几乎始终都要执行手动检测。 最后，必须提供足够的信息来对应用程序进行取证式的调试。 本文将介绍检测代码的不同方法，以及如何在不同的方法之间做出选择。

## <a name="eventsource"></a>EventSource
在 Visual Studio 中通过模板创建 Service Fabric 解决方案时，将生成 **EventSource** 派生类（**ServiceEventSource** 或 **ActorEventSource**）。 将会创建一个模板，可将应用程序或服务的事件添加到其中。 **EventSource** 名称**必须**唯一，应该将它重命名，不要使用默认的模板字符串 MyCompany-&lt;solution&gt;-&lt;project&gt;。 使用多个同名的 **EventSource** 定义会导致运行时出现问题。 每个定义的事件必须具有唯一标识符。 如果标识符不唯一，将发生运行时失败。 某些组织为标识符预先分配了值范围，避免不同的开发团队之间发生冲突。 有关详细信息，请参阅 [Vance 的博客](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)或 [MSDN 文档](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx)。

### <a name="using-structured-eventsource-events"></a>使用结构化 EventSource 事件

本部分的代码示例中的每个事件是针对特定的用例（例如，注册某个服务类型时）定义的。 如果按用例定义消息，可以连同错误文本一起打包数据，同时可以根据指定属性的名称或值更方便地执行搜索和筛选。 将检测输出结构化可使其变得更易于使用，但需要花费更多的精力和时间来为每种用例定义新的事件。 某些事件定义可在整个应用程序中共享。 例如，可在应用程序中的多个服务之间重复使用方法启动或停止事件。 特定于域的服务（例如订单系统）可以包含 **CreateOrder** 事件，该事件包含自身的唯一事件。 这种方法可能会生成大量的事件，并可能需要在项目团队之间协调标识符。 

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
```

### <a name="using-eventsource-generically"></a>一般情况下使用 EventSource

由于特定的事件可能难以定义，许多用户都会使用一组常用的参数来定义少量的事件，这些参数通常以字符串的形式输出其信息。 因此，大部分结构化信息已丢失，使结果搜索和筛选变得更加困难。 使用此方法时，将会定义一些通常对应于日志记录级别的事件。 以下代码片段定义调试和错误消息：

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
```

也可以混合使用结构化检测和常规检测方法。 结构化检测用于报告错误和指标。 常规事件可用于详细日志记录，工程师在排除故障时可以使用这些日志信息。

## <a name="aspnet-core-logging"></a>ASP.NET Core 日志记录

必须认真规划如何检测代码。 适当的检测计划有助于避免代码基变得不稳定，从而需要重新检测代码。 为了降低风险，可以选择一个检测库，例如，Microsoft ASP.NET Core 中包含的 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/)。 ASP.NET Core 提供一个可在所选提供程序中使用的 [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) 接口，同时尽量减轻对现有代码的影响。 可以在 Windows 和 Linux 上的 ASP.NET Core 中以及整个 .NET Framework 中使用代码，从而将检测代码标准化。 下面对此进行进一步介绍：

### <a name="using-microsoftextensionslogging-in-service-fabric"></a>在 Service Fabric 中使用 Microsoft.Extensions.Logging

1. 将 Microsoft.Extensions.Logging NuGet 包添加到要检测的项目。 此外，请添加所有提供程序包（有关第三方包，请参阅以下示例）。 有关详细信息，请参阅 [Logging in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)（ASP.NET Core 中的日志记录）。
2. 在服务文件中添加一条针对 Microsoft.Extensions.Logging 的 **using** 指令。
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

## <a name="using-other-logging-providers"></a>使用其他日志记录提供程序

某些第三方提供程序（包括 [Serilog](https://serilog.net/)、[NLog](http://nlog-project.org/) 和 [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging)）可以使用上一部分所述的方法。 可将其中的每个提供程序插入 ASP.NET Core 日志记录，也可以单独使用。 Serilog 中的某个功能可以扩充记录器发出的所有消息。 此功能对服务名称、类型和分区信息的输出可能很有作用。 若要在 ASP.NET Core 基础结构中使用此功能，请执行以下步骤：

1. 将 Serilog、Serilog.Extensions.Logging 和 Serilog.Sinks.Observable NuGet 包添加到项目。 对于以下示例，还应该添加 Serilog.Sinks.Literate。 本文稍后将介绍一种更好的方法。
2. 在 Serilog 中创建 LoggerConfiguration 和记录器实例。

  ```csharp
  Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
  ```

3. 将 Serilog.ILogger 参数添加到服务构造函数并传递新建的记录器。

  ```csharp
  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
  ```

4. 在服务构造函数中添加以下代码，为服务的 ServiceTypeName、ServiceName、PartitionId 和 InstanceId 属性创建属性扩充器。 它还会将一个属性扩充器添加到 ASP.NET Core 日志记录工厂，以便能够在代码中使用 Microsoft.Extensions.Logging.ILogger。

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
  >不建议在上面的示例中使用静态 Log.Logger。 Service Fabric 可在单个进程中托管同一服务类型的多个实例。 如果使用静态 Log.Logger，属性扩充器的最后一个写入者会显示所有正在运行的实例的值。 这是 _logger 变量为何是服务类的专用成员变量的原因之一。 另外，必须将 _logger 提供给可跨服务使用的通用代码使用。

## <a name="choosing-a-logging-provider"></a>选择日志记录提供程序

如果应用程序对性能的依赖程度很高，则最好使用 EventSource。 与 ASP.NET Core 日志记录或任何可用的第三方解决方案相比，**EventSource** 使用的资源*通常*更少，并且性能更佳。  对于许多服务来说这并不是一个问题，但如果服务以性能为中心，则 **EventSource** 可能是更好的选择。 但是，若要获得结构化日志记录的这些优势，EventSource 要求工程团队做出大笔投资。 如果可能，建议对一些日志记录选项执行快速原型，然后选择最符合需求的选项。

## <a name="next-steps"></a>后续步骤

选择了用于检测应用程序和服务的日志记录提供程序之后，需要将日志和事件聚合才能将其发送到任何的分析平台。 阅读有关 [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) 和 [WAD](service-fabric-diagnostics-event-aggregation-wad.md) 的信息，以便更好地了解一些推荐选项。

