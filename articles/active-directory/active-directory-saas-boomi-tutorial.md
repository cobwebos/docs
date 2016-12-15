---
title: "教程：Azure Active Directory 与 Boomi 集成 | Microsoft Docs"
description: "了解如何使用 Boomi 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: a315916e-8bfd-4390-bad2-ec9029314ab6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 51ea1ededc7b850e3dff24c9f735789d34391434


---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>教程：Azure Active Directory 与 Boomi 集成
本教程的目的是说明 Azure 与 Boomi 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 Boomi 单一登录的订阅

完成本教程后，分配到 Boomi 的 Azure AD 用户将能够在 Boomi 公司站点（服务提供商启动的登录）或通过阅读[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Boomi 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-boomi-tutorial/IC791134.png "Scenario")

## <a name="enabling-the-application-integration-for-boomi"></a>为 Boomi 启用应用程序集成
本部分旨在概述如何为 Boomi 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-boomi-perform-the-following-steps"></a>若要为 Boomi 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-boomi-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-boomi-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-boomi-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加一个应用程序](./media/active-directory-saas-boomi-tutorial/IC749322.png "Add an application from gallerry")
6. 在搜索框中，键入“Boomi”。
   
   ![应用程序库](./media/active-directory-saas-boomi-tutorial/IC790822.png "Application Gallery")
7. 在“结果”窗格中，选择“Boomi”，然后单击“完成”，添加该应用程序。
   
   ![Boomi](./media/active-directory-saas-boomi-tutorial/IC790823.png "Boomi")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Boomi 证明自己的身份。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“Boomi”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-boomi-tutorial/IC790824.png "Configure Single Sign-On")
2. 在“你希望用户如何登录 Boomi”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-boomi-tutorial/IC790825.png "Configure Single Sign-On")
3. 在“配置应用 URL”页的“Boomi 回复 URL”文本框中，键入 **Boomi AtomSphere 登录 URL**（例如：“*https://platform.boomi.com/sso/AccountName/saml*”），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-boomi-tutorial/IC790826.png "Configure App URL")
4. 在“配置 Boomi 的单一登录”页面上，若要下载证书，请单击“下载证书”，然后将证书文件保存在本地计算机上。
   
   ![配置单一登录](./media/active-directory-saas-boomi-tutorial/IC790827.png "Configure Single Sign-On")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录 Boomi 公司站点。
6. 在顶部工具栏中，单击公司名称，然后单击“设置”。
   
   ![设置](./media/active-directory-saas-boomi-tutorial/IC790828.png "Setup")
7. 单击“SSO 选项”。
   
   ![SSO 选项](./media/active-directory-saas-boomi-tutorial/IC790829.png "SSO Options")
8. 在“单一登录选项”部分中，执行以下步骤：
   
   ![单一登录选项](./media/active-directory-saas-boomi-tutorial/IC790830.png "Single Sign-On Options")
   
   1. 选择“启用 SAML 单一登录”。
   2. 单击“导入”上载已下载的证书。
   3. 在 Azure 经典门户的“配置 Boomi 的单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“标识提供者登录 URL”文本框中。
   4. 对于“联合 ID 位置”，请选择“联合 ID 位于使用者的 NameID 元素中”。
   5. 单击“保存” 。
9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-boomi-tutorial/IC775560.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录到 Boomi，必须将其预配到 Boomi 中。  
就 Boomi 来说，预配任务需要手动完成。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：
1. 以管理员身份登录 **Boomi** 公司站点。
2. 转到“用户管理”**\>**“用户”。
   
   ![用户](./media/active-directory-saas-boomi-tutorial/IC790831.png "Users")
3. 单击“添加用户”。
   
   ![添加用户](./media/active-directory-saas-boomi-tutorial/IC790832.png "Add User")
4. 在“添加用户角色”对话框页上，执行以下步骤：
   
   ![添加用户](./media/active-directory-saas-boomi-tutorial/IC790833.png "Add User")
   
   1. 将要预配的有效 AAD 帐户的**名字**、**姓氏**和**电子邮件**键入到相关文本框中。
   2. 单击“确定”。

> [!NOTE]
> 可使用其他任何 Boomi 用户帐户创建工具或 Boomi 提供的 API 预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要向希望能够通过应用程序访问配置的 Azure AD 用户分配访问权限，从而向他们授予该权限。

### <a name="to-assign-users-to-boomi-perform-the-following-steps"></a>若要将用户分配到 Boomi，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Boomi”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-boomi-tutorial/IC790834.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-boomi-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


