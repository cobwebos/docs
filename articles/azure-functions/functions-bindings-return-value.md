---
title: 使用 Azure 函数的返回值
description: 了解如何管理 Azure 函数的返回值
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 4ccfe192eaea94cb9b199bd3c6f0bdacf1685519
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61437712"
---
# <a name="using-the-azure-function-return-value"></a>使用 Azure 函数返回值

本文解释了返回值在函数内如何工作。

在支持返回值的语言中，可以将函数[输出绑定](./functions-triggers-bindings.md#binding-direction)绑定到返回值：

* 在 C# 类库，请将输出绑定特性应用到方法返回值。
* 在其他语言中，请将 *function.json* 中的 `name` 属性设置为 `$return`。

如果有多个输出绑定，请只使用其中一个绑定的返回值。

在 C# 和 C# 脚本中，将数据发送到输出绑定的替代方法是使用 `out` 参数和[收集器对象](functions-reference-csharp.md#writing-multiple-output-values)。

请参阅演示如何使用返回值的特定于语言的示例：

* [C#](#c-example)
* [C# 脚本 (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

## <a name="c-example"></a>C# 示例

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

## <a name="c-script-example"></a>C# 脚本示例

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

## <a name="f-example"></a>F# 示例

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

## <a name="javascript-example"></a>JavaScript 示例

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

## <a name="python-example"></a>Python 示例

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

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [处理 Azure Functions 绑定错误](./functions-bindings-errors.md)
