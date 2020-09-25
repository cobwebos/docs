---
title: 使用 Azure Application Insights 监视 Node.js 服务 | Microsoft Docs
description: 使用 Application Insights 监视 Node.js 服务的性能并诊断其问题。
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: 982adf6c6d7cd825d185802321ce30a04bd2f216
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323288"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>使用 Application Insights 监视 Node.js 服务和应用

[Application Insights](./app-insights-overview.md) 可以在部署后对后端服务和组件进行监视，以便发现并快速诊断性能问题和其他问题。 可以将 Application Insights 用于 Node.js 服务，不管这些服务是托管在数据中心、Azure VM 和 Web 应用中，还是在其他公有云中。

若要接收、存储和探索监视数据，请将 SDK 包括到代码中，然后在 Azure 中设置相应的 Application Insights 资源。 SDK 会将数据发送到该资源进行进一步的分析和探索。

Node.js SDK 可以自动监视传入和传出的 HTTP 请求、异常和某些系统指标。 从 0.20 版开始，SDK 也可监视某些常用的[第三方程序包](https://github.com/microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers#currently-supported-modules)，例如 MongoDB、MySQL、Redis。 所有与传入 HTTP 请求相关的事件都会进行关联，以加快故障排除速度。

可以使用 TelemetryClient API 手动检测和监视应用和系统的其他方面。 本文后面会更详细地介绍 TelemetryClient API。

## <a name="get-started"></a>入门

请完成以下任务，为应用或服务设置监视。

### <a name="prerequisites"></a>先决条件

开始之前，请确保拥有 Azure 订阅，否则请[免费获取一个新的][azure-free-offer]。 如果组织已经拥有 Azure 订阅，管理员可以按照[这些说明][add-aad-user]将你添加到该订阅。

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: ../../active-directory/fundamentals/add-users-azure-active-directory.md

### <a name="set-up-an-application-insights-resource"></a><a name="resource"></a> 设置 Application Insights 资源

1. 登录到 [Azure 门户][portal]。
2. [创建 Application Insights 资源](create-new-resource.md)

### <a name="set-up-the-nodejs-sdk"></a><a name="sdk"></a> 设置 Node.js SDK

将 SDK 包括到应用中，使之能够收集数据。

1. 从最新创建的资源中复制资源的检测密钥（也称 ikey）。 Application Insights 使用 ikey 将数据映射到 Azure 资源。 必须在环境变量或代码中指定 ikey，然后 SDK 才能使用该 ikey。  

   ![复制检测密钥](./media/nodejs/instrumentation-key-001.png)

2. 通过 package.json 将 Node.js SDK 库添加到应用的依赖项。 从应用的根文件夹，运行：

   ```bash
   npm install applicationinsights --save
   ```

    > [!NOTE]
    > 如果使用 TypeScript，请勿安装单独的“typings”包。 此 NPM 包包含内置的 typings。

3. 将该库显式加载到代码中。 由于 SDK 将检测注入到许多其他库中，请尽早加载该库，甚至应赶在其他 `require` 语句之前加载。

   ```javascript
   let appInsights = require('applicationinsights');
   ```
4.  也可通过环境变量 `APPINSIGHTS_INSTRUMENTATIONKEY` 来提供 ikey，不必手动将其传递给 `setup()` 或 `new appInsights.TelemetryClient()`。 这种做法允许将 ikey 脱离已提交的源代码，因此可以为不同的环境指定不同的 ikey。 手动配置调用 `appInsights.setup('[your ikey]');`。

    有关其他配置选项，请参阅以下各节。

    可以设置 `appInsights.defaultClient.config.disableAppInsights = true`，尝试在不发送遥测的情况下使用 SDK。

5. 开始通过调用 `appInsights.start();` 自动收集和发送数据。

### <a name="monitor-your-app"></a><a name="monitor"></a> 监视应用

SDK 会自动收集有关 Node.js 运行时和一些常见第三方模块的遥测。 请使用应用程序生成部分此类数据。

然后，在 [Azure 门户][portal]中转到此前创建的 Application Insights 资源。 在“概览时间线”中，查找前面的几个数据点。 若要查看更多详细数据，请在图表中选择不同的组件。

若要查看应用的已发现拓扑，可以使用[应用程序映射](app-map.md)。

#### <a name="no-data"></a>无数据

由于 SDK 对要提交的数据进行批处理，项目在门户中显示之前可能会有一段延迟。 如果在资源中看不到数据，可尝试下面的部分修复手段：

* 继续使用应用程序。 通过更多操作生成更多遥测。
* 在门户资源视图中单击“刷新”。 图表会定期自行刷新，但手动刷新会强制图表立刻刷新。
* 验证[所需传出端口](./ip-addresses.md)是否已打开。
* 使用[搜索](./diagnostic-search.md)查找特定事件。
* 查看[常见问题][FAQ]。

## <a name="basic-usage"></a>基本用法

对于开箱即用的 HTTP 请求集合、受欢迎的第三方库事件、未经处理的异常和系统指标：

```javascript

let appInsights = require("applicationinsights");
appInsights.setup("[your ikey]").start();

```

> [!NOTE]
> 如果在环境变量 `APPINSIGHTS_INSTRUMENTATIONKEY` 中设置检测密钥，则无需使用参数即可调用 `.setup()`。 这样就可以轻松地针对不同环境使用不同的 ikey。

加载其他包之前，请在脚本中提前加载 Application Insights 库 `require("applicationinsights")`。 此操作是必需的，以便 Application Insights 库可以为跟踪准备更高版本的包。 如果与执行类似准备的其他库发生冲突，请尝试在这些库后面加载 Application Insights 库。

由于 JavaScript 处理回调的方式，需要执行额外的工作，以跟踪跨外部依赖项和更高版本回调的请求。 默认情况下启用此附加跟踪；可通过调用 `setAutoDependencyCorrelation(false)` 禁用此跟踪（如以下“[配置](#sdk-configuration)”部分所述）。

## <a name="migrating-from-versions-prior-to-022"></a>从版本 0.22 之前的版本进行迁移

这些是版本 0.22 及更高版本之前的版本之间的重大更改。 这些更改旨在与其他 Application Insights SDK 保持一致并允许将来进行扩展。

通常，可以通过以下方式进行迁移：

- 用 `appInsights.defaultClient` 替换对 `appInsights.client` 的引用。
- 用 `new appInsights.TelemetryClient()` 替换对 `appInsights.getClient()` 的引用
- 将所有参数替换为 client.track* 方法，其中有一个包含命名属性的对象用作参数。 有关每种类型的遥测的例外对象，请参阅 IDE 的内置类型提示或 [TelemetryTypes](https://github.com/Microsoft/ApplicationInsights-node.js/tree/develop/Declarations/Contracts/TelemetryTypes)。

如果在不将 SDK 配置函数链接到 `appInsights.setup()` 的情况下访问这些函数，现在可以在 `appInsights.Configurations`（例如 `appInsights.Configuration.setAutoCollectDependencies(true)`）中找到这些函数。 查看下一节中对默认配置所做的更改。

## <a name="sdk-configuration"></a>SDK 配置

`appInsights` 对象提供了许多配置方法。 以下代码片段中列出了这些方法及其默认值。

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true, true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .setSendLiveMetrics(false)
    .setDistributedTracingMode(appInsights.DistributedTracingModes.AI)
    .start();
```

若要让服务中的事件完全相关联，请确保设置 `.setAutoDependencyCorrelation(true)`。 设置此选项以后，SDK 即可在 Node.js 中跨异步回调跟踪上下文。

有关这些控件和可选辅助参数的详细信息，请在 IDE 的内置类型提示或 [applicationinsights.config](https://github.com/microsoft/ApplicationInsights-node.js/blob/develop/applicationinsights.ts) 中查看其说明。

> [!NOTE]
>  默认情况下，`setAutoCollectConsole` 配置为排除对 `console.log`（以及其他控制台方法）的调用。 将只收集对受支持的第三方记录器（例如 winston 和 bunyan）的调用。 你可以通过使用 `setAutoCollectConsole(true, true)` 将此行为更改为包括对 `console` 方法的调用。

### <a name="sampling"></a>采样

默认情况下，SDK 会将收集的所有数据发送到 Application Insights 服务。 如果收集了大量数据，则可能需要启用采样来减少发送的数据量。 要完成此操作，请设置客户端的 `config` 对象上的 `samplingPercentage` 字段。 将 `samplingPercentage` 设置为100（默认值）表示将发送所有数据，设置为 0 则表示不会发送任何内容。

如果使用自动关联，则会将与单个请求关联的所有数据作为一个单元包括或排除。

若要启用采样，请添加以下代码：

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.config.samplingPercentage = 33; // 33% of all telemetry will be sent to Application Insights
appInsights.start();
```

### <a name="multiple-roles-for-multi-components-applications"></a>多组件应用程序的多个角色

如果你的应用程序包含多个你希望使用相同检测密钥来检测的组件，并且仍在门户中将这些组件视为单独的单元，就像它们使用单独的检测密钥一样（例如，就像应用程序映射上的单独节点一样），你可能需要手动配置“用户类型”字段，以将一个组件的遥测与用于将数据发送到 Application Insights 资源的其他组件区分开来。

使用以下内容设置 RoleName 字段：

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.context.tags[appInsights.defaultClient.context.keys.cloudRole] = "MyRoleName";
appInsights.start();
```

### <a name="automatic-third-party-instrumentation"></a>自动第三方检测

为了跨异步调用跟踪上下文，某些第三方库（如 MongoDB 和 Redis）需要进行一些更改。 默认情况下，Application Insights 将使用 [`diagnostic-channel-publishers`](https://github.com/Microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers) 猴子补丁来修补一些库。 可通过设置 `APPLICATION_INSIGHTS_NO_DIAGNOSTIC_CHANNEL` 环境变量禁用此操作。

> [!NOTE]
> 通过设置该环境变量，事件可能不再与正确的操作正确关联。

 可以通过将 `APPLICATION_INSIGHTS_NO_PATCH_MODULES` 环境变量设置为要禁用的包的逗号分隔列表（例如 `APPLICATION_INSIGHTS_NO_PATCH_MODULES=console,redis`）来禁用单个猴子补丁，以避免修补 `console` 和 `redis` 包。

目前已检测到 9 个包：`bunyan`、`console`、`mongodb`、`mongodb-core`、`mysql`、`redis`、`winston`、`pg` 和 `pg-pool`。 如需深入了解要修补这些包的哪个版本，请访问 [diagnostic-channel-publishers 自述文件](https://github.com/Microsoft/node-diagnostic-channel/blob/master/src/diagnostic-channel-publishers/README.md)。

`bunyan`、`winston` 和 `console` 补丁将根据是否启用 `setAutoCollectConsole` 生成 Application Insights 跟踪事件。 其余补丁将根据是否启用 `setAutoCollectDependencies` 生成 Insights Dependency 事件。

### <a name="live-metrics"></a>实时指标

若要允许将实时指标从应用发送到 Azure，请使用 `setSendLiveMetrics(true)`。 目前不支持在门户中筛选实时指标。

### <a name="extended-metrics"></a>扩展指标

> [!NOTE]
> 1\.4.0 版本中添加了发送扩展本机指标的功能。

若要允许将扩展本机指标从应用发送到 Azure，请安装单独的本机指标包。 SDK 将在安装后自动加载并开始收集 node.js 本机指标。

```bash
npm install applicationinsights-native-metrics
```

目前，本机指标包会自动收集垃圾回收 CPU 时间、事件循环计时和堆使用情况：

- **垃圾回收**：每种类型的垃圾回收所用的 CPU 时间，以及每种类型出现的次数。
- **事件循环**：发生了多少个计时周期，以及总共花费了多少 CPU 时间。
- **堆与非堆**：应用的内存使用情况有多少位于堆或非堆中。

### <a name="distributed-tracing-modes"></a>分布式跟踪模式

默认情况下，SDK 将发送被其他应用程序/服务理解的标头，可通过 Application Insights SDK 检测到此类应用程序/服务。 除了现有的 AI 标头，还可以根据需要启用 [W3C 跟踪上下文](https://github.com/w3c/trace-context)标头的发送/接收，这样就不会中断与任何现有旧服务的关联。 启用 W3C 标头将允许你的应用与未通过 Application Insights 检测的其他服务相关联，但会采用这一 W3C 标准。

```Javascript
const appInsights = require("applicationinsights");
appInsights
  .setup("<your ikey>")
  .setDistributedTracingMode(appInsights.DistributedTracingModes.AI_AND_W3C)
  .start()
```

## <a name="telemetryclient-api"></a>TelemetryClient API

有关 TelemetryClient API 的完整说明，请参阅[用于处理自定义事件和指标的 Application Insights API](./api-custom-events-metrics.md)。

可以使用 Application Insights Node.js SDK 跟踪任何请求、事件、指标或异常。 以下代码示例演示了部分可用 API：

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var. start() can be omitted to disable any non-custom data
let client = appInsights.defaultClient;
client.trackEvent({name: "my custom event", properties: {customProperty: "custom property value"}});
client.trackException({exception: new Error("handled exceptions can be logged with this method")});
client.trackMetric({name: "custom metric", value: 3});
client.trackTrace({message: "trace message"});
client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL"});
client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});

let http = require("http");
http.createServer( (req, res) => {
  client.trackNodeHttpRequest({request: req, response: res}); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>跟踪依赖项

使用以下代码跟踪依赖项：

```javascript
let appInsights = require("applicationinsights");
let client = new appInsights.TelemetryClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:duration, resultCode:0, success: true, dependencyTypeName: "ZSQL"});;
```

使用 `trackMetric` 度量事件循环计划所花费的时间的示例实用工具：  

```javascript
function startMeasuringEventLoop() {
  var startTime = process.hrtime();
  var sampleSum = 0;
  var sampleCount = 0;

  // Measure event loop scheduling delay
  setInterval(() => {
    var elapsed = process.hrtime(startTime);
    startTime = process.hrtime();
    sampleSum += elapsed[0] * 1e9 + elapsed[1];
    sampleCount++;
  }, 0);

  // Report custom metric every second
  setInterval(() => {
    var samples = sampleSum;
    var count = sampleCount;
    sampleSum = 0;
    sampleCount = 0;

    if (count > 0) {
      var avgNs = samples / count;
      var avgMs = Math.round(avgNs / 1e6);
      client.trackMetric({name: "Event Loop Delay", value: avgMs});
    }
  }, 1000);
}
```

### <a name="add-a-custom-property-to-all-events"></a>将自定义属性添加到所有事件

使用以下代码向所有事件添加自定义属性：

```javascript
appInsights.defaultClient.commonProperties = {
  environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>跟踪 HTTP GET 请求

使用以下代码手动跟踪 HTTP GET 请求：

> [!NOTE]
> 默认情况下跟踪所有请求。 若要禁用自动收集，请在调用 start() 之前调用 .setAutoCollectRequests(false)。

```javascript
appInsights.defaultClient.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});
```

也可使用 `trackNodeHttpRequest` 方法跟踪请求：

```javascript
var server = http.createServer((req, res) => {
  if ( req.method === "GET" ) {
      appInsights.defaultClient.trackNodeHttpRequest({request:req, response:res});
  }
  // other work here....
  res.end();
});
```

### <a name="track-server-startup-time"></a>跟踪服务器启动时间

使用以下代码跟踪服务器启动时间：

```javascript
let start = Date.now();
server.on("listening", () => {
  let duration = Date.now() - start;
  appInsights.defaultClient.trackMetric({name: "server startup time", value: duration});
});
```

### <a name="preprocess-data-with-telemetry-processors"></a>使用遥测处理器预处理数据

在发送数据之前，可以使用遥测处理器来处理和筛选收集的数据以进行保留。 遥测处理器在遥测项发送到云之前按其添加顺序依次调用。

```javascript
public addTelemetryProcessor(telemetryProcessor: (envelope: Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean)
```

如果遥测处理器返回 false，则不会发送该遥测项。

所有遥测处理器都接收遥测数据及其信封来检查和修改。 它们还会接收上下文对象。 在为手动跟踪的遥测调用跟踪方法时，此对象的内容由 `contextObjects` 参数定义。 对于自动收集的遥测，此对象使用 `appInsights.getCorrelationContext()` 提供的可用请求信息和持久性请求内容（如果启用了自动相关性关联）进行填充。

遥测处理器的 TypeScript 类型为：

```javascript
telemetryProcessor: (envelope: ContractsModule.Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean;
```

例如，可以按如下所示编写并添加从异常中删除堆栈跟踪数据的处理器：

```javascript
function removeStackTraces ( envelope, context ) {
  if (envelope.data.baseType === "Microsoft.ApplicationInsights.ExceptionData") {
    var data = envelope.data.baseData;
    if (data.exceptions && data.exceptions.length > 0) {
      for (var i = 0; i < data.exceptions.length; i++) {
        var exception = data.exceptions[i];
        exception.parsedStack = null;
        exception.hasFullStack = false;
      }
    }
  }
  return true;
}

appInsights.defaultClient.addTelemetryProcessor(removeStackTraces);
```

## <a name="use-multiple-instrumentation-keys"></a>使用多个检测密钥

可以创建多个 Application Insights 资源，并使用各自的检测密钥 (ikey) 将不同数据发送到每个资源。

 例如：

```javascript
let appInsights = require("applicationinsights");

// configure auto-collection under one ikey
appInsights.setup("_ikey-A_").start();

// track some events manually under another ikey
let otherClient = new appInsights.TelemetryClient("_ikey-B_");
otherClient.trackEvent({name: "my custom event"});
```

## <a name="advanced-configuration-options"></a>高级配置选项

客户端对象包含一个 `config` 属性，该属性具有多个适用于高级方案的可选设置。 可按如下所示设置这些设置：

```javascript
client.config.PROPERTYNAME = VALUE;
```

这些属性是特定于客户端的，因此你可以从使用 `new appInsights.TelemetryClient()` 创建的客户端单独配置 `appInsights.defaultClient`。

| Property                        | 说明                                                                                                |
| ------------------------------- |------------------------------------------------------------------------------------------------------------|
| instrumentationKey              | Application Insights 资源的标识符。                                                      |
| endpointUrl                     | 接收遥测有效负载的引入终结点。                                                      |
| quickPulseHost                  | 接收实时指标遥测的实时指标流主机。                                            |
| proxyHttpUrl                    | SDK HTTP 流量的代理服务器（可选，默认拉取自 `http_proxy` 环境变量）。     |
| proxyHttpsUrl                   | SDK HTTPS 流量的代理服务器（可选，默认拉取自 `https_proxy` 环境变量）。   |
| httpAgent                       | 用于 SDK HTTP 流量的 http.Agent（可选，默认为未定义）。                                   |
| httpsAgent                      | 用于 SDK HTTPS 流量的 http.Agent（可选，默认为未定义）。                                 |
| maxBatchSize                    | 要包括在引入终结点的有效负载中的最大遥测项数（默认为 `250`）。   |
| maxBatchIntervalMs              | 有效负载达到 maxBatchSize 之前要等待的最长时间（默认为 `15000`）。               |
| disableAppInsights              | 一个标志，用于指示是否禁用遥测传输（默认为 `false`）。                                 |
| samplingPercentage              | 应传输的已跟踪遥测项的百分比（默认为 `100`）。                      |
| correlationIdRetryIntervalMs    | 重试检索交叉组件相关的 ID 之前要等待的时间（默认为 `30000`）。     |
| correlationHeaderExcludedDomains| 要从交叉组件相关标头注入中排除的域列表（默认值，请参阅 [Config.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Config.ts)）。|

## <a name="next-steps"></a>后续步骤

* [在门户中监视遥测](./overview-dashboard.md)
* [通过遥测编写分析查询](../log-query/get-started-portal.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../faq.md

