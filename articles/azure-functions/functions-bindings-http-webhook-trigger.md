---
title: Azure Functions HTTP 触发器
description: 了解如何通过 HTTP 调用 Azure 函数。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 6466647056535635b67cd53012d051f11e9b484c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323305"
---
# <a name="azure-functions-http-trigger"></a>Azure Functions HTTP 触发器

借助 HTTP 触发器，可以使用 HTTP 请求调用函数。 可以使用 HTTP 触发器生成无服务器 API 和响应 Webhook。

HTTP 触发函数的默认返回值如下：

- `HTTP 204 No Content`，在 Functions 2.x 及更高版本中为空主体
- `HTTP 200 OK`，在 Functions 1.x 中为空主体

若要修改 HTTP 响应，请配置[输出绑定](./functions-bindings-http-webhook-output.md)。

有关 HTTP 绑定的详细信息，请参阅[概述](./functions-bindings-http-webhook.md)和[输出绑定参考](./functions-bindings-http-webhook-output.md)。

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>示例

# <a name="c"></a>[C#](#tab/csharp)

以下示例显示一个在查询字符串或 HTTP 请求正文中查找 `name` 参数的 [C# 函数](functions-dotnet-class-library.md)。 请注意，返回值用于输出绑定，但不需要返回值属性。

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

以下示例演示 function.json 文件中的一个触发器绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数在查询字符串或 HTTP 请求的正文中查找 `name` 参数。

function.json 文件如下所示：

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

[配置](#configuration)部分解释了这些属性。

下面是绑定到 `HttpRequest` 的 C# 脚本代码：

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

可以绑定到自定义对象而不是 `HttpRequest`。 此对象从请求正文进行创建，并分析为 JSON。 同样，可以将类型传递给 HTTP 响应输出绑定，并作为响应正文随 `200` 状态代码一同返回。

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

# <a name="java"></a>[Java](#tab/java)

* [从查询字符串中读取参数](#read-parameter-from-the-query-string)
* [从 POST 请求中读取正文](#read-body-from-a-post-request)
* [从路由中读取参数](#read-parameter-from-a-route)
* [从 POST 请求中读取 POJO 正文](#read-pojo-body-from-a-post-request)

下面的示例演示 HTTP 触发器绑定。

#### <a name="read-parameter-from-the-query-string"></a>从查询字符串中读取参数

以下示例从查询字符串中读取名为 `id` 的参数，然后使用该参数构建返回到客户端的 JSON 文档（内容类型为 `application/json`）。

```java
@FunctionName("TriggerStringGet")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<String>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("GET parameters are: " + request.getQueryParameters());

    // Get named parameter
    String id = request.getQueryParameters().getOrDefault("id", "");

    // Convert and display
    if (id.isEmpty()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String name = "fake_name";
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-body-from-a-post-request"></a>从 POST 请求中读取正文

以下示例将 POST 请求的正文读取为 `String`，然后使用该正文构建返回到客户端的 JSON 文档（内容类型为 `application/json`）。

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route"></a>从路由中读取参数

此示例读取名为 `id` 的必选参数，并从路由路径中读取可选参数 `name`，然后使用这两个参数构建返回到客户端的 JSON 文档（内容类型为 `application/json`）。 T

```java
@FunctionName("TriggerStringRoute")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS,
            route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
        HttpRequestMessage<Optional<String>> request,
        @BindingName("id") String id,
        @BindingName("name") String name,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Route parameters are: " + id);

    // Convert and display
    if (id == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-pojo-body-from-a-post-request"></a>从 POST 请求中读取 POJO 正文

下面是此示例中引用的 `ToDoItem` 类的代码：

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

此示例读取 POST 请求的正文。 该请求正文会自动反序列化为 `ToDoItem` 对象，然后以内容类型 `application/json` 返回到客户端。 当 `ToDoItem` 参数分配给 `HttpMessageResponse.Builder` 类的 `body` 属性时，它会由 Functions 运行时序列化。

```java
@FunctionName("TriggerPojoPost")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.POST}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<ToDoItem>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Request body is: " + request.getBody().orElse(null));

    // Check request body
    if (!request.getBody().isPresent()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final ToDoItem body = request.getBody().get();
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(body)
                        .build();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例演示 function.json 文件中的一个触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数在查询字符串或 HTTP 请求的正文中查找 `name` 参数。

function.json 文件如下所示：

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

[配置](#configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

下面的示例演示了文件 *function.js* 中的触发器绑定和 [PowerShell 函数](functions-reference-node.md)。 该函数在查询字符串或 HTTP 请求的正文中查找 `name` 参数。

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}
```

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

$body = "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."

if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body       = $body
})
```


# <a name="python"></a>[Python](#tab/python)

以下示例演示 function.json 文件中的一个触发器绑定以及使用该绑定的 [Python 函数](functions-reference-python.md)。 该函数在查询字符串或 HTTP 请求的正文中查找 `name` 参数。

function.json 文件如下所示：

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

[配置](#configuration)部分解释了这些属性。

下面是 Python 代码：

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

---

## <a name="attributes-and-annotations"></a>特性和注释

在 [C# 类库](functions-dotnet-class-library.md)和 Java 中，`HttpTrigger` 特性可用于配置函数。

可以在特性构造函数参数、Webhook 类型与路由模板中设置授权级别和允许的 HTTP 方法。 有关这些设置的详细信息，请参阅[配置](#configuration)。

# <a name="c"></a>[C#](#tab/csharp)

此示例演示如何使用 [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) 特性。

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

有关完整示例，请参阅[触发器示例](#example)。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="java"></a>[Java](#tab/java)

此示例演示如何使用 [HttpTrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java) 特性。

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

有关完整示例，请参阅[触发器示例](#example)。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell 不支持属性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

---

## <a name="configuration"></a>配置

下表解释了在 function.json 文件和 `HttpTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
| **type** | 不适用| 必需 - 必须设置为 `httpTrigger`。 |
| **direction** | 不适用| 必需 - 必须设置为 `in`。 |
| name | 不适用| 必需 - 在请求或请求正文的函数代码中使用的变量名称。 |
| <a name="http-auth"></a>**authLevel** |  AuthLevel |确定请求中需要提供的密钥（如果有），以便调用此函数。 授权级别可以是以下值之一： <ul><li><code>anonymous</code>&mdash;无需 API 密钥。</li><li><code>function</code>&mdash;特定于函数的 API 密钥是必需的。 如果未提供任何值，该值为默认值。</li><li><code>admin</code>&mdash;无需主密钥。</li></ul> 有关详细信息，请参阅有关[授权密钥](#authorization-keys)的部分。 |
| methods |**方法** | HTTP 方法的数组，该函数将响应此方法。 如果未指定，该函数将响应所有 HTTP 方法。 请参阅[自定义 HTTP 终结点](#customize-the-http-endpoint)。 |
| route | **Route** | 定义路由模板，控制函数将响应的请求 URL。 如果未提供任何值，则默认值为 `<functionname>`。 有关详细信息，请参阅[自定义 HTTP 终结点](#customize-the-http-endpoint)。 |
| webHookType | WebHookType | 仅支持 1.x 版运行时。<br/><br/>将 HTTP 触发器配置为充当指定提供程序的 [webhook](https://en.wikipedia.org/wiki/Webhook) 接收器。 如果未设置此属性，请不要设置 `methods` 属性。 Webhook 类型可以是以下值之一：<ul><li><code>genericJson</code>&mdash;不包含特定提供程序逻辑的常规用途 webhook 终结点。 此设置会将请求限制为仅请求使用 HTTP POST 以及内容类型为 `application/json`。</li><li><code>github</code>&mdash;该函数响应 [GitHub Webhook](https://developer.github.com/webhooks/)。 不要对 GitHub Webhook 使用 authLevel 属性。 有关详细信息，请参阅本文后面的“GitHub Webhook”部分。</li><li><code>slack</code>&mdash;该函数响应 [Slack Webhook](https://api.slack.com/outgoing-webhooks)。 不要对 Slack Webhook 使用 authLevel 属性。 有关详细信息，请参阅本文后面的“Slack Webhook”部分。</li></ul>|

## <a name="payload"></a>有效负载

触发器输入类型声明为 `HttpRequest` 或自定义类型。 如果选择 `HttpRequest`，会获得对请求对象的完全访问权限。 对于自定义类型，运行时会尝试分析 JSON 请求正文，以设置对象属性。

## <a name="customize-the-http-endpoint"></a>自定义 HTTP 终结点

默认情况下，创建 HTTP 触发器的函数时，可通过以下格式的路由对该函数进行寻址：

```http
http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>
```

在 HTTP 触发器的输入绑定中，可以使用可选 `route` 属性自定义此路由。 例如，以下 function.json 文件定义了 HTTP 触发器的 `route` 属性：

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
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

此配置使得函数代码可以支持地址中的两个参数：“category”和“id” 。

# <a name="c"></a>[C#](#tab/csharp)

可以将任何 [Web API 路由约束](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints)与参数配合使用。 以下 C# 函数代码使用了这两个参数。

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

可以将任何 [Web API 路由约束](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints)与参数配合使用。 以下 C# 函数代码使用了这两个参数。

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="java"></a>[Java](#tab/java)

函数执行上下文是 `HttpTrigger` 特性中声明的属性。 使用特性可以定义路由参数、授权级别、HTTP 谓词和传入请求实例。

路由参数通过 `HttpTrigger` 特性定义。

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

在 Node 中，Functions 运行时提供来自 `context` 对象的请求正文。 有关详细信息，请参阅 [JavaScript 触发器示例](#example)。

以下示例显示如何从 `context.bindingData` 读取路由参数。

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

在文件 *function.js* 中声明的路由参数可作为对象的属性进行访问 `$Request.Params` 。

```powershell
$Category = $Request.Params.category
$Id = $Request.Params.id

$Message = "Category:" + $Category + ", ID: " + $Id

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body = $Message
})
```

# <a name="python"></a>[Python](#tab/python)

函数执行上下文是通过声明为 `func.HttpRequest` 的参数公开的。 此实例允许函数访问数据路由参数、查询字符串值和允许返回 HTTP 响应的方法。

定义后，可以通过调用 `route_params` 方法将路由参数提供给函数。

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

---

默认情况下，所有函数路由的前缀均为 api。 还可以使用 [host.json](functions-host-json.md) 文件中的 `extensions.http.routePrefix` 属性自定义或删除前缀。 以下示例通过将空字符串用于 host.json 文件中的前缀删除 api 路由前缀。

```json
{
    "extensions": {
        "http": {
            "routePrefix": ""
        }
    }
}
```

## <a name="using-route-parameters"></a>使用路由参数

定义了函数的 `route` 模式的路由参数可用于每个绑定。 例如，如果将某个路由定义为 `"route": "products/{id}"`，则表存储绑定可以在绑定配置中使用 `{id}` 参数的值。

下面的配置演示如何将 `{id}` 参数传递到绑定的 `rowKey`。

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```

## <a name="working-with-client-identities"></a>使用客户端标识

如果函数应用使用[应用服务身份验证/授权](../app-service/overview-authentication-authorization.md)，则可通过代码查看有关已验证身份的客户端的信息。 此信息以[平台注入的请求标头](../app-service/app-service-authentication-how-to.md#access-user-claims)的形式提供。

还可从绑定数据中读取此信息。 此功能仅适用于 2.x 及更高版本的 Functions 运行时。 而且它目前仅可用于 .NET 语言。

# <a name="c"></a>[C#](#tab/csharp)

有关经过身份验证的客户端的信息以 [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) 的形式提供。 ClaimsPrincipal 作为请求上下文的一部分提供，如以下示例中所示：

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

或者，可直接将 ClaimsPrincipal 作为其他参数包含在函数签名中：

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

有关经过身份验证的客户端的信息以 [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) 的形式提供。 ClaimsPrincipal 作为请求上下文的一部分提供，如以下示例中所示：

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

或者，可直接将 ClaimsPrincipal 作为其他参数包含在函数签名中：

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="java"></a>[Java](#tab/java)

经过身份验证的用户通过 [HTTP 标头](../app-service/app-service-authentication-how-to.md#access-user-claims)提供。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

经过身份验证的用户通过 [HTTP 标头](../app-service/app-service-authentication-how-to.md#access-user-claims)提供。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

经过身份验证的用户通过 [HTTP 标头](../app-service/app-service-authentication-how-to.md#access-user-claims)提供。

# <a name="python"></a>[Python](#tab/python)

经过身份验证的用户通过 [HTTP 标头](../app-service/app-service-authentication-how-to.md#access-user-claims)提供。


---

## <a name="function-access-keys"></a><a name="authorization-keys"></a>函数访问密钥

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

## <a name="obtaining-keys"></a>获取密钥

密钥作为 Function App 的一部分存储在 Azure 中，并进行了静态加密。 若要查看密钥，请创建新的密钥或将密钥滚动到新值，在 [Azure 门户](https://portal.azure.com)中导航到某个 HTTP 触发的函数，然后选择“管理”。

![在门户中管理函数密钥。](./media/functions-bindings-http-webhook/manage-function-keys.png)

可以使用[密钥管理 API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) 以编程方式获取函数密钥。

## <a name="api-key-authorization"></a>API 密钥的授权

大多数 HTTP 触发器模板要求在请求中提供 API 密钥。 因此，HTTP 请求通常类似于以下 URL：

```http
https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>
```

可将该密钥包含在名为 `code` 的查询字符串变量中，如上所示。 也可以将它包含在 `x-functions-key` HTTP 标头中。 密钥的值可以为任意为函数定义的函数密钥，也可以为任意主机密钥。

可以允许匿名请求，它不需要密钥。 也可要求使用主密钥。 可使用绑定 JSON 中的 `authLevel` 属性更改默认授权级别。 有关详细信息，请参阅[触发器 - 配置](#configuration)。

> [!NOTE]
> 在本地运行函数时，不管指定的授权级别设置为何，都会禁用授权。 发布到 Azure 后，将强制执行触发器中的 `authLevel` 设置。 在[容器中本地](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally)运行时，仍然需要使用密钥。


## <a name="secure-an-http-endpoint-in-production"></a>在生产环境中保护 HTTP 终结点

若要全面保护生产环境中的函数终结点，应考虑实现以下函数应用级别的安全选项之一。 在使用这些函数应用级别的安全方法之一时，应将 HTTP 触发的函数授权级别设置为 `anonymous`。

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

#### <a name="deploy-your-function-app-in-isolation"></a>在隔离环境中部署函数应用

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

## <a name="webhooks"></a>Webhook

> [!NOTE]
> Webhook 模式仅适用于 1.x 版 Functions 运行时。 进行此更改是为了提高 2.x 及更高版本中 HTTP 触发器的性能。

在 1.x 版中，Webhook 模板为 Webhook 有效负载提供了额外的验证。 在 2.x 及更高版本中，基本 HTTP 触发器仍正常工作，且是针对 Webhook 的推荐方法。 

### <a name="github-webhooks"></a>GitHub Webhook

要响应 GitHub webhook，首先请创建包含 HTTP 触发器的函数，并将 webHookType 属性设置为 `github`。 然后将其 URL 和 API 密钥复制到 GitHub 存储库的“添加 Webhook”页。 

![显示如何添加函数的 Webhook 的屏幕截图。](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Slack Webhook

Slack webhook 为用户生成令牌，而非让用户指定它，所以必须使用 Slack 中的令牌配置特定于函数的密钥。 请参阅[授权密钥](#authorization-keys)。

## <a name="webhooks-and-keys"></a>Webhook 和密钥

Webhook 授权由属于 HTTP 触发器的 webhook 接收器组件处理，其机制因 webhook 类型而异。 每种机制都依赖于一个密钥。 默认情况下，使用名为“default”的函数密钥。 要使用其他密钥，请将 webhook 提供程序配置为使用以下方式之一的请求发送密钥名称：

* **查询字符串**：提供程序通过 `clientid` 查询字符串参数（例如，`https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`）传递密钥名称。
* **请求标头**：提供程序通过 `x-functions-clientid` 标头传递密钥名称。

## <a name="content-types"></a>内容类型

将二进制文件和窗体数据传递给非 C# 函数需要使用适当的 content-type 标头。 支持的内容类型包括 `octet-stream`（适用于二进制数据）和[多部分类型](https://www.iana.org/assignments/media-types/media-types.xhtml#multipart)。

### <a name="known-issues"></a>已知问题

在非 C# 函数中，使用 content-type `image/jpeg` 发送的请求会导致向函数传递 `string` 值。 在这种情况下，可以手动将 `string` 值转换为字节数组以访问原始二进制数据。

## <a name="limits"></a>限制

HTTP 请求长度限制为 100 MB（104,857,600 字节），并且 URL 长度限制为 4 KB（4,096 字节）。 这些限制由运行时的 [Web.config 文件](https://github.com/Azure/azure-functions-host/blob/v3.x/src/WebJobs.Script.WebHost/web.config)的 `httpRuntime` 元素指定。

如果使用 HTTP 触发器的函数未在 230 秒内完成，[Azure 负载均衡器](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds)将超时并返回 HTTP 502 错误。 该函数将继续运行，但将无法返回 HTTP 响应。 对于长时间运行的函数，我们建议你遵循异步模式，并返回可以 ping 通请求状态的位置。 有关函数可以运行多长时间的信息，请参阅[缩放和托管 - 消耗计划](functions-scale.md#timeout)。


## <a name="next-steps"></a>后续步骤

- [从函数返回 HTTP 响应](./functions-bindings-http-webhook-output.md)
