---
title: "教程：Azure Active Directory 与 UserVoice 集成 | Microsoft Docs"
description: "了解如何使用 UserVoice 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0bb23a4c2be40c6304d7a09a6ff978db0666ea18


---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>教程：Azure Active Directory 与 UserVoice 集成
本教程的目的是说明 Azure 与 UserVoice 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* UserVoice 租户

完成本教程后，已分配到 UserVoice 的 Azure AD 用户可在 UserVoice 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 启用 UserVoice 的应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-uservoice-tutorial/IC777514.png "Scenario")

## <a name="enabling-the-application-integration-for-uservoice"></a>启用 UserVoice 的应用程序集成
本部分旨在概述如何启用 UserVoice 的应用程序集成。

### <a name="to-enable-the-application-integration-for-uservoice-perform-the-following-steps"></a>若要启用 UserVoice 的应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-uservoice-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-uservoice-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-uservoice-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-uservoice-tutorial/IC749322.png "Add an application from gallerry")
6. 在“搜索框”中，键入“UserVoice”。
   
   ![应用程序库](./media/active-directory-saas-uservoice-tutorial/IC777513.png "Application gallery")
7. 在“结果”窗格中，选择“UserVoice”，然后单击“完成”以添加该应用程序。
   
   ![UserVoice](./media/active-directory-saas-uservoice-tutorial/IC777810.png "UserVoice")

## <a name="configuring-single-sign-on"></a>配置单一登录
此部分旨在概述如何使用基于 SAML 协议的联合身份验证，使用户通过其 Azure AD 中的帐户对 UserVoice 进行身份验证。  
配置 UserVoice 的单一登录需要检索证书的指纹值。  
如果不熟悉此过程，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户的“UserVoice”应用程序集成页上，单击“配置单一登录”打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-uservoice-tutorial/IC777515.png "Configure single sign-on")
2. 在“你希望用户如何登录 UserVoice”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-uservoice-tutorial/IC777516.png "Configure single sign-on")
3. 在“配置应用 URL”页上的“UserVoice 登录 URL”文本框中，使用以下模式“https://\<tenant-name\>.UserVoice.com”键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-uservoice-tutorial/IC777517.png "Configure App URL")
4. 在“配置 UserVoice 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件本地保存为“c:\\UserVoice.cer”。
   
   ![配置单一登录](./media/active-directory-saas-uservoice-tutorial/IC777518.png "Configure single sign-on")
5. 在其他 Web 浏览器窗口中，以管理员身份登录到 UserVoice 公司站点。
6. 在顶部工具栏中，单击“设置”，然后从菜单中选择“Web 门户”。
   
   ![设置](./media/active-directory-saas-uservoice-tutorial/IC777519.png "Settings")
7. 在“Web 门户”选项卡的“用户身份验证”部分中，单击“编辑”以打开“编辑用户身份验证”对话框页
   
   ![Web 门户](./media/active-directory-saas-uservoice-tutorial/IC777520.png "Web portal")
8. 在“编辑用户身份验证”对话框页上，执行以下步骤：
   
   ![编辑用户身份验证](./media/active-directory-saas-uservoice-tutorial/IC777521.png "Edit user authentication")
   
   1. 单击“单一登录(SSO)”。
   2. 在 Azure 经典门户的“配置 UserVoice 的单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“SSO 远程登录”文本框。
   3. 在 Azure 经典门户的“配置 UserVoice 的单一登录”对话框页上，复制“远程注销 URL”值，然后将其粘贴到“SSO 远程注销”文本框。
   4. 从导出的证书中复制“指纹”值，然后将其粘贴到“当前证书 SHA1 指纹”文本框中。  
      
      > [!TIP]
      > 有关详细信息，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)
      > 
      > 
   5. 单击“保存身份验证设置”。
9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-uservoice-tutorial/IC777522.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>配置用户设置
为了使 Azure AD 用户能够登录到 UserVoice，必须将其预配到 UserVoice 中。  
对于 UserVoice，需要手动执行预配。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 登录到 **UserVoice** 租户。
2. 转到“设置”。
   
   ![设置](./media/active-directory-saas-uservoice-tutorial/IC777811.png "Settings")
3. 单击“常规”。
4. 单击“代理和权限”。
   
   ![代理和权限](./media/active-directory-saas-uservoice-tutorial/IC777812.png "Agents and permissions")
5. 单击“添加管理员”。
   
   ![添加管理员](./media/active-directory-saas-uservoice-tutorial/IC777813.png "Add admins")
6. 在“邀请管理员”对话框中，执行以下步骤：
   
   ![邀请管理员](./media/active-directory-saas-uservoice-tutorial/IC777814.png "Invite admins")
   
   1. 在“电子邮件”文本框中，键入要预配帐户的电子邮件地址，然后单击“添加”。
   2. 单击“邀请”。

> [!NOTE]
> 可以使用 UserVoice 提供的任何其他 UserVoice 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-uservoice-perform-the-following-steps"></a>若要将用户分配到 UserVoice，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“UserVoice”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-uservoice-tutorial/IC777523.png "Assign users")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-uservoice-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


