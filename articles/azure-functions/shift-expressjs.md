---
title: 从 Express.js 切换到 Azure Functions
description: 了解如何重构 Express.js 终结点以 Azure Functions。
author: craigshoemaker
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: cshoe
ms.openlocfilehash: d035ef4bc90410cbf0899c038047dd5e6a001f10
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87462305"
---
# <a name="shifting-from-expressjs-to-azure-functions"></a>从 Express.js 切换到 Azure Functions

Express.js 是适用于 web 开发人员的最流行的 Node.js 框架之一，并且仍是构建提供 API 终结点的应用程序的绝佳选择。

将代码迁移到无服务器体系结构时，重构 Express.js 终结点会影响以下方面：

- **中间件**： Express.js 功能是一个强健的中间件集合。 许多中间件模块不再需要 Azure Functions 和[AZURE API 管理](../api-management/api-management-key-concepts.md)功能。 请确保在迁移终结点之前可以复制或替换由必要的中间件处理的任何逻辑。

- **不同 api**：用于处理请求和响应的 api 在 Azure Functions 和 Express.js 之间有所不同。 下面的示例详细介绍所需的更改。

- **默认路由**：默认情况下，Azure Functions 终结点在路由下公开 `api` 。 可以通过[ `routePrefix` 文件中的_host.js_ ](./functions-bindings-http-webhook-output.md#hostjson-settings)配置路由规则。

- **配置和约定**：函数应用使用文件_上的function.js_来定义 HTTP 谓词，定义安全策略，并可配置函数的[输入和输出](./functions-triggers-bindings.md)。 默认情况下，包含函数文件的文件夹名称定义了终结点名称，但你可以通过[function.json](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) file 中的 route 属性更改该名称。

> [!TIP]
> 有关详细信息，请参阅交互式教程[使用 Azure Functions 重构 Node.js 和 Express api 到无服务器 api](https://docs.microsoft.com/learn/modules/shift-nodejs-express-apis-serverless/)。

## <a name="example"></a>示例

### <a name="expressjs"></a>Express.js

下面的示例演示一个典型的 Express.js `GET` 终结点。

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

`GET`向发送请求时，将 `/hello` `HTTP 200` 返回包含的响应 `Success` 。 如果终结点遇到错误，响应就是 `HTTP 500` 包含错误详细信息的。

### <a name="azure-functions"></a>Azure Functions

Azure Functions 将配置和代码文件组织到每个函数的单个文件夹中。 默认情况下，该文件夹的名称将指示函数名称。

例如，名为的函数 `hello` 包含一个具有以下文件的文件夹。

``` files
| - hello
|  - function.json
|  - index.js
```

下面的示例实现与上述 Express.js 终结点相同的结果，但具有 Azure Functions。

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

移到函数时，将进行以下更改：

- **模块：** 函数代码实现为 JavaScript 模块。

- **Context 和 response 对象**： [`context`](./functions-reference-node.md#context-object) 允许您与函数的运行时通信。 在上下文中，可以读取请求数据并设置函数的响应。 同步代码需要调用 `context.done()` 来完成执行，而函数会 `asyc` 隐式解析请求。

- **命名约定**：默认情况下，用于包含 Azure Functions 文件的文件夹名称用作终结点的名称（可以在[function.js上](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint)进行重写）。

- **配置**：在[function.js上](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint)定义文件中的 HTTP 谓词，例如 `POST` 或 `PUT` 。

以下_function.js_文件保存了函数的配置信息。

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

通过 `get` 在数组中定义 `methods` ，该函数可用于 HTTP `GET` 请求。 如果希望 API 接受支持 `POST` 请求，还可以将添加 `post` 到该数组。

## <a name="next-steps"></a>后续步骤

- 了解更多有关交互式 Node.js 教程的详细信息，请参阅[Azure Functions](https://docs.microsoft.com/learn/modules/shift-nodejs-express-apis-serverless/)
