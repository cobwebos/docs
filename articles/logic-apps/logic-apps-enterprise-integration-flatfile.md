---
title: 对平面文件进行编码或解码
description: 使用 Enterprise Integration Pack 为 Azure 逻辑应用中的企业集成编码或解码平面文件
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 05/09/2020
ms.openlocfilehash: 81c1c95e2af7b537a12c8c86245b009005aa0aa2
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005379"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps-by-using-the-enterprise-integration-pack"></a>使用 Enterprise Integration Pack 在 Azure 逻辑应用中对平面文件进行编码和解码

在企业到企业（B2B）方案中将 XML 内容发送给业务合作伙伴之前，您可能需要先对该内容进行编码。 通过生成逻辑应用，可以使用**平面文件**连接器对平面文件进行编码和解码。 逻辑应用可以从各种源获取此 XML 内容，例如请求触发器、其他应用或[Azure 逻辑应用支持的其他连接器](../connectors/apis-list.md)。 有关详细信息，请参阅[什么是 Azure 逻辑应用](logic-apps-overview.md)？

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 要在其中使用**平面文件**连接器的逻辑应用，以及用于启动逻辑应用工作流的触发器。 **平面文件**连接器仅提供操作，而不提供触发器。 可以使用触发器或其他操作将 XML 内容送入逻辑应用进行编码或解码。 如果你不熟悉逻辑应用，请查看[快速入门：创建你的第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 与 Azure 订阅关联并链接到计划使用**平面文件**连接器的[逻辑应用](logic-apps-enterprise-integration-accounts.md#link-account)的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 逻辑应用和集成帐户必须存在于同一位置或 Azure 区域中。

* 已上传到集成帐户以便对 XML 内容进行编码或解码的平面文件[架构](logic-apps-enterprise-integration-schemas.md)

* 已在集成帐户中至少定义了两个[贸易合作伙伴](logic-apps-enterprise-integration-partners.md)

## <a name="add-flat-file-encode-action"></a>添加平面文件编码操作

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用中的触发器或操作下，选择 "**新建步骤** > " "**添加操作**"。 此示例使用请求触发器，该触发器在**收到 HTTP 请求时**命名，并处理来自逻辑应用外部的入站请求。

   > [!TIP]
   > 提供 JSON 架构是可选的。 如果有来自入站请求的示例有效负载，请选择 "**使用示例有效负载生成架构**"，输入示例负载，然后选择 "**完成**"。 架构将出现在 "**请求正文 JSON 架构**" 框中。

1. 在 "**选择操作**" 下`flat file`，输入。 从 "操作" 列表中，选择此操作：**平面文件编码**

   ![选择 "平面文件编码" 操作](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-encoding.png)

1. 在**内容**框中单击，以便显示动态内容列表。 从列表中的 "**收到 HTTP 请求时**" 部分，选择 "**正文**" 属性，其中包含触发器的请求正文输出和要编码的内容。

   ![从动态内容列表中选择要编码的内容](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode.png)

   > [!TIP]
   > 如果在 "动态内容" 列表中看不到 "**正文**" 属性，请选择 "**收到 HTTP 请求时**，**查看的详细信息**" 部分标签。
   > 还可以直接在 "**内容**" 框中输入要解码的内容。

1. 从 "**架构名称**" 列表中，选择要用于编码的链接集成帐户中的架构，例如：

   ![选择用于编码的架构](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-encoding.png)

   > [!NOTE]
   > 如果列表中未显示任何架构，则集成帐户不包含任何要用于编码的架构文件。 将想要使用的架构上传到集成帐户。

1. 保存逻辑应用。 若要测试连接器，请向 HTTPS 终结点发出请求，该终结点将出现在请求触发器的 " **HTTP POST URL** " 属性中，并在请求正文中包含要编码的 XML 内容。

你现在已经完成了设置平面文件编码操作的操作。 在实际应用程序中，您可能希望将编码的数据存储在业务线（LOB）应用程序中，如 Salesforce。 或者，您可以将编码的数据发送到贸易合作伙伴。 若要将编码操作的输出发送给 Salesforce 或贸易合作伙伴，请使用[Azure 逻辑应用中提供](../connectors/apis-list.md)的其他连接器。

## <a name="add-flat-file-decode-action"></a>添加平面文件解码操作

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用中的触发器或操作下，选择 "**新建步骤** > " "**添加操作**"。 此示例使用请求触发器，该触发器在**收到 HTTP 请求时**命名，并处理来自逻辑应用外部的入站请求。

   > [!TIP]
   > 提供 JSON 架构是可选的。 如果有来自入站请求的示例有效负载，请选择 "**使用示例有效负载生成架构**"，输入示例负载，然后选择 "**完成**"。 架构将出现在 "**请求正文 JSON 架构**" 框中。

1. 在 "**选择操作**" 下`flat file`，输入。 从 "操作" 列表中，选择此操作：**平面文件解码**

   ![选择 "平面文件解码" 操作](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-decoding.png)

1. 在**内容**框中单击，以便显示动态内容列表。 从列表中的 "**收到 HTTP 请求时**" 部分，选择 "**正文**" 属性，其中包含触发器的请求正文输出和要解码的内容。

   ![从动态内容列表中选择要解码的内容](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode.png)

   > [!TIP]
   > 如果在 "动态内容" 列表中看不到 "**正文**" 属性，请选择 "**收到 HTTP 请求时**，**查看的详细信息**" 部分标签。 还可以直接在 "**内容**" 框中输入要解码的内容。

1. 从 "**架构名称**" 列表中，选择要用于解码的链接集成帐户中的架构，例如：

   ![选择要用于解码的架构](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-decoding.png)

   > [!NOTE]
   > 如果列表中未显示任何架构，则集成帐户不包含用于解码的任何架构文件。 将想要使用的架构上传到集成帐户。

1. 保存逻辑应用。 若要测试连接器，请向 HTTPS 终结点发出请求，该终结点将出现在请求触发器的 " **HTTP POST URL** " 属性中，并在请求正文中包含要解码的 XML 内容。

你现在已经完成了设置平面文件解码操作。 在实际应用中，你可能需要将解码的数据存储在业务线（LOB）应用（例如 Salesforce）中。 或者，您可以将已解码的数据发送到贸易合作伙伴。 若要将解码操作的输出发送给 Salesforce 或贸易合作伙伴，请使用[Azure 逻辑应用中提供](../connectors/apis-list.md)的其他连接器。

## <a name="next-steps"></a>后续步骤

* 详细了解[Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)