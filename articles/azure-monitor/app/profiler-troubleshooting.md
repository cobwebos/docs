---
title: 排查 Azure Application Insights Profiler 的问题 | Microsoft Docs
description: 本文提供故障排除步骤和信息，帮助开发人员解决在启用或使用 Application Insights Profiler 时遇到的难题。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 404fe07b5e3c1f4322ec683f37b08942abfd6fcc
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358841"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>排查启用或查看 Application Insights Profiler 时遇到的问题

## <a id="troubleshooting"></a>常规故障排除

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>仅当在运行 Profiler 期间对应用程序发出了请求时，才上传配置文件

Azure Application Insights Profiler 每小时会收集两分钟的分析数据。 当你在“配置 Application Insights Profiler”窗格中选择“立即配置文件”按钮时，它也会收集数据。 但是，仅当可将分析数据附加到运行 Profiler 期间所发生的请求时，才会上传分析数据。 

Profiler 将跟踪消息和自定义事件写入到 Application Insights 资源。 可以使用这些事件来查看 Profiler 的运行方式。 如果你认为 Profiler 应该运行并捕获跟踪，但“性能”窗格中并未显示这些信息，则可以检查 Profiler 的运行方式：

1. 搜索由 Profiler 发送到 Application Insights 资源的跟踪消息和自定义事件。 可以使用此搜索字符串查找相关的数据：

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    下图显示了两个从两个 AI 资源中进行搜索的示例： 
    
    * 在左侧，应用程序在 Profiler 运行时不接收请求。 此消息说明，由于没有发生活动，上传操作已取消。 

    * 在右侧，Profiler 已启动，并在检测到它运行期间发生的请求时发送了自定义事件。 如果显示 ServiceProfilerSample 自定义事件，则表示 Profiler 已将一个跟踪附加到请求，你可以从“Application Insights 性能”窗格查看该跟踪。

    如果未显示遥测数据，则 Profiler 未运行。 要进行故障排除，请参阅本文后面的特定应用类型的故障排除部分。  

     ![搜索 Profiler 遥测数据][profiler-search-telemetry]

1. 如果在 Profiler 运行时发出了请求，请确保应用程序中已启用 Profiler 的组件可以处理该请求。 虽然应用程序有时包括多个组件，但只对一部分组件启用了 Profiler。 “配置 Application Insights Profiler”窗格会显示已上传跟踪的组件。

### <a name="other-things-to-check"></a>要检查的其他事项
* 确保应用在 .NET Framework 4.6 上运行。
* 如果 Web 应用是 ASP.NET Core 应用程序，则必须至少运行 ASP.NET Core 2.0。
* 如果要查看的数据的期限超过了好几周，请尝试限制时间筛选器并重试。 七天后将删除跟踪。
* 确保代理或防火墙未阻止对 https://gateway.azureserviceprofiler.net 的访问。

### <a id="double-counting"></a>并行线程的重复计算

在某些情况下，堆栈查看器中的总时间指标大于请求的持续时间。

如果存在与请求关联的两个或更多个线程并且它们正并行运行，则可能会发生这种情况。 在这种情况下，总线程时间就会超过已用时间。 一个线程可能会等待另一个线程完成。 查看器会尝试检测此情况并省略不相关的等待时间。 这样，它会倾向于显示过多信息，而不是省略关键信息。

如果看到跟踪中出现并行线程，请确定哪些线程处于等待状态，以便可以确定请求的关键路径。 通常情况下，快速进入等待状态的线程只是在等待其他线程完成。 请专注于其他线程，忽略等待中线程花费的时间。

### <a name="error-report-in-the-profile-viewer"></a>配置文件查看器中的错误报告
在门户中提交支持票证。 请务必包含错误消息中的相关性 ID。

