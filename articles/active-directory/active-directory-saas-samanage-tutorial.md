---
title: "教程：Azure Active Directory 与 Samanage 集成 | Microsoft 文档"
description: "了解如何使用 Samanage 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: da49f1d9f8aa5fbbaf6e16f1db03a16f80a2bc92


---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>教程：Azure Active Directory 与 Samanage 集成
本教程的目的是说明如何将 Samanage 与 Azure Active Directory (Azure AD) 集成。

将 Samanage 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Samanage
* 可以让用户使用其 Azure AD 帐户自动登录到 Samanage（单一登录）
* 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Samanage 的集成，需备齐以下项目：

* 一个有效的 Azure 订阅
* Samanage 租户

> [!NOTE]
> 测试本教程中的步骤时，建议不要使用生产环境。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
本教程的目的是介绍如何在测试环境中测试 Azure AD 单一登录。

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Samanage
2. 配置并测试 Azure AD 单一登录

## <a name="adding-samanage-from-the-gallery"></a>从库中添加 Samanage
若要配置 Samanage 与 Azure AD 的集成，需要从库中将 Samanage 添加到托管 SaaS 应用列表。

**若要从库中添加 Samanage，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-samanage-tutorial/tutorial_general_01.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-samanage-tutorial/tutorial_general_04.png "Add an application from gallerry")
6. 在搜索框中，键入“Samanage”。
   
   ![应用程序库](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_01.png "Application gallery")
7. 在结果窗格中，选择“Samanage”，然后单击“完成”以添加该应用程序。
   
   ![Samanage](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_02.png "Samanage")

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置并测试 Azure AD 单一登录
本部分的目的是说明如何基于名为“Britta Simon”的测试用户配置和测试 Samanage 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Samanage 用户。 换句话说，需要在 Azure AD 用户与 Samanage 中相关用户之间建立链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 Samanage 中“用户名”的值来建立此链接关系。

若要配置和测试 Samanage 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Samanage 测试用户](#creating-a-Samanage-test-user)** - 在 Samanage 中创建 Britta Simon 的对应用户，将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
在本部分中，将在经典门户中启用 Azure AD 单一登录并在 Samanage 应用程序中配置单一登录。

**若要配置 Samanage 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“Samanage”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-samanage-tutorial/tutorial_general_05.png "Configure single sign-on")
2. 在“你希望用户如何登录 Samanage”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![Microsoft Azure AD 单一登录](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_03.png "Microsoft Azure AD Single Sign-On")
3. 在“配置应用设置”对话框页上，执行以下步骤：
   
   ![配置应用 URL](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_04.png "Configure App URL")
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<Company Name>.samanage.com/saml_login/<Company Name>`。
   
   b.保留“数据库类型”设置，即设置为“共享”。 单击“下一步”
   
   > [!NOTE]
   > 请注意，这些不是实际值。 必须使用实际登录 URL 更新这些值。 若要获取这些值，请参阅步骤 8.c 了解更多详细信息或联系 Samanage。
   > 
   > 
4. 在“配置 Samanage 的单一登录”页上，单击“下载证书”，然后将该证书文件保存到计算机上。
   
   ![配置单一登录](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_05.png "Configure Single Sign-On")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录 Samanage 公司站点。
6. 在左侧导航窗格中，单击“仪表板”并选择“设置”。
   
   ![仪表板](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Dashboard")
7. 单击“单一登录”。
   
   ![单一登录](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_002.png "Single Sign-On")
8. 导航到“使用 SAML 登录”部分，执行以下步骤：
   
   ![使用 SAML 登录](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_003.png "Login using SAML")
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  单击“启用使用 SAML 单一登录”。
   
   b.保留“数据库类型”设置，即设置为“共享”。  在“标识提供者 URL”文本框中放置 Azure AD 应用程序配置向导中**标识提供者 ID** 的值。    
   
   c.  确认**登录 URL** 与步骤 3 中的**登录 URL** 匹配。
   
   d.单击“下一步”。  在“注销 URL”文本框中放置 Azure AD 应用程序配置向导中**远程注销 URL** 的值。
   
   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 在“SAML 颁发者”文本框中键入标识提供者中设置的应用 ID URI。
   
   f.  在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到“在下面粘贴标识提供者 x.509 证书”文本框中。
   
   g.  单击“创建用户(如果用户在 Samanage 中不存在)”。
   
   h.  单击“更新”。
9. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_06.png "Configure Single Sign-On")
10. 在“单一登录确认”页上，单击“完成”。
    
    ![配置单一登录](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_07.png "Configure Single Sign-On")

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在经典门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 测试用户](./media/active-directory-saas-samanage-tutorial/create_aaduser_00.png)

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-samanage-tutorial/create_aaduser_01.png)
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-samanage-tutorial/create_aaduser_02.png)
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-samanage-tutorial/create_aaduser_03.png)
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-samanage-tutorial/create_aaduser_04.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“用户配置文件”对话框页上，执行以下步骤：
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-samanage-tutorial/create_aaduser_05.png)
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
   b. 在“姓氏”文本框中，键入“Simon”。
   
   c. 在“显示名称”文本框中，键入“Britta Simon”。
   
   d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-samanage-tutorial/create_aaduser_06.png)
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-samanage-tutorial/create_aaduser_07.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-a-samanage-test-user"></a>创建 Samanage 测试用户
要使 Azure AD 用户能够登录 Samanage，必须将这些用户预配到 Samanage 中。对于 Samanage，预配是一项手动任务。

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 以管理员身份登录 Samanage 公司站点。
2. 在左侧导航窗格中，单击“仪表板”并选择“设置”。
   
   ![设置](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Setup")
3. 单击“用户”选项卡
   
   ![用户](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_006.png "Users")
4. 单击“新建用户”。
   
   ![新建用户](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_007.png "New User")
5. 键入要预配的 Azure AD 帐户的“名称”和“电子邮件地址”，然后单击“创建用户”。
   
   ![创建用户](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_008.png "Creat User")
   
   > [!NOTE]
   > AAD 帐户持有者将收到一封电子邮件，并打开用于在激活帐户前确认其帐户的链接。 可以使用任何其他 Samanage 用户帐户创建工具或 Samanage 提供的 API 来预配 AAD 用户帐户。
   > 
   > 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分的目的是通过授予 Britta Simon 访问 Samanage 的权限，允许她使用 Azure 单一登录。

![分配用户](./media/active-directory-saas-samanage-tutorial/assign_aaduser_00.png "Assign users")

**若要将 Britta Simon 分配到 Samanage，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户](./media/active-directory-saas-samanage-tutorial/assign_aaduser_01.png "Assign users")
2. 在应用程序列表中，选择“Samanage”。
   
    ![配置单一登录](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_08.png)
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户](./media/active-directory-saas-samanage-tutorial/assign_aaduser_02.png "Assign users")
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户](./media/active-directory-saas-samanage-tutorial/assign_aaduser_03.png "Assign users")

### <a name="testing-single-sign-on"></a>测试单一登录
本部分的目的是使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Samanage”磁贴时，用户应自动登录到 Samanage 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)




<!--HONumber=Nov16_HO3-->


