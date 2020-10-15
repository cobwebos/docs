---
title: 教程：Azure Active Directory 与 Mind Tools Toolkit 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Mind Tools Toolkit 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.openlocfilehash: 10773a2a379291fa861dbb2adfdda2ba3f63117f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88550632"
---
# <a name="tutorial-azure-active-directory-integration-with-mind-tools-toolkit"></a>教程：Azure Active Directory 与 Mind Tools Toolkit 的集成

本教程介绍如何将 Mind Tools Toolkit 与 Azure Active Directory (Azure AD) 集成。

进行此集成后，你可以：

* 在 Azure AD 中控制谁有权访问 Mind Tools Toolkit。
* 允许用户使用其 Azure AD 帐户自动登录到 Mind Tools Toolkit（单一登录）。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Mind Tools Toolkit 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Mind Tools Toolkit 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Mind Tools Toolkit 支持 SP 发起的 SSO。
* Mind Tools Toolkit 支持实时用户预配。
* 配置 Mind Tools Toolkit 后，可以强制实施会话控制。 此控制可实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-mind-tools-toolkit-from-the-gallery"></a>从库中添加 Mind Tools Toolkit

若要配置 Mind Tools Toolkit 与 Azure AD 的集成，需要从库中将 Mind Tools Toolkit 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在最左侧的导航窗格中，选择“Azure Active Directory”服务。
1. 转到“企业应用程序”，并选择“所有应用程序”。 
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，输入“Mind Tools Toolkit”。
1. 从搜索结果中选择“Mind Tools Toolkit”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，你将使用名为“B.Simon”的测试用户配置和测试 Mind Tools Toolkit 的 Azure AD 单一登录。 若要使单一登录有效，必须在 Azure AD 用户与 Mind Tools Toolkit 相关用户之间建立关联。

若要配置并测试 Mind Tools Toolkit 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以使用 B.Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 B.Simon 能够使用 Azure AD 单一登录。
1. **[配置 Mind Tools Toolkit SSO](#configure-mind-tools-toolkit-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Mind Tools Toolkit 测试用户](#create-a-mind-tools-toolkit-test-user)** - 在 Mind Tools Toolkit 中创建 B.Simon 的对应用户。 此对应用户将链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

此部分将按照以下步骤配置 Mind Tools Toolkit 的 Azure AD 单一登录：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Mind Tools Toolkit”应用程序集成页上，选择“单一登录” 。

    ![“管理”部分，其中突出显示了“单一登录”](common/select-sso.png)

1. 在“选择单一登录方法”对话框中，选择“SAML/WS-Fed”以启用单一登录。 

    ![“选择单一登录方法”对话框，其中突出显示了“SAML”](common/select-saml-option.png)

1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的铅笔图标以编辑设置 。

    ![“设置 SAML 单一登录”页，突出显示了与“基本 SAML 配置”对应的铅笔图标](common/edit-urls.png)

1. 在“基本 SAML 配置”部分的“登录 URL”框中，输入采用 `https://app.goodpractice.net/#/<subscriptionUrl>/s/<locationId>` 模式的 URL。

    > [!NOTE]
    > “登录 URL”值不是实际值。 请使用实际登录 URL 更新该值。 请联系 [Mind Tools Toolkit 客户端支持团队](mailto:support@goodpractice.com)获取该值。

1. 在“设置 SAML 单一登录”页上，转到“SAML 签名证书”部分。  在“联合元数据 XML”的右侧，选择“下载”以下载 XML 文本，并将其保存在计算机上。 XML 内容取决于所选的选项。

    ![“SAML 签名证书”部分，在“联合元数据 XML”旁边突出显示了“下载”](common/metadataxml.png)

1. 在“设置 Mind Tools Toolkit”部分中，复制所需的以下任一 URL。

    * **登录 URL**

    * **Azure AD 标识符**

    * **注销 URL**

    ![“设置 Mind Tools Toolkit”部分，突出显示了配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，你将在 Azure 门户中创建名为“B.Simon”的测试用户：

1. 在 Azure 门户的最左侧，选择“Azure Active Directory” > “用户” > “所有用户”。  
1. 在屏幕顶部选择“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“姓名”字段中输入 **B.Simon**。  
   1. 在“用户名”字段中，输入“B.Simon@companydomain.extension”。  例如，B.Simon@contoso.com 。
   1. 选中“显示密码”复选框，并记下“密码”框中显示的值。 
   1. 选择“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，你将通过授予 B.Simon 访问 Mind Tools Toolkit 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。 
1. 在“应用程序”列表中选择“Mind Tools Toolkit”。
1. 在应用的概览页中转到“管理”部分，然后选择“用户和组” 。

   ![“管理”部分，其中突出显示了“用户和组”](common/users-groups-blade.png)

1. 选择“添加用户”。 在“添加分配”对话框中选择“用户和组”。 

   ![“用户和组”窗口，其中突出显示了“添加用户”](common/add-assign-user.png)

1. 在“用户和组”对话框的“用户”列表中，选择“B.Simon”。  然后，选择屏幕底部的“选择”按钮。
1. 如果希望在 SAML 断言中使用任何角色值，请在“选择角色”对话框中，从列表中为用户选择相应的角色。 然后，选择屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中选择“分配”。 

## <a name="configure-mind-tools-toolkit-sso"></a>配置 Mind Tools Toolkit SSO

若要在 Mind Tools Toolkit 端配置单一登录，请将下载的“联合元数据 XML”文本以及先前复制的 URL 发送给 [Mind Tools Toolkit 支持团队](mailto:support@goodpractice.com)。 他们会对此设置进行配置，以在两端正确设置 SAML SSO 连接。

### <a name="create-a-mind-tools-toolkit-test-user"></a>创建 Mind Tools Toolkit 测试用户

本部分将在 Mind Tools Toolkit 中创建名为“B.Simon”的用户。

Mind Tools Toolkit 支持默认启用的实时预配。 在本部分中无需采取任何措施。 如果 Mind Tools Toolkit 中尚不存在用户，当你尝试访问 Mind Tools Toolkit 时会创建一个新用户。

### <a name="test-sso"></a>测试 SSO

本部分将使用“我的应用”门户测试 Azure AD 单一登录配置。

在“我的应用”门户中选择“Mind Tools Toolkit”磁贴时，你将自动登录到设置了 SSO 的 Mind Tools Toolkit。 有关“我的应用”门户的详细信息，请参阅[“我的应用”门户简介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他资源

- [用于将 SaaS 应用程序与 Azure Active Directory 集成的教程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Mind Tools Toolkit](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用高级可见性和控制保护 Mind Tools Toolkit](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
