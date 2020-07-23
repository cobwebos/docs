---
title: 教程：Azure Active Directory 与 Mimecast Personal Portal 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Mimecast Personal Portal 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d28835472198a1fddc5f7ed0fe5f0037b602f039
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848291"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-personal-portal"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Mimecast Personal Portal 集成

本教程介绍如何将 Mimecast Personal Portal 与 Azure Active Directory (Azure AD) 集成。 将 Mimecast Personal Portal 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Mimecast Personal Portal。
* 让用户使用其 Azure AD 帐户自动登录到 Mimecast Personal Portal。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Mimecast Personal Portal 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Mimecast Personal Portal 支持 SP 和 IDP 发起的 SSO
* 配置 Mimecast Personal Portal 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>从库中添加 Mimecast Personal Portal

要配置 Mimecast Personal Portal 与 Azure AD 的集成，需要从库中将 Mimecast Personal Portal 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务。
1. 导航到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入 Mimecast Personal Portal。 
1. 在结果面板中选择“Mimecast Personal Portal”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-mimecast-personal-portal"></a>配置并测试 Mimecast Personal Portal 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置并测试 Mimecast Personal Portal 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Mimecast Personal Portal 中的相关用户之间建立链接关系。

若要配置并测试 Mimecast Personal Portal 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Mimecast Personal Portal SSO](#configure-mimecast-personal-portal-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Mimecast Personal Portal 测试用户](#create-mimecast-personal-portal-test-user) - 在 Mimecast Personal Portal 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Mimecast Personal Portal”应用程序集成页面上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置部分”中执行以下步骤：

    a. 在“标识符”文本框中，使用以下模式键入 URL：

    | 区域  |  值 | 
    | --------------- | --------------- |
    | 欧洲          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | United States   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | 南非    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | 澳大利亚       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | 海外        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > 你将在“帐户” > “设置” > “帐户代码”下找到 Mimecast Personal Portal 中的 `accountcode` 值。 向标识符追加 `accountcode`。

    b. 在“回复 URL”文本框中键入 URL：

    | 区域  |  值 |
    | --------------- | --------------- |
    | 欧洲          | `https://eu-api.mimecast.com/login/saml`|
    | United States   | `https://us-api.mimecast.com/login/saml`|
    | 南非    | `https://za-api.mimecast.com/login/saml`|
    | 澳大利亚       | `https://au-api.mimecast.com/login/saml`|
    | 海外        | `https://jer-api.mimecast.com/login/saml`|

1. 如果要在“SP”发起的模式下配置应用程序：

    在“登录 URL”文本框中，键入 URL：

    | 区域  |  值 |
    | --------------- | --------------- |
    | 欧洲          | `https://eu-api.mimecast.com/login/saml`|
    | United States   | `https://us-api.mimecast.com/login/saml`|
    | 南非    | `https://za-api.mimecast.com/login/saml`|
    | 澳大利亚       | `https://au-api.mimecast.com/login/saml`|
    | 海外        | `https://jer-api.mimecast.com/login/saml`|

1. 单击“ **保存**”。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，单击“复制”按钮，以复制“应用联合元数据 URL”，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。 
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，将通过授予 B.Simon 访问 Mimecast Personal Portal 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Mimecast Personal Portal”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。  

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。  
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 
1. 在“添加分配”对话框中，单击“分配”按钮。 

## <a name="configure-mimecast-personal-portal-sso"></a>配置 Mimecast Personal Portal SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Mimecast Administration Console。

1. 导航到“管理” > “服务” > “应用程序”。

    ![Mimecast Personal Portal 配置](./media/mimecast-personal-portal-tutorial/services.png)

1. 单击“身份验证配置文件”选项卡。
    
    ![Mimecast Personal Portal 配置](./media/mimecast-personal-portal-tutorial/authentication-profiles.png)

1. 单击“新建身份验证配置文件”选项卡。

    ![Mimecast Personal Portal 配置](./media/mimecast-personal-portal-tutorial/new-authenticatio-profile.png)

1. 在“说明”文本框中提供有效说明，并选中“为 Mimecast Personal Portal 强制执行 SAML 身份验证”复选框。

    ![Mimecast Personal Portal 配置](./media/mimecast-personal-portal-tutorial/selecting-personal-portal.png)

1. 在“Mimecast Personal Portal 的 SAML 配置”页面上，执行以下步骤：

    ![Mimecast Personal Portal 配置](./media/mimecast-personal-portal-tutorial/sso-settings.png)

    a. 对于“提供程序”，从下拉菜单中选择“Azure Active Directory” 。

    b. 在“元数据 URL”文本框中，粘贴从 Azure 门户复制的“应用联合元数据 URL”值。 

    c. 单击“导入”。 导入元数据 URL 之后，字段将自动填充，无需对这些字段执行任何操作。

    d. 确保取消选中“使用受密码保护上下文”和“使用集成身份验证上下文”复选框。

    e. 单击“ **保存**”。

### <a name="create-mimecast-personal-portal-test-user"></a>创建 Mimecast Personal Portal 测试用户

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Mimecast Administration Console。

1. 导航到“管理” > “目录” > “内部目录”。

    ![Mimecast Personal Portal 配置](./media/mimecast-personal-portal-tutorial/internal-directories.png)

1. 如果下面提及你的域，请选择该域，否则请通过单击“新域”来创建新域。

    ![Mimecast Personal Portal 配置](./media/mimecast-personal-portal-tutorial/domain-name.png)

1. 单击“新地址”选项卡。

    ![Mimecast Personal Portal 配置](./media/mimecast-personal-portal-tutorial/new-address.png)

1. 在以下页面提供所需用户信息：

    ![Mimecast Personal Portal 配置](./media/mimecast-personal-portal-tutorial/user-information.png)

    a. 在“电子邮件地址”文本框中，输入用户的电子邮件地址，例如 `B.Simon@yourdomainname.com`。

    b. 在“全局名称”文本框中，输入用户的全名。 

    c. 在“密码”和“确认密码”文本框中，输入用户的密码。

    d. 选中“登录时强制更改”复选框。

    e. 单击“ **保存**”。

    f. 若要将角色分配给用户，请单击“角色编辑”，并根据组织要求向用户分配所需角色。

    ![Mimecast Personal Portal 配置](./media/mimecast-personal-portal-tutorial/assign-role.png)


## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Mimecast Personal Portal 磁贴时，应当会自动登录到已为其设置了 SSO 的 Mimecast Personal Portal。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [ Azure AD 中试用 Mimecast Personal Portal](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制保护 Mimecast Personal Portal](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
