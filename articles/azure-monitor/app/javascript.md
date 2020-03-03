---
title: 适用于 JavaScript web 应用的 Azure 应用程序 Insights
description: 获取页面视图和会话计数、web 客户端数据、单页面应用程序（SPA）和跟踪使用模式。 检测 JavaScript 网页中的异常和性能问题。
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: 600ca893e6d6b81fe24626a99cc1f6de80efb3e8
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228134"
---
# <a name="application-insights-for-web-pages"></a>适用于网页的 Application Insights

了解网页或应用的性能和使用情况。 如果你将[Application Insights](app-insights-overview.md)添加到页面脚本，则会获取页面加载和 ajax 调用、计数以及浏览器异常和 ajax 故障的详细信息以及用户和会话计数的计时。 所有这些信息可按页面、客户端 OS 和浏览器版本、地理位置和其他维度细分。 可以针对失败计数或页面加载缓慢情况设置警报。 并且通过在 JavaScript 代码中插入跟踪调用，可以跟踪网页应用程序的不同功能的使用情况。

可以在任何网页中使用 Application Insights - 刚刚添加了 JavaScript 的简短片段。 如果 web 服务是[Java](java-get-started.md)或[ASP.NET](asp-net.md)，则可以将服务器端 Sdk 与客户端 JavaScript SDK 结合使用，以获得对应用性能的端到端理解。

## <a name="adding-the-javascript-sdk"></a>添加 JavaScript SDK

