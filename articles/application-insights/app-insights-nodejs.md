---
title: "使用 Azure Application Insights 监视 Node.js 服务 | Microsoft Docs"
description: "使用 Application Insights 监视 Node.js 服务的性能并诊断其问题。"
services: application-insights
documentationcenter: nodejs
author: mrbullwinkle
manager: carmonm
ms.assetid: 2ec7f809-5e1a-41cf-9fcd-d0ed4bebd08c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/01/2017
ms.author: mbullwin
ms.openlocfilehash: 5b8e35a953c936949af0d496345f537ad85fc359
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>使用 Application Insights 监视 Node.js 服务和应用

[Azure Application Insights](app-insights-overview.md) 可以在部署后端服务和组件后对其进行监视，以便[发现并快速诊断性能问题和其他问题](app-insights-detect-triage-diagnose.md)。 可以将 Application Insights 用于 Node.js 服务，不管这些服务是托管在数据中心、Azure VM 和 Web 应用中，还是在其他公有云中。

若要接收、存储和探索监视数据，请将 SDK 包括到代码中，然后在 Azure 中设置相应的 Application Insights 资源。 SDK 会将数据发送到该资源进行进一步的分析和探索。

Node.js SDK 可以自动监视传入和传出的 HTTP 请求、异常和某些系统指标。 从 0.20 版开始，SDK 也可监视某些常用的第三方程序包，例如 MongoDB、MySQL、Redis。 所有与传入 HTTP 请求相关的事件都会进行关联，以加快故障排除速度。

可以使用 TelemetryClient API 手动检测和监视应用和系统的其他方面。 本文后面会更详细地介绍 TelemetryClient API。

![性能监视图表示例](./media/app-insights-nodejs/10-perf.png)

## <a name="get-started"></a>入门

请完成以下任务，为应用或服务设置监视。

### <a name="prerequisites"></a>先决条件

开始之前，请确保拥有 Azure 订阅，否则请[免费获取一个新的][azure-free-offer]。 如果组织已经拥有 Azure 订阅，管理员可以按照[这些说明][add-aad-user]你将添加到该订阅。

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: https://docs.microsoft.com/azure/active-directory/active-directory-users-create-azure-portal


### <a name="resource"></a> 设置 Application Insights 资源


1. 登录到 [Azure 门户][portal]。
2. 选择“创建资源” > “开发人员工具” > “Application Insights”。 该资源包括用于接收遥测数据的终结点、用于该数据的存储、保存的报告和仪表板、规则和警报配置等。

  ![创建 Application Insights 资源](./media/app-insights-nodejs/03-new_appinsights_resource.png)

3. 在资源创建页的“应用程序类型”框中选择“Node.js 应用程序”。 应用类型决定了创建的默认仪表板和报告。 （任何 Application Insights 资源都可以收集任何语言和平台的数据。）

  ![“新建 Application Insights 资源”窗体](./media/app-insights-nodejs/04-create_appinsights_resource.png)

### <a name="sdk"></a> 设置 Node.js SDK

将 SDK 包括到应用中，使之能够收集数据。 

1. 从 Azure 门户中复制资源的检测密钥（也称 ikey）。 Application Insights 使用 ikey 将数据映射到 Azure 资源。 必须在环境变量或代码中指定 ikey，然后 SDK 才能使用该 ikey。  

  ![复制检测密钥](./media/app-insights-nodejs/05-appinsights_ikey_portal.png)

2. 通过 package.json 将 Node.js SDK 库添加到应用的依赖项。 从应用的根文件夹，运行：

  ```bash
  npm install applicationinsights --save
  ```

