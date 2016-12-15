---
title: "教程：Azure Active Directory 与 Clarizen 的集成 | Microsoft 文档"
description: "了解如何将 Clarizen 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9f8d2418fe86163a4022960f87803c82487c86fd


---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>教程：Azure Active Directory 与 Clarizen 的集成
本教程的目标是展示 Azure 与 Clarizen 的集成。  
在本教程中概述的方案假定已具有以下各项：

* 有效的 Azure 订阅
* 启用了 Clarizen 单一登录的订阅

完成本教程后，分配到 Clarizen 的 Azure AD 用户将能够在你的 Clarizen 公司站点上（服务提供商启动的登录）或者使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以单一登录方式登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Clarizen 启用应用程序集成
2. 配置单一登录
3. 配置用户预配
4. 分配用户

![方案](./media/active-directory-saas-clarizen-tutorial/IC784679.png "Scenario")

## <a name="enabling-the-application-integration-for-clarizen"></a>为 Clarizen 启用应用程序集成
本部分的目的是概述如何为 Clarizen 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-clarizen-perform-the-following-steps"></a>若要为 Clarizen 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-clarizen-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-clarizen-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加一个应用程序](./media/active-directory-saas-clarizen-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜索框**中，键入“Clarizen”。
   
   ![应用程序库](./media/active-directory-saas-clarizen-tutorial/IC784680.png "Application Gallery")
7. 在结果窗格中，选择“Clarizen”，然后单击“完成”以添加该应用程序。
   
   ![Clarizen](./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Clarizen 证明自己的身份。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中，在 **Clarizen** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-clarizen-tutorial/IC784682.png "Configure Single Sign-On")
2. 在“你希望用户如何登录到 Clarizen”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-clarizen-tutorial/IC784683.png "Configure Single Sign-On")
3. 在“在 Clarizen 处配置单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-clarizen-tutorial/IC784684.png "Configure Single Sign-On")
4. 在另一个 Web 浏览器窗口中，以管理员身份登录到你的 **Clarizen** 公司站点（例如：*https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*）。
5. 单击你的用户名，然后单击“设置”。
   
   ![设置](./media/active-directory-saas-clarizen-tutorial/IC784685.png "Settings")
6. 单击“全局设置”选项卡，然后单击“联合身份验证”旁边的“编辑”。
   
   ![全局设置](./media/active-directory-saas-clarizen-tutorial/IC786906.png "Global Settings")
7. 在“联合身份验证”对话框中，执行以下步骤：
   
   ![联合身份验证](./media/active-directory-saas-clarizen-tutorial/IC785892.png "Federated Authentication")
   
   1. 单击“上载”以上载所下载的证书。
   2. 在 Azure 经典门户中，在“在 Clarizen 处配置单一登录”对话框页上，复制“单一登录服务 URL”值，然后将其粘贴到“登录 URL”文本框中。
   3. 在 Azure 经典门户中，在“在 Clarizen 处配置单一注销”对话框页上，复制“单一注销服务 URL”值，然后将其粘贴到“注销 URL”文本框中。
   4. 选择“使用 POST”。
   5. 单击“保存”。
8. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-clarizen-tutorial/IC784688.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>配置用户预配

为了使 Azure AD 用户能够登录到 Clarizen，必须将其预配到 Clarizen 中。  
对于 Clarizen，需要手动执行预配。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 以管理员身份登录到你的 **Clarizen** 公司站点。
2. 单击“人员”。
   
   ![人员](./media/active-directory-saas-clarizen-tutorial/IC784689.png "People")
3. 单击“邀请用户”。
   
   ![邀请用户](./media/active-directory-saas-clarizen-tutorial/IC784690.png "Invite Users")
4. 在“邀请人员”对话框页上，执行以下步骤：
   
   ![邀请人员](./media/active-directory-saas-clarizen-tutorial/IC784691.png "Invite People")
   
   1. 在“电子邮件”文本框中，键入要预配的有效 Azure Active Directory 帐户的电子邮件地址。
   2. 单击“邀请”。
   
   > [!NOTE]
   > Azure Active Directory 帐户持有者将收到一封电子邮件，并且将单击其中的链接以在激活帐户前确认帐户。
   > 
   > 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要向希望能够通过应用程序访问配置的 Azure AD 用户分配访问权限，从而向他们授予该权限。

### <a name="to-assign-users-to-clarizen-perform-the-following-steps"></a>若要将用户分配到 Clarizen，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在 **Clarizen** 应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-clarizen-tutorial/IC784692.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-clarizen-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


