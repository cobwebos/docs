---
title: "Azure Service Fabric 监视和诊断概述 | Microsoft 文档"
description: "了解如何监视和诊断 Azure、开发或本地环境中托管的 Microsoft Azure Service Fabric 应用程序。"
services: service-fabric
documentationcenter: .net
author: ms-toddabel
manager: mfussell
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/9/2017
ms.author: toddabel
translationtype: Human Translation
ms.sourcegitcommit: ebbb29ee031fb477ce284f7a0d27c1522317f4f0
ms.openlocfilehash: 46a35fa4ec341561ab234f7ec19fb20658fcb2c4
ms.lasthandoff: 02/27/2017


---
# <a name="monitor-and-diagnose-azure-service-fabric-applications"></a>监视和诊断 Azure Service Fabric 应用程序

在实际生产环境中，监视和诊断至关重要。 在开发服务的过程中，可以借助 Azure Service Fabric 来实施监视和诊断，确保该服务可在单一计算机、本地开发环境和实际生产群集设置中无缝运行。

在开发服务的过程中，可以借助监视和诊断来实现以下目的：
* 尽量减少对客户造成的干扰。
* 提供业务见解。
* 监视资源使用情况。
* 检测硬件和软件故障或性能问题。
* 诊断潜在的服务问题。

“监视”是一个泛指的术语，具体包括以下任务：
* 检测代码
* 收集检测日志
* 分析数据
* 根据日志数据可视化见解
* 根据日志值和见解设置警报
* 监视基础结构
* 检测和诊断对客户造成了影响的问题

本文概述如何使用 Microsoft .NET Framework 来监视在 Azure 或本地托管的，或者在 Windows 或 Linux 上部署的 Service Fabric 群集。 我们将探讨监视和诊断的三个重要方面：
- 检测代码或基础结构
- 收集生成的事件
- 存储、聚合、可视化和分析

尽管有些产品能够解决上述所有三个方面的问题，但许多客户倾向于选择不同的技术来满足每个监视要素。 重要的是，必须将每个组成部分对接在一起才能为应用程序提供端到端的监视解决方案。

## <a name="monitoring-infrastructure"></a>监视基础结构

在基础结构发生故障期间 Service Fabric 可帮助保持应用程序运行，但你需要了解错误是在应用程序中还是底层基础结构中发生的。 规划容量时也需要监视基础结构，以了解何时添加或删除基础结构。 构成 Service Fabric 部署的基础结构和应用程序对于监视和故障排除过程非常重要。 即使客户可以使用应用程序，基础结构也仍可能遇到问题。

### <a name="azure-monitor"></a>Azure 监视器

可以使用 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) 来监视构建 Service Fabric 群集时所在的许多 Azure 资源。 系统会自动收集[虚拟机规模集](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)和每个[虚拟机](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines)的一组指标并将其显示在 Azure 门户中。 若要在 Azure 门户中查看收集的信息，请选择包含 Service Fabric 群集的资源组。 然后，选择要查看的虚拟机规模集。 在“监视”部分中，选择“指标”可查看值的图表。

![Azure 门户视图：收集的指标信息](./media/service-fabric-diagnostics-overview/azure-monitoring-metrics.PNG)

