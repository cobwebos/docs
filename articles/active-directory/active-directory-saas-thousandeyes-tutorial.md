---
title: "教程：Azure Active Directory 与 ThousandEyes 集成 | Microsoft 文档"
description: "了解如何使用 ThousandEyes 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1cef7ff21a8d076c89688f1fe75cebdb7c468199
ms.openlocfilehash: e0f85965cb884022f665d3664bc2b824095ad0fe


---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>教程：Azure Active Directory 与 ThousandEyes 集成
本教程的目的是介绍如何在 Azure Active Directory (Azure AD) 和 ThousandEyes 之间设置单一登录。

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 ThousandEyes 单一登录的订阅

完成本教程后，已向 ThousandEyes 分配的 AAD 用户将能够在 ThousandEyes 公司站点（服务提供商发起的登录）或使用 AAD 访问面板单一登录到应用程序。

1. 为 ThousandEyes 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-thousandeyes-tutorial/IC790059.png "Scenario")

## <a name="enabling-the-application-integration-for-thousandeyes"></a>为 ThousandEyes 启用应用程序集成
本部分的目的是概述如何为 ThousandEyes 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-thousandeyes-perform-the-following-steps"></a>若要为 ThousandEyes 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-thousandeyes-tutorial/IC700993.png "Active Directory")

2. 从“目录”列表中，选择要为其启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-thousandeyes-tutorial/IC700994.png "Applications")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-thousandeyes-tutorial/IC749321.png "Add application")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库中添加应用程序](./media/active-directory-saas-thousandeyes-tutorial/IC749322.png "Add an application from gallerry")

6. 在搜索框中，键入“ThousandEyes”。
   
    ![应用程序库](./media/active-directory-saas-thousandeyes-tutorial/IC790060.png "Application Gallery")
7. 在结果窗格中，选择“ThousandEyes”，然后单击“完成”以添加该应用程序。
   
    ![ThousandEyes](./media/active-directory-saas-thousandeyes-tutorial/IC790061.png "ThousandEyes")

## <a name="configuring-single-sign-on"></a>配置单一登录
本部分概述如何让用户使用基于 SAML 协议的联合身份验证通过其在 Azure Active Directory 中的帐户向 ThousandEyes 进行身份验证。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“ThousandEyes”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-thousandeyes-tutorial/IC790062.png "Configure Single SignOn")

1. 在“你希望用户如何登录 ThousandEyes”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-thousandeyes-tutorial/IC790063.png "Configure Single SignOn")

3. 在“配置应用 URL”页上的“ThousandEyes 登录 URL”文本框中，输入用户用于登录 ThousandEyes 应用程序的 URL（例如：“*https://app.thousandeyes.com/login/sso*”），然后单击“下一步”。 
   
    ![配置应用 URL](./media/active-directory-saas-thousandeyes-tutorial/IC790064.png "Configure App URL")

4. 在“配置 ThousandEyes 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件保存在本地计算机上。
   
    ![配置单一登录](./media/active-directory-saas-thousandeyes-tutorial/IC790065.png "Configure Single SignOn")

5. 在另一个 Web 浏览器窗口中，以管理员身份登录 **ThousandEyes** 公司站点。

6. 在顶部菜单中，单击“设置”。
   
    ![设置](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Settings")

7. 单击“帐户”
   
    ![帐户](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Account")

8. 单击“安全性和身份验证”选项卡。
   
    ![安全性和身份验证](./media/active-directory-saas-thousandeyes-tutorial/IC790068.png "Security & Authentication")

9. 在“设置单一登录”部分中，执行以下步骤：
   
    ![设置单一登录](./media/active-directory-saas-thousandeyes-tutorial/IC790069.png "Setup Single Sign-On")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择“启用单一登录”。
   
    b.保留“数据库类型”设置，即设置为“共享”。 在 Microsoft Azure 经典门户的“配置 ThousandEyes 的单一登录”页上，复制“远程登录 URL”值，然后将其粘贴到“登录页 URL”文本框中。
   
    c. 在 Microsoft Azure 经典门户的“配置 ThousandEyes 的单一登录”页上，复制“远程注销 URL”值，然后将其粘贴到“注销页 URL”文本框中。
   
    d.单击“下一步”。 在 Microsoft Azure 经典门户的“配置 ThousandEyes 的单一登录”页上，复制“颁发者 URL”值，然后将其粘贴到“标识提供程序颁发者”文本框中。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 在“标识提供者证书”中，单击“选择文件”，然后上载已从 Microsoft Azure 经典门户下载的证书。
   
    f. 单击“保存” 。

10. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-thousandeyes-tutorial/IC790070.png "Configure Single SignOn")

## <a name="configuring-user-provisioning"></a>配置用户设置
要使 Azure AD 用户能够登录 ThousandEyes，必须将这些用户预配到 ThousandEyes 中。  
对于 ThousandEyes，预配是一项手动任务。

### <a name="to-provision-a-user-account-to-thousandeyes-perform-the-following-steps"></a>若要将用户帐户预配到 ThousandEyes，请执行以下步骤：
1. 以管理员身份登录 ThousandEyes 公司站点。

2. 单击“设置”。
   
    ![设置](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Settings")

3. 单击“帐户”。
   
    ![帐户](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Account")

4. 单击“帐户和用户”选项卡。
   
    ![帐户和用户](./media/active-directory-saas-thousandeyes-tutorial/IC790073.png "Accounts & Users")

5. 在“添加用户和帐户”部分中，执行以下步骤：
   
    ![添加用户帐户](./media/active-directory-saas-thousandeyes-tutorial/IC790074.png "Add User Accounts")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在相关文本框中键入要预配的有效 Azure Active Directory 帐户的“名称”、“电子邮件”和其他详细信息。
   
    b.保留“数据库类型”设置，即设置为“共享”。 单击“将新用户添加到帐户”。
      
    > [!NOTE]
    > AAD 帐户持有者将收到一封电子邮件，其中包含用于确认和激活帐户的链接。
    > 
    > 

> [!NOTE]
> 可以使用任何其他 ThousandEyes 用户帐户创建工具或 ThousandEyes 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-thousandeyes-perform-the-following-steps"></a>若要将用户分配到 ThousandEyes，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。

2. 在“ThousandEyes”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-thousandeyes-tutorial/IC790075.png "Assign Users")

3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
    ![是](./media/active-directory-saas-thousandeyes-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Dec16_HO1-->


