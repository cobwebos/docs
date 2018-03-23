---
title: Durable Functions 中的有状态单一实例 - Azure
description: 了解如何实现 Azure Functions 的 Durable Functions 扩展中的有状态单一实例。
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
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 46cdd8523117e1100e7ce2a29ade9eb2dc0afe75
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="stateful-singletons-in-durable-functions---counter-sample"></a>Durable Functions 中的有状态单一实例 - 计数器示例

> [!NOTE]
> 我们正在重写此示例。 已删除这些代码片段，包含新示例的新文章将替换它。

有状态单一实例是长时间运行（可能永久运行）的业务流程协调程序函数，可存储状态，也可由其他函数调用和查询。 在分布式计算中，有状态单一实例类似于[参与者模式](https://en.wikipedia.org/wiki/Actor_model)。

尽管不是完全的“参与者”实现，但业务流程协调程序函数仍具有许多相同的运行时特征。 例如，它们均具有以下特征：有状态、可靠、单线程、位置透明以及可全局寻址。 这些特征使真正的参与者实现变得特别有用，但无需单独的框架。

本文介绍如何运行计数器示例。 该示例演示一个单一实例对象，该对象支持递增和递减运算，并相应地更新其内部状态。

## <a name="prerequisites"></a>先决条件

* 按照[安装 Durable Functions](durable-functions-install.md) 中的说明设置示例。
* 本文假定用户已完成 [Hello Sequence](durable-functions-sequence.md) 示例演练。

## <a name="scenario-overview"></a>方案概述

使用常规无状态函数实现计时器方案是非常困难的。 面临的一项主要挑战是管理并发。 递增和递减等运算需要具有原子性，否则可能出现导致运算相互覆盖的争用条件。

一种方法是使用单个 VM 来承载计数器数据，但这样做成本太高，并且可能带来管理可靠性的难题，因为可能需要定期重启单个 VM。 或者，可以使用分布式平台通过同步工具（如 Blob 租用）来帮助管理并发，但这会大大地增加复杂性。

Durable Functions 使实现此类方案变得简单，因为业务流程实例与单个 VM 关联，且业务流程协调程序函数执行始终是单线程的。 不仅如此，它们还具有长时间运行、有状态、可响应外部事件等特点。 下面的代码示例演示如何以长时间运行的业务流程协调程序函数的形式实现此类计数器。

## <a name="the-sample-function"></a>示例函数

本文通过示例应用介绍 E3_Counter 函数。



## <a name="the-counter-orchestration"></a>计数器业务流程

以下部分介绍用于 Visual Studio Code 和 Azure 门户开发的代码。

### <a name="c-script"></a>C# 脚本

function.json 文件：

<!-- [!code-json[Main](~/samples-durable-functions/samples/csx/E3_Counter/function.json)] -->

run.csx 文件：

<!-- [!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Counter/run.csx)] -->

### <a name="precompiled-c"></a>预编译 C# 

以下各节介绍用于 Visual Studio 开发的代码。

下面的代码可实现业务流程协调程序函数：

<!-- [!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Counter.cs)] -->

### <a name="explanation-of-the-code"></a>代码说明

本质上，该业务流程协调程序函数执行以下操作：

1. 使用 [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) 侦听名为 operation 的外部事件。
2. 根据请求的运算递增或递减 `counterState` 本地变量。
3. 使用 [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) 方法重启业务流程协调程序，同时将 `counterState` 的最新值设为新的输入。
4. 继续永久运行，或在收到 end 消息前持续运行。

这是一个永久业务流程的示例 &mdash; 即可能永远都不会结束的业务流程。 该函数通过 [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) 方法响应收到的消息，并且任何非业务流程协调程序函数均可调用该函数。

此业务流程协调程序函数的唯一特征是实际上没有任何历史记录：每次处理事件后，`ContinueAsNew` 方法都会重置历史记录。 这是实现具有任意生存期的业务流程协调程序的首选方法。 使用 `while` 循环可能导致业务流程协调程序函数的历史记录的增长不受限制，从而导致内存使用量不必要地增高。

> [!NOTE]
> 除永久业务流程外，`ContinueAsNew` 方法还有其他用例。 有关详细信息，请参阅[永久业务流程](durable-functions-eternal-orchestrations.md)。

## <a name="run-the-sample"></a>运行示例

可以通过发送以下 HTTP POST 请求来启动业务流程。 为了允许 `counterState` 从零（`int` 的默认值）开始，此请求中没有任何内容。

```
POST http://{host}/orchestrators/E3_Counter
Content-Length: 0
```

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{
  "id":"bcf6fb5067b046fbb021b52ba7deae5a",
  "statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

E3_Counter 实例启动，然后立刻等待使用 `RaiseEventAsync` 或 202 响应中引用的 sendEventUrl HTTP POST webhook 向其发送一个事件。 有效的 `eventName` 值包括 incr、decr 和 end。

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Type: application/json
Content-Length: 6

"incr"
```

通过查看 Azure Functions 门户中的函数日志，可查看“incr”运算的结果。

```
2017-06-29T18:54:53.998 Function started (Id=34e34a61-38b3-4eac-b6e2-98b85e32eec8)
2017-06-29T18:54:53.998 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Function started (Id=b45d6c2f-39f3-42a2-b904-7761b2614232)
2017-06-29T18:58:01.458 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Received 'incr' operation.
2017-06-29T18:58:01.458 Function completed (Success, Id=b45d6c2f-39f3-42a2-b904-7761b2614232, Duration=8ms)
2017-06-29T18:58:11.518 Function started (Id=e1f38cb2-546a-404d-ab22-1ac8f81a93d9)
2017-06-29T18:58:11.518 Current counter state is 1. Waiting for next operation.
```

同样，如果查看业务流程协调程序状态，可看到已将 `input` 字段设为更新后的值 (1)。

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey} HTTP/1.1
```

```
HTTP/1.1 202 Accepted
Content-Length: 129
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":1,"output":null,"createdTime":"2017-06-29T18:58:01Z","lastUpdatedTime":"2017-06-29T18:58:11Z"}
```

可继续向此实例发送新运算并观察其相应更新的状态。 如果要终止进程，可发送 end 运算。

> [!WARNING]
> 编写代码时，如果在并行处理消息（例如外部事件或终止请求）时调用 `ContinueAsNew`，则会出现已知的争用条件。 有关这些争用条件的详细信息，请参阅 [GitHub 问题](https://github.com/Azure/azure-functions-durable-extension/issues/67)。

## <a name="next-steps"></a>后续步骤

此示例演示了如何在[有状态单一实例](durable-functions-singletons.md)中处理[外部事件](durable-functions-external-events.md)和实现[永久业务流程](durable-functions-eternal-orchestrations.md)。 下一个示例演示如何使用外部事件和[持久计时器](durable-functions-timers.md)处理人工交互。

> [!div class="nextstepaction"]
> [运行人工交互示例](durable-functions-phone-verification.md)
