---
title: "教程：Azure Active Directory 与 Work.com 的集成 | Microsoft Docs"
description: "了解如何使用 Work.com 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5159115742ffda1951b0489864764065a6a3bec5


---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>教程：Azure Active Directory 与 Work.com 的集成
本教程的目的是说明 Azure 与 Work.com 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 Work.com 单一登录的订阅

完成本教程后，已分配有 Work.com 访问权限的 AAD 用户可在 Work.com 公司站点（服务提供商发起的登录）单一登录到应用程序或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)。

在本教程中概述的方案由以下构建基块组成：

1. 启用 Work.com 的应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-work-com-tutorial/IC794105.png "Scenario")

## <a name="enabling-the-application-integration-for-workcom"></a>启用 Work.com 的应用程序集成
本部分旨在概述如何启用 Work.com 的应用程序集成。

### <a name="to-enable-the-application-integration-for-workcom-perform-the-following-steps"></a>若要启用 Work.com 的应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-work-com-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-work-com-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-work-com-tutorial/IC749322.png "Add an application from gallerry")
6. 在“搜索框”中，键入“Work.com”。
   
   ![应用程序库](./media/active-directory-saas-work-com-tutorial/IC794106.png "Application Gallery")
7. 在“结果”窗格中，选择“Work.com”，然后单击“完成”以添加该应用程序。
   
   ![Work.com](./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

## <a name="configuring-single-sign-on"></a>配置单一登录
此部分旨在概述如何使用基于 SAML 协议的联合身份验证，使用户通过其 Azure AD 中的帐户对 Work.com 进行身份验证。  
在此过程中，需要将证书上传到 Work.com。

> [!NOTE]
> 若要配置单一登录，需要设置一个自定义 Work.com 域名。 需要至少定义一个域名，测试该域名，并将其部署到整个组织。
> 
> 

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 以管理员身份登录到 Work.com 租户。
2. 转到“设置”。
   
   ![设置](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")
3. 在左侧导航窗格中的“管理”部分中，单击“域管理”以展开相关部分，然后单击“我的域”，打开“我的域”页。 
   
   ![我的域](./media/active-directory-saas-work-com-tutorial/IC767825.png "My Domain")
4. 若要验证你的域是否已正确设置，请确保它在“步骤 4 部署到用户”中，并复查“我的域设置”。
   
   ![部署到用户的域](./media/active-directory-saas-work-com-tutorial/IC784377.png "Doman Deployed to User")
5. 在另一个 Web 浏览器窗口中，登录到 Azure 经典门户。
6. 在“Work.com”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-work-com-tutorial/IC794109.png "Configure Single Sign-On")
7. 在“你希望用户如何登录 Work.com”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-work-com-tutorial/IC794110.png "Configure Single Sign-On")
8. 在“配置应用 URL”页的“Work.com 登录 URL”文本框中，键入用户用于登录 Work.com 应用程序的 URL（例如：“http://company.my.salesforce.com”），然后单击“下一步”： 
   
   ![配置应用 URL](./media/active-directory-saas-work-com-tutorial/IC794111.png "Configure App URL")
9. 在“配置 Work.com 的单一登录”页面上，若要下载证书，请单击“下载证书”，然后将证书文件保存在本地计算机上。
   
   ![配置单一登录](./media/active-directory-saas-work-com-tutorial/IC794112.png "Configure Single Sign-On")
10. 登录到 Work.com 租户。
11. 转到“设置”。
    
    ![设置](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")
12. 展开“安全控件”菜单，然后单击“单一登录设置”。
    
    ![单一登录设置](./media/active-directory-saas-work-com-tutorial/IC794113.png "Single Sign-On Settings")
13. 在“单一登录设置”对话框页上，执行以下步骤：
    
    ![已启用 SAML](./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML Enabled")
    
    1. 选择“已启用 SAML”。
    2. 单击“新建” 。
14. 在“SAML 单一登录设置”部分中，执行以下步骤：
    
    ![SAML 单一登录设置](./media/active-directory-saas-work-com-tutorial/IC794114.png "SAML Single Sign-On Setting")
    
    1. 在“名称”文本框中，键入配置名称。  
       
       > [!NOTE]
       > 提供“名称”值时会自动填充“API 名称”文本框。
       > 
       > 
    2. 在 Azure 经典门户的“配置 Work.com 的单一登录”对话框页上，复制“颁发者 URL”值，然后将其粘贴到“颁发者”文本框中。
    3. 若要上传已下载的证书，请单击“浏览”。
    4. 在“实体 ID”文本框中，键入 **https://salesforce-work.com**。
    5. 对于“SAML 标识类型”，选择“断言包含用户对象的联合 ID”。
    6. 对于“SAML 标识位置”，选择“标识位于 Subject 语句的 NameIdentifier 元素中”。
    7. 在 Azure 经典门户的“配置 Work.com 的单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“标识提供者登录 URL”文本框中。
    8. 在 Azure 经典门户的“配置 Work.com 的单一登录”对话框页上，复制“远程注销 URL”值，然后将其粘贴到“标识提供者注销 URL”文本框中。
    9. 对于“服务提供程序发起的请求绑定”，请选择“HTTP Post”。
    10. 单击“保存” 。
15. 在 Work.com 经典门户内，从左侧导航窗格中，单击“域管理”以展开相关部分，然后单击“我的域”，打开“我的域”页。 
    
    ![我的域](./media/active-directory-saas-work-com-tutorial/IC794115.png "My Domain")
16. 在“我的域”页上的“登录页品牌打造”部分中，单击“编辑”。
    
    ![登录页品牌打造](./media/active-directory-saas-work-com-tutorial/IC767826.png "Login Page Branding")
17. 在“登录页品牌打造”页上的“身份验证服务”部分中，会显示 **SAML SSO 设置**的名称。 选择它，然后单击“保存”。
    
    ![登录页品牌打造](./media/active-directory-saas-work-com-tutorial/IC784366.png "Login Page Branding")
18. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-work-com-tutorial/IC794116.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>配置用户设置
要使 Azure Active Directory 用户能够登录，必须将这些用户预配到 Work.com 中。  
对于 Work.com，需要手动执行预配。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：
1. 以管理员身份登录到 Work.com 公司站点。
2. 转到“设置”。
   
   ![设置](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")
3. 转到“管理用户”\>“用户”。
   
   ![管理用户](./media/active-directory-saas-work-com-tutorial/IC784369.png "Manage Users")
4. 单击“新建用户”。
   
   ![所有用户](./media/active-directory-saas-work-com-tutorial/IC794117.png "All Users")
5. 在“用户编辑”部分中，执行以下步骤：
   
   ![用户编辑](./media/active-directory-saas-work-com-tutorial/IC794118.png "User Edit")
   
   1. 在相关文本框中键入要预配的有效 Azure Active Directory 帐户的“姓氏”、“别名”、“电子邮件”、“用户名”和“昵称”。
   2. 选择“角色”、“用户许可证”和“配置文件”。
   3. 单击“保存” 。  
      
      > [!NOTE]
      > Azure Active Directory 帐户持有者会收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。
      > 
      > 

> [!NOTE]
> 可以使用任何其他 Work.com 用户帐户创建工具或 Work.com 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-workcom-perform-the-following-steps"></a>若要将用户分配到 Work.com，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Work.com”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-work-com-tutorial/IC794119.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-work-com-tutorial/IC767830.png "Yes")

现在请等待 10 分钟，然后验证该帐户是否已同步到 Work.com 沙盒。

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


