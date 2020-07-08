---
title: Azure Functions 自定义处理程序（预览版）
description: 了解如何在任何语言或运行时版本中使用 Azure Functions。
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: cdbb5bbde1e5efef9bef992a62a54f1525a16df7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85052584"
---
# <a name="azure-functions-custom-handlers-preview"></a>Azure Functions 自定义处理程序（预览版）

每个函数应用由特定于语言的处理程序执行。 尽管 Azure Functions 默认支持许多[语言处理程序](./supported-languages.md)，但在某些情况下，你可能想要对应用执行环境进行额外的控制。 自定义处理程序能够提供这种额外的控制。

自定义处理程序是可以从 Functions 主机接收事件的轻型 Web 服务器。 支持 HTTP 基元的任何语言都可以实现自定义处理程序。

自定义处理程序最适合用于以下场合：

- 使用并非正式支持的语言来实现函数应用。
- 默认情况下，不支持在语言版本或运行时中实现函数应用。
- 提供对函数应用执行环境的更精细控制。

使用自定义处理程序时，将通过[扩展绑定](./functions-bindings-register.md)来支持所有[触发器以及输入和输出绑定](./functions-triggers-bindings.md)。

## <a name="overview"></a>概述

下图显示了 Functions 主机与作为自定义处理程序实现的 Web 服务器之间的关系。

![Azure Functions 自定义处理程序概述](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- 事件触发发送到 Functions 主机的请求。 事件携带原始 HTTP 有效负载（适用于不带绑定的 HTTP 触发函数），或携带包含函数输入绑定数据的有效负载。
- 然后，Functions 主机通过发出[请求有效负载](#request-payload)，以代理身份将请求发送到 Web 服务器。
- Web 服务器执行单个函数，并向 Functions 主机返回[响应有效负载](#response-payload)。
- Functions 主机以代理身份将输出绑定有效负载形式的响应发送到目标。

作为自定义处理程序实现的 Azure Functions 应用必须根据几条约定配置 host.json 和 function.json 文件。  

## <a name="application-structure"></a>应用程序结构

若要实现自定义处理程序，需要准备好应用程序的以下方面的内容：

- 位于应用根目录的 host.json 文件 
- 每个函数有一个 function.json 文件（位于与函数名称匹配的文件夹中） 
- 用于运行 Web 服务器的命令、脚本或可执行文件

下图显示了名为“order”的函数的这些文件在文件系统中的结构。

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>配置

应用程序是通过 host.json 文件配置的。  此文件指向能够处理 HTTP 事件的 Web 服务器，以此告知 Functions 主机要将请求发送到哪个位置。

自定义处理程序的定义方式是通过 `httpWorker` 节在 host.json 文件中配置有关如何运行 Web 服务器的详细信息。

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "server.exe"
        }
    }
}
```

`httpWorker` 节指向 `defaultExecutablePath` 定义的目标。 执行目标可以某个命令、可执行文件，或实现 Web 服务器的文件。

对于脚本化应用，`defaultExecutablePath` 指向脚本语言的运行时，`defaultWorkerPath` 指向脚本文件位置。 以下示例演示如何将 Node.js 中的 JavaScript 应用配置为自定义处理程序。

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

也可以使用 `arguments` 数组传递参数：

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--argument1", "--argument2" ]
        }
    }
}
```

