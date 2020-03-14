---
title: Azure Functions 中的触发器和绑定
description: 了解如何使用触发器和绑定将 Azure 函数连接到联机事件和基于云的服务。
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276499"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions 触发器和绑定概念

本文介绍函数触发器和绑定的高级概念。

触发器是导致函数运行的原因。 触发器定义函数的调用方式，并且函数必须恰好有一个触发器。 触发器具有相关联的数据，这通常作为函数的负载提供。 

绑定到函数是以声明方式将其他资源连接到函数的方法;绑定可以连接为*输入绑定*、*输出绑定*或两者。 绑定中的数据作为参数提供给函数。

你可以混合和匹配不同的绑定以满足你的需求。 绑定是可选的，并且函数可能有一个或多个输入和/或输出绑定。

触发器和绑定使你可以避免硬编码访问其他服务。 函数接收函数参数中的数据（例如，队列消息内容）。 使用函数的返回值发送数据（例如，用于创建队列消息）。 

请考虑以下有关如何实现不同函数的示例。

| 示例方案 | 触发器 | 输入绑定 | 输出绑定 |
|-------------|---------|---------------|----------------|
| 一个新的队列消息到达，它运行一个函数以写入另一个队列。 | 队列<sup>*</sup> | 无 | 队列<sup>*</sup> |
|计划作业读取 Blob 存储内容并创建新的 Cosmos DB 文档。 | Timer | Blob 存储 | Cosmos DB |
|事件网格用于从 Blob 存储中读取映像，并使用 Cosmos DB 发送电子邮件。 | 事件网格 | Blob 存储和 Cosmos DB | SendGrid |
| 使用 Microsoft Graph 更新 Excel 工作表的 webhook。 | HTTP | 无 | Microsoft Graph |

<sup>\*</sup>表示不同的队列

这些示例并未详尽，只是为了说明如何将触发器和绑定一起使用。

###  <a name="trigger-and-binding-definitions"></a>触发器和绑定定义

根据开发方法，将以不同的方式定义触发器和绑定。

| 平台 | 触发器和绑定是通过 。 |
|-------------|--------------------------------------------|
| C# 类库 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;修饰方法和具有属性C#的参数 |
| 所有其他（包括 Azure 门户） | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;更新[函数 json](./functions-reference.md) （[架构](http://json.schemastore.org/function)） |

门户为此配置提供了一个 UI，但你可以通过在函数的 "**集成**" 选项卡中打开可用的 "**高级编辑器**" 来直接编辑该文件。

在 .NET 中，参数类型定义输入数据的数据类型。 例如，使用 `string` 绑定到队列触发器的文本，将字节数组读取为二进制，并使用自定义类型对对象进行反序列化。

对于动态键入的语言（如 JavaScript），请在 function.json `dataType`*文件中使用*  属性。 例如，若要以二进制格式读取 HTTP 请求的内容，将 `dataType` 设置为 `binary`：

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

所有触发器和绑定在 `direction`function.json[ 文件中都有一个 ](./functions-reference.md) 属性：

- 对于触发器，方向始终为 `in`
- 输入和输出绑定使用 `in` 和 `out`
- 某些绑定支持特殊方向 `inout`。 如果使用 `inout`，则只能通过门户中的 "**集成**" 选项卡使用 "**高级编辑器**"。

使用[类库中的特性](functions-dotnet-class-library.md)来配置触发器和绑定时，方向在特性构造函数中提供或推断自参数类型。

## <a name="supported-bindings"></a>支持的绑定

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

有关哪些绑定处于预览状态或已批准在生产环境中使用的信息，请参阅[支持的语言](supported-languages.md)。

## <a name="resources"></a>资源
- [绑定表达式和模式](./functions-bindings-expressions-patterns.md)
- [使用 Azure 函数返回值](./functions-bindings-return-value.md)
- [如何注册绑定表达式](./functions-bindings-register.md)
- 测试：
  - [在 Azure Functions 中测试代码的策略](functions-test-a-function.md)
  - [手动运行非 HTTP 触发的函数](functions-manually-run-non-http.md)
- [处理绑定错误](./functions-bindings-errors.md)

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [注册 Azure Functions 绑定扩展](./functions-bindings-register.md)
