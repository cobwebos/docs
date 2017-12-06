---
title: "Azure Application Insights 快照调试程序故障排除指南 | Microsoft Docs"
description: "有关 Azure Application Insights 快照调试程序的常见问题。"
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 
ms.service: application-insights
ms.workload: 
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mbullwin
ms.openlocfilehash: 5d6a2fe4c3ee373172f5324b6c7a39e4f94f4652
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="snapshot-debugger-troubleshooting-guide"></a>快照调试程序：故障排除指南

使用 Application Insights 快照调试程序可以自动从实时 Web 应用程序收集调试快照。 快照显示引发异常时源代码和变量的状态。 如果在启动和运行 Application Insights 快照调试程序时遇到问题，请参阅本文了解调试程序的工作原理，以及常见故障排除方案的解决方法。 

## <a name="how-does-application-insights-snapshot-debugger-work"></a>Application Insights 快照调试程序的工作原理

Application Insights 快照调试程序是 Application Insights 遥测管道（ITelemetryProcessor 的实例）的一部分，快照收集器既可监视代码中引发的异常 (AppDomain.FirstChanceException)，也可监视 Application Insights 异常遥测管道跟踪的异常。 成功将快照收集器添加到项目后，如果快照收集器在 Application Insights 遥测管道中检测到某个异常，则会在自定义数据中发送名为“AppInsightsSnapshotCollectorLogs”和“SnapshotCollectorEnabled”的 Application Insights 自定义事件。 同时，它会启动一个名为“MinidumpUploader.exe”的进程，以将收集的快照数据文件上传到 Application Insights。  启动“MinidumpUploader.exe”进程时，会发送名为“UploaderStart”的自定义事件。 完成上述步骤后，快照收集器进入其正常监视行为模式。

尽管快照收集器监视的是 Application Insights 异常遥测，但它使用配置中定义的参数（例如 ThresholdForSnapshotting、MaximumSnapshotsRequired、MaximumCollectionPlanSize、ProblemCounterResetInterval）确定何时收集快照。 符合所有规则时，收集器将请求在同一位置引发的下一个异常的快照。 同时，将会发送名为“AppInsightsSnapshotCollectorLogs”和“RequestSnapshots”的 Application Insights 自定义事件。 由于编译器将优化“发布”代码，局部变量不一定会在收集的快照中显示。 请求快照时，快照收集器会尝试取消优化引发异常的方法。 在此期间，将在自定义数据中发送名为“AppInsightsSnapshotCollectorLogs”和“ProductionBreakpointsDeOptimizeMethod”的 Application Insights 自定义事件。  收集下一个异常的快照后，将提供局部变量。 收集快照后，收集器会重新优化代码以确保性能。 

> [!NOTE]
> 取消优化需要安装 Application Insights 站点扩展。

请求特定异常的快照时，快照收集器会开始监视应用程序的异常处理管道 (AppDomain.FirstChanceException)。 如果再次发生该异常，收集器会启动一个快照（自定义数据中名为“AppInsightsSnapshotCollectorLogs”和“SnapshotStart”的 Application Insights 自定义事件）。 然后，生成运行中进程的影子副本（将复制页面表）。 此过程通常需要 10 到 20 毫秒。 完成此过程后，将在自定义数据中发送名为“AppInsightsSnapshotCollectorLogs”和“SnapshotStop”的 Application Insights 自定义事件。 创建分支进程后，总分页内存将会递增，增量为运行中应用程序的分页内存量（工作集要小得多）。 当应用程序进程正常运行时，卷影复制进程的内存将转储到磁盘，并上传到 Application Insights。 上传快照后，将会发送名为“UploadSnapshotFinish”的 Application Insights 自定义事件。

## <a name="is-the-snapshot-collector-working-properly"></a>快照收集器是否工作正常？

### <a name="how-to-find-snapshot-collector-logs"></a>如何查找快照收集器日志
如果已安装[快照收集器 NuGet 包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) 1.1.0 或更高版本，则快照收集器日志会发送到 Application Insights 帐户。 请确保 *ProvideAnonymousTelemetry* 未设置为 false（默认值为 true）。

* 在 Azure 门户中导航到 Application Insights 资源。
* 在“概述”部分中单击“搜索”。
* 在搜索框中输入以下字符串：
    ```
    AppInsightsSnapshotCollectorLogs OR AppInsightsSnapshotUploaderLogs OR UploadSnapshotFinish OR UploaderStart OR UploaderStop
    ```
* 注意：请根据需要更改“时间范围”。

![搜索快照收集器日志的屏幕截图](./media/app-insights-troubleshoot-snapshot-debugger/001.png)


### <a name="examine-snapshot-collector-logs"></a>检查快照收集器日志
搜索快照收集器日志时，目标时间范围内应包含“UploadSnapshotFinish”事件。 如果仍未看到用于打开快照的“打开调试快照”按钮，请向 snapshothelp@microsoft.com 发送电子邮件并随附 Application Insights 的检测密钥。

![检查快照收集器日志的屏幕截图](./media/app-insights-troubleshoot-snapshot-debugger/005.png)

## <a name="i-cannot-find-a-snapshot-to-open"></a>找不到要打开的快照
如果以下步骤未能帮助解决问题，请向 snapshothelp@microsoft.com 发送电子邮件并随附 Application Insights 的检测密钥。

