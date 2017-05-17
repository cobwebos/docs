---
title: "适用于 .NET 应用的 Azure Application Insights 快照调试器 | Microsoft Docs"
description: "生产 .NET 应用中引发异常时调试自动收集的快照"
services: application-insights
documentationcenter: 
author: qubitron
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/30/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 94b073df00b690f1347e246eeb7e12fdd1bd7cc1
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>.NET 应用中发生异常时调试快照

*此功能为预览版。*

发生异常时自动从实时 Web 应用程序收集调试快照。 快照显示引发异常时的源代码和变量状态。 [Application Insights](app-insights-overview.md) 中的快照调试器可以监视来自 Web 应用的异常遥测数据。 它可以针对最常引发的异常收集快照，提供诊断生产中的问题所需的信息。 请将[快照收集器 NuGet 包](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)添加到应用程序，并根据需要在 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 中配置收集参数。 快照显示在 Application Insights 门户中的[异常](app-insights-asp-net-exceptions.md)区域中。

可以在门户中查看调试快照，以查看调用堆栈并检查每个调用堆栈帧中的变量。 若要获取更强大的调试体验与源代码，请[下载用于 Visual Studio 的快照调试器扩展](https://aka.ms/snapshotdebugger)，在 Visual Studio 2017 Enterprise 中打开快照。

快照收集适用于在 .NET Framework 4.6 和更高版本上运行的、在 Azure 计算或 Azure 应用服务中的 IIS 上托管的 ASP.NET Web 应用。

## <a name="configure-snapshot-collection"></a>配置快照收集

1. [在 Web 应用中启用 Application Insights](app-insights-asp-net.md)（如果尚未这样做）。

2. 将 [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包添加到应用。 

3. 查看该包在 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 中添加的默认选项。

```xml
  <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
      <IsEnabled>true</IsEnabled>
      <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
      <!-- DeveloperMode is a property on the active TelemetryChannel. -->
      <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
      <!-- How many times we need to see an exception before we ask for snapshots. -->
      <ThresholdForSnapshotting>5</ThresholdForSnapshotting>
      <!-- The maximum number of examples we create for a single problem. -->
      <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
      <!-- The maximum number of problems that we can be tracking at any time. -->
      <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
      <!-- How often to reset problem counters. -->
      <ProblemCounterResetInterval>06:00:00</ProblemCounterResetInterval>
      <!-- The maximum number of snapshots allowed in one minute. -->
      <SnapshotsPerMinuteLimit>2</SnapshotsPerMinuteLimit>
      <!-- The maximum number of snapshots allowed per day. -->
      <SnapshotsPerDayLimit>50</SnapshotsPerDayLimit>
    </Add>
  </TelemetryProcessors>
```

仅当发生了 Application Insights SDK 可见的异常时，才收集快照。 在某些情况下，可能需要[配置异常收集](app-insights-asp-net-exceptions.md#exceptions)，以查看门户中显示的快照异常。

## <a name="debugging-snapshots-in-the-application-insights-portal"></a>在 Application Insights 门户中调试快照

如果某个快照适用于给定的异常或问题 ID，Application Insights 门户上的[异常](app-insights-asp-net-exceptions.md)区域中会显示“打开调试快照”链接。

![异常区域中的“打开调试快照”按钮](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

在“调试快照”视图中，可以看到调用堆栈和变量窗格。 在调用堆栈窗格中选择调用堆栈帧可在变量窗格中查看该函数调用的局部变量和参数。

![在门户中查看调试快照](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

快照可能包含敏感信息，默认情况下不可查看。 若要查看快照，必须在订阅或资源的门户中获取 `Application Insights Snapshot Debugger` 角色。 目前，只有订阅所有者才能分配此角色，或者根据用户分配此角色。 当前不支持将此角色分配给 Azure Active Directory 组。

## <a name="debugging-snapshots-with-visual-studio-2017-enterprise"></a>使用 Visual Studio 2017 Enterprise 调试快照
可以单击“下载快照”按钮，下载可在 Visual Studio 2017 Enterprise 中打开的 `.diagsession` 文件。 目前，打开 `.diagsession` 文件需要先[下载并安装用于 Visual Studio 的快照调试器扩展](https://aka.ms/snapshotdebugger)。

打开快照文件后，将转到 Visual Studio 的“小型转储调试”页，在该页中单击“调试托管代码”即可开始调试快照。 你将转到引发异常的代码行，可以调试进程的当前状态。

![在 Visual Studio 中查看调试快照](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

下载的快照中包含在 Web 应用程序服务器上找到的所有符号文件。 需要使用这些符号文件将快照数据与源代码相关联。 对于 Azure 应用服务应用，请确保在发布 Web 应用时部署符号。

## <a name="how-snapshots-work"></a>快照的工作原理

启动应用程序时，将创建一个可在应用程序中监视快照请求的独立快照上传程序进程。 请求快照时，将在大约 10 到 20 毫秒内创建运行中进程的卷影副本。 然后，将分析该卷影进程并创建快照，同时，主进程会继续运行并向用户提供流量。 接下来，将快照连同查看快照所需的任何相关符号文件 (.pdb) 一起上传到 Application Insights。

## <a name="current-limitations"></a>当前限制

### <a name="publishing-symbols"></a>发布符号
快照调试器要求生产服务器上存在符号文件，这样它才能在 Visual Studio 中解码变量并提供调试体验。 默认情况下，在发布到 Azure 应用服务时，Visual Studio 2017 版本 15.2 会发布适用于发行内部版本的符号。 在以前的版本中，需要将以下代码行添加到发布配置文件 `.pubxml`，以便在发布模式下发布符号。

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

对于 Azure 计算和其他类型，请确保符号文件位于主应用程序 .dll 所在的同一文件夹（通常为 `wwwroot/bin`）中，或者可通过当前路径访问。

### <a name="optimized-builds"></a>优化的内部版本
在某些情况下，由于生成过程中应用了优化，无法在发行内部版本中查看局部变量。 将来的 NuGet 包版本将解决此限制。

## <a name="next-steps"></a>后续步骤

* [诊断 Web 应用中的异常](app-insights-asp-net-exceptions.md)介绍了如何在 Application Insights 中显示更多的异常。 
* [智能检测](app-insights-proactive-diagnostics.md)可自动发现性能异常。

