---
title: "Application Insights 中的性能计数器 | Microsoft 文档"
description: "监视 Application Insights 中的系统和自定义的 .NET 性能计数器。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 94a4d2b85c845b135201fd919e8eac64a5e1cb37


---
# <a name="system-performance-counters-in-application-insights"></a>Application Insights 中的系统性能计数器
Windows 提供了各种[性能计数器](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters)，例如 CPU 占用、内存、磁盘和网络使用情况。 也可以自行定义。 如果应用程序在对其具有管理访问权限的本地主机或虚拟机的 IIS 下运行，[Application Insights](app-insights-overview.md) 可以显示这些性能计数器。 图表指示实时应用程序可用的资源，并有助于确定服务器实例之间的不平衡负载。

性能计数器将出现在“服务器”边栏选项卡中，其中包括按服务器实例进行分段的表格。

![Application Insights 中报告的性能计数器](./media/app-insights-performance-counters/counters-by-server-instance.png)

（性能计数器不适用于 Azure Web 应用。 但是可以[将 Azure 诊断发送到 Application Insights](app-insights-azure-diagnostics.md)。）

## <a name="configure"></a>配置
如果服务器计算机上尚未安装 Application Insights 状态监视器，需要安装它才能看到性能计数器。

在每个服务器实例上，下载并运行[状态监视器安装程序](http://go.microsoft.com/fwlink/?LinkId=506648)。 如果已安装它，无需重新进行安装。

* *我已在开发期间[在应用中安装 Application Insights SDK](app-insights-asp-net.md)。是否仍需要状态监视器？*
  
    是，需要状态监视器来收集 ASP.NET Web 应用的性能计数器。 你可能已经知道，状态监视器还可用于[监视已是实时应用的 Web 应用](app-insights-monitor-performance-live-website-now.md)，无需在开发期间安装 SDK。

## <a name="view-counters"></a>查看计数器
“服务器”边栏选项卡显示一组默认的性能计数器。 

若要查看其他计数器，则编辑“服务器”边栏选项卡上的图表，或打开新的“指标资源管理器”[](app-insights-metrics-explorer.md)边栏选项卡，然后添加新图表。 

编辑图表时，可用的计数器将以指标形式列出。

![Application Insights 中报告的性能计数器](./media/app-insights-performance-counters/choose-performance-counters.png)

若要在某一位置查看所有最有用的图表，请创建[仪表板](app-insights-dashboards.md)并将图表固定到它。

## <a name="add-counters"></a>添加计数器
如果所需的性能计数器未显示在指标列表中，这是因为 Application Insights SDK 未在 Web 服务器中收集它。 可以将它配置为执行此操作。

1. 通过在服务器上使用此 PowerShell 命令，了解服务器中有哪些计数器可用：
   
    `Get-Counter -ListSet *`
   
    （请参阅 [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx)。）
2. 打开 ApplicationInsights.config。
   
   * 如果在开发期间已将 Application Insights 添加到了应用，请在项目中编辑 ApplicationInsights.config，然后将它重新部署到服务器。
   * 如果已使用状态监视器在运行时检测 Web 应用，则在 IIS 的应用根目录中查找 ApplicationInsights.config。 在每个服务器实例的该位置更新它。
3. 编辑性能收集器指令：
   
   ```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

可以捕获标准计数器以及已自行实现的计数器。 `\Objects\Processes` 是标准计数器的一个示例，可在所有 Windows 系统上使用。 `\Sales(photo)\# Items Sold` 是自定义计数器的一个示例，可在 Web 服务中实现。 

格式为 `\Category(instance)\Counter"`，而对于不具有实例的类别，仅为 `\Category\Counter`。

`ReportAs` 对于与 `[a-zA-Z()/-_ \.]+` 不匹配的计数器名称而言是必需项，即，它们包含以下设置中没有的字符：字母、圆括号、正斜杠、连字符、下划线、空格、点。

如果指定实例，它将以所报告指标的维度“CounterInstanceName”进行收集。

### <a name="collecting-performance-counters-in-code"></a>收集代码中的性能计数器
若要收集系统性能计数器并将它们发送到 Application Insights，可以改编下面的片段：

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);

或者，可以执行与所创建自定义指标相同的操作：

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);

## <a name="performance-counters-in-analytics"></a>分析中的性能计数器
可以在[分析](app-insights-analytics.md)中搜索并显示性能计数器报告。

**PerformanceCounters** 架构公开每个性能计数器的 `category`、`counter` 名称和 `instance` 名称。  在每个应用程序的遥测中，将仅看到该应用程序的计数器。 例如，若要查看哪些计数器可用： 

![Application Insights 分析中的性能计数器](./media/app-insights-performance-counters/analytics-performance-counters.png)

（此处“实例”是指性能计数器实例，而不是角色或服务器计算机实例。 通常，性能计数器实例名称会按进程或应用程序的名称对计数器（如处理器时间）进行分段。）

若要获取最近一段时间内可用内存的图表： 

![Application Insights 分析中的内存时间图表](./media/app-insights-performance-counters/analytics-available-memory.png)

与其他遥测一样，**performanceCounters** 同样也具有列 `cloud_RoleInstance`，指示正在其上运行应用的主机服务器实例的标识。 例如，若要应用在不同计算机上的性能： 

![Application Insights 分析中按角色实例分段的性能](./media/app-insights-performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET 和 Application Insights 计数
*异常率和异常指标之间的区别是什么？*

* *异常率*是系统性能计数器。 CLR 会对所有引发的已处理和未经处理异常进行计数，并将总采样间隔除以间隔长度。 Application Insights SDK 会收集此结果，并将其发送到门户。
* *异常*是在图表的采样间隔内门户所接收的 TrackException 报告的计数。 它仅包括已将 TrackException 调用写入代码的已处理异常，并不包括所有[未经处理的异常](app-insights-asp-net-exceptions.md)。 

## <a name="alerts"></a>警报
与其他指标一样，可以[设置警报](app-insights-alerts.md)以便在性能计数器超出指定的限制时收到警报。 打开“警报”边栏选项卡，然后单击“添加警报”。

## <a name="a-namenextanext-steps"></a><a name="next"></a>后续步骤
* [依赖关系跟踪](app-insights-asp-net-dependencies.md)
* [异常跟踪](app-insights-asp-net-exceptions.md)




<!--HONumber=Nov16_HO3-->


