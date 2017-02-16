---
title: "在 Azure 逻辑应用中创建、链接、删除或移动集成帐户 | Microsoft 文档"
description: "如何创建集成帐户并将其链接到逻辑应用"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: dca762854f22721de76f69cbc28c0377d563fe28
ms.openlocfilehash: e26271a1291fc76e3ea8f93a7aa9ab8c811ff604


---

# <a name="what-is-an-integration-account"></a>什么是集成帐户？
集成帐户可让企业集成应用管理包括架构、映射、证书、合作伙伴和协议在内的项目。 创建的任何集成应用都使用集成帐户来访问这些架构、映射和证书等等。

## <a name="create-an-integration-account"></a>创建集成帐户
1. 选择“浏览”：   
   ![](./media/logic-apps-enterprise-integration-accounts/account-1.png)  
2. 在筛选搜索框中输入“集成”，然后从结果列表中选择“集成帐户”：     
   ![](./media/logic-apps-enterprise-integration-accounts/account-2.png)  
3. 从页面顶部的菜单中选择“添加”：      
   ![](./media/logic-apps-enterprise-integration-accounts/account-3.png)  
4. 输入“名称”，选择要使用的“订阅”，创建一个新的**资源组**或者选择现有资源组，选择要在其中托管集成帐户的“位置”，选择“定价层”，再选择“创建”按钮。   
   
   此时将在所选的位置中预配集成帐户。 此操作应在 1 分钟内完成。    
   ![](./media/logic-apps-enterprise-integration-accounts/account-4.png)  
5. 刷新页面。 将看到列出的新集成帐户：  
   ![](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

接下来，将刚刚创建的集成帐户链接到逻辑应用。 

## <a name="link-an-integration-account-to-a-logic-app"></a>将集成帐户链接到逻辑应用
为了使逻辑应用能够访问映射、架构、协议和其他位于集成帐户中的项目，需将集成帐户链接到逻辑应用。

#### <a name="prereqs"></a>先决条件
* 集成帐户
* 逻辑应用

> [!NOTE] 
> 在开始之前确保集成帐户和逻辑应用处于**相同的 Azure 位置**。

1. 从逻辑应用的菜单中选择“设置”链接：  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)   
2. 从“设置”边栏选项卡中选择“集成帐户”：  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)   
3. 从“选择集成帐户”下拉列表框中选择想要链接到逻辑应用的集成帐户：  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)   
4. 保存工作：  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-4.png)   
5. 此时可看到一个通知，该通知指示集成帐户已链接到逻辑应用，并且现在逻辑应用可访问集成帐户中的所有项目：  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)   

将集成帐户链接到逻辑应用后，可在逻辑应用中使用 B2B 连接器。 一些常见的 B2B 连接器包括 XML 验证、平面文件编码/解码。  

## <a name="how-to-delete-an-integration-account"></a>如何删除集成帐户？
1. 选择“浏览”：  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. 在筛选搜索框中输入“集成”，然后从结果列表中选择“集成帐户”：     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. 选择想要删除的**集成帐户**：  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. 在菜单中选择“删除”链接：   
   ![](./media/logic-apps-enterprise-integration-accounts/delete.png)  
5. 确认选择。    

## <a name="how-to-move-an-integration-account"></a>如何移动集成帐户？
可以轻松地将集成帐户移动到新的订阅和新的资源组。 如果需要移动集成帐户，请按照以下步骤：

> [!IMPORTANT]
> 移动集成帐户后，需要更新所有脚本才能使用新资源 ID。

1. 选择“浏览”：  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. 在筛选搜索框中输入“集成”，然后从结果列表中选择“集成帐户”：     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. 选择想要删除的**集成帐户**：  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. 在菜单中选择“移动”链接：   
   ![](./media/logic-apps-enterprise-integration-accounts/move.png)  
5. 确认选择。    

## <a name="next-steps"></a>后续步骤
* [了解有关协议的详细信息](../logic-apps/logic-apps-enterprise-integration-agreements.md "了解企业集成协议")  




<!--HONumber=Jan17_HO5-->


