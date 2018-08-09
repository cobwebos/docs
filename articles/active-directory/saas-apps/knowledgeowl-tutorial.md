---
title: 教程：Azure Active Directory 与 KnowledgeOwl 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 KnowledgeOwl 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: e902f5969611dd3b1074e899003abe5067857c04
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446129"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>教程：Azure Active Directory 与 KnowledgeOwl 集成

本教程介绍如何将 KnowledgeOwl 与 Azure Active Directory (Azure AD) 集成。

将 KnowledgeOwl 与 Azure AD 集成可以提供以下优势：

- 可在 Azure AD 中控制谁有权访问 KnowledgeOwl。
- 可以让用户通过其 Azure AD 帐户自动登录到 KnowledgeOwl（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 KnowledgeOwl 的集成，需要具有以下各项：

- Azure AD 订阅
- 启用了 KnowledgeOwl 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 KnowledgeOwl
1. 配置和测试 Azure AD 单一登录

## <a name="adding-knowledgeowl-from-the-gallery"></a>从库添加 KnowledgeOwl
要配置 KnowledgeOwl 与 Azure AD 的集成，需要从库中将 KnowledgeOwl 添加到托管 SaaS 应用列表。

若要从库中添加 KnowledgeOwl，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

1. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
1. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

1. 在搜索框中，键入“KnowledgeOwl”，在结果面板中选择“KnowledgeOwl”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 KnowledgeOwl 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 KnowledgeOwl 用户。 换句话说，需要建立 Azure AD 用户与 KnowledgeOwl 中相关用户之间的链接关系。

若要配置并测试 KnowledgeOwl 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. **[创建 KnowledgeOwl 测试用户](#create-a-knowledgeowl-test-user)** - 在 KnowledgeOwl 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 KnowledgeOwl 应用程序中配置单一登录。

若要配置 KnowledgeOwl 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的 KnowledgeOwl 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_samlbase.png)

1. 在“KnowledgeOwl 域和 URL”部分中，如果要在“IDP”发起的模式下配置应用程序，请执行以下步骤：

    ![KnowledgeOwl 域和 URL 单一登录信息](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url.png)

    a. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：
    |||
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|
    |||

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||

1. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![KnowledgeOwl 域和 URL 单一登录信息](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url1.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||
     
    > [!NOTE]
    > 这些不是实际值。 将需要从实际的标识符、答复 URL 和登录 URL 更新这些值（本教程稍后会介绍）。

1. KnowledgeOwl 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。

    ![配置单一登录](./media/knowledgeowl-tutorial/attribute.png)

1. 在“单一登录”对话框的“用户属性”部分中，按上图所示配置 SAML 令牌属性，再执行以下步骤：
    
    | 属性名称 | 属性值 | 命名空间|
    | ------------------- | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. 单击“添加属性”，打开“添加属性”对话框。
    
    ![配置单一登录](./media/knowledgeowl-tutorial/tutorial_attribute_04.png)

    ![配置单一登录](./media/knowledgeowl-tutorial/tutorial_attribute_05.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。
    
    c. 在“值”列表中，选择为该行显示的属性值。

    d. 在“命名空间”列表中输入为该行显示的命名空间值。
    
    e. 单击“确定” 。

1. 在“SAML 签名证书”部分中，单击“证书(原始)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_certificate.png) 

1. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/knowledgeowl-tutorial/tutorial_general_400.png)
    
1. 在“KnowledgeOwl 配置”部分，单击“配置 KnowledgeOwl”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![KnowledgeOwl 配置](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_configure.png)

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 KnowledgeOwl 公司站点。

1. 单击“设置”，然后选择“安全性”。

    ![KnowledgeOwl 配置](./media/knowledgeowl-tutorial/configure1.png)

1. 向下滚动至“SAML SSO 集成”并执行以下步骤：
    
    ![KnowledgeOwl 配置](./media/knowledgeowl-tutorial/configure2.png)

    a. 选择“启用 SAML SSO”。

    b. 复制“SP 实体 ID”值并将其粘贴到 Azure 门户上“KnowledgeOwl 域和 URL”部分的“标识符(实体 ID)”中。

    c. 复制“SP 登录 URL”值并将其粘贴到 Azure 门户上“KnowledgeOwl 域和 URL”部分的“登录 URL 和答复 URL”文本框中。

    d. 在“IdP 实体 ID”文本框中，粘贴从 Azure 门户复制的“SAML 实体 ID”值。

    e. 在“IdP 登录 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    f. 在“IdP 注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值

    g. 单击“上传 IdP 证书”，上传从 Azure 门户下载的证书。

    h. 单击“映射 SAML 属性”以映射属性并执行以下操作：
    
    ![KnowledgeOwl 配置](./media/knowledgeowl-tutorial/configure3.png)

    * 在“SSO ID”文本框中输入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid`
    * 在“用户名/电子邮件”文本框中输入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`。
    * 在“名字”文本框中输入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`。
    * 在“姓氏”文本框中输入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`。
    * 单击“保存”

    i. 单击页面底部的“保存”。

    ![KnowledgeOwl 配置](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/knowledgeowl-tutorial/create_aaduser_01.png)

1. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/knowledgeowl-tutorial/create_aaduser_02.png)

1. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/knowledgeowl-tutorial/create_aaduser_03.png)

1. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/knowledgeowl-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-knowledgeowl-test-user"></a>创建一个 KnowledgeOwl 测试用户

本部分的目的是在 KnowledgeOwl 中创建名为 Britta Simon 的用户。 KnowledgeOwl 支持在默认情况下启用的实时预配。 此部分不存在任何操作项。 如果用户尚不存在，则在尝试访问 KnowledgeOwl 期间会创建一个新用户。
>[!Note]
>如果需要手动创建用户，请联系 [KnowledgeOwl 支持团队](mailto:support@knowledgeowl.com)。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 KnowledgeOwl 的权限，支持其使用 Azure 单一登录。

![分配用户角色][200] 

若要将 Britta Simon 分配到 KnowledgeOwl，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“KnowledgeOwl”。

    ![应用程序列表中的 KnowledgeOwl 链接](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_app.png)  

1. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

1. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

1. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 KnowledgeOwl 磁贴时，应自动登录到 KnowledgeOwl 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/knowledgeowl-tutorial/tutorial_general_01.png
[2]: ./media/knowledgeowl-tutorial/tutorial_general_02.png
[3]: ./media/knowledgeowl-tutorial/tutorial_general_03.png
[4]: ./media/knowledgeowl-tutorial/tutorial_general_04.png

[100]: ./media/knowledgeowl-tutorial/tutorial_general_100.png

[200]: ./media/knowledgeowl-tutorial/tutorial_general_200.png
[201]: ./media/knowledgeowl-tutorial/tutorial_general_201.png
[202]: ./media/knowledgeowl-tutorial/tutorial_general_202.png
[203]: ./media/knowledgeowl-tutorial/tutorial_general_203.png

