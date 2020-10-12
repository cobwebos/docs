---
title: Azure Application Insights 中的连接字符串 | Microsoft Docs
description: 如何使用连接字符串。
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.custom: devx-track-js, devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: f4227c28329233c7f414c6c45e4a3c1420bf47be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335154"
---
# <a name="connection-strings"></a>连接字符串

## <a name="overview"></a>概述

连接字符串为 Application Insight 用户提供了单个配置设置，无需多个代理设置。 对于希望向监视服务发送数据的 Intranet Web 服务器、主权云或混合云环境非常有用。

键值对为用户提供了一种简单的方式来为每个 Application Insights (AI) 服务/产品定义前缀后缀组合。

> [!IMPORTANT]
> 建议不要同时设置连接字符串和检测密钥。 如果用户同时设置了这两个值，则以最后设置的值为准。 


## <a name="scenario-overview"></a>方案概述 

我们在其中将影响最大的此项设置可视化的客户方案：

- 防火墙例外或代理重定向 

    如果需要监视 Intranet Web 服务器，我们的早期解决方案会要求客户将各个服务终结点添加到你的配置中。 有关详细信息，请参阅[此文](../faq.md#can-i-monitor-an-intranet-web-server)。 
    连接字符串只需进行单个设置，简化了此工作，因此是一种更好的选择。 简单的前缀后缀修正，允许自动填充所有终结点并将其重定向到正确的服务。 

- 主权云或混合云环境

    用户可以将数据发送到定义的 [Azure 政府区域](../../azure-government/compare-azure-government-global-azure.md#application-insights)。
    使用连接字符串可以定义 Intranet 服务器或混合云设置的终结点设置。 

## <a name="getting-started"></a>入门

### <a name="finding-my-connection-string"></a>如何查找我的连接字符串？

连接字符串显示在 Application Insights 资源的“概览”边栏选项卡上。

![“概览”边栏选项卡上的连接字符串](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>架构

#### <a name="max-length"></a>最大长度

连接的最大支持长度为 4096 个字符。

#### <a name="key-value-pairs"></a>键值对

连接字符串由以分号分隔的键/值对表示的设置列表组成：`key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>语法

- `InstrumentationKey`（例如：00000000-0000-0000-0000-000000000000）连接字符串是必填字段。
- `Authorization`（例如：ikey）（此设置为可选，因为目前我们仅支持 ikey 授权。）
- `EndpointSuffix`（例如：applicationinsights.azure.cn）设置终结点后缀将指示 SDK 连接到哪个 Azure 云。 SDK 将为各个服务组装终结点的其余部分。
- 显式终结点。
  任何服务均可在连接字符串中显式重写。
   - `IngestionEndpoint`（例如：`https://dc.applicationinsights.azure.com`）
   - `LiveEndpoint`（例如：`https://live.applicationinsights.azure.com`）
   - `ProfilerEndpoint`（例如：`https://profiler.applicationinsights.azure.com`）
   - `SnapshotEndpoint`（例如：`https://snapshot.applicationinsights.azure.com`）

#### <a name="endpoint-schema"></a>终结点架构

`<prefix>.<suffix>`
- 前缀：定义某个服务。 
- 后缀：定义公共域名。

##### <a name="valid-suffixes"></a>有效后缀

下面是有效后缀的列表 
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

- 授权方案默认为“ikey” 
- 检测密钥：00000000-0000-0000-0000-000000000000
- 区域服务 URI 基于 [SDK 默认值](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6)，并将连接到公共全局 Azure：
   - 引入：`https://dc.services.visualstudio.com/`
   - 实时指标：`https://rt.services.visualstudio.com/`
   - 探查器：`https://agent.azureserviceprofiler.net/`
   - 调试程序：`https://agent.azureserviceprofiler.net/`



### <a name="connection-string-with-endpoint-suffix"></a>带有终结点后缀的连接字符串

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

在此示例中，此连接字符串指定终结点后缀，SDK 将构造服务终结点。

- 授权方案默认为“ikey” 
- 检测密钥：00000000-0000-0000-0000-000000000000
- 区域服务 URI 基于所提供的终结点后缀： 
   - 引入：`https://dc.ai.contoso.com`
   - 实时指标：`https://live.ai.contoso.com`
   - 探查器：`https://profiler.ai.contoso.com`
   - 调试程序：`https://snapshot.ai.contoso.com`  



### <a name="connection-string-with-explicit-endpoint-overrides"></a>带有显式终结点重写的连接字符串 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

在此示例中，此连接字符串指定每个服务的显式重写。 SDK 将使用所提供的确切终结点而不进行修改。

- 授权方案默认为“ikey” 
- 检测密钥：00000000-0000-0000-0000-000000000000
- 区域服务 URI 基于显式重写值： 
   - 引入：`https://custom.com:111/`
   - 实时指标：`https://custom.com:222/`
   - 探查器：`https://custom.com:333/`
   - 调试程序：`https://custom.com:444/`  


## <a name="how-to-set-a-connection-string"></a>如何设置连接字符串

以下 SDK 版本支持连接字符串：
- .NET 和 .NET Core v2.12.0
- Java v2.5.1 和 Java 3.0
- JavaScript v2.3.0
- NodeJS v1.5.0
- Python v1.0.0

可以通过代码、环境变量或配置文件设置连接字符串。



### <a name="environment-variable"></a>环境变量

- 连接字符串： `APPLICATIONINSIGHTS_CONNECTION_STRING`

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

TelemetryConfiguration.ConnectionString： https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

.NET 显式设置：
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

.NET 配置文件：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

NetCore 显式设置：
```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions { ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;" };
    services.AddApplicationInsightsTelemetry(options: options);
}
```

NetCore config.json： 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)


Java (v2.5.x) 显式设置：
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

# <a name="javascript"></a>[JavaScript](#tab/js)

重要说明：JavaScript 不支持使用环境变量。

使用代码片段：

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

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

我们建议用户设置环境变量。

若要显式设置连接字符串，请执行以下代码：

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>后续步骤

在运行时开始使用：

* [Azure VM 和 Azure 虚拟机规模集的托管 IIS 应用](./azure-vm-vmss-apps.md)
* [IIS 服务器](./monitor-performance-live-website-now.md)
* [Azure Web 应用](./azure-web-apps.md)

在开发时开始使用：

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)

