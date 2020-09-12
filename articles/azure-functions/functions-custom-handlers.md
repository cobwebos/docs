---
title: Azure Functions 自定义处理程序（预览版）
description: 了解如何在任何语言或运行时版本中使用 Azure Functions。
author: anthonychu
ms.author: antchu
ms.date: 8/18/2020
ms.topic: article
ms.openlocfilehash: f3106553def982eb90ccc90822206e75a11ce354
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89294588"
---
# <a name="azure-functions-custom-handlers-preview"></a>Azure Functions 自定义处理程序（预览版）

每个函数应用由特定于语言的处理程序执行。 虽然默认情况下 Azure Functions 支持许多 [语言处理程序](./supported-languages.md) ，但在某些情况下，你可能想要使用其他语言或运行时。

自定义处理程序是可以从 Functions 主机接收事件的轻型 Web 服务器。 支持 HTTP 基元的任何语言都可以实现自定义处理程序。

自定义处理程序最适合用于以下场合：

- 使用目前不受支持的语言（如中转和 Rust）来实现函数应用。
- 在当前不受支持的运行时（如 Deno）中实现函数应用。

使用自定义处理程序，可以通过[扩展捆绑](./functions-bindings-register.md)使用[触发器和输入绑定和输出绑定](./functions-triggers-bindings.md)。

## <a name="overview"></a>概述

下图显示了 Functions 主机与作为自定义处理程序实现的 Web 服务器之间的关系。

