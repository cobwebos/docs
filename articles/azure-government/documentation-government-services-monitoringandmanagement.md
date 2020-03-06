---
title: Azure 政府版监视 + 管理 | Microsoft Docs
description: 这里提供了针对 Azure Government 的应用程序开发使用到的功能和指南对比。
services: azure-government
cloud: gov
author: gsacavdm
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.topic: article
ms.workload: azure-government
ms.date: 12/11/2019
ms.author: gsacavdm
ms.openlocfilehash: b6f395964c286aca1bc8a1c190edeea00ba6e15c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361820"
---
# <a name="azure-government-monitoring--management"></a>Azure 政府版监视 + 管理
本文概述了对 Azure 政府版环境的监视和管理服务的变体和注意事项。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="advisor"></a>Advisor
顾问已在 Azure 政府版中正式发布。

有关详细信息，请参阅[Advisor 公共文档](../advisor/advisor-overview.md)。

### <a name="variations"></a>变体
以下顾问建议当前在 Azure 政府版中不可用：

* 高可用性
  * 将 VPN 网关配置为主动-主动，以获得连接复原能力
  * 创建 Azure 服务运行状况警报，以便在 Azure 问题影响你时收到通知
  * 配置流量管理器终结点以便进行复原
  * 使用 Azure 存储帐户的软删除
* 性能
  * 提高应用服务性能和可靠性
  * 减少流量管理器配置文件上的 DNS 生存时间，可更快地故障转移到正常运行的终结点
  * 提高 SQL 数据仓库性能
  * 使用高级存储
  * 将存储帐户迁移到 Azure 资源管理器
* 成本
  * 购买保留虚拟机实例，通过即用即付成本节省资金
  * 消除未设置的 ExpressRoute 线路
  * 删除或重新配置空闲虚拟网关

此计算用于建议你应在 Azure 政府版中使用正确大小或关闭未充分利用的虚拟机：

Advisor 会监视你的虚拟机使用情况7天，并识别低利用率的虚拟机。 如果虚拟机的 CPU 利用率为5% 或更低，并且其网络利用率小于2%，或者当前工作负荷可由较小的虚拟机大小容纳，则会将虚拟机视为低利用率。 如果你想要更积极地识别使用不足的虚拟机，你可以基于每个订阅调整 CPU 使用率规则。

## <a name="automation"></a>自动化
自动化在 Azure 政府版中已正式可用。

有关详细信息，请参阅[自动化公共文档](../automation/automation-intro.md)。

## <a name="azure-migrate"></a>Azure Migrate

Azure Migrate 在 Azure 政府版中正式发布。

有关详细信息，请参阅[Azure Migrate 文档](../migrate/migrate-overview.md)。

### <a name="variations"></a>变体
以下 Azure Migrate 功能当前在 Azure 政府版中不可用：

* 依赖关系可视化功能在 Azure 政府版中不可用，因为 Azure Migrate 依赖于 Azure 政府中当前不可用的依赖项可视化服务映射。
* 只能为 Azure 政府版创建评估作为目标区域，并使用 Azure 政府版产品/服务。

## <a name="backup"></a>备份
备份在 Azure 政府版中已正式发布。

有关详细信息，请参阅 [Azure 政府版备份](documentation-government-services-backup.md)。

## <a name="policy"></a>策略
策略已在 Azure 政府版中正式发布。

有关详细信息，请参阅 [Azure Policy](../governance/policy/overview.md)。

## <a name="site-recovery"></a>站点恢复
Azure Site Recovery 已在 Azure 政府中正式发布。

有关详细信息，请参阅[Site Recovery 商业文档](../site-recovery/site-recovery-overview.md)。

### <a name="variations"></a>变体
当前，以下站点恢复功能在 Azure 政府版中尚不可用：
* 电子邮件通知

| 站点恢复 | Classic | 资源管理器 |
| --- | --- | --- |
| VMware/物理  | GA | GA |
| Hyper-V | GA | GA |
| 站点到站点 | GA | GA |

