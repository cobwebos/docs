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
ms.openlocfilehash: 555b4d95358978e84e14e8a2e8b3d1c9cb2efc18
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614604"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Durable Functions 类型和功能 (Azure Functions)

Durable Functions 是 [Azure Functions](../functions-overview.md) 的一个扩展。 可将 Azure Functions 用于函数执行的有状态业务流程。 持久函数应用是由不同 Azure 函数构成的解决方案。 函数可在持久函数业务流程中扮演不同的角色。 

Azure Functions 中目前有四种持久函数类型：活动、业务流程协调程序、实体和客户端。 此部分的其余内容详细介绍业务流程中涉及的函数类型。

## <a name="orchestrator-functions"></a>业务流程协调程序函数

业务流程协调程序函数描述操作的执行方式和操作的执行顺序。 业务流程协调程序函数描述代码（C# 或 JavaScript）中的业务流程，如 [Durable Functions 应用程序模式](durable-functions-overview.md#application-patterns)中所示。 业务流程可以包含许多不同类型的操作，包括[活动函数](#activity-functions)、[子业务流程](durable-functions-orchestrations.md#sub-orchestrations)、[等待外部事件](durable-functions-orchestrations.md#external-events)、[HTTP](durable-functions-http-features.md) 和[计时器](durable-functions-orchestrations.md#durable-timers)。 业务流程协调程序函数也可以与[实体函数](#entity-functions)交互。

> [!NOTE]
> 业务流程协调程序函数使用常规代码编写，但对于如何编写代码存在严格的要求。 具体说来，业务流程协调程序函数代码必须是*确定性的*。 如果没有遵循这些确定性要求，则可能导致业务流程协调程序函数无法正确运行。 若要详细了解这些要求以及如何应对它们，可参阅[代码约束](durable-functions-code-constraints.md)主题。

若要更详细地了解业务流程协调程序函数及其功能，请参阅[持久业务流程](durable-functions-orchestrations.md)一文。

## <a name="activity-functions"></a>活动函数

活动函数是持久函数业务流程中的基本工作单元。 活动函数是在过程中协调的函数和任务。 例如，可以创建一个业务流程协调程序函数来处理订单。 任务涉及到检查库存、向客户收费和创建发货单。 每个任务都是一个单独的活动函数。 这些活动函数可以顺序执行，也可以并行执行，或按二者的某种组合来执行。

与业务流程协调程序函数不同，活动函数对于可在其中执行的工作类型没有限制。 活动函数频繁用于进行网络调用，或运行 CPU 密集型操作。 活动函数还可以将数据返回到业务流程协调程序函数。 Durable Task Framework 可保证在业务流程执行期间每个调用的活动函数至少执行一次。

> [!NOTE]
> 由于活动函数只保证至少执行一次，因此建议你尽可能将活动函数逻辑设置为幂等。

使用[活动触发器](durable-functions-bindings.md#activity-trigger)定义活动函数。 .NET 函数接收作为参数的 `DurableActivityContext`。 还可以将触发器绑定到任何其他可进行 JSON 序列化的对象，将输入传递给函数。 在 JavaScript 中，可以通过 `<activity trigger binding name>`[ 对象`context.bindings`中的 ](../functions-reference-node.md#bindings) 属性访问输入。 活动函数只能将单个值传递给它们。 若要传递多个值，必须使用元组、数组或复杂类型。

> [!NOTE]
> 只能从业务流程协调程序函数触发活动函数。

## <a name="entity-functions"></a>实体函数

实体函数定义用于读取和更新较小状态片段的操作。 我们通常将此类有状态实体称为“持久实体”。 与业务流程协调程序函数类似，实体函数是具有特殊触发器类型“实体触发器”的函数。 它们也可以从客户端函数或业务流程协调程序函数调用。 与业务流程协调程序函数不同，实体函数没有任何特定的代码约束。 实体函数还会显式管理状态，而不是通过控制流隐式表示状态。

> [!NOTE]
> 实体函数和相关功能仅在 Durable Functions 2.0 及更高版本中可用。

有关实体函数的详细信息，请参阅[持久实体](durable-functions-entities.md)一文。

## <a name="client-functions"></a>客户端函数

业务流程协调程序函数通过[业务流程触发器绑定](durable-functions-bindings.md#orchestration-trigger)触发，实体函数通过[实体触发器绑定](durable-functions-bindings.md#entity-trigger)触发。 这两个触发器在生效时，会响应在[任务中心](durable-functions-task-hubs.md)排队的消息。 若要传递这些消息，主要方式是在某个客户端函数中使用[业务流程协调程序客户端绑定](durable-functions-bindings.md#orchestration-client)或[实体客户端绑定](durable-functions-bindings.md#entity-client)。 任何非业务流程协调程序函数都可以是客户端函数。 例如，可以从 HTTP 触发的函数、Azure 事件中心触发的函数等触发 orchestrator。*客户端函数*使用持久性客户端输出绑定的功能是什么。

> [!NOTE]
> 与其他函数类型不同，业务流程协调程序函数和实体函数不能在 Azure 门户中使用按钮直接触发。 若要在 Azure 门户中测试某个业务流程协调程序函数或实体函数，必须改为运行一个客户端函数，该函数会在实现过程中启动业务流程协调程序函数或实体函数。 若要获取最简单的测试体验，建议使用手动触发器函数。

除了触发业务流程协调程序函数或实体函数，还可以使用持久客户端绑定与正在运行的业务流程和实体进行交互。 例如，业务流程可以查询、可以终止，还可以引发事件。 若要详细了解如何管理业务流程和实体，请参阅[实例管理](durable-functions-instance-management.md)一文。

## <a name="next-steps"></a>后续步骤

若要开始体验，请在 [C#](durable-functions-create-first-csharp.md) 或 [JavaScript](quickstart-js-vscode.md) 中创建第一个持久函数。

> [!div class="nextstepaction"]
> [详细了解 Durable Functions 业务流程](durable-functions-orchestrations.md)