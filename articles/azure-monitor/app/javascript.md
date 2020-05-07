---
title: 适用于 JavaScript web 应用的 Azure 应用程序 Insights
description: 获取页面视图和会话计数、web 客户端数据、单页面应用程序（SPA）和跟踪使用模式。 检测 JavaScript 网页中的异常和性能问题。
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: 50ce0d57ec7395c69bf65e41b67f0cb005a43cb8
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854983"
---
# <a name="application-insights-for-web-pages"></a>适用于网页的 Application Insights

了解网页或应用的性能和使用情况。 如果你将[Application Insights](app-insights-overview.md)添加到页面脚本，则会获取页面加载和 ajax 调用、计数以及浏览器异常和 ajax 故障的详细信息以及用户和会话计数的计时。 所有这些信息可按页面、客户端 OS 和浏览器版本、地理位置和其他维度细分。 可以针对失败计数或页面加载缓慢情况设置警报。 并且通过在 JavaScript 代码中插入跟踪调用，可以跟踪网页应用程序的不同功能的使用情况。

可以在任何网页中使用 Application Insights - 刚刚添加了 JavaScript 的简短片段。 如果 Web 服务是 [Java](java-get-started.md) 或 [ASP.NET](asp-net.md)，则你可以将服务器端 SDK 与客户端 JavaScript SDK 结合使用，以全方面地了解应用的性能。

## <a name="adding-the-javascript-sdk"></a>添加 JavaScript SDK

