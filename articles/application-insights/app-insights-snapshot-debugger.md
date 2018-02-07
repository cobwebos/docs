---
title: "适用于 .NET 应用的 Azure Application Insights 快照调试器 | Microsoft Docs"
description: "生产 .NET 应用中出现异常时会自动收集调试快照"
services: application-insights
documentationcenter: 
author: pharring
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: mbullwin
ms.openlocfilehash: 8d6f2347e06e58ec2b506aa9eaf716b3f71f3a77
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2018
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>.NET 应用中发生异常时的调试快照

发生异常时，可自动从实时 Web 应用程序收集调试快照。 快照显示发生异常时源代码和变量的状态。 [Azure Application Insights](app-insights-overview.md) 中的快照调试程序（预览版）可监视来自 Web 应用的异常遥测数据。 它可收集常出现的异常的调试快照，为诊断生产中的问题提供所需信息。 请将[快照收集器 NuGet 包](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)添加到应用程序，并按需在 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 中配置收集参数。快照显示在 Application Insights 门户中的[异常](app-insights-asp-net-exceptions.md)区域中。

可在门户中查看调试快照，查看调用堆栈并检查每个调用堆栈帧中的变量。 若要获取更强大的调试体验与源代码，请[下载用于 Visual Studio 的快照调试程序扩展](https://aka.ms/snapshotdebugger)，在 Visual Studio 2017 Enterprise 中打开快照。 在 Visual Studio 中，还可以[将快照设置为以交互方式拍摄快照](https://aka.ms/snappoint)，而无需等待异常。

快照集合可用于：
* 运行 .NET Framework 4.5 或更高版本的 .NET Framework 和 ASP.NET 应用程序。
* 在 Windows 上运行的 .NET Core 2.0 和 ASP.NET Core 2.0 应用程序。

支持以下环境：
* Azure 应用服务。
* 运行 OS 系列 4 或更高版本的 Azure 云服务。
* Windows Server 2012 R2 或更高版本上运行的 Azure Service Fabric 服务。
* 运行 Windows Server 2012 R2 或更高版本的 Azure 虚拟机。
* 运行 Windows Server 2012 R2 或更高版本的本地虚拟机或物理计算机。

> [!NOTE]
> 不支持客户端应用程序（例如，WPF、Windows 窗体或 UWP）。

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
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>50</SnapshotsPerDayLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. 仅当向 Application Insights 报告了异常时，才收集快照。 在某些情况下（例如，.NET 平台为较早版本时），可能需要[配置异常收集](app-insights-asp-net-exceptions.md#exceptions)，才能在门户的查看附带快照的异常。


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>为 ASP.NET Core 2.0 应用程序配置快照集合

1. 如果尚未启用，请[在 ASP.NET Core Web 应用中启用 Application Insights](app-insights-asp-net-core.md)。

    > [!NOTE]
    > 请确保应用程序引用 2.1.1 版或更新版本的 Microsoft.ApplicationInsights.AspNetCore 包。

2. 将 [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包添加到应用。

3. 修改应用程序的 `Startup` 类，添加并配置快照收集器的遥测处理器。

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   ...
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           private readonly IServiceProvider _serviceProvider;

           public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
               _serviceProvider = serviceProvider;

           public ITelemetryProcessor Create(ITelemetryProcessor next)
           {
               var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
               return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
           }
       }

       public Startup(IConfiguration configuration) => Configuration = configuration;

       public IConfiguration Configuration { get; }

       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           // Configure SnapshotCollector from application settings
           services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

           // Add SnapshotCollector telemetry processor.
           services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

           // TODO: Add other services your application needs here.
       }
   }
   ```

4. 通过将 SnapshotCollectorConfiguration 节添加到 appsettings.json 配置快照收集器。 例如：

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": true
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>为其他 .NET 应用程序配置快照集合

1. 如果尚未在 Application Insights 上检测到你的应用程序，请先[启用 Application Insights 并设置检测密钥](app-insights-windows-desktop.md)。

2. 将 [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包添加到应用。

3. 仅当向 Application Insights 报告了异常时，才收集快照。 可能需要修改代码才能报告。 异常处理代码取决于应用程序的结构，示例如下：
    ```csharp
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
    ```
    
## <a name="grant-permissions"></a>授予权限

Azure 订阅的所有者可以检查快照。 其他用户必须获得所有者的授权。

要授予权限，请将 `Application Insights Snapshot Debugger` 角色分配给要检查快照的用户。 订阅所有者可将该角色分配给目标 Application Insights 资源或其资源组或订阅的各个用户或组。

1. 打开“访问控制(IAM)”边栏选项卡。
1. 单击“+添加”按钮。
1. 从“角色”下拉列表中，选择“Application Insights 快照调试程序”。
1. 搜索并输入要添加的用户的名称。
1. 单击“保存”按钮将用户添加到角色。


> [!IMPORTANT]
> 快照的变量和参数值中可能包含个人信息和其他敏感信息。

## <a name="debug-snapshots-in-the-application-insights-portal"></a>在 Application Insights 门户中调试快照

如果某个快照适用于给定的异常或问题 ID，Application Insights 门户上的[异常](app-insights-asp-net-exceptions.md)区域中会显示“打开调试快照”按钮。

![异常区域中的“打开调试快照”按钮](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

在“调试快照”视图中，可以看到调用堆栈和变量窗格。 当在调用堆栈窗格中选择调用堆栈帧时，可在变量窗格中查看该函数调用的局部变量和参数。

![在门户中查看调试快照](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

快照可能包含敏感信息，默认情况下不可查看。 要查看快照，必须获取 `Application Insights Snapshot Debugger` 角色。

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>使用 Visual Studio 2017 Enterprise 调试快照
1. 单击“下载快照”按钮，下载可在 Visual Studio 2017 Enterprise 中打开的 `.diagsession` 文件。 

2. 要打开 `.diagsession` 文件，必须先[下载并安装用于 Visual Studio 的快照调试程序扩展](https://aka.ms/snapshotdebugger)。

3. 打开快照文件后，将出现 Visual Studio 中的“小型转储调试”页面。 单击“调试托管代码”开始调试快照。 快照将打开到引发异常的代码行，以便可以调试进程的当前状态。

    ![在 Visual Studio 中查看调试快照](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

下载的快照中包含在 Web 应用程序服务器上找到的所有符号文件。 需要使用这些符号文件将快照数据与源代码相关联。 对于应用服务应用，请确保在发布 Web 应用时启用符号部署。

## <a name="how-snapshots-work"></a>快照的工作原理

启动应用程序时，将创建一个可在应用程序中监视快照请求的独立快照上传程序进程。 请求快照时，会在大约 10 到 20 毫秒内创建运行中进程的影子副本。 然后，将分析该影子进程并创建快照，同时，主进程会继续运行并向用户提供流量。 接下来，将快照连同查看快照所需的任何相关符号文件 (.pdb) 一起上传到 Application Insights。

## <a name="current-limitations"></a>当前限制

### <a name="publish-symbols"></a>发布符号
快照调试程序要求符号文件位于生产服务器上，这样它才能在 Visual Studio 中解码变量并提供调试体验。 默认情况下，在发布到应用服务时，Visual Studio 2017 版本 15.2 会发布适用于发行版本的符号。 在以前的版本中，需要将以下代码行添加到发布配置文件 `.pubxml`，以便在发布模式下发布符号：

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

### <a name="troubleshooting-cloud-services"></a>云服务故障排除
对于云服务中的角色而言，默认临时文件夹可能太小，无法容纳小型转储文件，从而导致丢失快照。
所需的空间取决于应用程序的总工作集量和并发快照数。
32 位 ASP.NET Web 角色的工作集通常在 200 MB 到 500 MB 之间。
你应允许至少存在两个并发快照。
例如，如果应用程序使用 1 GB 的总工作集，则应确保是否至少有 2 GB 的磁盘空间来存储快照。
按照这些步骤为云服务角色配置快照的专用本地资源。

1. 通过编辑云服务定义 (.csdf) 文件将新的本地资源添加到云服务中。 以下示例使用 5 GB 大小定义名为 `SnapshotStore` 的资源。
```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
```

2. 修改角色的 `OnStart` 方法以添加指向 `SnapshotStore` 本地资源的环境变量。
```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
```

3. 更新角色的 ApplicationInsights.config 文件以重写 `SnapshotCollector` 使用的临时文件夹位置
```xml
  <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
  </TelemetryProcessors>
```

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

* [在代码中设置捕捉点](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications)，无需等待出现异常即可获取快照。
* [诊断 Web 应用中的异常](app-insights-asp-net-exceptions.md)介绍了如何在 Application Insights 中显示更多的异常。 
* [智能检测](app-insights-proactive-diagnostics.md)可自动发现性能异常。
