---
title: Durable Functions 中的函数链 - Azure
description: 了解如何运行执行一系列函数的 Durable Functions 示例。
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: 8da4ce7801cc98f9ffb32eb7b506eaf1ccd877dd
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562049"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Durable Functions 中的函数链 - Hello 序列示例

函数链是指以特定顺序执行一系列函数的模式。 通常需要将一个函数的输出应用于另一函数的输入。 本文介绍在完成 Durable Functions 快速入门（[C#](durable-functions-create-first-csharp.md) 或 [JavaScript](quickstart-js-vscode.md)）时创建的链接序列。 有关 Durable Functions 的详细信息，请参阅 [Durable Functions 概述](durable-functions-overview.md)。

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>函数

本文介绍示例应用中的以下函数：

* `E1_HelloSequence`：在一个序列中多次调用 `E1_SayHello` 的业务流程[协调程序函数](durable-functions-bindings.md#orchestration-trigger)。 它存储来自 `E1_SayHello` 调用的输出并记录结果。
* `E1_SayHello`：将字符串前面加上 "Hello" 的[活动函数](durable-functions-bindings.md#activity-trigger)。
* `HttpStart`：启动业务流程协调程序实例的 HTTP 触发函数。

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence orchestrator 函数

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

所有 C# orchestration 函数都必须具有 `DurableOrchestrationContext` 类型的参数，此参数存在于 `Microsoft.Azure.WebJobs.Extensions.DurableTask` 程序集中。 此上下文对象允许调用其他*活动*函数并使用其 `CallActivityAsync` 方法传递输入参数。

代码将在具有不同参数值的序列中调用三次 `E1_SayHello`。 每个调用的返回值都会添加到 `outputs` 列表，函数末尾会返回该列表。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> JavaScript Durable Functions 仅适用于2.0 运行时函数。

#### <a name="functionjson"></a>function.json

如果使用 Visual Studio Code 或 Azure 门户进行开发，则此处为用于业务流程协调程序函数的 function.json 文件的内容。 大多数 orchestrator function.json 文件的内容都与以下内容相似。

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

`orchestrationTrigger` 绑定类型非常重要。 所有 orchestrator 函数都必须使用此触发器类型。

> [!WARNING]
> 为遵守 orchestrator 函数的“无 I/O”规则，在使用 `orchestrationTrigger` 触发器绑定时不要使用任何输入或输出绑定。  如果需要其他输入或输出绑定，则应改为在业务流程协调程序调用的 `activityTrigger` 函数的上下文中使用。 有关详细信息，请参阅业务流程[协调程序函数代码约束](durable-functions-code-constraints.md)一文。

#### <a name="indexjs"></a>index.js

下面是函数：

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

所有 JavaScript 业务流程函数都必须包括 [`durable-functions` 模块](https://www.npmjs.com/package/durable-functions)。 它是一个库，可用于在 JavaScript 中编写 Durable Functions。 业务流程函数与其他 JavaScript 函数之间有三个明显差异：

1. 函数是一个[生成器函数。](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. 此函数包装在对 `durable-functions` 模块的 `orchestrator` 方法的调用（此处为 `df`）中。
3. 此函数必须是同步的。 因为“orchestrator”方法处理“context.done”的调用，所以此函数应该只是“return”。

`context` 对象包含一个 `df` 持久业务流程上下文对象，使你能够调用其他*活动*函数并使用其 `callActivity` 方法传递输入参数。 该代码按顺序采用不同的参数值三次调用 `E1_SayHello`，使用 `yield` 指示执行应当等待异步活动函数调用返回。 每个调用的返回值将添加到 `outputs` 数组中，该数组在函数的末尾返回。

---

### <a name="e1_sayhello-activity-function"></a>E1_SayHello 活动函数

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

活动使用 `ActivityTrigger` 特性。 使用提供的 `IDurableActivityContext` 执行活动相关操作，如使用 `GetInput<T>`访问输入值。

`E1_SayHello` 的实现是一种相对简单的字符串格式设置操作。

您可以直接绑定到传递到活动函数的类型，而不是绑定到 `IDurableActivityContext`。 例如：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.json

活动函数  *的 function.json*`E1_SayHello` 文件类似于 `E1_HelloSequence` 的 function.json 文件，只不过前者使用 `activityTrigger` 绑定类型而非 `orchestrationTrigger` 绑定类型。

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Orchestration 函数调用的任何函数都必须使用 `activityTrigger` 绑定。

`E1_SayHello` 的实现是一种相对简单的字符串格式设置操作。

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

与 JavaScript 业务流程函数不同，活动函数不需要特殊设置。 业务流程协调程序函数传递给它的输入位于 `context.bindings` 对象上，在 `activityTrigger` 绑定的名称下，在本例中为 `context.bindings.name`。 绑定名称可以设置为导出函数的参数并且可以直接访问，这是示例代码所做的事情。

---

### <a name="httpstart-client-function"></a>HttpStart 客户端函数

您可以使用客户端函数启动 orchestrator 函数的实例。 将使用 `HttpStart` HTTP 触发的函数启动 `E1_HelloSequence`的实例。

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

若要与协调器交互，函数必须包含 `DurableClient` 输入绑定。 您可以使用客户端来启动业务流程。 它还可帮助你返回包含用于检查新业务流程状态的 Url 的 HTTP 响应。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/函数 json

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

若要与协调器交互，函数必须包含 `durableClient` 输入绑定。

#### <a name="httpstartindexjs"></a>HttpStart/index

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

使用 `df.getClient` 获取 `DurableOrchestrationClient` 对象。 您可以使用客户端来启动业务流程。 它还可帮助你返回包含用于检查新业务流程状态的 Url 的 HTTP 响应。

---

## <a name="run-the-sample"></a>运行示例

若要执行 `E1_HelloSequence` 的业务流程，请向 `HttpStart` 函数发送以下 HTTP POST 请求。

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
> 可对其他触发器类型（如 `queueTrigger`、`eventHubTrigger` 或 `timerTrigger`）实施类似的启动器逻辑。

查看函数执行日志。 由于[业务流程可靠性](durable-functions-orchestrations.md#reliability)主题中所述的重播行为，`E1_HelloSequence` 函数已启动并完成了多次。 另一方面，由于未重播这些函数执行，因此只执行三次 `E1_SayHello`。

## <a name="next-steps"></a>后续步骤

此示例演示了简单的函数链业务流程。 下一示例演示如何实现扇出/扇入模式。

> [!div class="nextstepaction"]
> [运行扇出/扇入示例](durable-functions-cloud-backup.md)
