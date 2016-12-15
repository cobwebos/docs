---
title: "教程：Azure Active Directory 与 BlueJeans 的集成 | Microsoft Docs"
description: "了解如何使用 BlueJeans 与 Azure Active Directory 启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 00f00d91e54d35fb9009a3e927dfc8833cd7571e


---
# <a name="tutorial-azure-ad-integration-with-bluejeans"></a>教程：Azure AD 与 BlueJeans 的集成
本教程旨在介绍 Azure 和 BlueJeans 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 一个支持 BlueJeans 单一登录的订阅

完成本教程后，已分配到 BlueJeans 的 Azure AD 用户将能够在 BlueJeans 公司站点（服务提供商启动的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序中。

在本教程中概述的方案由以下构建基块组成：

1. 支持 BlueJeans 的应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-bluejeans-tutorial/IC785860.png "Scenario")

## <a name="enabling-the-application-integration-for-bluejeans"></a>支持 BlueJeans 的应用程序集成
本部分旨在概述如何支持 BlueJeans 的应用程序集成。

### <a name="to-enable-the-application-integration-for-bluejeans-perform-the-following-steps"></a>若要支持 BlueJeans 的应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-bluejeans-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加一个应用程序](./media/active-directory-saas-bluejeans-tutorial/IC749322.png "Add an application from gallerry")
6. 在“搜索框”中，键入 **BlueJeans**。
   
   ![应用程序库](./media/active-directory-saas-bluejeans-tutorial/IC785861.png "Application Gallery")
7. 在结果窗格中，选择“BlueJeans”，然后单击“完成”添加应用程序。
   
   ![BlueJeans](./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

此部分旨在概述如何使用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户对 BlueJeans 进行身份验证。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的 **BlueJeans** 应用程序集成页上，单击“配置单一登录”打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-bluejeans-tutorial/IC785863.png "Configure single sign-on")
2. 在“你希望用户如何登录 BlueJeans”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Configure Single Sign-On")
3. 在“配置应用 URL”页上的“BlueJeans 登录 URL”文本框中，使用以下模式“*https://company.BlueJeans.com*”键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-bluejeans-tutorial/IC785865.png "Configure App URL")
4. 在“配置 BlueJeans 的单一登录”页上，若要下载证书，请单击“下载证书”，然后在计算机上保存该证书文件。
   
   ![配置单一登录](./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Configure Single Sign-On")
5. 在其他 Web 浏览器窗口中，以管理员身份登录 **BlueJeans** 公司站点。
6. 依次转到“管理员”\>“组设置”\>“安全”。
   
   ![管理员](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Admin")
7. 在“安全”部分中，执行以下步骤：
   
   ![SAML 单一登录](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML Single Sign On")
   
   1. 选择“SAML 单一登录”。
   2. 选择“启用自动预配”。
8. 使用以下步骤继续：
   
   ![证书路径](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Certificate Path")
   
   1. 单击“选择文件”，然后上载所下载的证书。
   2. 在 Azure 经典门户的“配置 BlueJeans 的单一登录”对话框页面上，复制“远程登录 URL”值，然后将其粘贴到“登录 URL”文本框。
   3. 在 Azure 经典门户的“配置 BlueJeans 的单一登录”对话框页面上，复制“更改密码 URL”值，然后将其粘贴到“密码更改 URL”文本框。
   4. 在 Azure 经典门户的“配置 BlueJeans 的单一登录”对话框页面上，复制“远程注销 URL”值，然后将其粘贴到“注销 URL”文本框。
9. 使用以下步骤继续：
   
   ![保存更改](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Save Changes")
   
   1. 在“用户 ID”文本框中，键入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**。
   2. 在“电子邮件”文本框中，键入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**。
   3. 单击“保存更改”。
10. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”以关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户登录到 BlueJeans，必须将它们预配到 BlueJeans。  
对于 BlueJeans，预配是手动任务。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 以管理员身份登录到 **BlueJeans** 公司站点。
2. 依次转到“管理员”\>“管理用户”\>“添加用户”。
   
   ![管理员](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Admin")
   
   > [!IMPORTANT]
   > “添加用户”选项卡仅在“安全”选项卡中的的“启用自动预配”处于未选中状态时可用。
   > 
   > 
3. 在“添加用户”部分中，执行以下步骤：
   
   ![添加用户](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Add User")
   
   1. 在相关文本框中键入 **BlueJeans 用户名**、**电子邮件地址**、**BlueJeans 会议 ID**、**审查方密码**、**全名**、要配置的有效 AAD 帐户的**公司**。
   2. 单击“添加用户”。

> [!NOTE]
> 可使用 BlueJeans 提供的任何其他 BlueJeans 用户帐户创建工具或 API 预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要向希望能够通过应用程序访问配置的 Azure AD 用户分配访问权限，从而向他们授予该权限。

### <a name="to-assign-users-to-bluejeans-perform-the-following-steps"></a>若要将用户分配到 BlueJeans，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在 **BlueJeans **应用程序集成页面上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-bluejeans-tutorial/IC785887.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-bluejeans-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


