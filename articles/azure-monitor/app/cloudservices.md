---
title: 适用于 Azure 云服务的 Application Insights | Microsoft Docs
description: 使用 Application Insights 有效监视 Web 角色和辅助角色
ms.topic: conceptual
ms.date: 09/05/2018
ms.openlocfilehash: ce794a7bd18635fddfa30056ab2d675dc138097d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361816"
---
# <a name="application-insights-for-azure-cloud-services"></a>适用于 Azure 云服务的 Application Insights
[Application Insights][start]可以通过将来自 Application Insights sdk 的数据与云服务中的[Azure 诊断](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)数据相结合，监视[Azure 云服务应用](https://azure.microsoft.com/services/cloud-services/)的可用性、性能、故障和使用情况。 通过收到的有关应用在现实中的性能和有效性的反馈，可以针对每个开发生命周期确定合理的设计方向。

![“概述”仪表板](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>必备条件
开始前，需要具备：

* 一个 [Azure](https://azure.com) 订阅。 使用 Windows、XBox Live 或其他 Microsoft 云服务的 Microsoft 帐户登录。 
* Microsoft Azure Tools 2.9 或更高版本。
* Developer Analytics Tools 7.10 或更高版本。

## <a name="get-started-quickly"></a>快速入门
使用 Application Insights 监视云服务的最快、最简单方法是将服务发布到 Azure 时选择 Application Insights 选项。

![示例“诊断设置”页](./media/cloudservices/azure-cloud-application-insights.png)

此选项可在运行时检测应用，提供监视请求所需的全部遥测数据、Web 角色中的异常和依赖项。 它还可以辅助角色中的性能计数器。 应用生成的所有诊断跟踪也会发送到 Application Insights。

如果只需此选项，则操作到此完成。 

接下来的步骤是[从应用查看指标](../../azure-monitor/app/metrics-explorer.md)，[并通过分析查询数据](../../azure-monitor/app/analytics.md)。 

若要在浏览器中监视性能，还可能需要设置[可用性测试](../../azure-monitor/app/monitor-web-app-availability.md)并[将代码添加到网页](../../azure-monitor/app/javascript.md)。

下一部分将介绍以下附加选项：

* 通过不同的组件发送数据，以及在单独的资源上生成配置。
* 通过应用添加自定义遥测。

## <a name="sample-app-instrumented-with-application-insights"></a>使用 Application Insights 检测的示例应用
在此[示例应用](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)中，Application Insights 已添加到某个云服务，该服务包含 Azure 中托管的两个辅助角色。 

下一部分介绍如何以相同的方式改编你自己的云服务项目。

## <a name="plan-resources-and-resource-groups"></a>规划资源和资源组
应用中的遥测数据在 Application Insights 类型的 Azure 资源中进行存储、分析和显示。 

每个资源属于一个资源组。 资源组用于管理成本、向团队成员授予访问权限，以及在单个协调式事务中部署更新。 例如，可以[编写一个脚本](../../azure-resource-manager/templates/deploy-powershell.md)，以通过一个操作部署 Azure 云服务及其 Application Insights 监视资源。

### <a name="resources-for-components"></a>组件的资源
我们建议为应用的每个组件单独创建一个资源。 即，为每个 Web 角色和辅助角色创建一个资源。 可以单独分析每个组件，但也可以创建一个[仪表板](../../azure-monitor/app/overview-dashboard.md)，用于将所有组件中的关键图表汇总到一起，以便可以在一个视图中比较和监视资源。 

备选方法是将多个角色中的遥测数据发送到同一个资源，但[将维度属性添加到标识其源角色的每个遥测项](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)。 在此方法中，异常等指标图表通常显示不同角色的计数汇总，但你可根据需要按角色标识符将图表分段。 还可以按同一个维度筛选搜索结果。 使用这种备选方法可以方便一次性查看所有信息，但同时可能导致在角色之间产生一定的混淆。

浏览器遥测数据通常包含在与服务器端 Web 角色相同的资源中。

可将不同组件的 Application Insights 资源放在一个资源组中。 使用此方法可以轻松统一管理这些资源。 

### <a name="separate-development-test-and-production"></a>将开发、测试和生产隔开
要开发适合下一功能的自定义事件但上一版本仍有效，可将开发遥测数据发送到独立的 Application Insights 资源。 否则，可能很难在实时站点的所有流量中找到测试遥测数据。

为了避免这种情况，可以单独为系统的每个生成配置或“堆栈”（开发、测试、生产等）创建资源。 将每个生成配置的资源放在独立的资源组中。 

若要将遥测数据发送到相应的资源，可以设置 Application Insights SDK，使其根据生成配置选择不同的检测密钥。 

## <a name="create-an-application-insights-resource-for-each-role"></a>为每个角色创建 Application Insights 资源

如果你决定为每个角色单独创建资源（也许是为每个生成配置单独创建资源集），最简单的方法是在 Application Insights 门户中创建这些资源。 若要创建大量的资源，可[将创建过程自动化](../../azure-monitor/app/powershell.md)。

1. 在[Azure 门户][portal]中，选择 "**新** > **开发人员服务** > " **Application Insights**。  

    ![“Application Insights”窗格](./media/cloudservices/01-new.png)

1. 在“应用程序类型”下拉列表中，选择“ASP.NET Web 应用程序”。

每个资源由检测密钥标识。 以后若要手动配置或验证 SDK 的配置，可能需要使用此密钥。


## <a name="set-up-azure-diagnostics-for-each-role"></a>为每个角色设置 Azure 诊断
设置此选项可以使用 Application Insights 监视应用。 对于 Web 角色，此选项可提供性能监视、警报、诊断以及使用情况分析。 对于其他角色，可以搜索和监视 Azure 诊断信息，例如重启、性能计数器和对 System.Diagnostics.Trace 的调用。 

1. 在 Visual Studio 解决方案资源管理器中的“**YourCloudService>”\<“角色”下面，打开每个角色的属性。**  > 

1. 在“配置”中，选中“将诊断数据发送到 Application Insights”复选框，然后选择前面创建的 Application Insights 资源。

如果决定对每个生成配置使用不同的 Application Insights 资源，请先选择该配置。

![配置 Application Insights](./media/cloudservices/configure-azure-diagnostics.png)

这相当于将 Application Insights 检测密钥插入到名为 *ServiceConfiguration.\*.cscfg* 的文件。 下面是[示例代码](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg)。

若要改变发送到 Application Insights 的诊断信息级别，可以[直接编辑 *.cscfg* 文件](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)。

## <a name="sdk"></a>在每个项目中安装 SDK
使用此选项可将自定义的业务遥测添加到任何角色。 使用该选项可以更细致地分析应用的用法和性能。

在 Visual Studio 中，为每个云应用项目配置 Application Insights SDK。

1. 若要配置 Web 角色，请右键单击项目，并选择“配置 Application Insights”或“添加”>“Application Insights 遥测”。

1. 配置**辅助角色**： 

    a. 右键单击项目，并选择“管理 NuGet 包”。

    b. 添加[适用于 Windows Server 的 Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)。

    ![搜索“Application Insights”](./media/cloudservices/04-ai-nuget.png)

1. 将 SDK 配置为向 Application Insights 资源发送数据：

    a. 在适当的启动函数中，通过 *.cscfg* 文件中的配置设置指定检测密钥：
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    b. 针对应用中的每个角色重复“步骤 a”。 参阅示例：
   
    * [Web 角色](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [辅助角色](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [对于网页](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. 将 *ApplicationInsights.config* 文件设置为始终复制到输出目录。

   *.config* 文件中的消息会询问是否要将检测密钥放在该处。 但是，对于云应用，最好是通过 *.cscfg* 文件设置检测密钥。 此方法可确保在门户中正确识别角色。

## <a name="set-up-status-monitor-to-collect-full-sql-queries-optional"></a>设置状态监视器以收集完整的 SQL 查询（可选）

仅当要捕获 .NET Framework 上的完整 SQL 查询时，才需要执行此步骤。 

1. 在 `\*.csdef` 文件中添加每个角色的[启动任务](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)，如下所示 

    ```xml
    <Startup>
      <Task commandLine="AppInsightsAgent\InstallAgent.bat" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="ApplicationInsightsAgent.DownloadLink" value="http://go.microsoft.com/fwlink/?LinkID=522371" />
          <Variable name="RoleEnvironment.IsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
    
2. 下载[InstallAgent](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.bat)和[InstallAgent](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.ps1)，将其放入每个角色项目的 `AppInsightsAgent` 文件夹中。 请确保通过 Visual Studio 文件属性或生成脚本将这些文件复制到输出目录。

3. 在所有辅助角色上，添加环境变量： 

    ```xml
      <Environment>
        <Variable name="COR_ENABLE_PROFILING" value="1" />
        <Variable name="COR_PROFILER" value="{324F817A-7420-4E6D-B3C1-143FBED6D855}" />
        <Variable name="MicrosoftInstrumentationEngine_Host" value="{CA487940-57D2-10BF-11B2-A3AD5A13CBC0}" />
      </Environment>
    ```
    
## <a name="run-and-publish-the-app"></a>运行并发布应用

1. 运行应用并登录到 Azure。 

1. 打开前面创建的 Application Insights 资源。

   [搜索][diagnostic]中会显示每个数据点，[指标资源管理器](../../azure-monitor/app/metrics-explorer.md)中会显示聚合数据。

1. 添加更多遥测数据（请参阅以下部分），然后发布应用以获取实时诊断和使用情况反馈。 

如果没有数据，请执行以下操作：

1. 若要查看各个事件，请打开 "[搜索][diagnostic]" 磁贴。
1. 在应用中打开各个页面，以生成一些遥测数据。
1. 等待几秒，然后单击“刷新”。  

有关详细信息，请参阅 [故障排除][qna]。

## <a name="view-azure-diagnostics-events"></a>查看 Azure 诊断事件
可以在 Application Insights 中的以下位置找到 [Azure 诊断](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)信息：

* 性能计数器显示为自定义指标。 
* Windows 事件日志显示为跟踪和自定义事件。
* 应用程序日志、ETW 日志和所有诊断基础结构日志均显示为跟踪。

若要查看性能计数器和事件计数，请打开[指标资源管理器](../../azure-monitor/app/metrics-explorer.md)并添加以下图表：

![Azure 诊断数据](./media/cloudservices/23-wad.png)

若要在 Azure 诊断发送的各种跟踪日志中进行搜索，请使用[搜索](../../azure-monitor/app/diagnostic-search.md)或 [Analytics 查询](../../azure-monitor/log-query/get-started-portal.md)。 例如，假设某个未经处理的异常导致角色崩溃和回收。 该信息会在应用程序通道的 Windows 事件日志中显示。 可使用搜索来查看 Windows 事件日志错误，并获取异常的完整堆栈跟踪。 这样有助于找到问题的根本原因。

![Azure 诊断搜索](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>其他遥测数据
以下部分从应用的不同层面介绍如何获取其他遥测数据。

## <a name="track-requests-from-worker-roles"></a>从辅助角色跟踪请求
在 Web 角色中，请求模块会自动收集有关 HTTP 请求的数据。 有关如何覆盖默认收集行为的示例，请参阅[示例 MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)。 

可以像跟踪 HTTP 请求一样捕获辅助角色调用的性能。 在 Application Insights 中，请求遥测类型用于测量一个单位的可计时且可独立成功/失败的命名服务器端工作。 尽管 SDK 可以自动捕获 HTTP 请求，但你也可以插入自己的代码来跟踪针对辅助角色发出的请求。

请参阅报告请求的两个检测示例辅助角色： 
* [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>例外
有关如何从不同的 Web 应用类型收集未经处理的异常的信息，请参阅[在 Application Insights 中监视异常](../../azure-monitor/app/asp-net-exceptions.md)。

该示例 Web 角色包含 MVC5 和 Web API 2 控制器。 可使用以下处理程序捕获这两个控制器的未经处理的异常：

* [按此示例所示](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs)设置 MVC5 控制器的 [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* [按此示例所示](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs)设置 Web API 2 控制器的 [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

对于辅助角色，可通过两种方式跟踪异常：

* 使用 TrackException(ex)。
* 如果已添加 Application Insights 跟踪侦听程序 NuGet 包，可[按此示例所示](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)，使用 System.Diagnostics.Trace 来记录异常。

## <a name="performance-counters"></a>性能计数器
默认收集以下计数器：

* \Process （？？APP_WIN32_PROC？？）\% 处理器时间
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% 处理器时间

对于 Web 角色，还将收集以下计数器：

* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

可以通过编辑*applicationinsights.config*来指定其他自定义或其他 Windows 性能计数器[，如本示例中所示](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14)。

  ![性能计数器](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>辅助角色的关联遥测数据
若要获得丰富的诊断体验，可以查看请求失败或长时间延迟的原因。 使用 Web 角色，SDK 可在相关遥测数据之间自动设置关联。 

若要获取辅助角色的此视图，可以使用自定义遥测初始值设定项为所有遥测设置一个通用的 Operation.Id 上下文属性。 这样，延迟或失败问题是否由依赖项或代码造成便一目了然。 

以下是操作方法：

* [按此示例所示](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36)，将 correlationId 设置到 CallContext 中。 本例使用请求 ID 作为 correlationId。
* 添加自定义 TelemetryInitializer 实现，将 Operation.Id 设置为前面所设置的 correlationId。 有关示例，请参阅 [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)。
* 添加自定义遥测初始值设定项。 可*按此示例所示*，在 [ApplicationInsights.config](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233) 文件或代码中执行此操作。

## <a name="client-telemetry"></a>客户端遥测数据
若要获取基于浏览器的遥测数据（例如页面视图计数、页面加载时间或脚本异常），并在页面脚本中编写自定义遥测，请参阅[将 JAVASCRIPT SDK 添加到网页][client]。

## <a name="availability-tests"></a>可用性测试
若要确保你的应用程序保持活动状态并进行响应，请[设置 web 测试][availability]。

## <a name="display-everything-together"></a>统一显示所有信息
若要获得系统的整体视图，可在一个[仪表板](../../azure-monitor/app/overview-dashboard.md)中将关键的监视图表一起显示。 例如，可以固定每个角色的请求和失败次数。 

如果系统使用其他 Azure 服务（例如流分析），也可以包含这些服务的监视图表。 

如果有客户端移动应用，请使用 [App Center](../../azure-monitor/learn/mobile-center-quickstart.md)。 在 [Analytics](../../azure-monitor/app/analytics.md) 中创建查询来显示事件计数，并将事件固定到仪表板。

## <a name="example"></a>示例
[该示例](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)监视包含一个 Web 角色和两个辅助角色的服务。

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>在 Azure 云服务中运行时发生“找不到方法”异常
生成的项目是否面向 .NET 4.6？ Azure 云服务角色不能现成地支持 .NET 4.6。 运行应用之前，请先[在每个角色上安装 .NET 4.6](../../cloud-services/cloud-services-dotnet-install-dotnet.md)。

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>后续步骤
* [将 Azure 诊断配置为向 Application Insights 发送数据](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [自动创建 Application Insights 资源](../../azure-monitor/app/powershell.md)
* [自动化 Azure 诊断](../../azure-monitor/app/powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[azure]: ../../azure-monitor/app/app-insights-overview.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md 
