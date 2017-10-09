---
title: "使用 Application Insights 监视应用的运行状况和使用情况"
description: "Application Insights 入门。 分析本地或 Microsoft Azure 应用程序的使用情况、可用性和性能。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: a29f1e7b39b7f35073aa5aa6c6bd964ffaa6ffd0
ms.openlocfilehash: 9efe10fa35c6a7c84e0d448bbe53127d16d20870
ms.contentlocale: zh-cn
ms.lasthandoff: 09/21/2017

---
# <a name="monitor-performance-in-web-applications"></a>在 Web 应用程序中监视性能


确保应用程序性能良好，并快速查明任何故障。 [Application Insights][start]将告知任何性能问题和异常，并帮助查找并诊断根本原因。

Application Insights 可监视 Java 和 ASP.NET Web 应用程序和服务、WCF 服务。 可在本地、虚拟机上或作为 Microsoft Azure 网站托管它们。 

在客户端，Application Insights 可从网页和各种设备（包括 iOS、Android 和 Windows 应用商店应用）获取遥测。

>[!Note]
> 在查找 Web 应用程序中性能不佳的页面方面，我们已推出新的体验。 如果你无法访问此体验，可以通过在[预览边栏选项卡](app-insights-previews.md)中配置预览选项来启用它。 在[使用交互式性能调查来查找和修复性能瓶颈](#Find-and-fix-performance-bottlenecks-with-an-interactive-Performance-investigation)中了解此新体验。

## <a name="setup"></a>设置性能监视
如果尚未将 Application Insights 添加到项目（即，如果它没有 ApplicationInsights.config），则选择以下方式之一开始操作：

* [ASP.NET Web 应用](app-insights-asp-net.md)
  * [添加异常监视](app-insights-asp-net-exceptions.md)
  * [添加依赖项监视](app-insights-monitor-performance-live-website-now.md)
* [J2EE Web 应用](app-insights-java-get-started.md)
  * [添加依赖项监视](app-insights-java-agent.md)

## <a name="view"></a>探索性能指标
在 [Azure 门户](https://portal.azure.com)中，浏览到为应用程序设置的 Application Insights 资源。 “概述”边栏选项卡显示基本性能数据：

单击任意图表查看更多详细信息，并查看更长时间段的结果。 例如，单击“请求”磁贴，并选择时间范围：

![通过单击转到更多数据并选择时间范围](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

单击某个图表选择显示哪些指标，或添加新图标并选择其指标：

![单击某个图选择指标](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **取消选中所有指标**查看可用的完整选择。 指标分为多个组；选择某个组的任意成员时，仅显示该组的其他成员。

## <a name="metrics"></a>这一切意味着什么？ 性能磁贴和报表
有多个性能指标可供获取。 让我们先从默认在应用程序边栏选项卡上显示的指标开始。

### <a name="requests"></a>请求
指定时间段内收到的 HTTP 请求数。 将此结果与其他报表中的结果比较，查看应用行为如何因负载而异。

HTTP 请求包括对页面、数据和图像的所有 GET 或 POST 请求。

单击该磁贴获取特定 URL 的计数。

### <a name="average-response-time"></a>平均响应时间
测量从 Web 请求进入应用程序到返回响应之间的时间。

这些点显示移动平均值。 如果有大量请求，可能存在一些在图中未出现明显峰值或低谷的情况下偏离平均值的点。

寻找异常峰值。 通常，响应时间的上升应伴随请求数的上升。 如果此上升不相称，则应用可能遇到了资源限制，如 CPU 或它所使用的服务容量。

单击该磁贴可获取特定 URL 的时间。

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>速度最慢的请求
![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

显示哪些请求可能需要性能优化。

### <a name="failed-requests"></a>失败的请求
![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

引发未捕获异常的请求计数。

单击该磁贴查看特定失败的详细信息，并选择个别请求查看器详细信息。 

对于单个检查，仅保留失败的代表性示例。

### <a name="other-metrics"></a>其他指标
要查看可显示的其他指标，请单击某个图，并取消选中所有指标查看完整的可用集。 单击 (i) 查看每个指标的定义。

![取消选中所有指标查看完整集](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)

选择任何指标会禁用无法在同一图表中显示的其他指标。

## <a name="set-alerts"></a>设置警报
若要收到任意指标异常值的电子邮件通知，请添加警报。 可选择将电子邮件发送给帐户管理员或特定电子邮件地址。

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

在其他属性之前设置资源。 如果要设置关于性能或使用情况指标的警报，请不要选择 webtest 资源。

请仔细留意系统要求输入阈值所采用的单位。

*我看不到“添加警报”按钮。* - 这是否是你对其具有只读访问权限的组帐户？ 请咨询帐户管理员。

## <a name="diagnosis"></a>诊断问题
下面是查找和诊断性能问题的一些提示：

* 设置 [Web 测试][availability]，以便在网站出现故障或响应错误或缓慢时得到警报。 
* 将请求计数与其他指标比较，查看故障或响应缓慢是否与负载有关。
* 在代码中[插入和搜索跟踪语句][diagnostic]以帮助查明问题。
* 使用[实时指标流][livestream]监视正在运行的 Web 应用。
* 使用[快照调试器][snapshot]捕获 .Net 应用程序的状态。

>[!Note]
> 我们正在将 Application Insights 性能调查转换为交互式全屏体验。 以下文档首先介绍了新体验，然后回顾了以前的体验，为防止你仍然需要访问以前的体验，以前的体验在整个转换期间保持可用。

## <a name="find-and-fix-performance-bottlenecks-with-an-interactive-full-screen-performance-investigation"></a>使用交互式全屏性能调查来查找和修复性能瓶颈

可以使用新的 Application Insights 交互式性能调查来查找 Web 应用中性能低下的操作。 可以快速选择一个特定的性能低下的操作，并使用[探查器](app-insights-profiler.md)深入到代码中来查明导致操作性能低下的根源。 使用新的为选定操作显示的持续时间分布，只需一瞥，便可快速评估客户的体验有多糟糕。 实际上，对于性能低下的每个操作，可以查看有多少用户交互受到影响。 在下面的示例中，我们决定更详细地查看“GET Customers/Details”操作的体验。 在持续时间分布中，可以看到有三个峰值。 最左侧的峰值在 400ms 附近，表示响应体验很棒。 中间的峰值在 1.2s 附近，表示体验普普通通。 最后，在 3.6s 处有另一个小的峰值，它表示第 99 个百分位体验，这可能会导致我们的客户因不满意而离开。 该体验比同一操作的很棒体验慢十倍。 

![“GET Customers/Details”三个持续时间峰值](./media/app-insights-web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

若要更好地了解此操作的用户体验，我们可以选择一个更大的时间范围。 对于其中的操作特别慢的特定时间范围，还可以收缩时间范围。 在下面的示例中，我们从默认的 24 小时时间范围切换到了 7 天时间范围，然后在周二（12 日）与周三（13 日）之间放大到 9:47 到 12:47。 请注意，右侧的持续时间分布和示例数以及探查器跟踪数已更新。

![“GET Customers/Details”在 7 天范围中的三个持续时间峰值和时间窗口](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

为了收缩低性能体验的范围，我们接下来将对介于第 95 个百分位与第 99 个百分位之间的持续时间进行放大。 这些项表示有 4% 的用户交互特别慢。

![“GET Customers/Details”在 7 天范围中的三个持续时间峰值和时间窗口](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

现在，我们可以通过单击“Samples”按钮来查看有代表性的示例，或者通过单击“Profiler traces”按钮来查看有代表性的探查器跟踪。 在此示例中，在该时间范围和所关注的范围持续时间内为“GET Customers/Details”收集了四个跟踪。

有时候，问题不在代码中，而在代码所调用的依赖项中。 可以在性能会审视图中切换到“依赖项”选项卡来调查此类性能低下的依赖项。 请注意，默认情况下，性能视图显示的是平均值趋势，但你实际希望查看的是第 95 个百分位（或第 99 个百分位，如果监视的是非常成熟的服务）。 在下面的示例中，我们聚焦于性能低下的 Azure BLOB 依赖项，我们在该依赖项中调用了 PUT fabrikamaccount。 良好的体验聚集在 40ms 附近，而对同一依赖项的性能低下调用要慢三倍，聚集在 120ms 附近。 它没有进行许多这类调用，因此没有积少成多，未导致相应操作显著变慢。 可以钻取到有代表性的示例和探查器跟踪，就像使用“操作”选项卡可做的那样。

![“GET Customers/Details”在 7 天范围中的三个持续时间峰值和时间窗口](./media/app-insights-web-monitor-performance/SlowDependencies95thTrend.png)

交互式全屏性能调查新增的另一个确实强大的功能是与 Insights 的集成。 Application Insights 能够进行检测并以见解响应回归的形式显示检测结果，同时还可以帮助你识别你决定重点调查的示例集中的常用属性。 查看所有可用洞察信息的最佳方式是切换到一个 30 天时间范围，然后选择“总体”来查看过去一个月的所有操作的洞察信息。

![“GET Customers/Details”在 7 天范围中的三个持续时间峰值和时间窗口](./media/app-insights-web-monitor-performance/Performance30DayOveralllnsights.png)

新的性能会审视图中的 Application Insights 简直可以如大海捞针般帮你查明导致 Web 应用用户体验糟糕的问题。

## <a name="deprecated-find-and-fix-performance-bottlenecks-with-a-narrow-bladed-legacy-performance-investigation"></a>已弃用：使用窄的边栏选项卡式旧式性能调查来查找和修复性能瓶颈

可以使用旧式 Application Insights 边栏选项卡式性能调查来查找 Web 应用中拖慢总体性能的问题。 可以查找拖慢性能的具体页面，并且可以使用[探查器](app-insights-profiler.md)深入到代码中来查明这些问题的根源。 

### <a name="create-a-list-of-slow-performing-pages"></a>创建性能不佳页面的列表 

查找性能问题的第一步是获取响应速度较慢的页面列表。 以下屏幕截图演示如何使用“性能”边栏选项卡获取可能存在问题的页面列表，以做进一步调查。 通过此页面很快就能看出，应用的响应时间在大约下午 6:00 变慢，然后在大约晚上 10:00 再次变慢。 另外还可以看出，获取客户信息/详细信息的操作运行时间很长，其平均响应时间为 507.05 毫秒。 

![Application Insights 交互式性能](./media/app-insights-web-monitor-performance/performance1.png)

### <a name="drill-down-on-specific-pages"></a>根据特定的页面向下钻取

获得应用性能的快照后，可以获取有关性能不佳的特定操作的更多详细信息。 在列表中单击任一操作可查看详细信息，如下所示。 在图表中，可以看到性能是否基于依赖关系。 此外，还可以看到有多少用户遇到了不同的响应时间。 

![Application Insights 操作边栏选项卡](./media/app-insights-web-monitor-performance/performance5.png)

### <a name="drill-down-on-a-specific-time-period"></a>根据特定的时间段向下钻取

确定要调查的时间点之后，甚至可以进一步向下钻取，查找可能导致性能减慢的特定操作。 单击特定的时间点可获取页面的详细信息，如下所示。 在以下示例中，可以看到根据给定时间段列出的操作，以及服务器响应代码和操作持续时间。 此外，还会看到用于打开 TFS 工作项的 URL，以便将此信息发送到开发团队。

![Application Insights 时间切片](./media/app-insights-web-monitor-performance/performance2.png)

### <a name="drill-down-on-a-specific-operation"></a>根据特定的操作向下钻取

确定要调查的时间点之后，甚至可以进一步向下钻取，查找可能导致性能减慢的特定操作。 在列表中单击某个操作可查看该操作的详细信息，如下所示。 在此示例中可以看到该操作失败，Application Insights 已提供应用程序引发的异常的详细信息。 同样，可以轻松从此边栏选项卡创建 TFS 工作项。

![Application Insights 操作边栏选项卡](./media/app-insights-web-monitor-performance/performance3.png)


## <a name="next"></a>后续步骤
[Web 测试][availability] - 使 Web 请求按固定间隔从世界各地发送到应用程序。

[捕获和搜索诊断跟踪][diagnostic] - 插入跟踪调用并筛查结果以查明问题。

[使用情况跟踪][usage] - 查明用户使用应用程序的方式。

[故障排除][qna] - 和问答



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md
[livestream]: app-insights-live-stream.md
[snapshot]: app-insights-snapshot-debugger.md




