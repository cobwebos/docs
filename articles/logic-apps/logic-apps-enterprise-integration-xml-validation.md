---
title: 验证 B2B 企业集成的 XML
description: 在带有 Enterprise Integration Pack 的 Azure 逻辑应用中使用架构验证 XML
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "74792166"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>在带有 Enterprise Integration Pack 的 Azure 逻辑应用中验证用于 B2B 企业集成的 XML

通常在 B2B 方案中，协议中的贸易合作伙伴需确保他们交换的消息有效，才能开始处理数据。 可以使用 XML 验证操作（随 Enterprise Integration Pack 提供）按照预定义的架构验证文档。

## <a name="prerequisites"></a>必备条件

* Azure 订阅。 如果还没有订阅，请[注册免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 要在其中使用 XML 验证操作的空白或现有逻辑应用。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 一个[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，该帐户与 Azure 订阅相关联，并已关联到你计划在其中使用 XML 验证操作的逻辑应用，并包含需要用于验证 XML 内容的架构。 逻辑应用和集成帐户必须位于同一位置或 Azure 区域。

## <a name="add-xml-validation-action"></a>添加 XML 验证操作

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 如果有空白逻辑应用，请在逻辑应用设计器的搜索框中输入 `HTTP request` 作为筛选器，并选择“收到 HTTP 请求时”  触发器。 否则，继续执行下一步。

1. 在工作流的最后一步，选择“新建步骤”  。

   若要在现有步骤之间添加操作，请将指针移到连接这些步骤的箭头上方，以便显示加号 ( **+** )。 选择该加号，然后选择“添加操作”。 

1. 在“选择操作”下，选择“内置”。   在搜索框中，输入 `xml validation` 作为筛选器。 从操作列表中选择“XML 验证”  。

   ![找到并选择“XML 验证”操作](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. 若要指定要验证的 XML 内容，请单击“内容”框以显示动态内容列表。 

   ![打开动态内容列表](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   动态内容列表显示了属性标记，这些标记表示工作流中前面步骤的输出。 如果列表不显示预期的属性，请检查触发器或操作标题，确定是否可以选择“查看更多”  。

1. 从动态内容列表中，选择包含要验证的内容的属性。

   此示例从触发器中选择“正文”输出。 

   ![选择要验证的内容](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. 若要指定要用于验证的架构，请打开“架构名称”  列表，选择已添加到关联的集成帐户的验证架构。

   ![选择要用于验证的架构](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. 保存逻辑应用。

   现在已完成验证设置。 在实际应用中，可能需要将已验证的数据存储在业务线 (LOB) 应用（如 SalesForce）中。 要将已验证的输出发送到 Salesforce，请添加操作。

1. 若要测试验证操作，可以发送一个请求来触发逻辑应用的工作流。

## <a name="next-steps"></a>后续步骤

* 详细了解 [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)