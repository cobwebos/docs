---
title: 对平面文件进行编码或解码
description: 使用 Enterprise Integration Pack 对 Azure 逻辑应用中用于企业集成的平面文件进行编码或解码
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 05/09/2020
ms.openlocfilehash: aebce8f284ed4bb21d99efffc8dd6d0c51b39533
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87001479"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps-by-using-the-enterprise-integration-pack"></a>使用 Enterprise Integration Pack 对 Azure 逻辑应用中的平面文件进行编码或解码

在企业对企业 (B2B) 方案中将 XML 内容发送给业务合作伙伴之前，你可能希望先对该内容进行编码。 通过构建逻辑应用，你可以使用**平面文件**连接器对平面文件进行编码和解码。 逻辑应用可以从各种源（例如请求触发器、其他应用，或其他[由 Azure 逻辑应用支持的连接器](../connectors/apis-list.md)）获取此 XML 内容。 有关详细信息，请参阅[什么是 Azure 逻辑应用？](logic-apps-overview.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 你要在其中使用**平面文件**连接器的逻辑应用，以及用于启动逻辑应用工作流的触发器。 **平面文件**连接器只提供操作，不提供触发器。 可以使用触发器或其他操作将 XML 内容送入逻辑应用进行编码或解码。 如果不熟悉逻辑应用，请查看[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 一个[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，该帐户与你的 Azure 订阅相关联，并[已关联到逻辑应用](./logic-apps-enterprise-integration-create-integration-account.md#link-account)（你计划在其中使用**平面文件**连接器）。 逻辑应用和集成帐户必须位于同一位置或 Azure 区域。

* 已上传到集成帐户的平面文件[架构](logic-apps-enterprise-integration-schemas.md)，用于对 XML 内容进行编码或解码

* 已在集成帐户中定义的至少两个[贸易合作伙伴](logic-apps-enterprise-integration-partners.md)

## <a name="add-flat-file-encode-action"></a>添加平面文件编码操作

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 在你的逻辑应用中的触发器或操作下，选择“新建步骤” > “添加操作”。  此示例使用“请求”触发器，该触发器被命名为“收到 HTTP 请求时”，用于处理来自逻辑应用外部的入站请求。

   > [!TIP]
   > 提供 JSON 架构是可选的操作。 如果你有来自入站请求的示例有效负载，请选择“使用示例有效负载生成架构”，输入示例有效负载，然后选择“完成”。 架构将显示在“请求正文 JSON 架构”框中。

1. 在“选择操作”下，输入 `flat file`。 在操作列表中选择此操作：**平面文件编码**

   ![选择“平面文件编码”操作](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-encoding.png)

1. 在“内容”框中单击，以显示动态内容列表。 从列表的“收到 HTTP 请求时”部分选择“正文”属性，其中包含来自触发器的请求正文输出和要编码的内容。

   ![从动态内容列表中选择要编码的内容](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode.png)

   > [!TIP]
   > 如果在动态内容列表中未看到“正文”属性，请选择“收到 HTTP 请求时”节标签旁边的“查看更多”。 
   > 还可以直接在“内容”框中输入要解码的内容。

1. 从“架构名称”列表中，选择你的关联集成帐户中要用于编码的架构，例如：

   ![选择要用于编码的架构](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-encoding.png)

   > [!NOTE]
   > 如果列表中未显示任何架构，则表明你的集成帐户未包含任何可用于编码的架构文件。 请将你要使用的架构上传到你的集成帐户。

1. 保存逻辑应用。 若要测试连接器，请向 HTTPS 终结点（显示在“请求”触发器的“HTTP POST URL”属性中）发出一个请求，并在请求正文中包含要编码的 XML 内容。

你现在已经完成了平面文件编码操作的设置。 在实际应用中，你可能需要将已编码的数据存储在业务线 (LOB) 应用（如 Salesforce）中。 你也可以将已编码的数据发送给贸易合作伙伴。 若要将编码操作的输出发送到 Salesforce 或贸易合作伙伴，请使用其他[在 Azure 逻辑应用中提供的连接器](../connectors/apis-list.md)。

## <a name="add-flat-file-decode-action"></a>添加平面文件解码操作

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 在你的逻辑应用中的触发器或操作下，选择“新建步骤” > “添加操作”。  此示例使用“请求”触发器，该触发器被命名为“收到 HTTP 请求时”，用于处理来自逻辑应用外部的入站请求。

   > [!TIP]
   > 提供 JSON 架构是可选的操作。 如果你有来自入站请求的示例有效负载，请选择“使用示例有效负载生成架构”，输入示例有效负载，然后选择“完成”。 架构将显示在“请求正文 JSON 架构”框中。

1. 在“选择操作”下，输入 `flat file`。 在操作列表中选择此操作：**平面文件解码**

   ![选择“平面文件解码”操作](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-decoding.png)

1. 在“内容”框中单击，以显示动态内容列表。 从列表的“收到 HTTP 请求时”部分选择“正文”属性，其中包含来自触发器的请求正文输出和要解码的内容。

   ![从动态内容列表中选择要解码的内容](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode.png)

   > [!TIP]
   > 如果在动态内容列表中未看到“正文”属性，请选择“收到 HTTP 请求时”节标签旁边的“查看更多”。  还可以直接在“内容”框中输入要解码的内容。

1. 从“架构名称”列表中，选择你的关联集成帐户中要用于解码的架构，例如：

   ![选择要用于解码的架构](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-decoding.png)

   > [!NOTE]
   > 如果列表中未显示任何架构，则表明你的集成帐户未包含任何可用于解码的架构文件。 请将你要使用的架构上传到你的集成帐户。

1. 保存逻辑应用。 若要测试连接器，请向 HTTPS 终结点（显示在“请求”触发器的“HTTP POST URL”属性中）发出一个请求，并在请求正文中包含要解码的 XML 内容。

你现在已经完成了平面文件解码操作的设置。 在实际应用中，你可能需要将已解码的数据存储在业务线 (LOB) 应用（如 Salesforce）中。 你也可以将已解码的数据发送给贸易合作伙伴。 若要将解码操作的输出发送到 Salesforce 或贸易合作伙伴，请使用其他[在 Azure 逻辑应用中提供的连接器](../connectors/apis-list.md)。

## <a name="next-steps"></a>后续步骤

* 详细了解 [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
