---
title: Durable Functions 中的函数链 - Azure
description: 了解如何运行执行一系列函数的 Durable Functions 示例。
author: cgillum
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: e8c314b6288bc26ad48fd210e866b2b67e433e17
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231329"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Durable Functions 中的函数链 - Hello 序列示例

函数链是指以特定顺序执行一系列函数的模式。 通常需要将一个函数的输出应用于另一函数的输入。 本文介绍在完成 Durable Functions 快速入门（[C#](durable-functions-create-first-csharp.md) 或 [JavaScript](quickstart-js-vscode.md)）时创建的链接序列。 有关 Durable Functions 的详细信息，请参阅 [Durable Functions 概述](durable-functions-overview.md)。

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>函数

本文介绍示例应用中的以下函数：

* `E1_HelloSequence`：在一个序列中多次调用 `E1_SayHello` 的 orchestrator 函数。 它存储来自 `E1_SayHello` 调用的输出并记录结果。
* `E1_SayHello`：在字符串前添加“Hello”的活动函数。

以下部分介绍用于C#脚本编写和 JavaScript 的配置和代码。 本文末尾显示了用于 Visual Studio 开发的代码。

> [!NOTE]
> JavaScript Durable Functions 仅适用于2.0 运行时函数。

## <a name="e1_hellosequence"></a>E1_HelloSequence

### <a name="functionjson-file"></a>function.json 文件

如果使用 Visual Studio Code 或 Azure 门户进行开发，则此处为用于业务流程协调程序函数的 function.json 文件的内容。 大多数 orchestrator function.json 文件的内容都与以下内容相似。

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

`orchestrationTrigger` 绑定类型非常重要。 所有 orchestrator 函数都必须使用此触发器类型。

> [!WARNING]
> 为遵守 orchestrator 函数的“无 I/O”规则，在使用 `orchestrationTrigger` 触发器绑定时不要使用任何输入或输出绑定。  如果需要其他输入或输出绑定，则应改为在业务流程协调程序调用的 `activityTrigger` 函数的上下文中使用。 有关详细信息，请参阅业务流程[协调程序函数代码约束](durable-functions-code-constraints.md)一文。

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C# 脚本（Visual Studio Code 和 Azure 门户的示例代码）

下面是源代码：

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

所有 C# orchestration 函数都必须具有 `DurableOrchestrationContext` 类型的参数，此参数存在于 `Microsoft.Azure.WebJobs.Extensions.DurableTask` 程序集中。 如果使用 C# 脚本，则可以使用 `#r` 表示法引用程序集。 此上下文对象允许调用其他*活动*函数并使用其 `CallActivityAsync` 方法传递输入参数。

代码将在具有不同参数值的序列中调用三次 `E1_SayHello`。 每个调用的返回值都会添加到 `outputs` 列表，函数末尾会返回该列表。

### <a name="javascript"></a>Javascript

下面是源代码：

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

所有 JavaScript 业务流程函数都必须包括 [`durable-functions` 模块](https://www.npmjs.com/package/durable-functions)。 它是一个库，可用于在 JavaScript 中编写 Durable Functions。 业务流程函数与其他 JavaScript 函数之间有三个明显差异：

1. 此函数是一个[生成器函数](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)。
2. 此函数包装在对 `durable-functions` 模块的 `orchestrator` 方法的调用（此处为 `df`）中。
3. 此函数必须是同步的。 因为“orchestrator”方法处理“context.done”的调用，所以此函数应该只是“return”。

`context` 对象包含一个 `df` 对象，可使用其  *方法调用其他活动*`callActivity`函数并传递输入参数。 该代码按顺序采用不同的参数值三次调用 `E1_SayHello`，使用 `yield` 指示执行应当等待异步活动函数调用返回。 每个调用的返回值都会添加到 `outputs` 列表，函数末尾会返回该列表。

## <a name="e1_sayhello"></a>E1_SayHello

### <a name="functionjson-file"></a>function.json 文件

活动函数  *的 function.json*`E1_SayHello` 文件类似于 `E1_HelloSequence` 的 function.json 文件，只不过前者使用 `activityTrigger` 绑定类型而非 `orchestrationTrigger` 绑定类型。

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Orchestration 函数调用的任何函数都必须使用 `activityTrigger` 绑定。

`E1_SayHello` 的实现是一种相对简单的字符串格式设置操作。

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

此函数有一个 `DurableActivityContext`类型的参数，该参数用于获取由 orchestrator 函数对 `CallActivityAsync<T>`的调用传递给它的输入。

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

与 JavaScript 业务流程函数不同，活动函数不需要特殊设置。 业务流程协调程序函数传递给它的输入位于 `context.bindings` 对象上，在 `activityTrigger` 绑定的名称下，在本例中为 `context.bindings.name`。 绑定名称可以设置为导出函数的参数并且可以直接访问，这是示例代码所做的事情。

## <a name="run-the-sample"></a>运行示例

若要执行 `E1_HelloSequence` 业务流程，请发送以下 HTTP POST 请求。

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> 前面的 HTTP 代码片段假定 `host.json` 文件中有一个条目，该条目从所有 HTTP 触发器函数 URL 中删除默认的 `api/` 前缀。 可以在示例的 `host.json` 文件中找到此配置的标记。

例如，如果在名为“myfunctionapp”的函数应用中运行示例，请将 "{host}" 替换为“myfunctionapp.azurewebsites.net”。

结果为 HTTP 202 响应，如下所示（已简化）：

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

此时，业务流程已进行排队，并将立即开始运行。 `Location` 标头中的 URL可用于检查执行的状态。

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

结果为业务流程的状态。 它运行和完成的速度很快，因此处于已完成状态，并伴有如下所示响应（已简化）：

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

可以看到，实例的 `runtimeStatus` 为已完成，且 `output` 包含 orchestrator 函数执行的 JSON 序列化结果。

> [!NOTE]
> 启动 orchestrator 函数的 HTTP POST 终结点在示例应用中作为名为“HttpStart”的 HTTP 触发器函数实现。 可对其他触发器类型（如 `queueTrigger`、`eventHubTrigger` 或 `timerTrigger`）实施类似的启动器逻辑。

查看函数执行日志。 由于`E1_HelloSequence`业务流程可靠性[主题中所述的重播行为，](durable-functions-orchestrations.md#reliability) 函数已多次启动和完成。 另一方面，由于未重播这些函数执行，因此只执行三次 `E1_SayHello`。

## <a name="visual-studio-sample-code"></a>Visual Studio 示例代码

下面是 Visual Studio 项目中以单个 C# 文件形式提供的业务流程：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>后续步骤

此示例演示了简单的函数链业务流程。 下一示例演示如何实现扇出/扇入模式。

> [!div class="nextstepaction"]
> [运行扇出/扇入示例](durable-functions-cloud-backup.md)
