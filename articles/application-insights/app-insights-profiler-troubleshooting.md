---
title: 排查 Azure Application Insights Profiler 的问题 | Microsoft Docs
description: 本页包含故障排除步骤和信息，帮助开发人员解决在启用或使用 Application Insights Profiler 时遇到的难题。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 6013c0a1b404336ad7cca21edafb7adec5c7f7ca
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978836"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>排查启用或查看 Application Insights Profiler 时遇到的问题

## <a id="troubleshooting"></a>常规故障排除

### <a name="profiles-are-only-uploaded-if-there-are-requests-to-your-application-while-the-profiler-is-running"></a>仅当在运行 Profiler 期间对应用程序发出了请求时，才上传配置文件
Application Insights Profiler 每小时收集两分钟的 Profiler 数据，或者在“配置 Application Insights Profiler”页上按下[**立即探查**](app-insights-profiler-settings.md?toc=/azure/azure-monitor/toc.json)按钮时收集数据。 但是，仅当可将探查数据附加到运行 Profiler 期间所发生的请求时，才会上传探查数据。 

Profiler 将跟踪消息和自定义事件写入到 Application Insights 资源。 可以使用这些事件来查看 Profiler 的运行方式。 如果你认为 Profiler 应该运行并捕获跟踪，但“性能”页中并未显示这些信息，则可以检查 Profiler 的运行方式：

1. 搜索由 Profiler 发送到 Application Insights 资源的跟踪消息和自定义事件。 可以使用此搜索字符串查找相关的数据：

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    以下屏幕截图显示了在两个不同的 AI 资源中执行的两项搜索示例。 
    
    * 左侧的示例搜索在当 Profiler 运行时获取请求的应用程序中执行的。 可以看到，由于没有发生活动，消息上传操作已取消。 

    * 在右侧的示例中可以看到，Profiler 已启动，并在检测到它运行期间发生的请求时发送了自定义事件。 如果看到 ServiceProfilerSample 自定义事件，则表示 Profiler 已将一个跟踪附加到请求，你可以从 Application Insights 性能页查看该跟踪。

    * 如果未看到任何遥测数据，则表示 Profiler 未运行。 可能需要阅读本文档后面的与特定应用类型相关的故障排除部分。  

     ![搜索 Profiler 遥测数据][profiler-search-telemetry]

1. 如果在 Profiler 运行的时间段内发出了请求，请确保应用程序中已启用 Profiler 的组件可以处理该请求。 有时，应用程序包括多个组件，但只对一部分（而不是全部）组件启用了 Profiler。 “配置 Application Insights Profiler”页将显示已上传跟踪的组件。

### <a name="net-core-21-bug"></a>.Net Core 2.1 bug
探查器代理中有一个 bug，使其无法上传从 ASP.NET Core 2.1 上运行的应用程序中获取的跟踪。 我们正在努力修复，很快就会解决。 将在 10 月底部署此 bug 的修复程序。

### <a name="other-things-to-check"></a>要检查的其他事项：
* 应用在 .NET Framework 4.6 上运行。
* 如果 Web 应用是 ASP.NET Core 应用程序，则必须至少运行 ASP.NET Core 2.0。
* 如果要查看的数据的期限超过了好几周，请尝试限制时间筛选器并重试。 七天后将删除跟踪。
* 确保代理或防火墙未阻止对 https://gateway.azureserviceprofiler.net 的访问。

### <a id="double-counting"></a>并行线程的重复计算

在某些情况下，堆栈查看器中的总时间指标大于请求的持续时间。

如果存在与请求关联的两个或更多个线程并且它们正并行运行，则可能会发生这种情况。 在这种情况下，总线程时间就会超过已用时间。 一个线程可能会等待另一个线程完成。 查看器会尝试检测这种情况并省略不相关的等待时间，但是，它会倾向于显示过多信息，而不是省略关键信息。

如果看到跟踪中出现并行线程，请确定哪些线程处于等待状态，以便可以确定请求的关键路径。 在大多数情况下，快速进入等待状态的线程只是在等待其他线程完成。 请专注于其他线程，忽略等待中线程花费的时间。

### <a name="error-report-in-the-profile-viewer"></a>配置文件查看器中的错误报告
在门户中提交支持票证。 请务必包含错误消息中的相关性 ID。

