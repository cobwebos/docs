---
title: 验证用于 B2B 企业集成的 XML
description: 通过在 Azure 逻辑应用中使用架构来验证 XML Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792166"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>在带有 Enterprise Integration Pack 的 Azure 逻辑应用中验证用于 B2B 企业集成的 XML

通常在 B2B 方案中，协议中的贸易合作伙伴需要确保其交换的消息在任何数据处理开始之前都有效。 您可以使用 "XML 验证" 操作（随 Enterprise Integration Pack 一起提供）针对预定义的架构验证文档。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果还没有订阅，请[注册免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 要在其中使用 XML 验证操作的空白或现有逻辑应用。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 与 Azure 订阅关联的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)链接到计划使用 xml 验证操作的逻辑应用，并包含要用于验证 xml 内容的架构。 逻辑应用和集成帐户必须存在于同一位置或 Azure 区域中。

## <a name="add-xml-validation-action"></a>添加 XML 验证操作

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 如果有空白逻辑应用，请在逻辑应用设计器的搜索框中，输入 `HTTP request` 作为筛选器，并选择 "**收到 HTTP 请求时**" 触发器。 否则，继续执行下一步。

1. 在工作流的最后一步中，选择 "**新建步骤**"。

   若要在现有步骤之间添加操作，请将指针移到连接这些步骤的箭头上方，以便显示加号（ **+** ）。 选择该加号，然后选择 "**添加操作**"。

1. 在“选择操作”下，选择“内置”。 在搜索框中，输入 `xml validation` 作为筛选器。 从 "操作" 列表中，选择 " **XML 验证**"。

   ![查找并选择 "XML 验证" 操作](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. 若要指定要验证的 XML 内容，请在 "**内容**" 框中单击，以便显示 "动态内容" 列表。

   ![打开动态内容列表](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   动态内容列表显示了属性标记，这些标记表示工作流中前面步骤的输出。 如果列表不显示预期的属性，请选中 "触发器" 或 "操作" 标题，确定是否可以选择 "**查看更多**"。

1. 从 "动态内容" 列表中，选择包含要验证的内容的属性。

   此示例选择触发器的**正文**输出。

   ![选择要验证的内容](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. 若要指定要用于验证的架构，请打开 "**架构名称**" 列表，并选择已添加到链接集成帐户的验证架构。

   ![选择要用于验证的架构](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. 保存逻辑应用。

   你现在已经完成了验证设置。 在实际应用中，你可能想要将经过验证的数据存储在业务线（LOB）应用（例如 SalesForce）中。 要将已验证的输出发送到 Salesforce，请添加操作。

1. 若要测试验证操作，可以发送一个请求来触发逻辑应用的工作流。

## <a name="next-steps"></a>后续步骤

* 详细了解[Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)