1. 首先需要 Application Insights 的资源。 如果还没有资源和检测密钥，请按照[创建新的资源说明](create-new-resource.md)进行操作。
2. 从想要将 JavaScript 遥测发送到的资源中复制检测密钥。
3. 通过以下两个选项之一将 Application Insights JavaScript SDK 添加到网页或应用：
    * [npm 安装程序](#npm-based-setup)
    * [JavaScript 代码片段](#snippet-based-setup)

> [!IMPORTANT]
> 仅使用一种方法将 JavaScript SDK 添加到应用程序。 如果使用 NPM 安装程序，请不要使用代码片段，反之亦然。

> [!NOTE]
> NPM 安装程序会将 JavaScript SDK 作为依赖项安装到项目中，启用 IntelliSense，而代码片段会在运行时获取 SDK。 两者都支持相同的功能。 但是，需要更多自定义事件和配置的开发人员通常会选择 NPM 设置，而用户则需要快速启用现成的 web analytics 来选择代码片段。

### <a name="npm-based-setup"></a>基于 npm 的设置

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>基于代码段的设置

如果你的应用程序不使用 npm，则可以通过将此代码片段粘贴到每个页面的顶部，直接使用 Application Insights 来检测网页。 最好是 `<head>` 部分中的第一个脚本，以便它可以监视所有依赖项的任何潜在问题。 如果使用的是 Blazor 服务器应用，请在 "`<head>`" 部分的文件 `_Host.cshtml` 顶部添加代码片段。

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>将遥测发送到 Azure 门户

默认情况下，Application Insights JavaScript SDK autocollects 许多遥测项，有助于确定应用程序的运行状况和基础用户体验。 其中包括：

- 在应用中未捕获到的**异常**，包括有关
    - 堆栈跟踪
    - 异常详细信息和与此错误相关的消息
    - 行 & 错误的列数
    - 引发错误的 URL
- 应用**XHR**和**Fetch**发出的**网络依赖请求**（默认情况下，禁用提取集合）请求，包括有关
    - 依赖项源的 Url
    - 用于请求依赖项的命令 & 方法
    - 请求的持续时间
    - 请求的结果代码和成功状态
    - 发出请求的用户的 ID （如果有）
    - 发出请求的相关上下文（如果有）
- **用户信息**（例如，位置、网络、IP）
- **设备信息**（例如，浏览器、OS、版本、语言、分辨率、型号）
- **会话信息**

### <a name="telemetry-initializers"></a>遥测初始值设定项
遥测初始值设定项用于修改收集的遥测的内容，然后从用户的浏览器发送。 它们还可用于阻止发送某些遥测，方法是返回 `false`。 可以将多个遥测初始值设定项添加到 Application Insights 实例，并按添加它们的顺序执行这些初始值设定项。

`addTelemetryInitializer` 的输入参数是采用[`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer)作为参数并返回 `boolean` 或 `void`的回调。 如果返回 `false`，则不会发送遥测项，否则，它将继续到下一个遥测初始值设定项（如果有），或者发送到遥测集合终结点。

使用遥测初始值设定项的示例：
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```
## <a name="configuration"></a>配置
大多数配置字段的名称都可以默认为 false。 除 `instrumentationKey`之外，所有字段都是可选的。

| 名称 | 默认 | 说明 |
|------|---------|-------------|
| instrumentationKey | Null | **必需**<br>从 Azure 门户获取的检测密钥。 |
| accountId | Null | 可选帐户 ID （如果你的应用将用户分组到帐户中）。 无空格、逗号、分号、等于或竖线 |
| sessionRenewalMs | 1800000 | 如果用户处于非活动状态的时间长度（以毫秒为单位），则会记录会话。 默认值为 30 分钟 |
| sessionExpirationMs | 86400000 | 如果会话持续时间超过此时间，则会记录会话。 默认值为24小时 |
| maxBatchSizeInBytes | 10000 | 遥测批处理的最大大小。 如果批超过此限制，则会立即发送此限制，并启动新的批处理 |
| maxBatchInterval | 15000 | 发送前批处理遥测的时间（毫秒） |
| disableExceptionTracking | false | 如果为 true，则不 autocollected。 默认值为 false。 |
| disableTelemetry | false | 如果为 true，则不收集或发送遥测数据。 默认值为 false。 |
| enableDebug | false | 如果为 true，则**内部**调试数据将作为异常引发，**而不**是记录，不管 SDK 日志记录设置如何。 默认值为 false。 <br>***注意：*** 如果启用此设置，则在发生内部错误时，将导致丢弃遥测数据。 这对于快速识别配置或使用 SDK 的问题很有用。 如果你不希望在调试时丢失遥测数据，请考虑使用 `consoleLoggingLevel` 或 `telemetryLoggingLevel` 而不是 `enableDebug`。 |
| loggingLevelConsole | 0 | 将**内部**Application Insights 错误记录到控制台。 <br>0：关， <br>1：仅严重错误， <br>2：所有内容（错误 & 警告） |
| loggingLevelTelemetry | 1 | 将**内部**Application Insights 错误作为遥测数据发送。 <br>0：关， <br>1：仅严重错误， <br>2：所有内容（错误 & 警告） |
| diagnosticLogInterval | 10000 | internal内部日志记录队列的轮询间隔（毫秒） |
| samplingPercentage | 100 | 要发送的事件百分比。 默认值为100，表示发送所有事件。 如果希望为大型应用程序保留数据上限，请设置此项。 |
| autoTrackPageVisitTime | false | 如果为 true，则在 pageview 上，将跟踪以前检测的页面的视图时间并将其作为遥测发送，并为当前 pageview 启动新的计时器。 默认值为 false。 |
| disableAjaxTracking | false | 如果为 true，则 Ajax 调用是不 autocollected 的。 默认值为 false。 |
| disableFetchTracking | true | 如果为 true，则不 autocollected 提取请求。 默认值为 true |
| overridePageViewDuration | false | 如果为 true，则在调用 trackPageView 时，trackPageView 的默认行为将更改为记录页面视图持续时间间隔的结束时间。 如果为 false 且不向 trackPageView 提供自定义持续时间，则使用导航计时 API 计算页面视图性能。 默认值为 false。 |
| maxAjaxCallsPerView | 500 | 默认 500-控制每个页面视图将监视的 Ajax 调用数。 设置为-1 可监视页面上所有（无限制） Ajax 调用。 |
| disableDataLossAnalysis | true | 如果为 false，则将在启动时检查尚未发送的项目的内部遥测发件人缓冲区。 |
| disableCorrelationHeaders | false | 如果为 false，SDK 会将两个标头（"请求 Id" 和 "请求上下文"）添加到所有依赖项请求，以将它们与服务器端上的对应请求关联起来。 默认值为 false。 |
| correlationHeaderExcludedDomains |  | 禁用特定域的相关标头 |
| correlationHeaderDomains |  | 为特定域启用相关标头 |
| disableFlushOnBeforeUnload | false | 默认值为 false。 如果为 true，则在 onBeforeUnload 事件触发器时，不会调用 flush 方法 |
| enableSessionStorageBuffer | true | 默认值为 true。 如果为 true，则将所有未发送的遥测数据存储在会话存储中。 在页面加载时还原缓冲区 |
| isCookieUseDisabled | false | 默认值为 false。 如果为 true，则 SDK 将不会存储或读取 cookie 中的任何数据。|
| cookieDomain | Null | 自定义 cookie 域。 如果要跨子域共享 Application Insights cookie，这会很有帮助。 |
| isRetryDisabled | false | 默认值为 false。 如果为 false，则重试（206（部分成功）、408（超时）、429（请求过多）、500（内部服务器错误）、503（服务不可用）和0（仅在检测到的情况下为脱机） |
| isStorageUseDisabled | false | 如果为 true，则 SDK 将不会存储或读取本地和会话存储中的任何数据。 默认值为 false。 |
| isBeaconApiDisabled | true | 如果为 false，SDK 将使用[信标 API](https://www.w3.org/TR/beacon)发送所有遥测数据 |
| onunloadDisableBeacon | false | 默认值为 false。 当选项卡关闭时，SDK 将使用[信标 API](https://www.w3.org/TR/beacon)发送所有剩余的遥测数据 |
| sdkExtension | Null | 设置 sdk 扩展名称。 仅允许使用字母字符。 此扩展名称作为前缀添加到 "sdkVersion" 标记（例如，"ext_javascript： 2.0.0"）。 默认值为 null。 |
| isBrowserLinkTrackingEnabled | false | 默认值为 false。 如果为 true，则 SDK 将跟踪所有[浏览器链接](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink)请求。 |
| appId | Null | AppId 用于在客户端与服务器端请求之间进行的 AJAX 依赖项之间的相关性。 启用信标 API 后，不能自动使用，但可以在配置中手动设置。 默认值为 null |
| enableCorsCorrelation | false | 如果为 true，SDK 会将两个标头（"请求 Id" 和 "请求上下文"）添加到所有 CORS 请求，以将传出 AJAX 依赖项与服务器端上的对应请求关联起来。 默认值为 false |
| namePrefix | 未定义 | 一个可选值，将用作 localStorage 和 cookie 名称的名称后缀。
| enableAutoRouteTracking | false | 在单页面应用程序（SPA）中自动跟踪路由更改。 如果为 true，则每个路由更改会将新的 Pageview 发送到 Application Insights。 哈希路由更改（`example.com/foo#bar`）也记录为新的页面视图。
| enableRequestHeaderTracking | false | 如果为 true，则跟踪 AJAX & 提取请求标头，默认值为 false。
| enableResponseHeaderTracking | false | 如果为 true，则跟踪 AJAX & 提取请求的响应标头，默认值为 false。
| distributedTracingMode | `DistributedTracingModes.AI` | 设置分布式跟踪模式。 如果设置了 AI_AND_W3C 模式或 W3C 模式，则将生成 W3C 跟踪上下文标头（traceparent/tracestate），并将其包含在所有传出请求中。 提供 AI_AND_W3C 是为了与任何旧式 Application Insights 检测服务的后向兼容性。

## <a name="single-page-applications"></a>单页应用程序

默认情况下，此 SDK 将**不**会处理在单页面应用程序中发生的基于状态的路由更改。 若要为你的单页面应用程序启用自动路由更改跟踪，你可以将 `enableAutoRouteTracking: true` 添加到你的安装配置。

目前，我们提供了一个单独的[响应插件](#react-extensions)，你可以使用此 SDK 对其进行初始化。 它还将为你完成路由更改跟踪，并收集[其他响应特定遥测](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)数据。

## <a name="react-extensions"></a>响应扩展

| 扩展 |
|---------------|
| [React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [React Native](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>浏览浏览器/客户端数据

可以通过转到 "**指标**" 并添加感兴趣的单个度量值来查看浏览器/客户端数据： 

![](./media/javascript/page-view-load-time.png)

还可以通过门户中的浏览器体验查看 JavaScript SDK 中的数据。

选择 "**浏览器**"，然后选择 "**故障**" 或 "**性能**"。

![](./media/javascript/browser.png)

### <a name="performance"></a>性能 

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>依赖项

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analytics 

若要查询 JavaScript SDK 收集的遥测数据，请**在 "日志（分析）** " 按钮中选择 "查看"。 通过添加 `client_Type == "Browser"`的 `where` 语句，你只会看到来自 JavaScript SDK 的数据，其他 Sdk 收集的任何服务器端遥测都将被排除。
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>源映射支持

可以在 Azure 门户中 unminified 异常遥测的缩小调用堆栈。 "异常详细信息" 面板上的所有现有集成都适用于新的 unminified 调用堆栈。 拖放源映射 unminifying 支持所有现有和未来的 JS Sdk （+ node.js），因此您无需升级 SDK 版本。 若要查看 unminified 调用堆栈，
1. 在 Azure 门户中选择一个异常遥测项以查看其 "端到端事务详细信息"
2. 确定哪些源映射对应于此调用堆栈。 源映射必须与堆栈帧的源文件匹配，但带有后缀 `.map`
3. 将源映射拖放到 Azure 门户中的调用堆栈上 ![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web 基本

对于轻型体验，可以改为安装的基本版本 Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
此版本提供最少的功能和功能，并根据你的需要，根据需要进行构建。 例如，它不执行任何 autocollection （未捕获的异常、AJAX，等等）。 用于发送某些遥测类型的 Api （例如 `trackTrace`、`trackException`等）未包含在此版本中，因此你需要提供自己的包装。 唯一可用的 API 是 `track`。 下面是一个[示例](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html)。

## <a name="examples"></a>示例

有关可运行的示例，请参阅[Application Insights JAVASCRIPT SDK 示例](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>从旧版本的 Application Insights 升级

SDK V2 版本中的重大更改：
- 为了实现更好的 API 签名，某些 API 调用（例如 trackPageView 和 trackException）已更新。 不支持在 Internet Explorer 8 和早期版本的浏览器中运行。
- 由于数据架构更新，遥测信封具有字段名称和结构更改。
- 已将 `context.operation` 移动到 `context.telemetryTrace`中。 还更改了某些字段（`operation.id` --> `telemetryTrace.traceID`）。
  - 若要手动刷新当前 pageview ID （例如，在 SPA 应用中），请使用 `appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()`。
    > [!NOTE]
    > 若要使跟踪 ID 保持唯一，你以前使用 `Util.newId()`，现在请使用 `Util.generateW3CId()`。 最终最终都是操作 ID。

如果你使用的是当前 application insights 生产 SDK （1.0.20），并且想要查看新的 SDK 在运行时是否正常工作，请根据当前的 SDK 加载方案更新 URL。

- 通过 CDN 方案下载：更新当前用于指向以下 URL 的代码片段：
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- npm 方案：调用 `downloadAndSetup` 从 CDN 下载完整的 Applicationinsights.config 脚本并将其初始化为检测密钥：

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

在内部环境中进行测试，以验证监视遥测是否按预期方式工作。 如果一切正常，请将 API 签名适当地更新为 SDK V2 版本，并部署到生产环境中。

## <a name="sdk-performanceoverhead"></a>SDK 性能/开销

只需 25 KB gzip 压缩过，初始化大约需要15毫秒的时间，Application Insights 就会向你的网站添加可忽略的 loadtime 数量。 通过使用代码段，可以快速加载库的最小组件。 同时，会在后台下载完整的脚本。

在从 CDN 下载脚本时，页面的所有跟踪都将排队。 下载的脚本异步初始化完成后，将跟踪已排队的所有事件。 因此，你不会在页面的整个生命周期内丢失任何遥测数据。 此安装过程为页面提供了一个无缝分析系统，对用户不可见。

> 摘要：
> - **25 KB** gzip 压缩过
> - **15 ms**总体初始化时间
> - 在页面生命周期中**零**跟踪丢失

## <a name="browser-support"></a>浏览器支持

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
最新✔ |  Firefox 最新✔ | IE 9 + & Edge ✔ | Opera 最新✔ | Safari 最新✔ |

## <a name="open-source-sdk"></a>开源 SDK

Application Insights JavaScript SDK 是开放源代码，用于查看源代码或参与项目，请访问[官方 GitHub 存储库](https://github.com/Microsoft/ApplicationInsights-JS)。

## <a name="next"></a> 后续步骤
* [跟踪使用情况](usage-overview.md)
* [自定义事件和指标](api-custom-events-metrics.md)
* [Build-measure-learn](usage-overview.md)
