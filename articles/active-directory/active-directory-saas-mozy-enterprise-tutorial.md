---
title: "教程：Azure Active Directory 与 Mozy Enterprise 集成 | Microsoft 文档"
description: "了解如何将 Mozy Enterprise 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多内容！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d6034434c289936089afbb0fa5bd803363cde882


---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>教程：Azure Active Directory 与 Mozy Enterprise 的集成
本教程的目的是展示 Azure 与 Mozy Enterprise 的集成。  
在本教程中概述的方案假定已具有以下各项：

* 一个有效的 Azure 订阅
* 一个 Mozy Enterprise 租户

完成本教程后，分配到 Mozy Enterprise 的 Azure AD 用户将能够在你的 Mozy Enterprise 公司站点上（服务提供商启动的登录）或者使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以单一登录方式登录到应用程序。

本教程中概述的方案包括以下构建基块：

1. 为 Mozy Enterprise 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-mozy-enterprise-tutorial/IC777308.png "Scenario")

## <a name="enabling-the-application-integration-for-mozy-enterprise"></a>为 Mozy Enterprise 启用应用程序集成
本部分的目的是概述如何为 Mozy Enterprise 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-mozy-enterprise-perform-the-following-steps"></a>若要为 Mozy Enterprise 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户中，在左侧导航窗格上，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-mozy-enterprise-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-mozy-enterprise-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-mozy-enterprise-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-mozy-enterprise-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜索框**中，键入“mozy enterprise”。
   
   ![应用程序库](./media/active-directory-saas-mozy-enterprise-tutorial/IC777309.png "Application Gallery")
7. 在结果窗格中，选择“Mozy Enterprise”，然后单击“完成”以添加该应用程序。
   
   ![Mozy Enterprise](./media/active-directory-saas-mozy-enterprise-tutorial/IC777310.png "Mozy Enterprise")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Mozy Enterprise 证明自己的身份。  
在此过程中，需要将 base-64 编码的证书上载到 Mozy Enterprise 租户。  
如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中，在 **Mozy Enterprise** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-mozy-enterprise-tutorial/IC771709.png "Configure single sign-on")
2. 在“你希望用户如何登录到 Mozy Enterprise”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-mozy-enterprise-tutorial/IC777311.png "Configure single sign-on")
3. 在“配置应用 URL”页上，在“Mozy Enterprise 登录 URL”文本框中，使用“*https://\<tenant-name\>.Mozyenterprise.com*”模式键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-mozy-enterprise-tutorial/IC777312.png "Configure app URL")
4. 在“在 Mozy Enterprise 处配置单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-mozy-enterprise-tutorial/IC777313.png "Configure single sign-on")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到你的 Mozy Enterprise 公司站点。
6. 在“配置”部分中，单击“身份验证策略”。
   
   ![身份验证策略](./media/active-directory-saas-mozy-enterprise-tutorial/IC777314.png "Authentication policy")
7. 在“身份验证策略”部分中，执行以下步骤：
   
   ![身份验证策略](./media/active-directory-saas-mozy-enterprise-tutorial/IC777315.png "Authentication policy")
   
   1. 对于“提供程序”，选择“目录服务”。
   2. 选择“使用 LDAP 推送”。
   3. 单击“SAML 身份验证”选项卡。
   4. 在 Azure 经典门户中，在“在 Mozy Enterprise 处配置单一登录”对话框页上，复制“身份验证请求 URL”值，然后将其粘贴到“身份验证 URL”文本框中。
   5. 在 Azure 经典门户中，在“在 Mozy Enterprise 处配置单一登录”对话框页上，复制“标识提供程序 ID”值，然后将其粘贴到“SAML 终结点”文本框中。
   6. 基于下载的证书创建一个 **Base-64 编码**的文件。  
      
      > [!TIP]
      > 有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   7. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将整个证书粘贴到“SAML 证书”文本框中。
   8. 选择“为使用其网络凭据登录的管理员启用 SSO”。
   9. 单击“保存更改”。
8. 在 Azure 经典门户中，在“在 Mozy Enterprise 处配置单一登录”对话框页上，选择“单一登录配置确认”，然后单击“完成”。
   
   ![配置单一登录](./media/active-directory-saas-mozy-enterprise-tutorial/IC777316.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>配置用户预配

为了使 Azure AD 用户能够登录到 Mozy Enterprise，必须将其预配到 Mozy Enterprise 中。  
对于 Mozy Enterprise，需要手动执行预配。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 登录到 **Mozy Enterprise** 租户。
2. 单击“用户”，然后单击“添加新用户”。
   
   ![用户](./media/active-directory-saas-mozy-enterprise-tutorial/IC777317.png "Users")
   
   > [!NOTE]
   > 只有当在“身份验证策略”下选择了“Mozy”作为提供程序时才会显示“添加新用户”选项。 如果配置了 SAML 身份验证，则当用户首次通过单一登录进行登录时会自动添加用户。
   > 
   > 
3. 在“新建用户”对话框中，执行以下步骤：
   
   ![添加用户](./media/active-directory-saas-mozy-enterprise-tutorial/IC777318.png "Add Users")
   
   1. 从“选择组”列表中，选择一个组。
   2. 从“用户类型”列表中，选择一个类型。
   3. 在“用户名”文本框中，键入 Azure AD 用户的名称。
   4. 在“电子邮件”文本框中，键入 Azure AD 用户的电子邮件地址。
   5. 选择“发送用户说明电子邮件”。
   6. 单击“添加用户”。
   
   > [!NOTE]
   > 在创建用户后，将向 Azure AD 用户发送一封电子邮件，其中包含在激活帐户前用来确认帐户的链接。
   > 
   > 

> [!NOTE]
> 可以使用 Mozy Enterprise 提供的任何其他 Mozy Enterprise 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配用户的方式向要允许其使用应用程序的 Azure AD 用户授予应用程序访问权限。

### <a name="to-assign-users-to-mozy-enterprise-perform-the-following-steps"></a>若要将用户分配到 Mozy Enterprise，请执行以下步骤：
1. 在 Azure 经典门户中，创建一个测试帐户。
2. 在 **Mozy Enterprise** 应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-mozy-enterprise-tutorial/IC777319.png "Assign users")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-mozy-enterprise-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


