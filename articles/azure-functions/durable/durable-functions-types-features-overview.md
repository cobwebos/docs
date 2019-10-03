---
title: Azure Functions 的 Durable Functions 扩展中的函数类型
description: 了解 Azure Functions 的 Durable Functions 业务流程中用于支持函数间通信的函数类型和角色。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 7b395bd6024beb52b9263ac4fe655b5328a8e662
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933161"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Durable Functions 类型和功能 (Azure Functions)

Durable Functions 是 [Azure Functions](../functions-overview.md) 的一个扩展。 可将 Azure Functions 用于函数执行的有状态业务流程。 持久函数应用是一种解决方案，由不同的 Azure 功能组成。 函数可在持久函数业务流程中扮演不同的角色。 

目前 Azure Functions 中有四种持久函数类型：活动、orchestrator、实体和客户端。 本部分的其余部分将详细介绍业务流程中涉及的函数类型。

## <a name="orchestrator-functions"></a>业务流程协调程序函数

业务流程协调程序函数描述操作的执行方式和操作的执行顺序。 业务流程协调程序函数在代码（C#或 JavaScript）中描述了业务流程，如[Durable Functions 应用程序模式](durable-functions-overview.md#application-patterns)中所示。 业务流程可以有许多不同类型的操作，包括[活动函数](#activity-functions)、[子业务流程](durable-functions-orchestrations.md#sub-orchestrations)、[等待外部事件](durable-functions-orchestrations.md#external-events)、 [HTTP](durable-functions-orchestrations.md#calling-http-endpoints)和[计时器](durable-functions-orchestrations.md#durable-timers)。 业务流程协调程序函数也可以与[实体函数](#entity-functions)交互。

> [!NOTE]
> 业务流程协调程序函数是使用普通代码编写的，但对如何编写代码有严格的要求。 具体而言，orchestrator 函数代码必须是*确定性*的。 未能遵循这些确定性要求可能导致 orchestrator 功能无法正确运行。 有关这些要求以及如何解决这些要求的详细信息，请参阅[代码约束](durable-functions-code-constraints.md)主题。

有关 orchestrator 函数及其功能的更多详细信息，请参阅[持久业务流程](durable-functions-orchestrations.md)一文。

## <a name="activity-functions"></a>活动函数

活动函数是持久函数业务流程中的基本工作单元。 活动函数是在过程中协调的函数和任务。 例如，可以创建一个业务流程协调程序函数来处理订单。 任务涉及到检查库存、向客户收费和创建发货单。 每个任务都是一个单独的活动函数。 这些活动函数可以并行执行，也可以同时执行这两种函数。

与 orchestrator 函数不同，活动函数并不限于您可以在其中执行的工作类型。 活动函数经常用于进行网络调用或运行 CPU 密集型操作。 活动函数还可以将数据返回到业务流程协调程序函数。 持久性任务框架保证每个称为活动函数在业务流程执行期间*至少执行一次*。

> [!NOTE]
> 由于活动函数只保证*至少一次*执行，因此建议尽可能使活动函数逻辑*幂等*。

使用[活动触发器](durable-functions-bindings.md#activity-trigger)定义活动函数。 .NET 函数接收 [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) 作为参数。 你还可以将触发器绑定到任何其他 serializeable 对象，以便将输入传递到函数。 在 JavaScript 中，可以通过 [`context.bindings` 对象](../functions-reference-node.md#bindings)中的 `<activity trigger binding name>` 属性访问输入。 只能为活动函数传递单个值。 若要传递多个值，必须使用元组、数组或复杂类型。

> [!NOTE]
> 只能从业务流程协调程序函数触发活动函数。

## <a name="entity-functions"></a>实体函数

实体函数定义用于读取和更新小部分状态的操作。 我们经常将这些有状态实体作为*持久实体*来引用。 与业务流程协调程序函数类似，实体函数是具有特殊触发器类型“实体触发器”的函数。 还可以从客户端函数或 orchestrator 函数调用它们。 与业务流程协调程序函数不同，实体函数没有任何特定的代码约束。 实体函数还会显式管理状态，而不是通过控制流隐式表示状态。

> [!NOTE]
> 实体函数和相关功能仅在 Durable Functions 2.0 及更高版本中可用。 实体函数当前为公共预览版。

有关实体函数的详细信息，请参阅[持久性实体](durable-functions-entities.md)一文。

## <a name="client-functions"></a>客户端函数

业务流程协调程序函数由[业务流程触发器绑定](durable-functions-bindings.md#orchestration-trigger)触发，实体函数由[实体触发器绑定](durable-functions-bindings.md#entity-trigger)触发。 这两个触发器都通过对排队到[任务中心](durable-functions-task-hubs.md)的消息进行响应来运行。 传递这些消息的主要方法是通过使用业务流程*中的业务*流程[协调程序客户端绑定](durable-functions-bindings.md#orchestration-client)或[实体客户端绑定](durable-functions-bindings.md#entity-client)。 任何非业务流程协调程序函数都可以是*客户端函数*。 例如，可以从 HTTP 触发的函数、Azure 事件中心触发的函数等触发 orchestrator。*客户端函数*使用持久性客户端输出绑定的功能是什么。

> [!NOTE]
> 与其他函数类型不同，orchestrator 和 entity 函数不能使用 Azure 门户中的按钮直接触发。 如果要在 Azure 门户中测试 orchestrator 或 entity 函数，则必须改为运行一个*客户端函数*，该函数启动 orchestrator 或 entity 函数作为其实现的一部分。 为实现最简单的测试体验，建议使用*手动触发器*函数。

除了触发 orchestrator 或 entity 函数外，还可以使用*持久客户端*绑定与正在运行的业务流程和实体进行交互。 例如，可以对业务流程进行查询、终止，并可以将事件引发到它们。 有关管理业务流程和实体的详细信息，请参阅[实例管理](durable-functions-instance-management.md)一文。

## <a name="next-steps"></a>后续步骤

若要开始体验，请在 [C#](durable-functions-create-first-csharp.md) 或 [JavaScript](quickstart-js-vscode.md) 中创建第一个持久函数。

> [!div class="nextstepaction"]
> [阅读有关 Durable Functions 业务流程的详细信息](durable-functions-orchestrations.md)