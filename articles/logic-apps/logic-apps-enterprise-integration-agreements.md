---
title: 创建和管理贸易合作伙伴协议-Azure 逻辑应用
description: 创建和管理通过使用 Azure 逻辑应用和 Enterprise Integration Pack 贸易合作伙伴之间的协议
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 4bfee4ec442c9e7b0351b0fd0c6a2b8e163a2541
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330302"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>创建和管理 Azure 逻辑应用中的贸易合作伙伴协议

一个[贸易合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*协议*可帮助组织和企业无缝地相互通信通过定义要使用交换时的特定行业标准协议企业到企业 (B2B) 消息。 协议提供常见的优势，例如：

* 使组织能够通过使用熟知的格式交换信息。
* 执行 b2b 业务时提高效率。
* 可轻松地创建、 管理和使用用于生成企业集成解决方案。

本文介绍如何创建 AS2、 EDIFACT 或 X12 协议时通过使用构建企业 B2B 方案的集成解决方案，可以使用[Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)和[Azure 逻辑应用](../logic-apps/logic-apps-overview.md). 创建协议后，你可以使用 AS2、 EDIFACT 或 X12 交换 B2B 消息的连接器。

若要创建 RosettaNet 消息交换的协议，请参阅[Exchange RosettaNet 消息](../logic-apps/logic-apps-enterprise-integration-rosettanet.md)。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* [集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)用于存储你的协议和其他 B2B 项目。 此集成帐户必须与你的 Azure 订阅相关联。

* 在至少两台[贸易合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)，已创建集成帐户中。 协议需要主机合作伙伴和来宾合作伙伴。 这两个伙伴都必须使用相同的"业务标识"限定符作为你想要创建，如 AS2、 x12，或 EDIFACT 的协议。

* 可选：你想要使用你的协议和启动逻辑应用的工作流的触发器。 该逻辑应用。 若要只需创建集成帐户和 B2B 项目，不需要的逻辑应用。 但是，逻辑应用集成帐户中使用 B2B 项目之前，你必须将集成帐户链接到逻辑应用。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-agreements"></a>创建协议

1. 登录到 [Azure 门户](https://portal.azure.com)。
在 Azure 主菜单中，选择“所有服务”  。 在搜索框中，输入作为筛选器"集成"。 从结果中，选择此资源：**集成帐户**

   ![查找集成帐户](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. 下**集成帐户**，选择你想要创建协议的集成帐户。

   ![选择要在其中创建协议的集成帐户](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. 在右侧窗格中下,**组件**，选择**协议**磁贴。

   ![选择"协议"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. 在“协议”  下，选择“添加”  。 在中**添加**窗格中，提供有关你的协议的信息，例如：

   ![选择“添加”](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | 属性 | 需要 | Value | 说明 |
   |----------|----------|-------|-------------|
   | **Name** | 是 | <*agreement-name*> | 协议的名称 |
   | **协议类型** | 是 | **AS2**， **X12**，或**EDIFACT** | 你的协议的协议类型。 在创建协议文件时，该文件中的内容必须与协议类型匹配。 | |  
   | **主机合作伙伴** | 是 | <*host-partner-name*> | 主机合作伙伴代表指定的协议的组织 |
   | **主机标识** | 是 | <*host-partner-identifier*> | 主机合作伙伴标识符 |
   | **来宾合作伙伴** | 是 | <*guest-partner-name*> | 托管方代表与管理方进行交易的组织 |
   | **来宾标识** | 是 | <*guest-partner-identifier*> | 来宾合作伙伴的标识符 |
   | **接收设置** | 多种多样 | 多种多样 | 这些属性指定的主机合作伙伴如何接收从来宾合作伙伴协议中的所有传入的消息。 有关详细信息，请参阅相应的协议类型： <p>- [AS2 消息设置](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 消息设置](logic-apps-enterprise-integration-edifact.md) <br>- [X12 消息设置](logic-apps-enterprise-integration-x12.md) |
   | **发送设置** | 多种多样 | 多种多样 | 这些属性指定主机合作伙伴如何将所有传出消息发送到来宾合作伙伴协议中。 有关详细信息，请参阅相应的协议类型： <p>- [AS2 消息设置](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 消息设置](logic-apps-enterprise-integration-edifact.md) <br>- [X12 消息设置](logic-apps-enterprise-integration-x12.md) |
   |||||

1. 完成后在创建你的协议**外**页上，选择**确定**，并返回到集成帐户。

   **协议**列表现在显示在新的协议。

## <a name="edit-agreements"></a>编辑协议

1. 在中[Azure 门户](https://portal.azure.com)，在主 Azure 菜单中，选择**的所有服务**。

1. 在搜索框中，输入作为筛选器"集成"。 从结果中，选择此资源：**集成帐户**

1. 下**集成帐户**，选择包含你想要编辑的协议的集成帐户。

1. 在右侧窗格中下,**组件**，选择**协议**磁贴。

1. 下**协议**，选择你的协议，然后选择**编辑**。

1. 请然后保存所做的更改。

## <a name="delete-agreements"></a>删除协议

1. 在中[Azure 门户](https://portal.azure.com)，在主 Azure 菜单中，选择**的所有服务**。

1. 在搜索框中，输入作为筛选器"集成"。 从结果中，选择此资源：**集成帐户**

1. 下**集成帐户**，选择包含你想要删除的协议的集成帐户。

1. 在右侧窗格中下,**组件**，选择**协议**磁贴。

1. 下**协议**，选择你的协议，然后选择**删除**。

1. 确认要删除所选协议。

## <a name="next-steps"></a>后续步骤

* [交换 AS2 消息](logic-apps-enterprise-integration-as2.md)
* [交换 EDIFACT 消息](logic-apps-enterprise-integration-edifact.md)
* [交换 X12 消息](logic-apps-enterprise-integration-x12.md)
