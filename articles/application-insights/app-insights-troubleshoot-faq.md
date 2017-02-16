---
title: "有关 Azure Application Insights 的疑难解答和问题 | Microsoft Docs"
description: "Azure Application Insights 中有些内容不明白或不正常工作？ 试试这里。"
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 75b651bd3e77ac19e22dcc3442870469fe2aaca1
ms.openlocfilehash: 22d6222b8fd27eeeb562af1ba50164031c885f25


---
# <a name="questions---application-insights-for-aspnet"></a>问题 - 用于 ASP.NET 的 Application Insights
## <a name="configuration-problems"></a>配置问题
*我在设置以下内容时遇到问题：*

* [.NET 应用](app-insights-asp-net-troubleshoot-no-data.md)
* [监视已经在运行的应用](app-insights-monitor-performance-live-website-now.md#troubleshooting)
* [Azure 诊断](app-insights-azure-diagnostics.md)
* [Java Web 应用](app-insights-java-troubleshoot.md)
* [其他平台](app-insights-platforms.md)

*我无法从服务器获取任何数据*

* [设置防火墙异常](app-insights-ip-addresses.md)
* [设置 ASP.NET 服务器](app-insights-monitor-performance-live-website-now.md)
* [设置 Java 服务器](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>是否可以将 Application Insights 用于...？
[更多平台][platforms]

## <a name="is-it-free"></a>是否免费？

是的，对于实验用途。 在默认定价计划中，应用程序可以发送一些每月免费数据量。 免费的限额足以满足开发和为少量用户发布应用的需求。 可以设置上限，以防止所使用的数据量超出指定数据量。

需要使用企业计划来获取某些功能，如“连续导出”。 这会产生每日的费用。

[阅读定价计划](https://azure.microsoft.com/pricing/details/application-insights/)。


## <a name="a-nameq14awhat-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Application Insights 在我的项目中修改哪些内容？
详细信息取决于项目类型。 对于 Web 应用程序：

* 将这些文件添加到项目：
  
  * ApplicationInsights.config。
  * ai.js
* 安装以下 NuGet 包：
  
  * *Application Insights API* - 核心 API
  * *适用于 Web 应用程序 的 Application Insights API* - 用于从服务器发送遥测
  * *适用于 JavaScript 应用程序的 Application Insights API* - 用于从客户端发送遥测
    
    这些包包括以下程序集：
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* 将项目插入：
  
  * Web.config
  * packages.config
* （仅限新项目 - 如果[将 Application Insights 添加到现有项目][start]，则必须手动执行此操作。）在客户端和服务器代码中插入代码片段，以使用 Application Insights 资源 ID 初始化它们。 例如，在 MVC 应用中，代码插入到主页 Views/Shared/_Layout.cshtml 中

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>如何从较早的 SDK 版本升级？
请参阅[发行说明](app-insights-release-notes.md)了解对应于应用程序类型的 SDK。

## <a name="a-nameupdateahow-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>如何更改项目向哪个 Azure 资源发送数据？
在解决方案资源管理器中，右键单击 `ApplicationInsights.config` 并选择“更新 Application Insights”。 可在 Azure 中将数据发送到现有或新资源。 更新向导更改 ApplicationInsights.config 中的检测密钥，该密钥确定服务器 SDK 将数据发送到何处。 除非取消选中“更新全部”，否则它还将在网页中出现密钥的位置更改密钥。

#### <a name="a-namedataahow-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>数据在门户中保留多长时间？ 是否安全？
请参阅[数据保留和隐私][data]。

## <a name="logging"></a>日志记录
#### <a name="a-namepostahow-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>如何在诊断搜索中查看 POST 数据？
我们不自动记录 POST 数据，但你可以使用 TrackTrace 调用：将数据放置在消息参数中。 相比对字符串属性的限制，这具有更长的大小限制，尽管你无法对其进行筛选。

## <a name="security"></a>“安全”
#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>我的数据在门户中是否安全？ 保留多长时间？
请参阅[数据保留和隐私][data]。

## <a name="a-nameq17a-have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> 我是否已在 Application Insights 中启用所有内容？
| 你应看到 | 如何获取 | 为何需要它 |
| --- | --- | --- |
| 可用性图表 |[Web 测试](app-insights-monitor-web-app-availability.md) |知道 Web 应用已启动 |
| 服务器应用性能：响应时间、... |[将 Application Insights 添加到项目](app-insights-asp-net.md)或[在服务器上安装 AI 状态监视器](app-insights-monitor-performance-live-website-now.md)（或编写你自己的代码以[跟踪依赖项](app-insights-api-custom-events-metrics.md#track-dependency)） |检测性能问题 |
| 依赖项遥测 |[在服务器上安装 AI 状态监视器](app-insights-monitor-performance-live-website-now.md) |诊断数据库或其他外部组件问题 |
| 获取异常的堆栈跟踪 |[在代码中插入 TrackException 调用](app-insights-search-diagnostic-logs.md#exceptions)（但有些会自动报告） |检测和诊断异常 |
| 搜索日志跟踪 |[添加日志记录适配器](app-insights-search-diagnostic-logs.md) |诊断异常、性能问题 |
| 客户端使用基础知识：页面视图、会话、... |[网页中的 JavaScript 初始值设定项](app-insights-javascript.md) |使用情况分析 |
| 客户端自定义指标 |[在网页中跟踪调用](app-insights-api-custom-events-metrics.md) |增强用户体验 |
| 服务器自定义指标 |[在服务器中跟踪调用](app-insights-api-custom-events-metrics.md) |商业智能 |

## <a name="automation"></a>自动化
可[编写 PowerShell 脚本](app-insights-powershell.md)以创建和更新 Application Insights 资源。

## <a name="more-answers"></a>更多答案
* [Application Insights 论坛](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md



<!--HONumber=Dec16_HO1-->


