---
title: "教程：Azure Active Directory 与 Sprinklr 集成 | Microsoft 文档"
description: "了解如何使用 Sprinklr 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d8e8e65ead26ad8cd6829bf7fb67251b7a39e5b8


---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>教程：Azure Active Directory 与 Sprinklr 集成
本教程的目的是说明 Azure 与 Sprinklr 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Sprinklr 租户

完成本教程后，已向 Sprinklr 分配的 Azure AD 用户将能够在 Sprinklr 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Sprinklr 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-sprinklr-tutorial/IC782900.png "Scenario")

## <a name="enabling-the-application-integration-for-sprinklr"></a>为 Sprinklr 启用应用程序集成
本部分的目的是概述如何为 Sprinklr 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-sprinklr-perform-the-following-steps"></a>若要为 Sprinklr 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-sprinklr-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-sprinklr-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-sprinklr-tutorial/IC749322.png "Add an application from gallerry")
6. 在搜索框中，键入“Sprinklr”。
   
   ![应用程序库](./media/active-directory-saas-sprinklr-tutorial/IC782901.png "Application Gallery")
7. 在结果窗格中，选择“Sprinklr”，然后单击“完成”以添加该应用程序。
   
   ![Sprinklr](./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

## <a name="configuring-single-sign-on"></a>配置单一登录
本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Sprinklr 进行身份验证。  
在此过程中，需要创建 base-64 编码的证书文件。  
如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“Sprinklr”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-sprinklr-tutorial/IC782903.png "Configure single sign-on")
2. 在“你希望用户如何登录 Sprinklr”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-sprinklr-tutorial/IC782904.png "Configure single sign-on")
3. 在“配置应用 URL”页上的“Sprinklr 登录 URL”文本框中，使用模式“*https://\<tenant-name\>.sprinklr.com*”键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-sprinklr-tutorial/IC782905.png "Configure App URL")
4. 在“配置 Sprinklr 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-sprinklr-tutorial/IC782906.png "Configure single sign-on")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录 Sprinklr 公司站点。
6. 转到“管理”\>“设置”。
   
   ![管理](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administration")
7. 从左侧窗格转到“管理合作伙伴”\>“单一登录”。
   
   ![管理合作伙伴](./media/active-directory-saas-sprinklr-tutorial/IC782908.png "Manage Partner")
8. 单击“+添加单一登录”。
   
   ![单一登录](./media/active-directory-saas-sprinklr-tutorial/IC782909.png "Single Sign-Ons")
9. 在“单一登录”页上，执行以下步骤：
   
   ![单一登录](./media/active-directory-saas-sprinklr-tutorial/IC782910.png "Single Sign-Ons")
   
   1. 在“名称”文本框中，键入配置名称（例如：*WAADSSOTest*）。
   2. 选择“启用”。
   3. 选择“使用新 SSO 证书”。
   4. 基于下载的证书创建一个 **base-64 编码**的文件。  
      
      > [!TIP]
      > 有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   5. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后再粘贴到“标识提供者证书”文本框中。
   6. 在 Azure 经典门户的“配置 Sprinklr 的单一登录”对话框页上，复制“标识提供者 ID”值，然后将其粘贴到“实体 ID”文本框中。
   7. 在 Azure 经典门户的“配置 Sprinklr 的单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“标识提供者登录 URL”文本框中。
   8. 在 Azure 经典门户的“配置 Sprinklr 的单一登录”对话框页上，复制“远程注销 URL”值，然后将其粘贴到“标识提供者注销 URL”文本框中。
   9. 对于“SAML 用户 ID 类型”，请选择“断言包含用户的 sprinklr.com 用户名”。
   10. 对于“SAML 用户 ID 位置”，请选择“用户 ID 位于 Subject 语句的 NameIdentifier 元素中”。
   11. 单击“保存”。
       
       ![SAML](./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")
10. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-sprinklr-tutorial/IC782912.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>配置用户设置
要使 AAD 用户能够登录进行访问，必须将这些用户预配到 Sprinklr 应用程序中。  
本部分介绍如何在 Sprinklr 中创建 AAD 用户帐户。

### <a name="to-provision-a-user-account-in-sprinklr-perform-the-following-steps"></a>若要在 Sprinklr 中预配用户帐户，请执行以下步骤：
1. 以管理员身份登录 Sprinklr 公司站点。
2. 转到“管理”\>“设置”。
   
   ![管理](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administration")
3. 从左侧窗格转到“管理客户端”\>“用户”。
   
   ![设置](./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Settings")
4. 单击“添加用户”。
   
   ![设置](./media/active-directory-saas-sprinklr-tutorial/IC782915.png "Settings")
5. 在“编辑用户”对话框中，执行以下步骤：
   
   ![编辑用户](./media/active-directory-saas-sprinklr-tutorial/IC782916.png "Edit user")
   
   1. 在“电子邮件”、“名字”和“姓氏”文本框中，键入要预配的 Azure AD 用户帐户的信息。
   2. 选择“禁用密码”。
   3. 选择一种**语言**。
   4. 选择**用户类型**。
   5. 单击“更新”。
   
   > [!IMPORTANT]
   > 必须选择“禁用密码”才能让用户通过标识提供者登录。
   > 
   > 
6. 单击“角色”，然后执行以下步骤：
   
   ![合作伙伴角色](./media/active-directory-saas-sprinklr-tutorial/IC782917.png "Partner Roles")
   
   1. 从“全局”列表中，选择“ALL\_Permissions”。
   2. 单击“更新”。

> [!NOTE]
> 可以使用任何其他 Sprinklr 用户帐户创建工具或 Sprinklr 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

### <a name="to-assign-users-to-sprinklr-perform-the-following-steps"></a>若要将用户分配到 Sprinklr，请执行以下步骤：
1. 在 Azure 经典门户中，创建一个测试帐户。
2. 在“Sprinklr”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-sprinklr-tutorial/IC782918.png "Assign users")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-sprinklr-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


