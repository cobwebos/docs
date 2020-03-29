---
title: 贸易伙伴协议
description: 使用 Azure 逻辑应用和企业集成包创建和管理贸易伙伴之间的协议
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 521a0ef4053be55e6c7322da5af26ccfc6c844e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790739"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>在 Azure 逻辑应用中创建和管理交易伙伴协议

[贸易伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*协议*通过定义在交换企业对企业 （B2B） 消息时要使用的特定行业标准协议，帮助组织和企业无缝地通信。 协议提供共同的好处，例如：

* 使组织能够使用已知的格式交换信息。
* 在进行 B2B 交易时提高效率。
* 易于创建、管理和使用，用于构建企业集成解决方案。

本文演示如何创建 AS2、EDIFACT 或 X12 协议，在使用[企业集成包](../logic-apps/logic-apps-enterprise-integration-overview.md)和[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)为 B2B 方案构建企业集成解决方案时可以使用该协议。 创建协议后，可以使用 AS2、EDIFACT 或 X12 连接器交换 B2B 消息。

要创建交换罗塞塔网消息的协议，请参阅[交换罗塞塔网消息](../logic-apps/logic-apps-enterprise-integration-rosettanet.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 用于存储协议和其他 B2B 工件的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 此集成帐户必须与 Azure 订阅关联。

* 您已在集成帐户中创建的至少两个[贸易伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)。 协议需要主机合作伙伴和来宾合作伙伴。 两个合作伙伴必须使用与要创建的协议相同的"业务标识"限定符，例如 AS2、X12 或 EDIFACT。

* 可选：要使用协议的逻辑应用和启动逻辑应用工作流的触发器。 只需创建集成帐户和 B2B 项目，就不需要逻辑应用。 但是，在逻辑应用可以在集成帐户中使用 B2B 项目之前，必须将集成帐户链接到逻辑应用。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-agreements"></a>创建协议

1. 登录到 Azure[门户](https://portal.azure.com)。
在 Azure 主菜单中，选择“所有服务”****。 在搜索框中，输入"集成"作为筛选器。 从结果中选择此资源：**集成帐户**

   ![查找集成帐户](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. 在 **"集成帐户**"下，选择要创建协议的集成帐户。

   ![选择要在其中创建协议的集成帐户](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. 在右侧窗格中，在 **"组件"** 下，选择"**协议**"磁贴。

   ![选择"协议"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. 在“协议”**** 下，选择“添加”****。 在 **"添加"** 窗格中，提供有关协议的信息，例如：

   ![选择“添加”](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | properties | 必选 | “值” | 描述 |
   |----------|----------|-------|-------------|
   | **名称** | 是 | <*协议名称*> | 协议名称 |
   | **协议类型** | 是 | **AS2** **、X12**或**EDIFACT** | 协议的协议类型。 创建协议文件时，该文件中的内容必须与协议类型匹配。 | |  
   | **管理方** | 是 | <*主机合作伙伴名称*> | 主机合作伙伴表示指定协议的组织 |
   | **管理方标识** | 是 | <*主机合作伙伴标识符*> | 主机合作伙伴的标识符 |
   | **托管方** | 是 | <*客人-合作伙伴名称*> | 托管方代表与管理方进行交易的组织 |
   | **托管方标识** | 是 | <*来宾合作伙伴标识符*> | 来宾合作伙伴的标识符 |
   | **接收设置** | 不定 | 不定 | 这些属性指定主机合作伙伴如何接收来自协议中来宾合作伙伴的所有传入消息。 有关详细信息，请参阅相应的协议类型： <p>- [AS2 消息设置](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 消息设置](logic-apps-enterprise-integration-edifact.md) <br>- [X12 消息设置](logic-apps-enterprise-integration-x12.md) |
   | **发送设置** | 不定 | 不定 | 这些属性指定主机合作伙伴如何向协议中的来宾合作伙伴发送所有传出消息。 有关详细信息，请参阅相应的协议类型： <p>- [AS2 消息设置](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 消息设置](logic-apps-enterprise-integration-edifact.md) <br>- [X12 消息设置](logic-apps-enterprise-integration-x12.md) |
   |||||

1. 创建完协议后，在 **"添加**"页上，选择 **"确定**"并返回到集成帐户。

   "**协议**"列表现在显示您的新协议。

## <a name="edit-agreements"></a>编辑协议

1. 在[Azure 门户](https://portal.azure.com)中，在主 Azure 菜单上，选择 **"所有服务**"。

1. 在搜索框中，输入"集成"作为筛选器。 从结果中选择此资源：**集成帐户**

1. 在 **"集成帐户**"下，选择具有要编辑的协议的集成帐户。

1. 在右侧窗格中，在 **"组件"** 下，选择"**协议**"磁贴。

1. 在 **"协议**"下，选择您的协议，然后选择 **"编辑**"。

1. 进行更改，然后保存更改。

## <a name="delete-agreements"></a>删除协议

1. 在[Azure 门户](https://portal.azure.com)中，在主 Azure 菜单上，选择 **"所有服务**"。

1. 在搜索框中，输入"集成"作为筛选器。 从结果中选择此资源：**集成帐户**

1. 在 **"集成帐户**"下，选择具有要删除的协议的集成帐户。

1. 在右侧窗格中，在 **"组件"** 下，选择"**协议**"磁贴。

1. 在 **"协议**"下，选择您的协议，然后选择 **"删除**"。

1. 确认要删除所选协议。

## <a name="next-steps"></a>后续步骤

* [交换 AS2 消息](logic-apps-enterprise-integration-as2.md)
* [交换 EDIFACT 消息](logic-apps-enterprise-integration-edifact.md)
* [交换 X12 消息](logic-apps-enterprise-integration-x12.md)
