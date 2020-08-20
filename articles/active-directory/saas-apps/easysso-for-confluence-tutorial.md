---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 EasySSO for Confluence 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 EasySSO for Confluence 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2020
ms.author: jeedes
ms.openlocfilehash: e0e29bdc67e70a76b26126a82363bbfaa0b8961b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555512"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-confluence"></a>教程：Azure Active Directory 单一登录 (SSO) 与 EasySSO for Confluence 的集成

本教程介绍如何将 EasySSO for Confluence 与 Azure Active Directory (Azure AD) 集成。 将 EasySSO for Confluence 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Confluence。
* 让用户使用其 Azure AD 帐户自动登录到 Confluence。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 EasySSO for Confluence 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* EasySSO for Confluence 支持 SP 和 IDP 发起的 SSO
* EasySSO for Confluence 支持实时用户预配
* 配置 EasySSO for Confluence 后，就可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-easysso-for-confluence-from-the-gallery"></a>从库中添加 EasySSO for Confluence

若要配置 EasySSO for Confluence 与 Azure AD 的集成，需要从库中将 EasySSO for Confluence 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务。
1. 导航到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“EasySSO for Confluence”。
1. 从结果面板中选择“EasySSO for Confluence”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-confluence"></a>配置并测试 EasySSO for Confluence 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置并测试 EasySSO for Confluence 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 EasySSO for Confluence 相关用户之间建立关联。

