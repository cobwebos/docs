---
title: Azure Application Insights 替代默认 SDK 终结点
description: 修改 Azure 政府等区域的默认 Azure Monitor Application Insights SDK 终结点。
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: f5bf5b07f7c058b4778e7695f150fdc71e048182
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629178"
---
# <a name="application-insights-overriding-default-endpoints"></a>替代默认终结点的 Application Insights

若要将 Application Insights 中的数据发送到某些区域，需要替代默认终结点地址。 每个 SDK 都需要进行稍有不同的修改，本文将对所有这些修改进行说明。 这些更改需要调整示例代码，并将 `QuickPulse_Endpoint_Address`、`TelemetryChannel_Endpoint_Address` 和 `Profile_Query_Endpoint_address` 的占位符值替换为特定区域的实际终结点地址。 本文末尾包含指向需要此配置的区域的终结点地址的链接。

> [!NOTE]
> [连接字符串](https://docs.microsoft.com/azure/azure-monitor/app/sdk-connection-string?tabs=net)是在 Application Insights 中设置自定义终结点的新首选方法。

---

## <a name="sdk-code-changes"></a>SDK 代码更改

# <a name="net"></a>[.NET](#tab/net)

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

# <a name="net-core"></a>[.NET Core](#tab/netcore)

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
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

# <a name="azure-functions"></a>[Azure Functions](#tab/functions)

对于 Azure Functions 现在建议使用在函数的应用程序设置中设置的[连接字符串](https://docs.microsoft.com/azure/azure-monitor/app/sdk-connection-string?tabs=net)。 若要从 "函数" 窗格中访问函数的应用程序设置，请选择 "**设置** > " "**配置** > **应用程序设置**"。 

名称： `APPLICATIONINSIGHTS_CONNECTION_STRING`值：`Connection String Value`

# <a name="java"></a>[Java](#tab/java)

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

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

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

# <a name="javascript"></a>[JavaScript](#tab/js)

```javascript
<script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      endpointUrl: "TelemetryChannel_Endpoint_Address"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

# <a name="python"></a>[Python](#tab/python)

有关修改 opencensus SDK 的引入终结点的指南，请参阅 opencensus 存储库[。](https://github.com/census-instrumentation/opencensus-python/blob/af284a92b80bcbaf5db53e7e0813f96691b4c696/contrib/opencensus-ext-azure/opencensus/ext/azure/common/__init__.py)

---

## <a name="regions-that-require-endpoint-modification"></a>需要修改终结点的区域

目前唯一需要修改终结点的区域是 [Azure 政府](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)和 [Azure 中国](https://docs.microsoft.com/azure/china/resources-developer-guide)。

|区域 |  终结点名称 | “值” |
|-----------------|:------------|:-------------|
| Azure 中国 | 遥测通道 | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure 中国 | QuickPulse（实时指标） |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure 中国 | 配置文件查询 |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | 遥测通道 |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse（实时指标） |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | 配置文件查询 |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

如果当前使用的是 [Application Insights REST API](https://dev.applicationinsights.io/
)（通常通过“api.applicationinsights.io”访问），则需要使用你所在地区的本地终结点：

|区域 |  终结点名称 | “值” |
|-----------------|:------------|:-------------|
| Azure 中国 | REST API | `api.applicationinsights.azure.cn` |
| Azure Government | REST API | `api.applicationinsights.us`|

> [!NOTE]
> Azure 应用服务的基于无代码代理/扩展的监视在这些区域**目前不受支持**。 一旦该功能可用，本文将立即更新。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 政府的自定义修改的更多信息，请参阅有关 [Azure 监视和管理](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)的详细指南。
- 若要了解有关 Azure 中国的详细信息，请查阅 [Azure 中国 Playbook](https://docs.microsoft.com/azure/china/)。
