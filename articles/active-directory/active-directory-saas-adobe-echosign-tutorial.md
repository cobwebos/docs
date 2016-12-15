---
title: "教程：Azure Active Directory 与 Adobe EchoSign 集成 | Microsoft Docs"
description: "了解如何将 Adobe EchoSign 与 Azure Active Directory 结合使用以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 63c93a9f6812607cf8ce98f4f32bcb2c8b8823c3


---
# <a name="tutorial-azure-active-directory-integration-with-adobe-echosign"></a>教程：Azure Active Directory 与 Adobe EchoSign 集成
本教程旨在展示 Azure 与 Adobe EchoSign 集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 启用了 Adobe EchoSign 单一登录的订阅

完成本教程后，分配到 Adobe EchoSign 的 Azure AD 用户将能够在 Adobe EchoSign 公司站点（服务提供商启动的登录）或通过阅读[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 支持 Adobe EchoSign 的应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-adobe-echosign-tutorial/IC789511.png "Scenario")

## <a name="enabling-the-application-integration-for-adobe-echosign"></a>支持 Adobe EchoSign 的应用程序集成
本部分旨在概述如何为 Adobe EchoSign 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-adobe-echosign-perform-the-following-steps"></a>若要支持 Adobe EchoSign 的应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-adobe-echosign-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-adobe-echosign-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-adobe-echosign-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加一个应用程序](./media/active-directory-saas-adobe-echosign-tutorial/IC749322.png "Add an application from gallerry")
6. 在“搜索框”中，键入“Adobe EchoSign”。
   
   ![应用程序库](./media/active-directory-saas-adobe-echosign-tutorial/IC789514.png "Application Gallery")
7. 在“结果”窗格中，选择“Adobe EchoSign”，然后单击“完成”，添加该应用程序。
   
   ![Adobe EchoSign](./media/active-directory-saas-adobe-echosign-tutorial/IC789515.png "Adobe EchoSign")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分旨在概述如何使用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户对 Adobe EchoSign 进行身份验证。  
作为此过程的一部分，要求创建 base-64 编码的证书文件。  
如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户的“Adobe EchoSign”应用程序集成页面上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-adobe-echosign-tutorial/IC789516.png "Configure Single Sign-On")
2. 在“你希望用户如何登录 Adobe EchoSign”页面上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-adobe-echosign-tutorial/IC789517.png "Configure Single Sign-On")
3. 在“配置应用 URL”页上的“Adobe EchoSign 登录 URL”文本框中，使用以下模式“*https://company.echosign.com/*”键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-adobe-echosign-tutorial/IC789518.png "Configure App URL")
4. 在“配置 Adobe EchoSign 的单一登录”页上，请单击“下载证书”，然后在计算机上保存该证书文件。
   
   ![配置单一登录](./media/active-directory-saas-adobe-echosign-tutorial/IC789519.png "Configure Single Sign-On")
5. 在其他 Web 浏览器窗口中，以管理员身份登录 Adobe EchoSign 公司站点。
6. 在顶部菜单中，单击“帐户”，然后在左侧骰子的导航窗格中，单击“帐户设置”下的“SAML 设置”。
   
   ![帐户](./media/active-directory-saas-adobe-echosign-tutorial/IC789520.png "Account")
7. 在“SAML 设置”部分中执行以下步骤：
   
   ![SAML 设置](./media/active-directory-saas-adobe-echosign-tutorial/IC789521.png "SAML Settings")
   
   1. 对于“SAML 模式”，选择“SAML 强制”。
   2. 选择“允许 EchoSign 帐户管理员使用 EchoSign 凭据登录”。
   3. 对于“用户创建”，选择“自动添加通过 SAML 验证了身份的用户”。
8. 继续执行以下步骤：
   
   ![SAML 设置](./media/active-directory-saas-adobe-echosign-tutorial/IC789522.png "SAML Settings")
   
   1. 在 Azure 经典门户的“配置 Adobe EchoSign 的单一登录”对话框页面上，复制“实体 ID”值，然后将其粘贴到“IdP 实体 ID”文本框。
   2. 在 Azure 经典门户的“配置 Adobe EchoSign 的单一登录”对话框页面上，复制“远程登录 URL”值，然后将其粘贴到“IdP 登录 URL”文本框。
   3. 在 Azure 经典门户的“配置 Adobe EchoSign 的单一登录”对话框页面上，复制“远程注销 URL”值，然后将其粘贴到“IdP 注销 URL”文本框。
   4. 基于下载的证书创建一个 **base-64 编码**的文件。  
      
      > [!TIP]
      > 有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   5. 在记事本中打开 base-64 编码证书，将内容复制到剪贴板，然后将其粘贴到“IdP 证书”文本框
   6. 单击“保存更改”。
9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”以关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-adobe-echosign-tutorial/IC789523.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录 Adobe EchoSign，必须对其进行预配才能使其登录 Adobe EchoSign。  
就 Adobe EchoSign 来说，预配任务需要手动完成。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 以管理员身份登录 **Adobe EchoSign** 公司站点。
2. 在顶部菜单中，单击“帐户”，然后在左侧骰子的导航窗格中，单击“用户和组”，再单击“创建新用户”。
   
   ![帐户](./media/active-directory-saas-adobe-echosign-tutorial/IC789524.png "Account")
3. 在“创建新用户”部分中，执行以下步骤：
   
   ![创建用户](./media/active-directory-saas-adobe-echosign-tutorial/IC789525.png "Create User")
   
   1. 键入希望在相关文本框中预配的有效 AAD 帐户的“电子邮件地址”、“名字”和“姓氏”。
   2. 单击“创建用户”。
      
      > [!NOTE]
      > Azure Active Directory 帐户持有者将收到一封电子邮件，该邮件包含在激活帐户前要确认帐户的链接。
      > 
      > 

> [!NOTE]
> 可以使用任何其他 Adobe EchoSign 用户帐户创建工具或 Adobe EchoSign 提供的 API 预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要向希望能够通过应用程序访问配置的 Azure AD 用户分配访问权限，从而向他们授予该权限。

### <a name="to-assign-users-to-adobe-echosign-perform-the-following-steps"></a>若要将用户分配到 Adobe EchoSign，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Adobe EchoSign”应用程序集成页面上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-adobe-echosign-tutorial/IC789526.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-adobe-echosign-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