Site Recovery 的以下 URL 在 Azure 政府中是不同的：

| Azure Public | Azure Government | 注意 |
| --- | --- | --- |
| \*.hypervrecoverymanager.windowsazure.com | \*.hypervrecoverymanager.windowsazure.us | 访问 Site Recovery 服务 |
| \*.backup.windowsazure.com  | \*.backup.windowsazure.us | 访问保护服务 |
| \*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net | 用于存储 VM 快照 |
| https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | 下载 MySQL |

## <a name="monitor"></a>监视
Azure Monitor 在 Azure 政府版中正式发布。

有关详细信息，请参阅[监视商业文档](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)。

### <a name="variations"></a>变体
以下部分详细说明了 Azure 政府版中 Azure Monitor 功能的区别和解决方法：

#### <a name="action-groups"></a>操作组
操作组在 Azure 政府版中已正式发布，与商业 Azure 并无区别。   

#### <a name="activity-log-alerts"></a>活动日志警报
活动日志警报在 Azure 政府版中已正式发布，与商业 Azure 并无区别。

#### <a name="alerts-experience"></a>警报体验
统一警报 UI 体验可用于 Azure 政府版中的指标和日志警报。

#### <a name="autoscale"></a>自动缩放
自动缩放在 Azure 政府版中正式发布。

