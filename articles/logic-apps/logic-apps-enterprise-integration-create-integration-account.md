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
ms.openlocfilehash: 716e7b5bab8725dea0fd2b760d0e46e8e892c5b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-an-integration-account"></a>什么是集成帐户？

集成帐户可让企业集成应用管理包括架构、映射、证书、合作伙伴和协议在内的项目。 创建的任何集成应用都使用集成帐户来访问这些架构、映射和证书等等。

## <a name="create-an-integration-account"></a>创建集成帐户

1.  登录 [Azure 门户](http://portal.azure.com "Azure portal")。 在左侧菜单中，选择“更多服务”。

    ![选择“更多服务”](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. 在搜索框中，为筛选器键入“集成”。 在结果列表中，选择“集成帐户”。

    ![筛选“集成”，选择“集成帐户”](./media/logic-apps-enterprise-integration-accounts/account-2.png)  

3. 在页面顶部选择“添加”。

    ![选择“添加”](./media/logic-apps-enterprise-integration-accounts/account-3.png)

4. 为集成帐户命名，并选择要使用的 Azure 订阅。 可以创建新的**资源组**，或选择现有的资源组。 然后选择一个用于托管集成帐户的位置和一个定价层。 

    准备就绪后，选择“创建”。

    ![提供集成帐户的详细信息](./media/logic-apps-enterprise-integration-accounts/account-4.png)

    Azure 会在选定位置预配集成帐户，此过程在 1 分钟内应可完成。

5. 刷新页面。 此时可以看到已列出新集成帐户。

    ![显示了新的集成帐户](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

接下来，将创建的集成帐户链接到逻辑应用。 

## <a name="link-an-integration-account-to-a-logic-app"></a>将集成帐户链接到逻辑应用

为了使逻辑应用能够访问映射、架构、协议和其他位于集成帐户中的项目，需将集成帐户链接到逻辑应用。

### <a name="prerequisites"></a>先决条件

* 集成帐户
* 逻辑应用

> [!NOTE] 
> 在开始之前，请确保集成帐户和逻辑应用位于*相同的 Azure 位置*。


1. 在 Azure 门户中选择逻辑应用，然后检查逻辑应用的位置。

    ![选择逻辑应用并检查位置](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)

2. 在“设置”下面，选择“集成帐户”。

    ![选择“集成帐户”](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)

3. 从“选择集成帐户”列表中，选择想要链接到逻辑应用的集成帐户。 若要完成链接，请选择“保存”。

    ![选择集成帐户](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)

    随后将收到一条通知，指出集成帐户已链接到逻辑应用，并且逻辑应用现在可以访问该集成帐户中的所有项目。

    ![逻辑应用已链接到集成帐户](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)

将集成帐户链接到逻辑应用后，可在逻辑应用中使用 B2B 连接器。 常见的 B2B 连接器包括 XML 验证和平面文件编码/解码。  

## <a name="delete-your-integration-account"></a>删除集成帐户

1. 选择“更多服务”。

    ![选择“更多服务”](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. 在搜索框中，为筛选器键入“集成”。 在结果列表中，选择“集成帐户”。

    ![筛选“集成”，选择“集成帐户”](./media/logic-apps-enterprise-integration-accounts/account-2.png)  

3. 选择要删除的集成帐户。

    ![选择要删除的集成帐户](./media/logic-apps-enterprise-integration-accounts/account-5.png)

4. 在菜单中选择“删除”。

    ![选择“删除”](./media/logic-apps-enterprise-integration-accounts/delete.png)

5. 确认要删除该集成帐户。

## <a name="move-your-integration-account"></a>移动集成帐户

要将集成帐户移到另一个 Azure 订阅或资源组，请执行以下步骤。

> [!IMPORTANT]
> 移动集成帐户后，必须更新所有脚本才能使用新资源 ID。

1. 选择“更多服务”。

    ![选择“更多服务”](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. 在搜索框中，为筛选器键入“集成”。 在结果列表中，选择“集成帐户”。

    ![筛选“集成”，选择“集成帐户”](./media/logic-apps-enterprise-integration-accounts/account-2.png)

3. 选择要移动的集成帐户。 在“设置”下面，选择“属性”。

    ![选择要移动的集成帐户。 在“设置”下面选择“属性”](./media/logic-apps-enterprise-integration-accounts/move.png)

5. 更改与集成帐户关联的资源组或 Azure 订阅。

    ![选择“更改资源组”或“更改订阅”](./media/logic-apps-enterprise-integration-accounts/move-2.png)

## <a name="next-steps"></a>后续步骤
* [了解有关协议的详细信息](../logic-apps/logic-apps-enterprise-integration-agreements.md "了解企业集成协议")  

