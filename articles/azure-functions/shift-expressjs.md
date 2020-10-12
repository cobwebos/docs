---
title: 从 Express.js 转换到 Azure Functions
description: 了解如何将 Express.js 终结点重构为 Azure Functions。
author: craigshoemaker
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: cshoe
ms.openlocfilehash: 266df5371ff5f47526fa9d6567c62e31d51ebb05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87810218"
---
# <a name="shifting-from-expressjs-to-azure-functions"></a>从 Express.js 转换到 Azure Functions

Express.js 是 Web 开发人员最欢迎的 Node.js 框架之一，并且仍是构建提供 API 终结点的应用的绝佳选择。

将代码迁移到无服务器体系结构时，重构 Express.js 终结点会影响以下方面：

- **中间件**：Express.js 具有一系列强大的中间件。 鉴于 Azure Functions 和 [Azure API 管理](../api-management/api-management-key-concepts.md)功能，许多中间件模块不再需要用到。 确保迁移终结点之前可以复制或替换必要中间件处理的任何逻辑。

- **不同的 API**：Azure Functions 和 Express.js 中用于处理请求和响应的 API 有所不同。 下面的示例详细介绍必需的更改。

- **默认路由：** 默认情况下，Azure Functions 终结点公开在 `api` 路由下。 路由规则可通过 [host.json 文件中的 `routePrefix`](./functions-bindings-http-webhook-output.md#hostjson-settings) 进行配置。

- **配置和约定**：Functions 应用使用 function.json 文件来定义 HTTP 谓词和安全策略，并可以配置函数的[输入和输出](./functions-triggers-bindings.md)。 默认情况下，包含函数文件的文件夹名称定义终结点名称，但可以通过 [function.json](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) 文件中的 `route` 属性更改该名称。

> [!TIP]
> 有关详细信息，请参阅交互式教程[使用 Azure Functions 将 Node.js 和 Express API 重构为无服务器 API](/learn/modules/shift-nodejs-express-apis-serverless/)。

## <a name="example"></a>示例

### <a name="expressjs"></a>Express.js

下面的示例演示了典型的 Express.js `GET` 终结点。

```javascript
// server.js
app.get('/hello', (req, res) => {
  try {
    res.send("Success!");
  } catch(error) {
    const err = JSON.stringify(error);
    res.status(500).send(`Request error. ${err}`);
  }
});
```

向 `/hello` 发送 `GET` 请求时，将返回包含 `Success` 的 `HTTP 200` 响应。 如果终结点遇到错误，响应返回包含错误详细信息的 `HTTP 500`。

### <a name="azure-functions"></a>Azure Functions

Azure Functions 将配置和代码文件组织到每个函数的单个文件夹中。 默认情况下，该文件夹的名称指示函数名称。

例如，名为 `hello` 的函数具有包含以下文件的文件夹。

``` files
| - hello
|  - function.json
|  - index.js
```

以下示例实现与上述 Express.js 终结点相同的结果，但这是通过 Azure Functions 完成的。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// hello/index.js
module.exports = async function (context, req) {
  try {
    context.res = { body: "Success!" };
  } catch(error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`
    };
  }
};
```

# <a name="typescript"></a>[TypeScript](#tab/typescript)

```typescript
// hello/index.ts
import { AzureFunction, Context, HttpRequest } from "@azure/functions";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
  try {
    context.res = { body: "Success!" };
  } catch (error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`,
    };
  }
};

export default httpTrigger;
```

---

移动到 Functions 时，将进行以下更改：

- **模块：** 以 JavaScript 模块的形式实现函数代码。

- **上下文和响应对象**：[`context`](./functions-reference-node.md#context-object) 让你可以与函数的运行时进行通信。 在上下文中，可以读取请求数据并设置函数的响应。 同步代码需要调用 `context.done()` 来完成执行，而 `asyc` 函数隐式解析请求。

- **命名约定**：默认情况下，用于包含 Azure Functions 文件的文件夹名称用作终结点名称（可以在 [function.json](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) 中进行重写）。

- **配置**：在 [function.json](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) 文件中定义 HTTP 谓词，如 `POST` 或 `PUT`。

以下 function.json 文件保存函数的配置信息。

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

通过在 `methods` 数组中定义 `get`，该函数可用于 HTTP `GET` 请求。 如果希望 API 接受支持 `POST` 请求，还可以将 `post` 添加到数组中。

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅交互式教程[使用 Azure Functions 将 Node.js 和 Express API 重构为无服务器 API](/learn/modules/shift-nodejs-express-apis-serverless/)