如果使用 PowerShell/ARM/REST 调用来指定设置，请将自动缩放的 "位置" 设置为 "USGov 弗吉尼亚州" 或 "USGov 爱荷华"。 自动缩放目标的资源可以存在于任何区域中。 下面是此设置的一个示例：

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "1"
$rule2 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "2"
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1, $rule2 -Name "MyProfile"
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri https://example.com?mytoken=mytokenvalue
$notification1= New-AzAutoscaleNotification -CustomEmails myname@company.com -SendEmailToSubscriptionAdministrator -SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
Add-AzAutoscaleSetting -Location "USGov Virginia" -Name "MyScaleVMSSSetting" -ResourceGroup sdubeys-usgv -TargetResourceId /subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Web/serverFarms/ServerFarm1 -AutoscaleProfiles $profile1 -Notifications $notification1
```

如果你有兴趣实现资源的自动缩放，请使用 PowerShell/ARM/Rest 调用指定设置。

有关使用 PowerShell 的详细信息，请参阅[公共文档](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples#create-and-manage-autoscale-settings)。

#### <a name="metrics"></a>度量值
指标在 Azure 政府版中正式发布。 但是，仅通过 REST API 支持多维指标。 在 Azure 政府门户中[显示多维度量值](../azure-monitor/platform/metrics-charts.md)的功能处于预览阶段。

#### <a name="metric-alerts"></a>指标警报
第一代指标警报在 Azure 政府版和商业版 Azure 中都已正式发布。 第一代称为 "*警报（经典）* "。 现在还提供了第二代指标警报（也称为[统一的警报体验](../azure-monitor/platform/alerts-overview.md)），但与[公有云相比](../azure-monitor/platform/alerts-metric-near-real-time.md)，有一组资源提供程序有所减少。 随着时间的推移，将添加更多。 

第二代警报中当前支持的资源包括：
- Microsoft.ApiManagement/service
- Microsoft.Cache/redis
- Microsoft.Compute/virtualMachines
- Microsoft.DBforMySQL/servers
- Microsoft.DBforPostgreSQL/servers
- Microsoft.DBforMariaDB/servers
- Microsoft.Devices/IotHubs
- Microsoft.EventGrid/domains
- Microsoft.EventGrid/topics
- Microsoft.EventHub/clusters
- Microsoft.EventHub/namespaces
- Microsoft.Insights/components
- Microsoft.Network/dnsZones
- Microsoft.Network/loadBalancers
- NatGateways/网络
- PrivateEndpoints/网络
- PrivateLinkServices/网络
- Microsoft.Network/trafficManagerProfiles
- Microsoft.OperationalInsights/workspaces
- Microsoft.PowerBIDedicated/capacities
- Microsoft.Relay/namespaces
- Microsoft.ServiceBus/namespaces
- Microsoft.Sql/managedInstances
- Microsoft.Sql/servers/databases
- Microsoft.Sql/servers/elasticPools
- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices
- Microsoft.Web/hostingEnvironments/multiRolePools
- Microsoft.Web/hostingEnvironments/workerPools
- Microsoft.Web/serverfarms
- Microsoft.Web/sites
- Microsoft.Web/sites/slots

你仍可以对第二个警报版本中尚不可用的资源使用[经典警报](../azure-monitor/platform/alerts-classic.overview.md)。 

使用 PowerShell/ARM/Rest 调用创建指标警报时，需要将指标警报的 "位置" 设置为 "USGov 弗吉尼亚州" 或 "USGov 爱荷华"。 下面是此设置的一个示例：

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "USGov Virginia" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

有关使用 PowerShell 的详细信息，请参阅[公共文档](../azure-monitor/platform/powershell-quickstart-samples.md)。

## <a name="application-insights"></a>Application Insights

> [!NOTE]
> **当前不支持**对 Azure 应用服务进行基于代理/扩展的监视。 此功能推出后，将立即更新此文章。

本部分介绍在 Azure 政府版中使用 Application Insights 所需的补充配置。 若要详细了解 Azure Monitor 和 Application Insights 查看[完整文档](https://docs.microsoft.com/azure/azure-monitor/overview)。

### <a name="enable-application-insights-for-aspnet--aspnet-core-with-visual-studio"></a>使用 Visual Studio 为 ASP.NET & ASP.NET Core 启用 Application Insights

目前，对于 Azure 政府版客户，通过 Visual Studio 中的传统的 "**添加应用程序 Insights 遥测**" 按钮启用 Application Insights 的唯一方法是需要一种小型手动解决方法。 遇到相关问题的客户可能会看到错误消息，如 _"没有与此帐户关联的 Azure 订阅_" 或 _"所选订阅不支持 Application Insights，_ 即使 `microsoft.insights` 资源提供程序的状态为" 已为订阅注册 "也是如此。 若要缓解此问题，必须执行以下步骤：

1. 切换 Visual Studio 以[面向 Azure 政府云](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-vs)。

2. 创建（或现有设置） AzureGraphApiVersion 的用户环境变量，如下所示：（若要创建用户环境变量，请在**system** > **advanced System settings** > **高级** > **环境变量**中，请参阅 >  **"控制面板**"。

    `Variable name: AzureGraphApiVersion` `Variable value: 2014-04-01`

3. 根据项目类型，为[ASP.NET](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#net-with-applicationinsightsconfig)或[ASP.NET Core](#aspnet-core)进行适当的 Application Insights SDK 终结点修改。

### <a name="snapshot-debugger"></a>快照调试器

Snapshot Debugger 现在适用于 Azure 政府客户。 若要使用 Snapshot Debugger 唯一的附加先决条件是确保使用[Snapshot Collector 版本 1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5-pre-1906.403)或更高版本。 然后，只需遵循标准[Snapshot Debugger 文档](https://docs.microsoft.com/azure/azure-monitor/app/snapshot-debugger)即可。

### <a name="sdk-endpoint-modifications"></a>SDK 终结点修改

若要将数据从 Application Insights 发送到 Azure 政府区域，需要修改 Application Insights Sdk 使用的默认终结点地址。 每个 SDK 都需要略有不同的修改。

### <a name="net-with-applicationinsightsconfig"></a>具有 applicationinsights.config 的 .NET

> [!NOTE]
> 执行 SDK 升级时，将自动覆盖 applicationinsights.config 文件。 执行 SDK 升级后，请确保重新输入区域特定的终结点值。

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://quickpulse.applicationinsights.us/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.applicationinsights.us/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="aspnet-core"></a>ASP.NET Core

按如下所示修改项目中的 appsettings 文件以调整主终结点：

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "https://dc.applicationinsights.us/v2/track"
    }
  }
```