1. 首先需要一个 Application Insights 资源。 如果你尚未获得资源和检测密钥，请遵照[有关创建新资源的说明](create-new-resource.md)。
2. 从你要将 JavaScript 遥测数据发送到的资源复制检测密钥。
3. 通过以下两个选项之一，将 Application Insights JavaScript SDK 添加到网页或应用：
    * [npm 设置](#npm-based-setup)
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

### <a name="snippet-based-setup"></a>基于代码片段的设置

如果应用不使用 npm，则可以通过直接使用 Application Insights 来检测网页：只需将此代码片段粘贴到每个页面的顶部即可。 最好是将它用作 `<head>` 节中的第一个脚本，以便它可以监视所有依赖项存在的任何潜在问题。 如果使用的是 Blazor Server 应用，请在文件 `_Host.cshtml` 的顶部 `<head>` 部分中添加代码片段。

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>将遥测数据发送到 Azure 门户

默认情况下，Application Insights JavaScript SDK 会自动收集许多遥测项，这些项有助于确定应用程序和底层用户体验的运行状况。 其中包括:

- 应用中**未捕获到的异常**，包括以下相关信息
    - 堆栈跟踪
    - 异常详细信息和错误随附的消息
    - 错误的行号与列号
    - 引发错误的 URL
- 应用发出的**网络依赖项请求**：**XHR** 和 **FETCH**（默认已禁用提取集合）请求，包括以下相关信息
    - 依赖项源的 URL
    - 用于请求依赖项的命令和方法
    - 请求持续时间
    - 请求的结果代码和成功状态
    - 发出请求的用户的 ID（如果有）
    - 发出请求的关联上下文（如果有）
- **用户信息**（例如位置、网络、IP）
- **设备信息**（例如，浏览器、OS、版本、语言、型号）
- **会话信息**

### <a name="telemetry-initializers"></a>遥测初始化表达式
遥测初始化表达式用于在从用户浏览器发送收集的遥测内容之前先对其进行修改。 它们还可用于返回 `false` 以阻止发送某些遥测数据。 可将多个遥测初始化表达式添加到 Application Insights 实例，它们将按添加顺序执行。

的输入`addTelemetryInitializer`参数是一个回调，它[`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer)采用作为参数并返回`boolean`或。 `void` 如果返回 `false`，则不发送遥测项，否则将继续处理下一个遥测初始化表达式（如果有），或者将遥测项发送到遥测集合终结点。

使用遥测初始化表达式的示例：
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
大多数配置字段的命名都可默认为 false。 除 `instrumentationKey` 以外的所有字段都是可选的。

| 名称 | 默认 | 说明 |
|------|---------|-------------|
| instrumentationKey | null | **必需**<br>从 Azure 门户获取的检测密钥。 |
| accountId | null | 可选的帐户 ID（如果应用将用户分组到帐户中）。 不允许使用空格、逗号、分号、等于或竖线 |
| sessionRenewalMs | 1800000 | 如果用户处于非活动状态有这么长的时间（以毫秒为单位），则会记录会话。 默认值为 30 分钟 |
| sessionExpirationMs | 86400000 | 如果会话持续了这么长的时间（以毫秒为单位），则会记录会话。 默认值为 24 小时 |
| maxBatchSizeInBytes | 10000 | 遥测批的最大大小。 如果某个批超过此限制，则立即发送此批，并启动新批 |
| maxBatchInterval | 15000 | 发送前要批处理遥测数据的时间长短（毫秒） |
| disableExceptionTracking | false | 如果为 true，则不自动收集异常。 默认值为 false。 |
| disableTelemetry | false | 如果为 true，则不收集或发送遥测数据。 默认值为 false。 |
| enableDebug | false | 如果为 true，则不管 SDK 日志记录设置如何，**内部**调试数据都将引发为异常，**而不是**记录这些数据。 默认值为 false。 <br>***注意：*** 如果启用此设置，则在发生内部错误时，将导致丢弃遥测数据。 这可能有利于快速识别 SDK 的配置或用法问题。 如果你不希望在调试时丢失遥测数据，请考虑使用 `consoleLoggingLevel` 或 `telemetryLoggingLevel`，而不是 `enableDebug`。 |
| loggingLevelConsole | 0 | 将**内部** Application Insights 错误记录到控制台。 <br>0：关闭， <br>1：仅严重错误， <br>2：所有内容（错误 & 警告） |
| loggingLevelTelemetry | 1 | 将**内部** Application Insights 错误作为遥测数据发送。 <br>0：关闭， <br>1：仅严重错误， <br>2：所有内容（错误 & 警告） |
| diagnosticLogInterval | 10000 | 内部日志记录队列的（内部）轮询间隔（以毫秒为单位） |
| samplingPercentage | 100 | 要发送的事件百分比。 默认值为 100，表示发送所有事件。 如果你希望避免大型应用程序达到数据上限，请设置此项。 |
| autoTrackPageVisitTime | false | 如果为 true，则对于页面视图，将跟踪前一个检测的页面的查看时间并将其作为遥测数据发送，同时，为当前的页面视图启动新的计时器。 默认值为 false。 |
| disableAjaxTracking | false | 如果为 true，则不自动收集 Ajax 调用。 默认值为 false。 |
| disableFetchTracking | 是 | 如果为 true，则不自动收集 Fetch 请求。 默认值为 true |
| overridePageViewDuration | false | 如果为 true，则在调用 trackPageView 时，trackPageView 的默认行为将更改为记录页面视图持续时间间隔的结束时间。 如果为 false 且未为 trackPageView 提供自定义持续时间，则会使用导航计时 API 计算页面视图性能。 默认值为 false。 |
| maxAjaxCallsPerView | 500 | 默认值为 500 - 控制每个页面视图将监视多少个 Ajax 调用。 设置为 -1 可监视页面上的所有（无限制）Ajax 调用。 |
| disableDataLossAnalysis | 是 | 如果为 false，则对于尚未发送的项，启动时将检查内部遥测发送方缓冲区。 |
| disableCorrelationHeaders | false | 如果为 false，则 SDK 会将两个标头（“Request-Id”和“Request-Context”）添加到所有依赖项请求，以将其关联到服务器端上的对应请求。 默认值为 false。 |
| correlationHeaderExcludedDomains |  | 禁用特定域的关联标头 |
| correlationHeaderDomains |  | 启用特定域的关联标头 |
| disableFlushOnBeforeUnload | false | 默认值为 false。 如果为 true，则触发 onBeforeUnload 事件时不会调用 flush 方法 |
| enableSessionStorageBuffer | 是 | 默认值为 true。 如果为 true，则会将包含所有未发送的遥测数据的缓冲区存储在会话存储中。 加载页面时会还原该缓冲区 |
| isCookieUseDisabled | false | 默认值为 false。 如果为 true，则 SDK 不会存储或读取 Cookie 中的任何数据。|
| cookieDomain | null | 自定义 Cookie 域。 若要跨子域共享 Application Insights Cookie，此字段会有帮助。 |
| isRetryDisabled | false | 默认值为 false。 如果为 false，则出现代码 206（部分成功）、408（超时）、429（请求过多）、500（内部服务器错误）、503（服务不可用）和 0（脱机，仅当已检测到此状态时）时会重试 |
| isStorageUseDisabled | false | 如果为 true，则 SDK 不会存储或读取本地和会话存储中的任何数据。 默认值为 false。 |
| isBeaconApiDisabled | 是 | 如果为 false，则 SDK 将使用[信标 API](https://www.w3.org/TR/beacon) 发送所有遥测数据 |
| onunloadDisableBeacon | false | 默认值为 false。 选项卡关闭时，SDK 将使用[信标 API](https://www.w3.org/TR/beacon) 发送所有剩余的遥测 |
| sdkExtension | null | 设置 SDK 扩展名。 仅允许使用字母字符。 扩展名将添加为“ai.internal.sdkVersion”标记的前缀（例如“ext_javascript:2.0.0”）。 默认值为 null。 |
| isBrowserLinkTrackingEnabled | false | 默认值为 false。 如果为 true，则 SDK 将跟踪所有[浏览器链接](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink)请求。 |
| appId | null | appId 用于在客户端上发生的 AJAX 依赖项与服务器端请求之间进行关联。 启用信标 API 后，无法自动使用 appId，但可以在配置中手动设置它。 默认值为 null |
| enableCorsCorrelation | false | 如果为 true，则 SDK 会将两个标头（“Request-Id”和“Request-Context”）添加到所有 CORS 请求，以将传出的 AJAX 依赖项关联到服务器端上的对应请求。 默认值为 false |
| namePrefix | 未定义 | 一个可选值，用作 localStorage 和 Cookie 名称的名称后缀。
| enableAutoRouteTracking | false | 自动跟踪单页应用程序 (SPA) 中的路由更改。 如果为 true，则每次更改路由都会将一个新的页面视图发送到 Application Insights。 哈希路由更改 (`example.com/foo#bar`) 也会记录为新的页面视图。
| enableRequestHeaderTracking | false | 如果为 true，则跟踪 AJAX 和 Fetch 请求标头，默认值为 false。
| enableResponseHeaderTracking | false | 如果为 true，则跟踪 AJAX 和 Fetch 请求的响应标头，默认值为 false。
| distributedTracingMode | `DistributedTracingModes.AI` | 设置分布式跟踪模式。 如果设置了 AI_AND_W3C 模式或 W3C 模式，则将生成 W3C 跟踪上下文标头 (traceparent/tracestate)，并将其包含在所有传出请求中。 提供 AI_AND_W3C 是为了与任何旧版 Application Insights 检测服务向后兼容。

## <a name="single-page-applications"></a>单页应用程序

默认情况下，此 SDK **不会**处理单页应用程序中发生的基于状态的路由更改。 若要为单页应用程序启用自动路由更改跟踪，可将 `enableAutoRouteTracking: true` 添加到设置配置。

目前，我们提供了一个单独的[响应插件](#react-extensions)，你可以使用此 SDK 对其进行初始化。 该插件也能为你实现路由更改跟踪，并可收集[其他特定于 React 的遥测数据](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)。

> [!NOTE]
> 仅`enableAutoRouteTracking: true`在**未**使用 "响应" 插件时使用。 当路由更改时，这两种方法都能发送新的 PageViews。 如果两者都处于启用状态，则可能会发送重复的 PageViews。

## <a name="configuration-autotrackpagevisittime"></a>配置： autoTrackPageVisitTime

通过设置`autoTrackPageVisitTime: true`，跟踪用户在每个页面上所花费的时间。 在每个新的 PageView 上，用户在*上一*页上花费的持续时间将作为名`PageVisitTime`为的[自定义度量值](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-custom-overview)发送。 此自定义指标在[指标资源管理器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)中可作为 "基于日志的指标" 查看。

## <a name="react-extensions"></a>React 扩展

| 扩展 |
|---------------|
| [React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [响应本机](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>浏览浏览器/客户端数据

可以转到“指标”并添加你感兴趣的各个指标，来查看浏览器/客户端数据：****

![](./media/javascript/page-view-load-time.png)

还可以通过门户中的“浏览器”体验查看 JavaScript SDK 中的数据。

选择“浏览器”，然后选择“失败”或“性能”。************

![](./media/javascript/browser.png)

### <a name="performance"></a>性能

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>依赖关系

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analytics

若要查询 JavaScript SDK 收集的遥测数据，请选择“在日志(分析)中查看”按钮。**** 如果添加 `client_Type == "Browser"` 的 `where` 语句，则只会看到来自 JavaScript SDK 的数据，而其他 SDK 收集的任何服务器端遥测数据将被排除。
 
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

可以在 Azure 门户中取消缩小异常遥测的已缩小调用堆栈。 “异常详细信息”面板中的所有现有集成将适用于最近取消缩小的调用堆栈。

#### <a name="link-to-blob-storage-account"></a>链接到 Blob 存储帐户

可以将 Application Insights 资源链接到自己的 Azure Blob 存储容器，以自动 unminify 调用堆栈。 若要开始，请参阅[自动源映射支持](./source-map-support.md)。

### <a name="drag-and-drop"></a>拖放

1. 请在 Azure 门户中选择一个异常遥测项，以查看其“端到端事务详细信息”
2. 确定哪些源映射对应于此调用堆栈。 源映射必须与堆栈帧的源文件相匹配，但后缀为 `.map`
3. 将源映射拖放到 Azure 门户中的调用堆栈上 ![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web 基本版

若要获得精简的体验，可以改为安装基本版 Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
此版本附带最少数量的功能，你可以根据自己的需求对其进行构建。 例如，它不执行自动收集（未捕获的异常、AJAX 等等）。 此版本不包含用于发送某些遥测类型的 API（例如 `trackTrace`、`trackException` 等），因此你需要提供自己的包装器。 提供的唯一 API 是 `track`。 [此处](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html)提供了一个示例。

## <a name="examples"></a>示例

有关可运行的示例，请参阅[Application Insights JAVASCRIPT SDK 示例](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>从旧版本的 Application Insights 升级

SDK V2 版本中的重大更改：
- 为了实现更好的 API 签名，某些 API 调用（例如 trackPageView 和 trackException）已更新。 不支持在 Internet Explorer 8 和早期版本的浏览器中运行。
- 由于数据架构更新，遥测信封具有字段名称和结构更改。
- 已将 `context.operation` 转移到 `context.telemetryTrace`。 还更改了某些字段（`operation.id` --> `telemetryTrace.traceID`）。
  - 若要手动刷新当前 pageview ID （例如，在 SPA 应用中），请`appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()`使用。
    > [!NOTE]
    > 若要使跟踪 ID 保持唯一，你之前使用`Util.newId()`过，现在`Util.generateW3CId()`请使用。 最终最终都是操作 ID。

如果你正在使用最新的 Application Insights PRODUCTION SDK (1.0.20)，并想要查看新 SDK 是否可在运行时中正常工作，请根据当前的 SDK 加载方案更新 URL。

- 通过 CDN 方案下载：更新当前用于指向以下 URL 的代码片段：
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- npm 方案：调用`downloadAndSetup`从 CDN 下载完整的 applicationinsights.config 脚本并将其初始化为检测密钥：

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

在内部环境中进行测试，以验证是否可按预期方式监视遥测。 如果一切正常，请相应地将 API 签名更新为 SDK V2 版本，并部署到生产环境中。

## <a name="sdk-performanceoverhead"></a>SDK 性能/开销

经过 gzip 压缩后只有 25 KB，只需大约 15 毫秒即可完成初始化，Application Insights 在网站中的加载时间可忽略不计。 使用代码片段时，很快就能加载极少量的库组件。 同时，整个脚本将在后台下载。

从 CDN 下载脚本时，页面的所有跟踪将会排队。 在下载的脚本以异步方式完成初始化后，将会跟踪已排队的所有事件。 因此，在页面的整个生命周期内，你都不会丢失任何遥测数据。 此设置过程可为页面提供一个无缝分析系统，而用户察觉不到该系统。

> 摘要：
> - 经过 gzip 压缩后为 **25 KB**
> - 总初始化时间为 **15 毫秒**
> - 在页面的整个生命周期内都**不会**失去跟踪

## <a name="browser-support"></a>浏览器支持

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![FireFox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Chrome 最新版 ✔ |  Firefox 最新版 ✔ | IE 9 + 和 Edge ✔ | Opera 最新版 ✔ | Safari 最新版 ✔ |

## <a name="open-source-sdk"></a>开源 SDK

Application Insights JavaScript SDK 是开源的，用户可查看其源代码；若要对该项目做贡献，请访问[官方 GitHub 存储库](https://github.com/Microsoft/ApplicationInsights-JS)。

## <a name="next-steps"></a><a name="next"></a> 后续步骤
* [跟踪使用情况](usage-overview.md)
* [自定义事件和度量值](api-custom-events-metrics.md)
* [Build-measure-learn](usage-overview.md)
