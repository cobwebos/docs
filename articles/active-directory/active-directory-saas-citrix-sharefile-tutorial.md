---
title: "教程：Azure Active Directory 与 Citrix ShareFile 的集成 | Microsoft 文档"
description: "了解如何将 Citrix ShareFile 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 30f5ed9a5e9371193ef7663cdd8896793ca6359b


---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>教程：Azure Active Directory 与 Citrix ShareFile 的集成
本教程的目标是展示 Azure 与 Citrix ShareFile 的集成。  
在本教程中概述的方案假定已具有以下各项：

* 有效的 Azure 订阅
* Citrix ShareFile 租户

完成本教程后，分配到 Citrix ShareFile 的 Azure AD 用户将能够在你的 Citrix ShareFile 公司站点上（服务提供商启动的登录）或者使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以单一登录方式登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Citrix ShareFile 启用应用程序集成
2. 配置单一登录
3. 配置用户预配
4. 分配用户

![方案](./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "Scenario")

## <a name="enabling-the-application-integration-for-citrix-sharefile"></a>为 Citrix ShareFile 启用应用程序集成
本部分的目的是概述如何为 Citrix ShareFile 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-citrix-sharefile-perform-the-following-steps"></a>若要为 Citrix ShareFile 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加一个应用程序](./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜索框**中，键入“Citrix ShareFile”。
   
   ![应用程序库](./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "Application gallery")
7. 在结果窗格中，选择“Citrix ShareFile”，然后单击“完成”以添加该应用程序。
   
   ![Citrix ShareFile](./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Citrix ShareFile 证明自己的身份。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中，在 **Citrix ShareFile** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
   ![启用单一登录](./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "Enable single sign-on")
2. 在“你希望用户如何登录到 Citrix ShareFile”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Configure Single Sign-On")
3. 在“配置应用 URL”页上，在“Citrix ShareFile 登录 URL”文本框中，使用 `https://<tenant-name>.shareFile.com` 模式键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "Configure App URL")
4. 在“在 Citrix ShareFile 处配置单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "ConfigureSingle Sign-On")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到你的 **Citrix ShareFile** 公司站点。
6. 在顶部工具栏中，单击“管理”。
7. 在左侧导航窗格中，选择“配置单一登录”。
   
   ![帐户管理](./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "Account Administration")
8. 在“单一登录/ SAML 2.0 配置”对话框页上，在“基本设置”下执行以下步骤：
   
   ![单一登录](./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "Single sign-on")
   
   1. 单击“启用 SAML”。
   2. 在 Azure 经典门户中，在“在 Citrix ShareFile 处配置单一登录”对话框页上，复制“实体 ID”值，然后将其粘贴到“IDP 颁发者/实体 ID”文本框中。
   3. 在 Azure 经典门户中，在“在 Citrix ShareFile 处配置单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“登录 URL”文本框中。
   4. 在 Azure 经典门户中，在“在 Citrix ShareFile 处配置单一登录”对话框页上，复制“远程注销 URL”值，然后将其粘贴到“注销 URL”文本框中。
   5. 单击“X.509 证书”字段旁边的“更改”，然后上载从 Azure AD 经典门户下载的证书。
      ![基本设置](./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "Basic Settings")
9. 在 Citrix ShareFile 管理门户中单击“保存”。
10. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "Configure single sign-on")
    
    ## <a name="configuring-user-provisioning"></a>配置用户预配

为了使 Azure AD 用户能够登录到 Citrix ShareFile，必须将其预配到 Citrix ShareFile 中。  
对于 Citrix ShareFile，需要手动执行预配。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 登录到你的 **Citrix ShareFile** 租户。
2. 单击“管理用户”\>“管理用户主页”\>“+ 创建员工”。
   
   ![创建员工](./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "Create Employee")
3. 输入想要预配的有效 Azure AD 帐户的“电子邮件”、“名字”和“姓氏”。
   
   ![基本信息](./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "Basic Information")
4. 单击“添加用户”。
   
   > [!NOTE]
   > AAD 帐户持有者将收到一封电子邮件，并打开用于在激活帐户前确认其帐户的链接。
   > 
   > 

> [!NOTE]
> 可以使用 Citrix ShareFile 提供的任何其他 Citrix ShareFile 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要向希望能够通过应用程序访问配置的 Azure AD 用户分配访问权限，从而向他们授予该权限。

### <a name="to-assign-users-to-citrix-sharefile-perform-the-following-steps"></a>若要将用户分配到 Citrix ShareFile，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在 **Citrix ShareFile** 应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "Assign users")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


