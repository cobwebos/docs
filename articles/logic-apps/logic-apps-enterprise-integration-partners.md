---
title: "了解合作伙伴和 Enterprise Integration Pack | Microsoft Docs"
description: "了解如何将合作伙伴与 Enterprise Integration Pack 和逻辑应用一起使用"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 549dd716df6ee64892ef33ffe4d42751d8662ccb


---
# <a name="learn-about-partners-and-enterprise-integration-pack"></a>了解合作伙伴和 Enterprise Integration Pack
## <a name="overview"></a>概述
你和你要与之开展业务的组织必须先共享有助于你们识别并验证彼此发送的消息的信息，然后才能创建合作伙伴。 进行这些讨论，并且准备好开始建立业务关系之后，可以在集成帐户中创建合作伙伴。

## <a name="what-is-a-partner"></a>什么是合作伙伴？
合作伙伴是参与企业到企业 (B2B) 消息传递和事务的实体。 

## <a name="how-are-partners-used"></a>如何使用合作伙伴？
合作伙伴用于创建协议。 协议定义有关在合作伙伴之间交换的消息的详细信息。 

需要先将至少两个合作伙伴添加到集成帐户，然后才能创建协议。 协议的合作伙伴之一必须是你的组织。 代表你组织的合作伙伴称为**主机合作伙伴**。 第二个合作伙伴代表你的组织与之交换消息的另一个组织。 第二个合作伙伴称为**来宾合作伙伴**。 来宾合作伙伴可以是其他公司，甚至是你自己的组织内的部门。  

添加了合作伙伴之后，会使用这些合作伙伴创建协议。 

接收和发送设置源于主机合作伙伴的角度。 例如，协议中的接收设置确定主机合作伙伴如何接收从来宾合作伙伴发送的消息。 同样，协议中的发送设置指示主机合作伙伴如何将消息发送给来宾合作伙伴。

## <a name="how-to-create-a-partner"></a>如何创建合作伙伴？
通过 Azure 门户：  

1. 选择“浏览”  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. 在筛选器搜索框中输入**集成**，然后从结果列表中选择“集成帐户”     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. 选择要将合作伙伴添加到其中的**集成帐户**  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. 选择“合作伙伴”磁贴  
   ![](./media/logic-apps-enterprise-integration-partners/partner-1.png)  
5. 在打开的“合作伙伴”边栏选项卡中，选择“添加”按钮  
   ![](./media/logic-apps-enterprise-integration-partners/partner-2.png)  
6. 为合作伙伴输入“名称”，然后选择“限定符”，最后输入“值”。 该值用于帮助标识进入应用的文档。  
   ![](./media/logic-apps-enterprise-integration-partners/partner-3.png)  
7. 选择震铃通知图标可查看合作伙伴创建过程的进度。  
   ![](./media/logic-apps-enterprise-integration-partners/partner-4.png)  
8. 选择“合作伙伴”磁贴。 这会刷新该磁贴，你应看到合作伙伴的数量增加，以反映新合作伙伴已成功添加。    
   ![](./media/logic-apps-enterprise-integration-partners/partner-5.png)  
9. 选择“合作伙伴”磁贴之后，你还会看到新添加的合作伙伴显示在“合作伙伴”边栏选项卡中。    
   ![](./media/logic-apps-enterprise-integration-partners/partner-6.png)  

## <a name="how-to-edit-a-partner"></a>如何编辑合作伙伴
按照以下步骤可编辑集成帐户中已存在的合作伙伴：  

1. 选择“合作伙伴”磁贴  
2. 在“合作伙伴”边栏选项卡打开时，选择要编辑的合作伙伴  
3. 在“更新合作伙伴”磁贴中，进行所需更改  
4. 如果对更改感到满意，请选择“保存”链接，否则选择“放弃”链接以丢弃更改。  
   ![](./media/logic-apps-enterprise-integration-partners/edit-1.png)  

## <a name="how-to-delete-a-partner"></a>如何删除合作伙伴
1. 选择“合作伙伴”磁贴  
2. 在“合作伙伴”边栏选项卡打开时，选择要编辑的合作伙伴  
3. 选择“删除”链接    
   ![](./media/logic-apps-enterprise-integration-partners/delete-1.png)   

## <a name="next-steps"></a>后续步骤
* [了解有关协议的详细信息](../logic-apps/logic-apps-enterprise-integration-agreements.md "了解企业集成协议")  




<!--HONumber=Jan17_HO3-->


