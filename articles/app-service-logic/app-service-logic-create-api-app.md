---
title: "创建逻辑应用的 API"
description: "创建用于逻辑应用的自定义 API"
author: jeffhollan
manager: dwrede
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bd229179-7199-4aab-bae0-1baf072c7659
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: eb382850f8a64c18864e6717b62cee530cdf56d0


---
# <a name="creating-a-custom-api-to-use-with-logic-apps"></a>创建用于逻辑应用的自定义 API
如果想要扩展逻辑应用平台，有多种方法可以调用不是作为许多现成的连接器之一提供的 API 或系统。  其中一种方法是从逻辑应用工作流创建可以调用的 API 应用。

## <a name="helpful-tools"></a>有用的工具
为了使 API 更好地用于逻辑应用，我们建议生成 [swagger](http://swagger.io) 文档，用于详细介绍 API 支持的操作和参数。  许多库（如 [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle)）可为你自动生成 swagger。  你还可以使用 [TRex](https://github.com/nihaue/TRex) 帮助在 swagger 中进行批注，以便使其更好地用于逻辑应用（显示名称、属性类型等）。  对于为逻辑应用构建的一些 API 应用示例，请务必查阅我们的 [GitHub 存储库](http://github.com/logicappsio)或[博客](http://aka.ms/logicappsblog)。

## <a name="actions"></a>操作
逻辑应用的基本操作是一个控制器，用于接受 HTTP 请求，并返回响应（通常是 200）。  但是，可以根据自己的需求按照其他模式来扩展操作。

默认情况下，逻辑应用引擎在 1 分钟后将请求设为超时。  但是，可以按照下面详细介绍的异步或 webhook 模式，让 API 在花费时间更长的操作上执行，并让引擎等待执行完成。

对于标准操作，只需在通过 swagger 公开的 API 中编写 HTTP 请求方法。  可以在我们的 [GitHub 存储库](https://github.com/logicappsio)中查看用于逻辑应用的 API 应用示例。  以下是实现使用自定义连接器的常见模式的方法。

### <a name="long-running-actions---async-pattern"></a>长时间运行操作 — 异步模式
运行长时间步骤或任务时，需要做的第一件事就是确保引擎知道尚未超时。 你还需要与引擎沟通它如何知道任务完成时间，最后需要将相关数据返回给引擎，以便它可以继续工作流。 你可以按照下面的流通过 API 完成这些操作。 以下步骤是从自定义 API 的角度进行描述的：

1. 收到请求后，立即返回响应（在工作完成之前）。 此响应是一个 `202 ACCEPTED` 响应，让引擎知道你获取了数据，接受了有效负载，并且现在正在处理数据。 202 响应包含以下标头： 
   
   * `location` 标头（必需）：这是逻辑应用 URL 的绝对路径，可用于检查作业的状态。
   * `retry-after`（可选，对于操作，默认值为 20）。 这是引擎轮询位置标头 URL 以检查状态之前应等待的秒数。
2. 检查作业状态后，执行以下检查： 
   
   * 如果作业已完成：返回 `200 OK` 响应，并带有响应有效负载。
   * 如果作业仍在处理：返回另一个 `202 ACCEPTED` 响应，其标头与初始响应相同

该模式允许在自定义 API 线程中运行时间很长的任务，但与逻辑应用引擎保持活动连接，因此任务不会超时，将继续运行直到任务完成。 将此 API 添加到逻辑应用后，必须注意的是，在针对逻辑应用继续轮询和检查状态的定义中，无需执行任何操作。 只要引擎发现具有有效位置标头的 202 ACCEPTED 响应，就会遵循异步模式，并继续轮询位置标头，直到返回非 202 响应。

可以单击[此处](https://github.com/jeffhollan/LogicAppsAsyncResponseSample)查看 GitHub 中此模式的示例

### <a name="webhook-actions"></a>Webhook 操作
在工作流期间，可以让逻辑应用暂停并等待“回调”后继续运行。  回调的格式为 HTTP POST。  若要实现此模式，需要在控制器上提供两个终结点：订阅和取消订阅。

在“订阅”终结点上，逻辑应用创建并注册回调 URL，API 可以 HTTP POST 的格式存储并回调此 URL。  将任何内容/标头传递到逻辑应用，并可将它们用于工作流的其余部分。  一旦到达此步骤，逻辑应用引擎就会调用针对执行的订阅点。

如果运行已取消，逻辑应用引擎就会调用“取消订阅”终结点。  API 会根据需要取消注册回调 URL。

当前逻辑应用设计器不支持通过 swagger 发现 webhook 终结点，因此，若要使用此类型的操作，必须添加“Webhook”操作，并指定请求的 URL、标头和正文。  可以根据需要在任何字段中使用 `@listCallbackUrl()` 工作流函数，以传递回调 URL。

可以单击[此处](https://github.com/jeffhollan/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)查看 GitHub 中 webhook 模式的示例

## <a name="triggers"></a>触发器
除了操作外，还可以将自定义 API 用作逻辑应用的触发器。  可以遵循下面两种模式触发逻辑应用：

### <a name="polling-triggers"></a>轮询触发器
轮询触发器的行为非常类似于上面的长时间运行的异步操作。  一定时间之后，逻辑应用引擎调用触发器终结点（具体取决于 SKU，高级版为 15 秒，标准版为 1 分钟，免费版为 1 小时）。

如果没有数据可用，那么触发器返回 `202 ACCEPTED` 响应，该响应具有 `location` 和 `retry-after` 标头。  但是，对于触发器而言建议 `location` 标头包含查询参数 `triggerState`。  这是某种标识符以便于 API 知道最后一次触发逻辑应用的时间。  如果没有数据可用，那么触发器返回 `200 OK` 响应，该响应包含内容有效负载。  该响应将触发逻辑应用。

例如，如果要轮询以查看某个文件是否可用，则可以构建执行以下操作的轮询触发器：

* 如果接收的请求没有 triggerState，则 API 返回带 `location` 标头的 `202 ACCEPTED` 响应，此标头的 triggerState 为当前时间和值为 15 的 `retry-after`。
* 如果接收的请求带有 triggerState：
  * 查看在 triggerState 的日期时间之后是否添加了任何文件。 
  * 如果有 1 个文件，则返回包含内容有效负载的 `200 OK` 响应，将 triggerState 增加到我返回的文件的日期时间，并将 `retry-after` 设为 15。
  * 如果有多个文件，我一次可以使用 `200 OK` 返回一个，增加 `location` 标头中的 triggerState，并将 `retry-after` 设为 0。  这样引擎就会知道有很多数据可用，它将立即请求位于指定的 `location` 标头中的数据。
  * 如果没有可用的文件，则返回 `202 ACCEPTED` 响应，并保留 `location` triggerState 的值不变。  将 `retry-after` 设置为 15。

可以单击[此处](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)查看 GitHub 中轮询触发器的示例

### <a name="webhook-triggers"></a>Webhook 触发器
Webhook 触发器的行为非常类似于上述 Webhook 操作。  一旦添加并保存 webhook 触发器，逻辑应用引擎就会调用“订阅”终结点。  API 可以注册 webhook URL，并在数据可用时通过 HTTP POST 调用。  内容的有效负载和标头被传递到逻辑应用运行中。

如果删除了 webhook 触发器（删除整个逻辑应用，或只删除 webhook 触发器），则引擎将调用“取消订阅” URL，API 可以从中取消注册回调 URL，并根据需要停止任何进程。

当前逻辑应用设计器不支持通过 swagger 发现 webhook 触发器，因此，若要使用此类型的操作，必须添加“Webhook”触发器，并指定请求的 URL、标头和正文。  可以根据需要在任何字段中使用 `@listCallbackUrl()` 工作流函数，以传递回调 URL。

可以单击[此处](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)查看 GitHub 中 webhook 触发器的示例




<!--HONumber=Nov16_HO3-->