![Azure Functions 自定义处理程序概述](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

1. 每个事件都会触发发送到函数主机的请求。 事件是 Azure Functions 支持的任何触发器。
1. 然后，函数主机将 [请求负载](#request-payload) 颁发给 web 服务器。 负载保存函数的触发器和输入绑定数据以及其他元数据。
1. Web 服务器执行单个函数，并向 Functions 主机返回[响应有效负载](#response-payload)。
1. 函数主机将数据从响应传递到函数的输出绑定，以便进行处理。

作为自定义处理程序实现的 Azure Functions 应用程序必须根据几个约定在文件上配置 *host.js*、 *local.settings.js*和 *function.js* 。

## <a name="application-structure"></a>应用程序结构

若要实现自定义处理程序，需要准备好应用程序的以下方面的内容：

- 位于应用根目录的 host.json 文件
- 应用根目录中的文件*local.settings.js*
- 每个函数有一个 function.json 文件（位于与函数名称匹配的文件夹中）
- 用于运行 Web 服务器的命令、脚本或可执行文件

下图显示了这些文件如何在文件系统中查找名为 "MyQueueFunction" 的函数和名为 *handler.exe*的自定义处理程序可执行文件。

```bash
| /MyQueueFunction
|   function.json
|
| host.json
| local.settings.json
| handler.exe
```

### <a name="configuration"></a>配置

应用程序通过 * 上的host.js* 和文件 * 上的local.settings.js* 进行配置。

#### <a name="hostjson"></a>host.json

*host.js* 指示函数主机在何处发送请求，方法是指向能够处理 HTTP 事件的 web 服务器。

自定义处理程序的定义方式是通过 `customHandler` 节在 host.json 文件中配置有关如何运行 Web 服务器的详细信息。

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  }
}
```

`customHandler` 节指向 `defaultExecutablePath` 定义的目标。 执行目标可以某个命令、可执行文件，或实现 Web 服务器的文件。

使用 `arguments` 数组将任何参数传递给可执行文件。 自变量支持 (应用程序设置) 使用 `%%` 表示法来扩展环境变量。

还可以更改可执行文件使用的工作目录 `workingDirectory` 。

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "app/handler.exe",
      "arguments": [
        "--database-connection-string",
        "%DATABASE_CONNECTION_STRING%"
      ],
      "workingDirectory": "app"
    }
  }
}
```

##### <a name="bindings-support"></a>绑定支持

可以通过在 host.json 文件中引用[扩展捆绑](./functions-bindings-register.md)来使用标准触发器以及输入和输出绑定。

#### <a name="localsettingsjson"></a>local.settings.json

*local.settings.js定义在* 本地运行函数应用时使用的应用程序设置。 由于它可能包含机密， *local.settings.js* 应从源代码管理中排除。 在 Azure 中，改用应用程序设置。

对于自定义处理程序，将设置 `FUNCTIONS_WORKER_RUNTIME` 为 `Custom` 中 *local.settings.json*。

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "Custom"
  }
}
```

> [!NOTE]
> `Custom` 不能被识别为 Linux Premium 或应用服务计划中的有效运行时。 如果这是你的部署目标，则将设置 `FUNCTIONS_WORKER_RUNTIME` 为空字符串。

### <a name="function-metadata"></a>函数元数据

与自定义处理程序配合使用时，function.json 的内容与在任何其他上下文中定义函数时包含的内容没有什么不同。 唯一的要求是 function.json 文件必须位于名称与函数名称匹配的文件夹中。

上的以下 *function.js* 配置具有队列触发器和队列输出绑定的函数。 由于它位于名为 *MyQueueFunction*的文件夹中，因此它定义了一个名为 *MyQueueFunction*的函数。

**MyQueueFunction/function.js**

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

### <a name="request-payload"></a>请求有效负载

接收队列消息时，函数会将 HTTP post 请求发送到带有主体中的有效负载的自定义处理程序。

以下代码是一个示例请求有效负载。 该有效负载中的 JSON 结构包含以下两个成员：`Data` 和 `Metadata`。

`Data` 成员包含与 function.json 文件中的绑定数组内定义的输入和触发器名称匹配的键。

`Metadata` 成员包含[从事件源生成的元数据](./functions-bindings-expressions-patterns.md#trigger-metadata)。

```json
{
  "Data": {
    "myQueueItem": "{ message: \"Message sent\" }"
  },
  "Metadata": {
    "DequeueCount": 1,
    "ExpirationTime": "2019-10-16T17:58:31+00:00",
    "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
    "InsertionTime": "2019-10-09T17:58:31+00:00",
    "NextVisibleTime": "2019-10-09T18:08:32+00:00",
    "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
    "sys": {
      "MethodName": "QueueTrigger",
      "UtcNow": "2019-10-09T17:58:32.2205399Z",
      "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
    }
  }
}
```

### <a name="response-payload"></a>响应有效负载

根据约定，函数响应采用键/值对格式。 支持的键包括：

| <nobr>有效负载键</nobr>   | 数据类型 | 备注                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | object    | 保留中function.js的数组定义的响应值 `bindings` 。 *function.json*<br /><br />例如，如果使用名为 "myQueueOutput" 的队列输出绑定来配置函数，则 `Outputs` 包含名为的键 `myQueueOutput` ，该键由自定义处理程序设置为发送到队列的消息。 |
| `Logs`        | array     | 消息将显示在 Functions 调用日志中。<br /><br />在 Azure 中运行时，消息显示在 Application Insights 中。 |
| `ReturnValue` | string    | 将输出配置为 function.json 文件中的 `$return` 时用于提供响应。 |

这是一个响应负载的示例。

```json
{
  "Outputs": {
    "res": {
      "body": "Message enqueued"
    },
    "myQueueOutput": [
      "queue message 1",
      "queue message 2"
    ]
  },
  "Logs": [
    "Log message 1",
    "Log message 2"
  ],
  "ReturnValue": "{\"hello\":\"world\"}"
}
```

## <a name="examples"></a>示例

自定义处理程序可以采用支持接收 HTTP 事件的任何语言来实现。 下面的示例演示如何使用中转编程语言实现自定义处理程序。

### <a name="function-with-bindings"></a>带绑定的函数

此示例中实现的方案使用一个名为 `order` 的函数，该函数接受包含某个表示产品订单的有效负载的 `POST`。 将订单发布到该函数时，会创建队列存储消息并返回 HTTP 响应。

<a id="bindings-implementation" name="bindings-implementation"></a>

#### <a name="implementation"></a>实现

在名为 order 的文件夹中，function.json 文件将配置 HTTP 触发的函数。 

**订单/function.js**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

此函数定义为返回 [HTTP 响应](./functions-bindings-http-webhook-output.md)并输出[队列存储](./functions-bindings-storage-queue-output.md)消息的 [HTTP 触发的函数](./functions-bindings-http-webhook-trigger.md)。

在应用程序的根目录中，文件 * 上的host.js* 配置为 `handler.exe` `handler` 在 Linux 或 macOS) 中运行名为 (的可执行文件。

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[1.*, 2.0.0)"
  }
}
```

这是发送到函数运行时的 HTTP 请求。

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
Content-Type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

然后，函数运行时将以下 HTTP 请求发送到自定义处理程序：

```http
POST http://127.0.0.1:<FUNCTIONS_CUSTOMHANDLER_PORT>/order HTTP/1.1
Content-Type: application/json

{
  "Data": {
    "req": {
      "Url": "http://localhost:7071/api/order",
      "Method": "POST",
      "Query": "{}",
      "Headers": {
        "Content-Type": [
          "application/json"
        ]
      },
      "Params": {},
      "Body": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}"
    }
  },
  "Metadata": {
  }
}
```

> [!NOTE]
> 为了简洁起见，已经删除了负载的某些部分。

