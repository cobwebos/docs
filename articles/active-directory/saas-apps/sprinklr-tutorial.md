---
title: 教程：Azure Active Directory 与 Sprinklr 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 与 Sprinklr 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 1c3b95686b8c91552615a9014102fd6a14f8c385
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277062"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>教程：Azure Active Directory 与 Sprinklr 集成

在本教程中，了解如何将 Sprinklr 与 Azure Active Directory (Azure AD) 集成。
将 Sprinklr 与 Azure AD 集成可提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Sprinklr。
* 可让用户使用其 Azure AD 帐户自动登录到 Sprinklr（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Sprinklr 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Sprinklr 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Sprinklr 支持“SP”发起的 SSO

## <a name="adding-sprinklr-from-the-gallery"></a>从库添加 Sprinklr

若要配置 Sprinklr 与 Azure AD 的集成，需要从库中将 Sprinklr 添加到托管 SaaS 应用列表。

**若要从库中添加 Sprinklr，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Sprinklr”，在结果面板中选择“Sprinklr”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的 Sprinklr](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 Sprinklr 配置和测试 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 Sprinklr 相关用户之间建立链接关系。

若要配置和测试 Sprinklr 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Sprinklr 单一登录](#configure-sprinklr-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Sprinklr 测试用户](#create-sprinklr-test-user)** - 在 Sprinklr 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Sprinklr 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Sprinklr”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![Sprinklr 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL： `https://<subdomain>.sprinklr.com`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL： `https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Sprinklr 客户端支持团队](https://www.sprinklr.com/contact-us/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

5. 在“使用 SAML 设置单一登录”页上，在“SAML 签名证书”部分中，单击“下载”以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Sprinklr”部分，根据要求复制相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-sprinklr-single-sign-on"></a>配置 Sprinklr 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 Sprinklr 公司站点。

1. 转到“管理”\>“设置”。

    ![管理](./media/sprinklr-tutorial/ic782907.png "Administration")

1. 从左侧窗格转到“管理合作伙伴”\>“单一登录”。

    ![管理合作伙伴](./media/sprinklr-tutorial/ic782908.png "Manage Partner")

1. 单击“+添加单一登录”。

    ![单一登录](./media/sprinklr-tutorial/ic782909.png "Single Sign-Ons")

1. 在“单一登录”页上，执行以下步骤：

    ![单一登录](./media/sprinklr-tutorial/ic782910.png "Single Sign-Ons")

    a. 在“名称”文本框中，键入配置名称（例如：WAADSSOTest)。

    b. 选择“启用”。

    c. 选择“使用新 SSO 证书”。

    d. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到“标识提供者证书”文本框。

    e. 在“实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值。

    f. 在“标识提供者登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    g. 在“标识提供者注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    h. 对于“SAML 用户 ID 类型”，请选择“断言包含用户的 sprinklr.com 用户名”。

    i. 对于“SAML 用户 ID 位置”，请选择“用户 ID 位于 Subject 语句的 NameIdentifier 元素中”。

    j. 单击“ **保存**”。

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，通过授予 Britta Simon 访问 Sprinklr 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Sprinklr”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Sprinklr”。

    ![应用程序列表中的 Sprinklr 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-sprinklr-test-user"></a>创建 Sprinklr 测试用户

1. 以管理员身份登录 Sprinklr 公司站点。

1. 转到“管理”\>“设置”。

    ![管理](./media/sprinklr-tutorial/ic782907.png "Administration")

1. 从左侧窗格转到“管理客户端”\>“用户”。

    ![设置](./media/sprinklr-tutorial/ic782914.png "设置")

1. 单击“添加用户”。

    ![设置](./media/sprinklr-tutorial/ic782915.png "设置")

1. 在“编辑用户”对话框中，执行以下步骤：

    ![编辑用户](./media/sprinklr-tutorial/ic782916.png "Edit user")

    a. 在“电子邮件”、“名字”和“姓氏”文本框中，键入要预配的 Azure AD 用户帐户的信息。

    b. 选择“禁用密码”。

    c. 选择“语言”。

    d. 选择“用户类型”。

    e. 单击“更新”。

    > [!IMPORTANT]
    > 必须选择“禁用密码”才能让用户通过标识提供者登录。 

1. 单击“角色”，并执行以下步骤：

    ![合作伙伴角色](./media/sprinklr-tutorial/ic782917.png "Partner Roles")

    a. 从“全局”列表中，选择 **ALL_Permissions**。  

    b. 单击“更新”。

> [!NOTE]
> 可以使用任何其他 Sprinklr 用户帐户创建工具或 Sprinklr 提供的 API 来预配 Azure AD 用户帐户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Sprinklr 磁贴时，应会自动登录到为其设置了 SSO 的 Sprinklr。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory 中的条件访问是什么？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
