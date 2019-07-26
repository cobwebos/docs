---
title: Azure Monitor - Azure Application Insights 替代默认 SDK 终结点 | Microsoft Docs
description: 修改 Azure 政府等区域的默认 Azure Application Insights SDK 终结点。
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: mbullwin
ms.openlocfilehash: c086f94a161853cba3a9ed2b98f13ea17b90dd20
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478802"
---
 # <a name="application-insights-overriding-default-endpoints"></a>替代默认终结点的 Application Insights

若要将 Application Insights 中的数据发送到某些区域，需要替代默认终结点地址。 每个 SDK 都需要进行稍有不同的修改，本文将对所有这些修改进行说明。 这些更改需要调整示例代码，并将 `QuickPulse_Endpoint_Address`、`TelemetryChannel_Endpoint_Address` 和 `Profile_Query_Endpoint_address` 的占位符值替换为特定区域的实际终结点地址。 本文末尾包含指向需要此配置的区域的终结点地址的链接。

## <a name="sdk-code-changes"></a>SDK 代码更改

### <a name="net-with-applicationinsightsconfig"></a>使用 applicationinsights.config 的 .NET

> [!NOTE]
> 每次执行 SDK 升级时，都会自动重写 applicationinsights.config 文件。 执行 SDK 升级后，请确保重新输入区域特定的终结点值。

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>Custom_QuickPulse_Endpoint_Address</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>Profile_Query_Endpoint_address</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="net-core"></a>.NET Core

按如下所示修改项目中的 appsettings.json 文件以调整主终结点：

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

实时指标和配置文件查询终结点的值只能通过代码设置。 若要通过代码替代所有终结点值的默认值，请在 `Startup.cs` 文件的 `ConfigureServices` 方法中进行以下更改：

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton(new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //place in ConfigureServices method. If present, place this prior to   services.AddApplicationInsightsTelemetry("instrumentation key");
```

### <a name="java"></a>Java

修改 applicationinsights.xml 文件以更改默认终结点地址。

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
  <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

修改 `application.properties` 文件并添加：

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "TelemetryChannel_Endpoint_Address"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "Profile_Query_Endpoint_address"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "QuickPulse_Endpoint_Address"; //live metrics
appInsights.Configuration.start();
```

也可以通过环境变量配置终结点：

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
   var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
      function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
   }({
      instrumentationKey:"INSTRUMENTATION_KEY"
      endpointUrl: "TelemetryChannel_Endpoint_Address"
   });

   window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

## <a name="regions-that-require-endpoint-modification"></a>需要修改终结点的区域

目前, 只有[Azure 政府](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)版和[azure 中国](https://docs.microsoft.com/azure/china/resources-developer-guide)版才需要修改终结点。

|地区 |  终结点名称 | ReplTest1 |
|-----------------|:------------|:-------------|
| Azure 中国 | 遥测通道 | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure 中国 | QuickPulse（实时指标） |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure 中国 | 配置文件查询 |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure 政府 | 遥测通道 |`https://dc.applicationinsights.us/v2/track` |
| Azure 政府 | QuickPulse（实时指标） |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure 政府 | 配置文件查询 |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

> [!NOTE]
> Azure 应用服务的基于无代码代理/扩展的监视在这些区域**目前不受支持**。 一旦该功能可用，本文将立即更新。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 政府的自定义修改的更多信息，请参阅有关 [Azure 监视和管理](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)的详细指南。
- 若要了解有关 Azure 中国的详细信息，请查阅 [Azure 中国 Playbook](https://docs.microsoft.com/azure/china/)。