---
title: 教程：Azure Active Directory 与 JIRA SAML SSO by Microsoft 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 JIRA SAML SSO by Microsoft 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: jeedes
ms.openlocfilehash: 9c43e887d66b1a0bec096810bcd899d907499c78
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36230329"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft"></a>教程：Azure Active Directory 与 JIRA SAML SSO by Microsoft 集成

本教程介绍如何将 JIRA SAML SSO by Microsoft 与 Azure Active Directory (Azure AD) 集成。

将 JIRA SAML SSO by Microsoft 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 JIRA SAML SSO by Microsoft。
- 可以让用户通过其 Azure AD 帐户自动登录到 JIRA SAML SSO by Microsoft（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="description"></a>说明

在 Atlassian JIRA 服务器上使用 Microsoft Azure Active Directory 帐户启用单一登录。 这样，所有组织用户便可使用 Azure AD 凭据登录到 JIRA 应用程序。 此插件使用 SAML 2.0 进行联合身份验证。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 JIRA SAML SSO by Microsoft 的集成，需要以下项：

- Azure AD 订阅
- 应在 Windows 64 位版本上安装并配置 JIRA Core and Software 6.0 到 7.8，或 JIRA Service Desk 3.0 到 3.2
- JIRA 服务器已启用 HTTPS
- 请注意，下面部分列出了支持 JIRA 插件版本。
- JIRA 服务器应当可以访问 Internet，尤其是访问用于身份验证的 Azure AD 登录页，还应当可以接收来自 Azure AD 的令牌
- 在 JIRA 中 设置管理员凭据
- 在 JIRA 中禁用 WebSudo
- 在 JIRA 服务器应用程序中创建的测试用户

