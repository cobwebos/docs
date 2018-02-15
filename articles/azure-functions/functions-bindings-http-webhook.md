---
title: "Azure Functions HTTP 和 webhook 绑定"
description: "了解如何在 Azure Functions 中使用 HTTP 和 webhook 触发器和绑定。"
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构, HTTP, API, REST"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: mahender
ms.openlocfilehash: 608f5ec2fb4b8fa374778cb4f506f1d25eb7642b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Azure Functions HTTP 和 webhook 绑定

本文介绍如何在 Azure Functions 中使用 HTTP 绑定。 Azure Functions 支持 HTTP 触发器和输出绑定。

HTTP 触发器可进行自定义以响应 [Webhook](https://en.wikipedia.org/wiki/Webhook)。 Webhook 触发器仅接受 JSON 有效负载，并验证 JSON。 使用特殊版本的 Webhook 触发器可以更轻松地处理某些提供程序（例如 GitHub 和 Slack）的 Webhook。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="trigger"></a>触发器

借助 HTTP 触发器，可以使用 HTTP 请求调用函数。 可以使用 HTTP 触发器生成无服务器 API 和响应 Webhook。 

默认情况下，HTTP 触发器通过 HTTP 200 OK 状态代码和空白正文响应请求。 若要修改该响应，请配置 [HTTP 输出绑定](#http-output-binding)。

## <a name="trigger---example"></a>触发器 - 示例

参阅语言特定的示例：

* [C#](#trigger---c-example)
* [C# 脚本 (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>触发器 - C# 示例

以下示例显示一个在查询字符串或 HTTP 请求正文中查找 `name` 参数的 [C# 函数](functions-dotnet-class-library.md)。

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]HttpRequestMessage req, 
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

### <a name="trigger---c-script-example"></a>触发器 - C# 脚本示例

以下示例演示 *function.json* 文件中的一个触发器绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数在查询字符串或 HTTP 请求的正文中查找 `name` 参数。

下面是 *function.json* 文件中的绑定数据：

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

[配置](#trigger---configuration)部分解释了这些属性。

下面是绑定到 `HttpRequestMessage` 的 C# 脚本代码：

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

可以绑定到自定义对象而不是 `HttpRequestMessage`。 此对象从请求正文进行创建，并分析为 JSON。 同样，可以将类型传递给 HTTP 响应输出绑定，并作为响应正文随 200 状态代码一同返回。

```csharp
using System.Net;
using System.Threading.Tasks;

public static string Run(CustomObject req, TraceWriter log)
{
    return "Hello " + req?.name;
}

public class CustomObject {
     public String name {get; set;}
}
}
```

### <a name="trigger---f-example"></a>触发器 - F# 示例

以下示例演示 *function.json* 文件中的一个触发器绑定以及使用该绑定的 [F# 函数](functions-reference-fsharp.md)。 该函数在查询字符串或 HTTP 请求的正文中查找 `name` 参数。

下面是 *function.json* 文件中的绑定数据：

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

[配置](#trigger---configuration)部分解释了这些属性。

F# 代码如下所示：

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

需要一个使用 NuGet 引用 `FSharp.Interop.Dynamic` 和 `Dynamitey` 程序集的 `project.json` 文件，如以下示例中所示：

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

### <a name="trigger---javascript-example"></a>触发器 - JavaScript 示例

以下示例演示 *function.json* 文件中的一个触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数在查询字符串或 HTTP 请求的正文中查找 `name` 参数。

下面是 *function.json* 文件中的绑定数据：

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

[配置](#trigger---configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```
     
## <a name="trigger---webhook-example"></a>触发器 - Webhook 示例

参阅语言特定的示例：

* [C#](#webhook---c-example)
* [C# 脚本 (.csx)](#webhook---c-script-example)
* [F#](#webhook---f-example)
* [JavaScript](#webhook---javascript-example)

### <a name="webhook---c-example"></a>Webhook - C# 示例

以下示例显示一个在泛型 JSON 请求的响应中发送 HTTP 200 的 [C# 函数](functions-dotnet-class-library.md)。

```cs
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="webhook---c-script-example"></a>Webhook - C# 脚本示例

以下示例演示 *function.json* 文件中的一个 Webhook 触发器绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数记录 GitHub 问题注释。

下面是 *function.json* 文件中的绑定数据：

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

[配置](#trigger---configuration)部分解释了这些属性。

C# 脚本代码如下所示：

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

### <a name="webhook---f-example"></a>Webhook - F # 示例

以下示例演示 *function.json* 文件中的一个 Webhook 触发器绑定以及使用该绑定的 [F# 函数](functions-reference-fsharp.md)。 该函数记录 GitHub 问题注释。

下面是 *function.json* 文件中的绑定数据：

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

[配置](#trigger---configuration)部分解释了这些属性。

F# 代码如下所示：

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

### <a name="webhook---javascript-example"></a>Webhook - JavaScript 示例

以下示例演示 *function.json* 文件中的一个 Webhook 触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数记录 GitHub 问题注释。

下面是 *function.json* 文件中的绑定数据：

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

[配置](#trigger---configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="trigger---attributes"></a>触发器 - 特性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 NuGet 包 [Microsoft.Azure.WebJobs.Extensions.Http](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) 中定义的 [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) 特性。

可以在特性构造函数参数中设置授权级别和允许的 HTTP 方法，Webhook 类型和路由模板有相应的属性。 有关这些设置的详细信息，请参阅[触发器 - 配置](#trigger---configuration)。 下面是某个方法签名中的 `HttpTrigger` 特性：

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    ...
}
 ```

有关完整示例，请参阅[触发器 - C# 示例](#trigger---c-example)。

## <a name="trigger---configuration"></a>触发器 - 配置

下表解释了在 *function.json* 文件和 `HttpTrigger` 特性中设置的绑定配置属性。


|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
| **类型** | 不适用| 必需 - 必须设置为 `httpTrigger`。 |
| **direction** | 不适用| 必需 - 必须设置为 `in`。 |
| **name** | 不适用| 必需 - 在请求或请求正文的函数代码中使用的变量名称。 |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |确定请求中需要提供的密钥（如果有），以便调用此函数。 授权级别可以是以下值之一： <ul><li><code>anonymous</code>&mdash;无需 API 密钥。</li><li><code>function</code>&mdash;特定于函数的 API 密钥是必需的。 如果未提供任何值，该值为默认值。</li><li><code>admin</code>&mdash;主密钥是必需的。</li></ul> 有关详细信息，请参阅有关[授权密钥](#authorization-keys)的部分。 |
| **methods** |**方法** | HTTP 方法的数组，该函数将响应此方法。 如果未指定，该函数将响应所有 HTTP 方法。 参阅[自定义 HTTP 终结点](#trigger---customize-the-http-endpoint)。 |
| **route** | **Route** | 定义路由模板，控制函数将响应的请求 URL。 如果未提供任何值，则默认值为 `<functionname>`。 有关详细信息，请参阅[自定义 HTTP 终结点](#customize-the-http-endpoint)。 |
| **webHookType** | **WebHookType** |将 HTTP 触发器配置为充当指定提供程序的 [webhook](https://en.wikipedia.org/wiki/Webhook) 接收器。 如果未设置此属性，请不要设置 `methods` 属性。 Webhook 类型可以是以下值之一：<ul><li><code>genericJson</code>&mdash;不包含特定提供程序逻辑的常规用途 webhook 终结点。 此设置会将请求限制为仅请求使用 HTTP POST 以及内容类型为 `application/json`。</li><li><code>github</code>&mdash;该函数响应 [GitHub Webhook](https://developer.github.com/webhooks/)。 不要对 GitHub Webhook 使用 _authLevel_ 属性。 有关详细信息，请参阅本文后面的“GitHub Webhook”部分。</li><li><code>slack</code>&mdash;该函数响应 [Slack Webhook](https://api.slack.com/outgoing-webhooks)。 不要对 Slack Webhook 使用 _authLevel_ 属性。 有关详细信息，请参阅本文后面的“Slack Webhook”部分。</li></ul>|

## <a name="trigger---usage"></a>触发器 - 用法

对于 C# 和 F # 函数，可以将触发器输入的类型声明为 `HttpRequestMessage` 或自定义类型。 如果选择 `HttpRequestMessage`，会获得对请求对象的完全访问权限。 对于自定义类型，函数会尝试分析 JSON 请求正文，以设置对象属性。 

对于 JavaScript 函数，Functions 运行时提供请求正文而不是请求对象。 有关详细信息，请参阅 [JavaScript 触发器示例](#trigger---javascript-example)。

### <a name="github-webhooks"></a>GitHub Webhook

要响应 GitHub webhook，首先请创建包含 HTTP 触发器的函数，并将 webHookType 属性设置为 `github`。 然后将其 URL 和 API 密钥复制到 GitHub 存储库的“添加 Webhook”页。 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

有关示例，请参阅[创建由 GitHub webhook 触发的函数](functions-create-github-webhook-triggered-function.md)。

### <a name="slack-webhooks"></a>Slack Webhook

Slack webhook 为用户生成令牌，而非让用户指定它，所以必须使用 Slack 中的令牌配置特定于函数的密钥。 请参阅[授权密钥](#authorization-keys)。

### <a name="customize-the-http-endpoint"></a>自定义 HTTP 终结点

默认情况下，创建 HTTP 触发器或 WebHook 的函数时，可通过以下格式的路由对该函数进行寻址：

    http://<yourapp>.azurewebsites.net/api/<funcname> 

在 HTTP 触发器的输入绑定中，可以使用可选 `route` 属性自定义此路由。 例如，以下 *function.json* 文件定义了 HTTP 触发器的 `route` 属性：

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

通过此配置，现在可以通过以下路由对该函数进行寻址，而不是通过原始路由寻址。

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

这使得函数代码可以支持地址中的两个参数：“category”和“id”。可以将任何 [Web API 路由约束](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints)与参数配合使用。 以下 C# 函数代码使用了这两个参数。

```csharp
public static Task<HttpResponseMessage> Run(HttpRequestMessage req, string category, int? id, 
                                                TraceWriter log)
{
    if (id == null)
        return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
    else
        return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
}
```

以下是使用相同路由参数的 Node.js 函数代码。

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
} 
```

默认情况下，所有函数路由的前缀均为 *api*。 还可以使用 [host.json](functions-host-json.md) 文件中的 `http.routePrefix` 属性自定义或删除前缀。 以下示例通过将空字符串用于 *host.json* 文件中的前缀删除 *api* 路由前缀。

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="authorization-keys"></a>授权密钥

HTTP 触发器允许使用密钥提高安全性。 标准 HTTP 触发器可以将这些密钥用作 API 密钥，这需要在请求中提供此密钥。 Webhook 可以使用密钥以多种方式对请求授权，具体取决于提供程序支持何种方式。

密钥作为 Function App 的一部分存储在 Azure 中，并进行了静态加密。 若要查看密钥，请创建新的密钥或将密钥滚动到新值，导航到门户中的某个函数并选择“管理”。 

有两种类型的密钥：

- **主机密钥**：由 Function App 中的所有函数共享这些密钥。 这些密钥用作 API 密钥时，可以访问 Function App 中的任何函数。
- **函数密钥**：这些密钥仅适用于在其下定义它们的特定函数。 这些密钥用作 API 密钥时，只允许访问该函数。

命名每个密钥方便引用，并且在函数和主机级别存在名为“default”的默认密钥。 函数密钥优先于主机密钥。 如果为两个密钥定义的名称相同，则使用函数密钥。

主密钥是为每个函数应用定义的默认主机密钥，名为“_master”。 无法撤消此密钥。 它提供对运行时 API 的管理访问权限。 在绑定 JSON 中使用 `"authLevel": "admin"` 需要在请求中提供此密钥；任何其他密钥均会导致授权失败。

> [!IMPORTANT]  
> 由于提升的权限由主密钥所授予，因此不应与第三方共享此密钥或在本机客户端应用程序中分发此密钥。 选择管理员授权级别时，请务必审慎行事。

### <a name="api-key-authorization"></a>API 密钥的授权

默认情况下，HTTP 触发器要求 HTTP 请求中含有 API 密钥。 因此 HTTP 请求通常如下所示：

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

该密钥可以包含在名为 `code` 的查询字符串变量中（如上所示），也可以包含在 `x-functions-key` HTTP头中。 密钥的值可以为任意为函数定义的函数密钥，也可以为任意主机密钥。

可以允许匿名请求，它不需要密钥。 可能还需要使用主密钥。 可使用绑定 JSON 中的 `authLevel` 属性更改默认授权级别。 有关详细信息，请参阅[触发器 - 配置](#trigger---configuration)。

### <a name="keys-and-webhooks"></a>密钥和 webhook

Webhook 授权由属于 HTTP 触发器的 webhook 接收器组件处理，其机制因 webhook 类型而异。 但是每种机制都依赖于一个密钥。 默认情况下，使用名为“default”的函数密钥。 要使用其他密钥，请将 webhook 提供程序配置为使用以下方式之一的请求发送密钥名称：

- **查询字符串**：提供程序通过 `clientid` 查询字符串参数（例如，`https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`）传递密钥名称。
- **请求头**：提供程序通过 `x-functions-clientid` 头传递密钥名称。

## <a name="trigger---limits"></a>触发器 - 限制

HTTP 请求长度限制为 100K (102,400) 字节，并且 URL 长度限制为 4k (4,096) 字节。 这些限制由运行时的 [Web.config 文件](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config)的 `httpRuntime` 元素指定。

如果使用 HTTP 触发器的函数未在大约 2.5 分钟内完成，网关将超时并返回 HTTP 502 错误。 该函数将继续运行，但将无法返回 HTTP 响应。 对于长时间运行的函数，我们建议你遵循异步模式，并返回可以 ping 通请求状态的位置。 有关函数可以运行多长时间的信息，请参阅[缩放和托管 - 消耗计划](functions-scale.md#consumption-plan)。 

## <a name="trigger---hostjson-properties"></a>触发器 - host.json 属性

[host.json](functions-host-json.md) 文件包含控制 HTTP 触发器行为的设置。

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>输出

通过 HTTP 输出绑定响应 HTTP 请求发送者。 此绑定需要使用 HTTP 触发器，利用此绑定，可以自定义与触发器请求相关联的响应。 如果未提供 HTTP 输出绑定，HTTP 触发器将返回带空白正文的 HTTP 200 OK。 

## <a name="output---configuration"></a>输出 - 配置

对于 C# 类库，没有特定于输出的绑定配置属性。 若要发送 HTTP 响应，请让函数返回类型 `HttpResponseMessage` 或 `Task<HttpResponseMessage>`。

对于其他语言，HTTP 输出绑定定义为 function.json 的 `bindings` 数组中的 JSON 对象，如以下示例中所示：

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```

下表解释了在 *function.json* 文件中设置的绑定配置属性。

|属性  |说明  |
|---------|---------|
| **类型** |必须设置为 `http`。 |
| **direction** | 必须设置为 `out`。 |
|**name** | 在响应对象的函数代码中使用的变量名。 |

## <a name="output---usage"></a>输出 - 用法

可以使用输出参数来响应 HTTP 或 Webhook 调用方。 还可以使用语言标准响应模式。 有关示例响应，请参阅[触发器示例](#trigger---example)和 [Webhook 示例](#trigger---webhook-example)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
