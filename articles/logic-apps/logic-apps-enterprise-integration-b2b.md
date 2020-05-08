---
title: 交换 B2B 企业集成方案的消息
description: 使用 Enterprise Integration Pack 在 Azure 逻辑应用中的贸易合作伙伴之间接收和发送 B2B 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: b576fc99e2f203bb3d690a8135ee76cee26b3de8
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792354"
---
# <a name="receive-and-confirm--b2b-as2-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>使用 Azure 逻辑应用和 Enterprise Integration Pack 接收和确认 B2B AS2 消息

如果你的集成帐户定义了贸易合作伙伴和协议，则你可以创建一个自动化的企业到企业 (B2B) 工作流，用于通过 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和 [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) 在贸易合作伙伴之间交换消息。 Azure 逻辑应用使用支持 AS2、X12、EDIFACT 和 RosettaNet 行业标准协议的连接器。 还可以将这些连接器与[逻辑应用中提供的其他 连接器](../connectors/apis-list.md)（例如 Salesforce 和 Office 365 Outlook）结合使用。

本文介绍如何创建一个逻辑应用，用于通过“请求”触发器接收 HTTP 请求、通过 AS2 和 X12 操作解码消息内容，然后通过“响应”操作返回响应。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有订阅，请[注册免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 准备一个空白逻辑应用，以便可以使用后接以下操作的[请求](../connectors/connectors-native-reqres.md)触发器创建 B2B 工作流：

  * [AS2 解码](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [条件](../logic-apps/logic-apps-control-flow-conditional-statement.md)，它根据 AS2 解码操作是成功还是失败发送[响应](../connectors/connectors-native-reqres.md)

  * [解码 X12 消息](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 与 Azure 订阅相关联并已链接到逻辑应用的[集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)。 逻辑应用和集成帐户必须位于同一位置或 Azure 区域。

* 已在集成帐户中至少定义了两个[贸易合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)，并为这些合作伙伴创建了 [AS2 和 X12 协议](logic-apps-enterprise-integration-agreements.md)。

## <a name="add-request-trigger"></a>添加请求触发器

本示例使用 Azure 门户中的逻辑应用设计器，但你可以在 Visual Studio 中执行与逻辑应用设计器类似的步骤。

1. 在 [Azure 门户](https://portal.azure.com)中，在逻辑应用设计器中打开你的空白逻辑应用。

1. 在搜索框中输入 `when a http request`，然后选择“收到 HTTP 请求时”作为触发器。 

   ![选择“请求”触发器以启动逻辑应用工作流](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. 将“请求正文 JSON 架构”框留空，因为 X12 消息是平面文件。 

   ![将“请求正文 JSON 架构”留空](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. 完成后，请在设计器工具栏上选择“保存”  。

   此步骤生成 HTTP POST URL，用于发送触发逻辑应用的请求。  若要复制此 URL，请选择 URL 旁边的复制图标。

   ![为“请求”触发器生成的用于接收调用的 URL](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-as2-decode-action"></a>添加 AS2 解码操作

现在请添加要使用的 B2B 操作。 本示例使用 AS2 和 X12 操作。

1. 在触发器下，选择“新建步骤”。  若要隐藏触发器详细信息，请单击触发器的标题栏。

   ![将另一个步骤添加到逻辑应用工作流](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. 在“选择操作”下的搜索框中输入 `as2 decode`，然后选择“AS2 解码(v2)”。  

   ![找到并选择“AS2 解码(v2)”](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. 对于“要解码的消息”属性，请输入要让 AS2 操作解码的输入，即，HTTP 请求触发器收到的 `body` 内容。  可通过动态内容列表或使用表达式，以多种方式将此内容指定为输入：

   * 若要从显示可用触发器输出的列表中进行选择，请在“要解码的消息”框中单击。  显示动态内容列表后，在“收到 HTTP 请求时”下，选择“正文”属性值，例如：  

     ![从触发器中选择“正文”值](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * 若要输入引用触发器的 `body` 输出的表达式，请在“要解码的消息”框中单击。  显示动态内容列表后，选择“表达式”。  在表达式编辑器中输入表达式，然后选择“确定”： 

     `triggerOutputs()['body']`

     或者，在“要解码的消息”框中直接输入以下表达式： 

     `@triggerBody()`

     表达式将解析为“正文”标记。 

     ![解析触发器的正文输出](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. 对于“消息标头”属性，请输入 AS2 操作所需的任何标头，这些标头由 HTTP 请求触发器收到的 `headers` 内容进行描述。 

   若要输入引用触发器的 `headers` 输出的表达式，请在“消息标头”框中单击。  显示动态内容列表后，选择“表达式”。  在表达式编辑器中输入表达式，然后选择“确定”： 

   `triggerOutputs()['Headers']`

   若要使此表达式解析为此标记，请在设计器视图与代码视图之间进行切换，例如：

   ![已解析触发器的标头输出](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>为消息回执通知添加响应操作

若要通知贸易合作伙伴已收到消息，可以使用“响应”操作返回包含 AS2 消息处置通知 (MDN) 的响应。  如果紧接在“AS2 解码”操作后面添加此操作，当此操作失败时，逻辑应用不会继续进行处理。 

1. 在“AS2 解码”操作下，选择“新建步骤”。  

1. 在“选择操作”下的搜索框下，选择“内置”。   在搜索框中输入 `condition`。 在“操作”列表中选择“条件”。  

   ![添加“条件”操作](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   此时会显示条件形状，包括有关是否满足条件的路径。

   ![包含决策路径的条件形状](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. 现在请指定要评估的条件。 在“选择值”框中输入以下表达式： 

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   在中间框中，确保比较操作设置为 `is equal to`。 在右侧框中输入 `Expected` 值。 若要使该表达式解析为此标记，请在设计器视图与代码视图之间进行切换。

   ![包含决策路径的条件形状](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. 现在，请指定用于返回“AS2 解码”操作是否成功的响应。 

   1. 如果“AS2 解码”操作成功，请在“If true”形状中选择“添加操作”。    在“选择操作”下的搜索框中输入 `response`，然后选择“响应”。  

      ![找到并选择“响应”操作](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. 若要从“AS2 解码”操作的输出访问 AS2 MDN，请指定以下表达式： 

      * 在“响应”操作的“标头”属性中输入以下表达式：  

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * 在“响应”操作的“正文”属性中输入以下表达式：  

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. 若要使该表达式解析为标记，请在设计器视图与代码视图之间进行切换：

      ![已解析表达式来访问 AS2 MDN](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. 如果“AS2 解码”操作失败，请在“If false”形状中选择“添加操作”。    在“选择操作”下的搜索框中输入 `response`，然后选择“响应”。   设置“响应”操作以返回所需的状态和错误。 

1. 保存逻辑应用。

## <a name="add-decode-x12-message-action"></a>添加“解码 X12 消息”操作

1. 现在添加“解码 X12 消息”操作。  在“响应”操作下，选择“添加操作”。  

1. 在“选择操作”下的搜索框中输入 `x12 decode`，然后选择“解码 X12 消息”。  

   ![找到并选择“解码 X12 消息”操作](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. 如果 X12 操作提示输入连接信息，请提供连接的名称，选择要使用的集成帐户，然后选择“创建”。 

   ![与集成帐户建立 X12 连接](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. 现在请指定 X12 操作的输入。 本示例使用 AS2 操作的输出（消息内容），但请注意，此内容采用 JSON 对象格式且已经过 base64 编码。 因此，必须将此内容转换为字符串。

   在“要解码的 X12 平面文件消息”框中，输入以下表达式来转换 AS2 输出： 

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    若要使该表达式解析为此标记，请在设计器视图与代码视图之间进行切换。

    ![将 base64 编码的内容转换为字符串](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. 保存逻辑应用。

   如果需要在此逻辑应用中添加其他步骤（例如，解码消息内容并以 JSON 对象格式输出该内容），请继续生成逻辑应用。

现已完成 B2B 逻辑应用的设置。 在真实应用中，可能需要在业务线 (LOB) 应用或数据存储中存储解码的 X12 数据。 有关示例，请参阅以下文章：

* [从 Azure 逻辑应用连接到 SAP 系统](../logic-apps/logic-apps-using-sap-connector.md)
* [使用 SSH 与 Azure 逻辑应用监视、创建和管理 SFTP 文件](../connectors/connectors-sftp-ssh.md)

若要连接自己的 LOB 应用并在逻辑应用中使用这些 API，可以添加其他操作或[编写自定义 API](../logic-apps/logic-apps-create-api-app.md)。

## <a name="next-steps"></a>后续步骤

* [接收和响应传入的 HTTPS 调用](../connectors/connectors-native-reqres.md)
* [交换用于 B2B 企业集成的 AS2 消息](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [交换用于 B2B 企业集成的 X12 消息](../logic-apps/logic-apps-enterprise-integration-x12.md)
* 详细了解 [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
