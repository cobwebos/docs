---
title: 为企业到企业 (B2B) 消息创建合作伙伴 - Azure 逻辑应用 | Microsoft 文档
description: 了解如何使用 Enterprise Integration Pack 和逻辑应用向集成帐户添加合作伙伴
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: anneta
editor: ''
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 17f15c49e0f8137d5f11c57fa600588cda791c28
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="add-or-update-partners-in-business-to-business-agreements-in-your-workflow"></a>在工作流中添加或更新企业到企业协议中的合作伙伴

合作伙伴是参与企业到企业 (B2B) 交易并相互交换消息的实体。 在创建代表你和这些交易中的其他组织的合作伙伴之前，们双方必须先共享可识别并验证相互发送的消息的信息。 在讨论这些详细信息并准备好开始业务关系后，们便可以在集成帐户中创建代表你们双方的合作伙伴。

## <a name="what-roles-do-partners-play-in-your-integration-account"></a>合作伙伴在集成帐户中扮演什么角色？

要定义有关合作伙伴之间交换的消息的详细信息，需在这些合作伙伴之间创建协议。 但是，在创建协议前，必须先将至少两个合作伙伴添加到集成帐户。 组织必须作为**管理方**是协议的一部分。 其他合作伙伴或**托管方**表示与组织交换消息的组织。 托管方可以是其他公司，甚至是自己的组织中的部门。

添加这些合作伙伴后，便可以创建协议。

接收和发送设置源于主机合作伙伴的角度。 例如，协议中的接收设置确定主机合作伙伴如何接收从来宾合作伙伴发送的消息。 同样，协议中的发送设置指示主机合作伙伴如何将消息发送给来宾合作伙伴。

## <a name="create-partner"></a>创建合作伙伴

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在 Azure 主菜单中，选择“所有服务”。 在搜索框中输入“集成”，然后选择“集成帐户”。

   ![查找集成帐户](./media/logic-apps-enterprise-integration-partners/account-1.png)

3. 在“集成帐户”下，选择要添加合作伙伴的集成帐户。

   ![选择“集成帐户”](./media/logic-apps-enterprise-integration-partners/account-2.png)

4. 选择“合作伙伴”磁贴。

   ![选择“合作伙伴”](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. 在“合作伙伴”下，选择“添加”。

   ![选择“添加”](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. 为合作伙伴输入名称，并选择“限定符”。 输入**值**以标识应用将接收的文档。 完成后，选择“确定”。

   ![添加合作伙伴详细信息](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. 再次选择“合作伙伴”磁贴。

   ![选择“合作伙伴”磁贴](./media/logic-apps-enterprise-integration-partners/partner-5.png)

   现在将显示新合作伙伴。 

   ![查看新合作伙伴](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="edit-partner"></a>编辑合作伙伴

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择“集成帐户”。 选择“合作伙伴”磁贴。

   ![选择“合作伙伴”磁贴](./media/logic-apps-enterprise-integration-partners/edit.png)

2. 在“合作伙伴”下，选择要编辑的合作伙伴。

   ![选择要删除的合作伙伴](./media/logic-apps-enterprise-integration-partners/edit-1.png)

3. 在“更新合作伙伴”下，进行更改。
完成后，选择“保存”。 

   ![进行更改和保存更改](./media/logic-apps-enterprise-integration-partners/edit-2.png)

   若要取消所做的更改，请选择“放弃”。

## <a name="delete-partner"></a>删除合作伙伴

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择“集成帐户”。 选择“合作伙伴”磁贴。

   ![选择“合作伙伴”磁贴](./media/logic-apps-enterprise-integration-partners/delete.png)

2. 在“合作伙伴”下，选择要删除的合作伙伴。
选择“删除”。

   ![删除合作伙伴](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>后续步骤

* [了解有关协议的详细信息](../logic-apps/logic-apps-enterprise-integration-agreements.md "了解企业集成协议")  