若要自定义图表，请遵照 [Metrics in Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)（Microsoft Azure 中的指标）中的说明。 还可以根据 [Create alerts in Azure Monitor for Azure services](../monitoring-and-diagnostics/insights-alerts-portal.md)（在 Azure Monitor 中为 Azure 服务创建警报）中所述，基于这些指标创建警报。 可以根据 [Configure a web hook on an Azure metric alert](../monitoring-and-diagnostics/insights-webhooks-alerts.md)（针对 Azure 指标警报配置 Webhook）中所述，使用 Webhook 将警报发送到通知服务。 Azure Monitor 仅支持一个订阅。 如果需要监视多个订阅或者需要其他功能，Microsoft Operations Management Suite 中的 [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 为本地基础结构和基于云的基础结构提供了全方位的 IT 管理解决方案。 可将来自 Azure 监视的数据直接路由到 Log Analytics，以便在一个位置查看整个环境的指标和日志。

建议使用 Operations Management Suite 来监视本地基础结构，但是，也可以使用组织用来监视基础结构的任何现有解决方案。

### <a name="service-fabric-support-logs"></a>Service Fabric 支持日志

如需联系 Microsoft 支持部门来获取 Azure Service Fabric 群集方面的帮助，几乎始终都需要提供支持日志。 如果群集托管在 Azure 中，则会自动配置支持日志，并在创建群集的过程中收集这些日志。 日志存储在群集资源组中的专用存储帐户内。 该存储帐户没有固定的名称，但在其中可以看到以 *fabric* 开头的 Blob 容器和表。 有关为独立群集设置日志收集的信息，请参阅 [Create and manage a standalone Azure Service Fabric cluster](service-fabric-cluster-creation-for-windows-server.md)（创建和管理独立 Azure Service Fabric 群集）以及 [Configuration settings for a standalone Windows cluster](service-fabric-cluster-manifest.md)（Windows 独立群集的配置设置）。 对于独立的 Service Fabric 实例，应该将日志发送到本地文件共享。 **必须**提供这些日志才能获得支持，但是，这些日志只能由 Microsoft 客户支持团队使用。

## <a name="instrument-your-code"></a>检测代码

对于服务监视的其他大多数环节，检测代码是一个基本要求。 我们只能通过检测来判断是否出错以及诊断需要解决哪些问题。 尽管在技术上可将调试器连接到生产服务，但这种做法并不常见。 因此，提供详细的检测数据非常重要。 生成这种量级的信息时，从本地节点传送所有事件可能会消耗大量资源。 许多服务使用由两个部分构成的策略来处理检测数据量：
1.  将所有事件短期保存在本地滚动更新的日志文件中，只在需要调试时才收集。 通常，详细诊断所需的事件保存在节点上，以降低成本和资源利用率
2.  指示服务运行状况的所有事件将发送到中心存储库，可在其中用来针对不正常的服务发出警报。 服务运行状况事件包括错误事件、检测信号事件和性能事件。

某些产品可自动检测代码。 尽管这些解决方案能够正常运行，但几乎始终都要执行手动检测。 最后，必须提供足够的信息来对应用程序进行取证式的调试。 后续部分将介绍检测代码的不同方法，以及如何在不同的方法之间做出选择。

### <a name="eventsource"></a>EventSource

在 Visual Studio 中通过模板创建 Service Fabric 解决方案时，将生成 **EventSource** 派生类（**ServiceEventSource** 或 **ActorEventSource**）。 将会创建一个模板，可将应用程序或服务的事件添加到其中。 **EventSource** 名称**必须**唯一，应该将它重命名，不要使用默认的模板字符串 MyCompany-&lt;solution&gt;-&lt;project&gt;。 使用多个同名的 **EventSource** 定义会导致运行时出现问题。 每个定义的事件必须具有唯一标识符。 如果标识符不唯一，将发生运行时失败。 某些组织为标识符预先分配了值范围，避免不同的开发团队之间发生冲突。 有关详细信息，请参阅 [Vance 的博客](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)或 [MSDN 文档](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx)。

#### <a name="using-structured-eventsource-events"></a>使用结构化 EventSource 事件

本部分的代码示例中的每个事件是针对特定的用例（例如，注册某个服务类型时）定义的。 如果按用例定义消息，可以连同错误文本一起打包数据，同时可以根据指定属性的名称或值更方便地执行搜索和筛选。 将检测输出结构化可使其变得更易于使用，但需要花费更多的精力和时间来为每种用例定义新的事件。 某些事件定义可在整个应用程序中共享。 例如，可在应用程序中的多个服务之间重复使用方法启动或停止事件。 特定于域的服务（例如订单系统）可以包含 **CreateOrder** 事件，该事件包含自身的唯一事件。 这种方法可能会生成大量的事件，并可能需要在项目团队之间协调标识符。 有关 Service Fabric 中 **EventSource** 事件结构的完整示例，请参阅“合作群集”示例中的 **PartyCluster.ApplicationDeployService** 事件。

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

#### <a name="using-eventsource-generically"></a>一般情况下使用 EventSource

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

### <a name="aspnet-core-logging"></a>ASP.NET Core 日志记录

必须认真规划如何检测代码。 适当的检测计划有助于避免代码基变得不稳定，从而需要重新检测代码。 为了降低风险，可以选择一个检测库，例如，Microsoft ASP.NET Core 中包含的 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/)。 ASP.NET Core 提供一个可在所选提供程序中使用的 [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) 接口，同时尽量减轻对现有代码的影响。 可以在 Windows 和 Linux 上的 ASP.NET Core 中以及整个 .NET Framework 中使用代码，从而将检测代码标准化。

