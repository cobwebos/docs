---
title: Application Insights 中的性能计数器 | Microsoft 文档
description: 监视 Application Insights 中的系统和自定义的 .NET 性能计数器。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/13/2018
ms.openlocfilehash: 229216ee873ade9418574141017aaf88235ba9e4
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820707"
---
# <a name="system-performance-counters-in-application-insights"></a>Application Insights 中的系统性能计数器

Windows 提供了各种[性能计数器](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters)，例如 CPU 占用、内存、磁盘和网络使用情况。 你还可以定义自己的性能计数器。 只要您的应用程序在本地主机上的 IIS 或您具有管理访问权限的虚拟机下运行，就支持性能计数器集合。 虽然作为 Azure Web 应用运行的应用程序不能直接访问性能计数器，但 Application Insights 收集可用计数器的子集。

## <a name="view-counters"></a>查看计数器

“指标”窗格显示了一组默认的性能计数器。

![Application Insights 中报告的性能计数器](./media/performance-counters/performance-counters.png)

为 ASP.NET/ASP.NET Core web 应用程序配置的当前默认计数器为：
- 进程\\处理器时间百分比
- 进程\\处理器时间百分比百分比
- 内存\\可用字节数
- 每秒 ASP.NET 请求数
- 引发的 .NET CLR 异常数/秒
- ASP.NET ApplicationsRequest 执行时间
- 处理\\专用字节
- 处理\\IO 数据字节数/秒
- 应用程序队列中的 ASP.NET 应用程序\\请求
- 处理器（_Total）\\处理器时间百分比

## <a name="add-counters"></a>添加计数器

如果你需要的性能计数器未包括在指标列表中，则你可以添加它。

1. 通过在本地服务器上使用此 PowerShell 命令，了解服务器中有哪些计数器可用：

    `Get-Counter -ListSet *`

    （请参阅 [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx)。）
2. 打开 ApplicationInsights.config。

   * 如果在开发期间已将 Application Insights 添加到了应用，请在项目中编辑 ApplicationInsights.config，然后将它重新部署到服务器。
3. 编辑性能收集器指令：

    ```XML

        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <Counters>
            <Add PerformanceCounter="\Objects\Processes"/>
            <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
          </Counters>
        </Add>
    ```

> [!NOTE]
> ASP.NET Core 应用程序没有 `ApplicationInsights.config`，因此以上方法对于 ASP.NET Core 应用程序无效。

可以捕获标准计数器以及你自己实现的计数器。 `\Objects\Processes` 是标准计数器的一个示例，可在所有 Windows 系统上使用。 `\Sales(photo)\# Items Sold` 是自定义计数器的一个示例，可在 Web 服务中实现。

格式为 `\Category(instance)\Counter"`，而对于不具有实例的类别，仅为 `\Category\Counter`。

`ReportAs` 对于与 `[a-zA-Z()/-_ \.]+` 不匹配的计数器名称而言是必需项，即，它们包含以下设置中没有的字符：字母、圆括号、正斜杠、连字符、下划线、空格、点。

如果指定实例，它以所报告指标的维度“CounterInstanceName”进行收集。

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>在代码中收集 ASP.NET Web 应用程序或 .NET/.NET Core 控制台应用程序的性能计数器
要收集系统性能计数器并将它们发送到 Application Insights，可以改编下面的片段：


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

或者，可以执行与所创建自定义指标相同的操作：

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>在代码中收集 ASP.NET Core Web 应用程序的性能计数器

如下所示修改 `Startup.cs` 类中的 `ConfigureServices` 方法。

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>分析中的性能计数器
可以在[分析](../../azure-monitor/app/analytics.md)中搜索并显示性能计数器报告。

**PerformanceCounters** 架构公开每个性能计数器的 `category`、`counter` 名称和 `instance` 名称。  在每个应用程序的遥测中，将仅看到该应用程序的计数器。 例如，若要查看哪些计数器可用： 

![Application Insights 分析中的性能计数器](./media/performance-counters/analytics-performance-counters.png)

（此处“实例”是指性能计数器实例，而不是角色或服务器计算机实例。 通常，性能计数器实例名称会按进程或应用程序的名称对计数器（如处理器时间）进行分段。）

若要获取最近一段时间内可用内存的图表： 

![Application Insights 分析中的内存时间图表](./media/performance-counters/analytics-available-memory.png)

与其他遥测一样，**performanceCounters** 同样也具有列 `cloud_RoleInstance`，指示正在其上运行应用的主机服务器实例的标识。 例如，若要应用在不同计算机上的性能： 

![Application Insights 分析中按角色实例分段的性能](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET 和 Application Insights 计数

*异常率和异常指标之间的区别是什么？*

* *异常率*是系统性能计数器。 CLR 会对所有引发的已处理和未经处理异常进行计数，并将总采样间隔除以间隔长度。 Application Insights SDK 会收集此结果，并将其发送到门户。

* *异常*是在图表的采样间隔内门户所接收的 TrackException 报告的计数。 它仅包括已将 TrackException 调用写入代码的已处理异常，并不包括所有[未经处理的异常](../../azure-monitor/app/asp-net-exceptions.md)。 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>在 Azure Web 应用中运行的应用程序的性能计数器

部署到 Azure Web 应用的 ASP.NET 和 ASP.NET Core 应用程序在特殊的沙箱环境中运行。 此环境不允许直接访问系统性能计数器。 但是，有限的计数器子集作为环境变量公开，如[此处](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables)所述。 Application Insights SDK for ASP.NET 和 ASP.NET Core 从这些特殊环境变量收集 Azure Web 应用的性能计数器。 此环境中仅提供计数器的一个子集，并且可以在此处找到完整列表[。](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>ASP.NET Core 应用程序中的性能计数器

对 ASP.NET Core 中性能计数器的支持受到限制：

* 如果应用程序在 Azure Web 应用（Windows）中运行， [SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)版本2.4.1 和更高版本会收集性能计数器。
* SDK 版本2.7.1 和更高版本，如果应用程序在 Windows 和目标 `NETSTANDARD2.0` 或更高版本中运行，则会收集性能计数器。
* 对于面向 .NET Framework 的应用程序，SDK 的所有版本都支持性能计数器。
* SDK 版本2.8.0 和更高版本支持 Linux 中的 cpu/内存计数器。 Linux 不支持其他计数器。 在 Linux （和其他非 Windows 环境）中获取系统计数器的建议方法是使用[EventCounters](eventcounters.md)

## <a name="alerts"></a>警报
与其他指标一样，可以[设置警报](../../azure-monitor/app/alerts.md)以便在性能计数器超出指定的限制时收到警报。 打开“警报”窗格，并单击“添加警报”。

## <a name="next"></a>后续步骤

* [依赖关系跟踪](../../azure-monitor/app/asp-net-dependencies.md)
* [异常跟踪](../../azure-monitor/app/asp-net-exceptions.md)

