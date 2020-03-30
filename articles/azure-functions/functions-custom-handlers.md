---
title: Azure 函数自定义处理程序（预览）
description: 了解如何将 Azure 函数与任何语言或运行时版本一起使用。
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: 5abc216e182d7becd9d6f42e0f566ee96d09c2a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479249"
---
# <a name="azure-functions-custom-handlers-preview"></a>Azure 函数自定义处理程序（预览）

每个函数应用都由特定于语言的处理程序执行。 虽然默认情况下 Azure 函数支持许多[语言处理程序](./supported-languages.md)，但在某些情况下，您可能需要对应用执行环境进行额外控制。 自定义处理程序为您提供此附加控件。

自定义处理程序是从函数主机接收事件的轻量级 Web 服务器。 任何支持 HTTP 基元的语言都可以实现自定义处理程序。

自定义处理程序最适合您希望：

- 在官方支持的语言之外，使用语言实现函数应用
- 在语言版本或运行时中实现默认情况下不支持的功能应用
- 对应用执行环境进行精细控制

对于自定义处理程序，所有[触发器以及输入和输出绑定](./functions-triggers-bindings.md)都通过[扩展包](./functions-bindings-register.md)支持。

## <a name="overview"></a>概述

下图显示了函数主机和作为自定义处理程序实现的 Web 服务器之间的关系。

