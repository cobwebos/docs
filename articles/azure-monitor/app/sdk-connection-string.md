---
title: Azure 应用程序见解中的连接字符串 |微软文档
description: 如何使用连接字符串。
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 7b049c04913d3415074f46b9d90ec34be874a2da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136700"
---
# <a name="connection-strings"></a>连接字符串

## <a name="overview"></a>概述

连接字符串为应用程序洞察用户提供单个配置设置，无需多个代理设置。 对于希望将数据发送到监视服务的 Intranet Web 服务器、主权或混合云环境非常有用。

键值对为用户提供了一种为每个应用程序见解 （AI） 服务/产品定义前缀后缀组合的简单方法。

> [!IMPORTANT]
> 我们不建议同时设置连接字符串和检测密钥。 如果用户同时设置两者，则以上次设置者为准。 


## <a name="scenario-overview"></a>方案概述 

我们可视化的为客户情景具有最大影响：

- 防火墙异常或代理重定向 

    如果需要对 Intranet Web 服务器进行监视，我们较早的解决方案要求客户将单个服务终结点添加到您的配置中。 有关详细信息，请参阅[此处](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#can-i-monitor-an-intranet-web-server)。 
    通过将此工作量减少到单个设置，连接字符串提供了更好的替代方法。 一个简单的前缀，后缀修正允许自动填充和重定向所有终结点到正确的服务。 

- 主权云或混合云环境

    用户可以将数据发送到定义的 Azure[政府区域](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)。
    连接字符串允许您为 Intranet 服务器或混合云设置定义终结点设置。 

## <a name="getting-started"></a>入门

### <a name="finding-my-connection-string"></a>正在查找连接字符串？

连接字符串显示在应用程序见解资源的"概述"边栏选项卡上。

![概述边栏选项卡上的连接字符串](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>架构

#### <a name="max-length"></a>最大长度

连接的最大支持长度为 4096 个字符。

#### <a name="key-value-pairs"></a>键值对

连接字符串由表示为按分号分隔的键值对的设置列表组成：`key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>语法

- `InstrumentationKey`（如：00000-000-000-000-00000000000000） 连接字符串是**必填**字段。
- `Authorization`（例如：伊基）（此设置是可选的，因为今天我们仅支持 ikey 授权。
- `EndpointSuffix`（例如：applicationinsights.azure.cn）设置终结点后缀将指示要连接到哪个 Azure 云的 SDK。 SDK 将组装各个服务的终结点的其余部分。
- 显式终结点。
  可以在连接字符串中显式重写任何服务。
   - `IngestionEndpoint`（例如：https://dc.applicationinsights.azure.com)
   - `LiveEndpoint`（例如：https://live.applicationinsights.azure.com)
   - `ProfilerEndpoint`（例如：https://profiler.applicationinsights.azure.com)
   - `SnapshotEndpoint`（例如：https://snapshot.applicationinsights.azure.com)

#### <a name="endpoint-schema"></a>端点架构

`<prefix>.<suffix>`
- 前缀：定义服务。 
- 后缀：定义公共域名。

##### <a name="valid-suffixes"></a>有效的后缀

下面是有效的后缀列表 
- applicationinsights.azure.cn
- applicationinsights.us


另请参阅：https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>有效的前缀

- [遥测引入](./app-insights-overview.md)：`dc`
- [实时指标](./live-stream.md)：`live`
- [探查器](./profiler-overview.md)：`profiler`
- [快照](./snapshot-debugger.md)：`snapshot`



## <a name="connection-string-examples"></a>连接字符串示例


### <a name="minimal-valid-connection-string"></a>最小有效连接字符串

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

在此示例中，仅设置了检测密钥。

- 授权方案默认为"ikey" 
- 仪器检测密钥：0000000-0000-0000-000000000000000000
- 区域服务 URI 基于 SDK[默认值](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6)，并将连接到公共全局 Azure：
   - 摄入：https://dc.services.visualstudio.com/
   - 实时指标：https://rt.services.visualstudio.com/
   - 分析器：https://agent.azureserviceprofiler.net/
   - 调试：https://agent.azureserviceprofiler.net/  



### <a name="connection-string-with-endpoint-suffix"></a>具有终结点后缀的连接字符串

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

在此示例中，此连接字符串指定终结点后缀，SDK 将构造服务终结点。

- 授权方案默认为"ikey" 
- 仪器检测密钥：0000000-0000-0000-000000000000000000
- 区域服务 URI 基于提供的终结点后缀： 
   - 摄入：https://dc.ai.contoso.com
   - 实时指标：https://live.ai.contoso.com
   - 分析器：https://profiler.ai.contoso.com 
   - 调试：https://snapshot.ai.contoso.com   



### <a name="connection-string-with-explicit-endpoint-overrides"></a>具有显式终结点覆盖的连接字符串 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

在此示例中，此连接字符串为每个服务指定显式覆盖。 SDK 将使用未经修改提供的确切终结点。

- 授权方案默认为"ikey" 
- 仪器检测密钥：0000000-0000-0000-000000000000000000
- 区域服务 URI 基于显式覆盖值： 
   - 摄入： https：\//custom.com:111/
   - 实时指标： https：\//custom.com:222/
   - 探查器： https：\//custom.com:333/ 
   - 调试器： https：\//custom.com:444/   


## <a name="how-to-set-a-connection-string"></a>如何设置连接字符串

以下 SDK 版本支持连接字符串：
- .NET 和 .NET 核心 v2.12.0
- Java v2.5.1
- Javascript v2.3.0
- NodeJS v1.5.0
- Python v1.0.0

连接字符串可以通过代码、环境变量或配置文件进行设置。



### <a name="environment-variable"></a>环境变量

- 连接字符串： `APPLICATIONINSIGHTS_CONNECTION_STRING`

### <a name="net-sdk-example"></a>.网络 SDK 示例

遥测配置.连接字符串：https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

.Net 显式设置：
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

.净配置文件：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```


NetCore 配置.json： 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


### <a name="java-sdk-example"></a>Java SDK 示例


Java 显式设置：
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

### <a name="javascript-sdk-example"></a>Javascript SDK 示例

重要提示：Javascript 不支持使用环境变量。

使用代码段：

```javascript
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```


手动设置：
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

### <a name="node-sdk-example"></a>节点 SDK 示例

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

### <a name="python-sdk-example"></a>Python SDK 示例

我们建议用户设置环境变量。

要显式设置连接字符串：

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>后续步骤

在运行时开始使用：

* [Azure VM 和 Azure 虚拟机规模集的托管 IIS 应用](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [IIS 服务器](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Web 应用](../../azure-monitor/app/azure-web-apps.md)

在开发时开始使用：

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)
