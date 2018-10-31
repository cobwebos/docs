---
title: 教程：Azure Active Directory 与 GitHub 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 GitHub 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeedes
ms.openlocfilehash: dbd4634c575fd4f1886d3e7714ef9ddabbde0f8a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341151"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>教程：Azure Active Directory 与 GitHub 集成

本教程介绍如何将 GitHub 与 Azure Active Directory (Azure AD) 集成。

将 GitHub 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 GitHub。
- 可以让用户使用其 Azure AD 帐户自动登录到 GitHub（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 GitHub 的集成，需要以下项：

- Azure AD 订阅
- 启用了 GitHub 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 GitHub
2. 配置和测试 Azure AD 单一登录

## <a name="adding-github-from-the-gallery"></a>从库中添加 GitHub
要配置 GitHub 与 Azure AD 的集成，需要从库中将 GitHub 添加到托管 SaaS 应用列表。

**若要从库中添加 GitHub，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![图像](./media/github-tutorial/selectazuread.png)

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![图像](./media/github-tutorial/a_select_app.png)
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![图像](./media/github-tutorial/a_new_app.png)

4. 在搜索框中，键入“GitHub”，在结果面板中选择“GitHub”，然后单击“添加”按钮添加该应用程序。

     ![图像](./media/github-tutorial/tutorial_github_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 GitHub 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 GitHub 用户。 换句话说，需要在 Azure AD 用户与 GitHub 中的相关用户之间建立链接关系。

若要配置和测试 GitHub 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 GitHub 测试用户](#create-a-github-test-user)** - 在 GitHub 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，会在 Azure 门户中启用 Azure AD 单一登录并在 GitHub 应用程序中配置单一登录。

**若要配置 GitHub 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com/)的 **GitHub** 应用程序集成页上，选择“单一登录”。

    ![图像](./media/github-tutorial/b1_b2_select_sso.png)

2. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![图像](./media/github-tutorial/b1_b2_saml_sso.png)

3. 在“设置 SAML 单一登录”页上，单击“编辑”按钮，以打开“基本 SAML 配置”对话框。

    ![图像](./media/github-tutorial/b1-domains_and_urlsedit.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![图像](./media/github-tutorial/tutorial_github_url.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL：`https://github.com/orgs/<entity-id>/sso`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL： `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > 请注意，这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 此处我们建议在“标识符”中使用字符串的唯一值。 转到“GitHub 管理”部分检索这些值。

5. GitHub 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 单击“编辑”按钮以打开“用户属性”对话框。

    ![图像](./media/github-tutorial/i3-attribute.png)

6. 在“用户属性”对话框的“用户声明”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“编辑”按钮，以打开“管理用户声明”对话框。

    ![图像](./media/github-tutorial/i2-attribute.png)

    ![图像](./media/github-tutorial/i4-attribute.png)

    b. 在“源属性”列表中，选择属性值。

    c. 单击“ **保存**”。
 
7. 在“SAML 签名证书”部分中，单击“下载”以下载“证书(Base64)”并将其保存到计算机上。

    ![图像](./media/github-tutorial/tutorial_github_certficate.png)

8. 在“设置 GitHub”部分中，根据要求复制相应的 URL。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

    ![图像](./media/github-tutorial/d1_samlsonfigure.png) 

9. 在另一个 Web 浏览器窗口中，以管理员身份登录到 GitHub 组织站点。

10. 导航“设置”并单击“安全性”。

    ![设置](./media/github-tutorial/tutorial_github_config_github_03.png)

11. 选中“启用 SAML 身份验证”框，显示“单一登录”配置字段。 然后，使用单一登录 URL 值更新 Azure AD 配置中的单一登录 URL。

    ![设置](./media/github-tutorial/tutorial_github_config_github_13.png)

12. 配置以下字段：

    ![设置](./media/github-tutorial/tutorial_github_config_github_051.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    b. 在“颁发者”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值。

    c. 在记事本中打开从 Azure 门户下载的证书，将内容粘贴到“公共证书”文本框中。

    d. 单击“编辑”图标，将“签名方法”和“摘要方法”从 **RSA-SHA1** 和 **SHA1** 编辑为 **RSA-SHA256** 和 **SHA256**，如下所示。

    ![图像](./media/github-tutorial/tutorial_github_sha.png) 
    
13. 单击“测试 SAML 配置”，确认在 SSO 期间未发生验证失败错误。

    ![设置](./media/github-tutorial/tutorial_github_config_github_06.png)

14. 单击“保存”

> [!NOTE]
> GitHub 中的单一登录向 GitHub 中的特定组织进行身份验证，并不替换 GitHub 本身的身份验证。 因此，如果用户的 GitHub.com 会话已过期，可能会要求你在单一登录过程中使用 GitHub 的 ID/密码进行身份验证。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![图像](./media/github-tutorial/d_users_and_groups.png)

2. 选择屏幕顶部的“新建用户”。

    ![图像](./media/github-tutorial/d_adduser.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![图像](./media/github-tutorial/d_userproperties.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。
 
### <a name="create-a-github-test-user"></a>创建 GitHub 测试用户

本部分的目的是在 GitHub 中创建名为“Britta Simon”的用户。 GitHub 支持在默认情况下启用的自动用户预配。 有关如何配置自动用户预配的更多详细信息，请参见[此处](github-provisioning-tutorial.md)。

如果需要手动创建用户，请执行以下步骤：

1. 以管理员身份登录到 GitHub 公司站点。

2. 单击“人员”。

    ![人员](./media/github-tutorial/tutorial_github_config_github_08.png "人员")

3. 单击“邀请成员”。

    ![邀请用户](./media/github-tutorial/tutorial_github_config_github_09.png "邀请用户")

4. 在“邀请成员”对话框页上，执行以下步骤：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“电子邮件”文本框中，键入 Britta Simon 帐户的电子邮件地址。

    ![邀请人员](./media/github-tutorial/tutorial_github_config_github_10.png "邀请人员")

    b. 单击“发送邀请”。

    ![邀请人员](./media/github-tutorial/tutorial_github_config_github_11.png "邀请人员")

    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，并且将单击其中的链接以在激活帐户前确认帐户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分通过授予 Britta Simon 访问 GitHub 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![图像](./media/github-tutorial/d_all_applications.png)

2. 在应用程序列表中，选择“GitHub”。

    ![图像](./media/github-tutorial/tutorial_github_app.png)

3. 在左侧菜单中，选择“用户和组”。

    ![图像](./media/github-tutorial/d_leftpaneusers.png)

4. 选择“添加”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![图像](./media/github-tutorial/d_assign_user.png)

4. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

5. 在“添加分配”对话框中，选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 GitHub 磁贴时，应自动登录到 GitHub 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)