#### <a name="using-microsoftextensionslogging-in-service-fabric"></a>在 Service Fabric 中使用 Microsoft.Extensions.Logging

1. 将 Microsoft.Extensions.Logging NuGet 包添加到要检测的项目。 此外，请添加所有提供程序包（有关第三方包，请参阅以下示例）。 有关详细信息，请参阅 [Logging in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)（ASP.NET Core 中的日志记录）。
2. 在服务文件中添加一条针对 Microsoft.Extensions.Logging 的 **using** 指令。
3. 在服务类中定义一个专用变量。

  ```csharp
  private ILogger _logger = null;

  ```
4. 在服务类的构造函数中添加此代码。

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

#### <a name="using-other-logging-providers"></a>使用其他日志记录提供程序

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

4. 在服务构造函数中添加以下代码。 该代码为服务的 **ServiceTypeName**、**ServiceName**、**PartitionId** 和 **InstanceId** 属性创建属性扩充器。 它还会将一个属性扩充器添加到 ASP.NET Core 日志记录工厂，以便能够在代码中使用 Microsoft.Extensions.Logging.ILogger。

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

  > [!NOTE]
  > 不建议在上面的示例中使用静态 Log.Logger。 Service Fabric 可在单个进程中托管同一服务类型的多个实例。 如果使用静态 Log.Logger，属性扩充器的最后一个写入者会显示所有正在运行的实例的值。 这是 _logger 变量为何是服务类的专用成员变量的原因之一。 另外，必须将 _logger 提供给可跨服务使用的通用代码使用。

### <a name="choosing-a-logging-provider"></a>选择日志记录提供程序

如果应用程序对性能的依赖程度很高，则最好使用 **EventSource**。 与 ASP.NET Core 日志记录或任何可用的第三方解决方案相比，**EventSource** 使用的资源*通常*更少，并且性能更佳。  对于许多服务来说这并不是一个问题，但如果服务以性能为中心，则 **EventSource** 可能是更好的选择。 若要获得与结构化日志记录相同的优势，**EventSource** 要求工程团队做出大笔投资。 若要确定要为项目使用哪种方法，请根据每种选项快速制作一个原型，然后选择最符合需要的选项。

## <a name="event-and-log-collection"></a>事件和日志收集

### <a name="azure-diagnostics"></a>Azure 诊断

除了 Azure Monitor 提供的信息以外，Azure 还在一个中心位置从每个服务收集事件。 有关详细信息，请参阅有关如何为 [Windows](service-fabric-diagnostics-how-to-setup-wad.md) 和 [Linux](service-fabric-diagnostics-how-to-setup-lad.md) 配置事件收集的文章。 这些文章说明了如何收集事件数据并将其发送到 Azure 存储。 可以通过在 Azure 门户或 Azure Resource Manager 模板中启用诊断来实现此目的。 Azure 诊断可收集 Service Fabric 自动生成的一些事件源：