*handler.exe* 是运行 web 服务器的已编译的 "开始" 自定义处理程序，用于响应来自函数主机的函数调用请求。

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "os"
)

type InvokeRequest struct {
    Data     map[string]json.RawMessage
    Metadata map[string]interface{}
}

type InvokeResponse struct {
    Outputs     map[string]interface{}
    Logs        []string
    ReturnValue interface{}
}

func orderHandler(w http.ResponseWriter, r *http.Request) {
    var invokeRequest InvokeRequest

    d := json.NewDecoder(r.Body)
    d.Decode(&invokeRequest)

    var reqData map[string]interface{}
    json.Unmarshal(invokeRequest.Data["req"], &reqData)

    outputs := make(map[string]interface{})
    outputs["message"] = reqData["Body"]

    resData := make(map[string]interface{})
    resData["body"] = "Order enqueued"
    outputs["res"] = resData
    invokeResponse := InvokeResponse{outputs, nil, nil}

    responseJson, _ := json.Marshal(invokeResponse)

    w.Header().Set("Content-Type", "application/json")
    w.Write(responseJson)
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/order", orderHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

在此示例中，自定义处理程序运行 web 服务器来处理 HTTP 事件，并将设置为通过侦听请求 `FUNCTIONS_CUSTOMHANDLER_PORT` 。

即使函数主机接收到的原始 HTTP 请求 `/api/order` ，它也会使用函数名称调用自定义处理程序， (其文件夹名称) 。 在此示例中，函数是在的路径中定义的 `/order` 。 宿主在的路径发送自定义处理程序的 HTTP 请求 `/order` 。

将 `POST` 请求发送到此函数时，可以通过 HTTP 请求正文获取触发器数据和函数元数据。 可以在负载的中访问原始 HTTP 请求正文 `Data.req.Body` 。

函数的响应被格式化为键/值对，其中的 `Outputs` 成员保存一个 JSON 值，其中的键与文件中的 *function.js* 中定义的输出相匹配。

这是此处理程序向函数主机返回的示例负载。

```json
{
  "Outputs": {
    "message": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}",
    "res": {
      "body": "Order enqueued"
    }
  },
  "Logs": null,
  "ReturnValue": null
}
```

通过将输出设置为与 `message` 来自请求的订单数据相等，该函数会将顺序数据输出到配置的队列。 函数主机还将在中配置的 HTTP 响应返回 `res` 给调用方。

### <a name="http-only-function"></a>仅限 HTTP 的函数

对于没有其他绑定或输出的 HTTP 触发函数，你可能希望处理程序直接使用 HTTP 请求和响应，而不是自定义处理程序 [请求](#request-payload) 和 [响应](#response-payload) 负载。 此行为可在 *host.js上* 使用设置进行配置 `enableForwardingHttpRequest` 。

> [!IMPORTANT]
> 自定义处理程序功能的主要用途是在 Azure Functions 上启用当前不具有一流支持的语言和运行时。 尽管可以使用自定义处理程序运行 web 应用程序，但 Azure Functions 不是标准的反向代理。 某些功能（如响应流、HTTP/2 和 Websocket）不可用。 某些 HTTP 请求组件（如某些标头和路由）可能会受到限制。 您的应用程序也可能遇到过多的 [冷启动](functions-scale.md#cold-start)。
>
> 若要解决这种情况，请考虑在 [Azure App Service](../app-service/overview.md)上运行 web 应用。

以下示例演示如何配置一个没有其他绑定或输出的 HTTP 触发函数。 此示例中实现的方案使用了一个名为 `hello` 的、接受 `GET` 或 `POST` 的函数。

<a id="hello-implementation" name="hello-implementation"></a>

#### <a name="implementation"></a>实现

在名为 " *hello*" 的文件夹中，" *function.js* " 文件配置 HTTP 触发的函数。

**function.js上**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

该函数配置为接受 `GET` 和 `POST` 请求，结果值通过名为 `res` 的参数提供。

在应用程序的根位置，将文件 * 上的host.js* 配置为运行， `handler.exe` 并 `enableForwardingHttpRequest` 将设置为 `true` 。

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    },
    "enableForwardingHttpRequest": true
  }
}
```

当 `enableForwardingHttpRequest` 为时 `true` ，仅限 HTTP 函数的行为与默认的自定义处理程序行为不同：

* HTTP 请求不包含自定义处理程序 [请求](#request-payload) 负载。 相反，函数 host 使用原始 HTTP 请求的副本调用处理程序。
* 函数 host 调用具有与原始请求相同的路径的处理程序，包括任何查询字符串参数。
* 函数主机返回处理程序的 HTTP 响应副本，作为对原始请求的响应。

下面是对函数主机的 POST 请求。 然后，函数主机将请求的副本发送到相同路径中的自定义处理程序。

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
Content-Type: application/json

{
  "message": "Hello World!"
}
```

