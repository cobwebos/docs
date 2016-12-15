---
title: "教程：Azure Active Directory 与 MCM 的集成 | Microsoft Docs"
description: "了解如何将 MCM 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多内容！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 7f00799d-e3e9-4ba9-ae4a-fbca843ac5db
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 51a736c051bef8550662060fdde89fc50e32928a


---
# <a name="tutorial-azure-active-directory-integration-with-mcm"></a>教程：Azure Active Directory 与 MCM 的集成
本教程的目的是展示如何将 MCM 与 Azure Active Directory (Azure AD) 进行集成。

将 MCM 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 MCM
* 可以让用户使用其 Azure AD 帐户自动登录到 MCM（单一登录）
* 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 MCM 的集成，需要具有以下项：

* 一个有效的 Azure 订阅
* 启用了 MCM 单一登录的订阅

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

1. 从库中添加 MCM
2. 配置并测试 Azure AD 单一登录

## <a name="adding-mcm-from-the-gallery"></a>从库中添加 MCM
若要配置 MCM 与 Azure AD 的集成，需要从库中将 MCM 添加到托管 SaaS 应用列表。

**若要从库中添加 MCM，请执行以下步骤：**

1. 在 Azure 经典门户中，在左侧导航窗格上，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-mcm-tutorial/tutorial_general_01.png "Active Directory")

2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-mcm-tutorial/tutorial_general_02.png "Applications")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-mcm-tutorial/tutorial_general_03.png "Add application")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库中添加应用程序](./media/active-directory-saas-mcm-tutorial/tutorial_general_04.png "Add an application from gallerry")

6. 在**搜索框**中，键入“MCM”。
   
    ![应用程序库](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_01.png "Application gallery")

7. 在结果窗格中，选择“MCM”，然后单击“完成”以添加该应用程序。
   
    ![MCM](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_001.png "MCM")

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置并测试 Azure AD 单一登录
本部分的目的是基于名为“Britta Simon”的测试用户展示如何配置并测试 MCM 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 MCM 用户。 换句话说，需要在 Azure AD 用户与 MCM 中的相关用户之间建立链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 MCM 中“用户名”的值来建立此链接关系。

若要配置并测试 MCM 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 MCM 测试用户](#creating-a-mcm-test-user)** - 在 MCM 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
在本部分中，将在经典门户中启用 Azure AD 单一登录并在 MCM 应用程序中配置单一登录。

**若要配置 MCM 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中，在 **MCM** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-mcm-tutorial/tutorial_general_05.png "Configure single sign-on")

2. 在“你希望用户如何登录到 MCM”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![Microsoft Azure AD 单一登录](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_03.png "Microsoft Azure AD Single Sign-On")

3. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![配置应用 URL](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_04.png "Configure App URL")
   
    a. 在“登录 URL”文本框中，键入：`https://myaba.co.uk/client-access/<company name>/saml.php`。
   
    b. 单击“下一步”

4. 在“在 MCM 处配置单一登录”页上，单击“下载元数据”，然后将证书文件保存在计算机上。
   
    ![配置单一登录](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_05.png "Configure Single Sign-On")

5. 若要为应用程序配置 SSO，请联系 MCM 支持团队。 附加下载的元数据文件，与 MCM 团队共享该文件以在他们那边设置 SSO。

6. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_06.png "Configure Single Sign-On")

7. 在“单一登录确认”页上，单击“完成”。
   
    ![配置单一登录](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_07.png "Configure Single Sign-On")

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在经典门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 测试用户](./media/active-directory-saas-mcm-tutorial/create_aaduser_00.png)

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-mcm-tutorial/create_aaduser_01.png)

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-mcm-tutorial/create_aaduser_02.png)

4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-mcm-tutorial/create_aaduser_03.png)

5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-mcm-tutorial/create_aaduser_04.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“资源组名称” 的 Azure 数据工厂。

6. 在“用户配置文件”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-mcm-tutorial/create_aaduser_05.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
    b. 在“姓氏”文本框中，键入“Simon”。
   
    c. 在“显示名称”文本框中，键入“Britta Simon”。
   
    d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“资源组名称” 的 Azure 数据工厂。

7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-mcm-tutorial/create_aaduser_06.png)

8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-mcm-tutorial/create_aaduser_07.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-a-mcm-test-user"></a>创建 MCM 测试用户
在本部分中，将在 MCM 中创建一个名为 Britta Simon 的用户。 请与 MCM 支持团队协作，将用户添加到 MCM 平台中。

> [!NOTE]
> 可以使用 MCM 提供的任何其他 MCM 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分的目的是通过向 Britta Simon 授予对 MCM 的访问权限，使她能够使用 Azure 单一登录。

![分配用户](./media/active-directory-saas-mcm-tutorial/assign_aaduser_00.png "Assign users")

**若要将 Britta Simon 分配到 MCM，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户](./media/active-directory-saas-mcm-tutorial/assign_aaduser_01.png "Assign users")

2. 在应用程序列表中，选择“MCM”。
   
    ![配置单一登录](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_08.png)

3. 在顶部菜单中，单击“用户”。
   
    ![分配用户](./media/active-directory-saas-mcm-tutorial/assign_aaduser_02.png "Assign users")

4. 在“用户”列表中，选择“Britta Simon”。

5. 在底部工具栏中，单击“分配”。
   
    ![分配用户](./media/active-directory-saas-mcm-tutorial/assign_aaduser_03.png "Assign users")

### <a name="testing-single-sign-on"></a>测试单一登录
本部分的目的是使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 MCM 磁贴时，应当会自动登录到 MCM 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)




<!--HONumber=Nov16_HO3-->


