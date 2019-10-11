---
title: 等待并响应事件-Azure 逻辑应用
description: 使用 Azure 逻辑应用基于服务终结点上的事件自动触发、暂停和恢复工作流
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 10/10/2019
tags: connectors
ms.openlocfilehash: 36b0ea7233b449584bd83450b45276da5baa135b
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264337"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>在 Azure 逻辑应用中使用 HTTP webhook 创建和运行自动基于事件的工作流

使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和内置的 HTTP Webhook 连接器，可以通过生成逻辑应用，基于 HTTP 或 HTTPS 终结点上发生的特定事件自动化处于等待和运行状态的工作流。 例如，可以创建一个逻辑应用用于监视服务终结点：该逻辑应用可以等待特定的事件发生，然后触发工作流并运行指定的操作，这样，你就无需定期检查或轮询该终结点。

下面是一些基于事件的工作流示例：

* 在触发逻辑应用运行之前，等待来自 [Azure 事件中心](https://github.com/logicappsio/EventHubAPI)的某个项抵达。
* 在继续运行工作流之前等待批准。

## <a name="how-do-webhooks-work"></a>Webhook 的工作原理

HTTP Webhook 触发器基于事件，它不依赖于定期检查或轮询新项。 保存以 Webhook 触发器开头的逻辑应用，或者将逻辑应用从已禁用状态更改为已启用状态时，Webhook 触发器会通过将一个回调 URL 注册到特定的服务或终结点，来订阅该服务或终结点。 然后，该触发器将等待该服务或终结点调用该 URL，从而开始运行逻辑应用。 与[请求触发器](connectors-native-reqres.md)类似，发生指定的事件时，会立即激发逻辑应用。 如果你删除触发器并保存逻辑应用，或者将逻辑应用从已启用状态更改为已禁用状态，则触发器将从服务或终结点取消订阅。

HTTP Webhook 操作也基于事件，它会通过将一个回调 URL 注册到特定的服务或终结点，来订阅该服务或终结点。 Webhook 操作将暂停逻辑应用的工作流，在逻辑应用恢复运行之前，会等待该服务或终结点调用该 URL。 在以下情况下，操作逻辑应用将从服务或终结点取消订阅：

* 当 Webhook 操作成功完成时
* 如果逻辑应用运行在等待响应时已取消
* 在逻辑应用超时之前

例如, Office 365 Outlook 连接器的[**发送审批电子邮件**](connectors-create-api-office365-outlook.md)操作就是遵循此模式的 Webhook 操作示例。 可以使用 Webhook 操作将此模式扩展到任何服务中。

> [!NOTE]
> 逻辑应用在接收回 HTTP webhook 触发器或操作的回调时强制执行传输层安全性（TLS）1.2。 如果出现 SSL 握手错误，请确保使用 TLS 1.2。

有关详细信息，请参阅以下主题：

* [HTTP Webhook 触发器参数](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhook 和订阅](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [创建支持 Webhook 的自定义 API](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 适当支持[逻辑应用中 Webhook 触发器](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)或[逻辑应用中 Webhook 操作](../logic-apps/logic-apps-create-api-app.md#webhook-actions) Webhook 订阅和取消订阅模式的已部署终结点或 API 的 URL

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

* 要在其中等待目标终结点上发生特定事件的逻辑应用。 若要开始使用 HTTP Webhook 触发器，请[创建一个空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 HTTP Webhook 操作，请使用所需的任何触发器启动逻辑应用。 此示例的第一步是使用 HTTP 触发器。

## <a name="add-an-http-webhook-trigger"></a>添加 HTTP Webhook 触发器

此内置触发器将一个回调 URL 注册到指定的服务，并等待该服务向该 URL 发送 HTTP POST 请求。 发生此事件时，触发器将会激发，并立即运行逻辑应用。

1. 登录到 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开空白逻辑应用。

1. 在设计器的搜索框中，输入“http webhook”作为筛选器。 在“触发器”列表中选择“HTTP Webhook”触发器。

   ![选择 HTTP Webhook 触发器](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   此示例将触发器重命名为“HTTP Webhook trigger”，使步骤的名称更具描述性。 此外，该示例稍后会添加 HTTP Webhook 操作，因此，两个名称必须唯一。

1. 提供要对订阅和取消订阅调用使用的 [HTTP Webhook 触发器参数](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)值，例如：

   ![输入 HTTP Webhook 触发器参数](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. 若要添加其他可用参数，请打开“添加新参数”列表，并选择所需的参数。

   有关可用于 HTTP Webhook 的身份验证类型的详细信息，请参阅[对 HTTP 触发器和操作进行身份验证](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)。

1. 继续使用触发器激发时运行的操作生成逻辑应用的工作流。

1. 完成后，请记得保存逻辑应用。 在设计器工具栏上选择“保存”。

   保存逻辑应用会调用订阅终结点，并注册用于触发此逻辑应用的回调 URL。

1. 现在，每当目标服务向回调 URL 发送 `HTTP POST` 请求时，逻辑应用都会激发，并包含通过该请求传递的所有数据。

## <a name="add-an-http-webhook-action"></a>添加 HTTP Webhook 操作

此内置操作将一个回调 URL 注册到指定的服务，暂停逻辑应用的工作流，并等待该服务向该 URL 发送 HTTP POST 请求。 发生此事件时，该操作将继续运行逻辑应用。

1. 登录到 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开逻辑应用。

   此示例的第一步是使用 HTTP Webhook 触发器。

1. 在要添加 HTTP Webhook 操作的步骤下，选择“新建步骤”。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 选择出现的加号 ( **+** )，然后选择“添加操作”。

1. 在设计器的搜索框中，输入“http webhook”作为筛选器。 在“操作”列表中选择“HTTP Webhook”操作。

   ![选择 HTTP Webhook 操作](./media/connectors-native-webhook/select-http-webhook-action.png)

   此示例将操作重命名为“HTTP Webhook action”，使步骤的名称更具描述性。

1. 提供要对订阅和取消订阅调用使用的 HTTP Webhook 操作参数（类似于 [HTTP Webhook 触发器参数](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger)）值，例如：

   ![输入 HTTP Webhook 操作参数](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   在运行时，逻辑应用将在运行此操作时调用订阅终结点。 然后，逻辑应用会暂停工作流，并等待目标服务向回调 URL 发送 `HTTP POST` 请求。 如果该操作成功完成，它将从终结点取消订阅，而逻辑应用将继续运行工作流。

1. 若要添加其他可用参数，请打开“添加新参数”列表，并选择所需的参数。

   有关可用于 HTTP Webhook 的身份验证类型的详细信息，请参阅[对 HTTP 触发器和操作进行身份验证](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)。

1. 完成后，请记得保存逻辑应用。 在设计器工具栏上选择“保存”。

## <a name="connector-reference"></a>连接器参考

有关触发器和操作参数的详细信息，请参阅 [HTTP Webhook 参数](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger)。

### <a name="output-details"></a>输出详细信息

下面是有关 HTTP Webhook 触发器或操作的输出的详细信息，输出中将返回以下信息：

| 属性名 | type | 描述 |
|---------------|------|-------------|
| headers | object | 请求中的标头 |
| body | object | JSON 对象 | 包含请求中正文内容的对象 |
| 状态代码 | int | 请求中的状态代码 |
|||

| 状态代码 | 描述 |
|-------------|-------------|
| 200 | OK |
| 202 | 已接受 |
| 400 | 错误的请求 |
| 401 | 未授权 |
| 403 | 禁止 |
| 404 | 未找到 |
| 500 | 内部服务器错误。 发生未知错误。 |
|||

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
