---
title: Azure Functions 自定义处理程序（预览版）
description: 了解如何在任何语言或运行时版本中使用 Azure Functions。
author: anthonychu
ms.author: antchu
ms.date: 8/18/2020
ms.topic: article
ms.openlocfilehash: f3106553def982eb90ccc90822206e75a11ce354
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89294588"
---
# <a name="azure-functions-custom-handlers-preview"></a>Azure Functions 自定义处理程序（预览版）

每个函数应用由特定于语言的处理程序执行。 尽管 Azure Functions 默认支持许多[语言处理程序](./supported-languages.md)，但在某些情况下，你可能需要使用其他语言或运行时。

自定义处理程序是可以从 Functions 主机接收事件的轻型 Web 服务器。 支持 HTTP 基元的任何语言都可以实现自定义处理程序。

自定义处理程序最适合用于以下场合：

- 使用当前不受支持的语言（如 Go 和 Rust）实现函数应用。
- 使用当前不受支持的运行时（如 Deno）实现函数应用。

对于自定义处理程序，可以通过[扩展绑定](./functions-bindings-register.md)来使用[触发器以及输入和输出绑定](./functions-triggers-bindings.md)。

## <a name="overview"></a>概述

下图显示了 Functions 主机与作为自定义处理程序实现的 Web 服务器之间的关系。

