---
title: 使用实时指标流进行诊断 - Azure Application Insights
description: 通过自定义指标实时监视 Web 应用，使用实时失败、跟踪和事件源以诊断问题。
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: 1b8b4c43c559831810db9b92da6c2743556cd2ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90973588"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>实时指标流：以 1 秒的延迟进行监视和诊断

使用实时指标流 (也称为 QuickPulse) [Application Insights](./app-insights-overview.md)中监视实时的生产型 web 应用程序。 选择并筛选指标和性能计数器进行实时监视，且服务不会受到任何干扰。 从失败请求和异常的样本中检查堆栈跟踪。 除了 [探查器](./profiler.md) 和 [快照调试器](./snapshot-debugger.md)，实时指标流为您的实时网站提供了一个功能强大的非干扰性诊断工具。

使用实时指标流可实现以下操作：

* 通过监视性能和故障计数，在修补程序发布时对其进行验证。
* 监视负载测试的效果，实时诊断问题。
* 通过选择和筛选想要监视的指标，重点处理特定的测试会话或筛选出已知问题。
* 发生异常时获取异常跟踪。
* 试用筛选器，查找最相关的 KPI。
* 实时监视任何 Windows 性能计数器。
* 轻松识别有问题的服务器，并筛选出只与该服务器相关的所有 KPI/实时源。

![“实时指标”选项卡](./media/live-stream/live-metric.png)

目前 ASP.NET、ASP.NET Core、Azure Functions、Java 和 Node.js 应用支持实时指标。

## <a name="get-started"></a>入门

