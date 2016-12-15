---
title: "教程：Azure Active Directory 与 SimpleNexus 集成 | Microsoft 文档"
description: "了解如何使用 SimpleNexus 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 89821a05-88e2-4579-b144-0123b2b9cb95
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4226d82ac8f0b5e0d8806df2c93f1a1a73162316


---
# <a name="tutorial-azure-active-directory-integration-with-simplenexus"></a>教程：Azure Active Directory 与 SimpleNexus 集成
本教程的目的是说明 Azure 与 SimpleNexus 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 SimpleNexus 单一登录的订阅

完成本教程后，已向 SimpleNexus 分配的 Azure AD 用户将能够在 SimpleNexus 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 SimpleNexus 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-simplenexus-tutorial/IC785893.png "Scenario")

## <a name="enabling-the-application-integration-for-simplenexus"></a>为 SimpleNexus 启用应用程序集成
本部分的目的是概述如何为 SimpleNexus 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-simplenexus-perform-the-following-steps"></a>若要为 SimpleNexus 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-simplenexus-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-simplenexus-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-simplenexus-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-simplenexus-tutorial/IC749322.png "Add an application from gallerry")
6. 在搜索框中，键入“simple nexus”。
   
   ![应用程序库](./media/active-directory-saas-simplenexus-tutorial/IC785894.png "Application Gallery")
7. 在结果窗格中，选择“SimpleNexus”，然后单击“完成”以添加该应用程序。
   
   ![Simple Nexus](./media/active-directory-saas-simplenexus-tutorial/IC809578.png "Simple Nexus")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 SimpleNexus 进行身份验证。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“SimpleNexus”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-simplenexus-tutorial/IC785896.png "Configure Single Sign-On")
2. 在“你希望用户如何登录 SimpleNexus”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-simplenexus-tutorial/IC785897.png "Configure Single Sign-On")
3. 在“配置应用 URL”页上的“SimpleNexus 登录 URL”文本框中，使用模式“*https://simplenexus.com/CompanyName\_login*”键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-simplenexus-tutorial/IC786904.png "Configure App URL")
4. 在“配置 SimpleNexus 的单一登录”页上，单击“下载元数据”，然后将元数据文件转发给 SimpleNexus 支持团队。
   
   ![配置单一登录](./media/active-directory-saas-simplenexus-tutorial/IC785899.png "Configure Single Sign-On")
   
   > [!NOTE]
   > 单一登录需要由 SimpleNexus 支持团队启用。
   > 
   > 
5. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-simplenexus-tutorial/IC785900.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>配置用户设置

要使 Azure AD 用户能够登录 SimpleNexus，必须将这些用户预配到 SimpleNexus 中。  
对于 SimpleNexus，预配是由租户管理员执行的一项手动任务。

> [!NOTE]
> 可以使用任何其他 SimpleNexus 用户帐户创建工具或 SimpleNexus 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

### <a name="to-assign-users-to-simplenexus-perform-the-following-steps"></a>若要将用户分配到 SimpleNexus，请执行以下步骤：
1. 在 Azure 经典门户中，创建一个测试帐户。
2. 在“SimpleNexus”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-simplenexus-tutorial/IC785901.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-simplenexus-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


