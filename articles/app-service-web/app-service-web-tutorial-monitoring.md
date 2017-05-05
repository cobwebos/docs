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
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 8d9b4a4fa3b62659fc7e2aa1c6329fdc5e01fe39
ms.lasthandoff: 04/21/2017

---
# <a name="monitor-app-service"></a>监视应用服务
本教程逐步讲解如何监视应用，以及在出现问题时如何使用内置的平台工具解决问题。

本文档的每个部分介绍一项具体功能。 结合使用这些功能可以：
- 识别应用中的问题。
- 确定该问题是由代码还是平台引起的。
- 在代码中缩小问题的起源范围。
- 调试并解决问题。

## <a name="before-you-begin"></a>开始之前
- 需要通过 Web 应用进行监视，并按下列步骤操作。 
    - 可按[使用 SQL 数据库在 Azure 中创建 ASP.NET 应用](app-service-web-tutorial-dotnet-sqldatabase.md)教程中所述步骤来创建应用程序。

- 若要尝试对应用程序进行**远程调试**，则需要使用 Visual Studio。 
    - 如果尚未安装 Visual Studio 2017，可以下载并使用免费的 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 
    - 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。

## <a name="metrics"></a>步骤 1 - 查看指标
**指标**有助于了解以下信息： 
- 应用程序运行状况
- 应用性能
- 资源消耗

调查应用程序问题时，最好是先查看指标。 Azure 门户使用 **Azure Monitor** 能直观快速地检查应用的各指标。

指标提供应用的多个关键聚合数据的历史视图。 对于应用服务中托管的任何应用，应该同时监视 Web 应用和应用服务计划。

> [!NOTE]
> 应用服务计划表示用于托管应用的物理资源集合。 分配到应用服务计划的所有应用程序将共享该计划定义的资源，在托管多个应用时可以节省成本。
>
> 应用服务计划定义：
> * 区域：北欧、美国东部、东南亚，等等。
> * 实例大小：小、中、大，等等。
> * 规模计数：一个、两个、三个实例，等等。
> * SKU：免费、共享、基本、标准、高级，等等。

若要查看 Web 应用的指标，请转到要监视的应用的“概述”边栏选项卡。 在此处，可以**监视磁贴**的形式查看应用的指标图表。 单击磁贴，对要查看的指标和要显示的时间范围进行编辑和配置。 

默认情况下，资源边栏选项卡提供过去一个小时的应用程序请求和错误视图。
![监视应用](media/app-service-web-tutorial-monitoring/app-service-monitor.png)

在示例中可以看到，某个应用程序正在生成大量的 **HTTP 服务器错误**。 出现大量错误，就是需要调查此应用程序的第一个迹象。