![Azure Functions 自定义处理程序概述](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

1. 每个事件都会触发发送到 Functions 主机的请求。 事件是由 Azure Functions 支持的触发器。
1. 然后，Functions 主机向 Web 服务器发出[请求有效负载](#request-payload)。 有效负载保存该函数的触发和输入绑定数据以及其他元数据。
1. Web 服务器执行单个函数，并向 Functions 主机返回[响应有效负载](#response-payload)。
1. Functions 主机将数据从响应传递到函数的输出绑定以进行处理。

作为自定义处理程序实现的 Azure Functions 应用必须根据几条约定配置 host.json、local.settings.json 和 function.json 文件  。

## <a name="application-structure"></a>应用程序结构

若要实现自定义处理程序，需要准备好应用程序的以下方面的内容：

- 位于应用根目录的 host.json 文件
- 位于应用根目录的 local.settings.json 文件
- 每个函数有一个 function.json 文件（位于与函数名称匹配的文件夹中）
- 用于运行 Web 服务器的命令、脚本或可执行文件

下图显示了对于名为“MyQueueFunction”的函数和名为“handler.exe”的自定义处理程序可执行文件，这些文件在文件系统上是什么样子。

```bash
| /MyQueueFunction
|   function.json
|
| host.json
| local.settings.json
| handler.exe
```

### <a name="configuration"></a>配置

应用程序是通过 host.json 和 local.settings.json 文件配置的 。

#### <a name="hostjson"></a>host.json

host.json 告知 Functions 主机要将请求发送到哪个位置，方法是指向能够处理 HTTP 事件的 Web 服务器。

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

使用 `arguments` 数组将任何参数传递给可执行文件。 参数支持使用 `%%` 表示法扩展环境变量（应用程序设置）。

还可以通过 `workingDirectory` 更改可执行文件使用的工作目录。

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

local.settings.json 定义在本地运行函数应用时使用的应用程序设置。 由于 local.settings.json 可能包含机密，因此应将其从源代码管理中排除。 在 Azure 中，改用应用程序设置。

对于自定义处理程序，请在“local.settings.json”中将 `FUNCTIONS_WORKER_RUNTIME` 设置为 `Custom`。

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "Custom"
  }
}
```

> [!NOTE]
> 在 Linux Premium 上或应用服务计划中，`Custom` 可能不会被识别为有效的运行时。 如果这是你的部署目标，则将 `FUNCTIONS_WORKER_RUNTIME` 设置为空字符串。

### <a name="function-metadata"></a>函数元数据

与自定义处理程序配合使用时，function.json 的内容与在任何其他上下文中定义函数时包含的内容没有什么不同。 唯一的要求是 function.json 文件必须位于名称与函数名称匹配的文件夹中。

以下 function.json 配置了一个具有队列触发器和队列输出绑定的函数。 因为它位于名为“MyQueueFunction”的文件夹中，所以它定义了一个名为“MyQueueFunction”的函数 。

**MyQueueFunction/function.json**

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

接收到队列消息后，Functions 主机会将 HTTP 发布请求发送到自定义处理程序，该处理程序的主体中具有有效负载。

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
| `Outputs`     | object    | 保存由 function.json 中的 `bindings` 数组定义的响应值。<br /><br />例如，如果一个函数配置有名为“myQueueOutput”的队列输出绑定，则 `Outputs` 包含一个名为 `myQueueOutput` 的键，该键由自定义处理程序设置为发送到队列的消息。 |
| `Logs`        | array     | 消息将显示在 Functions 调用日志中。<br /><br />在 Azure 中运行时，消息显示在 Application Insights 中。 |
| `ReturnValue` | string    | 将输出配置为 function.json 文件中的 `$return` 时用于提供响应。 |

下面是一个响应有效负载的示例。

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

自定义处理程序可以使用支持接收 HTTP 事件的任何语言实现。 以下示例演示如何使用 Go 编程语言实现自定义处理程序。

### <a name="function-with-bindings"></a>带绑定的函数

此示例中实现的方案使用一个名为 `order` 的函数，该函数接受包含某个表示产品订单的有效负载的 `POST`。 将订单发布到该函数时，会创建队列存储消息并返回 HTTP 响应。

<a id="bindings-implementation" name="bindings-implementation"></a>

#### <a name="implementation"></a>实现

在名为 order 的文件夹中，function.json 文件将配置 HTTP 触发的函数。 

**order/function.json**

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

在应用的根目录中，host.json 文件配置为运行名为 `handler.exe`（在 Linux 或 macOS 中为 `handler`）的可执行文件。

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

这是发送到 Functions 运行时的 HTTP 请求。

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
Content-Type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

然后，Functions 运行时将以下 HTTP 请求发送到自定义处理程序：

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
> 为简洁起见，删除了有效负载的某些部分。

handler.exe 是已编译的 Go 自定义处理程序，它运行 Web 服务器并响应来自 Functions 主机的函数调用请求。

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

在此示例中，自定义处理程序运行一个 Web 服务器来处理 HTTP 事件，并设置为通过 `FUNCTIONS_CUSTOMHANDLER_PORT` 侦听请求。

尽管 Functions 主机在 `/api/order` 处收到原始 HTTP 请求，它也会使用函数名称（其文件夹名称）来调用自定义处理程序。 在此示例中，该函数在 `/order` 路径中定义。 主机在 `/order` 路径中向自定义处理程序发送 HTTP 请求。

当 `POST` 请求发送到此函数时，可通过 HTTP 请求正文获取触发器数据和函数元数据。 可以在有效负载的 `Data.req.Body` 中访问原始 HTTP 请求正文。

函数的响应采用键/值对格式，其中，`Outputs` 成员保存一个 JSON 值，该值中的键与 function.json 文件中定义的输出相匹配。

下面是此处理程序返回到 Functions 主机的示例有效负载。

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

通过将 `message` 输出设置为等于来自请求的订单数据，该函数将该订单数据输出到已配置的队列。 Functions 主机还会将在 `res` 中配置的 HTTP 响应返回给调用方。

### <a name="http-only-function"></a>仅限 HTTP 的函数

对于没有其他绑定或输出的 HTTP 触发的函数，你可能希望处理程序直接处理 HTTP 请求和响应，而不是处理自定义处理程序[请求](#request-payload)和[响应](#response-payload)有效负载。 可以使用 `enableForwardingHttpRequest` 设置在 host.json 中配置此行为。

> [!IMPORTANT]
> 自定义处理程序功能的主要用途是在 Azure Functions 上启用当前不具有一流支持的语言和运行时。 虽然可以使用自定义处理程序运行 Web 应用程序，但 Azure Functions 不是标准的反向代理。 某些功能（如响应流式处理、HTTP/2 和 Websocket）不可用。 HTTP 请求的某些组件（如某些标头和路由）可能会受到限制。 应用程序也可能遇到过多的[冷启动](functions-scale.md#cold-start)。
>
> 若要解决这些情况，请考虑在 [Azure 应用服务](../app-service/overview.md)上运行 Web 应用。

以下示例演示如何配置一个没有其他绑定或输出的 HTTP 触发函数。 此示例中实现的方案使用了一个名为 `hello` 的、接受 `GET` 或 `POST` 的函数。

<a id="hello-implementation" name="hello-implementation"></a>

#### <a name="implementation"></a>实现

在名为“hello”的文件夹中，function.json 文件会配置 HTTP 触发的函数 。

**hello/function.json**

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

在应用的根目录中，host.json 文件配置为运行 `handler.exe`，`enableForwardingHttpRequest` 设置为 `true`。

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

如果 `enableForwardingHttpRequest` 为 `true`，仅限 HTTP 的函数的行为与默认的自定义处理程序行为之间存在以下区别：

* HTTP 请求不包含自定义处理程序[请求](#request-payload)有效负载。 Functions 主机使用原始 HTTP 请求的副本调用处理程序。
* Functions 主机通过与原始请求相同的路径调用处理程序，包括任何查询字符串参数。
* Functions 主机返回处理程序的 HTTP 响应副本作为对原始请求的响应。

下面是对 Functions 主机的 POST 请求。 然后，Functions 主机将请求的副本发送到相同路径中的自定义处理程序。

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
Content-Type: application/json

{
  "message": "Hello World!"
}
```

文件 handler.go 实现 Web 服务器和 HTTP 函数。

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

在此示例中，自定义处理程序创建一个 Web 服务器来处理 HTTP 事件，并设置为通过 `FUNCTIONS_CUSTOMHANDLER_PORT` 侦听请求。

通过返回一个字符串来处理 `GET` 请求，`POST` 请求具有对请求正文的访问权限。

此处订单函数的路由为 `/api/hello`，与原始请求相同。

>[!NOTE]
>`FUNCTIONS_CUSTOMHANDLER_PORT` 不是用于调用函数的面向公众的端口。 此端口由 Functions 主机用来调用自定义处理程序。

## <a name="deploying"></a>正在部署

可将自定义处理程序部署到每个 Azure Functions 托管选项。 如果处理程序需要操作系统或平台依赖项 (如语言运行时) ，则可能需要使用 [自定义容器](./functions-create-function-linux-custom-image.md)。

在 Azure 中为自定义处理程序创建函数应用时，建议选择 .NET Core 作为堆栈。 未来将为自定义处理程序添加“自定义”堆栈。

若要使用 Azure Functions Core Tools 部署自定义处理程序应用，请运行以下命令。

```bash
func azure functionapp publish $functionAppName
```

> [!NOTE]
> 请确保运行自定义处理程序所需的所有文件都位于文件夹中，并包含在部署中。 如果自定义处理程序是二进制可执行文件或具有特定于平台的依赖项，请确保这些文件与目标部署平台相匹配。

## <a name="restrictions"></a>限制

- 自定义处理程序 Web 服务器需要在 60 秒内启动。

## <a name="samples"></a>示例

有关如何在各种不同的语言中实现函数的示例，请参阅[自定义处理程序示例 GitHub 存储库](https://github.com/Azure-Samples/functions-custom-handlers)。

## <a name="troubleshooting-and-support"></a>故障排除和支持

### <a name="trace-logging"></a>跟踪日志记录

如果自定义处理程序进程无法启动，或在与 Functions 主机通信时出现问题，可将函数应用的日志级别提高到 `Trace` 以查看来自主机的更多诊断消息。

若要更改函数应用的默认日志级别，请在 host.json 的 `logging` 部分中配置 `logLevel` 设置。

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

Functions 主机输出额外的日志消息，包括与自定义处理程序进程相关的信息。 使用日志调查启动自定义处理程序进程或调用自定义处理程序中的函数时所遇到的问题。

日志在本地被输出到控制台。

在 Azure 中，[查询 Application Insights 跟踪](functions-monitoring.md#query-telemetry-data)以查看日志消息。 如果应用生成大量日志，则只有一个日志消息子集会被发送到 Application Insights。 [禁用采样](functions-monitoring.md#configure-sampling)以确保记录所有消息。

### <a name="test-custom-handler-in-isolation"></a>以隔离方式测试自定义处理程序

自定义处理程序应用是一个 Web 服务器进程，因此，通过使用 [cURL](https://curl.haxx.se/) 或 [Postman](https://www.postman.com/) 等工具发送模拟 [HTTP 请求](#request-payload)来自行启动该应用程序并测试函数调用可能会有所帮助。

还可以在 CI/CD 管道中使用此策略以在自定义处理程序上运行自动化测试。

### <a name="execution-environment"></a>执行环境

自定义处理程序与典型 Azure Functions 应用在同一环境中运行。 测试处理程序，以确保环境包含其运行所需的所有依赖项。 对于需要其他依赖项的应用程序，你可能需要使用托管在 Azure Functions[高级计划](functions-premium-plan.md)中的[自定义容器映像](functions-create-function-linux-custom-image.md)来运行它们。

### <a name="get-support"></a>获取支持

如果你需要有关包含自定义处理程序的函数应用的帮助，可通过常规支持渠道提交请求。 但是，由于用于生成自定义处理程序应用的语言多种多样，因此支持并不是无限的。

如果 Functions 主机在启动自定义处理程序进程或与自定义处理程序进程通信时遇到问题，可以获取相关支持。 对于针对自定义处理程序进程的内部工作原理的问题（例如所选语言或框架的问题），我们的支持团队无法在此上下文中提供帮助。
