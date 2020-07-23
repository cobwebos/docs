---
title: 教程：Azure Active Directory 与 Help Scout 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Help Scout 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b71ccbc6cfdb9d3d37fc46b0e932fa98eee2fb43
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159091"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>教程：Azure Active Directory 与 Help Scout 集成

本教程介绍如何将 Help Scout 与 Azure Active Directory (Azure AD) 集成。
将 Help Scout 与 Azure AD 集成具有以下优势：

* 可以在 Azure AD 中控制谁有权访问 Help Scout。
* 可让用户使用其 Azure AD 帐户自动登录到 Help Scout（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

要配置 Azure AD 与 Help Scout 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 Help Scout 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Help Scout 支持 **SP 和 IDP** 发起的 SSO
* Help Scout 支持**恰时**用户预配

## <a name="adding-help-scout-from-the-gallery"></a>从库中添加 Help Scout

要配置 Help Scout 与 Azure AD 的集成，需要从库中将 Help Scout 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Help Scout”   。
1. 从结果面板中选择“Help Scout”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，请基于名为“B.Simon”的测试用户，配置并测试 Help Scout 的 Azure AD 单一登录  。
若要使单一登录有效，需要在 Azure AD 用户与 Help Scout 相关用户之间建立链接关系。

要配置和测试 Help Scout 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Help Scout SSO](#configure-help-scout-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Help Scout 测试用户](#create-help-scout-test-user)** - 在 Help Scout 中创建 B.Simo 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Help Scout 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的 **Help Scout** 应用程序集成页上，选择“单一登录”。 

    ![配置单一登录链接](common/select-sso.png)

1. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

1. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中执行以下步骤：

    ![Help Scout 域和 URL 单一登录信息](common/idp-intiated.png)

    a. “标识符”是 Help Scout 的“受众 URI (服务提供程序实体 ID)”，以 `urn:` 开头

    b. “回复 URL”是 Help Scout 的“发回 URL (断言使用者服务 URL)”，以 `https://` 开头 

    > [!NOTE]
    > 这些 URL 中的值仅用于演示。 需要使用实际回复 URL 和标识符更新这些值。 可从“身份验证”部分下面的“单一登录”选项卡获取这些值，本教程稍后将会介绍。 

1. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![Help Scout 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”文本框中，键入 URL `https://secure.helpscout.net/members/login/`

1. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Help Scout”部分中，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为“B.Simon”的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“姓名”字段中，输入“B.Simon”   。
  
    b. 在“用户名”字段中，键入 B.Simon\@yourcompanydomain.extension  
    例如： B.Simon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，请向 B.Simon 授予对 Help Scout 的访问权限，使之能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Help Scout”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Help Scout”  。

    ![应用程序列表中的 Help Scout 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的“B. Simon”，然后单击屏幕底部的“选择”按钮    。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-help-scout-sso"></a>配置 Help Scout SSO

1. 若要在 Help Scout 中自动执行配置，需要安装“我的应用安全登录浏览器扩展”，方法是单击“安装扩展”   。

    ![我的应用扩展](common/install-myappssecure-extension.png)

1. 将扩展添加到浏览器后，单击“设置 Help Scout”，此时系统会将你定向到 Help Scout 应用程序  。 请在此处提供管理员凭据以登录到 Help Scout。 浏览器扩展会自动配置应用程序，并自动执行第 3-7 步。

    ![设置配置](common/setup-sso.png)

1. 若要手动设置 Scout，请打开新的 Web 浏览器窗口，以管理员身份登录 Help Scout 公司站点，并执行以下步骤：

1. 在顶部菜单中单击“管理”，并从下拉菜单中选择“公司”。  

    ![配置单一登录](./media/helpscout-tutorial/settings1.png)

1. 从左侧导航窗格中选择“身份验证”。 

    ![配置单一登录](./media/helpscout-tutorial/settings2.png)

1. 随后会转到“SAML 设置”部分，请执行以下步骤：

    ![配置单一登录](./media/helpscout-tutorial/settings3.png)

    a. 复制“发回 URL (断言使用者服务 URL)”值，并将其粘贴到 Azure 门户的“基本 SAML 配置”部分的“回复 URL”文本框中。   

    b. 复制“受众 URL (服务提供商实体 ID)”值，并将其粘贴到 Azure 门户的“基本 SAML 配置”部分的“标识符”文本框中。   

1. 将“启用 SAML”切换为打开状态，并执行以下步骤： 

    ![配置单一登录](./media/helpscout-tutorial/settings4.png)

    a. 在“单一登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    b. 单击“上传证书”，上传已从 Azure 门户下载的**证书 (Base64)**。

    c. 在“电子邮件域”文本框中输入组织的电子邮件域，例如 `contoso.com`。  可使用逗号分隔多个域。 每当 Help Scout 用户或管理员在 [Help Scout 登录页](https://secure.helpscout.net/members/login/)上输入该特定域时，都会路由到“标识提供者”，以使用其凭据进行身份验证。

    d. 最后，可以希望用户只能通过此方法登录到 Help Scout，可将“强制 SAML 登录”切换为打开状态。  如果希望用户仍旧使用其 Help Scout 凭据登录，可将此选项切换为关闭状态。 即使启用此选项，帐户所有者也始终能够使用其帐户密码登录到 Help Scout。

    e. 单击“保存”  。

### <a name="create-help-scout-test-user"></a>创建 Help Scout 测试用户

在本部分中，我们将在 Help Scout 中创建一个名为 B.Simon 的用户。 Help Scout 支持默认启用的恰时用户预配。 此部分不存在任何操作项。 如果 Help Scout 中尚不存在用户，身份验证后会创建一个新用户。

### <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Help Scout 磁贴时，应会自动登录到为其设置了 SSO 的 Help Scout。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Help Scout](https://aad.portal.azure.com/)