## <a name="troubleshooting-profiler-on-app-services"></a>排查应用服务中 Profiler 的问题
### <a name="for-the-profiler-to-work-properly"></a>要使 Profiler 正常工作：
* Web 应用服务计划必须属于“基本”层或更高层。
* Web 应用中必须已安装应用服务的 Application Insights 扩展 (2.6.5)。
* 必须在 Web 应用的 **APPINSIGHTS_INSTRUMENTATIONKEY** 应用设置中配置 Application Insights SDK 所用的相同检测密钥。
* 必须定义 Web 应用的 **APPINSIGHTS_PROFILERFEATURE_VERSION** 应用设置，并将其设置为 1.0.0。
* **ApplicationInsightsProfiler2** Web 作业必须正在运行。 可以转到 [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/)，并打开“工具”菜单下的“WebJobs 仪表板”来检查 Web 作业。 如以下屏幕截图所示，单击“ApplicationInsightsProfiler2”链接可以查看 Webjob 的详细信息，包括日志。

    下面是查看 Webjob 详细信息所要单击的链接： 

    ![profiler-webjob]

    下面是显示详细信息的页面。 如果你不明白 Profiler 为何不能正常工作，可以下载日志并将其发送给我们的团队。
    
    ![profiler-webjob-log]

### <a name="manual-installation"></a>手动安装

配置 Profiler 时，将对 Web 应用的设置进行更新。 如果你的环境有此要求，则可以手动应用更新。 例如，应用程序在适用于 PowerApps 的 Web 应用环境中运行。

1. 在“Web 应用控制”窗格中，打开“设置”。
1. 将“.Net Framework 版本”设置为“v4.6”。
1. 将“Always On”设置为“打开”。
1. 添加应用设置“APPINSIGHTS_INSTRUMENTATIONKEY”，将值设置为 SDK 使用的同一检测密钥。
1. 添加 **APPINSIGHTS_PROFILERFEATURE_VERSION** 应用设置，并将值设置为 1.0.0。
1. 打开“高级工具”。
1. 选择“转到”打开 Kudu 网站。
1. 在 Kudu 网站上，选择“站点扩展”。
1. 从 Azure Web 应用库中安装“Application Insights”。
1. 重新启动 Web 应用。

### <a name="too-many-active-profiling-sessions"></a>活动分析会话太多

目前，最多可以对同一服务计划中运行的 4 个 Azure Web 应用和部署槽启用 Profiler。 如果你拥有的 Web 应用数多于在一个应用服务计划中运行的 Web 应用数，则可能会看到 Profiler 引发 Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException。 Profiler 为每个 Web 应用单独运行，并尝试为每个应用启动 ETW 会话。 但是，可以同时处于活动状态的 ETW 会话数量有限。 如果 Profiler Web 作业报告活动探查会话太多，请将一些 Web 应用移到另一服务计划。

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>部署错误：目录不为空“D:\\home\\site\\wwwroot\\App_Data\\jobs”

如果在已启用 Profiler 的情况下将 Web 应用重新部署到 Web 应用资源，可能会看到如下消息：

*目录不为空“D:\\home\\site\\wwwroot\\App_Data\\jobs”*

如果通过脚本或通过 Azure DevOps 部署管道运行 Web 部署，则会发生此错误。 解决方法是将以下附加部署参数添加到 Web 部署任务：

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

这些参数将删除 Application Insights Profiler 所用的文件夹，并取消阻止重新部署进程。 它们不影响当前运行的 Profiler 实例。

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>如何确定 Application Insights Profiler 是否正在运行？

Profiler 在 Web 应用中以连续 Web 作业的形式运行。 可以在 [Azure 门户](https://portal.azure.com)中打开 Web 应用资源。 在“WebJobs”窗格中，查看 ApplicationInsightsProfiler 的状态。 如果探查器未运行，请打开“日志”获取详细信息。

## <a name="troubleshooting-problems-with-profiler-and-wad"></a>排查 Profiler 和 WAD 的问题

需要检查三个项目来确定 WAD 是否正确配置了 Profiler。 首先，需要检查部署的 WAD 配置内容是否符合预期。 其次，需要检查 WAD 是否在 Profiler 命令行中传递了正确的 iKey。 最后，可以检查 Profiler 日志文件，以查看 Profiler 是否已运行但收到了错误。 

若要检查用于配置 WAD 的设置，需要登录到 VM，并打开以下位置的日志文件： 
```
c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.logs  
```
在该文件中，可以搜索字符串“WadCfg”，找到传递给 VM 用于配置 WAD 的设置。 可以检查 Profiler 接收器使用的 iKey 是否正确。

其次，可以检查用于启动 Profiler 的命令行。 以下文件包含用于启动 Profiler 的参数。
```
D:\ProgramData\ApplicationInsightsProfiler\config.json
```
检查 Profiler 命令行中的 ikey 是否正确。 

最后，使用上述 config.json 文件中的路径检查 Profiler 日志文件。 该文件将显示调试信息，指示 Profiler 正在使用的设置，以及 Profiler 的状态和发出的错误消息。 如果当应用程序接收请求时 Profiler 正在运行，则会显示以下消息：“检测到来自 iKey 的活动”。 上传跟踪时，将显示以下消息：“开始上传跟踪”。 


[profiler-search-telemetry]:./media/app-insights-profiler/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/app-insights-profiler/Profiler-webjob.png
[profiler-webjob-log]:./media/app-insights-profiler/Profiler-webjob-log.png








