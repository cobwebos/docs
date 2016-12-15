---
title: "使用 Application Insights 监视应用的运行状况和使用情况"
description: "Application Insights 入门。 分析本地或 Microsoft Azure 应用程序的使用情况、可用性和性能。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/25/2015
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: d84ab993b1d9489ca9d2edaa1cb9672d9bced899
ms.openlocfilehash: 7dd1289453fe146b446322570fcb1c0df74b4db2


---
# <a name="monitor-performance-in-web-applications"></a>在 Web 应用程序中监视性能


确保应用程序性能良好，并快速查明任何故障。 [Application Insights][启动]将告知任何性能问题和异常，并帮助查找并诊断根本原因。

Application Insights 可监视 Java 和 ASP.NET Web 应用程序和服务、WCF 服务。 可以在本地、在虚拟机上或作为 Microsoft Azure 网站托管它们。 

在客户端，Application Insights 可从网页和各种设备（包括 iOS、Android 和 Windows 应用商店应用）获取遥测。

## <a name="a-namesetupaset-up-performance-monitoring"></a><a name="setup"></a>设置性能监视
如果尚未将 Application Insights 添加到项目（即，如果它没有 ApplicationInsights.config），则选择以下方式之一开始操作：

* [ASP.NET Web 应用](app-insights-asp-net.md)
  * [添加异常监视](app-insights-asp-net-exceptions.md)
  * [添加依赖项监视](app-insights-monitor-performance-live-website-now.md)
* [J2EE Web 应用](app-insights-java-get-started.md)
  * [添加依赖项监视](app-insights-java-agent.md)

## <a name="a-nameviewaexploring-performance-metrics"></a><a name="view"></a>探索性能指标
在 [Azure 门户](https://portal.azure.com)中，浏览到为应用程序设置的 Application Insights 资源。 “概述”边栏选项卡显示基本性能数据：

单击任意图表查看更多详细信息，并查看更长时间段的结果。 例如，单击“请求”磁贴，然后选择时间范围：

![通过单击转到更多数据并选择时间范围](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

单击某个图表选择显示哪些指标，或添加新图标并选择其指标：

![单击某个图选择指标](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **取消选中所有指标**查看可用的完整选择。 指标分为多个组；选择某个组的任意成员时，仅显示该组的其他成员。
> 
> 

## <a name="a-namemetricsawhat-does-it-all-mean-performance-tiles-and-reports"></a><a name="metrics"></a>这一切意味着什么？ 性能磁贴和报表
有多种性能指标可供获取。 让我们先从默认在应用程序边栏选项卡上显示的指标开始。

### <a name="requests"></a>请求
指定时间段内收到的 HTTP 请求数。 将此结果与其他报表中的结果比较，查看应用行为如何因负载而异。

HTTP 请求包括对页面、数据和图像的所有 GET 或 POST 请求。

单击该磁贴获取特定 URL 的计数。

### <a name="average-response-time"></a>平均响应时间
测量从 Web 请求进入应用程序到返回响应之间的时间。

这些点显示移动平均值。 如果有大量请求，可能存在一些在图中未出现明显峰值或低谷的情况下偏离平均值的点。

寻找异常峰值。 通常，响应时间的上升应伴随请求数的上升。 如果此上升不相称，则应用可能遇到了资源限制，如 CPU 或它所使用的服务容量。

单击该磁贴获取特定 URL 的时间。

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
若要查看可显示的其他指标，请单击某个图，然后取消选中所有指标查看完整的可用集。 单击 (i) 查看每个指标的定义。

![取消选中所有指标查看完整集](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)

选择任何指标都会禁用无法在同一图表中显示的其他指标。

## <a name="set-alerts"></a>设置警报
若要收到任意指标异常值的电子邮件通知，请添加警报。 可选择将电子邮件发送给帐户管理员或特定电子邮件地址。

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

在其他属性之前设置资源。 如果要设置关于性能或使用情况指标的警报，请不要选择 webtest 资源。

请仔细留意系统要求输入阈值所采用的单位。

*我看不到“添加警报”按钮。* - 这是否是你拥有只读访问权限的组帐户？ 请咨询帐户管理员。

## <a name="a-namediagnosisadiagnosing-issues"></a><a name="diagnosis"></a>诊断问题
下面是查找和诊断性能问题的一些提示：

* 设置 [Web 测试][可用性]，以在网站出现故障或响应错误或缓慢时得到警报。 
* 将请求计数与其他指标比较，查看故障或响应缓慢是否与负载有关。
* 在代码中[插入搜索跟踪语句][诊断]以帮助查明问题。

## <a name="a-namenextanext-steps"></a><a name="next"></a>后续步骤
[Web 测试][可用性] - 使 Web 请求按固定间隔从世界各地发送到应用程序。

[捕获和搜索诊断跟踪][诊断] - 插入跟踪调用并筛查结果以查明问题。

[使用情况跟踪][使用情况] - 查明用户使用应用程序的方式。

[故障排除][qna] - 和问题与解答

## <a name="video"></a>视频
> [!VIDEO https://channel9.msdn.com/Series/ConnectOn-Demand/222/player]


<!--Link references-->

[可用性]: app-insights-monitor-web-app-availability.md
[诊断]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[启动]: app-insights-overview.md
[使用情况]: app-insights-web-track-usage.md





<!--HONumber=Nov16_HO3-->


