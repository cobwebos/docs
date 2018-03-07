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
ms.date: 02/23/2017
ms.author: LADocs; mandia
ms.openlocfilehash: b238ef8cf9d1328913334a92c042584334d81e3c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="what-is-an-integration-account"></a>什么是集成帐户？

集成帐户为企业集成应用（具体而言是逻辑应用）提供了一种方法来访问和管理 B2B 项目，例如，贸易合作伙伴、协议、地图、架构、证书，等等。 若要提供此访问权限，在确保集成帐户和逻辑应用具有*相同的 Azure 位置*后，将集成帐户链接到逻辑应用。

## <a name="create-an-integration-account"></a>创建集成帐户

1. 登录 [Azure 门户](http://portal.azure.com "Azure portal")。 

2. 在 Azure 主菜单中，选择“所有服务”。 在搜索框中输入“集成”，然后选择“集成帐户”。

   ![创建集成帐户](./media/logic-apps-enterprise-integration-accounts/account-1.png)

3. 在页面顶部选择“添加”。

   ![选择“添加”](./media/logic-apps-enterprise-integration-accounts/account-3.png)

4. 为集成帐户命名，并选择要使用的 Azure 订阅。 可以创建新的**资源组**，或选择现有的资源组。 选择一个用于托管集成帐户的位置和一个定价层。 准备就绪后，选择“创建”。

   ![提供集成帐户的详细信息](./media/logic-apps-enterprise-integration-accounts/account-4.png)

   Azure 会在选定位置预配集成帐户，此过程应当会在一分钟内完成。

5. 刷新页面。 此时可以看到已列出新集成帐户。

   ![显示了新的集成帐户](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

接下来，将创建的集成帐户链接到逻辑应用。 

## <a name="link-an-integration-account-to-a-logic-app"></a>将集成帐户链接到逻辑应用

若要使逻辑应用能够访问集成帐户中的贸易合作伙伴、协议、地图和架构等 B2B 项目，请将集成帐户链接到逻辑应用。 

1. 在 Azure 门户中选择逻辑应用，并检查逻辑应用的位置。

   ![选择逻辑应用并检查位置](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)

2. 在“设置”下面，选择“集成帐户”。

   ![选择“集成帐户”](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)

3. 从“选择集成帐户”列表中，选择想要链接到逻辑应用的集成帐户。 若要完成链接，请选择“保存”。

   ![选择集成帐户](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)

   随后将收到一条通知，指出集成帐户已链接到逻辑应用，并且逻辑应用现在可以访问该集成帐户中的所有项目。

   ![逻辑应用已链接到集成帐户](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)

将集成帐户链接到逻辑应用后，可在逻辑应用中使用 B2B 连接器。 常见的 B2B 连接器包括 XML 验证和平面文件编码/解码。  

## <a name="delete-your-integration-account"></a>删除集成帐户

1. 在 Azure 主菜单中，选择“所有服务”。 在搜索框中输入“集成”，然后选择“集成帐户”。

   ![查找集成帐户](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. 选择要删除的集成帐户。

    ![选择要删除的集成帐户](./media/logic-apps-enterprise-integration-accounts/account-5.png)

3. 在菜单中选择“删除”。

    ![选择“删除”](./media/logic-apps-enterprise-integration-accounts/delete.png)

4. 确认要删除该集成帐户。

## <a name="move-your-integration-account"></a>移动集成帐户

要将集成帐户移到另一个 Azure 订阅或资源组，请执行以下步骤。 移动集成帐户后，请确保更新所有脚本来使用新资源 ID。

1. 在 Azure 主菜单中，选择“所有服务”。 在搜索框中输入“集成”，然后选择“集成帐户”。

   ![查找集成帐户](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. 选择要移动的集成帐户。 在“设置”下面，选择“属性”。

   ![选择要移动的集成帐户。 在“设置”下面选择“属性”](./media/logic-apps-enterprise-integration-accounts/move.png)

3. 更改与集成帐户关联的资源组或 Azure 订阅。

   ![选择“更改资源组”或“更改订阅”](./media/logic-apps-enterprise-integration-accounts/move-2.png)

## <a name="next-steps"></a>后续步骤

* [了解有关协议的详细信息](../logic-apps/logic-apps-enterprise-integration-agreements.md "了解企业集成协议")  

