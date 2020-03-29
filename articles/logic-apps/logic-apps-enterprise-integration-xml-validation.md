---
title: 验证 XML 进行 B2B 企业集成
description: 使用 Azure 逻辑应用中的架构与企业集成包一起验证 XML
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792166"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>在带有 Enterprise Integration Pack 的 Azure 逻辑应用中验证用于 B2B 企业集成的 XML

通常，在 B2B 方案中，协议中的贸易伙伴需要确保他们交换的消息在开始任何数据处理之前有效。 您可以使用企业集成包提供的 XML 验证操作，根据预定义的架构验证文档。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果您还没有订阅，[请注册一个免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 要使用 XML 验证操作的空白或现有逻辑应用。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 与 Azure 订阅关联的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)链接到计划使用 XML 验证操作的逻辑应用，并包含要用于验证 XML 内容的架构。 逻辑应用和集成帐户必须存在于同一位置或 Azure 区域中。

## <a name="add-xml-validation-action"></a>添加 XML 验证操作

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 如果在"逻辑应用设计器"中，在搜索框中有一个空白逻辑应用，请`HTTP request`输入为筛选器，然后选择 **"何时收到 HTTP 请求"** 触发器。 否则，继续执行下一步。

1. 在工作流的最后一步下，选择 **"新建步骤**"。

   要在现有步骤之间添加操作，请将指针移到连接这些步骤的箭头上，以便出现加号 （**+**）。 选择该加号，然后选择 **"添加操作**"。

1. 在“选择操作”下，选择“内置”。******** 在搜索框中，输入 `xml validation` 作为筛选器。 从操作列表中，选择**XML 验证**。

   ![查找并选择"XML 验证"操作](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. 要指定要验证的 XML 内容，请在 **"内容"** 框中单击，以便显示动态内容列表。

   ![打开动态内容列表](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   动态内容列表显示表示工作流中以前步骤的输出的属性令牌。 如果列表未显示预期属性，请检查触发器或操作标题，是否可以选择 **"查看更多**"。

1. 从动态内容列表中选择具有要验证的内容的属性。

   本示例从触发器中选择 **"正文**"输出。

   ![选择要验证的内容](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. 要指定要用于验证的架构，请打开**架构名称**列表，然后选择添加到链接集成帐户的验证架构。

   ![选择要用于验证的架构](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. 保存逻辑应用。

   现在，您已完成设置验证。 在真实应用中，您可能希望将验证的数据存储在业务线 （LOB） 应用中（如 SalesForce）。 要将已验证的输出发送到 Salesforce，请添加操作。

1. 要测试验证操作，可以发送请求以触发逻辑应用的工作流。

## <a name="next-steps"></a>后续步骤

* 了解有关[企业集成包的更多](../logic-apps/logic-apps-enterprise-integration-overview.md)