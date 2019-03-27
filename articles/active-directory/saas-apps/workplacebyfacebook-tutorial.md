---
title: 教程：Azure Active Directory 与 Workplace by Facebook 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Workplace by Facebook 间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27173c8beeecf2be43e80f59df8907952734c06b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57900873"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>教程：Azure Active Directory 与 Workplace by Facebook 集成

本教程介绍如何将 Workplace by Facebook 与 Azure Active Directory (Azure AD) 集成。
将 Workplace by Facebook 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Workplace by Facebook。
* 可以让用户使用其 Azure AD 帐户自动登录到 Workplace by Facebook（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Workplace by Facebook 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Workplace by Facebook 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

> [!NOTE]
> Facebook 有两种产品：Workplace Standard（免费）和 Workplace Premium（付费）。 任何 Workplace Premium 租户都可以配置 SCIM 和 SSO 集成，对成本或所需的许可证没有其他影响。 SSO 和 SCIM 在 Workplace Standard 实例中不可用。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Workplace by Facebook 支持 SP 发起的 SSO
* Workplace by Facebook 支持实时预配
* Workplace by Facebook 支持[自动用户预配](workplacebyfacebook-provisioning-tutorial.md)

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>从库添加 Workplace by Facebook

要配置 Workplace by Facebook 与 Azure AD 的集成，需要从库中将 Facebook 的 Workplace 添加到托管 SaaS 应用列表。

**若要从库中添加 Workplace by Facebook，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Workplace by Facebook”，在结果面板中选择“Workplace by Facebook”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的 Workplace by Facebook](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Workplace by Facebook 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 Workplace by Facebook 相关用户之间建立链接关系。

若要配置和测试 Workplace by Facebook 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Workplace by Facebook 单一登录](#configure-workplace-by-facebook-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. [创建 Workplace by Facebook 测试用户](#create-workplace-by-facebook-test-user) - 在 Workplace by Facebook 中创建 Britta Simon 的对应用户，将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Workplace by Facebook 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Workplace by Facebook”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![Workplace by Facebook 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<instancename>.facebook.com`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请参阅工作区公司仪表板的身份验证页，了解你的工作区社区的正确值。

5. 在“使用 SAML 设置单一登录”页上，在“SAML 签名证书”部分中，单击“下载”以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Workplace by Facebook”部分中，根据要求复制相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-workplace-by-facebook-single-sign-on"></a>配置 Workplace by Facebook 单一登录

1. 在另一 Web 浏览器窗口中，以管理员身份登录 Workplace by Facebook 公司站点。
  
    > [!NOTE]
    > 作为 SAML 身份验证过程的一部分，Workplace 可使用查询字符串（最大为 2.5 KB）将参数传递给 Azure AD。

2. 在“管理面板”中，转到“安全性”标签页。

    ![管理面板](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

3. 在“身份验证”标签页下，选择“单一登录(SSO)”并执行以下步骤：

    ![“身份验证”标签页](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. 在“SAML URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    b. 在“SAML 颁发者 URI”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值。

    c. 在“SAML 注销重定向”（可选）中，粘贴从 Azure 门户复制的“注销 URL”值。

    d. 在记事本中打开从 Azure 门户下载的 base-64 编码证书，将其内容复制到剪贴板，然后再粘贴到“SAML 证书”文本框。

    e. 复制实例的“受众 URL”，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符(实体 ID)”文本框中。

    f. 复制实例的“收件人 URL”，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“登录 URL”文本框中。

    g. 滚动到该部分的底部，单击“测试 SSO”按钮。 此时会出现包含 Azure AD 登录页面的一个弹出窗口。 照常输入凭据进行身份验证。

    故障排除：确保从 Azure AD 返回的电子邮件地址与用于登录的 Workplace 帐户相同。

    h. 成功完成测试后，滚动到页面底部并单击“保存”按钮。

    i. 现在所有使用 Workplace 的用户都会在 Azure AD 登录页面上进行身份验证。

4. **SAML 注销重定向（可选）** -

    可以选择配置一个 SAML 注销 URL，该 URL 可用于指向 Azure AD 的注销页面。 启用并配置此设置后，用户将不会再被定向到 Workplace 注销页面。 用户会转而被重定向到在“SAML 注销重定向”设置中添加的 URL。

### <a name="configuring-reauthentication-frequency"></a>配置重新进行身份验证的频率

可将 Workplace 配置为每天、每三天、每周、每两周、每个月提示进行 SAML 检查或从不进行检查。

> [!NOTE]
> 移动应用程序上 SAML 检查的最小值为每周。

你也可以使用此按钮强制为所有用户重置 SAML：现在需要对所有用户进行 SAML 身份验证。

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

在本部分中，通过授予 Britta Simon 访问 Workplace by Facebook 的权限，使她能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Workplace by Facebook”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，键入并选择“Workplace by Facebook”。

    ![应用程序列表中的 Workplace by Facebook 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-workplace-by-facebook-test-user"></a>创建 Workplace by Facebook 测试用户

本部分将在 Workplace by Facebook 中创建一个名为 Britta Simon 的用户。 Workplace by Facebook 支持默认启用的实时预配。

在此部分中无需进行任何操作。 尝试访问 Workplace by Facebook 时，如果 Workplace by Facebook 中尚不存在用户，则系统会创建一个新用户。

>[!Note]
>如需手动创建用户，请联系 [Workplace by Facebook 客户端支持团队](https://workplace.fb.com/faq/)

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Workplace by Facebook 磁贴时，应当会自动登录到为其设置了 SSO 的 Workplace by Facebook。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [配置用户预配](workplacebyfacebook-provisioning-tutorial.md)
