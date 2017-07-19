---
title: "适用于 .NET 应用的 Azure Application Insights 快照调试器 | Microsoft Docs"
description: "生产 .NET 应用中出现异常时会自动收集调试快照"
services: application-insights
documentationcenter: 
author: qubitron
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: a499f1d5f3a53a0cafb9056c9b5a4d164f80d8c5
ms.contentlocale: zh-cn
ms.lasthandoff: 07/04/2017


---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>.NET 应用中发生异常时的调试快照

发生异常时，可自动从实时 Web 应用程序收集调试快照。 快照显示发生异常时源代码和变量的状态。 [Azure Application Insights](app-insights-overview.md) 中的快照调试程序（预览版）可监视来自 Web 应用的异常遥测数据。 它可收集常出现的异常的调试快照，为诊断生产中的问题提供所需信息。 请将[快照收集器 NuGet 包](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)添加到应用程序，并按需在 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 中配置收集参数。 快照显示在 Application Insights 门户中的[异常](app-insights-asp-net-exceptions.md)区域中。

可在门户中查看调试快照，查看调用堆栈并检查每个调用堆栈帧中的变量。 若要获取更强大的调试体验与源代码，请[下载用于 Visual Studio 的快照调试程序扩展](https://aka.ms/snapshotdebugger)，在 Visual Studio 2017 Enterprise 中打开快照。

快照集合可用于：
* 运行 .NET Framework 4.5 或更高版本的 .NET Framework 和 ASP.NET 应用程序。
* 在 Windows 上运行的 .NET Core 2.0 和 ASP.NET Core 2.0 应用程序。

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>为 ASP.NET 应用程序配置快照集合

1. 如果尚未启用，请[在 Web 应用中启用 Application Insights](app-insights-asp-net.md)。

2. 将 [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包添加到应用。 

3. 查看该包在 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 中添加的默认选项：

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

4. 仅当向 Application Insights 报告了异常时，才收集快照。 在某些情况下（例如，.NET 平台为较早版本时），可能需要[配置异常收集](app-insights-asp-net-exceptions.md#exceptions)，才能在门户的查看附带快照的异常。


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>为 ASP.NET Core 2.0 应用程序配置快照集合

1. 如果尚未启用，请[在 ASP.NET Core Web 应用中启用 Application Insights](app-insights-asp-net-core.md)。

2. 将 [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包添加到应用。

3. 修改应用程序 `Startup` 类中的 `ConfigureServices` 方法，添加快照收集器的遥测处理器。
   ```C#
   using Microsoft.ApplicationInsights.SnapshotCollector;
   ...
   class Startup
   {
       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           services.AddSingleton<Func<ITelemetryProcessor, ITelemetryProcessor>>(next => new SnapshotCollectorTelemetryProcessor(next));
           // TODO: Add any other services your application needs here.
       }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>为其他 .NET 应用程序配置快照集合

1. 如果尚未在 Application Insights 上检测到你的应用程序，请先[启用 Application Insights 并设置检测密钥](app-insights-windows-desktop.md)。

2. 将 [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包添加到应用。

3. 仅当向 Application Insights 报告了异常时，才收集快照。 可能需要修改代码才能报告。 异常处理代码取决于应用程序的结构，示例如下：
   ```C#
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }

## Grant permissions

Owners of the Azure subscription can inspect snapshots. Other users must be granted permission by an owner.

To grant permission, assign the `Application Insights Snapshot Debugger` role to users who will inspect snapshots. This role can be assigned to individual users or groups by subscription owners for the target Application Insights resource or its resource group or subscription.

1. On the Application Insights navigation menu, select **Access Control (IAM)**.
2. Click **Roles** > **Application Insights Snapshot Debugger**.
3. Click **Add**, and then select a user or group.

    >[!IMPORTANT] 
    Snapshots can potentially contain personal and other sensitive information in variable and parameter values.

## Debug snapshots in the Application Insights portal

If a snapshot is available for a given exception or a problem ID, an **Open Debug Snapshot** button appears on the [exception](app-insights-asp-net-exceptions.md) in the Application Insights portal.

![Open Debug Snapshot button on exception](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

In the Debug Snapshot view, you see a call stack and a variables pane. When you select frames of the call stack in the call stack pane, you can view local variables and parameters for that function call in the variables pane.

![View Debug Snapshot in the portal](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Snapshots might contain sensitive information, and by default they are not viewable. To view snapshots, you must have the `Application Insights Snapshot Debugger` role assigned to you.

## Debug snapshots with Visual Studio 2017 Enterprise
1. Click the **Download Snapshot** button to download a `.diagsession` file, which can be opened by Visual Studio 2017 Enterprise. 

2. To open the `.diagsession` file, you must first [download and install the Snapshot Debugger extension for Visual Studio](https://aka.ms/snapshotdebugger).

3. After you open the snapshot file, the Minidump Debugging page in Visual Studio appears. Click **Debug Managed Code** to start debugging the snapshot. The snapshot opens to the line of code where the exception was thrown so that you can debug the current state of the process.

    ![View debug snapshot in Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

The downloaded snapshot contains any symbol files that were found on your web application server. These symbol files are required to associate snapshot data with source code. For App Service apps, make sure to enable symbol deployment when you publish your web apps.

## How snapshots work

When your application starts, a separate snapshot uploader process is created that monitors your application for snapshot requests. When a snapshot is requested, a shadow copy of the running process is made in about 10 to 20 minutes. The shadow process is then analyzed, and a snapshot is created while the main process continues to run and serve traffic to users. The snapshot is then uploaded to Application Insights along with any relevant symbol (.pdb) files that are needed to view the snapshot.

## Current limitations

### Publish symbols
The Snapshot Debugger requires symbol files on the production server to decode variables and to provide a debugging experience in Visual Studio. The 15.2 release of Visual Studio 2017 publishes symbols for release builds by default when it publishes to App Service. In prior versions, you need to add the following line to your publish profile `.pubxml` file so that symbols are published in release mode:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

对于 Azure 计算和其他类型，请确保符号文件位于主应用程序 .dll 所在的同一文件夹（通常为 `wwwroot/bin`）中，或者可通过当前路径访问。

### <a name="optimized-builds"></a>优化的内部版本
在某些情况下，由于生成过程中应用了优化，无法在发行内部版本中查看局部变量。

## <a name="troubleshooting"></a>故障排除

这些提示有助于解决快照调试程序的问题。

### <a name="verify-the-instrumentation-key"></a>验证检测密钥

请确保在发布的应用程序中使用正确的检测密钥。 通常，Application Insights 从 ApplicationInsights.config 文件中读取检测密钥。 请验证该值是否与在门户中出现的 Application Insights 资源的检测密钥相同。

### <a name="check-the-uploader-logs"></a>检查上传程序日志

创建快照后，将在磁盘上创建一个小型转储文件 (.dmp)。 一个单独的上传程序进程会获取该小型转储文件，并将其连同所有关联的 PDB 一起上传到 Application Insights 快照调试程序存储。 成功上传小型转储后，会从磁盘将其删除。 磁盘上会保留小型转储上传程序的日志文件。 在应用服务环境中，可在 `D:\Home\LogFiles\Uploader_*.log` 中找到这些日志。 通过应用服务的 Kudu 管理站点查找这些日志文件。

1. 在 Azure 门户中，打开应用服务应用程序。

2. 选择“高级工具”边栏选项卡，或搜索“Kudu”。
3. 单击“开始”。
4. 在“调试控制台”下拉列表框中，选择“CMD”。
5. 单击“日志文件”。

这是应出现至少一个名称以 `Uploader_` 开头，扩展名为 `.log` 的文件。 单击相应图标，下载任意日志文件或在浏览器中打开文件。
文件名中包括计算机名称。 如果应用服务实例托管于多台计算机上，则每台计算机都有单独的日志文件。 上传程序检测到一个新小型转储文件时，该文件会记录在日志文件中。 下面是成功上传的示例：

```
MinidumpUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:08.0349846Z
MinidumpUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 329.12 MB
    DateTime=2017-05-25T14:25:16.0145444Z
MinidumpUploader.exe Information: 0 : Upload successful.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2017-05-25T14:25:44.2310982Z
MinidumpUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2017-05-25T14:25:44.5435948Z
MinidumpUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:44.6095821Z
```

在之前的示例中，检测密钥是 `c12a605e73c44346a984e00000000000`。 此值应与应用程序的检测密钥相匹配。
小型转储与 ID 为 `139e411a23934dc0b9ea08a626db16c5` 的快照相关联。 稍后可以使用此 ID 在 Application Insights Analytics 中找到关联的异常遥测。

上传程序大约每 15 分钟扫描一次新 PDB。 下面是一个示例：

```
MinidumpUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\home\site\wwwroot\ for local PDBs.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\local\Temporary ASP.NET Files\root\a6554c94\e3ad6f22\assembly\dl3\81d5008b\00b93cc8_dec5d201 for local PDBs.
    DateTime=2017-05-25T15:11:38.8160276Z
MinidumpUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2017-05-25T15:11:38.8316450Z
MinidumpUploader.exe Information: 0 : Deleted PDB scan marker D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\.pdbscan.
    DateTime=2017-05-25T15:11:38.8316450Z
```

对于未托管于应用服务的应用程序，上传程序日志与小型转储位于同一文件夹：`%TEMP%\Dumps\<ikey>`（其中 `<ikey>` 是检测密钥）。

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>使用 Application Insights 搜索查找附带快照的异常

创建快照后，出现的异常标记有快照 ID。 向 Application Insights 报告异常遥测时，该快照 ID 作为自定义属性包含在内。 通过 Application Insights 中的“搜索”边栏选项卡，可借助 `ai.snapshot.id` 自定义属性找到所有遥测。

1. 在 Azure 门户中浏览到 Application Insights 资源。
2. 单击“搜索”。
3. 在“搜索”文本框中输入 `ai.snapshot.id`，然后按 Enter。

![在门户中使用快照 ID 搜索遥测](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

如果此搜索未返回任何结果，则表示在选定的时间范围内，未向 Application Insights 报告任何有关你的应用程序的快照。

若要从上传程序日志中搜索特定快照 ID，请在“搜索”框中键入该 ID。 如果快照已上传但找不到该快照的遥测，请按照以下步骤操作：

1. 请验证检测密钥，仔细检查正在查看的 Application Insights 资源是否正确。

2. 使用上传程序日志中的时间戳调整搜索的“时间范围”筛选器，以包含该时间范围。

如果仍然看不到任何带快照 ID 的异常，则表示未向 Application Insights 报告该异常遥测。 如果应用程序在拍摄快照后，但还未报告异常遥测就崩溃，则会发生这种情况。 在这种情况下，请检查 `Diagnose and solve problems` 下的应用服务日志，了解是否存在意外重启或未处理的异常。

## <a name="next-steps"></a>后续步骤

* [在代码中设置捕捉点](https://azure.microsoft.com/blog/snapshot-debugger-for-azure/)，无需等待出现异常即可获取快照。
* [诊断 Web 应用中的异常](app-insights-asp-net-exceptions.md)介绍了如何在 Application Insights 中显示更多的异常。 
* [智能检测](app-insights-proactive-diagnostics.md)可自动发现性能异常。

