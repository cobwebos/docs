---
title: 从 Azure 逻辑应用连接到 HTTP 或 HTTPS 终结点
description: 使用 Azure 逻辑应用监视中自动化的任务、 流程和工作流的 HTTP 或 HTTPS 终结点
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: fa5fd3ef8b144826468f56ea2a14be592cef5dc1
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541251"
---
# <a name="call-http-or-https-endpoints-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用调用 HTTP 或 HTTPS 终结点

与[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和内置的 HTTP 连接器，你可以自动执行定期通过构建逻辑应用调用任何 HTTP 或 HTTPS 终结点的工作流。 例如，可以通过检查终结点上指定的计划为你的网站监视的服务终结点。 特定事件发生在该终结点，如你的网站，停止运行时事件触发逻辑应用的工作流，并运行指定的操作。

若要检查或*轮询*终结点按定期计划，您可以使用 HTTP 触发器的第一步工作流中。 每次检查时，该触发器会向该终结点发送调用或请求。  该终结点的响应确定了逻辑应用的工作流是否运行。 触发器将响应中的任何内容传递到逻辑应用中的操作。

可将 HTTP 操作用作工作流中的另一个步骤，用于调用所需的终结点。 该终结点的响应确定了工作流剩余操作的运行方式。

基于目标终结点的功能，HTTP 连接器支持传输层安全性 (TLS) 1.0、 1.1 和 1.2 的版本。 逻辑应用通过使用可能支持的最高版本与终结点协商。 因此，例如，如果终结点支持 1.2 版，则连接器首先使用 1.2 版。 否则，连接器将使用下一个受支持的最高版本。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 你想要调用的目标终结点 URL

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

* 逻辑应用从你想要调用的目标终结点。 若要使用 HTTP 触发器，启动[创建空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 HTTP 操作，请使用所需的任何触发器启动逻辑应用。 此示例使用 HTTP 触发器的第一步。

## <a name="add-an-http-trigger"></a>添加 HTTP 触发器

此内置的触发器对终结点的指定 URL 的 HTTP 调用并返回响应。

1. 登录到 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开空白逻辑应用。

1. 在设计器中，搜索框中，作为筛选器中输入"http"。 从**触发器**列表中，选择**HTTP**触发器。

   ![选择 HTTP 触发器](./media/connectors-native-http/select-http-trigger.png)

   此示例将"HTTP 触发器"的触发器，以便具有更具描述性的名称。 此外，示例更高版本将添加一个 HTTP 操作，并且这两个名称必须是唯一。

1. 提供的值[HTTP 触发器参数](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)想要包含到目标终结点调用中。 设置你希望多久触发器的重复检查目标终结点。

   ![输入 HTTP 触发器参数](./media/connectors-native-http/http-trigger-parameters.png)

   适用于 HTTP 身份验证类型可用的详细信息，请参阅[进行身份验证的 HTTP 触发器和操作](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)。

1. 若要添加其他可用参数，请打开**添加新参数**列表，然后选择所需的参数。

1. 继续使用触发器激发时运行的操作生成逻辑应用的工作流。

1. 完成后，完成后，请记住保存逻辑应用。 在设计器工具栏上，选择**保存**。

## <a name="add-an-http-action"></a>添加 HTTP 操作

此内置操作对指定的终结点的 URL 的 HTTP 调用并返回响应。

1. 登录到 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开逻辑应用。

   此示例使用 HTTP 触发器的第一步。

1. 在下，你想要添加 HTTP 操作步骤中，选择**新步骤**。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 选择加号 ( **+** ) 的出现，并选择**添加操作**。

1. 在设计器中，搜索框中，作为筛选器中输入"http"。 从**操作**列表中，选择**HTTP**操作。

   ![选择“HTTP”操作](./media/connectors-native-http/select-http-action.png)

   此示例中重命名为"HTTP 操作"的操作，以便具有更具描述性的名称。

1. 提供的值[HTTP 操作参数](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)想要包含到目标终结点调用中。

   ![输入 HTTP 操作参数](./media/connectors-native-http/http-action-parameters.png)

   适用于 HTTP 身份验证类型可用的详细信息，请参阅[进行身份验证的 HTTP 触发器和操作](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)。

1. 若要添加其他可用参数，请打开**添加新参数**列表，然后选择所需的参数。

1. 完成后，请记住保存逻辑应用。 在设计器工具栏上，选择**保存**。

## <a name="connector-reference"></a>连接器参考

有关触发器和操作参数的详细信息，请参阅以下部分：

* [HTTP 触发器参数](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)
* [HTTP 操作参数](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)

### <a name="output-details"></a>输出详细信息

下面是有关 HTTP 触发器或操作，返回此信息的输出的详细信息：

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
