---
title: 等待事件和响应事件
description: 使用 Azure 逻辑应用自动执行基于服务终结点的事件触发、暂停和恢复的工作流
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 08/27/2020
tags: connectors
ms.openlocfilehash: 7c6f3c4e3e4a2a29fe6a02c03043e3dfb81a2010
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89227893"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>在 Azure 逻辑应用中使用 HTTP Webhook 创建和运行基于事件的自动化工作流

使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md) 和内置 HTTP Webhook 连接器，你可以创建用于订阅服务终结点的自动化任务和工作流，等待特定事件，并基于这些事件运行，而不是定期检查或 *轮询* 该终结点。

下面是一些基于 webhook 的示例工作流：

* 在触发逻辑应用运行之前，等待来自 [Azure 事件中心](https://github.com/logicappsio/EventHubAPI)的某个项抵达。
* 在继续运行工作流之前等待批准。

本文介绍如何使用 Webhook 触发器和 Webhook 操作，以便逻辑应用可以接收服务终结点上的事件并对其作出响应。

## <a name="how-do-webhooks-work"></a>Webhook 的工作原理

Webhook 触发器是基于事件的，不依赖于定期检查或轮询新项。 当你保存以 webhook 触发器开头的逻辑应用，或者将逻辑应用从 "禁用" 更改为 "已启用" 时，webhook 触发器将通过向该终结点注册*回调 URL*来*订阅*指定的服务终结点。 然后，触发器等待该服务终结点调用 URL，该 URL 开始运行逻辑应用。 与[请求触发器](connectors-native-reqres.md)类似，发生指定的事件时，会立即激发逻辑应用。 如果删除触发器并保存逻辑应用，或者将逻辑应用从 "启用" 更改为 "已禁用"，webhook 触发器将从服务终结点取消 *订阅* 。

Webhook 操作也是基于事件的，并通过向指定的服务终结点注册一个*回调 URL*来*订阅*该终结点。 Webhook 操作会暂停逻辑应用的工作流，并等到服务终结点调用 URL，然后再继续运行逻辑应用。 在这些情况下，webhook 操作将从服务终结点取消 *订阅* ：

* 当 Webhook 操作成功完成时
* 如果逻辑应用运行在等待响应时已取消
* 在逻辑应用超时之前

例如, Office 365 Outlook 连接器的[**发送审批电子邮件**](connectors-create-api-office365-outlook.md)操作就是遵循此模式的 Webhook 操作示例。 可以使用 Webhook 操作将此模式扩展到任何服务中。

有关详细信息，请参阅以下主题：

* [Webhook 和订阅](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [创建支持 Webhook 的自定义 API](../logic-apps/logic-apps-create-api-app.md)

有关对逻辑应用的入站调用的加密、安全和授权（例如 [传输层安全性 (TLS) ](https://en.wikipedia.org/wiki/Transport_Layer_Security)，之前称为安全套接字层 (SSL) ），或 [Azure Active Directory 开放式身份验证 (Azure AD OAuth) ](../active-directory/develop/index.yml)，请参阅 [对基于请求的触发器的入站调用的安全访问和数据访问](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 适当支持[逻辑应用中 Webhook 触发器](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)或[逻辑应用中 Webhook 操作](../logic-apps/logic-apps-create-api-app.md#webhook-actions) Webhook 订阅和取消订阅模式的已部署终结点或 API 的 URL

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

* 要在其中等待目标终结点上发生特定事件的逻辑应用。 若要开始使用 HTTP Webhook 触发器，请[创建一个空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 HTTP Webhook 操作，请使用所需的任何触发器启动逻辑应用。 此示例的第一步是使用 HTTP 触发器。

## <a name="add-an-http-webhook-trigger"></a>添加 HTTP Webhook 触发器

此内置触发器调用目标服务上的订阅终结点，并向目标服务注册回调 URL。 然后，逻辑应用会等待目标服务向回调 URL 发送 `HTTP POST` 请求。 发生此事件时，触发器将触发，并会将请求中的任何数据传递给工作流。

1. 登录到 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开空白逻辑应用。

1. 在设计器上的搜索框中，输入 `http webhook` 作为筛选器。 在“触发器”列表中选择“HTTP Webhook”触发器。 

   ![选择 HTTP Webhook 触发器](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   此示例将触发器重命名为 `HTTP Webhook trigger`，使步骤的名称更具描述性。 此外，该示例稍后会添加 HTTP Webhook 操作，因此，两个名称必须唯一。

1. 提供要对订阅和取消订阅调用使用的 [HTTP Webhook 触发器参数](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)值。

   在此示例中，触发器包括执行订阅和取消订阅操作时要使用的方法、URI 和消息正文。

   ![输入 HTTP Webhook 触发器参数](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **订阅 - 方法** | 是 | 订阅目标终结点时使用的方法 |
   | **订阅 - URI** | 是 | 用于订阅目标终结点的 URL |
   | **订阅 - 正文** | 否 | 要包含在订阅请求中的任何消息正文。 此示例包括使用 `@listCallbackUrl()` 表达式来检索逻辑应用的回调 URL，该 URL 唯一标识订阅方（即逻辑应用）。 |
   | **取消订阅 - 方法** | 否 | 取消订阅目标终结点时使用的方法 |
   | **取消订阅 - URI** | 否 | 用于取消订阅目标终结点的 URL |
   | **取消订阅 - 文本** | 否 | 要包含在取消订阅请求中的可选消息正文 <p><p>**注意**：此属性不支持使用 `listCallbackUrl()` 函数。 但是，触发器会自动包括并发送标头、`x-ms-client-tracking-id` 和 `x-ms-workflow-operation-name`，目标服务可以使用这些标头来唯一标识订阅方。 |
   ||||

1. 若要添加其他触发器属性，请打开“添加新参数”列表。

   ![添加更多触发器属性](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   例如，如果需要使用身份验证，可以添加“订阅 - 身份验证”和“取消订阅 - 身份验证”属性 。 有关 HTTP Webhook 可用的身份验证类型的详细信息，请参阅[向出站调用添加身份验证](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 继续使用触发器激发时运行的操作生成逻辑应用的工作流。

1. 完成后，请记得保存逻辑应用。 在设计器工具栏上选择“保存”。

   保存逻辑应用将在目标服务上调用订阅终结点并注册回调 URL。 然后，逻辑应用会等待目标服务向回调 URL 发送 `HTTP POST` 请求。 发生此事件时，触发器将触发，并会将请求中的任何数据传递给工作流。 如果此操作成功完成，触发器将取消订阅终结点，并且逻辑应用将继续运行剩余的工作流。

## <a name="add-an-http-webhook-action"></a>添加 HTTP Webhook 操作

此内置操作调用目标服务上的订阅终结点，并向目标服务注册回调 URL。 然后，逻辑应用会暂停并等待目标服务向回调 URL 发送 `HTTP POST` 请求。 发生此事件时，操作会将请求中的任何数据传递给工作流。 如果此操作成功完成，操作将取消订阅终结点，并且逻辑应用将继续运行剩余的工作流。

1. 登录到 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开逻辑应用。

   此示例的第一步是使用 HTTP Webhook 触发器。

1. 在要添加 HTTP Webhook 操作的步骤下，选择“新建步骤”。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 选择出现的加号 ( **+** )，然后选择“添加操作”。

1. 在设计器上的搜索框中，输入 `http webhook` 作为筛选器。 在“操作”列表中选择“HTTP Webhook”操作。 

   ![选择 HTTP Webhook 操作](./media/connectors-native-webhook/select-http-webhook-action.png)

   此示例将操作重命名为“HTTP Webhook action”，使步骤的名称更具描述性。

1. 提供要对订阅和取消订阅调用使用的 HTTP Webhook 操作参数（类似于 [HTTP Webhook 触发器参数](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)）值。

   在此示例中，操作包括执行订阅和取消订阅操作时要使用的方法、URI 和消息正文。

   ![输入 HTTP Webhook 操作参数](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **订阅 - 方法** | 是 | 订阅目标终结点时使用的方法 |
   | **订阅 - URI** | 是 | 用于订阅目标终结点的 URL |
   | **订阅 - 正文** | 否 | 要包含在订阅请求中的任何消息正文。 此示例包括使用 `@listCallbackUrl()` 表达式来检索逻辑应用的回调 URL，该 URL 唯一标识订阅方（即逻辑应用）。 |
   | **取消订阅 - 方法** | 否 | 取消订阅目标终结点时使用的方法 |
   | **取消订阅 - URI** | 否 | 用于取消订阅目标终结点的 URL |
   | **取消订阅 - 文本** | 否 | 要包含在取消订阅请求中的可选消息正文 <p><p>**注意**：此属性不支持使用 `listCallbackUrl()` 函数。 但是，操作会自动包括并发送标头、`x-ms-client-tracking-id` 和 `x-ms-workflow-operation-name`，目标服务可以使用这些标头来唯一标识订阅方。 |
   ||||

1. 若要添加其他操作属性，请打开“添加新参数”列表。

   ![添加更多操作属性](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   例如，如果需要使用身份验证，可以添加“订阅 - 身份验证”和“取消订阅 - 身份验证”属性 。 有关 HTTP Webhook 可用的身份验证类型的详细信息，请参阅[向出站调用添加身份验证](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 完成后，请记得保存逻辑应用。 在设计器工具栏上选择“保存”。

   现在，此操作运行时，逻辑应用调用目标服务上的订阅终结点并注册回调 URL。 然后，逻辑应用会暂停工作流，并等待目标服务向回调 URL 发送 `HTTP POST` 请求。 发生此事件时，操作会将请求中的任何数据传递给工作流。 如果此操作成功完成，操作将取消订阅终结点，并且逻辑应用将继续运行剩余的工作流。

## <a name="trigger-and-action-outputs"></a>触发器和操作输出

下面是有关 HTTP Webhook 触发器或操作的输出的详细信息，输出中将返回以下信息：

| 属性名称 | 类型 | 说明 |
|---------------|------|-------------|
| headers | object | 请求中的标头 |
| body | object | JSON 对象 | 包含请求中正文内容的对象 |
| 状态代码 | int | 请求中的状态代码 |
|||

| 状态代码 | 说明 |
|-------------|-------------|
| 200 | 确定 |
| 202 | 已接受 |
| 400 | 错误的请求 |
| 401 | 未授权 |
| 403 | 禁止 |
| 404 | 未找到 |
| 500 | 内部服务器错误。 发生未知错误。 |
|||

## <a name="connector-reference"></a>连接器参考

有关触发器和操作参数的详细信息，请参阅 [HTTP Webhook 参数](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)。

## <a name="next-steps"></a>后续步骤

* [对基于请求的触发器的入站调用进行安全访问和数据访问](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [适用于逻辑应用的连接器](../connectors/apis-list.md)
