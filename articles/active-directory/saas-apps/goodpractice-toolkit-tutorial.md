---
title: 教程：Azure Active Directory 与 Mind Tools Toolkit 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Mind Tools Toolkit 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 65b2979d-9e2f-4530-bc08-546975269ebc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b12bd8ba7998b924035a0946f9e32b88ce206e4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "79476495"
---
# <a name="tutorial-azure-active-directory-integration-with-mind-tools-toolkit"></a>教程：Azure Active Directory 与 Mind Tools Toolkit 的集成

本教程介绍如何将 Mind Tools Toolkit 与 Azure Active Directory (Azure AD) 集成。
将 Mind Tools Toolkit 与 Azure AD 集成可带来以下优势：

* 可在 Azure AD 中控制谁有权访问 Mind Tools Toolkit。
* 可让用户使用其 Azure AD 帐户自动登录到 Mind Tools Toolkit（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Mind Tools Toolkit 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Mind Tools Toolkit 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Mind Tools Toolkit 支持 **SP** 发起的 SSO
* Mind Tools Toolkit 支持**实时**用户预配
* 配置 Mind Tools Toolkit 后，可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-mind-tools-toolkit-from-the-gallery"></a>从库中添加 Mind Tools Toolkit

若要配置 Mind Tools Toolkit 与 Azure AD 的集成，需要从库中将 Mind Tools Toolkit 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Mind Tools Toolkit**。 
1. 在结果面板中选择“Mind Tools Toolkit”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，你将基于名为 **B.Simon** 的测试用户，配置并测试 Mind Tools Toolkit 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 Mind Tools Toolkit 中的相关用户之间建立链接关系。

若要配置并测试 Mind Tools Toolkit 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Mind Tools Toolkit SSO](#configure-mind-tools-toolkit-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Mind Tools Toolkit 测试用户](#create-mind-tools-toolkit-test-user)** - 在 Mind Tools Toolkit 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Mind Tools Toolkit 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Mind Tools Toolkit”应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://app.goodpractice.net/#/<subscriptionUrl>/s/<locationId>`。

    > [!Note]
    > 登录 URL 值不是实际值。 请使用实际登录 URL 更新此值。 请联系 [Mind Tools Toolkit 客户端支持团队](mailto:support@goodpractice.com)获取该值。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Mind Tools Toolkit”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Mind Tools Toolkit 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“Mind Tools Toolkit”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-mind-tools-toolkit-sso"></a>配置 Mind Tools Toolkit SSO

若要在 **Mind Tools Toolkit** 端配置单一登录，需要将下载的“联合元数据 XML”以及从 Azure 门户复制的相应 URL 发送给 [Mind Tools Toolkit 支持团队](mailto:support@goodpractice.com)。  他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-mind-tools-toolkit-test-user"></a>创建 Mind Tools Toolkit 测试用户

本部分将在 Mind Tools Toolkit 中创建名为 B.Simon 的用户。 Mind Tools Toolkit 支持默认已启用的**实时用户预配**。 此部分不存在任何操作项。 如果 Mind Tools Toolkit 中尚不存在用户，当你尝试访问 Mind Tools Toolkit 时会创建一个新用户。

### <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Mind Tools Toolkit”磁贴时，应会自动登录到设置了 SSO 的 Mind Tools Toolkit。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Mind Tools Toolkit](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用高级可见性和控制保护 Mind Tools Toolkit](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)