1. 遵循语言特定的准则来启用实时指标。
   * [ASP.NET](./asp-net.md) -默认情况下启用实时度量值。
   * 默认情况下， [ASP.NET Core](./asp-net-core.md)启用-Live 指标。
   * [.Net/.Net Core 控制台/辅助角色](./worker-service.md)-默认情况下启用实时指标。
   * [.Net 应用程序-使用代码启用](#enable-livemetrics-using-code-for-any-net-application)。
   * [Node.js](./nodejs.md#live-metrics)

2. 在 [Azure 门户](https://portal.azure.com)中，打开应用的 Application Insights 资源，然后打开实时流。

3. 如果可能在筛选器中使用客户名称等敏感数据，请[确保控制通道的安全](#secure-the-control-channel)。

### <a name="enable-livemetrics-using-code-for-any-net-application"></a>使用适用于任何 .NET 应用程序的代码启用 LiveMetrics

即使默认情况下在使用 .NET 应用程序的推荐说明的情况下安装 LiveMetrics，但以下说明了如何手动设置实时度量值。

1. 安装 NuGet 包 [applicationinsights.config。 PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector)
2. 下面的示例控制台应用代码显示了如何设置实时指标。

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using System;
using System.Threading.Tasks;

namespace LiveMetricsDemo
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create a TelemetryConfiguration instance.
            TelemetryConfiguration config = TelemetryConfiguration.CreateDefault();
            config.InstrumentationKey = "INSTRUMENTATION-KEY-HERE";
            QuickPulseTelemetryProcessor quickPulseProcessor = null;
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    quickPulseProcessor = new QuickPulseTelemetryProcessor(next);
                    return quickPulseProcessor;
                })
                .Build();

            var quickPulseModule = new QuickPulseTelemetryModule();

            // Secure the control channel.
            // This is optional, but recommended.
            quickPulseModule.AuthenticationApiKey = "YOUR-API-KEY-HERE";
            quickPulseModule.Initialize(config);
            quickPulseModule.RegisterTelemetryProcessor(quickPulseProcessor);

            // Create a TelemetryClient instance. It is important
            // to use the same TelemetryConfiguration here as the one
            // used to setup Live Metrics.
            TelemetryClient client = new TelemetryClient(config);

            // This sample runs indefinitely. Replace with actual application logic.
            while (true)
            {
                // Send dependency and request telemetry.
                // These will be shown in Live Metrics stream.
                // CPU/Memory Performance counter is also shown
                // automatically without any additional steps.
                client.TrackDependency("My dependency", "target", "http://sample",
                    DateTimeOffset.Now, TimeSpan.FromMilliseconds(300), true);
                client.TrackRequest("My Request", DateTimeOffset.Now,
                    TimeSpan.FromMilliseconds(230), "200", true);
                Task.Delay(1000).Wait();
            }
        }
    }
}
```

尽管上面的示例适用于控制台应用程序，但在任何 .NET 应用程序中都可以使用相同的代码。 如果启用了自动收集遥测的任何其他 TelemetryModules，请务必确保用于初始化这些模块的相同配置也用于 "实时指标" 模块。

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>实时指标流与指标资源管理器、Analytics 有何差异？

| |实时流 | 指标资源管理器和 Analytics |
|---|---|---|
|**延迟**|在一秒内显示数据|在几分钟聚合|
|**无保留期**|当数据在图表上显示时会得到保留，不显示时将被丢弃。|[数据会保留 90 天](./data-retention-privacy.md#how-long-is-the-data-kept)|
|**按需**|仅“实时指标”窗格处于打开状态时才会流式处理数据 |每当安装并启用 SDK 时会发送数据|
|**免费**|实时流数据不收取费用|遵从[定价](./pricing.md)中的标准
|**采样**|传输所有选择的指标和计数器。 对失败和堆栈跟踪进行采样。 |可能会对事件进行[采样](./api-filtering-sampling.md)|
|**控制通道**|筛选器的控制信号会发送到 SDK。 建议确保此通道的安全。|通信为单向通信，即通向门户|

## <a name="select-and-filter-your-metrics"></a>选择和筛选指标

（适用于 ASP.NET、ASP.NET Core 和 Azure Functions (v2)。）

可以通过在门户中针对任何 Application Insights 遥测数据应用任意筛选器来实时监视自定义 KPI。 单击筛选器控件（将鼠标悬停在任何图表上时会显示）。 下图绘制了一个自定义请求计数 KPI，其中的数据已按“URL”和“持续时间”属性进行筛选。 使用“流预览”部分（显示与在任意时间点指定的条件匹配的实时遥测源）验证筛选器。

![筛选请求速率](./media/live-stream/filter-request.png)

可以监视与“计数”不同的值。 可用的选项取决于流的类型，这可能是任何 Application Insights 遥测数据：请求、依赖项、异常、跟踪、事件或指标。 它也可能是自己的[自定义度量值](./api-custom-events-metrics.md#properties)：

![针对请求速率的查询生成器（使用自定义指标）](./media/live-stream/query-builder-request.png)

除了 Application Insights 遥测数据以外，还可以监视任何 Windows 性能计数器：从流选项中选择该类型，并提供性能计数器的名称。

实时指标分别在以下两个点上聚合：在每个服务器本地，并在所有服务器上。 可以通过在相应的下拉列表中选择其他选项来更改默认选项。

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>样本遥测数据：自定义实时诊断事件
默认情况下，事件实时源显示已失败的请求和依赖项调用、异常、事件与跟踪的样本。 单击筛选器图标可以查看在任意时间点应用的条件。

![“筛选器”按钮](./media/live-stream/filter.png)

与使用指标时一样，可以将任意条件指定为任何 Application Insights 遥测类型。 在本示例中，我们将选择特定的请求失败和事件。

![查询生成器](./media/live-stream/query-builder.png)

> [!NOTE]
> 目前，对于基于异常消息的条件，请使用外部异常消息。 在前面的示例中，若要筛选出具有内部异常消息“客户端已断开连接”（追随“<--”分隔符查找）的良性异常， 请使用不包含“读取请求内容时出错”条件的消息。

单击实时源中的某个项可查看其详细信息。 可以通过单击“暂停”、向下滚动或单击某个项来暂停源。 在实时源处于暂停状态时，滚回到顶部后，或者单击收集的项的计数器时，该实时源会恢复。

![屏幕截图显示示例遥测窗口，并在窗口底部显示异常详细信息。](./media/live-stream/sample-telemetry.png)

## <a name="filter-by-server-instance"></a>按服务器实例筛选

如果要监视特定服务器角色实例，则可以按服务器进行筛选。 要进行筛选，请在“服务器”下选择服务器名称。

![采样的实时失败](./media/live-stream/filter-by-server.png)

## <a name="secure-the-control-channel"></a>确保控制通道的安全

> [!NOTE]
> 目前，只能使用基于代码的监视设置经过身份验证的通道，而不能使用无代码置备 attach 对服务器进行身份验证。

在 Live 指标门户中指定的自定义筛选器条件将被发送回 Application Insights SDK 中的 "实时指标" 组件。 筛选器可能包含 customerID 等敏感信息。 可以使用机密 API 密钥以及检测密钥来保护通道的安全。

### <a name="create-an-api-key"></a>创建 API 密钥

![“API 密钥”>“创建 API 密钥”](./media/live-stream/api-key.png)
![“创建 API 密钥”选项卡。依次选择“对 SDK 控制通道进行身份验证”、“生产密钥”](./media/live-stream/create-api-key.png)

### <a name="add-api-key-to-configuration"></a>将 API 密钥添加到配置中

### <a name="aspnet"></a>ASP.NET

在 applicationinsights.config 文件中，将 AuthenticationApiKey 添加到 QuickPulseTelemetryModule：

```XML
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>
```

### <a name="aspnet-core"></a>ASP.NET Core

对于 [ASP.NET Core](./asp-net-core.md) 应用程序，请按照以下说明进行操作。

修改 `ConfigureServices` Startup.cs 文件，如下所示：

添加以下命名空间。

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

然后 `ConfigureServices` 按如下所示修改方法。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // existing code which include services.AddApplicationInsightsTelemetry() to enable Application Insights.
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
}
```