> [!NOTE]
> 不建议使用 JIRA 生产环境测试本教程中的步骤。 首先在应用程序的开发环境或过渡环境中测试集成，然后使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可在此处获取一个月的试用版：[试用产品/服务](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="supported-versions-of-jira"></a>支持的 JIRA 版本

*   JIRA 核心和软件：6.0 到 7.8
*   JIRA Service Desk 3.0 到 3.2
*   JIRA 还支持 5.2。 有关更多详细信息，请单击 [Microsoft Azure Active Directory single sign-on for JIRA 5.2](jira52microsoft-tutorial.md)

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。
本教程中概述的方案包括两个主要构建基块：

1. 从库添加 JIRA SAML SSO by Microsoft
2. 配置和测试 Azure AD 单一登录

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>从库添加 JIRA SAML SSO by Microsoft
若要配置 JIRA SAML SSO by Microsoft 与 Azure AD 的集成，需要从库中将 JIRA SAML SSO by Microsoft 添加到托管 SaaS 应用列表。

若要从库中添加 JIRA SAML SSO by Microsoft，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入“JIRA SAML SSO by Microsoft”，在结果面板中选择“JIRA SAML SSO by Microsoft”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 JIRA SAML SSO by Microsoft](./media/jiramicrosoft-tutorial/tutorial_singlesign-onforjira_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于一个名为“Britta Simon”的测试用户配置并测试 JIRA SAML SSO by Microsoft 的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道与 Azure AD 用户相对应的 JIRA SAML SSO by Microsoft 用户。 换句话说，需要在 Azure AD 用户与 JIRA SAML SSO by Microsoft 中的相关用户之间建立关联关系。

若要配置和测试 JIRA SAML SSO by Microsoft 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 JIRA SAML SSO by Microsoft 测试用户](#create-a-jira-saml-sso-by-microsoft-test-user)** - 在 JIRA SAML SSO by Microsoft 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 JIRA SAML SSO by Microsoft 应用程序中配置单一登录。

若要配置 JIRA SAML SSO by Microsoft 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的 JIRA SAML SSO by Microsoft 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。

    ![“单一登录”对话框](./media/jiramicrosoft-tutorial/tutorial_singlesign-onforjira_samlbase.png)

3. 在“JIRA SAML SSO by Microsoft 域和 URL”部分中，执行以下步骤：

    ![JIRA SAML SSO by Microsoft 域和 URL 单一登录信息](./media/jiramicrosoft-tutorial/tutorial_singlesign-onforjira_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<domain:port>/plugins/servlet/saml/auth`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<domain:port>/`

    c. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 端口可选，以防止其为命名 URL。 在配置 JIRA 插件的过程中，将接收这些值，这将在教程的后面部分进行说明。

4. 在“SAML 签名证书”部分上，单击”复制”按钮来复制**应用联合元数据 URL**，并将其粘贴到记事本。

    ![配置单一登录](./media/jiramicrosoft-tutorial/tutorial_metadataurl.png)

5. 单击“保存”按钮。

    ![配置单一登录](./media/jiramicrosoft-tutorial/tutorial_general_400.png)

6. 在另一个 Web 浏览器窗口中，以管理员身份登录到 JIRA 实例。

7. 将鼠标悬停在小齿轮上，并单击“外接程序”。

    ![配置单一登录](./media/jiramicrosoft-tutorial/addon1.png)

8. 从 [Microsoft 下载中心](https://www.microsoft.com/en-us/download/details.aspx?id=56506)下载插件。 使用“上传加载项”菜单手动上传由 Microsoft 提供的插件。 [Microsoft 服务协议](https://www.microsoft.com/en-us/servicesagreement/)涵盖了插件下载。

    ![配置单一登录](./media/jiramicrosoft-tutorial/addon12.png)

9. 插件安装后，它会显示在“管理加载项”部分的“用户已安装”加载项部分。 单击“配置”配置新的插件。

    ![配置单一登录](./media/jiramicrosoft-tutorial/addon13.png)

10. 在配置页上执行下列步骤：

    ![配置单一登录](./media/jiramicrosoft-tutorial/addon52.png)

    > [!TIP]
    > 请确保一个应用仅映射一个证书，以免在解析元数据时出错。 如果有多个证书，则管理员会在解析元数据时收到错误。

    a. 在“元数据 URL”文本框中，粘贴从 Azure 门户复制的**应用联合元数据 URL**值，然后单击“解析”按钮。 它将读取 IdP 元数据 URL，并填充所有字段信息。

    b. 复制“标识符”、“回 URL”和“登录 URL”值，并将其分别粘贴到 Azure 门户中，“JIRA SAML SSO by Microsoft 域和 URL”部分下的“标识符、回复 URL 和登录 URL”文本框内。

    c. 在“登录按钮名”中键入组织希望用户在登录屏幕上看到的按钮名称。

    d. 在“SAML 用户 ID 位置”中，选择“用户 ID 位于 Subject 语句的 NameIdentifier 元素之中”或“用户 ID 位于 Attribute 元素之中”。  此 ID 必须为 JIRA 用户 ID。如果用户 ID 不匹配，系统将禁止用户登录。

    > [!Note]
    > 默认 SAML 用户 ID 位置是名称标识符。 可将其更改为属性选项，并输入适当的属性名称。

    e. 如果选择“用户 ID 位于 Attribute 元素之中”选项，则请在“Attribute 名称”文本框内键入应该出现用户 ID 的属性名称。

    f. 如果正在使用 Azure AD 的联合域（如 ADFS 等），请单击“启用主领域发现”选项，并配置“域名”。

    g. 如果是基于 ADFS 的登录，请在“域名”中键入域名。

    h. 从 JIRA 注销时，如果要从 Azure AD 注销，请选择“启用单一注销”。

    i. 单击“保存”按钮保存设置。

    > [!NOTE]
    > 有关安装和故障排除的详细信息，请访问 [MS JIRA SSO 连接器管理员指南](../ms-confluence-jira-plugin-adminguide.md)，还可以参阅[常见问题解答](../ms-confluence-jira-plugin-faq.md)以获得帮助

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/jiramicrosoft-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/jiramicrosoft-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/jiramicrosoft-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/jiramicrosoft-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="create-a-jira-saml-sso-by-microsoft-test-user"></a>创建 JIRA SAML SSO by Microsoft 测试用户

为了使 Azure AD 用户能够登录 JIRA 本地服务器，必须将其预配到 JIRA SAML SSO by Microsoft。 对于 JIRA SAML SSO by Microsoft，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 JIRA 本地服务器。

2. 将鼠标悬停在小齿轮上，并单击“用户管理”。

    ![添加员工](./media/jiramicrosoft-tutorial/user1.png)

3. 重定向到“管理员访问权限”页后，输入密码，并单击“确认”按钮。

    ![添加员工](./media/jiramicrosoft-tutorial/user2.png)

4. 在“用户管理”选项卡部分，单击“创建用户”。

    ![添加员工](./media/jiramicrosoft-tutorial/user3.png) 

5. 在“新建用户”对话框页中，执行以下步骤：

    ![添加员工](./media/jiramicrosoft-tutorial/user4.png) 

    a. 在“电子邮件地址”文本框中，键入用户的电子邮件地址（例如 Brittasimon@contoso.com）。

    b. 在“全名”文本框中，键入用户（例如 Britta Simon）的全名。

    c. 在“用户名”文本框中，键入用户的电子邮件地址（例如 Brittasimon@contoso.com）。

    d. 在“密码”文本框中，键入用户的密码。

    e. 单击“创建用户”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 JIRA SAML SSO by Microsoft 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

要将 Britta Simon 分配到 JIRA SAML SSO by Microsoft，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“JIRA SAML SSO by Microsoft”。

    ![应用程序列表中的 JIRA SAML SSO by Microsoft 链接](./media/jiramicrosoft-tutorial/tutorial_singlesign-onforjira_app.png)

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 JIRA SAML SSO by Microsoft 磁贴时，应当会自动登录到 JIRA SAML SSO by Microsoft 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/msaadssojira-tutorial/tutorial_general_01.png
[2]: ./media/msaadssojira-tutorial/tutorial_general_02.png
[3]: ./media/msaadssojira-tutorial/tutorial_general_03.png
[4]: ./media/msaadssojira-tutorial/tutorial_general_04.png

[100]: ./media/msaadssojira-tutorial/tutorial_general_100.png

[200]: ./media/msaadssojira-tutorial\tutorial_general_200.png
[201]: ./media/msaadssojira-tutorial\tutorial_general_201.png
[202]: ./media/msaadssojira-tutorial\tutorial_general_202.png
[203]: ./media/msaadssojira-tutorial\tutorial_general_203.png