许多调试设置都需要参数。 有关更多详细信息，请参阅[调试](#debugging)部分。

> [!NOTE]
> *host.json* 文件在目录结构中的级别必须与正在运行的 Web 服务器的级别相同。 某些语言和工具链默认可能不会将此文件放在应用程序根目录中。

#### <a name="bindings-support"></a>绑定支持

可以通过在 host.json 文件中引用[扩展捆绑](./functions-bindings-register.md)来使用标准触发器以及输入和输出绑定。 

### <a name="function-metadata"></a>函数元数据

与自定义处理程序配合使用时，function.json 的内容与在任何其他上下文中定义函数时包含的内容没有什么不同。  唯一的要求是 function.json 文件必须位于名称与函数名称匹配的文件夹中。 

### <a name="request-payload"></a>请求有效负载

纯 HTTP 函数的请求有效负载是原始 HTTP 请求有效负载。 纯 HTTP 函数定义为不带输入或输出绑定的、返回 HTTP 响应的函数。

包含输入、输出绑定的，或者通过非 HTTP 的事件源触发的任何其他类型的函数具有自定义请求有效负载。

以下代码是一个示例请求有效负载。 该有效负载中的 JSON 结构包含以下两个成员：`Data` 和 `Metadata`。

`Data` 成员包含与 function.json 文件中的绑定数组内定义的输入和触发器名称匹配的键。 

`Metadata` 成员包含[从事件源生成的元数据](./functions-bindings-expressions-patterns.md#trigger-metadata)。

假设以下 function.json 文件中定义的绑定是： 

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

将返回类似于以下示例的请求有效负载：

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
| `Outputs`     | JSON      | 保存 function.json 文件中 `bindings` 数组定义的响应值。 <br /><br />例如，如果为某个函数定义了名为“blob”的 blob 存储输出绑定，则 `Outputs` 将包含名为 `blob` 的键，此键设置为 blob 的值。 |
| `Logs`        | array     | 消息将显示在 Functions 调用日志中。<br /><br />在 Azure 中运行时，消息显示在 Application Insights 中。 |
| `ReturnValue` | 字符串    | 用于在将输出配置为 `$return` 文件中的*function.js*时提供响应。 |

[有关示例负载](#bindings-implementation)，请参阅示例。

## <a name="examples"></a>示例

自定义处理程序可以采用任何支持 HTTP 事件的语言来实现。 尽管 Azure Functions[完全支持 JavaScript 和 Node.js](./functions-reference-node.md)，但下面的示例演示如何在 Node.js 中使用 JavaScript 来实现自定义处理程序，以便进行说明。

> [!TIP]
> 尽管是学习如何在其他语言中实现自定义处理程序的指南，但是，如果想要在不支持的 Node.js 版本中运行函数应用，则此处所示的基于 Node.js 的示例也可能会很有用。

## <a name="http-only-function"></a>仅 HTTP 函数

下面的示例演示如何配置没有其他绑定或输出的 HTTP 触发的函数。 本示例中实现的方案具有一个名为 `http` 的函数，该函数接受 `GET` 或 `POST` 。

下面的代码片段表示如何编写对函数的请求。

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>实现

在名为 " *http*" 的文件夹中，" *function.js* " 文件配置 http 触发的函数。

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
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

该函数配置为接受 `GET` 和 `POST` 请求，结果值通过名为的参数提供 `res` 。

在应用程序的根目录，文件*上的host.js*配置为运行 Node.js 并指向该 `server.js` 文件。

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

文件*server.js*文件实现 web 服务器和 HTTP 函数。

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.get("/hello", (req, res) => {
  res.json("Hello World!");
});

app.post("/hello", (req, res) => {
  res.json({ value: req.body });
});
```

在此示例中，Express 用于创建 web 服务器来处理 HTTP 事件，并设置为通过侦听请求 `FUNCTIONS_HTTPWORKER_PORT` 。

函数在的路径中定义 `/hello` 。 `GET`通过返回一个简单的 JSON 对象来处理请求，并且 `POST` 请求可以通过访问请求正文 `req.body` 。

此处的 order 函数的路由为 `/hello` ，而不是 `/api/hello` 因为函数主机将请求代理到自定义处理程序。

>[!NOTE]
>不 `FUNCTIONS_HTTPWORKER_PORT` 是用于调用函数的面向公众的端口。 函数主机使用此端口调用自定义处理程序。

## <a name="function-with-bindings"></a>带有绑定的函数

本示例中实现的方案提供了一个名为 `order` 的函数，该函数接受一个 `POST` 表示产品订单的有效负载。 在将订单发送到函数时，将创建队列存储消息并返回 HTTP 响应。

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
content-type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

<a id="bindings-implementation" name="bindings-implementation"></a>

### <a name="implementation"></a>实现

在名为*order*的文件夹中， *function.js*文件配置 HTTP 触发的函数。

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
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

此函数定义为[http 触发函数](./functions-bindings-http-webhook-trigger.md)，该函数返回[http 响应](./functions-bindings-http-webhook-output.md)并输出[队列存储](./functions-bindings-storage-queue-output.md)消息。

在应用程序的根目录，文件*上的host.js*配置为运行 Node.js 并指向该 `server.js` 文件。

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

文件*server.js*文件实现 web 服务器和 HTTP 函数。

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.post("/order", (req, res) => {
  const message = req.body.Data.req.Body;
  const response = {
    Outputs: {
      message: message,
      res: {
        statusCode: 200,
        body: "Order complete"
      }
    },
    Logs: ["order processed"]
  };
  res.json(response);
});
```

在此示例中，Express 用于创建 web 服务器来处理 HTTP 事件，并设置为通过侦听请求 `FUNCTIONS_HTTPWORKER_PORT` 。

函数在的路径中定义 `/order` 。  此处的 order 函数的路由为 `/order` ，而不是 `/api/order` 因为函数主机将请求代理到自定义处理程序。

当 `POST` 向此函数发送请求时，将通过几个点公开数据：

- 请求正文可通过`req.body`
- 发布到函数的数据可通过`req.body.Data.req.Body`

函数的响应被格式化为一个键/值对，其中的 `Outputs` 成员保存一个 JSON 值，其中的键与文件中的*function.js*中定义的输出相匹配。

通过将 `message` 等于从请求传入的消息和 `res` 预期 HTTP 响应，此函数会将消息输出到队列存储并返回 HTTP 响应。

## <a name="debugging"></a>调试

若要调试函数自定义处理程序应用，需要添加适用于语言和运行时的参数来启用调试。

例如，若要调试 Node.js 应用程序， `--inspect` 标志将作为参数传递到文件的*host.js*中。

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--inspect" ]
        }
    }
}
```

> [!NOTE]
> 调试配置是您的*host.js*文件的一部分，这意味着在部署到生产环境之前，您可能需要删除一些参数。

使用此配置，可以使用以下命令启动函数的主机进程：

```bash
func host start
```

启动进程后，可以附加调试器并命中断点。

### <a name="visual-studio-code"></a>Visual Studio Code

下面的示例演示如何在文件中设置*launch.js* ，以将应用程序连接到 Visual Studio Code 调试器。

此示例用于 Node.js，因此你可能需要为其他语言或运行时更改此示例。

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to Node Functions",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "preLaunchTask": "func: host start"
    }
  ]
}
```

## <a name="deploying"></a>正在部署

可以将自定义处理程序部署到几乎每个 Azure Functions 承载选项（请参阅[限制](#restrictions)）。 如果处理程序需要自定义依赖项（如语言运行时），则可能需要使用[自定义容器](./functions-create-function-linux-custom-image.md)。

若要使用 Azure Functions Core Tools 部署自定义处理程序应用，请运行以下命令。

```bash
func azure functionapp publish $functionAppName --no-build --force
```

## <a name="restrictions"></a>限制

- Web 服务器需要在60秒内启动。

## <a name="samples"></a>示例

有关如何使用各种不同的语言实现函数的示例，请参阅[自定义处理程序示例 GitHub](https://github.com/Azure-Samples/functions-custom-handlers)存储库。
