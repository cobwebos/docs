---
title: 教程：Azure Active Directory 与 SAML SSO for Jira by resolution GmbH 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SAML SSO for Jira by resolution GmbH 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeedes
ms.openlocfilehash: b0835d89786e2d389b845c8a4bdbbfb63c0bbcd3
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352355"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>教程：Azure Active Directory 与 SAML SSO for Jira by resolution GmbH 集成

本教程介绍如何将 SAML SSO for Jira by resolution GmbH 与 Azure Active Directory (Azure AD) 集成。

将 SAML SSO for Jira by resolution GmbH 与 Azure AD 集成具有以下优势：

- 可以在 Azure AD 中控制谁有权访问 SAML SSO for Jira by resolution GmbH
- 可以让用户通过其 Azure AD 帐户自动登录到 SAML SSO for Jira by resolution GmbH（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 SAML SSO for Jira by resolution GmbH 的集成，需备齐以下项目：

- Azure AD 订阅
- 一个启用了 SAML SSO for Jira by resolution GmbH 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 SAML SSO for Jira by resolution GmbH
2. 配置和测试 Azure AD 单一登录

## <a name="adding-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>从库添加 SAML SSO for Jira by resolution GmbH
要配置 SAML SSO for Jira by resolution GmbH 的集成（集成到 Azure AD 中），需从库中将 SAML SSO for Jira by resolution GmbH 添加到托管 SaaS 应用的列表中。

**若要从库中添加 SAML SSO for Jira by resolution GmbH，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“SAML SSO for Jira by resolution GmbH”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_search.png)

5. 在结果窗格中，选择“SAML SSO for Jira by resolution GmbH”，并单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 SAML SSO for Jira by resolution GmbH 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 SAML SSO for Jira by resolution GmbH 用户。 换句话说，需要在 Azure AD 用户与 SAML SSO for Jira by resolution GmbH 中相关用户之间建立链接关系。

可通过将 Azure AD 中“用户名”的值指定为 SAML SSO for Jira by resolution GmbH 中“用户名”的值来建立此链接关系。

若要配置并测试 SAML SSO for Jira by resolution GmbH 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 SAML SSO for Jira by resolution GmbH 测试用户](#creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user)** - 在 SAML SSO for Jira by resolution GmbH 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分介绍了在 Azure 门户中启用 Azure AD 单一登录，并在 SAML SSO for Jira by resolution GmbH 应用程序中配置单一登录。

**若要配置 SAML SSO for Jira by resolution GmbH 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“SAML SSO for Jira by resolution GmbH”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_samlbase.png)

3. 在“SAML SSO for Jira by resolution GmbH 域和 URL”部分中，如果要在“IDP 启动的模式”下配置应用程序，请执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_url_1.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/samlsso`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/samlsso`

4. 选中“显示高级 URL 设置”。 如果要在“SP”发起的模式下配置应用程序：

    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_url_2.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 若要获取这些值，请联系 [SAML SSO for Jira by resolution GmbH 客户支持团队](https://www.resolution.de/go/support)。 

5. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_certificate.png) 

6. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/tutorial_general_400.png)
    
7. 在另一个 Web 浏览器窗口中，以管理员身份登录到 **SAML SSO for Jira by resolution GmbH 管理门户**。

8. 将鼠标悬停在小齿轮上，并单击“外接程序”。
    
    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/addon1.png)

9. 系统会你将重定向到“管理员访问权限”页。 输入密码，并单击“确认”按钮。

    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/addon2.png)

10. 在“外接程序”选项卡部分，单击“查找新外接程序”。 搜索“SAML Single Sign On (SSO) for JIRA”，并单击“安装”按钮安装新的 SAML 插件。

    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/addon7.png)

11. 插件安装随即开始。 单击“**关闭**”。

    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/addon8.png)

    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/addon9.png)

12. 单击“管理”。

    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/addon10.png)
    
13. 单击“配置”配置新的插件。

    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/addon11.png)

14. 在“SAML 单一登录插件配置”页上单击“添加新 IdP”按钮，配置标识提供者的设置。

    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/addon4.png)

15. 在“选择 SAML 标识提供者”页上，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/addon5a.png)
 
    a. 设置“Azure AD”作为 IdP 类型。
    
    b. 添加标识提供者（例如 Azure AD）的“名称”。
    
    c. 添加标识提供者（例如 Azure AD）的“说明”。
    
    d. 单击“资源组名称” 的 Azure 数据工厂。
    
16. 在“标识提供者配置”页上，单击“下一步”按钮。

    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/addon5b.png)

17. 在“导入 SAML IdP 元数据”页上，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/addon5c.png)

    a. 单击“加载文件”按钮，然后选择在步骤 5 中下载的元数据 XML 文件。

    b. 单击“导入”按钮。
    
    c. 在导入成功之前，请等待片刻。
    
    d. 单击“下一步”按钮。
    
18. 在“用户 ID 属性和转换”页上，单击“下一步”按钮。

    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/addon5d.png)
    
19. 在“用户创建和更新”页上，单击“保存并下一步”保存设置。   
    
    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/addon6a.png)
    
20. 在“测试设置”页上，单击“跳过测试并手动配置”暂时跳过用户测试。 此测试将在下一部分中执行，并需要在 Azure 门户中进行某些设置。 
    
    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/addon6b.png)
    
21. 在出现的显示“跳过测试意味着...”对话框中，单击“确定”。
    
    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/addon6c.png)

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>创建 SAML SSO for Jira by resolution GmbH 测试用户

要使 Azure AD 用户能够登录 SAML SSO for Jira by resolution GmbH，必须将其预配到 SAML SSO for Jira by resolution GmbH 中。  
在 SAML SSO for Jira by resolution GmbH 中，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 SAML SSO for Jira by resolution GmbH 公司站点。

2. 将鼠标悬停在小齿轮上，并单击“用户管理”。

    ![添加员工](./media/active-directory-saas-samlssojira-tutorial/user1.png) 

3. 重定向到“管理员访问权限”页后，输入密码，并单击“确认”按钮。

    ![添加员工](./media/active-directory-saas-samlssojira-tutorial/user2.png) 

4. 在“用户管理”选项卡部分，单击“创建用户”。

    ![添加员工](./media/active-directory-saas-samlssojira-tutorial/user3.png) 

5. 在“新建用户”对话框页中，执行以下步骤：

    ![添加员工](./media/active-directory-saas-samlssojira-tutorial/user4.png) 

    a. 在“电子邮件地址”文本框中，键入用户的电子邮件地址（例如 Brittasimon@contoso.com）。

    b. 在“全名”文本框中，键入用户（例如 Britta Simon）的全名。

    c. 在“用户名”文本框中，键入用户的电子邮件地址（例如 Brittasimon@contoso.com）。

    d. 在“密码”文本框中，键入用户的密码。

    e. 单击“创建用户”。   

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 SAML SSO for Jira by resolution GmbH 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 SAML SSO for Jira by resolution GmbH，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“SAML SSO for Jira by resolution GmbH”。

    ![配置单一登录](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“SAML SSO for Jira by resolution GmbH”磁贴就会自动登录到 SAML SSO for Jira by resolution GmbH 应用程序。
有关访问面板的详细信息，请参阅 [introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_203.png

