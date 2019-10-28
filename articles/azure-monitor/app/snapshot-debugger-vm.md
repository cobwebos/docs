---
title: 为 Azure Service Fabric、云服务和虚拟机中的 .NET 应用启用 Snapshot Debugger |Microsoft Docs
description: 为 Azure Service Fabric、云服务和虚拟机中的 .NET 应用启用 Snapshot Debugger
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 98ceeeb8efb11e2caeffadeb48270c419cc7e430
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899816"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>为 Azure Service Fabric、云服务和虚拟机中的 .NET 应用启用 Snapshot Debugger

如果 ASP.NET 或 ASP.NET 核心应用程序 Azure App Service 中运行，强烈建议[通过 Application Insights 门户页启用 Snapshot Debugger](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)。 但是，如果应用程序需要自定义的 Snapshot Debugger 配置或 .NET core 的预览版本，则***除了***[通过 Application Insights 门户启用的说明外，还应遵循此说明页面](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)。

如果你的应用程序在 Azure Service Fabric、云服务、虚拟机或本地计算机中运行，则应使用以下说明。 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>为 ASP.NET 应用程序配置快照集合

1. 如果尚未启用，请[在 Web 应用中启用 Application Insights](../../azure-monitor/app/asp-net.md)。

2. 将 [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包添加到应用。

3. 如果需要，自定义添加到[applicationinsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)的 Snapshot Debugger 配置。默认 Snapshot Debugger 配置主要为空，所有设置都是可选的。 下面是一个示例，显示等效于默认配置的配置：

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. 仅当向 Application Insights 报告了异常时，才收集快照。 在某些情况下（例如，.NET 平台为较早版本时），可能需要[配置异常收集](../../azure-monitor/app/asp-net-exceptions.md#exceptions)，才能在门户的查看附带快照的异常。


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>使用 ASP.NET Core 2.0 或更高版本的应用程序配置快照集合

1. 如果尚未启用，请[在 ASP.NET Core Web 应用中启用 Application Insights](../../azure-monitor/app/asp-net-core.md)。

    > [!NOTE]
    > 请确保应用程序引用 2.1.1 版或更新版本的 Microsoft.ApplicationInsights.AspNetCore 包。

2. 将 [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包添加到应用。

3. 修改应用程序的 `Startup` 类，添加并配置快照收集器的遥测处理器。
    1. 如果使用了[applicationinsights.config. Microsoft.applicationinsights.snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包版本1.3.5 或更高版本，请将以下 using 语句添加到 `Startup.cs`。

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       将以下项添加到 `Startup.cs``Startup` 类中的 ConfigureServices 方法的末尾。

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. 如果使用了[applicationinsights.config. Microsoft.applicationinsights.snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包版本1.3.4 或更低版本，请将以下 using 语句添加到 `Startup.cs`。

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```
    
       将以下 `SnapshotCollectorTelemetryProcessorFactory` 类添加到 `Startup` 类。
    
       ```csharp
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
           ...
        ```
        将 `SnapshotCollectorConfiguration` 和 `SnapshotCollectorTelemetryProcessorFactory` 服务添加到启动管道：
    
        ```csharp
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

4. 如果需要，通过将 SnapshotCollectorConfiguration 节添加到 appsettings 来自定义 Snapshot Debugger 配置。 Snapshot Debugger 配置中的所有设置都是可选的。 下面是一个示例，显示等效于默认配置的配置：

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>为其他 .NET 应用程序配置快照集合

1. 如果尚未在 Application Insights 上检测到你的应用程序，请先[启用 Application Insights 并设置检测密钥](../../azure-monitor/app/windows-desktop.md)。

2. 将 [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包添加到应用。

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

## <a name="next-steps"></a>后续步骤

- 生成可触发异常的应用程序的流量。 然后，等待10到15分钟，将快照发送到 Application Insights 的实例。
- 请参阅 Azure 门户中的[快照](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal)。
- 若要帮助解决 Snapshot Debugger 问题，请参阅[Snapshot Debugger 故障排除](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)。
