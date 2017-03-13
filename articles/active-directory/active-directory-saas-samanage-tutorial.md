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
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eacd69207e21a5a7c3ef71902bc6c20c37f2003c
ms.openlocfilehash: 0936f7e0b5e1967b629b31cac3c817d04a75287f
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>教程：Azure Active Directory 与 Samanage 集成
本教程的目的是说明如何将 Samanage 与 Azure Active Directory (Azure AD) 集成。

将 Samanage 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Samanage
* 可以让用户使用其 Azure AD 帐户自动登录到 Samanage 单一登录 (SSO)
* 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Samanage 的集成，需备齐以下项目：

* 一个有效的 Azure 订阅
* Samanage 租户

>[!NOTE]
>测试本教程中的步骤时，建议不要使用生产环境。 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以获取[一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
本教程旨在介绍如何在测试环境中测试 Azure AD SSO。

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Samanage
2. 配置和测试 Azure AD SSO

## <a name="adding-samanage-from-the-gallery"></a>从库中添加 Samanage
若要配置 Samanage 与 Azure AD 的集成，需要从库中将 Samanage 添加到托管 SaaS 应用列表。

**若要从库中添加 Samanage，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-samanage-tutorial/tutorial_general_01.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-samanage-tutorial/tutorial_general_04.png "从库添加应用程序")
6. 在搜索框中，键入“Samanage”。
   
   ![应用程序库](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_01.png "应用程序库")
7. 在结果窗格中，选择“Samanage”，然后单击“完成”以添加该应用程序。
   
   ![Samanage](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_02.png "Samanage")

## <a name="configure-and-test-azure-ad-sso"></a>配置和测试 Azure AD SSO
本部分的目的是说明如何基于名为“Britta Simon”的测试用户配置和测试 Samanage 的 Azure AD SSO。

若要运行 SSO，Azure AD 需要知道与 Azure AD 用户相对应的 Samanage 用户。 换句话说，需要在 Azure AD 用户与 Samanage 中相关用户之间建立链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 Samanage 中“用户名”的值来建立此链接关系。

若要配置和测试 Samanage 的 Azure AD SSO，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Samanage 测试用户](#creating-a-Samanage-test-user)** - 在 Samanage 中创建 Britta Simon 的对应用户，将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-sso"></a>配置 Azure AD SSO
在本部分中，在经典门户中启用 Azure AD 单一登录，并在 Samanage 应用程序中配置 SSO。

**若要配置 Samanage 的 Azure AD SSO，请执行以下步骤：**

1. 在 Azure 经典门户中的“Samanage”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-samanage-tutorial/tutorial_general_05.png "配置单一登录")
2. 在“你希望用户如何登录 Samanage”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![Microsoft Azure AD 单一登录](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_03.png "Microsoft Azure AD 单一登录")
3. 在“配置应用设置”对话框页上，执行以下步骤：
   
   ![配置应用 URL](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_04.png "配置应用 URL") 
 1. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<Company Name>.samanage.com/saml_login/<Company Name>`。 
 2. 单击“下一步”。
 
   >[!NOTE]
   >请注意，这些不是实际值。 必须使用实际登录 URL 更新这些值。 若要获取这些值，请参阅步骤 8.c 了解更多详细信息或联系 Samanage。
   > 
 
4. 在“配置 Samanage 的单一登录”页上，单击“下载证书”，然后将该证书文件保存到计算机上。
   
   ![配置单一登录](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_05.png "配置单一登录")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录 Samanage 公司站点。
6. 在左侧导航窗格中，单击“仪表板”并选择“设置”。
   
   ![仪表板](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Dashboard")
7. 单击“单一登录”。
   
   ![单一登录](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_002.png "单一登录")
8. 导航到“使用 SAML 登录”部分，执行以下步骤：
   
   ![使用 SAML 登录](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_003.png "使用 SAML 登录")
 1. 单击“启用使用 SAML 单一登录”。  
 2. 在“标识提供者 URL”文本框中放置 Azure AD 应用程序配置向导中**标识提供者 ID** 的值。    
 3. 确认**登录 URL** 与步骤 3 中的**登录 URL** 匹配。
 4. 在“注销 URL”文本框中放置 Azure AD 应用程序配置向导中**远程注销 URL** 的值。
 5. 在“SAML 颁发者”文本框中键入标识提供者中设置的应用 ID URI。
 6. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到“在下面粘贴标识提供者 x.509 证书”文本框中。
 7. 单击“创建用户(如果用户在 Samanage 中不存在)”。
 8. 单击“更新”。
9. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_06.png "配置单一登录")
10. 在“单一登录确认”页上，单击“完成”。
    
    ![配置单一登录](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_07.png "配置单一登录")

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在经典门户中创建名为“Britta Simon”的测试用户。

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
 1. 在“用户类型”中，选择“你的组织中的新用户”。 
 2. 在“用户名”文本框中，键入“BrittaSimon”。 
 3. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“用户配置文件”对话框页上，执行以下步骤：
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-samanage-tutorial/create_aaduser_05.png) 
 1. 在“名字”文本框中，键入“Britta”。   
 2. 在“姓氏”文本框中，键入“Simon”。 
 3. 在“显示名称”文本框中，键入“Britta Simon”。 
 4. 在“角色”列表中，选择“用户”。 
 5. 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-samanage-tutorial/create_aaduser_06.png)
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-samanage-tutorial/create_aaduser_07.png)  
 1. 写下“新密码”的值。 
 2. 单击“完成”。   

### <a name="create-a-samanage-test-user"></a>创建 Samanage 测试用户
要使 Azure AD 用户能够登录 Samanage，必须将这些用户预配到 Samanage 中。对于 Samanage，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录 Samanage 公司站点。
2. 在左侧导航窗格中，单击“仪表板”并选择“设置”。
   
   ![设置](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "设置")
3. 单击“用户”选项卡
   
   ![用户](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_006.png "用户")
4. 单击“新建用户”。
   
   ![新建用户](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_007.png "New User")
5. 键入要预配的 Azure AD 帐户的“名称”和“电子邮件地址”，然后单击“创建用户”。
   
   ![创建用户](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_008.png "创建用户")
   
   >[!NOTE]
   >AAD 帐户持有者将收到一封电子邮件，并打开用于在激活帐户前确认其帐户的链接。 可以使用任何其他 Samanage 用户帐户创建工具或 Samanage 提供的 API 来预配 AAD 用户帐户。
   >  

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分旨在通过授予 Britta Simon 访问 Samanage 的权限，允许她使用 Azure SSO。

![分配用户](./media/active-directory-saas-samanage-tutorial/assign_aaduser_00.png "分配用户")

**若要将 Britta Simon 分配到 Samanage，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户](./media/active-directory-saas-samanage-tutorial/assign_aaduser_01.png "分配用户")
2. 在应用程序列表中，选择“Samanage”。
   
    ![配置单一登录](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_08.png)
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户](./media/active-directory-saas-samanage-tutorial/assign_aaduser_02.png "分配用户")
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户](./media/active-directory-saas-samanage-tutorial/assign_aaduser_03.png "分配用户")

### <a name="test-single-sign-on"></a>测试单一登录
本部分旨在使用“访问面板”测试 Azure AD SSO 配置。

单击访问面板中的“Samanage”磁贴时，用户应自动登录到 Samanage 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)


