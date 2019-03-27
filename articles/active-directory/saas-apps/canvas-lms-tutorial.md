---
title: 教程：Azure Active Directory 与 Canvas 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Canvas 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7556e93e6e7b7e9bdb1f19f5a6e5b3c15c7b910
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57894064"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>教程：Azure Active Directory 与 Canvas 集成

本教程介绍了如何将 Canvas 与 Azure Active Directory (Azure AD) 集成。
将 Canvas 与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 Canvas。
* 可以让用户使用其 Azure AD 帐户自动登录到 Canvas（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Canvas 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Canvas 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Canvas 支持 SP 发起的 SSO

## <a name="adding-canvas-from-the-gallery"></a>从库中添加 Canvas

若要配置 Canvas 与 Azure AD 的集成，需要从库中将 Canvas 添加到托管 SaaS 应用列表。

**若要从库中添加 Canvas，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Canvas”，在结果面板中选择“Canvas”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的 Canvas](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于名为“Britta Simon”的测试用户配置和测试 Canvas 的 Azure AD 单一登录。
若要使单一登录有效，需要在 Azure AD 用户与 Canvas 中的相关用户之间建立链接关系。

若要配置并测试 Canvas 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Canvas 单一登录](#configure-canvas-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Canvas 测试用户](#create-canvas-test-user)** - 在 Canvas 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Canvas 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/) 中的 Canvas 应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![Canvas 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<tenant-name>.instructure.com`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Canvas 客户端支持团队](https://community.canvaslms.com/community/help)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

5. 在“SAML 签名证书”部分中，单击“编辑”按钮以打开“SAML 签名证书”对话框。

    ![编辑 SAML 签名证书](common/edit-certificate.png)

6. 在“SAML 签名证书”部分中，复制**指纹**并将其保存在计算机上。

    ![复制指纹值](common/copy-thumbprint.png)

7. 在“设置 Canvas”部分，根据要求复制相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-canvas-single-sign-on"></a>配置 Canvas 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 Canvas 公司站点。

2. 转到“课程”**\>“托管帐户”\>**“Microsoft”。

    ![Canvas](./media/canvas-lms-tutorial/ic775990.png "Canvas")

3. 在左侧导航窗格中，选择“身份验证”，并单击“添加新 SAML 配置”。

    ![身份验证](./media/canvas-lms-tutorial/ic775991.png "身份验证")

4. 在“当前集成”页上，执行以下步骤：

    ![当前集成](./media/canvas-lms-tutorial/ic775992.png "Current Integration")

    a. 在“IdP 实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure Ad 标识符”值。

    b. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    c. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    d. 在“更改密码链接”文本框中，粘贴从 Azure 门户复制的“更改密码 URL”值。

    e. 在“证书指纹”文本框中，粘贴从 Azure 门户复制的证书“指纹”值。

    f. 从“登录名属性”列表中，选择 **NameID**。

    g. 从“标识符格式”列表中，选择 **emailAddress**。

    h. 单击“保存身份验证设置”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中，键入 brittasimon\@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Canvas 的访问权限支持她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Canvas”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Canvas”。

    ![应用程序列表中的 Canvas 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-canvas-test-user"></a>创建 Canvas 测试用户

为了使 Azure AD 用户能够登录到 Canvas，必须将其预配到 Canvas 中。 对于 Canvas，需要手动执行用户预配。

**若要预配用户帐户，请执行以下步骤：**

1. 登录“Canvas”租户。

2. 转到“课程”**\>“托管帐户”\>**“Microsoft”。

   ![Canvas](./media/canvas-lms-tutorial/ic775990.png "Canvas")

3. 单击“用户”。

   ![用户](./media/canvas-lms-tutorial/ic775995.png "用户")

4. 单击“添加新用户”。

   ![用户](./media/canvas-lms-tutorial/ic775996.png "用户")

5. 在“添加新用户”对话框页上，执行以下步骤：

   ![添加用户](./media/canvas-lms-tutorial/ic775997.png "添加用户")

   a. 在“全名”文本框中输入用户的姓名，例如 BrittaSimon。

   b. 在“电子邮件”文本框中，输入用户的电子邮件，例如 brittasimon\@contoso.com。

   c. 在“登录名”文本框中，输入用户的 Azure AD 电子邮件地址（例如 brittasimon\@contoso.com）。

   d. 选择“通过电子邮件告知用户此帐户创建操作”。

   e. 单击“添加用户”。

> [!NOTE]
> 可以使用 Canvas 提供的任何其他 Canvas 用户帐户创建工具或 API 来预配 AAD 用户帐户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Canvas 磁贴时，应会自动登录到为其设置了 SSO 的 Canvas。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

