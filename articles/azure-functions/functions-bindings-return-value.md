---
title: Using return value from an Azure Function
description: Learn to manage return values for Azure Functions
author: craigshoemaker
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 30c9caf267482d7c3731d4848cfb26cc8120b1ac
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231073"
---
# <a name="using-the-azure-function-return-value"></a>Using the Azure Function return value

This article explains how return values work inside a function.

In languages that have a return value, you can bind a function [output binding](./functions-triggers-bindings.md#binding-direction) to the return value:

* 在 C# 类库，请将输出绑定特性应用到方法返回值。
* 在其他语言中，请将 *function.json* 中的 `name` 属性设置为 `$return`。

如果有多个输出绑定，请只使用其中一个绑定的返回值。

在 C# 和 C# 脚本中，将数据发送到输出绑定的替代方法是使用 `out` 参数和[收集器对象](functions-reference-csharp.md#writing-multiple-output-values)。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

以下 C# 代码使用输出绑定的返回值，后接异步示例：

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="c-scripttabcsharp-script"></a>[C# Script](#tab/csharp-script)

下面是 *function.json* 文件中的输出绑定：

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

下面是 C# 脚本代码，后接异步示例：

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="ftabfsharp"></a>[F#](#tab/fsharp)

下面是 *function.json* 文件中的输出绑定：

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

F# 代码如下所示：

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

下面是 *function.json* 文件中的输出绑定：

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

在 JavaScript 中，返回值位于 `context.done` 的第二个参数中：

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

下面是 *function.json* 文件中的输出绑定：

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
下面是 Python 代码：

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Handle Azure Functions binding errors](./functions-bindings-errors.md)
