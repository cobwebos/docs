---
title: "教程：Azure Active Directory 与 Spring CM 集成 | Microsoft 文档"
description: "了解如何使用 Spring CM 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9f821f2e8bf1a545a437961ee3cbee78bb6be2eb


---
# <a name="tutorial-azure-active-directory-integration-with-spring-cm"></a>教程：Azure Active Directory 与 Spring CM 集成
本教程的目的是介绍如何在 Azure Active Directory 和 Spring CM 之间设置单一登录。

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 SpringCM 单一登录的订阅

完成本教程后，已向 SpringCM 分配的 Azure Active Directory 用户将能够使用 AAD 访问面板单一登录。

1. 为 SpringCM 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-spring-cm-tutorial/IC797044.png "Scenario")

## <a name="enabling-the-application-integration-for-springcm"></a>为 SpringCM 启用应用程序集成
本部分的目的是概述如何为 SpringCM 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-springcm-perform-the-following-steps"></a>若要为 SpringCM 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-spring-cm-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-spring-cm-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-spring-cm-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-spring-cm-tutorial/IC749322.png "Add an application from gallerry")
6. 在搜索框中，键入“SpringCM”。
   
   ![应用程序库](./media/active-directory-saas-spring-cm-tutorial/IC797045.png "Application Gallery")
7. 在结果窗格中，选择“SpringCM”，然后单击“完成”以添加该应用程序。
   
   ![SpringCM](./media/active-directory-saas-spring-cm-tutorial/IC797046.png "SpringCM")

## <a name="configuring-single-sign-on"></a>配置单一登录
本部分概述如何让用户使用基于 SAML 协议的联合身份验证通过其在 Azure Active Directory 中的帐户向 SpringCM 进行身份验证。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“SpringCM”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-spring-cm-tutorial/IC797047.png "Configure single Sign-On")
2. 在“你希望用户如何登录 SpringCM”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-spring-cm-tutorial/IC797048.png "Configure single Sign-On")
3. 在“配置应用 URL”页的“SpringCM 登录 URL”文本框中，键入用户用于登录 SpringCM 应用程序的 URL，然后单击“下一步”。 
   
   此应用 URL 是 SpringCM 租户 URL（例如：*https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=16826*）：
   
   ![配置应用 URL](./media/active-directory-saas-spring-cm-tutorial/IC797049.png "Configure App URL")
4. 在“配置 SpringCM 的单一登录”页上，若要下载证书，请单击“下载证书”，然后在计算机上本地保存该证书文件。
   
   ![配置单一登录](./media/active-directory-saas-spring-cm-tutorial/IC797050.png "Configure Single SignOn")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录 **SpringCM** 公司站点。
6. 在顶部菜单中，单击“转到”，单击“首选项”，然后在“帐户首选项”部分中，单击“SAML SSO”。
   
   ![SAML SSO](./media/active-directory-saas-spring-cm-tutorial/IC797051.png "SAML SSO")
7. 在“标识提供者配置”部分执行以下步骤：
   
   ![标识提供者配置](./media/active-directory-saas-spring-cm-tutorial/IC797052.png "Identity Provider Configuration")
   
   1. 若要上载已下载的 Azure Active Directory 证书，请单击“选择颁发者证书”或“更改颁发者证书”。
   2. 在 Azure 经典门户的“配置 SpringCM 的单一登录”页上，复制“颁发者 URL”值，然后将其粘贴到“颁发者”文本框中。
   3. 在 Azure 经典门户的“配置 SpringCM 的单一登录”页上，复制“单一登录服务 URL”值，然后将其粘贴到“服务提供商(SP)启动的终结点”文本框中。
   4. 对于“启用 SAML”，选择“启用”。
   5. 单击“保存” 。
8. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-spring-cm-tutorial/IC797053.png "Configure Single SignOn")

## <a name="configuring-user-provisioning"></a>配置用户设置
要使 Azure AD 用户能够登录 SpringCM，必须将这些用户预配到 SpringCM 中。  
对于 SpringCM，预配是一项手动任务。

> [!NOTE]
> 有关更多详细信息，请参阅[创建和编辑 SpringCM 用户](http://knowledge.springcm.com/create-and-edit-a-springcm-user)
> 
> 

### <a name="to-provision-a-user-account-to-springcm-perform-the-following-steps"></a>若要将用户帐户预配到 SpringCM，请执行以下步骤：
1. 以管理员身份登录 **SpringCM** 公司站点。
2. 单击“转到”，然后单击“通讯簿”。
   
   ![创建用户](./media/active-directory-saas-spring-cm-tutorial/IC797054.png "Create User")
3. 单击“创建用户”。
4. 选择**用户角色**。
5. 选择“发送激活电子邮件”。
6. 在相关文本框中键入要预配的有效 Azure Active Directory 用户帐户的名字、姓氏和电子邮件地址。
7. 将用户添加到某个**安全组**。
8. 单击“保存” 。

> [!NOTE]
> 可以使用任何其他 SpringCM 用户帐户创建工具或 SpringCM 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

### <a name="to-assign-users-to-springcm-perform-the-following-steps"></a>若要将用户分配到 SpringCM，请执行以下步骤：
1. 在 Azure 经典门户中，创建一个测试帐户。
2. 在“SpringCM”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-spring-cm-tutorial/IC797055.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-spring-cm-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


