---
title: "教程：Azure Active Directory 与 RunMyProcess 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 RunMyProcess 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 223a0abfcd1ae2997e4d2facfd065cf8dc721668


---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>教程：Azure Active Directory 与 RunMyProcess 集成
本教程的目的是说明如何将 RunMyProcess 与 Azure Active Directory (Azure AD) 集成。

将 RunMyProcess 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 RunMyProcess
* 可以让用户使用其 Azure AD 帐户自动登录到 RunMyProcess（单一登录）
* 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 RunMyProcess 的集成，需要以下项：

* Azure AD 订阅
* 已启用 RunMyProcess 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
本教程的目的是介绍如何在测试环境中测试 Azure AD 单一登录。

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 RunMyProcess
2. 配置和测试 Azure AD 单一登录

## <a name="adding-runmyprocess-from-the-gallery"></a>从库中添加 RunMyProcess
若要配置 RunMyProcess 与 Azure AD 的集成，需要从库中将 RunMyProcess 添加到托管 SaaS 应用列表。

**若要从库中添加 RunMyProcess，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“RunMyProcess”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_01.png)
7. 在结果窗格中，选择“RunMyProcess”，然后单击“完成”以添加该应用程序。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分的目的是说明如何基于名为“Britta Simon”的测试用户配置和测试 RunMyProcess 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 RunMyProcess 用户。 换句话说，需要在 Azure AD 用户与 RunMyProcess 中相关用户之间建立链接关系。

通过将 Azure AD 中“用户名”的值分配为 RunMyProcess 中“用户名”的值来建立此链接关系。

若要配置和测试 RunMyProcess 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 RunMyProcess 测试用户](#creating-a-runmyprocess-test-user)** - 在 RunMyProcess 中创建 Britta Simon 的对应用户，将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
在本部分中，将在经典门户中启用 Azure AD 单一登录并在 RunMyProcess 应用程序中配置单一登录。

**若要配置 RunMyProcess 的 Azure AD 单一登录，请执行以下步骤：**

1. 在经典门户中的“RunMyProcess”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录 RunMyProcess”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_03.png) 
3. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_04.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL：`https://live.runmyprocess.com/live/<tenant id>`。
   
    b.保留“数据库类型”设置，即设置为“共享”。 单击“下一步”
   
   > [!NOTE]
   > 请注意，必须使用实际登录 URL 更新这些值。 若要获取此值，请通过 <mailto:support@runmyprocess.com> 与 RunMyProcess 支持团队联系。
   > 
   > 
4. 在“配置 RunMyProcess 的单一登录”页上，请单击“下载证书”，然后在计算机上保存该文件：
   
    ![配置单一登录](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_05.png)
5. 在另一个 Web 浏览器窗口中，以管理员身份登录 RunMyProcess 租户。
6. 在左侧导航面板中，单击“帐户”，然后选择“配置”。
   
    ![在应用端配置单一登录](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_001.png)
7. 转到“身份验证方法”部分，并执行以下步骤：
   
    ![在应用端配置单一登录](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_002.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“方法”，选择“使用 Samlv2 的 SSO”。
   
    b.保留“数据库类型”设置，即设置为“共享”。 在“SSO 重定向”文本框中放置 Azure AD 应用程序配置向导中 **SAML SSO URL** 的值。
   
    c. 在“注销重定向”文本框中放置 Azure AD 应用程序配置向导中**单一注销服务 URL** 的值。
   
    d.单击“下一步”。 在“名称 ID 格式”文本框中放置 Azure AD 应用程序配置向导中**名称标识符格式**的值。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 复制已下载的证书文件的内容，然后将其粘贴到“证书”文本框中。 
   
    f. 单击“保存”图标。
8. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
    ![Azure AD 单一登录][10]
9. 在“单一登录确认”页上，单击“完成”。  
   
    ![Azure AD 单一登录][11]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在经典门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_01.png) 
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_02.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_03.png) 
5. 在“告诉我们有关此用户的信息”对话框页中，执行以下步骤： ![创建 Azure AD 测试用户](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_04.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“用户配置文件”对话框页面上，执行以下步骤：![创建 Azure AD 测试用户](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_05.png) 
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
   b. 在“姓氏”文本框中，键入“Simon”。
   
   c. 在“显示名称”文本框中，键入“Britta Simon”。
   
   d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_06.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_07.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-a-runmyprocess-test-user"></a>创建 RunMyProcess 测试用户
要使 Azure AD 用户能够登录 RunMyProcess，必须将这些用户预配到 RunMyProcess 中。 对于 RunMyProcess，预配是一项手动任务。

#### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 以管理员身份登录 RunMyProcess 公司站点。
2. 在左侧导航面板中，单击“帐户”并选择“用户”，然后单击“新建用户”。
   
   ![新建用户](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_003.png "New User")
3. 在“用户设置”部分中执行以下步骤：
   
   ![配置文件](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profile")
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在相关文本框中键入要预配的有效 AAD 帐户的“名称”和“电子邮件”。
   
   b.保留“数据库类型”设置，即设置为“共享”。 选择 **IDE 语言**、**语言**和**配置文件**。
   
   c. 选择“将帐户创建电子邮件发送给我”。
   
   d.单击“下一步”。 单击“保存” 。
   
   > [!NOTE]
   > 可以使用任何其他 RunMyProcess 用户帐户创建工具或 RunMyProcess 提供的 API 来预配 AAD 用户帐户。
   > 
   > 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分的目的是通过授予 Britta Simon 访问 RunMyProcess 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 RunMyProcess，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在应用程序列表中，选择“RunMyProcess”。
   
    ![配置单一登录](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_50.png) 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]
4. 在“用户列表”中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分的目的是使用“访问面板”测试 Azure AD 单一登录配置。

单击访问面板中的“RunMyProcess”磁贴时，用户应自动登录到 RunMyProcess 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


