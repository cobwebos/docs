---
title: "教程：Azure Active Directory 与 FM:Systems 的集成 | Microsoft Docs"
description: "了解如何使用 FM:Systems 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f78c58c5-6e98-458b-8991-78624a245665
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/23/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 789ec6f0a230f49ecd935bc85ea4ad984e936da9
ms.openlocfilehash: bd2bfa1696215ded3da40d349111efa13ddbed37


---
# <a name="tutorial-azure-active-directory-integration-with-fmsystems"></a>教程：Azure Active Directory 与 FM:Systems 集成
本教程的目的是说明 Azure 与 FM:Systems 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 启用了 FM:Systems 单一登录的订阅

完成本教程后，已向 FM:Systems 分配的 Azure AD 用户将能够在 FM:Systems 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 FM:Systems 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-fm-systems-tutorial/IC795899.png "Scenario")

## <a name="enabling-the-application-integration-for-fmsystems"></a>为 FM:Systems 启用应用程序集成
本部分的目的是概述如何为 FM:Systems 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-fmsystems-perform-the-following-steps"></a>若要支持 FM:Systems 的应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-fm-systems-tutorial/IC700993.png "Active Directory")

2. 从“目录”列表中，选择要为其启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-fm-systems-tutorial/IC700994.png "Applications")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-fm-systems-tutorial/IC749321.png "Add application")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库中添加应用程序](./media/active-directory-saas-fm-systems-tutorial/IC749322.png "Add an application from gallerry")

6. 在“搜索”框中，键入“FM:Systems”。
   
    ![应用程序库](./media/active-directory-saas-fm-systems-tutorial/IC795900.png "Application Gallery")

7. 在结果窗格中，选择“FM:Systems”，然后单击“完成”以添加该应用程序。
   
    ![FM:Systems](./media/active-directory-saas-fm-systems-tutorial/IC800213.png "FM:Systems")
   
## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 FM:Systems 进行身份验证。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“FM:Systems”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-fm-systems-tutorial/IC790810.png "Configure Single Sign-On")

2. 在“你希望用户如何登录 FM:Systems”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-fm-systems-tutorial/IC795901.png "Configure Single Sign-On")

3. 在“配置应用 URL”页上，执行以下步骤：
   
    ![配置应用 URL](./media/active-directory-saas-fm-systems-tutorial/IC795902.png "Configure App URL")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“FM:Systems 登录 URL”文本框中，键入 FM:Systems“回复 URL”（例如：*https://dpr.fmshosted.com/fminteract/ConsumerService2.aspx*）。  
      
    > [!NOTE]
    > 可以从 FM:Systems 支持团队获取该值。
    > 
    > 
   
    b.保留“数据库类型”设置，即设置为“共享”。 单击“下一步”

4. 在“配置 FM:Systems 的单一登录”页上，若要下载元数据，请单击“下载元数据”，然后将元数据保存在计算机上。
   
    ![配置单一登录](./media/active-directory-saas-fm-systems-tutorial/IC795903.png "Configure Single Sign-On")

5. 将下载的元数据文件提交给 FM:Systems 支持团队。
   
    > [!NOTE]
    > 必须由 FM:Systems 支持团队执行实际的 SSO 配置。
    > 在为订阅启用了 SSO 后，你将收到通知。
    > 
    > 
6. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-fm-systems-tutorial/IC795904.png "Configure Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录到 FM:Systems，必须将其预配到 FM:Systems 中。  
对于 FM:Systems，需要手动执行预配。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：
1. 在 Web 浏览器窗口中，以管理员身份登录 FM:Systemsn 公司站点。

2. 转到“系统管理”\>“管理安全性”\>“用户”\>“用户列表”。
   
    ![系统管理](./media/active-directory-saas-fm-systems-tutorial/IC795905.png "System Administration")

3. 单击“创建用户”。
   
    ![创建新用户](./media/active-directory-saas-fm-systems-tutorial/IC795906.png "Create New User")

4. 在“创建用户”部分中，执行以下步骤：
   
    ![创建用户](./media/active-directory-saas-fm-systems-tutorial/IC795907.png "Create User")
   
    a. 键入要在相关文本框中预配的有效 Azure Active Directory 帐户的用户名、密码、确认密码、电子邮件地址和员工 ID。
   
    b.保留“数据库类型”设置，即设置为“共享”。 单击“资源组名称” 的 Azure 数据工厂。
 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-fmsystems-perform-the-following-steps"></a>若要将用户分配到 FM:Systems，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“FM:Systems”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-fm-systems-tutorial/IC795908.png "Assign Users")

3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
    ![是](./media/active-directory-saas-fm-systems-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO4-->


