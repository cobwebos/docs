---
title: "集成帐户和 Enterprise Integration Pack 概述 | Microsoft Docs"
description: "了解有关集成帐户、Enterprise Integration Pack 和逻辑应用的所有信息"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: ce2f01045195b9881bc1b3c337c453d10cd39540


---
# <a name="overview-of-integration-accounts"></a>集成帐户概述
## <a name="what-is-an-integration-account"></a>什么是集成帐户？
集成帐户是一个 Azure 帐户，允许企业集成应用管理包括架构、映射、证书、合作伙伴和协议在内的项目。 你创建的任何集成应用都需要使用一个集成帐户才能访问如架构、映射或证书。

## <a name="create-an-integration-account"></a>创建集成帐户
1. 选择“浏览”   
   ![](./media/logic-apps-enterprise-integration-accounts/account-1.png)  
2. 在筛选器搜索框中输入**集成**，然后从结果列表中选择“集成帐户”     
   ![](./media/logic-apps-enterprise-integration-accounts/account-2.png)  
3. 从页面顶部的菜单中选择“添加”。      
   ![](./media/logic-apps-enterprise-integration-accounts/account-3.png)  
4. 输入“名称”，选择要使用的“订阅”，创建一个新的**资源组**或者选择现有资源组，选择要在其中托管集成帐户的“位置”，选择“定价层”，再选择“创建”按钮。   
   
   此时将在所选的位置中预配集成帐户。 此操作应在 1 分钟内完成。    
   ![](./media/logic-apps-enterprise-integration-accounts/account-4.png)  
5. 刷新页面。 可看到列出的新集成帐户。 祝贺你！  
   ![](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

## <a name="how-to-link-an-integration-account-to-a-logic-app"></a>如何将集成帐户链接到逻辑应用
为了使逻辑应用能够访问映射、架构、协议和其他位于集成帐户的项目，必须首先将集成帐户链接到逻辑应用。

### <a name="here-are-the-steps-to-link-an-integration-account-to-a-logic-app"></a>将集成帐户链接到逻辑应用的步骤
#### <a name="prerequisites"></a>先决条件
* 集成帐户
* 逻辑应用

> [!NOTE]
> 在开始之前确保集成帐户和逻辑应用处于**相同的 Azure 位置**
> 
> 

1. 从逻辑应用的菜单中选择“设置”链接  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)   
2. 从“设置”边栏选项卡中选择“集成帐户”  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)   
3. 从“选择集成帐户”下拉列表框中选择想要链接到逻辑应用的集成帐户  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)   
4. 保存工作  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-4.png)   
5. 此时可看到一个通知，该通知指示集成帐户已链接到逻辑应用，并且现在逻辑应用可访问集成帐户中的所有项目。  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)   

既然集成帐户已链接到逻辑应用，那么可以转到逻辑应用，并使用 XML 验证、平面文件编码/解码或转换等 B2B 连接器，以便使用 B2B 功能创建应用。  

## <a name="how-to-delete-an-integration-account"></a>如何删除集成帐户？
1. 选择“浏览”  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. 在筛选器搜索框中输入**集成**，然后从结果列表中选择“集成帐户”     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. 选择想要删除的**集成帐户**  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. 选择位于菜单的中的“删除”链接   
   ![](./media/logic-apps-enterprise-integration-accounts/delete.png)  
5. 确认选择    

## <a name="how-to-move-an-integration-account"></a>如何移动集成帐户？
可以轻松地将集成帐户移动到新的订阅和新的资源组。 如果需要移动集成帐户，请按照以下步骤：

> [!IMPORTANT]
> 移动集成帐户后需要更新所有的脚本，以便使用新的资源 ID。
> 
> 

1. 选择“浏览”  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. 在筛选器搜索框中输入**集成**，然后从结果列表中选择“集成帐户”     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. 选择想要删除的**集成帐户**  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. 选择位于菜单的中的“移动”链接   
   ![](./media/logic-apps-enterprise-integration-accounts/move.png)  
5. 确认选择    

## <a name="next-steps"></a>后续步骤
* [了解有关协议的详细信息](../logic-apps/logic-apps-enterprise-integration-agreements.md "了解企业集成协议")  




<!--HONumber=Jan17_HO3-->


