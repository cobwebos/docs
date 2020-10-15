---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Azure AD SAML Toolkit 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Azure AD SAML Toolkit 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 7886691559a63e6d54ea748582f641f33cecf995
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90979748"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Azure AD SAML Toolkit 集成

本教程介绍如何将 Azure AD SAML Toolkit 与 Azure Active Directory (Azure AD) 集成。 将 Azure AD SAML Toolkit 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Azure AD SAML Toolkit。
* 可让用户使用其 Azure AD 帐户自动登录到 Azure AD SAML Toolkit。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Azure AD SAML Toolkit 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Azure AD SAML Toolkit 支持 **SP** 发起的 SSO

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>从库中添加 Azure AD SAML Toolkit

若要配置 Azure AD SAML Toolkit 与 Azure AD 的集成，需要从库中将 Azure AD SAML Toolkit 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Azure AD SAML Toolkit**  。
1. 从结果面板中选择“Azure AD SAML Toolkit”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-azure-ad-saml-toolkit"></a>配置并测试 Azure AD SAML Toolkit 的 Azure AD SSO

使用名为 **B. Simon** 的测试用户配置并测试 Azure AD SAML Toolkit 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Azure AD SAML Toolkit 中的相关用户之间建立链接关系。

若要配置并测试 Azure AD SAML Toolkit 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Azure AD SAML Toolkit SSO](#configure-azure-ad-saml-toolkit-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Azure AD SAML Toolkit 测试用户](#create-azure-ad-saml-toolkit-test-user)** - 在 Azure AD SAML Toolkit 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Azure AD SAML Toolkit”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”页上，输入以下字段的值  ：

    a. 在“登录 URL”文本框中，键入 URL：`https://samltoolkit.azurewebsites.net/`

    b. 在“回复 URL”文本框中键入 URL：`https://samltoolkit.azurewebsites.net/SAML/Consume`

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(原始)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificateraw.png)

1. 在“设置 Azure AD SAML Toolkit”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

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

在本部分，我们通过授予 B.Simon 访问 Azure AD SAML Toolkit 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Azure AD SAML Toolkit”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-azure-ad-saml-toolkit-sso"></a>配置 Azure AD SAML Toolkit SSO

1. 打开新的 Web 浏览器窗口，如果你尚未在 Azure AD SAML Toolkit 网站中注册，请先单击“注册”  。 如果已注册，请使用注册的凭据登录到 Azure AD SAML Toolkit 公司站点。

    ![Azure AD SAML Toolkit 注册](./media/saml-toolkit-tutorial/register.png)

1. 单击“SAML 配置”  。

    ![Azure AD SAML Toolkit - SAML 配置](./media/saml-toolkit-tutorial/saml-configure.png)

1. 单击“创建”。 

    ![Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/createsso.png)

1. 在“SAML SSO 配置”  页上，执行以下步骤：

    ![Azure AD SAML Toolkit 创建 SSO 配置](./media/saml-toolkit-tutorial/fill-details.png)

    1. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    1. 在“Azure AD 标识符”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    1. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    1. 单击“选择文件”  并上传从 Azure 门户下载的“证书(原始)”  文件。

    1. 单击“创建”。 

    1. 在“SAML 工具包 SSO 配置”页上复制“登录 URL”、“标识符”和“ACS URL”值，然后将其粘贴到 Azure 门户的“基本 SAML 配置”部分  中的相应文本框中。

### <a name="create-azure-ad-saml-toolkit-test-user"></a>创建 Azure AD SAML Toolkit 测试用户

在本部分，我们将在 Azure AD SAML Toolkit 中创建名为 B.Simon 的用户。 请通过注册新用户在工具中创建一个测试用户，并提供所有用户详细信息。 

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

1. 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 SAML Toolkit 登录 URL，可以在其中启动登录流。 

2. 直接转到 SAML Toolkit 登录 URL，并在其中启动登录流。

3. 可以使用 Microsoft 访问面板。 单击访问面板中的 SAML Toolkit 磁贴时，应会自动登录到设置了 SSO 的 SAML Toolkit。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="next-steps"></a>后续步骤

配置 Azure AD SAML Toolkit 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制扩展自条件访问。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)
