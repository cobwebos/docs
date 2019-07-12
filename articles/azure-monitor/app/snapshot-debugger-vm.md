---
title: 启用 Azure Service Fabric、 云服务和虚拟机中的.NET 应用的快照调试器 |Microsoft Docs
description: 在 Azure Service Fabric、云服务和虚拟机中为 .NET 应用启用快照调试器
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: bfung
ms.openlocfilehash: 5ac1d1339cb8a26cc86157d4d2aa664517418095
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67617798"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>在 Azure Service Fabric、云服务和虚拟机中为 .NET 应用启用快照调试器

如果您的 ASP.NET 或 ASP.NET core 应用程序运行 Azure 应用服务中的，强烈建议对[通过 Application Insights 门户页启用 Snapshot Debugger](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)。 但是，如果你的应用程序需要自定义的快照调试程序配置或.NET core 的预览版本，然后此指令应后接***此外***有关的说明的[通过启用Application Insights 门户页](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)。

如果应用程序在 Azure Service Fabric、云服务、虚拟机或本地计算机中运行，则应使用以下说明。 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>为 ASP.NET 应用程序配置快照集合

1. 如果尚未启用，请[在 Web 应用中启用 Application Insights](../../azure-monitor/app/asp-net.md)。

2. 将 [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包添加到应用。

3. 如果需要，可自定义添加到 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 的快照调试器配置。默认快照调试器配置大多为空，并且所有设置都是可选的。 以下示例显示与默认配置等效的配置：

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


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>配置快照收集应用程序使用 ASP.NET Core 2.0 或更高版本

1. 如果尚未启用，请[在 ASP.NET Core Web 应用中启用 Application Insights](../../azure-monitor/app/asp-net-core.md)。

    > [!NOTE]
    > 请确保应用程序引用 2.1.1 版或更新版本的 Microsoft.ApplicationInsights.AspNetCore 包。

2. 将 [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包添加到应用。

3. 修改应用程序的 `Startup` 类，添加并配置快照收集器的遥测处理器。
    1. 如果[Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包的版本 1.3.5 或更高版本，则添加以下 using 语句`Startup.cs`。

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       中的 ConfigureServices 方法的末尾添加以下`Startup`类中`Startup.cs`。

       ```csharp
            services.AddSnapshotCollector((configuration) =>
            {
                IConfigurationSection section = Configuration.GetSection(nameof(SnapshotCollectorConfiguration));
                if (section.Value != null)
                {
                    section.Bind(configuration);
                }
            });

       ```
    2. 如果[Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包的版本为 1.3.4 或以下，则添加以下 using 语句`Startup.cs`。

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

4. 如果需要，可通过在 appsetings.json 中添加 SnapshotCollectorConfiguration 节来自定义快照调试器配置。 快照调试器配置中的所有设置都是可选的。 以下示例显示与默认配置等效的配置：

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

- 为应用程序生成可触发异常的流量。 然后等待 10 到 15 分钟，这样快照就会发送到 Application Insights 实例。
- 请参见 Azure 门户中的[快照](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal)。
- 快照调试程序问题疑难解答的帮助，请参阅[快照调试程序故障排除](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)。