若要配置并测试 EasySSO for Confluence 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 EasySSO for Confluence SSO](#configure-easysso-for-confluence-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 EasySSO for Confluence 测试用户](#create-easysso-for-confluence-test-user)** - 在 EasySSO for Confluence 中创建 B.Simon 的对应用户，并将其关联到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“EasySSO for Confluence”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值 ：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/easysso/saml`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/easysso/saml`

1. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<server-base-url>/login.jsp`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 如有疑问，请联系 [EasySSO 支持团队](mailto:support@techtime.co.nz)来获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. EasySSO for Confluence 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除上述属性以外，EasySSO for Confluence 应用程序还要求在 SAML 响应中传回其他几个属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。
    
    | 名称 | 源属性|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    如果 Azure AD 用户已配置 sAMAccountName，则必须将 urn:oid:0.9.2342.19200300.100.1.1 映射到 sAMAccountName 属性  。
    
1. 在“设置 SAML 单一登录”页面的“SAML 签名证书”部分，单击“证书(Base64)”或“联合元数据 XML”选项的下载链接，然后将其中一个链接或全部保存到计算机    。 稍后将需要使用该链接来配置 Confluence EasySSO。

    ![证书下载链接](./media/easysso-for-confluence-tutorial/certificate.png)
    
    如果计划使用证书手动执行 EasySSO for Confluence 配置，则还需要从下面的部分复制登录 URL 和 Azure AD 标识符，并将其保存在计算机上 。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。 
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 EasySSO for Confluence 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“EasySSO for Confluence”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。  

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。  
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 
1. 在“添加分配”对话框中，单击“分配”按钮。 

## <a name="configure-easysso-for-confluence-sso"></a>配置 EasySSO for Confluence SSO

1. 使用管理员权限登录到 Atlassian Confluence 实例，并导航到“管理应用”部分。 

    ![管理应用](./media/easysso-for-confluence-tutorial/confluence-admin-1.png)

2. 在左侧找到“EasySSO”并单击它。 然后，单击“配置”按钮。

    ![EasySSO](./media/easysso-for-confluence-tutorial/confluence-admin-2.png)

3. 选择“SAML”选项。 这会使你进入 SAML 配置部分。

    ![SAML](./media/easysso-for-confluence-tutorial/confluence-admin-3.png)

4. 选择顶部的“证书”选项卡，你将看到以下屏幕： 

    ![元数据 URL](./media/easysso-for-confluence-tutorial/confluence-admin-4.png)

5. 现在，找到在 Azure AD SSO 配置的之前步骤中保存的“证书(Base64)”或“元数据文件”  。 对于如何继续操作，可使用以下选项：

    a. 使用下载到计算机本地文件中的应用联合“元数据文件”。 选择“上传”单选按钮，然后按照特定于操作系统的上传文件对话框操作

    **OR**

    b. 打开应用联合“元数据文件”以查看文件内容（在任何纯文本编辑器中），并将它复制到剪贴板中。 选择“输入”选项，并将剪贴板内容粘贴到文本字段中。
 
    **OR**

    c. 完全手动配置。 打开应用联合“证书(Base64)”以查看文件内容（在任何纯文本编辑器中），并将它复制到剪贴板中。 将它粘贴到“IdP 令牌签名证书”文本字段中。 然后导航到“常规”选项卡，在“POST 绑定 URL”和“实体 ID”字段中分别填入之前保存的“登录 URL”和“Azure AD 标识符”的值    。
 
6. 单击页面底部的“保存”按钮。 你将看到元数据文件或证书文件的内容分析为配置字段。 EasySSO for Confluence 配置已完成。

7. 若要获得最佳测试体验，请导航到“外观和感受”选项卡，并选中“SAML 登录按钮”选项。 这将在 Confluence 登录屏幕上启用单独的按钮，专门用于测试端到端 Azure AD SAML 集成。 对于生产模式，也可以让此按钮保留启用状态，并配置其位置、颜色和转换。

    ![外观和感受](./media/easysso-for-confluence-tutorial/confluence-admin-5.png)

    > [!NOTE]
    > 如果你有任何问题，请联系 [EasySSO 支持团队](mailto:support@techtime.co.nz)。

### <a name="create-easysso-for-confluence-test-user"></a>创建 EasySSO for Confluence 测试用户

在本部分，我们会在 Confluence 中创建一个名为 Britta Simon 的用户。 EasySSO for Confluence 支持默认禁用的实时用户预配。 若要启用用户预配，必须在 EasySSO 插件配置的“常规”部分中显式选中“成功登录后创建用户”选项。 如果 Confluence 中不存在用户，则会在身份验证后创建一个新用户。

但是，如果不希望在用户首次登录时启用自动用户预配，则用户必须存在于 Confluence 实例使用的后端用户目录（如 LDAP 或 Atlassian Crowd）中。

![用户预配](./media/easysso-for-confluence-tutorial/confluence-admin-6.png)

## <a name="test-sso"></a>测试 SSO 

### <a name="idp-initiated-workflow"></a>IDP 发起的工作流

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 EasySSO for Confluence 磁贴时，应当会自动登录到为其设置了 SSO 的 Confluence 实例。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

### <a name="sp-initiated-workflow"></a>SP 发起的工作流

在本部分中，使用 Confluence 的“SAML 登录”按钮测试 Azure AD 单一登录配置。

![用户 SAML 登录](./media/easysso-for-confluence-tutorial/confluence-admin-7.png)

此方案假定你已在 Confluence EasySSO 配置页面的“外观和感受”选项卡中启用了“SAML 登录”按钮（请参阅上文） 。 在浏览器隐身模式下打开 Confluence 登录 URL，以避免对现有会话造成任何干扰。 单击“SAML 登录”按钮，系统会将你重定向到 Azure AD 用户身份验证流。 成功完成后，会通过 SAML 将你作为已完成身份验证的用户重定向回 Confluence 实例。

从 Azure AD 重定向回后，你可能会看到以下屏幕

![EasySSO 故障屏幕](./media/easysso-for-confluence-tutorial/confluence-admin-8.png)

此时，必须按照[此页面上的说明]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS)操作才能访问 atlassian-confluence.log 文件。 错误的详细信息将通过 EasySSO 错误页面上的参考 ID 提供。

如果你在理解日志消息时遇到任何问题，请联系 [EasySSO 支持团队](mailto:support@techtime.co.nz)。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 EasySSO for Confluence](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制保护 EasySSO for Confluence](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
