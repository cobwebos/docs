---
title: 教程：Azure Active Directory 与 Heroku 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Heroku 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d7d72ec6-4a60-4524-8634-26d8fbbcc833
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 97b837b32c8ec679497f97c03ddccdc935985963
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-heroku"></a>教程：Azure Active Directory 与 Heroku 集成

本教程介绍了如何将 Heroku 与 Azure Active Directory (Azure AD) 进行集成。

将 Heroku 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Heroku
- 可以让用户使用其 Azure AD 帐户自动登录到 Heroku（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Heroku 的集成，需要以下项目：

- Azure AD 订阅
- 已启用 Heroku 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Heroku
2. 配置和测试 Azure AD 单一登录

## <a name="adding-heroku-from-the-gallery"></a>从库中添加 Heroku
要配置 Heroku 与 Azure AD 的集成，需要将库中的 Heroku 添加到托管的 SaaS 应用列表。

**若要从库中添加 Heroku，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Heroku”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_search.png)

5. 在结果面板中，选择“Heroku”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于名为“Britta Simon”的测试用户配置和测试 Heroku 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Heroku 用户。 换句话说，需要建立 Azure AD 用户与 Heroku 中相关用户之间的关联关系。

可通过将 Azure AD 中“用户名”的值指定为 Heroku 中“用户名”的值来建立此链接关系。

若要配置和测试 Heroku 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Heroku 测试用户](#creating-a-heroku-test-user) - 在 Heroku 中创建 Britta Simon 的对应用户，并且将其链接到用户在 Azure AD 中的代表。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，在 Azure 门户中启用 Azure AD 单一登录，并在 Heroku 应用程序中配置单一登录。

**若要配置 Heroku 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 Heroku 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_samlbase.png)

3. 在“Heroku 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：    
    `https://sso.heroku.com/saml/<company-name>/init`

    b. 在“标识符 URL”文本框中，使用以下模式键入 URL：            
    `https://sso.heroku.com/saml/<company-name>`

    > [!NOTE]
    >这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 可从 Heroku 团队获取这些值，本文的稍后部分将对此进行描述。 
        
4. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![配置单一登录](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-heroku-tutorial/tutorial_general_400.png)

6. 若要在 Heroku 中启用 SSO，请执行以下步骤：
   
    a. 以管理员身份登录到 Heroku 帐户。

    b. 单击“设置”选项卡。

    c. 在“单一登录”页面上，单击“上载元数据”。

    d. 上传从 Azure 门户下载的元数据文件。

    e. 安装成功后，管理员会看到一个确认对话框，其中显示最终用户的 SSO 登录 URL。 

    f. 复制“Heroku 登录 URL”和“Heroku 实体 ID”值，返回 Azure 门户中的“Heroku 域和 URL”部分，并分别将这两个值粘贴到“登录 URL”和“标识符”文本框中。

    ![配置单一登录](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_52.png) 
    
8. 单击“资源组名称” 的 Azure 数据工厂。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory 企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-heroku-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-heroku-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-heroku-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-heroku-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-heroku-test-user"></a>创建 Heroku 测试用户

在本部分中，会在 Heroku 中创建一个名为“Britta Simon”的用户。 Heroku 支持在默认情况下启用的实时预配。

此部分不存在任何操作项。 访问 Heroku 时，如果该用户尚不存在，则将创建新用户。 预配帐户后，最终用户会收到一封验证电子邮件，需单击确认链接。

>[!NOTE]
>如果需要手动创建用户，则需要联系 [Heroku 客户端支持团队](https://www.heroku.com/support)。
>  

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Heroku 的访问权限，支持其使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 Heroku，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Heroku”。

    ![配置单一登录](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Heroku 磁贴时，应该会自动登录 Heroku 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_203.png
