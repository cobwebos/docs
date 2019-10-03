---
title: 在 Azure 逻辑应用中创建基于事件的任务和工作流
description: 触发、 暂停和恢复自动化的任务、 流程和基于使用 Azure 逻辑应用终结点处发生的事件的工作流
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: c2658df185d4836210c496d2c46a00a3541257a2
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541363"
---
# <a name="automate-event-based-tasks-and-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>在 Azure 逻辑应用中使用 HTTP webhook 自动执行基于事件的任务和工作流

与[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和内置 HTTP Webhook 连接器，你可以自动执行等待和基于通过构建逻辑应用的 HTTP 或 HTTPS 终结点处发生的特定事件运行的工作流。 例如，可以创建一个逻辑应用，通过触发工作流和运行指定的操作，而不是定期检查之前等待特定事件来监视服务终结点或*轮询*该终结点。

下面是一些示例基于事件的工作流：

* 等待的项从到达[Azure 事件中心](https://github.com/logicappsio/EventHubAPI)之前触发逻辑应用运行。
* 等待获得批准才能继续工作流。

## <a name="how-do-webhooks-work"></a>Webhook 是如何工作的？

HTTP webhook 触发器是基于事件的这并不依赖于检查或定期轮询的新项。 保存逻辑应用 webhook 触发器，以开始或者更改逻辑应用从禁用启用 webhook 触发器时*订阅*到特定服务或终结点通过注册*回调 URL*与该服务或终结点。 然后，该触发器等待该服务或终结点为调用的 URL，开始运行逻辑应用。 类似于[请求触发器](connectors-native-reqres.md)，立即在指定的事件发生时，会触发逻辑应用。 触发器*取消订阅*从服务或终结点删除的触发器和保存逻辑应用，如果更改从逻辑应用时启用或为已禁用。

HTTP webhook 操作也是基于事件的并*订阅*到特定服务或通过注册的终结点*回调 URL*与该服务或终结点。 Webhook 操作暂停逻辑应用工作流，并将一直等待直到服务或终结点调用逻辑应用继续运行之前的 URL。 操作逻辑应用*取消订阅*从服务或在这些情况下的终结点：

* Webhook 操作成功完成
* 如果等待响应时取消逻辑应用运行
* 之前的逻辑应用超时

例如，Office 365 Outlook 连接器[**发送审批电子邮件**](connectors-create-api-office365-outlook.md)操作是采用这种模式的 webhook 操作的示例。 使用 webhook 操作，您可以将此模式扩展到任何服务。

有关详细信息，请参阅以下主题：

* [HTTP Webhook 触发器参数](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhook 和订阅](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [创建支持 webhook 的自定义 Api](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 已部署的终结点的 URL 或 API，支持 webhook 订阅和取消订阅模式[逻辑应用中的 webhook 触发器](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)或[在逻辑应用中的 webhook 操作](../logic-apps/logic-apps-create-api-app.md#webhook-actions)根据

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

* 想要等待的时间在目标终结点上的特定事件的逻辑应用。 若要使用 HTTP Webhook 触发器，启动[创建空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 HTTP Webhook 操作，请使用所需的任何触发器启动逻辑应用。 此示例使用 HTTP 触发器的第一步。

## <a name="add-an-http-webhook-trigger"></a>添加 HTTP Webhook 触发器

此内置触发器与指定的服务注册回调 URL，并等待该服务将 HTTP POST 请求发送到该 URL。 此事件发生时，触发器触发并立即运行逻辑应用。

1. 登录到 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开空白逻辑应用。

1. 在设计器中，搜索框中，作为筛选器中输入"http webhook"。 从**触发器**列表中，选择**HTTP Webhook**触发器。

   ![选择 HTTP Webhook 触发器](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   此示例将"HTTP Webhook 触发器"的触发器，以便具有更具描述性的名称。 此外，示例更高版本将添加 HTTP Webhook 操作，并且这两个名称必须是唯一。

1. 提供的值[HTTP Webhook 触发器参数](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)你想要用于订阅和取消订阅调用，例如：

   ![输入 HTTP Webhook 触发器参数](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. 若要添加其他可用参数，请打开**添加新参数**列表，然后选择所需的参数。

   有关可用的身份验证类型为 HTTP Webhook 的详细信息，请参阅[进行身份验证的 HTTP 触发器和操作](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)。

1. 继续使用触发器激发时运行的操作生成逻辑应用的工作流。

1. 完成后，完成后，请记住保存逻辑应用。 在设计器工具栏上，选择**保存**。

   正在保存逻辑应用调用订阅终结点并注册回调 URL 用于触发此逻辑应用。

1. 现在，只要目标服务发送`HTTP POST`请求到回调 URL，逻辑应用就会激发，并且包括任何传递请求的数据。

## <a name="add-an-http-webhook-action"></a>添加 HTTP Webhook 操作

此内置操作使用指定的服务注册回调 URL、 暂停逻辑应用的工作流，并等待该服务将 HTTP POST 请求发送到该 URL。 此事件发生时，该操作将继续运行逻辑应用。

1. 登录到 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开逻辑应用。

   此示例的第一步使用 HTTP Webhook 触发器。

1. 在下，你想要添加 HTTP Webhook 操作步骤中，选择**新步骤**。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 选择加号 ( **+** ) 的出现，并选择**添加操作**。

1. 在设计器中，搜索框中，作为筛选器中输入"http webhook"。 从**操作**列表中，选择**HTTP Webhook**操作。

   ![选择 HTTP Webhook 操作](./media/connectors-native-webhook/select-http-webhook-action.png)

   此示例中重命名为"HTTP Webhook 操作"的操作，以便具有更具描述性的名称。

1. HTTP Webhook 操作参数，类似于提供的值[HTTP Webhook 触发器参数](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger)你想要用于订阅和取消订阅调用，例如：

   ![输入 HTTP Webhook 操作参数](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   在运行时，逻辑应用调用订阅终结点，运行此操作时。 逻辑应用，然后暂停工作流并等待目标服务以发送`HTTP POST`到回调 URL 的请求。 如果操作成功完成，该操作的取消订阅从终结点，并且恢复逻辑应用运行工作流。

1. 若要添加其他可用参数，请打开**添加新参数**列表，然后选择所需的参数。

   有关可用的身份验证类型为 HTTP Webhook 的详细信息，请参阅[进行身份验证的 HTTP 触发器和操作](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)。

1. 完成后，请记住保存逻辑应用。 在设计器工具栏上，选择**保存**。

## <a name="connector-reference"></a>连接器参考

详细了解触发器和操作的参数，分别彼此相似，请参阅[HTTP Webhook 参数](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger)。

### <a name="output-details"></a>输出详细信息

下面是有关从 HTTP Webhook 触发器或操作，它将返回此信息的输出的详细信息：

| 属性名称 | Type | 描述 |
|---------------|------|-------------|
| headers | object | 从请求标头 |
| body | object | JSON 对象 | 具有从请求的正文内容的对象 |
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
