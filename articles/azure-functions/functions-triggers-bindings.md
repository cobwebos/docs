---
title: Azure Functions 中的触发器和绑定
description: Learn to use triggers and bindings to connect your Azure Function to online events and cloud-based services.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226566"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions 触发器和绑定概念

In this article you learn the high-level concepts surrounding functions triggers and bindings.

Triggers are what cause a function to run. A trigger defines how a function is invoked and a function must have exactly one trigger. Triggers have associated data, which is often provided as the payload of the function. 

Binding to a function is a way of declaratively connecting another resource to the function; bindings may be connected as *input bindings*, *output bindings*, or both. 绑定中的数据作为参数提供给函数。

You can mix and match different bindings to suit your needs. Bindings are optional and a function might have one or multiple input and/or output bindings.

Triggers and bindings let you avoid hardcoding access to other services. 函数接收函数参数中的数据（例如，队列消息内容）。 使用函数的返回值发送数据（例如，用于创建队列消息）。 

Consider the following examples of how you could implement different functions.

| 示例方案 | 触发器 | Input binding | Output binding |
|-------------|---------|---------------|----------------|
| A new queue message arrives which runs a function to write to another queue. | Queue<sup>*</sup> | 无 | Queue<sup>*</sup> |
|A scheduled job reads Blob Storage contents and creates a new Cosmos DB document. | 计时器 | Blob 存储 | Azure Cosmos DB |
|The Event Grid is used to read an image from Blob Storage and a document from Cosmos DB to send an email. | 事件网格 | Blob Storage and  Cosmos DB | SendGrid |
| A webhook that uses Microsoft Graph to update an Excel sheet. | HTTP | 无 | Microsoft Graph |

<sup>\*</sup> Represents different queues

These examples are not meant to be exhaustive, but are provided to illustrate how you can use triggers and bindings together.

###  <a name="trigger-and-binding-definitions"></a>Trigger and binding definitions

Triggers and bindings are defined differently depending on the development approach.

| 平台 | Triggers and bindings are configured by... |
|-------------|--------------------------------------------|
| C# 类库 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;decorating methods and parameters with C# attributes |
| All others (including Azure portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;updating [function.json](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

The portal provides a UI for this configuration, but you can edit the file directly by opening the **Advanced editor** available via the **Integrate** tab of your function.

In .NET, the parameter type defines the data type for input data. For instance, use `string` to bind to the text of a queue trigger, a byte array to read as binary and a custom type to de-serialize to an object.

对于动态键入的语言（如 JavaScript），请在 function.json文件中使用 `dataType` 属性。 例如，若要以二进制格式读取 HTTP 请求的内容，将 `dataType` 设置为 `binary`：

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

`dataType` 的其他选项是 `stream` 和 `string`。

## <a name="binding-direction"></a>绑定方向

所有触发器和绑定在 [function.json](./functions-reference.md) 文件中都有一个 `direction` 属性：

- 对于触发器，方向始终为 `in`
- 输入和输出绑定使用 `in` 和 `out`
- 某些绑定支持特殊方向 `inout`。 If you use `inout`, only the **Advanced editor** is available via the **Integrate** tab in the portal.

使用[类库中的特性](functions-dotnet-class-library.md)来配置触发器和绑定时，方向在特性构造函数中提供或推断自参数类型。

## <a name="supported-bindings"></a>支持的绑定

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

有关哪些绑定处于预览状态或已批准在生产环境中使用的信息，请参阅[支持的语言](supported-languages.md)。

## <a name="resources"></a>资源
- [Binding expressions and patterns](./functions-bindings-expressions-patterns.md)
- [Using the Azure Function return value](./functions-bindings-return-value.md)
- [How to register a binding expression](./functions-bindings-register.md)
- 测试：
  - [在 Azure Functions 中测试代码的策略](functions-test-a-function.md)
  - [手动运行非 HTTP 触发的函数](functions-manually-run-non-http.md)
- [Handling binding errors](./functions-bindings-errors.md)

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [Register Azure Functions binding extensions](./functions-bindings-register.md)