3. 将该库显式加载到代码中。 由于 SDK 将检测注入到许多其他库中，请尽早加载该库，甚至应赶在其他 `require` 语句之前加载。 

  在第一个 .js 文件顶部添加以下代码。 `setup` 方法配置默认情况下用于所有已跟踪项的密钥（因此也配置 Azure 资源）。

  ```javascript
  const appInsights = require("applicationinsights");
  appInsights.setup("<instrumentation_key>");
  appInsights.start();
  ```
   
  也可通过环境变量 APPINSIGHTS\_INSTRUMENTATIONKEY 来提供 ikey，不必手动将其传递给 `setup()` 或 `new appInsights.TelemetryClient()`。 这种做法允许将 ikey 脱离已提交的源代码，因此可以为不同的环境指定不同的 ikey。

  有关其他配置选项，请参阅以下各节。

  可以设置 `appInsights.defaultClient.config.disableAppInsights = true`，尝试在不发送遥测的情况下使用 SDK。

### <a name="monitor"></a> 监视应用

SDK 自动收集 Node.js 运行时和一些常用第三方模块的遥测。 请使用应用程序生成部分此类数据。

然后，在 [Azure 门户][portal]中转到此前创建的 Application Insights 资源。 在“概览时间线”中，查找前面的几个数据点。 若要查看更多详细数据，请在图表中选择不同的组件。

![首部分数据点](./media/app-insights-nodejs/12-first-perf.png)

若要查看应用的已发现拓扑，请选择“应用程序映射”按钮。 在映射中选择组件，以便查看更多详细信息。

![简单的应用映射](./media/app-insights-nodejs/06-appinsights_appmap.png)

若要详细了解应用并排查问题，请在“调查”部分选择可用的其他视图。

![“调查”部分](./media/app-insights-nodejs/07-appinsights_investigate_blades.png)

#### <a name="no-data"></a>没有数据？

由于 SDK 对要提交的数据进行批处理，项目在门户中显示之前可能会有一段延迟。 如果在资源中看不到数据，可尝试下面的部分修复手段：

* 继续使用应用程序。 通过更多操作生成更多遥测。
* 在门户资源视图中单击“刷新”。 图表会定期自行刷新，但手动刷新会强制图表立刻刷新。
* 验证[所需传出端口](app-insights-ip-addresses.md)是否已打开。
* 使用[搜索](app-insights-diagnostic-search.md)查找特定事件。
* 查看[常见问题解答][FAQ]。


## <a name="sdk-configuration"></a>SDK 配置

SDK 的配置方法和默认值在以下代码示例中列出。

若要让服务中的事件完全相关联，请确保设置 `.setAutoDependencyCorrelation(true)`。 设置此选项以后，SDK 即可在 Node.js 中跨异步回调跟踪上下文。

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .start();
```

## <a name="telemetryclient-api"></a>TelemetryClient API

有关 TelemetryClient API 的完整说明，请参阅[用于处理自定义事件和指标的 Application Insights API](app-insights-api-custom-events-metrics.md)。

可以使用 Application Insights Node.js SDK 跟踪任何请求、事件、指标或异常。 以下代码示例演示了部分可用 API：

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey is in env var
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
let client = appInsights.defaultClient;

var success = false;
let startTime = Date.now();
// Execute dependency call here...
let duration = Date.now() - startTime;
success = true;

client.trackDependency({dependencyTypeName: "dependency name", name: "command name", duration: duration, success: success});
```

### <a name="add-a-custom-property-to-all-events"></a>将自定义属性添加到所有事件

使用以下代码向所有事件添加自定义属性：

```javascript
appInsights.defaultClient.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>跟踪 HTTP GET 请求

使用以下代码跟踪 HTTP GET 请求：

```javascript
var server = http.createServer((req, res) => {
    if ( req.method === "GET" ) {
            appInsights.defaultClient.trackNodeHttpRequest({request: req, response: res});
    }
    // Other work here...
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

## <a name="next-steps"></a>后续步骤

* [在门户中监视遥测](app-insights-dashboards.md)
* [通过遥测编写分析查询](app-insights-analytics-tour.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: app-insights-troubleshoot-faq.md

