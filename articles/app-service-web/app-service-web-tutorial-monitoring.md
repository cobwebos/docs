---
title: "监视 Web 应用 | Microsoft Docs"
description: "了解如何在 Web 应用上设置监视"
services: App-Service
keywords: 
author: btardif
ms.author: byvinyal
ms.date: 04/04/2017
ms.topic: article
ms.service: app-service-web
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 63bfc6922de224f56186003991f4a51d8f99ed35
ms.lasthandoff: 04/15/2017

---
# <a name="monitor-app-service"></a>监视应用服务
本教程演示如何使用内置平台工具，对托管在应用服务中的应用进行监视和诊断。 

## <a name="in-this-tutorial"></a>在本教程中

1. [进程资源管理器](#explorer)
    - 获取有关应用在应用服务计划实例中运行的详细信息
1. [应用服务指标](#metrics) 
   - 了解如何使用内置图表监视应用
   - 配置图表以满足需求
   - 通过固定自定义图表来创建自定义仪表板
1. [配置警报](#alerts)
    - 了解如何为应用和应用服务计划配置警报
1. [应用服务助手](#Companion)
    - 使用移动设备对应用进行监视和故障排除。
1. [配置日志记录](#logging)
    - 了解如何收集服务器和应用程序日志。
    - 了解用来存储日志的不同位置，以及访问方式。
1. [日志流式处理](#streaming)
    - 使用流式处理日志来查看发出的应用程序和 wc3 日志。
1. [远程调试](#remote)
    - 使用 Visual Studio 对运行在应用服务上的项目进行远程调试。
1. [诊断并解决问题](#diagnose)
    - 确定应用程序的问题，并获取关于如何解决这些问题的信息。
1. [Application Insights](#insights)
    - 应用的高级分析和监视

## <a name="before-you-begin"></a>开始之前

- 需要通过 Web 应用进行监视，并按下列步骤操作。 
    - 可按[使用 SQL 数据库在 Azure 中创建 ASP.NET 应用](app-service-web-tutorial-dotnet-sqldatabase.md)教程中所述步骤来创建应用程序。

- 若要尝试对应用程序进行**远程调试**，则需要使用 Visual Studio。 
    - 如果尚未安装 Visual Studio 2017，可以下载并使用免费的 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 
    - 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。

## <a name="explorer"></a>步骤 1 - 进程资源管理器

通过进程资源管理器这个工具，可以获取应用服务计划内部运作的详细信息。

使用**进程资源管理器**来：

- 枚举应用服务计划中不同实例之间的所有进程。
- 向下钻取并查看与每个进程关联的句柄和模块。 
- 在进程级别查看 CPU、工作集和线程计数，以便确定失控进程数
- 查找打开的文件句柄，甚至终止特定的进程实例。

进程资源管理器可以在“监视” > “进程资源管理器”之下。

![进程资源管理器](media/app-service-web-tutorial-monitoring/app-service-monitor-processexplorer.png)

## <a name="metrics"></a>步骤 2 - 查看应用服务指标
**指标**提供关于 Web 应用的详细信息，以及 Web 应用与其用户和平台交互的详细信息。

通过使用指标可以深入了解：
- 应用当前所使用的资源数
- 应用的流量
- 总请求/失败数
- 数据输入/数据输出量

对任何托管在应用服务中的应用，都必须监视 Web 应用和应用服务计划。

- **应用**指标提供关于 http 请求/失败数和平均响应时间的信息。
- **应用服务计划**指标提供关于资源利用率的信息。

Azure 门户使用 **Azure Monitor** 能直观快速地检查应用的各指标。

- 转到要监视的应用的“概述”边栏选项卡。

![监视应用](media/app-service-web-tutorial-monitoring/app-service-monitor.png)

- 可以“监视磁贴”的形式查看应用的指标。
- 单击磁贴，对要查看的指标和要显示的时间范围进行编辑和配置。

![配置图表](media/app-service-web-tutorial-monitoring/app-service-monitor-configure.png)

- 可以将自定义图表固定到仪表板，以便轻松访问和快速引用。

![固定图表](media/app-service-web-tutorial-monitoring/app-service-monitor-pin.png)

> [!TIP]
> 要了解关于 Azure Monitor 的详细信息，请访问以下链接：
> - [Azure Monitor 入门](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [Azure 指标](..\monitoring-and-diagnostics\monitoring-overview-metrics.md)
> - [Azure Monitor 支持的指标](..\monitoring-and-diagnostics\monitoring-supported-metrics.md#microsoftwebsites-including-functions)
> - [Azure 门户仪表板](..\azure-portal\azure-portal-dashboards.md)

## <a name="alerts"></a>步骤 3 - 配置警报

通过**警报**，可自动执行应用程序监视。

当检测到影响应用的条件时，使用警报来接收通知。

创建警报：
- 转到要监视的应用的“概述”边栏选项卡。
- 从菜单中导航至“监视” > “警报”
- 选择“[+] 添加警报”
- 根据需要配置警报。

![警报](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts.png)

> [!TIP]
> 要了解关于 Azure 警报的详细信息，请访问以下链接：
> - [什么是 Microsoft Azure 中的警报](..\monitoring-and-diagnostics\monitoring-overview-alerts.md)
> - [对指标执行操作](..\monitoring-and-diagnostics\monitoring-overview.md)

## <a name="companion"></a>步骤 4 - 应用服务助手
**应用服务助手**通过移动设备（iOS 或 Android）中的本机体验，提供一种便捷的监视应用的方式。

使用应用服务助手来：
- 查看应用程序指标
- 查看应用程序警报和建议并执行相应操作
- 执行基本的故障排除（浏览、启动、停止和重启应用）
- 获取关键事件的推送通知。

![应用服务助手](media/app-service-web-tutorial-monitoring/app-service-companion.png)

[![应用服务助手应用商店](media/app-service-web-tutorial-monitoring/app-service-companion-appStore.png)](https://itunes.apple.com/app/azure-app-service-companion/id1146659260)
[![应用服务助手 Google Play](media/app-service-web-tutorial-monitoring/app-service-companion-googlePlay.png)](https://play.google.com/store/apps/details?id=azureApps.AzureApps)

可以从[应用商店](https://itunes.apple.com/app/azure-app-service-companion/id1146659260)或 [Google Play](https://play.google.com/store/apps/details?id=azureApps.AzureApps) 安装应用服务助手

## <a name="logging"></a>步骤 5 - 日志记录
通过日志记录，可以为 Web 应用收集**应用程序诊断**和 **Web 服务器诊断**日志。

使用诊断日志了解应用程序的行为、解决应用程序的问题并了解故障条件。

### <a name="application-diagnostics"></a>应用程序诊断
应用程序诊断可以捕获在运行时由应用程序产生的跟踪。 

启用应用程序日志记录：

- 请转到“监视” > “诊断日志”。 
- 使用切换启用应用程序日志记录。

应用程序日志可以存储在 Web 应用的文件系统中，或被推送到 Blob 存储。

> [!TIP]
> 对于生产方案，建议使用 Blob 存储

![监视应用](media/app-service-web-tutorial-monitoring/app-service-monitor-applogs.png)

在 ASP.NET 中可以使用 [System.Diagnostics.Trace 类](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx)记录应用程序跟踪，以生成由日志基础结构捕获的事件。 还可以指定跟踪的严重级别，以便筛选。

```csharp
public ActionResult Delete(Guid? id)
{
    System.Diagnostics.Trace.TraceInformation("GET /Todos/Delete/" + id);
    if (id == null)
    {
        System.Diagnostics.Trace.TraceError("/Todos/Delete/ failed, ID is null");
        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
    }
    Todo todo = db.Todoes.Find(id);
    if (todo == null)
    {
        System.Diagnostics.Trace.TraceWarning("/Todos/Delete/ failed, ID: " + id + " could not be found");
        return HttpNotFound();
    }
    System.Diagnostics.Trace.TraceInformation("GET /Todos/Delete/" + id + "completed successfully");
    return View(todo);
}
```

> [!IMPORTANT]
> 启用日志记录将影响应用程序的性能和资源利用率。 对于生产方案，建议使用错误日志。 仅在调查问题时启用更详细的日志记录。

 ### <a name="web-server-diagnostics"></a>Web 服务器诊断
应用服务可收集三种不同类型的服务器日志：

- **Web 服务器日志记录** 
    - 使用 [W3C 扩展日志文件格式](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)的 HTTP 事务信息。 
    - 这在确定整体站点指标（如处理的请求数量或来自特定 IP 地址的请求数）时非常有用。
- **详细错误日志记录** 
    - 指示故障的 HTTP 状态代码（状态代码 400 或更大数字）的详细错误消息。 
- **失败请求跟踪** 
    - 有关失败请求的详细信息，包括对用于处理请求的 IIS 组件和每个组件所用的时间的跟踪。 
    - 失败请求日志在尝试隔离造成特定 HTTP 错误的因素时十分有用。

启用服务器日志记录：
- 请转到“监视” > “诊断日志”。 
- 使用切换启用不同类型的 Web 服务器诊断。

![监视应用](media/app-service-web-tutorial-monitoring/app-service-monitor-serverlogs.png)

> [!IMPORTANT]
> 启用日志记录将影响应用程序的性能和资源利用率。 对于生产方案，建议使用错误日志，仅在调查问题时启用更详细的日志记录。

### <a name="accessing-logs"></a>访问日志
使用 Azure 存储资源管理器访问存储在 Blob 存储中的日志。

通过 FTP 访问存储在 Web 应用的文件系统中的日志，路径如下：

- **应用程序日志** - /LogFiles/Application/。 
    - 此文件夹包含一个或多个包含应用程序日志记录生成的信息的文本文件。
- **失败请求跟踪** - /LogFiles/W3SVC#########/。 
    - 此文件夹包含一个 XSL 文件和一个或多个 XML 文件。 
- **详细错误日志** - /LogFiles/DetailedErrors/。 
    - 此文件夹包含一个或多个 .htm 文件，提供由应用生成的 HTTP 错误的详细信息。
- **Web 服务器日志** - /LogFiles/http/RawLogs。 
    - 此文件夹包含使用 W3C 扩展日志文件格式进行格式化的一个或多个文本文件。

## <a name="streaming"></a>步骤 6 - 日志流式处理
应用服务可在生成**应用程序日志**和 **Web 服务器日志**时对其进行流式处理。 

使用流式处理日志调试应用程序十分便利，因为相较于通过 FTP 其他方法访问日志，它更节省时间。

> [!TIP]
> 在尝试流式处理日志前，请确保你已启用收集日志，如[日志记录](#logging)部分所述。

流式处理日志：
- 转到“监视”> “日志流”
- 选择“应用程序日志”或“Web 服务器日志”，具体取决于你要查找的信息。
- 在此处，可以暂停、重启和清除缓冲区。

![流式处理日志](media/app-service-web-tutorial-monitoring/app-service-monitor-logstream.png)

> [!TIP]
> 仅当应用中有流量时才生成日志，你也可以增加日志的详细程度，以获取更多事件或信息。

## <a name="remote"></a>步骤 7 - 远程调试
通过**远程调试**，可将调试程序附加到在云端运行的 Web 应用。 可以设置断点、直接操作内存、逐行执行代码，甚至更改代码路径（就像对本地运行的应用所做的操作一样）。

使用远程调试和诊断日志查找应用程序的问题并进行修复。

将调试程序附加到在云端运行的应用：

- 使用 Visual Studio 2017，打开要进行调试的应用的解决方案 
- 像在本地开发中操作的那样设置一些断点。
- 打开“云资源管理器”（ctr + /，ctrl + x）。
- 根据需要，使用 Azure 凭据登录。
- 查找要调试的应用
- 在“操作”窗格中选择“附加调试程序”。

![远程调试](media/app-service-web-tutorial-monitoring/app-service-monitor-vsdebug.png)

Visual Studio 为应用程序配置远程调试，并启动一个导航至应用的浏览器窗口。 浏览应用，以触发断点并逐步执行代码。

> [!WARNING]
> 不建议在生产环境中以调试模式运行。 如果生产应用未进行扩展以容纳多个服务器实例，则调试会阻止 Web 服务器响应其他请求。 对于如何解决生产问题，最佳资源是[配置日志记录](#logging)和 [Application Insights](#insights)

## <a name="diagnose"></a>步骤 8 - 诊断并解决问题
**诊断并解决问题**是一个内置工具，用于扫描 Web 应用最近 24 小时内的活动。 UX 提供了任何所发现问题的摘要视图。

使用此功能有助于从平台问题中区分应用程序问题，并找到使 Web 应用恢复正常运行状况的可能缓解方案。

![诊断并解决问题](media/app-service-web-tutorial-monitoring/app-service-monitor-diagnosis.png)

## <a name="insights"></a>步骤 9 - Application Insights
**Application Insights** 为应用提供了应用程序分析和高级监视功能。 

使用 Application Insights 在 Web 应用中检测和诊断异常以及性能问题。

在“监视” > “Application Insights”中可以为 Web 应用启用 Application Insights 

> [!NOTE]
> Application Insights 可能会提示你安装 Application Insights 站点扩展，以开始收集数据。 安装站点扩展会导致应用程序重启。

![Application Insights](media/app-service-web-tutorial-monitoring/app-service-monitor-appinsights.png)

Application Insights 拥有丰富的功能集，要了解详细信息，请参阅[后续步骤](#next)部分中的链接。

## <a name="next"></a>后续步骤

 - [什么是 Application Insights](..\application-insights\app-insights-overview.md)
 - [使用 Application Insights 监视 Azure Web 应用性能](..\application-insights\app-insights-azure-web-apps.md)
 - [使用 Application Insights 监视任何网站的可用性和响应能力](..\application-insights\app-insights-monitor-web-app-availability.md)
