---
title: "Azure Application Insights 中具有自定义指标和诊断的实时指标流 | Microsoft Docs"
description: "通过自定义指标实时监视 Web 应用，使用实时失败、跟踪和事件源以诊断问题。"
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 68820f9c018b1076bae8dc0195906d0f34590748
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017

---

# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>实时指标流：以 1 秒的延迟进行监视和诊断 

通过使用 [Application Insights](app-insights-overview.md) 中的实时指标流探测实时和生产时的 Web 应用程序的信号。 选择并筛选指标和性能计数器进行实时监视，且服务不会受到任何干扰。 从失败请求和异常的样本中检查堆栈跟踪。 实时指标流与[探查器](app-insights-profiler.md)、[快照调试器](app-insights-snapshot-debugger.md)和[性能测试](app-insights-monitor-web-app-availability.md#performance-tests)一同为实时网站提供了功能强大且非入侵性的诊断工具。

使用实时指标流可实现以下操作：

* 通过监视性能和故障计数，在修补程序发布时对其进行验证。
* 监视负载测试的效果，实时诊断问题。 
* 通过选择和筛选想要监视的指标，重点处理特定的测试会话或筛选出已知问题。
* 发生异常时获取异常跟踪。
* 试用筛选器，查找最相关的 KPI。
* 实时监视任何 Windows 性能计数器。
* 轻松识别有问题的服务器，并筛选出只与该服务器相关的所有 KPI/实时源。

[![实时指标流视频](./media/app-insights-live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

实时指标流当前在本地或云端运行的 ASP.NET 应用中可用。 

## <a name="get-started"></a>入门

1. 如果尚未在 ASP.NET Web 应用或 [Windows Server 应用](app-insights-windows-services.md)中[安装 Application Insights](app-insights-asp-net.md)，请立即进行安装。 
2. 更新到最新版本的 Application Insights 包。 在 Visual Studio 中右键单击项目，然后选择“管理 NuGet 包”。 打开“更新”选项卡，选中“包括预先发行版”，然后选择所有的 Microsoft.ApplicationInsights.* 包。

    重新部署应用。

3. 在 [Azure 门户](https://portal.azure.com)中，打开应用的 Application Insights 资源，然后打开实时流。

4. 如果可能在筛选器中使用客户名称等敏感数据，请[确保控制通道的安全](#secure-the-control-channel)。


![在“概览”边栏选项卡中单击“实时流”](./media/app-insights-live-stream/live-stream-2.png)

### <a name="no-data-check-your-server-firewall"></a>没有数据？ 请检查服务器的防火墙

请检查[实时指标流的传出端口](app-insights-ip-addresses.md#outgoing-ports)是否在服务器的防火墙中为打开状态。 


## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>实时指标流与指标资源管理器、Analytics 有何差异？

| |实时流 | 指标资源管理器和 Analytics |
|---|---|---|
|延迟|在一秒内显示数据|在几分钟聚合|
|无保留期|当数据在图表上显示时会得到保留，不显示时将被丢弃。|[数据会保留 90 天](app-insights-data-retention-privacy.md#how-long-is-the-data-kept)|
|按需|打开实时指标时会流式处理数据|每当安装并启用 SDK 时会发送数据|
|免费|实时流数据不收取费用|遵从[定价](app-insights-pricing.md)中的标准
|采样|传输所有选择的指标和计数器。 对失败和堆栈跟踪进行采样。 不应用 TelemetryProcessors。|可能会对事件进行[采样](app-insights-api-filtering-sampling.md)|
|控制通道|筛选器的控制信号会发送到 SDK。 建议[确保此通道的安全](#secure-channel)。|通信为单向通信，即通向门户|


## <a name="select-and-filter-your-metrics"></a>选择和筛选指标

（拥有最新版本 SDK 的经典 ASP.NET 应用上可用。）

可以通过在门户中针对任何 Application Insights 遥测数据应用任意筛选器来实时监视自定义 KPI。 单击筛选器控件（将鼠标悬停在任何图表上时会显示）。 下图绘制了一个自定义请求计数 KPI，其中的数据已按“URL”和“持续时间”属性进行筛选。 使用“流预览”部分（显示与在任意时间点指定的条件匹配的实时遥测源）验证筛选器。 

![自定义请求 KPI](./media/app-insights-live-stream/live-stream-filteredMetric.png)

可以监视与“计数”不同的值。 可用的选项取决于流的类型，这可能是任何 Application Insights 遥测数据：请求、依赖项、异常、跟踪、事件或指标。 它也可能是你自己的[自定义度量值](app-insights-api-custom-events-metrics.md#properties)：

![值选项](./media/app-insights-live-stream/live-stream-valueoptions.png)

除了 Application Insights 遥测数据以外，还可以监视任何 Windows 性能计数器：从流选项中选择该类型，然后提供性能计数器的名称。

实时指标分别在以下两个点上聚合：在每个服务器本地，然后在所有服务器上。 可以通过在相应的下拉列表中选择其他选项来更改默认选项。

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>样本遥测数据：自定义实时诊断事件
默认情况下，事件实时源显示已失败的请求和依赖项调用、异常、事件与跟踪的样本。 单击筛选器图标可以查看在任意时间点应用的条件。 

![默认的实时源](./media/app-insights-live-stream/live-stream-eventsdefault.png)

与使用指标时一样，可以将任意条件指定为任何 Application Insights 遥测类型。 在本示例中，我们将选择特定的请求失败、跟踪和事件。 另外，我们还将选择所有异常和依赖项失败。

![自定义实时源](./media/app-insights-live-stream/live-stream-events.png)

注意：目前，对于基于异常消息的条件，请使用外部异常消息。 在前面的示例中，若要筛选出具有内部异常消息“客户端已断开连接”（追随“<--”分隔符查找）的良性异常， 请使用不包含“读取请求内容时出错”条件的消息。

单击实时源中的某个项可查看其详细信息。 可以通过单击“暂停”、向下滚动或单击某个项来暂停源。 在实时源处于暂停状态时，滚回到顶部后，或者单击收集的项的计数器时，该实时源将会恢复。

![采样的实时失败](./media/app-insights-live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>按服务器实例筛选

如果要监视特定服务器角色实例，则可以按服务器进行筛选。

![采样的实时失败](./media/app-insights-live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>SDK 要求
自定义实时指标流适用于 2.4.0-beta2 或更高版本的 [Application Insights SDK for Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)。 请记得从 NuGet 包管理器中选择“包括预发行版”选项。

## <a name="secure-the-control-channel"></a>确保控制通道的安全
指定的自定义筛选器条件将发回到 Application Insights SDK 中的“实时指标”组件。 筛选器可能包含 customerID 等敏感信息。 可以使用机密 API 密钥以及检测密钥来保护通道的安全。
### <a name="create-an-api-key"></a>创建 API 密钥

![创建 API 密钥](./media/app-insights-live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>将 API 密钥添加到配置中
在 applicationinsights.config 文件中，将 AuthenticationApiKey 添加到 QuickPulseTelemetryModule：
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add> 

```
或者在代码中的 QuickPulseTelemetryModule 内设置该密钥：

``` C#

    module.AuthenticationApiKey = "YOUR-API-KEY-HERE";

```

但是，如果认识并信任所有连接的服务器，则可以尝试使用不包含经过身份验证的通道的自定义筛选器。 可以使用此选项六个月。 每建立一个新的会话或者新服务器联机时，都需要进行这种替代。

![实时指标身份验证选项](./media/app-insights-live-stream/live-stream-auth.png)

>[!NOTE]
>在筛选条件中输入 CustomerID 等潜在的敏感信息之前，我们强烈建议设置经过身份验证的通道。
>

## <a name="generating-a-performance-test-load"></a>生成性能测试负载

若要监视负载增大的效果，请使用“性能测试”边栏选项卡。 可模拟多个用户同时发出的请求。 可运行单一 URL 的“手动测试”（ping 测试），或运行上传的[多步骤 Web 性能测试](app-insights-monitor-web-app-availability.md#multi-step-web-tests)（方式与可用性测试相同）。

> [!TIP]
> 创建性能测试后，请在不同窗口中打开测试和“实时流”边栏选项卡。 可以看到排队的性能测试的启动时间，同时监视实时流。
>


## <a name="troubleshooting"></a>故障排除

没有数据？ 如果应用程序位于受保护的网络中：实时指标流使用不同于其他 Application Insights 遥测功能的 IP 地址。 请确保在防火墙中开放[这些 IP 地址](app-insights-ip-addresses.md)。



## <a name="next-steps"></a>后续步骤
* [使用 Application Insights 监视使用情况](app-insights-web-track-usage.md)
* [使用诊断搜索](app-insights-diagnostic-search.md)
* [探查器](app-insights-profiler.md)
* [快照调试器](app-insights-snapshot-debugger.md)

