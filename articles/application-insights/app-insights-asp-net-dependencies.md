---
title: 在 Azure Application Insights 中跟踪依赖项 | Microsoft Docs
description: 通过 Application Insights 分析本地或 Microsoft Azure Web 应用程序的使用情况、可用性和性能。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: mbullwin
ms.openlocfilehash: f2ebd2a021d3803b6e3f7d805b9253d181cb16c3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293633"
---
# <a name="set-up-application-insights-dependency-tracking"></a>设置 Application Insights：依赖项跟踪
*依赖项*是应用调用的外部组件。 它通常是使用 HTTP、数据库或文件系统调用的服务。 [Application Insights](app-insights-overview.md) 可以度量应用程序等待依赖项的时长以及依赖项调用失败的频率。 可以调查特定的调用，并将其与请求和异常相关联。

![示例图表](./media/app-insights-asp-net-dependencies/10-intro.png)

全新依赖项监视器当前报告对这些类型的依赖项的调用：

* 服务器
  * SQL 数据库
  * 使用基于 HTTP 绑定的 ASP.NET Web 和 WCF 服务
  * 本地或远程 HTTP 调用
  * Azure Cosmos DB、表、Blob 存储和队列
* 网页
  * AJAX 调用

通过围绕所选方法使用[字节代码检测](https://msdn.microsoft.com/library/z9z62c29.aspx)监视工作。 性能开销最低。

还可以编写自己的 SDK 调用，以便使用 [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency) 监视客户端和服务器代码中的其他依赖项。

## <a name="set-up-dependency-monitoring"></a>设置依赖项监视
[Application Insights SDK](app-insights-asp-net.md) 会自动收集部分依赖项信息。 若要获取完整数据，请为主机服务器安装相应的代理。

| 平台 | 安装 |
| --- | --- |
| IIS 服务器 |[在服务器上安装状态监视器](app-insights-monitor-performance-live-website-now.md)或者[将应用程序升级到 .NET framework 4.6 或更高版本](http://go.microsoft.com/fwlink/?LinkId=528259)，并在应用中安装 [Application Insights SDK](app-insights-asp-net.md)。 |
| Azure Web 应用 |在 Web 应用控件面板中[打开“Application Insights”边栏选项卡](app-insights-azure-web-apps.md)，并在出现提示时选择“安装”。 |
| Azure 云服务 |[使用启动任务](app-insights-cloudservices.md)或[安装 .NET framework 4.6+](../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="where-to-find-dependency-data"></a>在何处查找依赖项数据
* [应用程序地图](#application-map)直观显示应用与相邻组件之间的依赖关系。
* [性能、浏览器和失败边栏选项卡](#performance-and-blades)显示服务器依赖性数据。
* [浏览器边栏选项卡](#ajax-calls)显示从用户浏览器发出的 AJAX 调用。
* [单击缓慢或失败的请求](#diagnose-slow-requests)可以检查其依赖性调用。
* [Analytics](#analytics) 可用于查询依赖性数据。

## <a name="application-map"></a>应用程序地图
应用程序地图发挥视觉辅助作用，发现应用程序组件之间的依赖项。 这些信息是基于应用发送的遥测数据自动生成的。 此示例显示了从浏览器脚本发出的 AJAX 调用，以及从服务器应用向两个外部服务发出的 REST 调用。

![应用程序地图](./media/app-insights-asp-net-dependencies/08.png)

* **在框中导航**到相关依赖项和其他图表。
* **将地图固定**到[仪表板](app-insights-dashboards.md)，在这里它将完全发挥效用。

[了解详细信息](app-insights-app-map.md)。

## <a name="performance-and-failure-blades"></a>性能和失败边栏选项卡
性能边栏选项卡显示服务器应用发出的依赖项调用的持续时间。 其中提供了一个摘要图表，以及一个按调用分段的表。

![性能边栏选项卡中的依赖项图表](./media/app-insights-asp-net-dependencies/dependencies-in-performance-blade.png)

单击摘要图表或表项可以搜索这些调用的原始匹配项。

![依赖项调用实例](./media/app-insights-asp-net-dependencies/dependency-call-instance.png)

“失败计数”显示在“失败”边栏选项卡上。 失败是指不在 200-399 范围内或者未知的返回代码。

> [!NOTE]
> **全部失败？** - 这可能表示只获取了部分依赖项数据。 需要[设置适用于平台的依赖项监视](#set-up-dependency-monitoring)。
>
>

## <a name="ajax-calls"></a>AJAX 调用
“浏览器”边栏选项卡显示[网页中的 JavaScript](app-insights-javascript.md) 发出的 AJAX 调用的持续时间和失败率。 这些信息显示为依赖项。

## <a name="diagnosis"></a>诊断慢速请求
每个请求事件是应用处理请求时跟踪到的依赖项调用、异常和其他事件相关联。 因此，如果某些请求的性能不佳，可以判断其原因是否为某个依赖项的响应速度缓慢。

让我们演练一个相关的示例。

### <a name="tracing-from-requests-to-dependencies"></a>从发往依赖项的请求开始跟踪
打开“性能”边栏选项卡，并查看请求网格：

![包含平均值和计数的请求列表](./media/app-insights-asp-net-dependencies/02-reqs.png)

第一个请求花费很长时间。 让我们看看是否可以查明花费时间的部分。

单击该行，查看单独的请求事件：

![请求事件的列表](./media/app-insights-asp-net-dependencies/03-instances.png)

单击任何一个长时间运行的实例以做进一步调查，并向下滚动到与此请求相关的远程依赖项调用：

![查找对远程依赖项的调用，标识异常持续时间](./media/app-insights-asp-net-dependencies/04-dependencies.png)

似乎处理此请求的大部分时间都花在对本地服务的调用上。

选择该行，获取详细信息：

![单击该远程依赖项，标识原因](./media/app-insights-asp-net-dependencies/05-detail.png)

看起来这就是问题所在。 我们已经查明了问题，现在只需调查该调用为何花费了这么长时间。

### <a name="request-timeline"></a>请求时间线
在不同的情况下，依赖项调用的持续时间都不是很长。 但切换到时间线视图后，可以发现内部进程中发生的延迟：

![查找对远程依赖项的调用，标识异常持续时间](./media/app-insights-asp-net-dependencies/04-1.png)

似乎在第一次依赖项调用后经过了一个较长的时间间隔，我们应在代码中调查原因是什么。

### <a name="profile-your-live-site"></a>分析实时站点

不知道时间花到哪去了？ [Application Insights 探查器](app-insights-profiler.md)将跟踪对实时站点的 HTTP 调用，并显示代码中有哪些函数花费了最长的时间。

## <a name="failed-requests"></a>失败的请求
失败的请求还可能与依赖项的失败调用相关联。 同样，我们可以单击相应的项来跟踪问题。

![单击失败的请求图表](./media/app-insights-asp-net-dependencies/06-fail.png)

单击某个失败的请求，并查看其关联的事件。

![单击请求类型、单击实例以访问同一实例的不同视图、单击它以获取异常详细信息。](./media/app-insights-asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>分析
可以跟踪 [Log Analytics 查询语言](https://docs.loganalytics.io/)中的依赖项。 下面是一些示例。

* 查找所有失败的依赖项调用：

```

    dependencies | where success != "True" | take 10
```

* 查找 AJAX 调用：

```

    dependencies | where client_Type == "Browser" | take 10
```

* 查找与请求关联的依赖项调用：

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* 查找与页面视图关联的 AJAX 调用：

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```



## <a name="custom-dependency-tracking"></a>自定义依赖项跟踪
标准依赖项跟踪模块自动发现内部依赖项，例如数据库和 REST API。 但可能希望使用相同方式处理某些其他组件。

可以通过标准模块所使用的同一 [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency)，编写可发送依赖项信息的代码。

例如，如果使用未自行编写的程序集生成代码，可以对其所有调用进行计时，以了解它对响应时间所做的贡献。 若要使此数据显示在 Application Insights 中的依赖项图表中，请使用 `TrackDependency` 发送它。

```csharp

            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
                // The call above has been made obsolete in the latest SDK. The updated call follows this format:
                // TrackDependency (string dependencyTypeName, string dependencyName, string data, DateTimeOffset startTime, TimeSpan duration, bool success);
            }
```

如果想要关闭标准依赖项跟踪模块，请在 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 中删除对 DependencyTrackingTelemetryModule 的引用。

## <a name="troubleshooting"></a>故障排除
*依赖项成功标志使用显示 true 或 false。*

*SQL 查询未完整显示。*

请参阅下表，并确保你选择了正确的配置以启用应用程序的依赖项监视。

| 平台 | 安装 |
| --- | --- |
| IIS 服务器 |[在服务器上安装状态监视器](app-insights-monitor-performance-live-website-now.md)， 或者[将应用程序升级到 .NET framework 4.6 或更高版本](http://go.microsoft.com/fwlink/?LinkId=528259)，并在应用中安装 [Application Insights SDK](app-insights-asp-net.md)。 |
| Azure Web 应用 |在 Web 应用控件面板中[打开“Application Insights”边栏选项卡](app-insights-azure-web-apps.md)，并在出现提示时选择“安装”。 |
| Azure 云服务 |[使用启动任务](app-insights-cloudservices.md)或[安装 .NET framework 4.6+](../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>后续步骤
* [异常](app-insights-asp-net-exceptions.md)
* [用户和页面数据](app-insights-javascript.md)
* [可用性](app-insights-monitor-web-app-availability.md)