> [!TIP]
> 要了解关于 Azure Monitor 的详细信息，请访问以下链接：
> - [Azure Monitor 入门](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [Azure 指标](..\monitoring-and-diagnostics\monitoring-overview-metrics.md)
> - [Azure Monitor 支持的指标](..\monitoring-and-diagnostics\monitoring-supported-metrics.md#microsoftwebsites-including-functions)
> - [Azure 仪表板](..\azure-portal\azure-portal-dashboards.md)

## <a name="alerts"></a>步骤 2 - 配置警报
可将**警报**配置为在应用出现特定的状态时触发。

在[步骤 1 - 查看指标](#metrics)中，我们看到该应用程序生成了大量错误。 

让我们配置一个警报，以便在发生错误时自动接收通知。 在本例中，我们希望每当 HTTP 50X 错误数超过特定的阈值时，该警报就会发送电子邮件。

若要创建警报，请导航到“监视” > “警报”，然后单击“[+] 添加警报”。

![警报](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts.png)

为警报配置提供值：
- **资源：**要使用警报监视的站点。 
- **名称：**警报的名称，在本例中为 *High HTTP 50X*。
- **说明：**描述此警报的监视内容的纯文本说明。
- **警报依据：**警报可以监视指标或事件，在本示例中，我们要监视指标。
- **指标：**要监视的指标，在本例中为“HTTP 服务器错误”。
- **条件：**何时触发警报，本例选择了“大于”选项。
- **阈值：**要监视的值，在本例中为 *400*。
- **时间段：**警报针对指标的平均值运行。 时间段越小，生成的警报越灵敏。 在本例中，监视的时间段为“5 分钟”。 
- **电子邮件所有者和参与者：**在本例中设置为“已启用”。

创建警报后，每当应用超过配置的阈值时，系统会发送一封电子邮件。 还可以在 Azure 门户中查看活动的警报。

![触发的警报](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts-triggered.png)


> [!TIP]
> 要了解关于 Azure 警报的详细信息，请访问以下链接：
> - [什么是 Microsoft Azure 中的警报](..\monitoring-and-diagnostics\monitoring-overview-alerts.md)
> - [对指标执行操作](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [创建指标警报](..\monitoring-and-diagnostics\insights-alerts-portal.md)

## <a name="companion"></a>步骤 3 - 应用服务助手
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

## <a name="diagnose"></a>步骤 4 - 诊断并解决问题
**诊断并解决问题**可帮助你将应用程序问题与平台问题区分开来。 它还可以建议可行的缓解措施，使 Web 应用恢复正常。
 
![诊断并解决问题](media/app-service-web-tutorial-monitoring/app-service-monitor-diagnosis.png)

沿用前面步骤中的示例，我们可以看到，应用程序一直存在可用性问题。 相比之下，平台可用性从未低于 100%。

应用有问题但平台一直正常，很明显，我们要处理的是应用程序问题。

## <a name="logging"></a>步骤 5 - 日志记录
将故障原因的范围缩小到应用程序问题后，可以查看应用程序和服务器日志来获取详细信息。

通过日志记录，可以为 Web 应用收集**应用程序诊断**和 **Web 服务器诊断**日志。

### <a name="application-diagnostics"></a>应用程序诊断
应用程序诊断可以捕获在运行时由应用程序产生的跟踪。 

将跟踪添加到应用程序可以大大提高调试和查明问题的能力。

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
若要启用应用程序日志记录，请转到“监视” > “诊断日志”，然后使用切换开关启用应用程序日志记录。

![监视应用](media/app-service-web-tutorial-monitoring/app-service-monitor-applogs.png)

应用程序日志可以存储在 Web 应用的文件系统中，或被推送到 Blob 存储。 对于生产方案，我们建议使用 Blob 存储。

> [!IMPORTANT]
> 启用日志记录将影响应用程序的性能和资源利用率。 对于生产方案，建议使用错误日志。 仅在调查问题时启用更详细的日志记录。

 ### <a name="web-server-diagnostics"></a>Web 服务器诊断
即使未检测应用，也会生成 Web 服务器日志。 应用服务可收集三种不同类型的服务器日志：

- **Web 服务器日志记录** 
    - 使用 [W3C 扩展日志文件格式](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)的 HTTP 事务信息。 
    - 这在确定整体站点指标（如处理的请求数量或来自特定 IP 地址的请求数）时非常有用。
- **详细错误日志记录** 
    - 指示故障的 HTTP 状态代码（状态代码 400 或更大数字）的详细错误消息。 
    - [了解详细错误日志记录](https://www.iis.net/learn/troubleshoot/diagnosing-http-errors/how-to-use-http-detailed-errors-in-iis)
- **失败请求跟踪** 
    - 有关失败请求的详细信息，包括对用于处理请求的 IIS 组件和每个组件所用的时间的跟踪。 
    - 失败请求日志在尝试隔离造成特定 HTTP 错误的因素时十分有用。
    - [详细了解失败请求跟踪](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing/troubleshooting-failed-requests-using-tracing-in-iis)

启用服务器日志记录：
- 请转到“监视” > “诊断日志”。 
- 使用切换启用不同类型的 Web 服务器诊断。

![监视应用](media/app-service-web-tutorial-monitoring/app-service-monitor-serverlogs.png)

> [!IMPORTANT]
> 启用日志记录将影响应用程序的性能和资源利用率。 对于生产方案，建议使用错误日志，仅在调查问题时启用更详细的日志记录。

### <a name="accessing-logs"></a>访问日志
使用 Azure 存储资源管理器访问存储在 Blob 存储中的日志。 通过 FTP 访问存储在 Web 应用的文件系统中的日志，路径如下：

- **应用程序日志** - `%HOME%/LogFiles/Application/`。
    - 此文件夹包含一个或多个包含应用程序日志记录生成的信息的文本文件。
- **失败请求跟踪** - `%HOME%/LogFiles/W3SVC#########/`。 
    - 此文件夹包含一个 XSL 文件和一个或多个 XML 文件。 
- **详细错误日志** - `%HOME%/LogFiles/DetailedErrors/`。 
    - 此文件夹包含一个或多个 .htm 文件，提供由应用生成的 HTTP 错误的详细信息。
- **Web 服务器日志** - `%HOME%/LogFiles/http/RawLogs`。 
    - 此文件夹包含使用 W3C 扩展日志文件格式进行格式化的一个或多个文本文件。

## <a name="streaming"></a>步骤 6 - 日志流式处理
使用流式处理日志调试应用程序十分便利，因为相较于通过 FTP [访问日志](#Accessing-Logs)，它更节省时间。

应用服务可在生成**应用程序日志**和 **Web 服务器日志**时对其进行流式处理。 

> [!TIP]
> 在尝试流式处理日志前，请确保你已启用收集日志，如[日志记录](#logging)部分所述。

若要流式处理日志，请转到“监视”> “日志流”。 选择“应用程序日志”或“Web 服务器日志”，具体取决于你要查找的信息。 在此处，可以暂停、重启和清除缓冲区。

![流式处理日志](media/app-service-web-tutorial-monitoring/app-service-monitor-logstream.png)

> [!TIP]
> 仅当应用中有流量时才生成日志，你也可以增加日志的详细程度，以获取更多事件或信息。

## <a name="remote"></a>步骤 7 - 远程调试
查明应用程序问题的起源后，请使用**远程调试**逐行排查代码。

使用远程调试可将调试程序附加到云中运行的 Web 应用。 可以设置断点、直接操作内存、逐行执行代码，甚至更改代码路径（就像对本地运行的应用所做的操作一样）。

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
> 不建议在生产环境中以调试模式运行。 如果生产应用未进行扩展以容纳多个服务器实例，则调试会阻止 Web 服务器响应其他请求。 对于如何解决生产问题，最佳资源是[配置日志记录](#logging)和 [Application Insights](#insights)。



## <a name="explorer"></a>步骤 8 - 进程资源管理器
将应用程序扩展到多个实例后，**进程资源管理器**可帮助你识别实例特定的问题。

使用**进程资源管理器**来：

- 枚举应用服务计划中不同实例之间的所有进程。
- 向下钻取并查看与每个进程关联的句柄和模块。 
- 在进程级别查看 CPU、工作集和线程计数，以便确定失控进程数
- 查找打开的文件句柄，甚至终止特定的进程实例。

可以在“监视” > “进程资源管理器”下面找到进程资源管理器。

![进程资源管理器](media/app-service-web-tutorial-monitoring/app-service-monitor-processexplorer.png)


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
