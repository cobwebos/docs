---
title: 教程：Azure Active Directory 与 Zendesk 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Zendesk 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d656a72e681c8cc73fa8b660684b8c194df3ae48
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301207"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>教程：Azure Active Directory 与 Zendesk 的集成

本教程介绍了如何将 Zendesk 与 Azure Active Directory (Azure AD) 集成。
将 Zendesk 与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 Zendesk。
* 可以让用户使用其 Azure AD 帐户自动登录到 Zendesk（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Zendesk 的集成，需备齐以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了单一登录的 Zendesk 订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Zendesk 支持 **SP** 发起的 SSO

* Zendesk 支持[**自动**用户预配](zendesk-provisioning-tutorial.md)

## <a name="adding-zendesk-from-the-gallery"></a>从库中添加 Zendesk

若要配置 Zendesk 与 Azure AD 的集成，需要从库中将 Zendesk 添加到托管 SaaS 应用列表。

若要从库中添加 Zendesk，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Zendesk”，在结果面板中选择“Zendesk”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的 Zendesk](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为 **Britta Simon** 的测试用户配置和测试 Zendesk 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 Zendesk 中相关用户之间建立链接关系。

若要配置和测试 Zendesk 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Zendesk 单一登录](#configure-zendesk-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Zendesk 测试用户](#create-zendesk-test-user)** - 在 Zendesk 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Zendesk 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的 Zendesk 应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![Zendesk 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.zendesk.com`

  b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<subdomain>.zendesk.com`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Zendesk 客户端支持团队](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

5. Zendesk 应用程序需要特定格式的 SAML 断言。 没有强制的 SAML 属性，但可以选择从应用程序集成页上的“用户属性”部分中进行管理。 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“用户属性”对话框。

    ![图像](common/edit-attribute.png)

6. 在“用户属性”对话框的“用户声明”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：

    a. 单击“添加新声明”以打开“管理用户声明”对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 将“命名空间”留空。

    d. 选择“源”作为“属性”。

    e. 在“源属性”列表中，选择合适的属性值。

    f. 单击“确定”

    g. 单击“ **保存**”。

    > [!NOTE]
    > 可以使用扩展属性添加默认情况下不在 Azure AD 中的属性。 单击 [可在 SAML 中设置的用户属性](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-)，获取 Zendesk 接受的 SAML 属性的完整列表。

7. 在“使用 SAML 设置单一登录”页面上，在“SAML 签名证书”部分中，复制**指纹**并将其保存到计算机上。

    ![复制指纹值](common/copy-certificatethumbprint.png)

8. 在“设置 Zendesk”部分中，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

9. 可通过两种方式配置 Zendesk：自动和手动。
  
10. 若要在 Zendesk 中自动执行配置，需要通过单击“安装扩展”，安装“我的应用安全登录浏览器扩展”。

    ![图像](./media/zendesk-tutorial/install_extension.png)

11. 将扩展添加到浏览器后，单击“安装 Zendesk”会将你定向到 Zendesk 应用程序。 在此处，请提供管理员凭据以登录到 Zendesk。 浏览器扩展将自动配置应用程序，并自动执行“配置 Zendesk 单一登录”部分。

    ![图像](./media/zendesk-tutorial/d2_saml.png)

### <a name="configure-zendesk-single-sign-on"></a>配置 Zendesk 单一登录

1. 如果你想要手动安装 Zendesk，打开新的 Web 浏览器窗口，以管理员身份登录到你的 Zendesk 公司网站，并执行以下步骤：

2. 单击“管理”。

3. 在左侧导航窗格中，单击“设置”，并单击“安全”。

4. 在“安全”页中，执行以下步骤：

    ![安全](././media/zendesk-tutorial/ic773089.png "安全")

    ![单一登录](././media/zendesk-tutorial/ic773090.png "单一登录")

    a. 单击“管理员和代理”选项卡。

    b. 选择“单一登录 (SSO) 和 SAML”，并选择“SAML”。

    c. 在“SAML SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    d. 在“远程注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    e. 在“证书指纹”文本框中，粘贴从 Azure 门户复制的证书“指纹”值。

    f. 单击“ **保存**”。

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

在本部分中，通过授予 Britta Simon 访问 Zendesk 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Zendesk”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，键入并选择“Zendesk”。

    ![应用程序列表中的 Zendesk 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-zendesk-test-user"></a>创建 Zendesk 测试用户

本部分的目的是在 Zendesk 中创建名为“Britta Simon”的用户。 Zendesk 支持在默认情况下启用的自动用户预配。 有关如何配置自动用户预配的更多详细信息，请参见[此处](Zendesk-provisioning-tutorial.md)。

如果需要手动创建用户，请执行以下步骤：

> [!NOTE]
> 在登录时会自动预配“最终用户”帐户。 在登录前需要在 Zendesk 中手动预配“代理”和“管理员”帐户。

1. 登录到“Zendesk”租户。

2. 选择“客户列表”选项卡。

3. 选择“用户”选项卡，并单击“添加”。

    ![添加用户](././media/zendesk-tutorial/ic773632.png "添加用户")
4. 键入要预配的现有 Azure AD 帐户的**名称**和**电子邮件**，然后单击“保存”。

    ![新用户](././media/zendesk-tutorial/ic773633.png "新用户")

> [!NOTE]
> 可以使用 Zendesk 提供的任何其他 Zendesk 用户帐户创建工具或 API 来预配 AAD 用户帐户。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Zendesk 磁贴时，应当会自动登录到你为其设置了 SSO 的 Zendesk。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [配置用户预配](zendesk-provisioning-tutorial.md)