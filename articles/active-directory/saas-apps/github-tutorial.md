---
title: 教程：Azure Active Directory 与 GitHub 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 GitHub 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: af5021d3a8bed48003f38f01c3d61eac4bdd96b0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159219"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>教程：Azure Active Directory 与 GitHub 集成

本教程介绍如何将 GitHub 与 Azure Active Directory (Azure AD) 集成。
将 GitHub 与 Azure AD 集成可提供以下优势：

* 可在 Azure AD 中控制谁有权访问 GitHub。
* 可让用户使用其 Azure AD 帐户自动登录到 GitHub（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 GitHub 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 一家在 [GitHub 企业云](https://help.github.com/articles/github-s-products/#github-enterprise)（需要 [GitHub Enterprise 计费计划](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)）中创建的 GitHub 组织

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* GitHub 支持 **SP** 发起的 SSO

* GitHub 支持[**自动**用户预配](github-provisioning-tutorial.md)

## <a name="adding-github-from-the-gallery"></a>从库中添加 GitHub

要配置 GitHub 与 Azure AD 的集成，需要从库中将 GitHub 添加到托管 SaaS 应用列表。

**若要从库中添加 GitHub，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“GitHub”  ，在结果面板中选择“GitHub.com”  ，然后单击“添加”  按钮添加该应用程序。

     ![结果列表中的 GitHub](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”  的测试用户配置和测试 GitHub 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 GitHub 相关用户之间建立链接关系。

若要配置和测试 GitHub 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 GitHub 单一登录](#configure-github-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 GitHub 测试用户](#create-github-test-user)** - 在 GitHub 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 GitHub 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)的 **GitHub** 应用程序集成页上，选择“单一登录”  。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![GitHub 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://github.com/orgs/<entity-id>/sso` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://github.com/orgs/<entity-id>` 

    > [!NOTE]
    > 请注意，这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 此处我们建议在“标识符”中使用字符串的唯一值。 转到“GitHub 管理”部分检索这些值。

5. GitHub 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表，其中的 **nameidentifier** 通过 **user.userprincipalname** 进行映射。 GitHub 应用程序要求通过 **user.mail** 对 **nameidentifier** 进行映射，因此需单击“编辑”图标对属性映射进行编辑，然后更改属性映射。 

    ![image](common/edit-attribute.png)

6. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

7. 在“设置 GitHub”部分中，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-github-single-sign-on"></a>配置 GitHub 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 GitHub 组织站点。

2. 导航“设置”并单击“安全性”。  

    ![设置](./media/github-tutorial/tutorial_github_config_github_03.png)

3. 选中“启用 SAML 身份验证”框，显示“单一登录”配置字段。  然后，使用单一登录 URL 值更新 Azure AD 配置中的单一登录 URL。

    ![设置](./media/github-tutorial/tutorial_github_config_github_13.png)

4. 配置以下字段：

    ![设置](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    b. 在“颁发者”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    c. 在记事本中打开从 Azure 门户下载的证书，将内容粘贴到“公共证书”  文本框中。

    d. 单击“编辑”图标，将“签名方法”和“摘要方法”从 **RSA-SHA1** 和 **SHA1** 编辑为 **RSA-SHA256** 和 **SHA256**，如下所示    。

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. 单击“测试 SAML 配置”，确认在 SSO 期间未发生验证失败错误。 

    ![设置](./media/github-tutorial/tutorial_github_config_github_06.png)

6. 单击“保存” 

> [!NOTE]
> GitHub 中的单一登录向 GitHub 中的特定组织进行身份验证，并不替换 GitHub 本身的身份验证。 因此，如果用户的 github.com 会话已过期，可能会要求你在单一登录过程中使用 GitHub 的 ID/密码进行身份验证。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中，键入 brittasimon\@yourcompanydomain.extension    
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分通过授予 Britta Simon 访问 GitHub 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“GitHub”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“GitHub”  。

    ![应用程序列表中的 GitHub 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-github-test-user"></a>创建 GitHub 测试用户

本部分的目的是在 GitHub 中创建名为“Britta Simon”的用户。 GitHub 支持在默认情况下启用的自动用户预配。 有关如何配置自动用户预配的更多详细信息，请参见[此处](github-provisioning-tutorial.md)。

如果需要手动创建用户，请执行以下步骤： 

1. 以管理员身份登录到 GitHub 公司站点。

2. 单击“人员”。 

    ![People](./media/github-tutorial/tutorial_github_config_github_08.png "人员")

3. 单击“邀请成员”。 

    ![邀请用户](./media/github-tutorial/tutorial_github_config_github_09.png "邀请用户")

4. 在“邀请成员”对话框页上，执行以下步骤： 

    a. 在“电子邮件”文本框中，键入 Britta Simon 帐户的电子邮件地址。 

    ![邀请人员](./media/github-tutorial/tutorial_github_config_github_10.png "邀请人员")

    b. 单击“发送邀请”。 

    ![邀请人员](./media/github-tutorial/tutorial_github_config_github_11.png "邀请人员")

    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，并且将单击其中的链接以在激活帐户前确认帐户。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 GitHub 磁贴时，应当会自动登录到已为其设置了 SSO 的 GitHub。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