- 使用 Reliable Actor 编程模型时生成的 **EventSource** 事件和性能计数器。 [Diagnostics and performance monitoring for Reliable Actors](service-fabric-reliable-actors-diagnostics.md)（Reliable Actors 的诊断和性能监视）中列举了这些事件。
- 使用 Reliable Services 编程模型时生成的 **EventSource** 事件。 [Diagnostic functionality for stateful Reliable Services](service-fabric-reliable-services-diagnostics.md)（有状态 Reliable Services 的诊断功能）中列举了这些事件。
- 系统事件以 Windows 事件跟踪 (ETW) 事件的形式发出。 从 Service Fabric 发出的许多事件都属于此类别，包括服务位置和启动/停止事件。 查看发出的事件的最佳方式是在本地计算机上使用 [Visual Studio 诊断事件查看器](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。 由于这些事件属于本机 ETW 事件，因此其收集方式存在一些限制。
- 从 Service Fabric 版本 5.4 开始，将会公开运行状况和负载指标事件。 这样，便可以使用事件收集来提供历史报告和发出警报。 这些事件也属于本机 ETW 事件，因此其收集方式存在一些限制。

经过配置后，事件将显示在创建群集时创建的某个 Azure 存储帐户中（假设已启用诊断）。 表命名为 WADServiceFabricReliableActorEventTable、WADServiceFabricReliableServiceEventTable 和 WADServiceFabricSystemEventTable。 默认不会添加运行状况事件；必须修改 Resource Manager 模板才能添加这些事件。 有关详细信息，请参阅[使用 Azure 诊断收集日志](service-fabric-diagnostics-how-to-setup-wad.md)。

在本部分列出的这些文章还说明了如何将自定义事件引入 Azure 存储中。 有关配置性能计数器的其他 Azure 诊断文章，或者有关将其他监视信息从虚拟机引入 Azure 诊断的文章同样适用于 Service Fabric 群集。 例如，如果不想要将 Azure 表存储用作目标，可参阅 [Streaming Azure Diagnostics data in the hot path by using Event Hubs](../event-hubs/event-hubs-streaming-azure-diags-data.md)（使用事件中心流式传输热路径中的 Azure 诊断数据）。 事件进入 Azure 事件中心后，可以读取这些事件并将其发送到所选位置。 详细了解如何[使用 Application Insights 集成 Azure 诊断信息](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)。

使用 Azure 诊断的一个缺点是，只能使用 Resource Manager 模板对它进行设置， 而诊断只能在虚拟机规模集级别进行。 虚拟机规模集对应于 Service Fabric 中的节点类型。 需要针对可在每个类型的节点上运行的所有应用程序和服务配置每个节点类型。 根据配置的应用程序和服务数目，这可能会生成大量的 **EventSource** 事件。 此外，每当应用程序的配置发生更改时，都必须部署 Resource Manager。 理想的情况下，监视配置会连同服务配置一起传播。

Azure 诊断仅适用于部署到 Azure 的 Service Fabric 群集， 包括 Windows 和 Linux 群集。

### <a name="eventflow"></a>EventFlow

[Microsoft 诊断 EventFlow](https://github.com/Azure/diagnostics-eventflow) 可将事件从某个节点路由到一个或多个监视目标。 由于它以 NuGet 包的形式包含在服务项目中，EventFlow 代码和配置可以连同服务一起传播，消除了前面所述的需要在 Azure 诊断中为每个节点完成配置的问题。 EventFlow 在服务进程中运行，直接连接到配置的输出。 由于这种直接连接，EventFlow 适用于 Azure、容器和本地服务部署。 在容器等高密度方案中运行 EventFlow 时请保持谨慎，因为每个 EventFlow 管道会建立外部连接。 如果托管大量进程，最终会建立大量的出站连接！ 这对于 Service Fabric 应用程序而言并不是一个大问题，因为 `ServiceType` 的所有副本在同一个进程中运行，限制了出站连接数。 EventFlow 还提供事件筛选，以便只发送与指定的筛选器匹配的事件。 有关如何在 Service Fabric 中使用 EventFlow 的详细信息，请参阅[直接从 Azure Service Fabric 服务进程收集日志](service-fabric-diagnostic-collect-logs-without-an-agent.md)。

使用 EventFlow：

1. 将 NuGet 包添加到服务项目。
2. 在服务的 **Main** 函数中创建 EventFlow 管道并配置输出。 以下示例使用 Serilog 作为输出。

  ```csharp
  internal static class Program
  {
      /// <summary>
      /// This is the entry point of the service host process.
      /// </summary>
      private static void Main()
      {
          try
          {
              using (var pipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MonitoringE2E-Stateless-Pipeline"))
              {
                  Log.Logger = new LoggerConfiguration().WriteTo.EventFlow(pipeline).CreateLogger();

                  // The ServiceManifest.xml file defines one or more service type names.
                  // Registering a service maps a service type name to a .NET type.
                  // When Service Fabric creates an instance of this service type,
                  // an instance of the class is created in this host process.

                  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
                  ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless).Name);

                  // Prevents this host process from terminating, so services keep running.
                  Thread.Sleep(Timeout.Infinite);
              }
          }
          catch (Exception e)
          {
              ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
              throw;
          }
      }
  }
  ```

3. 在服务的 \\PackageRoot\\Config 文件夹中创建名为 eventFlowConfig.json 的文件。 该文件中的配置如下所示：

  ```json
      {
      "inputs": [
          {
          "type": "EventSource",
          "sources": [
              { "providerName": "Microsoft-ServiceFabric-Services" },
              { "providerName": "Microsoft-ServiceFabric-Actors" },
              { "providerName": "MyCompany-MonitoringE2E-Stateless" }
          ]
          },
          {
          "type": "Serilog"
          }
      ],
      "filters": [
          {
          "type": "drop",
          "include": "Level == Verbose"
          },
          {
          "type": "metadata",
          "metadata": "request",
          "requestNameProperty": "RequestName",
          "include":  "RequestName==MyRequest",
          "durationProperty": "Duration",
          "durationUnit": "milliseconds"
          }
      ],
      "outputs": [
          {
          "type": "StdOutput"
          },
          {
          "type": "ApplicationInsights",
          "instrumentationKey": "== instrumentation key here =="
          }
      ],
      "schemaVersion": "2016-08-11",
      "extensions": []
      }
  ```
    配置中定义了两个输入：Service Fabric 创建的两个基于 **EventSource** 的源，以及服务的 **EventSource**。 使用 ETW 的系统级别和运行状况事件不可用于 EventFlow。 这是因为，需要使用高级特权来侦听 ETW 源，并且永远不应使用高特权运行服务。 另一个输入为 Serilog。 Serilog 配置出现在 **Main** 方法中。  应用了一些筛选器。 第一个筛选器告知 EventFlow 要删除属于详细事件级别的所有事件。 配置了两个输出：标准输出，用于将数据写入 Visual Studio 中的输出窗口；ApplicationInsights。 请确保添加检测密钥。

4. 检测代码。 在下一示例中，我们将以几种不同的方式检测 `RunAsync`。 以下代码仍使用 Serilog。 使用的某些语法特定于 Serilog。 请注意所选日志记录解决方案的具体功能。 生成了三个事件：一个调试级事件和两个信息性事件。 第二个信息性事件跟踪请求持续时间。 在上述 EventFlow 配置中，调试级事件不应流向输出。

  ```csharp
      Stopwatch sw = Stopwatch.StartNew();

      while (true)
      {
          cancellationToken.ThrowIfCancellationRequested();

          sw.Restart();

          // Delay a random interval, to provide a more interesting request duration.
          await Task.Delay(TimeSpan.FromMilliseconds(DateTime.Now.Millisecond), cancellationToken);

          ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);
          _logger.LogDebug("Debug level event from Microsoft.Logging");
          _logger.LogInformation("Informational level event from Microsoft.Logging");
          _logger.LogInformation("{RequestName} {Duration}", "MyRequest", sw.ElapsedMilliseconds);
      }
  ```

若要在 Azure Application Insights 中查看事件，请在 Azure 门户中转到你的 Application Insights 资源。 若要查看事件，请选中“搜索”框。

![Application Insights 中的事件搜索视图](./media/service-fabric-diagnostics-overview/ai-search-events.PNG)

在上面的屏幕截图底部可以看到跟踪。 其中只显示了两个事件，因为调试级事件已被 EventFlow 删除。 位于跟踪前面的请求条目是第三个 `_logger` 检测行。 该行显示该事件已转换成 Application Insights 中的请求指标。

在筛选器定义中，类型为 **metadata**。 此定义声明某个事件具有 `RequestName` 属性且其值为 `MyRequest`，另一个属性 `Duration` 包含请求持续时间，以毫秒为单位。 这就是在 Application Insights 中的请求事件内看到的内容。 同样的方法适用于任何受支持的 EventFlow 输入，包括 **EventSource**。

如果某个独立群集出于策略原因无法连接到基于云的解决方案，可以使用 Elasticsearch 作为输出。 但是，可以写入其他输出。我们建议使用提取请求。 ASP.NET Core 日志记录的某些第三方提供程序提供支持本地安装的解决方案。

## <a name="azure-service-fabric-health-and-load-reporting"></a>Azure Service Fabric 运行状况和负载报告

Service Fabric 具有自身的运行状况模型，以下文章对此做了详细介绍：
- [Service Fabric 运行状况监视简介](service-fabric-health-introduction.md)
- [报告并检查服务运行状况](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [添加自定义 Service Fabric 运行状况报告](service-fabric-report-health.md)
- [查看 Service Fabric 运行状况报告](service-fabric-view-entities-aggregated-health.md)

运行状况监视对于运行服务的多个方面至关重要。 当 Service Fabric 执行命名应用程序升级时，运行状况监视尤为重要。 服务的每个升级域都已升级并且提供给客户使用后，升级域必须先通过运行状况检查，然后部署才能转到下一个升级域。 如果无法实现良好的运行状况，部署将会回滚，使应用程序保持一种已知正常的状态。 尽管在回滚服务之前某些客户可能会受到影响，但大多数客户不会遇到问题。 此外，问题的解决速度相对较快，无需等待操作员的人工操作。 在代码中合并的运行状况检查越多，服务应对部署问题的弹性就越高。

服务运行状况的另一个方面是从服务报告指标。 指标在 Service Fabric 中非常重要，因为它们用于均衡资源使用量。 指标还可用作系统运行状况的指示器。 例如，假设某个应用程序包含许多服务，每个实例报告每秒请求数 (RPS) 指标。 如果一个服务使用的资源比另一个服务要多，Service Fabric 会围绕群集移动服务实例，尽量使资源利用率保持均衡。 有关资源利用的工作原理的详细说明，请参阅 [Manage resource consumption and load in Service Fabric with metrics](service-fabric-cluster-resource-manager-metrics.md)（在 Service Fabric 中使用指标管理资源消耗和负载）。

使用指标还能洞察服务的执行情况。 在不同的时间，都可以使用指标来检查服务是否根据所需的参数运行。 例如，如果趋势表明星期一上午 9 点的平均 RPS 为 1,000，则可以设置一份运行状况报告，在 RPS 低于 500 或高于 1,500 时发出警报。 尽管一切看上去可能正常，但还是值得执行一番检查，确保客户获得优越的体验。 服务可以定义一组可在执行运行状况检查时报告的指标，同时避免对群集的资源均衡造成影响。 为此，可将指标权重设置为零。 建议一开始为所有指标使用零权重，只有在确定指标加权会对群集的资源均衡产生何种影响时，才增大权重。

> [!TIP]
> 不要使用过多的加权指标， 否则可能难以了解服务实例为何会出于均衡目的而被移动， 并且某些指标可能会持续很长时间！

可以指明应用程序的运行状况和性能的任何信息都是指标和运行状况报告的候选项。 CPU 性能计数器可以告知节点的利用情况，但不会指明特定的服务是否正常，因为单个节点上可能运行了多个服务。 但是，RPS、已处理的项数和请求延迟等指标都可以指明特定服务的运行状况。

若要报告运行状况，请使用如下所示的代码：

  ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
  ```

若要报告指标，请使用如下所示的代码：

  ```csharp
    this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
  ```

## <a name="watchdogs"></a>监视器

监视器是一个独立的服务，可以监视各个服务的运行状况和负载，并报告运行状况模型层次结构中任何组件的运行状况。 这有助于防止出现基于单个服务的视图所检测不到的错误。 此外，监视器也是一个不错的代码托管位置，它可以针对已知状态执行补救措施，而无需用户交互。

## <a name="visualization-analysis-and-alerts"></a>可视化、分析和警报

监视的最后一个部分是可视化事件流、针对服务性能提供报告，以及在检测到问题时发出警报。 可以使用不同的解决方案实现监视的这个层面。 可以使用 Azure Application Insights 和 Operations Management Suite 来基于事件流发出警报。 可以使用 Microsoft Power BI 或第三方解决方案（如 [Kibana](https://www.elastic.co/products/kibana) 或 [Splunk](https://www.splunk.com/)）来可视化数据。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 诊断收集日志](service-fabric-diagnostics-how-to-setup-wad.md)
* [直接从 Azure Service Fabric 服务进程收集日志](service-fabric-diagnostic-collect-logs-without-an-agent.md)
*  [在 Service Fabric 中使用指标管理资源消耗和负载](service-fabric-cluster-resource-manager-metrics.md)

