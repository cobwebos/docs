---
title: ApplicationInsights.config 参考 - Azure | Microsoft Docs
description: 启用或禁用数据收集模块，并添加性能计数器和其他参数。
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: olegan
ms.openlocfilehash: b2c407036277b17c0f8c08f3261c932a6dc66624
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77655475"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>使用 ApplicationInsights.config 或 .xml 配置 Application Insights SDK
Application Insights .NET SDK 由多个 NuGet 包组成。 [核心包](https://www.nuget.org/packages/Microsoft.ApplicationInsights)提供 API，用于将遥测数据发送到 Application Insights。 [其他包](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights)提供遥测*模块*和*初始值设定项*，用于自动从应用程序及其上下文跟踪遥测。 通过调整配置文件，可以启用或禁用遥测模块和初始值设定项，并为其中一些参数设置参数。

配置文件名为 `ApplicationInsights.config` 或 `ApplicationInsights.xml`，具体取决于应用程序的类型。 [安装大多数版本的 SDK][start]时，会自动将其添加到你的项目中。 默认情况下，在使用支持**Add > Application Insights 遥测**的 Visual Studio 模板项目中的自动体验时，将在项目根文件夹中创建 applicationinsights.config 文件，并在将编译复制到 bin 文件夹时创建该文件。 还可以通过[在 IIS 服务器上状态监视器][redfield]来将其添加到 web 应用。 如果使用 azure[网站的扩展](azure-web-apps.md)或[azure VM 和虚拟机规模集的扩展](azure-vm-vmss-apps.md)，则会忽略配置文件。

[在网页中][client]不存在用于控制 SDK 的等效文件。

本文档说明配置文件中显示的节、控制 SDK 组件的方式，以及哪些 NuGet 包会加载这些组件。

> [!NOTE]
> ApplicationInsights.config 和 .xml 指令不适用于 .NET Core SDK。 若要配置 .NET Core 应用程序，请遵循[本](../../azure-monitor/app/asp-net-core.md)指南。

## <a name="telemetry-modules-aspnet"></a>遥测模块 (ASP.NET)
每个遥测模块都收集特定类型的数据，并使用核心 API 发送数据。 不同的 NuGet 包会安装这些模块，同时在 .config 文件中添加所需的行。

在配置文件中，每个模块都有一个对应的节点。 要禁用某个模块，请删除该节点或将其注释掉。

### <a name="dependency-tracking"></a>依赖项跟踪
[依赖项跟踪](../../azure-monitor/app/asp-net-dependencies.md)收集有关应用对数据库以及外部服务和数据库的调用的遥测数据。 若要允许此模块在 IIS 服务器中工作，需要[安装状态监视器][redfield]。

还可以使用 [TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency) 编写自己的依赖项跟踪代码。

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet 包。

可以自动收集依赖项，而无需使用基于代理的（无代码置备）附加来修改代码。 若要在 Azure web 应用中使用它，请启用[Application Insights 扩展](azure-web-apps.md)。 若要在 Azure VM 或 Azure 虚拟机规模集中使用它，请启用[VM 和虚拟机规模集的应用程序监视扩展](azure-vm-vmss-apps.md)。

### <a name="performance-collector"></a>性能收集器
[收集系统性能计数器](../../azure-monitor/app/performance-counters.md)，例如 IIS 安装中的 CPU、内存和网络负载。 可以指定要收集哪些计数器，包括自己设置的性能计数器。

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet 包。

### <a name="application-insights-diagnostics-telemetry"></a>Application Insights 诊断遥测
`DiagnosticsTelemetryModule` 报告 Application Insights 检测代码本身中的错误。 例如，代码无法访问性能计数器，或 `ITelemetryInitializer` 引发异常。 此模块跟踪的跟踪遥测显示在[诊断搜索][diagnostic]中。

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>开发人员模式
将调试器附加到应用程序进程后，`DeveloperModeWithDebuggerAttachedTelemetryModule` 强制 `TelemetryChannel` 立即发送数据，一次发送一个遥测项。 这可以减少应用程序跟踪遥测数据与在 Application Insights 门户显示遥测数据的间隔时间， 但会明显增大 CPU 和网络带宽的开销。

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet 包

### <a name="web-request-tracking"></a>Web 请求跟踪
报告 HTTP 请求的[响应时间和结果代码](../../azure-monitor/app/asp-net.md)。

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet 包

### <a name="exception-tracking"></a>异常跟踪
`ExceptionTrackingTelemetryModule` 跟踪 Web 应用中未经处理的异常。 请参阅[故障和异常][exceptions]。

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet 包
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` - 跟踪[未观察到的任务异常](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx)。
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` - 跟踪辅助角色、Windows 服务和控制台应用程序的未经处理的异常。
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet 包。

### <a name="eventsource-tracking"></a>EventSource 跟踪
通过 `EventSourceTelemetryModule` 可配置要作为跟踪发送到 Application Insights 的 EventSource 事件。 有关跟踪 EventSource 事件的信息，请参阅[使用 EventSource 事件](../../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events)。

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>ETW 事件跟踪
通过 `EtwCollectorTelemetryModule` 可从 ETW 提供程序配置要作为跟踪发送到 Application Insights 的事件。 有关跟踪 ETW 事件的信息，请参阅[使用 ETW 事件](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events)。

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Microsoft.ApplicationInsights 包提供 SDK 的[核心 API](https://msdn.microsoft.com/library/mt420197.aspx)。 其他遥测模块使用此方法，你也可以[使用它来定义自己的遥测](../../azure-monitor/app/api-custom-events-metrics.md)数据。

* ApplicationInsights.config 中没有条目。
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet 包。 如果只安装此 NuGet，则不会生成任何 .config 文件。

## <a name="telemetry-channel"></a>遥测通道
[遥测通道](telemetry-channels.md)管理对 Application Insights 服务的遥测的缓冲和传输。

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` 是 web 应用程序的默认通道。 它在内存中缓冲数据，使用重试机制和本地磁盘存储来实现更可靠的遥测传递。
* `Microsoft.ApplicationInsights.InMemoryChannel` 是一种轻型遥测通道，如果未配置其他通道，则使用该通道。 

## <a name="telemetry-initializers-aspnet"></a>遥测初始值设定项 (ASP.NET)
遥测初始值设定项设置与遥测的每个项一起发送的上下文属性。

可以[编写自己的初始值设定项](../../azure-monitor/app/api-filtering-sampling.md#add-properties)来设置上下文属性。

标准的初始值设定项由 Web 或 WindowsServer NuGet 包设置：

* `AccountIdTelemetryInitializer` 设置 AccountId 属性。
* `AuthenticatedUserIdTelemetryInitializer` 像 JavaScript SDK 一样设置 AuthenticatedUserId 属性。
* 对于包含从 Azure 运行时环境提取的信息的所有遥测项，`AzureRoleEnvironmentTelemetryInitializer` 将更新 `RoleName` 上下文的 `RoleInstance` 和 `Device` 属性。
* 对于包含从 MS 版本生成的 `BuildInfoConfigComponentVersionTelemetryInitializer` 文件提取的值的所有遥测项，`Version` 将更新 `Component` 上下文的 `BuildInfo.config` 属性。
* `ClientIpHeaderTelemetryInitializer` 根据请求的 `Ip` HTTP 标头更新所有遥测项的 `Location` 上下文的 `X-Forwarded-For` 属性。
* `DeviceTelemetryInitializer` 更新所有遥测项的 `Device` 上下文的以下属性。
  * `Type` 设置为“PC”
  * `Id` 设置为 Web 应用程序运行所在的计算机的域名。
  * `OemName` 设置为使用 WMI 从 `Win32_ComputerSystem.Manufacturer` 字段提取的值。
  * `Model` 设置为使用 WMI 从 `Win32_ComputerSystem.Model` 字段提取的值。
  * `NetworkType` 设置为从 `NetworkInterface` 字段提取的值。
  * `Language` 设置为 `CurrentCulture` 的名称。
* 对于包含 Web 应用程序运行所在计算机的域名的所有遥测项，`DomainNameRoleInstanceTelemetryInitializer` 将更新 `RoleInstance` 上下文的 `Device` 属性。
* `OperationNameTelemetryInitializer` 根据 HTTP 方法、ASP.NET MVC 控制器的名称以及为了处理请求而调用的操作，更新所有遥测项的 `Name` 的 `RequestTelemetry` 属性，以及 `Name` 上下文的 `Operation` 属性。
* `OperationIdTelemetryInitializer` 或 `OperationCorrelationTelemetryInitializer` 在处理包含自动生成的 `Operation.Id` 的请求时，更新跟踪的所有遥测项的 `RequestTelemetry.Id` 上下文属性。
* 对于包含从用户浏览器中运行的 Application Insights JavaScript 检测代码生成的 `SessionTelemetryInitializer` Cookie 提取的值的所有遥测项，`Id` 将更新 `Session` 上下文的 `ai_session` 属性。
* `SyntheticTelemetryInitializer` 或 `SyntheticUserAgentTelemetryInitializer` 在处理来自综合源（例如可用性测试或搜索引擎 Bot）的请求时，更新跟踪的所有遥测项的 `User`、`Session` 和 `Operation` 上下文属性。 默认情况下，[指标资源管理器](../../azure-monitor/app/metrics-explorer.md)不显示综合遥测数据。

    `<Filters>` 设置请求的标识属性。
* 对于包含从用户浏览器中运行的 Application Insights JavaScript 检测代码生成的 `UserTelemetryInitializer` Cookie 提取的值的所有遥测项，`Id` 将更新 `AcquisitionDate` 上下文的 `User` 和 `ai_user` 属性。
* `WebTestTelemetryInitializer` 设置来自[可用性测试](../../azure-monitor/app/monitor-web-app-availability.md)的 HTTP 请求的用户 ID、会话 ID 和综合源属性。
  `<Filters>` 设置请求的标识属性。

对于在 Service Fabric 中运行的 .NET 应用程序，可包含 `Microsoft.ApplicationInsights.ServiceFabric` NuGet 包。 该包所含的 `FabricTelemetryInitializer` 会将 Service Fabric 属性添加到遥测项。 有关详细信息，请参阅[GitHub 页](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md)，了解由此 NuGet 包所添加的属性。

## <a name="telemetry-processors-aspnet"></a>遥测处理器 (ASP.NET)
仅在将遥测项从 SDK 发送到门户之前，遥测处理器才能筛选和修改每个遥测项。

可以[编写自己的遥测处理器](../../azure-monitor/app/api-filtering-sampling.md#filtering)。

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>自适应采样遥测处理器（来自 2.0.0-beta3）
此项已默认启用。 如果应用程序要发送大量遥测数据，此处理器将删除某些遥测数据。

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

参数将提供算法尝试实现的目标。 每个 SDK 实例独立工作，因此，如果服务器是由多个计算机组成的群集，实际遥测量会相应地倍增。

[了解有关采样的详细信息](../../azure-monitor/app/sampling.md)。

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>固定速率采样遥测处理器（来自 2.0.0-beta1）
还有标准的[采样遥测处理器](../../azure-monitor/app/api-filtering-sampling.md)（从2.0.1 开始）：

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>通道参数 (Java)
这些参数会影响到 Java SDK 存储和刷新它所收集的遥测数据的方式。

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
可以存储在 SDK 内存中的遥测项数。 达到此数目时，将刷新遥测缓冲区 - 即，将遥测项发送到 Application Insights 服务器。

* 最小值：1
* 最大值：1000
* 默认值：500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds
确定以何种频率刷新存储在内存中的数据（以及将数据发送到 Application Insights）。

* 最小值：1
* 最大值：300
* 默认值：5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB
确定分配给本地磁盘上持久性存储的最大大小，以 MB 为单位。 此存储用于保存无法发送到 Application Insights 终结点的遥测项。 达到该存储大小时，将丢弃新遥测项。

* 最小值：1
* 最大值：100
* 默认值：10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```

## <a name="instrumentationkey"></a>InstrumentationKey
确定显示数据的 Application Insights 资源。 通常，我们会使用单独的密钥为每个应用程序单独创建一个资源。

如果想要以动态方式设置密钥（例如，要将应用程序的结果发送到不同的资源），可以在配置文件中省略密钥，并在代码中设置密钥。

设置 TelemetryClient 的所有实例的密钥，包括标准遥测模块。 请在初始化方法中执行此操作，例如通过 ASP.NET 服务中的 global.aspx.cs：

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights;

    protected void Application_Start()
    {
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
        var telemetryClient = new TelemetryClient(configuration);
   
```

如果只想将特定的一组事件发送到不同的资源，可以针对特定的 TelemetryClient 设置密钥：

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

若要获取新密钥，请[在 Application Insights 门户中创建新资源][new]。



## <a name="applicationid-provider"></a>ApplicationId 提供程序

_从 v2.6.0 开始可用_

此提供程序的用途是根据检测密钥查找应用程序 ID。 应用程序 ID 包括在 RequestTelemetry 和 DependencyTelemetry 中并用来在门户中确定关联。

这是通过在代码或配置中设置 `TelemetryConfiguration.ApplicationIdProvider` 来提供的。

### <a name="interface-iapplicationidprovider"></a>接口：IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


我们在 [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) SDK 中提供了两个实现：`ApplicationInsightsApplicationIdProvider` 和 `DictionaryApplicationIdProvider`。

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

这是围绕我们的配置文件 API 的一个包装器。 它将限制请求和缓存结果。

当安装 [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) 或 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 时此提供程序将添加到配置文件中

此类有一个可选属性 `ProfileQueryEndpoint`。
默认情况下，这设置为 `https://dc.services.visualstudio.com/api/profiles/{0}/appId`。
如果需要为此配置来配置一个代理，建议为基址使用代理并包括“/api/profiles/{0}/appId”。 注意，“{0}”将在运行时根据请求替换为检测密钥。

#### <a name="example-configuration-via-applicationinsightsconfig"></a>通过 ApplicationInsights.config 实现的示例配置：
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>通过代码实现的示例配置：
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

这是一个静态提供程序，将依赖于所配置的检测密钥/应用程序 ID 对。

此类有一个 `Defined` 属性，它是包含检测密钥和应用程序 ID 对的一个 Dictionary<string,string>。

此类有一个可选的 `Next` 属性，这可以用来配置当请求你的配置中不存在的检测密钥时要使用的其他提供程序。

#### <a name="example-configuration-via-applicationinsightsconfig"></a>通过 ApplicationInsights.config 实现的示例配置：
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.DictionaryApplicationIdProvider, Microsoft.ApplicationInsights">
        <Defined>
            <Type key="InstrumentationKey_1" value="ApplicationId_1"/>
            <Type key="InstrumentationKey_2" value="ApplicationId_2"/>
        </Defined>
        <Next Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>通过代码实现的示例配置：
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>后续步骤
[了解有关 API 的详细信息][api]。

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: ../../azure-monitor/app/asp-net-exceptions.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[new]: ../../azure-monitor/app/create-new-resource.md 
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
