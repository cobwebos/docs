---
title: 在 Azure 云服务中收集性能计数器 | Microsoft Docs
description: 了解如何使用 Azure 诊断和 Application Insights 在云服务中发现、使用和创建性能计数器。
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/18
ms.author: adegeo
ms.openlocfilehash: 3e0af48c172fa912f0ac9e05b7b761dd7eaad795
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
ms.locfileid: "29134044"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>收集 Azure 云服务的性能计数器

使用性能计数器可以跟踪应用程序和主机的运行情况。 Windows Server 提供与硬件、应用程序、操作系统等相关的许多不同性能计数器。 通过收集性能计数器并将其发送到 Azure，可以分析此信息以帮助做出更明智的决策。 

## <a name="discover-available-counters"></a>发现可用的计数器

性能计数器由两个部分组成：集名称（也称为类别），以及一个或多个计数器。 可以使用 PowerShell 获取可用性能计数器的列表：

```PowerShell
Get-Counter -ListSet * | Select-Object CounterSetName, Paths | Sort-Object CounterSetName

CounterSetName                                  Paths
--------------                                  -----
.NET CLR Data                                   {\.NET CLR Data(*)\SqlClient...
.NET CLR Exceptions                             {\.NET CLR Exceptions(*)\# o...
.NET CLR Interop                                {\.NET CLR Interop(*)\# of C...
.NET CLR Jit                                    {\.NET CLR Jit(*)\# of Metho...
.NET Data Provider for Oracle                   {\.NET Data Provider for Ora...
.NET Data Provider for SqlServer                {\.NET Data Provider for Sql...
.NET Memory Cache 4.0                           {\.NET Memory Cache 4.0(*)\C...
AppV Client Streamed Data Percentage            {\AppV Client Streamed Data ...
ASP.NET                                         {\ASP.NET\Application Restar...
ASP.NET Apps v4.0.30319                         {\ASP.NET Apps v4.0.30319(*)...
ASP.NET State Service                           {\ASP.NET State Service\Stat...
ASP.NET v2.0.50727                              {\ASP.NET v2.0.50727\Applica...
ASP.NET v4.0.30319                              {\ASP.NET v4.0.30319\Applica...
Authorization Manager Applications              {\Authorization Manager Appl...

#... results cut to save space ...
```

`CounterSetName` 属性表示集（或类别），可以很好地反映性能计数器与哪些组件相关。 `Paths` 属性表示某个集的计数器集合。 还可以获取 `Description` 属性来了解有关计数器集的详细信息。

若要获取某个集的所有计数器，请使用 `CounterSetName` 值并扩展 `Paths` 集合。 每个路径项是可以查询的计数器。 例如，若要获取 `Processor` 集相关的可用计数器，请扩展 `Paths` 集合：

```PowerShell
Get-Counter -ListSet * | Where-Object CounterSetName -eq "Processor" | Select -ExpandProperty Paths

\Processor(*)\% Processor Time
\Processor(*)\% User Time
\Processor(*)\% Privileged Time
\Processor(*)\Interrupts/sec
\Processor(*)\% DPC Time
\Processor(*)\% Interrupt Time
\Processor(*)\DPCs Queued/sec
\Processor(*)\DPC Rate
\Processor(*)\% Idle Time
\Processor(*)\% C1 Time
\Processor(*)\% C2 Time
\Processor(*)\% C3 Time
\Processor(*)\C1 Transitions/sec
\Processor(*)\C2 Transitions/sec
\Processor(*)\C3 Transitions/sec
```

可将这些单独的计数器路径添加到云服务使用的诊断框架。 有关如何构造性能计数器路径的详细信息，请参阅[指定计数器路径](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))。

## <a name="collect-a-performance-counter"></a>收集性能计数器

可将性能计数器添加到 Azure 诊断或 Application Insights 的云服务。

### <a name="application-insights"></a>Application Insights