### <a name="step-1-make-sure-your-application-is-sending-telemetry-data-and-exception-data-to-application-insights"></a>步骤 1：确保应用程序正在向 Application Insights 发送遥测数据和异常数据
导航到 Application Insights 资源，检查应用程序发来的数据。

### <a name="step-2-make-sure-snapshot-collector-is-added-correctly-to-your-applications-application-insights-telemetry-pipeline"></a>步骤 2：确保已正确将快照收集器添加到应用程序的 Application Insights 遥测管道
如果在“如何查找快照收集器日志”步骤中可以找到日志，则表示已正确将快照收集器添加到项目，因此可忽略本步骤。

如果未看到快照收集器日志，请检查以下各项：
* 对于经典 ASP.NET 应用程序，请在 *ApplicationInsights.config* 文件中检查 *<Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">* 行。

* 对于 ASP.NET Core 应用程序，请确保已将包含 *SnapshotCollectorTelemetryProcessor* 的 *ITelemetryProcessorFactory* 添加到 *IServiceCollection* 服务。

* 另请检查是否在发布的应用程序中使用了正确的检测密钥。

* 快照收集器不支持在一个应用程序中使用多个检测密钥，它会将快照发送到其观测到的第一个异常的检测密钥。

* 如果在代码中手动设置了 *InstrmentationKey*，请更新 *ApplicationInsights.config* 中的 *InstrumentationKey* 元素。

### <a name="step-3-make-sure-the-minidump-uploader-is-started"></a>步骤 3：确保已启动小型转储上传程序
在快照收集器日志中，搜索 *UploaderStart*（在搜索文本框中键入 UploaderStart）。 应会看到一个事件指出快照收集器监视到第一个异常的时间。 如果此事件不存在，请检查其他日志了解详细信息。 解决此问题的一种可能方法是重启应用程序。

### <a name="step-4-make-sure-snapshot-collector-expressed-its-intent-to-collect-snapshots"></a>步骤 4：确保快照收集器表达了收集快照的意向
在快照收集器日志中，搜索 *RequestSnapshots*（在搜索文本框中键入 ```RequestSnapshots```）。  如果此事件不存在，请检查配置，例如 *ThresholdForSnapshotting*，它指示了在开始收集快照之前可能发生的特定异常数目。

### <a name="step-5-make-sure-that-snapshot-is-not-disabled-due-to-memory-protection"></a>步骤 5：确保快照未因内存保护而被禁用
为了保护应用程序的性能，仅当具有正常的内存缓冲区时，才会捕获快照。 在快照收集器日志中，搜索“CannotSnapshotDueToMemoryUsage”。 事件的自定义数据中会提供该事件的详细原因。 如果应用程序在 Azure Web 应用中运行，可能会受到严格限制， 因为在符合特定的内存规则时，Azure Web 应用会重启你的应用。 可以尝试将服务计划纵向扩展为具有更多内存的计算机，以解决此问题。

### <a name="step-6-make-sure-snapshots-were-captured"></a>步骤 6：确保已捕获快照
在快照收集器日志中搜索 ```RequestSnapshots```。  如果没有任何事件，请检查配置，例如 ```ThresholdForSnapshotting```，它指示收集快照之前发生的特定异常数目。

### <a name="step-7-make-sure-snapshots-are-uploaded-correctly"></a>步骤 7：确保正确上传快照
在快照收集器日志中搜索 ```UploadSnapshotFinish```。  如果此事件不存在，请向 snapshothelp@microsoft.com 发送电子邮件并随附 Application Insights 的检测密钥。 如果此事件存在，请打开某条日志，并复制自定义数据中的“SnapshotId”值。 然后搜索该值。 这样即可找到对应于该快照的异常。 然后单击该异常并打开调试快照。 （如果没有对应的异常，异常遥测数据可能会由于数量较多而被采样，此时，请尝试另一个 snapshotId。）

![查找 SnapshotId 的屏幕截图](./media/app-insights-troubleshoot-snapshot-debugger/002.png)

![打开异常的屏幕截图](./media/app-insights-troubleshoot-snapshot-debugger/004b.png)

![打开调试快照的屏幕截图](./media/app-insights-troubleshoot-snapshot-debugger/003.png)

## <a name="snapshot-view-local-variables-are-not-complete"></a>快照视图中的局部变量不完整

缺少某些本地变量。 如果应用程序正在运行发行代码，编译器会优化掉某些变量。 例如：

```csharp
    const int a = 1; // a will be discarded by compiler and the value 1 will be inline.
    Random rand = new Random();
    int b = rand.Next() % 300; // b will be discarded and the value will be directly put to the 'FindNthPrimeNumber' call stack.
    long primeNumber = FindNthPrimeNumber(b);
```

如果你的情况与此不同，可能是存在 bug。 请向 snapshothelp@microsoft.com 发送电子邮件并连同代码片段一起随附 Application Insights 的检测密钥。

## <a name="snapshot-view-cannot-obtain-value-of-the-local-variable-or-argument"></a>快照视图：无法获取局部变量或参数的值
请确保已安装 [Application Insights 站点扩展](https://www.siteextensions.net/packages/Microsoft.ApplicationInsights.AzureWebSites/)。 如果问题持续出现，请向 snapshothelp@microsoft.com 发送电子邮件并随附 Application Insights 的检测密钥。