## <a name="troubleshoot-profiler-on-azure-app-service"></a>排查 Azure 应用服务中的 Profiler 问题
要使 Profiler 正常工作：
* Web 应用服务计划必须属于“基本”层或更高层。
* Web 应用必须已启用 Application Insights。
* 必须在 Web 应用的 **APPINSIGHTS_INSTRUMENTATIONKEY** 应用设置中配置 Application Insights SDK 所用的相同检测密钥。
* 必须定义 Web 应用的 **APPINSIGHTS_PROFILERFEATURE_VERSION** 应用设置，并将其设置为 1.0.0。
* Web 应用必须已定义 DiagnosticServices_EXTENSION_VERSION 应用设置并将该值设置为 ~3。
* ApplicationInsightsProfiler3 webjob 必须正在运行。 若要检查 webjob：
   1. 转到 [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/)。
   1. 在“工具”菜单中，选择“WebJobs 仪表板”。  
      “WebJobs”窗格随即打开。 
   
      ![profiler-webjob]   
   
   1. 若要查看 webjob 的详细信息，包括日志，请选择“ApplicationInsightsProfiler2”链接。  
     “连续 WebJob 详细信息”窗格随即打开。

      ![profiler-webjob-log]

如果你不明白 Profiler 为何不能正常工作，可以下载日志并将其发送给我们的团队以获取帮助。 
    
### <a name="manual-installation"></a>手动安装

配置 Profiler 时，将对 Web 应用的设置进行更新。 如果你的环境有此要求，则可以手动应用更新。 例如，应用程序在适用于 PowerApps 的 Web 应用环境中运行。 若要手动应用更新，请执行以下操作：

1. 在“Web 应用控制”窗格中，打开“设置”。

1. 将“.Net Framework 版本”设置为“v4.6”。

1. 将“Always On”设置为“打开”。

1. 添加应用设置“APPINSIGHTS_INSTRUMENTATIONKEY”，将值设置为 SDK 使用的同一检测密钥。

1. 添加 **APPINSIGHTS_PROFILERFEATURE_VERSION** 应用设置，并将值设置为 1.0.0。

1. 添加 DiagnosticServices_EXTENSION_VERSION 应用设置，并将该值设置为 ~3。

### <a name="too-many-active-profiling-sessions"></a>活动分析会话太多

目前，最多可以对同一服务计划中运行的 4 个 Azure Web 应用和部署槽启用 Profiler。 如果在一个应用服务计划中运行的 Web 应用超过四个，则 Profiler 可能会引发 Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException。 Profiler 为每个 Web 应用单独运行，并尝试为每个应用启动 Windows 事件跟踪 (ETW) 会话。 但是，可以同时处于活动状态的 ETW 会话数量有限。 如果 Profiler webjob 报告活动探查会话太多，请将一些 Web 应用移到另一服务计划。

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

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>排查 Profiler 和 Azure 诊断的问题

若要查看 Azure 诊断是否正确配置了 Profiler，请执行以下三项操作： 
1. 首先，检查部署的 Azure 诊断配置的内容是否符合预期。 

1. 其次，确保 Azure 诊断在 Profiler 命令行上传递正确的 iKey。 

1. 最后，检查 Profiler 日志文件，以查看 Profiler 是否已运行但返回了错误。 

若要检查用于配置 Azure 诊断的设置：

1. 登录到虚拟机 (VM)，然后打开位于此位置的日志文件： 

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.logs  
    ```

1. 在该文件中，可以搜索字符串“WadCfg”，找到传递给 VM 用于配置 Azure 诊断的设置。 可以检查 Profiler 接收器使用的 iKey 是否正确。

1. 检查用于启动 Profiler 的命令行。 用于启动 Profiler 的参数位于以下文件中：

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. 确保 Profiler 命令行中的 iKey 是正确的。 

1. 使用上述 config.json 文件中的路径检查 Profiler 日志文件。 它将显示表示 Profiler 正在使用的设置的调试信息。 此外，还将显示来自 Profiler 的状态和错误消息。  

    如果当应用程序接收请求时 Profiler 正在运行，则会显示以下消息：“检测到来自 iKey 的活动”。 

    上传跟踪时，会显示以下消息：“开始上传跟踪”。 


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








