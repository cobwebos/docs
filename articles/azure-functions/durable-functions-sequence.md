---
title: "Durable Functions 中的函数链 - Azure"
description: "了解如何运行执行一系列函数的 Durable Functions 示例。"
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 0973f83ae839597f3b499814a4a04a8a640a1fb6
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Durable Functions 中的函数链 - Hello 序列示例

函数链是指以特定顺序执行一系列函数的模式。 通常需要将一个函数的输出应用于另一函数的输入。 本文介绍使用 [Durable Functions](durable-functions-overview.md) 实现函数链的示例。

## <a name="prerequisites"></a>先决条件

* 按照[安装 Durable Functions](durable-functions-install.md) 中的说明设置示例。

## <a name="the-functions"></a>函数

本文介绍示例应用中的以下函数：

* `E1_HelloSequence`：在一个序列中多次调用 `E1_SayHello` 的 orchestrator 函数。 它存储来自 `E1_SayHello` 调用的输出并记录结果。
* `E1_SayHello`：在字符串前添加“Hello”的活动函数。

以下各节介绍用于 Azure 门户开发的配置和代码。 文章末尾展示了用于 Visual Studio 开发的代码。
 
## <a name="functionjson-file"></a>function.json 文件

如果使用 Azure 门户进行开发，则此处为用于 orchestrator 函数的 function.json 文件的内容。 大多数 orchestrator function.json 文件的内容都与以下内容相似。

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

`orchestrationTrigger` 绑定类型非常重要。 所有 orchestrator 函数都必须使用此触发器类型。

> [!WARNING]
> 为遵守 orchestrator 函数的“无 I/O”规则，在使用 `orchestrationTrigger` 触发器绑定时不要使用任何输入或输出绑定。  如果需要其他输入或输出绑定，则应改为在业务流程协调程序调用的 `activityTrigger` 函数的上下文中使用。

## <a name="c-script"></a>C# 脚本

下面是源代码：

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

所有 C# orchestration 函数都必须具有 `DurableOrchestrationContext` 类型的参数，此参数存在于 `Microsoft.Azure.WebJobs.Extensions.DurableTask` 程序集中。 如果使用 C# 脚本，则可以使用 `#r` 表示法引用程序集。 借助此上下文对象，可使用其 [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) 方法调用其他活动函数并传递输入参数。

代码将在具有不同参数值的序列中调用三次 `E1_SayHello`。 每个调用的返回值都会添加到 `outputs` 列表，函数末尾会返回该列表。

活动函数 `E1_SayHello` 的 function.json 文件类似于 `E1_HelloSequence` 的 function.json 文件，只不过前者使用 `activityTrigger` 绑定类型而非 `orchestrationTrigger` 绑定类型。

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Orchestration 函数调用的任何函数都必须使用 `activityTrigger` 绑定。

`E1_SayHello` 的实现是一种相对简单的字符串格式设置操作。

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

此函数具有 [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) 类型的参数，该参数可用于获取由 orchestrator 函数对 [`CallActivityAsync<T>`](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) 的调用传递给它的输入。

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
