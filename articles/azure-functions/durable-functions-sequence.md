---
title: Durable Functions 中的函数链 - Azure
description: 了解如何运行执行一系列函数的 Durable Functions 示例。
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: e53b38bf336816ca670fad3ab70a43e5cc8b3437
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763554"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Durable Functions 中的函数链 - Hello 序列示例

函数链是指以特定顺序执行一系列函数的模式。 通常需要将一个函数的输出应用于另一函数的输入。 本文介绍使用 [Durable Functions](durable-functions-overview.md) 实现函数链的示例。

## <a name="prerequisites"></a>先决条件

* [安装 Durable Functions](durable-functions-install.md)。

## <a name="the-functions"></a>函数

本文介绍示例应用中的以下函数：

* `E1_HelloSequence`：在一个序列中多次调用 `E1_SayHello` 的 orchestrator 函数。 它存储来自 `E1_SayHello` 调用的输出并记录结果。
* `E1_SayHello`：在字符串前添加“Hello”的活动函数。

以下各部分介绍了用于 C# 脚本和 JavaScript 的配置和代码。 文章末尾展示了用于 Visual Studio 开发的代码。

> [!NOTE]
> 只有在 v2 Functions 运行时中，Durable Functions 在 JavaScript 中才可用。

## <a name="e1hellosequence"></a>E1_HelloSequence
### <a name="functionjson-file"></a>function.json 文件

如果使用 Visual Studio Code 或 Azure 门户进行开发，则此处为用于业务流程协调程序函数的 function.json 文件的内容。 大多数 orchestrator function.json 文件的内容都与以下内容相似。

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

`orchestrationTrigger` 绑定类型非常重要。 所有 orchestrator 函数都必须使用此触发器类型。

> [!WARNING]
> 为遵守 orchestrator 函数的“无 I/O”规则，在使用 `orchestrationTrigger` 触发器绑定时不要使用任何输入或输出绑定。  如果需要其他输入或输出绑定，则应改为在业务流程协调程序调用的 `activityTrigger` 函数的上下文中使用。

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C# 脚本（Visual Studio Code 和 Azure 门户的示例代码） 

下面是源代码：

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

所有 C# orchestration 函数都必须具有 `DurableOrchestrationContext` 类型的参数，此参数存在于 `Microsoft.Azure.WebJobs.Extensions.DurableTask` 程序集中。 如果使用 C# 脚本，则可以使用 `#r` 表示法引用程序集。 借助此上下文对象，可使用其 [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) 方法调用其他活动函数并传递输入参数。

代码将在具有不同参数值的序列中调用三次 `E1_SayHello`。 每个调用的返回值都会添加到 `outputs` 列表，函数末尾会返回该列表。

### <a name="javascript"></a>Javascript

下面是源代码：

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

所有 JavaScript 业务流程函数都必须包括 `durable-functions` 模块。 这是一个 JavaScript 库，它将业务流程函数的操作转换为进程外语言的 Durable Functions 执行协议。 业务流程函数与其他 JavaScript 函数之间有三个明显差异：

1. 此函数是一个[生成器函数](https://docs.microsoft.com/en-us/scripting/javascript/advanced/iterators-and-generators-javascript)。
2. 此函数包装在对 `durable-functions` 模块的调用（此处为 `df`）中。
3. 此函数通过调用 `return` 而非 `context.done` 结束。

`context` 对象包含一个 `df` 对象，可使用其 `callActivityAsync` 方法调用其他活动函数并传递输入参数。 该代码按顺序采用不同的参数值三次调用 `E1_SayHello`，使用 `yield` 指示执行应当等待异步活动函数调用返回。 每个调用的返回值都会添加到 `outputs` 列表，函数末尾会返回该列表。

## <a name="e1sayhello"></a>E1_SayHello
### <a name="functionjson-file"></a>function.json 文件

活动函数 `E1_SayHello` 的 function.json 文件类似于 `E1_HelloSequence` 的 function.json 文件，只不过前者使用 `activityTrigger` 绑定类型而非 `orchestrationTrigger` 绑定类型。

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Orchestration 函数调用的任何函数都必须使用 `activityTrigger` 绑定。

`E1_SayHello` 的实现是一种相对简单的字符串格式设置操作。

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

此函数具有 [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) 类型的参数，该参数可用于获取由 orchestrator 函数对 [`CallActivityAsync<T>`](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) 的调用传递给它的输入。

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

与 JavaScript 业务流程函数不同，JavaScript 活动函数不需要特殊设置。 业务流程协调程序函数传递给它的输入位于 `context.bindings` 对象上，在 `activitytrigger` 绑定的名称下，在本例中为 `context.bindings.name`。 绑定名称可以设置为导出函数的参数并且可以直接访问，这是示例代码所做的事情。

## <a name="run-the-sample"></a>运行示例

若要执行 `E1_HelloSequence` 业务流程，请发送以下 HTTP POST 请求。

```
POST http://{host}/orchestrators/E1_HelloSequence
```

例如，如果在名为“myfunctionapp”的函数应用中运行示例，请将 "{host}" 替换为“myfunctionapp.azurewebsites.net”。

结果为 HTTP 202 响应，如下所示（已简化）：

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

此时，业务流程已进行排队，并将立即开始运行。 `Location` 标头中的 URL可用于检查执行的状态。

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
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

查看函数执行日志。 由于[概述](durable-functions-overview.md)中所述的重播行为，`E1_HelloSequence` 函数启动并完成了多次。 另一方面，由于未重播这些函数执行，因此只执行三次 `E1_SayHello`。

## <a name="visual-studio-sample-code"></a>Visual Studio 代码示例

下面是 Visual Studio 项目中以单个 C# 文件形式提供的业务流程：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>后续步骤

此示例演示了简单的函数链业务流程。 下一示例演示如何实现扇出/扇入模式。 

> [!div class="nextstepaction"]
> [运行扇出/扇入示例](durable-functions-cloud-backup.md)
