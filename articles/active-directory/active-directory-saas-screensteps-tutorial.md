---
title: "教程：Azure Active Directory 与 ScreenSteps 集成 | Microsoft 文档"
description: "了解如何使用 ScreenSteps 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 9e097ed265381225deeda19642c281223907a4cc


---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>教程：Azure Active Directory 与 ScreenSteps 集成
本教程的目的是说明 Azure 与 ScreenSteps 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* ScreenSteps 租户

完成本教程后，已向 ScreenSteps 分配的 Azure AD 用户将能够在 ScreenSteps 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 ScreenSteps 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-screensteps-tutorial/IC778516.png "Scenario")

## <a name="enabling-the-application-integration-for-screensteps"></a>为 ScreenSteps 启用应用程序集成
本部分的目的是概述如何为 ScreenSteps 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-screensteps-perform-the-following-steps"></a>若要为 ScreenSteps 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-screensteps-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-screensteps-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-screensteps-tutorial/IC749322.png "Add an application from gallerry")
6. 在搜索框中，键入“ScreenSteps”。
   
   ![应用程序库](./media/active-directory-saas-screensteps-tutorial/IC778517.png "Application gallery")
7. 在结果窗格中，选择“ScreenSteps”，然后单击“完成”以添加该应用程序。
   
   ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 ScreenSteps 进行身份验证。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“ScreenSteps”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-screensteps-tutorial/IC778519.png "Configure single sign-on")
2. 在“你希望用户如何登录 ScreenSteps”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-screensteps-tutorial/IC778520.png "Configure single sign-on")
3. 在“配置应用 URL”页上的“ScreenSteps 登录 URL”文本框中，使用模式“*https://\<tenant-name\>.ScreenSteps.com*”键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-screensteps-tutorial/IC778521.png "Configure app URL")
4. 在“配置 ScreenSteps 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将该证书文件保存到计算机上。
   
   ![配置单一登录](./media/active-directory-saas-screensteps-tutorial/IC778522.png "Configure single sign-on")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录 ScreenSteps 公司站点。
6. 单击“帐户管理”。
   
   ![帐户管理](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Account management")
7. 单击“远程身份验证”。
   
   ![远程身份验证](./media/active-directory-saas-screensteps-tutorial/IC778524.png "Remote authentication")
8. 单击“创建身份验证终结点”。
   
   ![远程身份验证](./media/active-directory-saas-screensteps-tutorial/IC778525.png "Remote authentication")
9. 在“创建身份验证终结点”部分中，执行以下步骤：
   
   ![创建身份验证终结点](./media/active-directory-saas-screensteps-tutorial/IC778526.png "Create an authentication endpoint")
   
   1. 在“标题”文本框中，键入标题。
   2. 从“模式”列表中，选择“SAML”。
   3. 单击“创建” 。
10. 在“远程身份验证终结点”部分中，执行以下步骤：
    
    ![远程身份验证终结点](./media/active-directory-saas-screensteps-tutorial/IC778527.png "Remote authentication endpoint")
    
    1. 在 Azure 经典门户的“配置 ScreenSteps 的单一登录”页上，复制“远程登录 URL”值，然后将其粘贴到“远程登录 URL”文本框中。
    2. 在 Azure 经典门户的“配置 ScreenSteps 的单一登录”页上，复制“远程注销 URL”值，然后将其粘贴到“注销 URL”文本框中。
    3. 单击“选择文件”，然后上载已下载的证书。
    4. 单击“更新”。
11. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-screensteps-tutorial/IC778542.png "Configure single sign-on")
    
    ## <a name="configuring-user-provisioning"></a>配置用户设置

要使 Azure AD 用户能够登录 **ScreenSteps**，必须将这些用户预配到 **ScreenSteps** 中。  
对于 **ScreenSteps**，预配是一项手动任务。

### <a name="to-provision-a-user-account-to-screensteps-perform-the-following-steps"></a>若要将用户帐户预配到 ScreenSteps，请执行以下步骤：
1. 登录到 **ScreenSteps** 租户。
2. 单击“帐户管理”。
   
   ![帐户管理](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Account management")
3. 单击“用户”。
   
   ![用户](./media/active-directory-saas-screensteps-tutorial/IC778544.png "Users")
4. 单击“创建用户”。
   
   ![所有用户](./media/active-directory-saas-screensteps-tutorial/IC778545.png "All Users")
5. 从“用户角色”列表中，选择用户的角色。
6. 在“用户角色”部分的相关文本框中，键入要预配的有效 AAD 帐户的**名字**、**姓氏**、**电子邮件**、**登录名**、**密码**和**密码确认**。
   
   ![新建用户](./media/active-directory-saas-screensteps-tutorial/IC778546.png "New user")
7. 在“组”部分中，选择“身份验证组(SAML)”，然后单击“创建用户”。
   
   ![组](./media/active-directory-saas-screensteps-tutorial/IC778547.png "Groups")

> [!NOTE]
> 可以使用任何其他 ScreenSteps 用户帐户创建工具或 ScreenSteps 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

### <a name="to-assign-users-to-screensteps-perform-the-following-steps"></a>若要将用户分配到 ScreenSteps，请执行以下步骤：
1. 在 Azure 经典门户中，创建一个测试帐户。
2. 在“ScreenSteps”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-screensteps-tutorial/IC773094.png "Assign users")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![分配用户](./media/active-directory-saas-screensteps-tutorial/IC778548.png "Assign users")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