使用适用于云服务的 Azure Application Insights 可以指定要收集的性能计数器。 将 [Application Insights 添加到项目](../application-insights/app-insights-cloudservices.md#sdk)之后，名为 **ApplicationInsights.config** 的配置文件将添加到 Visual Studio 项目。 此配置文件定义 Application Insights 要收集并发送到 Azure 的信息类型。

打开 **ApplicationInsights.config** 文件并找到 **ApplicationInsights** > **TelemetryModules** 元素。 每个 `<Add>` 子元素定义要收集的一种遥测类型及其配置。 性能计数器遥测模块类型为 `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`。 如果已定义此元素，请不要再次添加它。 要收集的每个性能计数器在名为 `<Counters>` 的节点下定义。 下面是收集驱动器性能计数器的示例：

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Write Bytes/sec" ReportAs="Disk write (C:)" />
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Read Bytes/sec" ReportAs="Disk read (C:)" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

每个性能计数器在 `<Counters>` 下表示为 `<Add>` 元素。 `PerformanceCounter` 属性定义要收集的性能计数器。 `ReportAs` 属性是在 Azure 门户中针对性能计数器显示的标题。 收集的任何性能计数器将在门户中放入名为“自定义”的类别中。 与 Azure 诊断不同，无法设置收集这些性能计数器并将其发送到 Azure 的间隔。 使用 Application Insights 时，每隔一分钟收集和发送性能计数器一次。 

Application Insights 会自动收集以下性能计数器：

* \Process(??APP_WIN32_PROC??)\%处理器时间
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% 处理器时间

有关详细信息，请参阅 [Application Insights 中的系统性能计数器](../application-insights/app-insights-performance-counters.md)和[适用于 Azure 云服务的 Application Insights](../application-insights/app-insights-cloudservices.md#performance-counters)。

### <a name="azure-diagnostics"></a>Azure 诊断

> [!IMPORTANT]
> 尽管所有这些数据都会聚合到存储帐户中，但门户**不**提供绘制数据图表的本机方法。 我们强烈建议将另一个诊断服务（如 Application Insights）集成到应用程序中。

使用适用于云服务的 Azure 诊断扩展可以指定要收集的性能计数器。 若要设置 Azure 诊断，请参阅[云服务监视概述](cloud-services-how-to-monitor.md#setup-diagnostics-extension)。

要收集的性能计数器在 **diagnostics.wadcfgx** 文件中定义。 请在 Visual Studio 中打开此文件（为每个角色定义了此文件），并找到 **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters** 元素。 将新的 **PerformanceCounterConfiguration** 元素添加为子级。 此元素有两个属性：`counterSpecifier` 和 `sampleRate`。 `counterSpecifier` 属性定义要收集的系统性能计数器集（请参阅上一部分）。 `sampleRate` 值指示轮询值的频率。 将会根据父 `PerformanceCounters` 元素的 `scheduledTransferPeriod` 属性值，将所有性能计数器作为一个整体传输到 Azure。

有关 `PerformanceCounters` 架构元素的详细信息，请参阅 [Azure 诊断架构](../monitoring-and-diagnostics/azure-diagnostics-schema-1dot3-and-later.md#performancecounters-element)。

`sampleRate` 属性定义的时间段使用 XML 持续时间数据类型来指示轮询性能计数器的频率。 在以下示例中，频率设置为 `PT3M`，表示 `[P]eriod[T]ime[3][M]inutes`：每隔 3 分钟。

有关 `sampleRate` 和 `scheduledTransferPeriod` 定义方式的详细信息，请参阅 [W3 XML 日期和时间日期类型](https://www.w3schools.com/XML/schema_dtypes_date.asp)教程中的“持续时间数据类型”部分。

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />

          <!-- This is a new perf counter which will track the C: disk read activity in bytes per second, every minute -->
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(C:)\Disk Read Bytes/sec" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="create-a-new-perf-counter"></a>创建新的性能计数器

可以在代码中创建和使用新的性能计数器。 创建新性能计数器的代码必须以提升的权限运行，否则会失败。 云服务 `OnStart` 启动代码可以创建性能计数器，这需要在权限提升的上下文中运行角色。 或者，可以创建一个以提升的权限运行并可创建性能计数器的启动任务。 有关启动任务的详细信息，请参阅[如何配置和运行云服务的启动任务](cloud-services-startup-tasks.md)。

若要将角色配置为以提升的权限运行，请将 `<Runtime>` 元素添加到 [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) 文件。

```xml
<ServiceDefinition name="CloudServiceLoadTesting" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Large">

    <!-- ... cut to save space ... -->

    <Runtime executionContext="elevated">
      
    </Runtime>

    <!-- ... cut to save space ... -->

  </WorkerRole>
</ServiceDefinition>
```

只需几行代码就能创建并注册新的性能计数器。 使用可以创建类别和计数器的 `System.Diagnostics.PerformanceCounterCategory.Create` 方法重载。 以下代码首先检查类别是否存在，如果不存在，则创建该类别和计数器。

```csharp
using System.Diagnostics;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRoleWithSBQueue1
{
    public class WorkerRole : RoleEntryPoint
    {
        // Perf counter variable representing times service was used.
        private PerformanceCounter counterServiceUsed;

        public override bool OnStart()
        {
            // ... Other startup code here ...

            // Define the cateogry and counter names.
            string perfCounterCatName = "MyService";
            string perfCounterName = "Times Used";

            // Create the counter if needed. Our counter category only has a single counter.
            // Both the category and counter are created in the same method call.
            if (!PerformanceCounterCategory.Exists(perfCounterCatName))
            {
                PerformanceCounterCategory.Create(perfCounterCatName, "Collects information about the cloud service.", 
                                                  PerformanceCounterCategoryType.SingleInstance, 
                                                  perfCounterName, "How many times the cloud service was used.");
            }

            // Get reference to our counter
            counterServiceUsed = new PerformanceCounter(perfCounterCatName, perfCounterName);
            counterServiceUsed.ReadOnly = false;
            
            return base.OnStart();
        }

        // ... cut class code to save space
    }
}
```

若要使用计数器，请调用 `Increment` 或 `IncrementBy` 方法。

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

在应用程序使用自定义计数器后，需要配置 Azure 诊断或 Application Insights 来跟踪该计数器。


### <a name="application-insights"></a>Application Insights

如前所述，Application Insights 的性能计数器在 **ApplicationInsights.config** 文件中定义。 打开 **ApplicationInsights.config** 并找到 **ApplicationInsights** > **TelemetryModules** > **Add** > **Counters** 元素。 创建 `<Add>` 子元素，并将 `PerformanceCounter` 属性设置为代码中创建的性能计数器的类别和名称。 将 `ReportAs` 属性设置为要在门户中显示的友好名称。

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <!-- ... cut other perf counters to save space ... -->

        <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
        <Add PerformanceCounter="\MyService\Times Used" ReportAs="Service used counter" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

### <a name="azure-diagnostics"></a>Azure 诊断

如前所述，要收集的性能计数器在 **diagnostics.wadcfgx** 文件中定义。 请在 Visual Studio 中打开此文件（为每个角色定义了此文件），并找到 **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters** 元素。 将新的 **PerformanceCounterConfiguration** 元素添加为子级。 将 `counterSpecifier` 属性设置为代码中创建的性能计数器的类别和名称。 

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <!-- ... cut other perf counters to save space ... -->
          
          <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
          <PerformanceCounterConfiguration counterSpecifier="\MyService\Times Used" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="more-information"></a>详细信息

- [适用于 Azure 云服务的 Application Insights](../application-insights/app-insights-cloudservices.md#performance-counters)
- [Application Insights 中的系统性能计数器](../application-insights/app-insights-performance-counters.md)
- [指定计数器路径](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Azure 诊断架构 - 性能计数器](../monitoring-and-diagnostics/azure-diagnostics-schema-1dot3-and-later.md#performancecounters-element)