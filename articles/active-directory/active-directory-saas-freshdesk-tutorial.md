---
title: "教程：Azure Active Directory 与 Freshdesk 集成 | Microsoft Docs"
description: "了解如何使用 Freshdesk 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 31177994-7910-4a72-bd76-5fa6260242fb
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7043787c74c138984fdf5370784678d6dfcaa81e


---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>教程：Azure Active Directory 与 Freshdesk 集成
本教程旨在说明 Azure 与 Freshdesk 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Freshdesk 租户

完成本教程后，已向 Freshdesk 分配的 Azure AD 用户将能够在 Freshdesk 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Freshdesk 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-freshdesk-tutorial/IC776761.png "Scenario")

## <a name="enabling-the-application-integration-for-freshdesk"></a>为 Freshdesk 启用应用程序集成
本部分的目的是概述如何为 Freshdesk 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-freshdesk-perform-the-following-steps"></a>若要为 Freshdesk 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-freshdesk-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-freshdesk-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-freshdesk-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加一个应用程序](./media/active-directory-saas-freshdesk-tutorial/IC749322.png "Add an application from gallerry")
6. 在搜索框中，键入“Freshdesk”。
   
   ![应用程序库](./media/active-directory-saas-freshdesk-tutorial/IC776762.png "Application gallery")
7. 在结果窗格中，选择“Freshdesk”，然后单击“完成”以添加该应用程序。
   
   ![Freshdesk](./media/active-directory-saas-freshdesk-tutorial/IC776763.png "Freshdesk")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Freshdesk 进行身份验证。  
配置 Freshdesk 的单一登录需要检索证书的指纹值。  
如果不熟悉此过程，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中，在 **Freshdesk** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-freshdesk-tutorial/IC776764.png "Configure Single Sign-On")
2. 在“你希望用户如何登录 Freshdesk”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-freshdesk-tutorial/IC776765.png "Configure Single Sign-On")
3. 在“配置应用 URL”页上的“Freshdesk 登录 URL”文本框中，使用模式“*https://\<tenant-name\>.Freshdesk.com*”键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-freshdesk-tutorial/IC776766.png "Configure App URL")
4. 在“配置 Freshdesk 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件本地保存为“c:\\Freshdesk.cer”。
   
   ![配置单一登录](./media/active-directory-saas-freshdesk-tutorial/IC776767.png "Configure Single Sign-On")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录 Freshdesk 公司站点。
6. 在顶部菜单中，单击“管理员”。
   
   ![管理员](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Admin")
7. 在“常规设置”选项卡上，单击“安全”。
   
   ![“安全”](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Security")
8. 在“安全”部分中，执行以下步骤：
   
   ![单一登录](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Single Sign On")
   
   1. 对于“单一登录(SSO)”，请选择“启用”。
   2. 选择“SAML SSO”。
   3. 在 Azure 经典门户的“配置 Freshdesk 的单一登录”对话框页面上，复制“远程登录 URL”值，然后将其粘贴到“SAML 登录 URL”文本框中。
   4. 在 Azure 经典门户的“配置 Freshdesk 的单一登录”对话框页面上，复制“远程注销 URL”值，然后将其粘贴到“注销 URL”文本框中。
   5. 从导出的证书中复制“指纹”值，然后将其粘贴到“安全证书指纹”文本框中。  
      
      > [!TIP]
      > 有关详细信息，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)
      > 
      > 
   6. 单击“保存” 。
9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-freshdesk-tutorial/IC776771.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>配置用户设置

为使 Azure AD 用户能够登录到 Freshdesk，必须将其预配到 Freshdesk。  
就 Freshdesk 而言，预配属手动任务。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 登录到“Freshdesk”租户。
2. 在顶部菜单中，单击“管理员”。
   
   ![管理员](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Admin")
3. 在“常规设置”选项卡上，单击“代理”。
   
   ![代理](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agents")
4. 单击“新建代理”。
   
   ![新建代理](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "New Agent")
5. 在“代理信息”对话框中，执行以下步骤：
   
   ![代理信息](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Agent Information")
   
   1. 在“完整名称”文本框中，键入要预配的 Azure AD 帐户的名称。
   2. 在“电子邮件”文本框中，键入要预配的 Azure AD 帐户的 Azure AD 电子邮件地址。
   3. 在“名称”文本框中，键入要预配的 Azure AD 帐户的名称。
   4. 选择“代理角色”，然后单击“分配”。
   5. 单击“保存” 。
      
      > [!NOTE]
      > Azure AD 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。
      > 
      > 

> [!NOTE]
> 可以使用任何其他 Freshdesk 用户帐户创建工具或 Freshdesk 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要向希望能够通过应用程序访问配置的 Azure AD 用户分配访问权限，从而向他们授予该权限。

### <a name="to-assign-users-to-freshdesk-perform-the-following-steps"></a>若要将用户分配到 Freshdesk，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Freshdesk”应用程序集成页面上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-freshdesk-tutorial/IC776776.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-freshdesk-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


