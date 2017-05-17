---
title: "Azure Application Insights 中的自定义实时指标和诊断 | Microsoft Docs"
description: "实时监视 Web 应用，使用实时失败、跟踪和事件源解决自定义指标和诊断问题。"
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
ms.date: 05/10/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 86e01cf6cb14334e85da4102610fa7feb66cb543
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---

# <a name="custom-live-metrics-and-events-monitor--diagnose-with-1-second-latency"></a>自定义实时指标和事件：以 1 秒的延迟进行监视和诊断 
实时指标流以近乎实时的延迟（1 秒）显示 [Application Insights](app-insights-overview.md) 指标和事件。 这种即时监视功能有助于减少“平均检测和诊断时间”和符合 SLA。 你可以：
* 实时监视自定义 KPI：在门户中体验筛选器，并分解现有的 Application Insights 遥测数据，以便即时获取最相关的 KPI。 无需更改代码或配置，也不需要部署。 还可以使用你可能想要发送的自定义指标或度量值。
* 实时检测和诊断：查看发生的请求和依赖项错误以及详细的异常跟踪。 筛选出任何已知问题，以专注于实时/新的问题。
* 实时调试：重现问题，并使用自定义筛选器（或使用任何自定义属性）进一步深入到标识重现交互操作的特定会话 ID，实时查看所有相关的遥测数据。 从任一/所有服务器收集信息，为解决问题奠定良好的开端。
* 即时了解资源消耗与负载之间的关系：运行负载测试时实时监视*任何* Windows 性能计数器，或者监视生产流程的运作，避免任何环节受到负面影响。 无需更改配置，也不需要部署。
* 验证要发布的修复程序。 确保对服务所做的更新能够正常运行。 验证已修复的错误是否不再发生。
* 轻松识别有问题的服务器，并筛选出只与该服务器相关的所有 KPI/实时源。

实时指标和事件流数据是免费的：不会计入帐单中。 当你打开门户体验时，将根据需要从服务器流式传输数据。 数据只会在图表上显示时才会得到保留，不显示时将被丢弃。 完整的功能仅适用于 ASP.NET 经典应用程序，.NET Core 应用程序目前只提供一组固定的实时指标和样本。 我们将会提供所有受支持的 SDK，以满足最新实时传送视频流功能的需要。 

在进行任何采样或者应用任何自定义 TelemetryProcessor 之前，我们将从你的应用程序实例收集实时流。 

![实时指标流视频](./media/app-insights-live-stream/youtube.png)[实时指标流视频](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

单击左侧的选项或者“概述”边栏选项卡中的按钮访问实时指标和事件：

![在“概览”边栏选项卡中单击“实时流”](./media/app-insights-live-stream/live-stream-2.png)

## <a name="custom-live-kpi"></a>自定义实时 KPI
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

## <a name="authenticated-channel"></a>经过身份验证的通道
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

## <a name="troubleshooting"></a>故障排除

没有数据？ 如果应用程序位于受保护的网络中：实时指标流使用不同于其他 Application Insights 遥测功能的 IP 地址。 请确保在防火墙中开放[这些 IP 地址](app-insights-ip-addresses.md)。



## <a name="next-steps"></a>后续步骤
* [使用 Application Insights 监视使用情况](app-insights-web-track-usage.md)
* [使用诊断搜索](app-insights-diagnostic-search.md)


