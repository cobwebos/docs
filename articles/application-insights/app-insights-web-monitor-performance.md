---
title: 使用 Application Insights 监视应用的运行状况和使用情况
description: Application Insights 入门。 分析本地或 Microsoft Azure 应用程序的使用情况、可用性和性能。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: sdash
ms.openlocfilehash: 02421492528e44ed6a913443a7793235170d4881
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34159401"
---
# <a name="monitor-performance-in-web-applications"></a>在 Web 应用程序中监视性能


确保应用程序性能良好，并快速查明任何故障。 [Application Insights][start]将告知任何性能问题和异常，并帮助查找并诊断根本原因。

Application Insights 可监视 Java 和 ASP.NET Web 应用程序和服务、WCF 服务。 可在本地、虚拟机上或作为 Microsoft Azure 网站托管它们。 

在客户端，Application Insights 可从网页和各种设备（包括 iOS、Android 和 Windows 应用商店应用）获取遥测。

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

### <a name="requests"></a>Requests
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

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>通过性能调查体验来发现和修复性能瓶颈

可以使用新的性能调查体验来审查 Web 应用中性能低下的操作。 可以快速选择一个特定的性能低下的操作，并使用[探查器](app-insights-profiler.md)深入到代码中来查明导致操作性能低下的根源。 使用新的为选定操作显示的持续时间分布，只需一瞥，便可快速评估客户的体验有多糟糕。 你可以看到每个性能低下的操作影响了多少用户交互。 在下面的示例中，我们决定更详细地查看“GET Customers/Details”操作的体验。 在持续时间分布中，我们可以看到有三个峰值。 最左侧的峰值约为 400 ms，表示响应体验很棒。 中间峰值约为 1.2 s，表示体验一般。 最后一个是 3.6 s，这里出现一个小的峰值，表示 99% 的体验，这可能会导致我们的客户因不满意而离开。 该体验比同一操作的很棒体验慢十倍。 

![“GET Customers/Details”三个持续时间峰值](./media/app-insights-web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

若要更好地了解此操作的用户体验，我们可以选择一个更大的时间范围。 然后，还可以缩小操作特别慢的特定时间范围。 在下面的示例中，我们将时间范围从默认的 24 小时切换到了 7 天，然后在周二（12 日）与周三（13 日）之间放大到 9:47 到 12:47。 右侧的持续时间分布和示例数以及探查器跟踪数均已更新。

![“GET Customers/Details”在 7 天范围中的三个持续时间峰值和时间窗口](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

为了收缩低性能体验的范围，我们接下来将对介于第 95 个百分位与第 99 个百分位之间的持续时间进行放大。 这些表示有 4% 的用户交互特别慢。

![“GET Customers/Details”在 7 天范围中的三个持续时间峰值和时间窗口](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

现在，我们可以通过单击“Samples”按钮来查看有代表性的示例，或者通过单击“Profiler traces”按钮来查看有代表性的探查器跟踪。 在此示例中，在该时间范围和所关注的范围持续时间内，为“GET Customers/Details”收集了四个跟踪。

有时问题不在代码中，而是在代码所调用的依赖项中。 可以在性能会审视图中切换到“依赖项”选项卡来调查此类性能低下的依赖项。 默认情况下，性能视图显示的是趋势平均值，但你实际希望查看的是第 95 个百分位（或第 99 个百分位，如果监控的是成熟服务）。 在下面的示例中，我们聚焦于性能低下的 Azure BLOB 依赖项，我们在该依赖项中调用了 PUT fabrikamaccount。 良好的体验大约为 40 ms，而对同一依赖项的性能低下调用要慢三倍，约为 120 ms。 它没有进行许多这类调用，因此没有积少成多，未导致相应操作显著变慢。 可以钻取到有代表性的示例和探查器跟踪，就像使用“操作”选项卡可做的那样。

![“GET Customers/Details”在 7 天范围中的三个持续时间峰值和时间窗口](./media/app-insights-web-monitor-performance/SlowDependencies95thTrend.png)

性能调查体验显示了你决定关注的样本集的相关见解。 查看所有可用洞察信息的最佳方式是切换到一个 30 天时间范围，然后选择“总体”来查看过去一个月的所有操作的洞察信息。

![“GET Customers/Details”在 7 天范围中的三个持续时间峰值和时间窗口](./media/app-insights-web-monitor-performance/Performance30DayOveralllnsights.png)


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