仅可通过代码设置实时度量值和配置文件查询终结点的值。 若要通过代码覆盖所有终结点值的默认值，请在 `Startup.cs` 文件的 `ConfigureServices` 方法中进行以下更改：

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel; //place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="https://quickpulse.applicationinsights.us/QuickPulseService.svc");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });

    //place in ConfigureServices method. If present, place this prior to   services.AddApplicationInsightsTelemetry("instrumentation key");
```

### <a name="azure-functions"></a>Azure Functions

请将以下包安装到函数项目中：

- Applicationinsights.config 版本2.10。0
- Applicationinsights.config. PerfCounterCollector 版本2.10。0
- Applicationinsights.config. WindowsServer. TelemetryChannel 版本2.10。0

同时，添加（或修改）函数应用程序的启动代码：

```csharp
[assembly: FunctionsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IFunctionsHostBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });
      }
  }
}
```

### <a name="java"></a>Java

修改 applicationinsights.config 文件以更改默认终结点地址。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

修改 `application.properties` 文件并添加：

```yaml
azure.application-insights.channel.in-process.endpoint-address= https://dc.applicationinsights.us/v2/track
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "https://dc.applicationinsights.us/v2/track"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"; //live metrics
appInsights.Configuration.start();
```

还可以通过环境变量配置终结点：

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "https://dc.applicationinsights.us/api/profiles/{0}/appId"
Live Metrics Endpoint: "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
   var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
      function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
   }({
      instrumentationKey:"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
      endpointUrl: "https://dc.applicationinsights.us/v2/track"
   });

   window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="firewall-exceptions"></a>防火墙例外

Azure 应用程序 Insights 服务使用多个 IP 地址。 如果要监视的应用托管在防火墙后面，可能需要知道这些 IP 地址。

> [!NOTE]
> 尽管这些地址是静态的，但我们可能随时需要更改它们。 除可用性监视和 webhook （需要入站防火墙规则）之外，所有 Application Insights 流量均表示出站流量。

### <a name="outgoing-ports"></a>传出端口
需要在服务器防火墙中打开某些传出端口，允许 Application Insights SDK 和/或状态监视器将数据发送到门户：

| 目的 | URL | IP | 端口 |
| --- | --- | --- | --- |
| 遥测 | dc.applicationinsights.us | 23.97.4.113 | 443 |

## <a name="azure-monitor-logs"></a>Azure Monitor 日志
Azure Monitor 日志在 Azure 政府版中正式发布。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="variations"></a>变体

* Azure 政府版中提供的解决方案包括：
  * [网络性能监视器（NPM）](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/) -NPM 是一种基于云的网络监视解决方案，适用于公共和混合云环境。 组织使用 NPM 来监视跨本地和云环境的网络可用性。  终结点监视器-subcapability of NPM，监视与应用程序的网络连接。

以下 Azure Monitor 日志功能和解决方案目前在 Azure 政府版中不可用。

* Microsoft Azure 中预览的解决方案包括：
  * 服务映射
  * Windows 10 升级分析解决方案
  * Application Insights 解决方案
  * Azure 网络安全组分析解决方案
  * Azure 自动化分析解决方案
  * 密钥保管库分析解决方案
* 需要更新到本地软件的解决方案和功能包括：
  * Surface Hub 解决方案
* 全球 Azure 中的预览版功能，包括：
  * 将数据导出到 Power BI
* Azure 指标和 Azure 诊断

Azure 政府版中 Azure Monitor 日志的 Url 有所不同：

| Azure Public | Azure Government | 注意 |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Log Analytics 工作区门户 |
| *workspaceId*.ods.opinsights.azure.com |*workspaceId*.ods.opinsights.azure.us |[数据收集器 API](../azure-monitor/platform/data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*.ods.opinsights.azure.us |代理通信 - [配置防火墙设置](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*.oms.opinsights.azure.us |代理通信 - [配置防火墙设置](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*.blob.core.usgovcloudapi.net |代理通信 - [配置防火墙设置](../log-analytics/log-analytics-proxy-firewall.md) |
| portal.loganalytics.io |portal.loganalytics.us |高级分析门户-[配置防火墙设置](../azure-monitor/log-query/portals.md) |
| api.loganalytics.io |api.loganalytics.us |高级分析门户-[配置防火墙设置](../azure-monitor/log-query/portals.md) |
| docs.loganalytics.io |docs.loganalytics.us |高级分析门户-[配置防火墙设置](../azure-monitor/log-query/portals.md) |
| \*.azure-automation.net |\*. azure-automation.us |Azure 自动化-[配置防火墙设置](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |
| 不可用 | *. usgovtrafficmanager.net | Azure 流量管理器-[配置防火墙设置](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |

以下 Azure Monitor 日志功能在 Azure 政府版中的行为不同：

* 若要将 System Center Operations Manager 管理组连接到 Azure Monitor 日志，需要下载并导入更新的管理包。
  + System Center Operations Manager 2016
    1. 安装 [System Center Operations Manager 2016 更新汇总 2](https://support.microsoft.com/help/3209591)。
    2. 将更新汇总 2 中附带的管理包导入 Operations Manager。 有关如何从磁盘导入管理包的信息，请参阅[如何导入 Operations Manager 管理包](https://technet.microsoft.com/library/hh212691.aspx)。
    3. 若要将 Operations Manager 连接到 Azure Monitor 日志，请按照[将 Operations Manager 连接到 Azure Monitor 日志](../azure-monitor/platform/om-agents.md)中的步骤进行操作。
  + System Center Operations Manager 2012 R2 UR3（或更高版本）/Operations Manager 2012 SP1 UR7（或更高版本）
    1. 下载并保存[更新管理包](https://go.microsoft.com/fwlink/?LinkId=828749)。
    2. 解压缩已下载的文件。
    3. 将管理包导入 Operations Manager。 有关如何从磁盘导入管理包的信息，请参阅[如何导入 Operations Manager 管理包](https://technet.microsoft.com/library/hh212691.aspx)。
    4. 若要将 Operations Manager 连接到 Azure Monitor 日志，请按照[将 Operations Manager 连接到 Azure Monitor 日志](../azure-monitor/platform/om-agents.md)中的步骤进行操作。

* 有关 Configuration Manager 中使用计算机组的详细信息，请参阅[将 Configuration Manager 连接到 Azure Monitor](../azure-monitor/platform/collect-sccm.md)。

### <a name="frequently-asked-questions"></a>常见问题
* 是否可以将数据从 Microsoft Azure 中的 Azure Monitor 日志迁移到 Azure 政府版？
  * No。 无法将数据或工作区从 Microsoft Azure 迁移到 Azure 政府版。
* 能否从 Operations Management Suite 门户中的 Microsoft Azure 和 Azure 政府工作区之间切换？
  * No。 适用于 Microsoft Azure 和 Azure 政府版的门户是独立的，并且不共享信息。

有关详细信息，请参阅[Azure Monitor 日志公共文档](../log-analytics/log-analytics-overview.md)。

## <a name="scheduler"></a>Scheduler
有关此服务以及如何使用此服务的信息，请参阅[Azure 计划程序文档](../scheduler/index.md)。

## <a name="azure-portal"></a>Azure 门户
可在[此处](https://portal.azure.us)访问 Azure 政府门户。

## <a name="azure-resource-manager"></a>Azure 资源管理器
有关此服务以及如何使用此服务的信息，请参阅[Azure 资源管理器文档](../azure-resource-manager/management/overview.md)。

## <a name="next-steps"></a>后续步骤
* 订阅[Azure 政府版博客](https://blogs.msdn.microsoft.com/azuregov/)
* 使用[azure gov](https://stackoverflow.com/questions/tagged/azure-gov)获取有关 Stack Overflow 的帮助
* 通过[Azure 政府反馈论坛](https://feedback.azure.com/forums/558487-azure-government)提供反馈或请求新功能
