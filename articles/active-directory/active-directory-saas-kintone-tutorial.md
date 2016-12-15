---
title: "教程：Azure Active Directory 与 Kintone 集成 | Microsoft Docs"
description: "了解如何使用 Kintone 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7a25e030e8ade95db2ac9cd58fe4d94bbb7775ba


---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>教程：Azure Active Directory 与 Kintone 集成
本教程的目的是说明 Azure 与 Kintone 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 Kintone 单一登录的订阅

完成本教程后，已向 Kintone 分配的 Azure AD 用户将能够在 Kintone 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Kintone 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-kintone-tutorial/IC785859.png "Scenario")

## <a name="enabling-the-application-integration-for-kintone"></a>为 Kintone 启用应用程序集成
本部分的目的是概述如何为 Kintone 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-kintone-perform-the-following-steps"></a>若要为 Kintone 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-kintone-tutorial/IC700993.png "Active Directory")

2. 从“目录”列表中，选择要为其启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-kintone-tutorial/IC700994.png "Applications")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-kintone-tutorial/IC749321.png "Add application")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库中添加应用程序](./media/active-directory-saas-kintone-tutorial/IC749322.png "Add an application from gallerry")

6. 在**搜索框**中，键入“Kintone”。
   
    ![应用程序库](./media/active-directory-saas-kintone-tutorial/IC785867.png "Application Gallery")

7. 在结果窗格中，选择“Kintone”，然后单击“完成”以添加该应用程序。
   
    ![Kintone](./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
   
## <a name="configuring-single-sign-on"></a>配置单一登录

此部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Kintone 进行身份验证。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“Kintone”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-kintone-tutorial/IC785872.png "Configure Single Sign-On")

2. 在“你希望用户如何登录 Kintone”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-kintone-tutorial/IC785873.png "Configure Single Sign-On")

3. 在“配置应用 URL”页上的“Kintone 登录 URL”文本框中，使用模式“*https://company.kintone.com*”键入 URL，然后单击“下一步”。
   
    ![配置应用 URL](./media/active-directory-saas-kintone-tutorial/IC785875.png "Configure App URL")

4. 在“配置 Kintone 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将该证书文件保存到计算机上。
   
    ![配置单一登录](./media/active-directory-saas-kintone-tutorial/IC785878.png "Configure Single Sign-On")

5. 在另一个 Web 浏览器窗口中，以管理员身份登录到 **Kintone** 公司站点。

6. 单击“设置”。
   
    ![设置](./media/active-directory-saas-kintone-tutorial/IC785879.png "Settings")

7. 单击“用户和系统管理”。
   
    ![用户和系统管理](./media/active-directory-saas-kintone-tutorial/IC785880.png "Users & System Administration")

8. 在“系统管理”\>“安全性”下，单击“登录”。
   
    ![登录](./media/active-directory-saas-kintone-tutorial/IC785881.png "Login")

9. 单击“启用 SAML 身份验证”。
   
    ![SAML 身份验证](./media/active-directory-saas-kintone-tutorial/IC785882.png "SAML Authentication")

10. 在“SAML 身份验证”部分中，执行以下步骤：
    
    ![SAML 身份验证](./media/active-directory-saas-kintone-tutorial/IC785883.png "SAML Authentication")
    
    1. 在 Azure 经典门户的“配置 Kintone 的单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“登录 URL”文本框中。
   
    2. 在 Azure 经典门户的“配置 Kintone 的单一登录”对话框页上，复制“远程注销 URL”值，然后将其粘贴到“注销 URL”文本框中。
    
    3. 单击“浏览”以上载已下载的证书。
    
    4. 单击“保存” 。

11. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-kintone-tutorial/IC785884.png "Configure Single Sign-On")
    
## <a name="configuring-user-provisioning"></a>配置用户设置

要使 Azure AD 用户能够登录 Kintone，必须将这些用户预配到 Kintone 中。  
对于 kintone，预配是一项手动任务。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 以管理员身份登录到 **Kintone** 公司站点。

2. 单击“设置”。
   
    ![设置](./media/active-directory-saas-kintone-tutorial/IC785879.png "Settings")

3. 单击“用户和系统管理”。
   
    ![用户和系统管理](./media/active-directory-saas-kintone-tutorial/IC785880.png "User & System Administration")

4. 在“用户管理”下，单击“部门和用户”。
   
    ![部门和用户](./media/active-directory-saas-kintone-tutorial/IC785888.png "Department & Users")

5. 单击“新建用户”。
   
    ![新建用户](./media/active-directory-saas-kintone-tutorial/IC785889.png "New Users")

6. 在“新建用户”部分中，执行以下步骤：
   
    ![新建用户](./media/active-directory-saas-kintone-tutorial/IC785890.png "New Users")
   
    1. 在相关文本框中键入要预配的有效 AAD 帐户的**显示名称**、**登录名**、**新密码**、**确认密码**、**电子邮件地址**和其他详细信息。
 
    2. 单击“保存” 。

> [!NOTE]
> 可以使用 Kintone 提供的任何其他 Kintone 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-kintone-perform-the-following-steps"></a>若要将用户分配到 Kintone，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。

2. 在“Kintone”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-kintone-tutorial/IC785891.png "Assign Users")

3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
    ![是](./media/active-directory-saas-kintone-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


