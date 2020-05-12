---
title: 什么是 Azure Application Insights？ | Microsoft Docs
description: 应用程序性能管理和实时 Web 应用程序的使用情况跟踪。  检测、会审和诊断问题，了解用户使用应用的方式。
ms.topic: overview
ms.date: 06/03/2019
ms.custom: mvc
ms.openlocfilehash: 0ffa9e38d0ffbca41e4f4a8f3d27015ebe4b8bae
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891743"
---
# <a name="what-is-application-insights"></a>什么是 Application Insights？
Application Insights 是 [Azure Monitor](../overview.md) 的一项功能，是面向开发人员和 DevOps 专业人员的可扩展应用程序性能管理 (APM) 服务。 使用它可以监视实时应用程序。 它将自动检测性能异常，并且包含了强大的分析工具来帮助诊断问题，了解用户在应用中实际执行了哪些操作。  它旨在帮助持续提高性能与可用性。 它适用于本地云、混合云或任何公有云中托管的各种平台（包括 .NET、Node.js、Java 和 Python）上的应用。 它与 DevOps 进程集成，并且具有与不同开发工具的连接点。 可以通过与 Visual Studio App Center 集成来监视和分析移动应用的遥测数据。

## <a name="how-does-application-insights-work"></a>Application Insights 的工作原理是什么？
你在应用程序中安装了小型检测包 (SDK)，或者以无代码方式启用了 Application Insights（如果[受支持](../../azure-monitor/app/platforms.md)）。 检测技术会监视应用，并使用我们称为“检测密钥”的唯一 GUID 将遥测数据定向到 Azure Application Insights 资源。

不仅可以检测 Web 服务应用程序，还可以检测所有后台组件以及 Web 页面中的 JavaScript。 应用程序及其组件可以在任何地方运行 - 不必在 Azure 中托管。

![应用中的 Application Insights 检测将遥测发送到 Application Insights 资源。](./media/app-insights-overview/diagram.png)

此外，还可以从主机环境（如性能计数器、Azure 诊断或 Docker 日志）中拉取遥测。 还可以对定期向 Web 服务发送综合请求的 Web 测试进行设置。

所有这些遥测流都将集成到 Azure Monitor 中。 在 Azure 门户中，可以对原始数据应用强大的分析和搜索工具。

### <a name="whats-the-overhead"></a>什么是开销？
应用性能受到的影响非常小。 跟踪调用是非阻塞式的，以批量方式进行并在单独的线程中发送。

## <a name="what-does-application-insights-monitor"></a>Application Insights 监视器的工作原理是什么？

Application Insights 主要面向开发团队，旨在帮助用户了解应用的运行性能和使用方式。 监视：

* **请求率、响应时间和失败率** - 了解最受欢迎的页面、时段以及用户的位置。 查看哪些页面效果最好。 当有较多请求时，如果响应时间长且失败率高，则可能存在资源问题。 
* **依赖项速率、响应时间和失败率** - 了解外部服务是否正拖慢速度。
* 异常  - 分析聚合的统计信息，或选择特定实例并钻取堆栈跟踪和相关请求。 报告服务器和浏览器异常。
* **页面查看次数和负载性能** - 由用户的浏览器报告。
* **AJAX 调用**（从网页） - 速率、响应时间和失败率。
* **用户和会话计数**。
* Windows 或 Linux 服务器计算机中的**性能计数器**，例如 CPU、内存和网络使用情况。 
* Docker 或 Azure 中的**主机诊断**。 
* 应用中的**诊断跟踪日志**- 可以将跟踪事件与请求相关联。
* 在客户端或服务器代码中自行编写的**自定义事件和指标**，用于跟踪业务事件（例如销售的商品或赢得的游戏）。

## <a name="where-do-i-see-my-telemetry"></a>在哪里可以查看我的遥测数据？

可通过许多方式浏览数据。 请查看以下文章：