有关配置 ASP.NET Core 应用程序的详细信息，请参阅在 [ASP.NET Core 中配置遥测模块](./asp-net-core.md#configuring-or-removing-default-telemetrymodules)的指南。

### <a name="workerservice"></a>WorkerService

对于 [WorkerService](./worker-service.md) 应用程序，请按照以下说明进行操作。

添加以下命名空间。

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

接下来，在调用前添加以下行 `services.AddApplicationInsightsTelemetryWorkerService` 。

```csharp
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

有关配置 WorkerService 应用程序的详细信息，请参阅有关在 [WorkerServices 中配置遥测模块](./worker-service.md#configuring-or-removing-default-telemetrymodules)的指南。

### <a name="azure-function-apps"></a>Azure 函数应用

对于使用 API 密钥保护通道的 Azure 函数应用 (v2)，可以通过一个环境变量来实现。

从 Application Insights 资源中创建 API 密钥，然后在 Function App 中转到 " **设置" > "配置** "。 选择 " **新建应用程序设置** "，并输入 `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` 和与 API 密钥对应的值。

但是，如果认识并信任所有连接的服务器，则可以尝试使用不包含经过身份验证的通道的自定义筛选器。 可以使用此选项六个月。 每建立一个新的会话或者新服务器联机时，都需要进行这种替代。

![实时指标身份验证选项](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>在筛选条件中输入 CustomerID 等潜在的敏感信息之前，我们强烈建议设置经过身份验证的通道。
>

## <a name="supported-features-table"></a>支持的功能表

| 语言                         | 基本指标       | 性能指标 | 自定义筛选    | 样本遥测数据    | CPU 按进程划分 |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET Framework                   | 支持 (V2.7.2+) | 支持 (V2.7.2+) | 支持 (V2.7.2+) | 支持 (V2.7.2+) | 支持 (V2.7.2+)  |
| .NET Core（目标=.NET Framework）| 支持 (V2.4.1+) | 支持 (V2.4.1+) | 支持 (V2.4.1+) | 支持 (V2.4.1+) | 支持 (V2.4.1+)  |
| .NET Core（目标=.NET Core）     | 支持 (V2.4.1+) | 支持*          | 支持 (V2.4.1+) | 支持 (V2.4.1+) | **不支持**    |
| Azure Functions v2               | 支持           | 支持           | 支持           | 支持           | **不支持**    |
| Java                             | 支持 (V2.0.0+) | 支持 (V2.0.0+) | **不支持**   | **不支持**   | **不支持**    |
| Node.js                          | 支持 (V1.3.0+) | 支持 (V1.3.0+) | **不支持**   | 支持 (V1.3.0+) | **不支持**    |

基本指标包括请求、依赖项和异常率。 性能指标（性能计数器）包括内存和 CPU。 示例遥测显示了有关失败的请求和依赖项、异常、事件和跟踪的详细信息流。

 \* PerfCounters 支持在不针对 .NET Framework 的 .NET Core 版本之间略有不同：

- 在适用于 Windows 的 Azure 应用服务中运行时，支持 PerfCounters 指标。 （AspNetCore SDK 版本 2.4.1 或更高版本）
- 当应用在任何 Windows 计算机（VM、云服务或本地等）上运行时（AspNetCore SDK 版本 2.7.1 或更高版本），支持 PerfCounters，但针对 .NET Core 2.0 或更高版本的应用除外。
- 当应用程序 (Linux、Windows、适用于 Linux 的应用服务的任何位置 )  (SDK 版本2.8.0 或) 更高版本中，但仅适用于面向 .NET Core 2.0 或更高版本的应用时，支持 PerfCounters。

## <a name="troubleshooting"></a>疑难解答

实时指标流使用不同于其他 Application Insights 遥测功能的 IP 地址。 请确保在防火墙中开放[这些 IP 地址](./ip-addresses.md)。 同时，检查的 [传出端口](./ip-addresses.md#outgoing-ports) 是否在服务器的防火墙中打开实时指标流。

## <a name="next-steps"></a>后续步骤

* [使用 Application Insights 监视使用情况](./usage-overview.md)
* [使用诊断搜索](./diagnostic-search.md)
* [探查器](./profiler.md)
* [快照调试器](./snapshot-debugger.md)