文件 *处理程序* 文件将实现 web 服务器和 HTTP 函数。

```go
package main

import (
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
)

func helloHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    if r.Method == "GET" {
        w.Write([]byte("hello world"))
    } else {
        body, _ := ioutil.ReadAll(r.Body)
        w.Write(body)
    }
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/api/hello", helloHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

在此示例中，自定义处理程序将创建一个 web 服务器来处理 HTTP 事件，并将设置为通过侦听请求 `FUNCTIONS_CUSTOMHANDLER_PORT` 。

`GET` 通过返回字符串来处理请求，并且 `POST` 请求可以访问请求正文。

此处的 order 函数的路由与 `/api/hello` 原始请求相同。

>[!NOTE]
>`FUNCTIONS_CUSTOMHANDLER_PORT` 不是用于调用函数的面向公众的端口。 此端口由 Functions 主机用来调用自定义处理程序。

## <a name="deploying"></a>正在部署

自定义处理程序可部署到每个 Azure Functions 承载选项。 如果处理程序需要操作系统或平台依赖项 (如语言运行时) ，则可能需要使用 [自定义容器](./functions-create-function-linux-custom-image.md)。

在 Azure 中为自定义处理程序创建函数应用时，建议选择 .NET Core 作为堆栈。 未来将添加自定义处理程序的 "自定义" 堆栈。

若要使用 Azure Functions Core Tools 部署自定义处理程序应用，请运行以下命令。

```bash
func azure functionapp publish $functionAppName
```

> [!NOTE]
> 确保运行自定义处理程序所需的所有文件都位于文件夹中，并包含在部署中。 如果自定义处理程序是二进制可执行文件或具有平台特定的依赖项，请确保这些文件与目标部署平台匹配。

## <a name="restrictions"></a>限制

- 自定义处理程序 web 服务器需要在60秒内启动。

## <a name="samples"></a>示例

有关如何在各种不同的语言中实现函数的示例，请参阅[自定义处理程序示例 GitHub 存储库](https://github.com/Azure-Samples/functions-custom-handlers)。

## <a name="troubleshooting-and-support"></a>故障排除和支持

### <a name="trace-logging"></a>跟踪日志记录

如果自定义处理程序过程无法启动，或者它在与函数主机通信时出现问题，则可以将 function app 的日志级别增加到， `Trace` 以查看来自主机的更多诊断消息。

若要更改函数应用的默认日志级别，请 `logLevel` 在的 "host.js" 部分中配置设置 `logging` 。 *host.json*

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "logging": {
    "logLevel": {
      "default": "Trace"
    }
  }
}
```

函数主机输出额外的日志消息，包括与自定义处理程序进程相关的信息。 使用日志来调查在自定义处理程序过程中开始或在自定义处理程序中调用函数时出现的问题。

在本地，将日志输出到控制台。

在 Azure 中， [查询 Application Insights 跟踪](functions-monitoring.md#query-telemetry-data) 以查看日志消息。 如果应用生成大量日志，则只会将一条日志消息发送到 Application Insights。 [禁用采样](functions-monitoring.md#configure-sampling) 以确保记录所有消息。

### <a name="test-custom-handler-in-isolation"></a>独立测试自定义处理程序

自定义处理程序应用程序是一种 web 服务器进程，因此，通过使用一种类似于[卷](https://curl.haxx.se/)或[Postman](https://www.postman.com/)的工具发送模拟[HTTP 请求](#request-payload)，可以在自己的过程中启动该应用程序并对其进行测试。

你还可以使用 CI/CD 管道中的此策略在自定义处理程序上运行自动测试。

### <a name="execution-environment"></a>执行环境

自定义处理程序与典型 Azure Functions 应用在同一环境中运行。 测试您的处理程序，以确保环境包含它需要运行的所有依赖项。 对于需要其他依赖项的应用程序，你可能需要使用托管在 Azure Functions[高级计划](functions-premium-plan.md)中的[自定义容器映像](functions-create-function-linux-custom-image.md)来运行它们。

### <a name="get-support"></a>获取支持

如果需要使用自定义处理程序的函数应用的相关帮助，可以通过常规支持渠道提交请求。 但是，由于用于构建自定义处理程序应用的各种可能的语言，支持并不是无限的。

如果函数主机在启动或与自定义处理程序进程通信时出现问题，则可以使用支持。 对于特定于自定义处理程序过程的内部工作原理的问题（例如，所选语言或框架的问题），我们的支持团队无法在此上下文中提供帮助。