|  |  |
| --- | --- |
| [**智能检测和手动警报**](../../azure-monitor/app/proactive-diagnostics.md)<br/>设置自动警报，使其适应应用的正常遥测模式并在出现异常情况时触发。 还可以在自定义或标准指标的特定级别上[设置警报](../../azure-monitor/app/alerts.md)。 |![警报示例](./media/app-insights-overview/alerts-tn.png) |
| [**应用程序映射**](../../azure-monitor/app/app-map.md)<br/>探索应用的组件，包含关键指标和警报。 |![应用程序映射](./media/app-insights-overview/appmap-tn.png)  |
| [**探查器**](../../azure-monitor/app/profiler.md)<br/>检查抽样请求的执行配置文件。 |![探查器](./media/app-insights-overview/profiler.png) |
| [**使用情况分析**](../../azure-monitor/app/usage-overview.md)<br/>分析用户细分和保留情况。|![留存情况工具](./media/app-insights-overview/retention.png) |
| [**实例数据的诊断搜索**](../../azure-monitor/app/diagnostic-search.md)<br/>搜索和筛选事件，例如请求、异常、依赖项调用、日志跟踪和页面视图。  |![搜索遥测](./media/app-insights-overview/search-tn.png) |
| [**聚合数据的指标资源管理器**](../../azure-monitor/platform/metrics-charts.md)<br/>浏览、筛选和细分聚合的数据，例如请求率、故障率和异常率；响应时间、页面加载时间。 |![指标](./media/app-insights-overview/metrics-tn.png) |
| [**仪表板**](../../azure-monitor/app/overview-dashboard.md)<br/>混合使用来自多个资源的数据并与他人共享。 对于多组件应用程序和在团队聊天室中连续显示很有用。 |![仪表板示例](./media/app-insights-overview/dashboard-tn.png) |
| [**实时指标流**](../../azure-monitor/app/live-stream.md)<br/>部署新的生成时，观看这些准实时性能指示器，确保一切按预期工作。 |![实时指标示例](./media/app-insights-overview/live-metrics-tn.png) |
| [**分析**](../../azure-monitor/app/analytics.md)<br/>使用此功能强大的查询语言，回答有关应用的性能和使用情况的疑难问题。 |![分析示例](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](../../azure-monitor/app/visual-studio.md)<br/>查看代码中的性能数据。 从堆栈跟踪转到代码。|![Visual Studio](./media/app-insights-overview/visual-studio-tn.png) |
| [**快照调试器**](../../azure-monitor/app/snapshot-debugger.md)<br/>使用参数值调试从实时操作采样的快照。|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](../../azure-monitor/app/export-power-bi.md )<br/>将使用指标与其他商业智能集成。| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**REST API**](https://dev.applicationinsights.io/)<br/>编写代码以对指标和原始数据运行查询。| ![REST API](./media/app-insights-overview/rest-tn.png) |
| [**连续导出**](../../azure-monitor/app/export-telemetry.md)<br/>原始数据到达后，立即将其批量导出到存储。 |![导出](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>如何使用 Application Insights？

### <a name="monitor"></a>监视
在应用中安装 Application Insights，设置[可用性 Web 测试](../../azure-monitor/app/monitor-web-app-availability.md)，然后：

* 为团队聊天室签出默认[应用程序仪表板](../../azure-monitor/app/overview-dashboard.md)，以便监视负载、响应能力和依赖项性能、页面加载和 AJAX 调用。
* 发现最慢和多数情况下失败的请求。
* 在部署新版本时查看[实时流](../../azure-monitor/app/live-stream.md)，快速掌握任何降级情况。

### <a name="detect-diagnose"></a>检测、诊断
当收到警报，或发现问题时：

* 评估受到影响的的用户的数量。
* 将失败与异常、依赖项调用和跟踪关联起来。
* 检查探查器、快照、堆栈转储和跟踪日志。

### <a name="build-measure-learn"></a>生成、衡量、学习
[测量有效性](../../azure-monitor/app/usage-overview.md)（针对部署的每个新功能）。

* 规划以衡量客户使用新的 UX 或业务功能的方式。
* 将自定义遥测写入代码。
* 使下一个开发周期基于遥测的硬性证据。

## <a name="get-started"></a>入门
Application Insights 是 Microsoft Azure 中托管的众多服务之一，遥测会发送到此处进行分析和展示。 因此在执行任何其他操作之前，需要有 [Microsoft Azure](https://azure.com) 订阅。 可免费注册该订阅，如果选择 Application Insights 的基本[pricing plan](https://azure.microsoft.com/pricing/details/application-insights/)（定价计划），不会产生费用，但如果应用的使用量大幅增加，会产生费用。 如果组织已有订阅，则组织可向其中添加 Microsoft 帐户。

可通过多种方法开始使用。 可从最适合方法开始。 可在以后添加其他方法。

* **运行时：检测服务器上的 Web 应用。** 非常适合已部署的应用程序。 避免任何代码更新。
  * [**在 Azure Web 应用中托管的 ASP.NET 或 ASP.NET Core 应用程序**](../../azure-monitor/app/azure-web-apps.md)
  * [在 Azure VM 或 Azure 虚拟机规模集的 IIS 中托管的 ASP.NET 应用程序](../../azure-monitor/app/azure-vm-vmss-apps.md) 
  * [在 VM 的 IIS 中托管的 ASP.NET 应用程序](../../azure-monitor/app/monitor-performance-live-website-now.md) 
* **开发时：将 Application Insights 添加到代码。** 可以自定义遥测数据收集和发送其他遥测数据。
  * [ASP.NET 应用程序](../../azure-monitor/app/asp-net.md)
  * [ASP.NET Core 应用程序](../../azure-monitor/app/asp-net-core.md)
  * [.NET 控制台应用程序](../../azure-monitor/app/console.md)
  * [Java](../../azure-monitor/app/java-get-started.md)
  * [Node.js](../../azure-monitor/app/nodejs.md)
  * [Python](../../azure-monitor/app/opencensus-python.md)
  * [其他平台](../../azure-monitor/app/platforms.md)
* 针对页面视图、AJAX 和其他客户端遥测 **[检测网页](../../azure-monitor/app/javascript.md)** 。
* 通过与 Visual Studio App Center 集成 **[分析移动应用使用情况](../../azure-monitor/learn/mobile-center-quickstart.md)** 。
* **[可用性测试](../../azure-monitor/app/monitor-web-app-availability.md)** - 从我们的服务器定期对网站执行 ping 操作。

## <a name="next-steps"></a>后续步骤
在运行时开始使用：

* [Azure VM 和 Azure 虚拟机规模集的托管 IIS 应用](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [IIS 服务器](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Web 应用](../../azure-monitor/app/azure-web-apps.md)

在开发时开始使用：

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)


## <a name="support-and-feedback"></a>支持和反馈
* 问题：
  * [故障排除][qna]
  * [MSDN 论坛](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
  * [StackOverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* 用户建议：
  * [UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* 博客：
  * [Application Insights blog](https://azure.microsoft.com/blog/tag/application-insights)（Application Insights 博客）

<!--Link references-->

[android]: ../../azure-monitor/learn/mobile-center-quickstart.md
[azure]: ../../insights-perf-analytics.md
[client]: ../../azure-monitor/app/javascript.md
[desktop]: ../../azure-monitor/app/windows-desktop.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[ios]: ../../azure-monitor/learn/mobile-center-quickstart.md
[java]: ../../azure-monitor/app/java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: ../../azure-monitor/app/platforms.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
