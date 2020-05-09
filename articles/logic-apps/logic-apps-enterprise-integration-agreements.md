---
title: 贸易合作伙伴协议
description: 使用 Azure 逻辑应用和 Enterprise Integration Pack 创建并管理贸易合作伙伴之间的协议
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: c8cbfb619c9eed325161503f705bf5c4c0746265
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612327"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>在 Azure 逻辑应用中创建并管理贸易合作伙伴协议

[贸易合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md) 
 ** 协议 (agreement) 通过定义在交换企业对企业 (B2B) 消息时使用的特定的行业标准协议 (protocol)，帮助组织和企业彼此无缝通信。 协议有一些常见的好处，例如：

* 使组织能够以熟知的格式交换信息。
* 可提高进行 B2B 交易的效率。
* 创建和管理起来很容易，并且可以轻松地用来构建企业集成解决方案。

本文介绍如何创建 AS2、EDIFACT 或 X12 协议。在通过 [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) 和 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)构建适用于 B2B 方案的企业集成解决方案时，可以使用该协议。 创建协议后，可以使用 AS2、EDIFACT 或 X12 连接器来交换 B2B 消息。

若要创建用于交换 RosettaNet 消息的协议，请参阅[交换 RosettaNet 消息](../logic-apps/logic-apps-enterprise-integration-rosettanet.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 用于存储协议和其他 B2B 项目的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 此集成帐户必须与 Azure 订阅相关联。

* 至少两个已在集成帐户中创建的[贸易合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)。 协议需要有主机合作伙伴和来宾合作伙伴。 两个合作伙伴都必须使用与你要创建的协议（如 AS2、X12 或 EDIFACT）相同的“业务标识”限定符。

* 可选：要在其中使用协议的逻辑应用，以及用于启动逻辑应用工作流的触发器。 若只创建集成帐户和 B2B 项目，则不需逻辑应用。 但是，必须先将集成帐户关联到逻辑应用，然后逻辑应用才能使用集成帐户中的 B2B 项目。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-agreements"></a>创建协议

1. 登录到 [Azure 门户](https://portal.azure.com)。
在 Azure 主菜单中，选择“所有服务”  。 在搜索框中，输入“集成”作为筛选器。 从结果中选择此资源：**集成帐户**

   ![查找集成帐户](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. 在“集成帐户”下，选择要在其中创建协议的集成帐户。 

   ![选择要在其中创建协议的集成帐户](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. 在右侧窗格的“组件”  下，选择“协议”  磁贴。

   ![选择“协议”](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. 在“协议”  下，选择“添加”  。 在“添加”  窗格中提供有关协议的信息，例如：

   ![选择“添加”](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | 属性 | 必须 | Value | 说明 |
   |----------|----------|-------|-------------|
   | **名称** | 是 | <*agreement-name*> | 协议的名称 |
   | **协议类型** | 是 | **AS2**、**X12** 或 **EDIFACT** | 协议 (agreement) 的协议 (protocol) 类型。 创建协议 (agreement) 文件时，该文件中的内容必须与协议 (agreement) 类型匹配。 | |  
   | **主机合作伙伴** | 是 | <*host-partner-name*> | 主机合作伙伴代表指定协议的组织 |
   | **主机标识** | 是 | <*host-partner-identifier*> | 主机合作伙伴的标识符 |
   | **来宾合作伙伴** | 是 | <*guest-partner-name*> | 托管方代表与管理方进行交易的组织 |
   | **来宾标识** | 是 | <*guest-partner-identifier*> | 来宾合作伙伴的标识符 |
   | **接收设置** | 多种多样 | 多种多样 | 这些属性指定主机合作伙伴如何从协议中的来宾合作伙伴处接收所有传入消息。 有关详细信息，请参阅相应的协议类型： <p>- [AS2 消息设置](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 消息设置](logic-apps-enterprise-integration-edifact.md) <br>- [X12 消息设置](logic-apps-enterprise-integration-x12.md) |
   | **发送设置** | 多种多样 | 多种多样 | 这些属性指定主机合作伙伴如何向协议中的来宾合作伙伴发送所有传出消息。 有关详细信息，请参阅相应的协议类型： <p>- [AS2 消息设置](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 消息设置](logic-apps-enterprise-integration-edifact.md) <br>- [X12 消息设置](logic-apps-enterprise-integration-x12.md) |
   |||||

   > [!IMPORTANT]
   > 协议的解决方法取决于与合作伙伴和传入消息中定义的项匹配：
   >
   > * 发件人的限定符和标识符
   > * 接收方的限定符和标识符
   >
   > 如果合作伙伴的这些值发生更改，请确保也更新协议。

1. 创建完协议后，在 "**添加**" 页上，选择 **"确定"**，并返回到集成帐户。

   **协议**列表现在显示你的新协议。

## <a name="edit-agreements"></a>编辑协议

1. 在[Azure 门户](https://portal.azure.com)的 Azure 主菜单中，选择 "**所有服务**"。

1. 在搜索框中，输入 "集成" 作为筛选器。 在结果中，选择 "此资源：**集成帐户**"

1. 在 "**集成帐户**" 下，选择具有要编辑的协议的集成帐户。

1. 在右侧窗格中的 "**组件**" 下，选择 "**协议**" 磁贴。

1. 在 "**协议**" 下，选择协议，然后选择 "**编辑**"。

1. 进行更改，并保存所做的更改。

## <a name="delete-agreements"></a>删除协议

1. 在[Azure 门户](https://portal.azure.com)的 Azure 主菜单中，选择 "**所有服务**"。

1. 在搜索框中，输入 "集成" 作为筛选器。 在结果中，选择 "此资源：**集成帐户**"

1. 在 "**集成帐户**" 下，选择具有要删除的协议的集成帐户。

1. 在右侧窗格中的 "**组件**" 下，选择 "**协议**" 磁贴。

1. 在 "**协议**" 下，选择协议，然后选择 "**删除**"。

1. 确认要删除所选协议。

## <a name="next-steps"></a>后续步骤

* [交换 AS2 消息](logic-apps-enterprise-integration-as2.md)
* [交换 EDIFACT 消息](logic-apps-enterprise-integration-edifact.md)
* [交换 X12 消息](logic-apps-enterprise-integration-x12.md)
