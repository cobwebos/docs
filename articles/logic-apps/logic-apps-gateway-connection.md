---
title: "逻辑应用本地数据网关连接 | Microsoft Docs"
description: "有关如何从逻辑应用创建与本地数据网关之间的连接的信息。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 0a16f22b6e3bb60091409c64b631afcba3d6db10


---
# <a name="connect-to-the-on-premises-data-gateway-for-logic-apps"></a>为逻辑应用连接到本地数据网关
通过受支持的逻辑应用连接器可以配置连接，以便通过本地数据网关访问本地数据。  以下步骤将逐步引导你完成如何安装和配置本地数据网关以使用逻辑应用的过程。

## <a name="prerequisites"></a>先决条件
  * 必须在 Azure 中使用工作或学校电子邮件地址将本地数据网关与帐户（基于 Azure Active Directory 的帐户）相关联
  * 如果在使用 Microsoft 帐户（例如 @outlook.com, @live.com)），则可以[按照此处的步骤](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)，使用 Azure 帐户创建工作或学校电子邮件地址 
  * 必须将本地数据网关[安装在本地计算机上](logic-apps-gateway-install.md)。
  * 网关不得由另一个 Azure 本地数据网关进行声明（[声明随下面步骤 2 的创建一起进行](#2-create-an-azure-on-premises-data-gateway-resource)）- 一个安装只能关联到一个网关资源。

## <a name="installing-and-configuring-the-connection"></a>安装和配置连接
### <a name="1-install-the-on-premises-data-gateway"></a>1.安装本地数据网关
有关安装本地数据网关的信息可以在[本文](logic-apps-gateway-install.md)中找到。  网关必须先安装在本地计算机上，然后你才能继续执行剩余步骤。

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2.创建 Azure 本地数据网关资源
安装之后，必须将 Azure 订阅与本地数据网关相关联。

1. 使用在网关安装过程中使用的相同工作或学校电子邮件地址登录到 Azure
2. 单击“新建”资源按钮
3. 搜索并选择“本地数据网关”
4. 填写相应信息以将网关与帐户相关联 - 包括选择相应的“安装名称”
   
    ![本地数据网关连接][1]
5. 单击“创建”按钮以创建资源

### <a name="3-create-a-logic-app-connection-in-the-designer"></a>3.在设计器中创建逻辑应用连接
现在 Azure 订阅已与本地数据网关的实例相关联，你可以从逻辑应用中创建与它的连接。

1. 打开逻辑应用，然后选择支持本地连接的连接器（撰写本文时是 SQL Server）
2. 选中“通过本地数据网关连接”对应的复选框
   
    ![逻辑应用设计器网关创建][2]
3. 选择“网关”以连接到并完成所需的任何其他连接信息
4. 单击“创建”以创建连接

连接现在应已成功配置，可在逻辑应用中使用。  

## <a name="next-steps"></a>后续步骤
* [逻辑应用的常见示例和方案](../logic-apps/logic-apps-examples-and-scenarios.md)
* [企业集成功能](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-gateway-connection/createblade.png
[2]: ./media/logic-apps-gateway-connection/blankconnection.png
[3]: ./media/logic-apps-logic-gateway-connection/checkbox.png



<!--HONumber=Jan17_HO3-->


