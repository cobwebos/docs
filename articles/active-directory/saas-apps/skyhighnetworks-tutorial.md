---
title: 教程：Azure Active Directory 与 MVISION Cloud Azure AD SSO 配置集成 |Microsoft Docs
description: 了解如何在 Azure Active Directory 和 MVISION Cloud Azure AD SSO 配置之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: d529123b52e0e90702839f6822a586cf53dffa52
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546706"
---
# <a name="tutorial-integrate-mvision-cloud-azure-ad-sso-configuration-with-azure-active-directory"></a>教程：将 MVISION Cloud Azure AD SSO 配置与 Azure Active Directory 集成

本教程介绍如何将 MVISION Cloud Azure AD SSO 配置与 Azure Active Directory (Azure AD) 集成。 将 MVISION Cloud Azure AD SSO 配置与 Azure AD 集成后，可以：

* 控制在 Azure AD 中谁有权访问 MVISION Cloud Azure AD SSO 配置。
* 让用户使用其 Azure AD 帐户自动登录到 MVISION Cloud Azure AD SSO 配置。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以从[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月免费试用版。
* 启用了 MVISION Cloud Azure AD SSO 配置单一登录 (SSO) 的订阅。


## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* MVISION Cloud Azure AD SSO 配置支持 SP 和 IDP 启动的 SSO
* 配置 Dropbox 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-mvision-cloud-azure-ad-sso-configuration-from-the-gallery"></a>从库中添加 MVISION Cloud Azure AD SSO 配置

若要配置 MVISION Cloud Azure AD SSO 配置到 Azure AD 集成，需要将 MVISION Cloud Azure AD SSO 配置从库添加到托管 SaaS 应用列表中。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务。
1. 导航到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“MVISION Cloud Azure AD SSO 配置” 。
1. 从结果面板中选择“MVISION Cloud Azure AD SSO 配置”，然后添加应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 Britta Simon 的测试用户配置和测试 MVISION Cloud Azure AD SSO 配置的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 MVISION Cloud Azure AD SSO 配置中的相关用户之间建立链接关系。

若要配置并测试 MVISION Cloud Azure AD SSO 配置的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
    4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[配置MVISION Cloud Azure AD SSO 配置 SSO](#configure-mvision-cloud-azure-ad-sso-configuration-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 MVISION Cloud Azure AD SSO 配置测试用户](#create-mvision-cloud-azure-ad-sso-configuration-test-user)** - 在 MVISION Cloud Azure AD SSO 配置中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中，在 **Datadog** 应用程序集成页上，找到“管理”部分并选择“单一登录” 。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)


4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中执行以下步骤：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<ENV>.myshn.net/shndash/response/saml-postlogin`

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![MVISION Cloud Azure AD SSO 配置域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [MVISION Cloud Azure AD SSO 配置客户端支持团队](mailto:support@skyhighnetworks.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

6. 在“使用 SAML 设置单一登录”页上，在“SAML 签名证书”部分中，单击“下载”以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

7. 在“设置 MVISION Cloud Azure AD SSO 配置”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)


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

在本部分中，通过授予 Britta Simon 访问 MVISION Cloud Azure AD SSO 配置的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“MVISION Cloud Azure AD SSO 配置”  。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“MVISION Cloud Azure AD SSO 配置”。

    ![应用程序列表中的 MVISION Cloud Azure AD SSO 配置链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。 


## <a name="configure-mvision-cloud-azure-ad-sso-configuration-sso"></a>配置 MVISION Cloud Azure AD SSO 配置 SSO

若要在 MVISION Cloud Azure AD SSO 配置端配置单一登录，需要将下载的“证书(Base64)”以及从 Azure 门户复制的相应 URL 发送给 [MVISION Cloud Azure AD SSO 配置支持团队](mailto:support@skyhighnetworks.com) 。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。


### <a name="create-mvision-cloud-azure-ad-sso-configuration-test-user"></a>创建 MVISION Cloud Azure AD SSO 配置测试用户

在本部分中，你将在 MVISION Cloud Azure AD SSO 配置中创建名为 B.Simon 的用户。 与  [MVISION Cloud Azure AD SSO 配置支持团队](mailto:support@skyhighnetworks.com)协作，在 MVISION Cloud Azure AD SSO 配置平台中添加用户。 使用单一登录前，必须先创建并激活用户。

### <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“MVISION Cloud Azure AD SSO 配置”磁贴时，应会自动登录到设置了 SSO 的 MVISION Cloud Azure AD SSO 配置。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [尝试使用 Azure AD 进行 MVISION Cloud Azure AD SSO 配置](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)