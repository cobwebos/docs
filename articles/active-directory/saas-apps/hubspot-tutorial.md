---
title: 教程：Azure Active Directory 与 HubSpot 的集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 与 HubSpot 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: adcd0f094d584e770f1a3f4938ee677ba58a21a8
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995687"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>教程：Azure Active Directory 与 HubSpot 的集成

在本教程中，了解如何将 HubSpot 与 Azure Active Directory (Azure AD) 集成。
将 HubSpot 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 HubSpot。
* 可让用户使用其 Azure AD 帐户自动登录到 HubSpot（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 HubSpot 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 启用了 HubSpot 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* HubSpot 支持“SP 和 IDP”发起的 SSO

## <a name="adding-hubspot-from-the-gallery"></a>从库中添加 HubSpot

要配置 HubSpot 与 Azure AD 的集成，需要从库中将 HubSpot 添加到托管 SaaS 应用列表。

若要从库中添加 HubSpot，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“HubSpot”，在结果面板中选择“HubSpot”，然后单击“添加”按钮添加应用程序。

    ![结果列表中的 HubSpot](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 HubSpot 配置和测试 Azure AD 单一登录。
若要使单一登录有效，需要在 Azure AD 用户与 HubSpot 相关用户之间建立链接关系。

若要配置和测试 HubSpot 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 HubSpot 单一登录](#configure-hubspot-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 HubSpot 测试用户](#create-hubspot-test-user)** - 在 HubSpot 中创建 Britta Simon 的对应用户，将其关联到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 HubSpot 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，在“HubSpot”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤：

    ![HubSpot 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://api.hubspot.com/login-api/v1/saml/login?portalId=<CUSTOMER ID>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://api.hubspot.com/login-api/v1/saml/acs?portalId=<CUSTOMER ID>`

    > [!NOTE]
    > 这些不是实际值。 本教程稍后将介绍如何使用实际的标识符和回复 URL 来更新该值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![HubSpot 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”文本框中，键入 URL：`https://app.hubspot.com/login`

6. 在“使用 SAML 设置单一登录”页上，在“SAML 签名证书”部分中，单击“下载”以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

7. 在“设置 HubSpot”部分中，根据要求复制相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-hubspot-single-sign-on"></a>配置 HubSpot 单一登录

1. 在浏览器中打开新选项卡并登录到 HubSpot 管理员帐户。

2. 单击页面右上角的“设置”图标。

    ![配置单一登录](./media/hubspot-tutorial/config1.png)

3. 单击“帐户默认值”。

    ![配置单一登录](./media/hubspot-tutorial/config2.png)

4. 向下滚动到“安全”部分，并单击“设置”。

    ![配置单一登录](./media/hubspot-tutorial/config3.png)

5. 在“设置单一登录”部分中，执行以下步骤：

    ![配置单一登录](./media/hubspot-tutorial/config4.png)

    a. 单击“复制”按钮以复制“受众 URI (服务提供商实体 ID)”值，并将其粘贴到 Azure 门户中“基本 SAML 配置”部分的“标识符”文本框中。

    b. 单击“复制”按钮以复制“登录 URI、ACS、收件人或重定向”值，并将其粘贴到 Azure 门户中“基本 SAML 配置”部分的“回复 URL”文本框中。

    c. 在“标识提供者标识符或证书颁发者 URL”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值。

    d. 在“标识提供者单一登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    e. 在记事本中打开已下载的  **Certificate(Base64)**  文件。 将其内容复制到剪贴板，然后粘贴到“X.509 证书”框 ****。

    f. 单击“验证”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 `brittasimon@yourcompanydomain.extension`。 例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 HubSpot 的访问权限支持她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“HubSpot”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“HubSpot”。

    ![应用程序列表中的 HubSpot 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-hubspot-test-user"></a>创建 HubSpot 测试用户

为了使 Azure AD 用户能够登录到 HubSpot，必须将其预配到 HubSpot 中。 就 HubSpot 来说，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 HubSpot 公司站点。

2. 单击页面右上角的“设置”图标。

    ![配置单一登录](./media/hubspot-tutorial/config1.png)

3. 单击“用户和团队”。

    ![配置单一登录](./media/hubspot-tutorial/user1.png)

4. 单击“创建用户”。

    ![配置单一登录](./media/hubspot-tutorial/user2.png)

5. 在“添加电子邮件地址”文本框中输入用户的电子邮件地址（如，`brittasimon\@contoso.com`），然后单击“下一步”。

    ![配置单一登录](./media/hubspot-tutorial/user3.png)

6. 在“创建用户”部分中，请浏览各个选项卡并为用户选择相应的选项和权限，然后单击“下一步”。

    ![配置单一登录](./media/hubspot-tutorial/user4.png)

7. 单击“发送”以向用户发送邀请。

    ![配置单一登录](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > 接受邀请后，将激活用户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 HubSpot 磁贴时，应当会自动登录到为其设置了 SSO 的 HubSpot。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

