---
title: 等待并响应事件
description: 使用 Azure 逻辑应用基于服务终结点上的事件自动触发、暂停和恢复工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 10/10/2019
tags: connectors
ms.openlocfilehash: 24746b7bbbbf3985a9801139b301a829c51a14da
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030080"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>在 Azure 逻辑应用中使用 HTTP webhook 创建和运行自动基于事件的工作流

使用[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和内置 HTTP Webhook 连接器，可以根据在 HTTP 或 HTTPS 终结点上生成的特定事件，自动执行等待和运行的工作流。 例如，可以创建一个逻辑应用，用于监视服务终结点，方法是在触发工作流并运行指定的操作之前等待特定的事件，而不是定期检查或*轮询*该终结点。

下面是一些基于事件的示例工作流：

* 在触发逻辑应用运行之前，等待某个项从[Azure 事件中心](https://github.com/logicappsio/EventHubAPI)到达。
* 继续工作流之前等待审批。

## <a name="how-do-webhooks-work"></a>Webhook 的工作原理是什么？

HTTP webhook 触发器是基于事件的，不依赖于定期检查或轮询新项。 当你保存以 webhook 触发器开头的逻辑应用，或者将逻辑应用从 "禁用" 更改为 "已启用" 时，webhook 触发器将通过向该服务或终结点注册*回调 URL*来*订阅*特定服务或终结点。 然后，触发器等待该服务或终结点调用 URL，该 URL 开始运行逻辑应用。 与[请求触发器](connectors-native-reqres.md)类似，当发生指定的事件时，逻辑应用会立即触发。 如果删除触发器并保存逻辑应用，或者将逻辑应用从 "启用" 更改为 "禁用"，则触发器将从服务或终结点取消*订阅*。

HTTP webhook 操作也是基于事件的操作，并通过使用该服务或终结点注册*回调 URL*来*订阅*特定服务或终结点。 Webhook 操作会暂停逻辑应用的工作流，并等待服务或终结点调用 URL，然后再继续运行逻辑应用。 在这些情况下，操作逻辑应用从服务或终结点取消*订阅*：

* Webhook 操作成功完成时
* 如果在等待响应时取消逻辑应用运行
* 逻辑应用超时之前

例如，Office 365 Outlook connector 的 "[**发送审批电子邮件**](connectors-create-api-office365-outlook.md)" 操作是遵循此模式的 webhook 操作示例。 可以通过使用 webhook 操作将此模式扩展到任何服务。

> [!NOTE]
> 逻辑应用在接收回 HTTP webhook 触发器或操作的回调时强制执行传输层安全性（TLS）1.2。 如果出现 SSL 握手错误，请确保使用 TLS 1.2。 对于传入调用，以下是受支持的密码套件：
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

有关详细信息，请参阅以下主题：

* [HTTP Webhook 触发器参数](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhook 和订阅](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [创建支持 webhook 的自定义 Api](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 已部署的终结点或 API 的 URL，该 URL 支持[逻辑应用中的 webhook](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)订阅和取消订阅模式，或逻辑应用中的 webhook 操作适当的[webhook 操作](../logic-apps/logic-apps-create-api-app.md#webhook-actions)

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

* 要在其中等待目标终结点上的特定事件的逻辑应用。 若要开始使用 HTTP Webhook 触发器，请[创建一个空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 HTTP Webhook 操作，请使用所需的任何触发器启动逻辑应用。 此示例使用 HTTP 触发器作为第一步。

## <a name="add-an-http-webhook-trigger"></a>添加 HTTP Webhook 触发器

此内置触发器向指定的服务注册一个回调 URL，并等待该服务向该 URL 发送 HTTP POST 请求。 发生此事件时，触发器将触发，并立即运行逻辑应用。

1. 登录 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开空白逻辑应用。

1. 在设计器的搜索框中，输入 "http webhook" 作为筛选器。 从 "**触发器**" 列表中，选择**HTTP Webhook**触发器。

   ![选择 HTTP Webhook 触发器](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   此示例将触发器重命名为 "HTTP Webhook 触发器"，以使该步骤具有更具描述性的名称。 此外，该示例稍后会添加 HTTP Webhook 操作，并且这两个名称必须是唯一的。

1. 提供要用于订阅和取消订阅调用的[HTTP Webhook 触发器参数](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)的值，例如：

   ![输入 HTTP Webhook 触发器参数](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. 若要添加其他可用参数，请打开 "**添加新参数**" 列表，然后选择所需的参数。

   有关可用于 HTTP Webhook 的身份验证类型的详细信息，请参阅[将身份验证添加到出站调用](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 继续使用触发器激发时运行的操作生成逻辑应用的工作流。

1. 完成后，请记住保存逻辑应用。 在设计器工具栏上，选择“保存”。

   保存逻辑应用会调用订阅终结点并注册用于触发此逻辑应用的回调 URL。

1. 现在，每当目标服务向回调 URL 发送 `HTTP POST` 请求时，逻辑应用都将触发，并包含通过请求传递的所有数据。

## <a name="add-an-http-webhook-action"></a>添加 HTTP Webhook 操作

此内置操作向指定的服务注册回调 URL，暂停逻辑应用的工作流，并等待该服务向该 URL 发送 HTTP POST 请求。 发生此事件时，操作会继续运行逻辑应用。

1. 登录 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开逻辑应用。

   此示例使用 HTTP Webhook 触发器作为第一步。

1. 在要添加 HTTP Webhook 操作的步骤下，选择 "**新建步骤**"。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 选择出现的加号（ **+** ），然后选择 "**添加操作**"。

1. 在设计器的搜索框中，输入 "http webhook" 作为筛选器。 从 "**操作**" 列表中，选择**HTTP Webhook**操作。

   ![选择 HTTP Webhook 操作](./media/connectors-native-webhook/select-http-webhook-action.png)

   此示例将操作重命名为 "HTTP Webhook 操作"，以使该步骤具有更具描述性的名称。

1. 为 HTTP Webhook 操作参数提供值，这些参数类似于要用于订阅和取消订阅调用的[Http webhook 触发器参数](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)，例如：

   ![输入 HTTP Webhook 操作参数](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   在运行时，逻辑应用在运行此操作时调用订阅终结点。 逻辑应用随后会暂停工作流，并等待目标服务将 `HTTP POST` 请求发送到回调 URL。 如果操作成功完成，则操作将从终结点取消订阅，逻辑应用会继续运行工作流。

1. 若要添加其他可用参数，请打开 "**添加新参数**" 列表，然后选择所需的参数。

   有关可用于 HTTP Webhook 的身份验证类型的详细信息，请参阅[将身份验证添加到出站调用](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 完成后，请记得保存逻辑应用。 在设计器工具栏上，选择“保存”。

## <a name="connector-reference"></a>连接器参考

有关触发器和操作参数的详细信息，请参阅[HTTP Webhook 参数](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)。

### <a name="output-details"></a>输出详细信息

下面是有关从 HTTP Webhook 触发器或操作返回的输出的详细信息：

| 属性名称 | 类型 | Description |
|---------------|------|-------------|
| headers | 对象 | 请求中的标头 |
| body | 对象 | JSON 对象 | 具有来自请求的正文内容的对象 |
| 状态代码 | int | 请求的状态代码 |
|||

| 状态代码 | Description |
|-------------|-------------|
| 200 | 确定 |
| 202 | 已接受 |
| 400 | 错误的请求 |
| 401 | 未授权 |
| 403 | 禁止 |
| 404 | 未找到 |
| 500 | 内部服务器错误。 发生未知错误。 |
|||

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