![Azure 函数自定义处理程序概述](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- 事件触发发送到函数主机的请求。 该事件携带原始 HTTP 负载（对于没有绑定的 HTTP 触发函数）或保存函数的输入绑定数据的有效负载。
- 然后，函数主机通过发出[请求负载](#request-payload)将请求代理到 Web 服务器。
- Web 服务器执行单个函数，并将[响应负载](#response-payload)返回给函数主机。
- 函数主机将响应代理为目标的输出绑定负载。

作为自定义处理程序实现的 Azure 函数应用必须根据几个约定配置*host.json*和*函数.json*文件。

## <a name="application-structure"></a>应用程序结构

要实现自定义处理程序，需要应用程序中的以下几个方面：

- 应用根目录的*host.json*文件
- 每个*函数的函数.json*文件（在与函数名称匹配的文件夹中）
- 运行 Web 服务器的命令、脚本或可执行文件

下图显示了这些文件在文件系统上查找名为"order"的函数的方式。

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Configuration

应用程序通过*主机.json*文件进行配置。 此文件通过指向能够处理 HTTP 事件的 Web 服务器告诉函数主机发送请求的位置。

自定义处理程序是通过配置*host.json*文件来定义的，该文件包含有关如何通过`httpWorker`该部分运行 Web 服务器的详细信息。

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

该`httpWorker`节指向 由 定义的目标`defaultExecutablePath`。 执行目标可以是命令、可执行文件或实现 Web 服务器的文件。

对于脚本化应用，`defaultExecutablePath`指向脚本语言的运行时并`defaultWorkerPath`指向脚本文件位置。 下面的示例显示了 Node.js 中的 JavaScript 应用如何配置为自定义处理程序。

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

您还可以使用`arguments`数组传递参数：

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

许多调试设置都有必要使用参数。 有关详细信息，请参阅[调试](#debugging)部分。

> [!NOTE]
> *host.json*文件必须与正在运行的 Web 服务器处于目录结构中的同一级别。 默认情况下，某些语言和工具链可能不会将此文件放在应用程序根目录。

#### <a name="bindings-support"></a>绑定支持

标准触发器以及输入和输出绑定可通过引用*host.json*文件中[的扩展包](./functions-bindings-register.md)来获取。

### <a name="function-metadata"></a>函数元数据

当与自定义处理程序一起使用时，*函数.json*内容与在任何其他上下文中定义函数的方式没有什么不同。 唯一的要求是*函数.json*文件必须位于命名的文件夹中，以匹配函数名称。

### <a name="request-payload"></a>请求有效负载

纯 HTTP 函数的请求负载是原始 HTTP 请求负载。 纯 HTTP 函数定义为没有输入或输出绑定的函数，这些函数返回 HTTP 响应。

任何包含输入、输出绑定或通过 HTTP 以外的事件源触发的其他类型的函数都有自定义请求负载。

以下代码表示示例请求负载。 有效负载包括一个 JSON 结构，`Data`其中包含`Metadata`两个成员：和 。

成员`Data`包括匹配*函数.json*文件中绑定数组中定义的输入和触发器名称的键。

该`Metadata`成员包括[从事件源生成的元数据](./functions-bindings-expressions-patterns.md#trigger-metadata)。

给定以下*函数.json*文件中定义的绑定：

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

返回与此示例类似的请求负载：

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

按照惯例，函数响应格式化为键/值对。 支持的密钥包括：

| <nobr>有效负载键</nobr>   | 数据类型 | 备注                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | 保存`bindings`*函数.json*文件数组定义的响应值。<br /><br />例如，如果函数配置了名为"blob"的 Blob 存储输出绑定，则`Outputs`包含名为 的键`blob`，该键设置为 blob 的值。 |
| `Logs`        | array     | 消息将显示在函数调用日志中。<br /><br />在 Azure 中运行时，消息将显示在应用程序见解中。 |
| `ReturnValue` | 字符串    | 用于在将输出配置为`$return`*函数.json*文件中时提供响应。 |

有关[示例有效负载，请参阅示例](#bindings-implementation)。

## <a name="examples"></a>示例

自定义处理程序可以使用支持 HTTP 事件的任何语言实现。 虽然 Azure 函数[完全支持 JavaScript 和 Node.js，](./functions-reference-node.md)但以下示例演示如何在 Node.js 中使用 JavaScript 实现自定义处理程序以进行教学。

> [!TIP]
> 作为学习如何以其他语言实现自定义处理程序的指南，如果希望在不支持的 Node.js 版本中运行函数应用，则此处显示的基于 Node.js 的示例可能也很有用。

## <a name="http-only-function"></a>仅 HTTP 函数

下面的示例演示如何配置没有附加绑定或输出的 HTTP 触发函数。 在此示例中实现的方案具有一个名为 的`http`函数，该函数`GET`接受`POST`或 。

以下代码段表示如何组成对函数的请求。

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>实现

在名为*http*的文件夹中，*函数.json*文件配置 HTTP 触发的功能。

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

该函数配置为接受 和`GET``POST`请求，结果值通过名为 的`res`参数提供。

在应用的根目录 *，host.json*文件配置为运行 Node.js 并指向`server.js`该文件。

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

文件*服务器.js*文件实现 Web 服务器和 HTTP 功能。

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

在此示例中，Express 用于创建 Web 服务器来处理 HTTP 事件，并设置为通过 侦听请求`FUNCTIONS_HTTPWORKER_PORT`。

函数在 的路径上定义`/hello`。 `GET`请求通过返回一个简单的 JSON 对象进行处理`POST`，并且请求可以通过`req.body`访问请求正文。

此处的 order 函数的路由`/hello`是，`/api/hello`而不是因为函数主机将请求代理到自定义处理程序。

>[!NOTE]
>`FUNCTIONS_HTTPWORKER_PORT`不是用于调用函数的面向公共端口。 函数主机使用此端口调用自定义处理程序。

## <a name="function-with-bindings"></a>带绑定的功能

在此示例中实现的方案具有一个名为 的`order`函数，该函数接受`POST`具有表示产品订单的有效负载的 函数。 当订单过帐到函数时，将创建队列存储消息并返回 HTTP 响应。

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

在名为*顺序*的文件夹中，*函数.json*文件配置 HTTP 触发的功能。

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

此功能定义为[HTTP 触发函数](./functions-bindings-http-webhook-trigger.md)，该函数返回[HTTP 响应](./functions-bindings-http-webhook-output.md)并输出[队列存储](./functions-bindings-storage-queue-output.md)消息。

在应用的根目录 *，host.json*文件配置为运行 Node.js 并指向`server.js`该文件。

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

文件*服务器.js*文件实现 Web 服务器和 HTTP 功能。

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

在此示例中，Express 用于创建 Web 服务器来处理 HTTP 事件，并设置为通过 侦听请求`FUNCTIONS_HTTPWORKER_PORT`。

函数在 的路径上定义`/order`。  此处的 order 函数的路由`/order`是，`/api/order`而不是因为函数主机将请求代理到自定义处理程序。

当`POST`请求发送到此函数时，数据将通过几个点公开：

- 请求正文可通过`req.body`
- 发布到函数的数据可通过`req.body.Data.req.Body`

函数的响应格式化为键/值对，`Outputs`其中成员持有 JSON 值，其中键与*函数.json*文件中定义的输出匹配。

通过设置`message`等于来自请求的消息和`res`预期的 HTTP 响应，此函数将消息输出到队列存储并返回 HTTP 响应。

## <a name="debugging"></a>调试

要调试函数自定义处理程序应用，您需要添加适合语言和运行时的参数以启用调试。

例如，要调试 Node.js 应用程序，标志`--inspect`将作为*host.json*文件中的参数传递。

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
> 调试配置是*host.json*文件的一部分，这意味着您可能需要在部署到生产之前删除某些参数。

使用此配置，可以使用以下命令启动函数的主机进程：

```bash
func host start
```

启动该过程后，可以附加调试器并命中断点。

### <a name="visual-studio-code"></a>Visual Studio Code

下面的示例配置演示如何设置*启动.json*文件，将应用连接到 Visual Studio 代码调试器。

此示例适用于 Node.js，因此您可能需要更改其他语言或运行时的此示例。

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

自定义处理程序可以部署到几乎每个 Azure 函数托管选项（请参阅[限制](#restrictions)）。 如果处理程序需要自定义依赖项（如语言运行时），则可能需要使用[自定义容器](./functions-create-function-linux-custom-image.md)。

## <a name="restrictions"></a>限制

- Linux 消费计划中不支持自定义处理程序。
- Web 服务器需要在 60 秒内启动。

## <a name="samples"></a>示例

有关如何在各种不同语言中实现函数的示例，请参阅[自定义处理程序示例 GitHub 存储库](https://github.com/Azure-Samples/functions-custom-handlers)。
