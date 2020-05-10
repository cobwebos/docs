---
title: 为 Azure 逻辑应用创建 Web API 和 REST API
description: 创建用于调用 API、服务或系统的 Web API 和 REST API，以便在 Azure 逻辑应用中进行系统集成
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/26/2017
ms.openlocfilehash: d892dc75d4e745912ceaf444b56494a2e0ed2a19
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005255"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>创建可从 Azure 逻辑应用调用的自定义 API

虽然 Azure 逻辑应用提供可用于逻辑应用工作流的[数百个内置连接器](../connectors/apis-list.md)，但建议调用不作为连接器提供的 API、系统和服务。 可以创建自己的 API，提供在逻辑应用中使用的操作和触发器。 下面是一些其他理由，解释为何建议创建可从逻辑应用工作流调用的自定义 API：

* 扩展当前系统集成和数据集成工作流。
* 帮助客户使用服务来管理专业或个人任务。
* 扩展服务的市场宣传、可发现性和使用。

连接器本质上是 Web API，此类 API 将 REST 用于可插入接口、将 [Swagger 元数据格式](https://swagger.io/specification/)用于文档、将 JSON 用作其数据交换格式。 因为连接器是通过 HTTP 终结点进行通信的 REST API，所以可以使用任何语言生成连接器，如 .NET、Java、Python 或 Node.js。 此外，还可在 [Azure 应用服务](../app-service/overview.md)上托管API，前者是一款平台即服务 (PaaS) 产品，可为 API 托管提供一种最简单且可缩放性最高的最佳方法。 

对于要用于逻辑应用的自定义 API，API 可以提供在逻辑应用工作流中执行特定任务的[操作](./logic-apps-overview.md#logic-app-concepts)**。 API 还可充当[触发器](./logic-apps-overview.md#logic-app-concepts)**，在新数据或事件满足指定条件时启动逻辑应用工作流。 本主题介绍根据想要 API 提供的行为，在 API 中生成操作和触发器可以遵循的常见模式。

可在 [Azure App Service](../app-service/overview.md) 上托管API，它是一款平台即服务 (PaaS) 产品，可提供简单的高缩放性 API 托管。

> [!TIP] 
> 虽然可以将 API 部署为 Web 应用，但请考虑将 API 部署为 API 应用，这样可以更轻松地在云和本地生成、托管和使用 API。 不必更改 API 中的任何代码 - 可直接将代码部署到 API 应用。 例如，了解如何生成使用以下语言创建的 API 应用： 
> 
> * [ASP.NET](../app-service/app-service-web-get-started-dotnet.md)。 
> * [Java](../app-service/app-service-web-get-started-java.md)
> * [Node.js](../app-service/app-service-web-get-started-nodejs.md)
> * [PHP](../app-service/app-service-web-get-started-php.md)
> * [Python](../app-service/containers/quickstart-python.md)
> * [Ruby](../app-service/containers/quickstart-ruby.md)
>
> 有关为逻辑应用生成的 API 应用示例，请访问 [Azure 逻辑应用 GitHub 存储库](https://github.com/logicappsio)或[博客](https://aka.ms/logicappsblog)。

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>自定义 API 和自定义连接器有何不同？

本质上来说，自定义 API 和[自定义连接器](../logic-apps/custom-connector-overview.md)都是 Web API，此类 API 将 REST 用于可插入接口、将 [Swagger 元数据格式](https://swagger.io/specification/)用于文档、将 JSON 用作其数据交换格式。 因为这些 API 和连接器是通过 HTTP 终结点进行通信的 REST API，所以可以使用任何语言生成自定义 API 和连接器，如 .NET、Java、Python 或 Node.js。

自定义 API 允许调用非连接器 API，并提供可使用 HTTP + Swagger、Azure API 管理或应用程序服务调用的终结点。 自定义连接器的工作方式与自定义 API 类似，但它还具有以下属性：

* 在 Azure 中注册为逻辑应用连接器资源。
* 在逻辑应用设计器中，显示在 Microsoft 托管连接器旁边（含图标）。
* 仅适用于满足以下条件的连接器创建者和逻辑应用用户：在部署逻辑应用的区域中具有相同的 Azure Active Directory 租户和 Azure 订阅。

还可指定注册的连接器进行 Microsoft 认证。 此过程验证注册的连接器是否满足公共使用的条件，并使这些连接器可供 Power Automate 和 Microsoft Power Apps 中的用户使用。

有关自定义连接器的详细信息，请参阅 

* [自定义连接器概述](../logic-apps/custom-connector-overview.md)
* [从 Web API 创建自定义连接器](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [在 Azure 逻辑应用中注册自定义连接器](../logic-apps/logic-apps-custom-connector-register.md)

## <a name="helpful-tools"></a>有用的工具

当 API 也具有描述 API 操作和参数的 [Swagger 文档](https://swagger.io/specification/)时，自定义 API 最适用于逻辑应用。
许多库（如 [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle)）可自动为你生成 Swagger 文件。 若要批注 Swagger 文件的显示名称、属性类型等，也可使用 [TRex](https://github.com/nihaue/TRex) 以便 Swagger 文件更适用于逻辑应用。

<a name="actions"></a>

## <a name="action-patterns"></a>操作模式

对于执行任务的逻辑应用，自定义 API 应提供[操作](./logic-apps-overview.md#logic-app-concepts)**。 API 中的每个操作 (operation) 将映射到操作 (action)。 基本操作是接受 HTTP 请求并返回 HTTP 响应的控制器。 例如，逻辑应用将 HTTP 请求发送到 Web 应用或 API 应用。 然后应用返回 HTTP 响应及逻辑应用可处理的内容。

对于标准操作，可在 API 中编写 HTTP 请求方法，并在 Swagger 文件中描述该方法。 然后，可以使用 [HTTP 操作](../connectors/connectors-native-http.md)或 [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) 操作直接调用 API。 默认情况下，必须在[请求超时限制](./logic-apps-limits-and-config.md)内返回响应。 

![标准操作模式](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a>要在 API 完成较长时间运行的任务时使逻辑应用等待，API 可以遵循本主题中所述的[异步轮询模式](#async-pattern)或[异步 Webhook 模式](#webhook-actions)。 为帮助你形象化这些模式的不同行为，把这一过程想象成从面包店预定定制的蛋糕。 轮询模式类似于你每隔 20 分钟打电话给面包店询问蛋糕是否做好。 Webhook 模式类似于面包店记下你的电话号码以便蛋糕做好后打电话给你。

有关示例，请访问[逻辑应用 GitHub 存储库](https://github.com/logicappsio)。 此外，详细了解[操作的用量计量](logic-apps-pricing.md)。

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>使用轮询操作模式执行长时间运行任务

为使 API 执行时间超过[请求超时限制](./logic-apps-limits-and-config.md)的任务，可以使用异步轮询模式。 此模式使 API 在单独线程中执行任务，但与逻辑应用引擎保持活动连接。 这样一来，在 API 完成工作之前，逻辑应用不会超时或继续进行工作流的下一步。

以下是常规模式：

1. 请确保引擎知道 API 接受了请求并已开始工作。
2. 当引擎针对作业状态进行后续请求时，让引擎知道 API 完成任务的时间。
3. 返回引擎的相关数据，以便逻辑应用工作流可以继续。

<a name="bakery-polling-action"></a>现在将之前的面包店类比用于轮询模式，假设你打电话给面包店预订定制的蛋糕并要求送货。 做蛋糕的过程需要花费时间，而且你不想在面包店做蛋糕的时候拿着电话等待。 面包店确认订单，并让你每隔 20 分钟打电话确认蛋糕是否做好。 20 分钟之后，你打电话给面包店，但是面包店告诉你蛋糕还没有做好，请过 20 分钟再打电话。 这一来一回的过程持续到面包店告诉你面包已经做好并且送出。 

那么让我们回来轮询模式。 面包店代表自定义 API，而你 - 预订蛋糕的客户代表逻辑应用引擎。 当引擎通过请求调用 API 时， API 确认请求，并隔段时间在引擎检查作业状态时进行响应。 引擎继续检查作业状态，直到 API 响应作业已完成，并将数据返回给逻辑应用，随之推进工作流。 

![轮询操作模式](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

以下是 API 要跟随的具体步骤（从 API 的角度）：

1. 当 API 获取 HTTP 请求开始工作时，立即返回 HTTP `202 ACCEPTED` 响应与此步骤中后面所述的 `location` 标头。 此响应让逻辑应用引擎知道 API 收到了请求、接受了请求有效负载（数据输入），并且现在正在处理。 
   
   `202 ACCEPTED` 响应应包含这些标头：
   
   * 必需**：用于指定逻辑应用引擎检查 API 作业状态的 URL 的绝对路径的 `location` 标头

   * 可选**：用于指定引擎检查 `location` URL 获知作业状态之前应等待秒数的 `retry-after` 标头。 

     默认情况下，引擎每隔 20 秒检查一次。 若要指定不同的时间间隔，请包括 `retry-after` 标头和下次轮询前的秒数。

2. 超过指定的时间后，逻辑应用引擎轮询 `location` URL 来检查作业状态。 API 应执行以下检查，并返回以下响应：
   
   * 如果作业已完成，返回 HTTP `200 OK` 响应及响应有效负载（下一步的输入）。

   * 如果作业仍在处理，返回另一个 HTTP `202 ACCEPTED` 响应，但其标头与初始响应相同。

当 API 遵循此模式时，若要继续检查作业状态，无需在逻辑应用工作流定义中执行任何操作。 当引擎获取 HTTP `202 ACCEPTED` 响应和有效的 `location` 标头，引擎尊重异步模式，并检查 `location` 标头，直到 API 返回非 202 响应。

> [!TIP]
> 有关异步模式的示例，查看 [GitHub 中异步控制器响应示例](https://github.com/logicappsio/LogicAppsAsyncResponseSample)。

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>使用 Webhook 操作模式执行长时间运行任务

作为替代方法，可对长时间运行的任务和异步处理使用 Webhook 模式。 此模式让逻辑应用暂停并等待 API 的“回叫”，以完成处理并继续工作流。 此回叫是在事件发生时将消息发送到 URL 的 HTTP POST。 

<a name="bakery-webhook-action"></a>现在将之前的面包店类比用于 Webhook 模式，假设你打电话给面包店预订定制的蛋糕并要求送货。 做蛋糕的过程需要花费时间，而且你不想在面包店做蛋糕的时候拿着电话等待。 面包店确认你的订单，但这一次，你把电话号码给面包店，以便蛋糕做好后他们可以给你打电话。 这次面包店在蛋糕做好并送出时告知你。

我们回到 Webhook 模式，面包店代表自定义 API，而你 - 预订蛋糕的客户代表逻辑应用引擎。 引擎通过请求调用 API，且包括了“回叫”URL。
完成作业后，API 使用 URL 来通知引擎并将数据返回到逻辑应用，然后继续工作流。 

对于此模式，在控制器上设置两个终结点：`subscribe` 和 `unsubscribe`

*  `subscribe` 终结点：当执行到达工作流中的 API 操作时，逻辑应用引擎调用 `subscribe` 终结点。 此步骤导致逻辑应用创建 API 将存储的回叫 URL，然后等待工作完成时 API 的回叫。 API 通过 HTTP POST 回叫 URL，并将任何返回的内容和标头作为输入传递到逻辑应用。

* `unsubscribe` 终结点： 如果逻辑应用运行已取消，逻辑应用引擎将调用 `unsubscribe` 终结点。 然后 API 可以取消注册回叫 URL，并根据需要停止任何进程。

![Webhook 操作模式](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

目前，逻辑应用设计器不支持通过 Swagger 发现 Webhook 终结点。 因此对于此模式，需要添加 [Webhook **** 操作](../connectors/connectors-native-webhook.md)并指定 URL、标头以及请求正文。 另请参阅[工作流操作和触发器](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action)。 有关 Webhook 模式的示例，查看 [GitHub 中的 Webhook 触发器示例](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)。

下面是一些其他提示和说明：

* 若要传入回叫 URL，可以根据需要使用以前任何字段中的 `@listCallbackUrl()` 工作流函数。

* 如果同时拥有逻辑应用和订阅服务，则在调用回调 URL 之后，无需`unsubscribe`调用终结点。 否则，逻辑应用运行时需要调用`unsubscribe`终结点，以指示不需要更多调用，也不允许服务器端的资源清理。

<a name="triggers"></a>

## <a name="trigger-patterns"></a>触发器模式

自定义 API 还可充当[触发器](./logic-apps-overview.md#logic-app-concepts)，在新数据或事件满足指定条件时启动逻辑应用**。 此触发器可以定期检查或者等待侦听服务终结点上的新数据或事件。 如果新数据或事件满足指定的条件，触发器触发并启动正在侦听该触发器的逻辑应用。 若要以这种方式启动逻辑应用，API 可以遵循[轮询触发器](#polling-triggers)** 或 [Webhook 触发器](#webhook-triggers)** 模式。 这些模式类似于其[轮询操作](#async-pattern)和 [Webhook 操作](#webhook-actions)的对应项。 另请详细了解[触发器的用量计量](logic-apps-pricing.md)。

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>使用轮询触发器模式定期检查新数据或事件

轮询触发器** 的行为非常类似于本主题中前面所述的[轮询操作](#async-pattern)。 逻辑应用引擎定期调用并检查触发器终结点，查看新数据或事件。 如果引擎发现满足指定条件的新数据或事件，触发器即触发。 然后，引擎创建将数据处理为输入的逻辑应用实例。 

![轮询触发器模式](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> 每个轮询请求算作操作执行，即使未创建任何逻辑应用实例。 若要防止多次处理相同数据，触发器应清理已读取且传递到逻辑应用的数据。

以下是轮询触发器的具体步骤（从 API 的角度）：

| 找到新数据或事件？  | API 响应 | 
| ------------------------- | ------------ |
| 已找到 | 返回 HTTP `200 OK` 状态与响应有效负载（下一步的输入）。 <br/>此响应创建逻辑应用实例，并启动工作流。 | 
| 未找到 | 返回 HTTP `202 ACCEPTED` 状态与 `location` 标头和 `retry-after` 标头。 <br/>对于触发器，`location` 标头还应包含 `triggerState` 查询参数，通常是“时间戳”。 API 可以使用此标识符跟踪上次触发逻辑应用的时间。 | 
||| 

例如，若要定期检查服务的新文件，可生成具有以下行为的轮询触发器：

| 请求是否包含 `triggerState`？ | API 响应 | 
| -------------------------------- | -------------| 
| 否 | 返回 HTTP `202 ACCEPTED` 状态及 `triggerState` 设置为当前时间、`retry-after` 间隔为 15 秒的 `location` 标头。 | 
| 是 | 在 `triggerState` 的 `DateTime` 之后检查服务的已添加文件。 | 
||| 

| 找到的文件数 | API 响应 | 
| --------------------- | -------------| 
| 单个文件 | 返回 HTTP `200 OK` 状态和内容有效负载，将返回文件的 `triggerState` 更新为 `DateTime`，并将 `retry-after` 间隔设置为 15 秒。 | 
| 多个文件 | 每次返回一个文件和 HTTP `200 OK` 状态，更新 `triggerState`，并将 `retry-after` 间隔设置为 0 秒。 </br>这些步骤让引擎知道更多数据已可用，而且引擎应立即通过 `location` 标头中的 URL 请求数据。 | 
| 无文件 | 返回 HTTP `202 ACCEPTED` 状态，不更改 `triggerState`，并将 `retry-after` 间隔设置为 15 秒。 | 
||| 

> [!TIP]
> 有关轮询触发器模式的示例，请查看 [GitHub 中的轮询触发器控制器示例](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs)。

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>使用 Webhook 触发器模式等待和侦听新数据或事件

Webhook 触发器是推送触发器**，等待并侦听服务终结点上的新数据或事件。 如果新数据或事件满足指定的条件，触发器即触发，并创建逻辑应用实例，然后将数据处理为输入。
Webhook 触发器的行为非常类似于之前本主题中所述的 [Webhook 操作](#webhook-actions)，并设置有 `subscribe` 和 `unsubscribe` 终结点。 

* `subscribe` 终结点：在逻辑应用中添加和保存 Webhook 触发器时，逻辑应用引擎调用 `subscribe` 终结点。 此步骤将导致逻辑应用创建 API 将存储的回叫 URL。 存在满足指定条件的新数据或事件时， API 通过 HTTP POST 回叫 URL。 内容有效负载和标头作为输入传递到逻辑应用。

* `unsubscribe` 终结点：如果删除 Webhook 触发器或整个逻辑应用，逻辑应用引擎调用 `unsubscribe` 终结点。 然后 API 可以取消注册回叫 URL，并根据需要停止任何进程。

![Webhook 触发器模式](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

目前，逻辑应用设计器不支持通过 Swagger 发现 Webhook 终结点。 因此对于此模式，需要添加 [Webhook 触发器](../connectors/connectors-native-webhook.md)**** 并指定 URL、 标头以及请求正文。 另请参阅 [HTTPWebhook 触发器](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger)。 有关 Webhook 模式的示例，请查看 [GitHub 中的 Webhook 触发器控制器示例](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)。

下面是一些其他提示和说明：

* 若要传入回叫 URL，可以根据需要使用以前任何字段中的 `@listCallbackUrl()` 工作流函数。

* 若要防止多次处理相同数据，触发器应清理已读取且传递到逻辑应用的数据。

* 如果同时拥有逻辑应用和订阅服务，则在调用回调 URL 之后，无需`unsubscribe`调用终结点。 否则，逻辑应用运行时需要调用`unsubscribe`终结点，以指示不需要更多调用，也不允许服务器端的资源清理。

## <a name="improve-security-for-calls-to-your-apis-from-logic-apps"></a>提高从逻辑应用对 Api 的调用的安全性

创建自定义 API 后，请为 API 设置身份验证，以便可以通过逻辑应用安全地调用它们。 了解[如何提高从逻辑应用对自定义 api 的调用的安全性](../logic-apps/logic-apps-custom-api-authentication.md)。

## <a name="deploy-and-call-your-apis"></a>部署和调用 API

设置身份验证后，请为 API 设置部署。 了解[如何通过逻辑应用部署和调用自定义 API](../logic-apps/logic-apps-custom-api-host-deploy-call.md)。

## <a name="publish-custom-apis-to-azure"></a>将自定义 API 发布到 Azure

要使自定义 API 可供 Azure 中其他逻辑应用用户使用，必须添加安全性并将这些 API 注册为逻辑应用连接器。 有关详细信息，请参阅[自定义连接器概述](../logic-apps/custom-connector-overview.md)。 

要使自定义 API 可供逻辑应用、Power Automate 和 Microsoft Power Apps 中所有用户使用，必须添加安全性、将 API 注册为逻辑应用连接器并向 [Microsoft Azure 认证计划](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/)提名你的连接器。 

## <a name="get-support"></a>获取支持

* 有关自定义 Api 的特定帮助， [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com)请联系。

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

* 为帮助改进逻辑应用，敬请在[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)上投票或发表看法。 

## <a name="next-steps"></a>后续步骤

* [处理错误和异常](../logic-apps/logic-apps-exception-handling.md)
* [使用 HTTP 终结点调用、触发或嵌套逻辑应用](../logic-apps/logic-apps-http-endpoint.md)
* [操作和触发器的用量计量](../logic-apps/logic-apps-pricing.md)
