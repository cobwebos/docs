---
title: 用于 B2B 企业集成方案的交换消息
description: 使用 Enterprise Integration Pack 在 Azure 逻辑应用中的贸易合作伙伴之间接收和发送 B2B 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 01b2bd464db51e255930fe83a3f4321687322275
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77151069"
---
# <a name="receive-and-send-b2b-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>使用 Azure 逻辑应用和 Enterprise Integration Pack 接收和发送 B2B 消息

如果你有一个定义贸易合作伙伴和协议的集成帐户，则可以创建一个自动企业到企业（B2B）工作流，用于在贸易合作伙伴之间交换消息，方法是将[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)与[Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)结合使用。 Azure 逻辑应用适用于支持 AS2、X12、EDIFACT 和 RosettaNet 行业标准协议的连接器。 你还可以将这些连接器与[逻辑应用中提供](../connectors/apis-list.md)的其他连接器（例如 Salesforce 和 Office 365 Outlook）结合使用。

本文介绍如何创建一个逻辑应用，以便通过使用请求触发器来接收 HTTP 请求，使用 AS2 和 X12 操作对消息内容进行解码，然后使用响应操作返回响应。

## <a name="prerequisites"></a>必备条件

* Azure 订阅。 如果还没有订阅，请[注册免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 空白逻辑应用，以便可以使用后跟以下操作的[Request](../connectors/connectors-native-reqres.md)触发器来创建 B2B 工作流：

  * [AS2 解码](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [条件](../logic-apps/logic-apps-control-flow-conditional-statement.md)，它根据 AS2 解码操作是成功还是失败发送[响应](../connectors/connectors-native-reqres.md)

  * [解码 X12 消息](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  如果不熟悉逻辑应用，请参阅[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 与 Azure 订阅关联并链接到逻辑应用的[集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)。 逻辑应用和集成帐户必须存在于同一位置或 Azure 区域中。

* 在集成帐户中至少定义了两个[贸易合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)，其中包含这些合作伙伴的[AS2 和 X12 协议](logic-apps-enterprise-integration-agreements.md)。

## <a name="add-request-trigger"></a>添加请求触发器

此示例使用 Azure 门户中的逻辑应用设计器，但可以在 Visual Studio 中针对逻辑应用设计器执行类似的步骤。

1. 在[Azure 门户](https://portal.azure.com)中，打开逻辑应用设计器中的空白逻辑应用。

1. 在搜索框中，输入 `when a http request`，并选择**接收 HTTP 请求**以用作触发器的时间。

   ![选择 "请求触发器" 启动逻辑应用工作流](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. 将 "**请求正文 JSON 架构**" 框留空，因为 X12 消息为平面文件。

   ![保留 "请求正文 JSON 架构" 为空](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. 完成后，请在设计器工具栏上选择 "**保存**"。

   此步骤将生成用于发送触发逻辑应用的请求的**HTTP POST URL** 。 若要复制此 URL，请选择 URL 旁边的 "复制" 图标。

   ![为请求触发器生成的用于接收调用的 URL](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-as2-decode-action"></a>添加 AS2 解码操作

现在添加要使用的 B2B 操作。 此示例使用 AS2 和 X12 操作。

1. 在触发器下，选择“新建步骤”。 若要隐藏触发器的详细信息，请单击触发器的标题栏。

   ![向逻辑应用工作流添加另一个步骤](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. 在 "**选择操作**" 下的搜索框中，输入 `as2 decode`，然后选择 " **AS2 解码（v2）** "。

   ![查找并选择 "AS2 解码（v2）"](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. 对于 "**要解码的消息**" 属性，请输入希望 AS2 操作进行解码的输入，该输入是 HTTP 请求触发器收到的 `body` 内容。 可以通过多种方式将此内容指定为动态内容列表或表达式中的输入：

   * 若要从显示可用触发器输出的列表中进行选择，请在 "**要解码的消息**" 框中单击。 显示动态内容列表后，在**收到 HTTP 请求时**，选择 "**正文**" 属性值，例如：

     ![从触发器中选择 "正文" 值](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * 若要输入引用触发器 `body` 输出的表达式，请在 "**要解码的消息**" 框中单击。 显示动态内容列表后，选择 "**表达式**"。 在 "表达式编辑器" 中，输入表达式，然后选择 **"确定"** ：

     `triggerOutputs()['body']`

     或者，在 "**要解码的消息**" 框中，直接输入以下表达式：

     `@triggerBody()`

     表达式解析为**正文**标记。

     ![从触发器解析的正文输出](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. 对于 "**消息标头**" 属性，请输入 AS2 操作所需的任何标头，该操作由 HTTP 请求触发器收到 `headers` 内容描述。

   若要输入一个表达式，该表达式引用触发器的 `headers` 输出，请在 "**消息标题**" 框内单击。 显示动态内容列表后，选择 "**表达式**"。 在 "表达式编辑器" 中，输入表达式，然后选择 **"确定"** ：

   `triggerOutputs()['Headers']`

   若要使此表达式解析为此标记，请在设计器和代码视图之间切换，例如：

   ![从触发器解析的标头输出](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>为消息回执通知添加响应操作

若要通知贸易合作伙伴收到消息，可以使用**响应**操作返回包含 AS2 消息处置通知（MDN）的响应。 通过在**AS2 解码**操作后立即添加此操作，如果操作失败，则逻辑应用不会继续处理。

1. 在**AS2 解码**操作下，选择 "**新建步骤**"。

1. 在 "**选择操作**" 下的搜索框下，选择 "**内置**"。 在搜索框中输入 `condition`。 从 "**操作**" 列表中，选择 "**条件**"。

   ![添加 "条件" 操作](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   此时将显示条件形状，包括是否满足条件的路径。

   ![具有决策路径的条件形状](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. 现在，请指定要计算的条件。 在 "**选择值**" 框中，输入以下表达式：

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   在中间框中，确保比较操作设置为 `is equal to`。 在右侧框中，输入 `Expected`的值。 若要使表达式解析为此标记，请在设计器和代码视图之间切换。

   ![具有决策路径的条件形状](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. 现在指定响应以返回**AS2 解码**操作是否成功。

   1. 对于**AS2 解码**操作成功的情况，请在 "**如果为 true** " 形状中选择 "**添加操作**"。 在 "**选择操作**" 下的搜索框中，输入 `response`，然后选择 "**响应**"。

      ![查找并选择 "响应" 操作](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. 若要从**As2 解码**操作的输出访问 as2 MDN，请指定以下表达式：

      * 在**响应**操作的 "**标头**" 属性中，输入以下表达式：

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * 在**响应**操作的 "**正文**" 属性中，输入以下表达式：

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. 若要使表达式解析为标记，请在设计器和代码视图之间切换：

      ![解析的表达式以访问 AS2 MDN](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. 对于**AS2 解码**操作失败的情况，请在 "**如果错误**" 形状中选择 "**添加操作**"。 在 "**选择操作**" 下的搜索框中，输入 `response`，然后选择 "**响应**"。 设置**响应**操作以返回所需的状态和错误。

1. 保存逻辑应用。

## <a name="add-decode-x12-message-action"></a>添加解码 X12 消息操作

1. 现在添加 "**解码 X12 消息**" 操作。 在**响应**操作下，选择 "**添加操作**"。

1. 在 "**选择操作**" 下的搜索框中，输入 `x12 decode`，然后选择 "**解码 X12 消息**"。

   ![查找并选择 "解码 X12 消息" 操作](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. 如果 X12 操作提示您输入连接信息，请提供连接的名称，选择要使用的集成帐户，然后选择 "**创建**"。

   ![创建与集成帐户的 X12 连接](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. 现在为 X12 操作指定输入。 此示例使用 AS2 操作的输出，此操作是消息内容，但请注意，此内容是 JSON 对象格式，且是 base64 编码的。 因此，你必须将此内容转换为字符串。

   在 "**要解码的 X12 平面文件消息**" 框中，输入以下表达式来转换 AS2 输出：

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    若要使表达式解析为此标记，请在设计器和代码视图之间切换。

    ![将 base64 编码的内容转换为字符串](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. 保存逻辑应用。

   例如，如果需要此逻辑应用的其他步骤（例如，要解码消息内容并以 JSON 对象格式输出该内容），请继续构建逻辑应用。

你现在已经完成了 B2B 逻辑应用的设置。 在实际应用中，你可能需要将解码的 X12 数据存储在业务线（LOB）应用或数据存储中。 例如，请参阅以下文章：

* [从 Azure 逻辑应用连接到 SAP 系统](../logic-apps/logic-apps-using-sap-connector.md)
* [使用 SSH 和 Azure 逻辑应用监视、创建和管理 SFTP 文件](../connectors/connectors-sftp-ssh.md)

若要连接自己的 LOB 应用并在逻辑应用中使用这些 Api，可以添加更多操作或[编写自定义 api](../logic-apps/logic-apps-create-api-app.md)。

## <a name="next-steps"></a>后续步骤

* [接收和响应传入的 HTTPS 调用](../connectors/connectors-native-reqres.md)
* [交换用于 B2B 企业集成的 AS2 消息](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [交换 B2B 企业集成的 X12 消息](../logic-apps/logic-apps-enterprise-integration-x12.md)
* 详细了解[Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)