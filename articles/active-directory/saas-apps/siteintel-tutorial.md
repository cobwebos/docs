---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 SiteIntel 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SiteIntel 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: jeedes
ms.openlocfilehash: f681dd2931300ec00fd6388b4636015c87f38170
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525065"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-siteintel"></a>教程：Azure Active Directory 单一登录 (SSO) 与 SiteIntel 集成

本教程介绍如何将 SiteIntel 与 Azure Active Directory (Azure AD) 集成。 将 SiteIntel 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 SiteIntel。
* 让用户使用其 Azure AD 帐户自动登录到 SiteIntel。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 SiteIntel 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* SiteIntel 支持 SP 和 IdP 发起的 SSO。
* 配置 SiteIntel 后，就可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-siteintel-from-the-gallery"></a>从库中添加 SiteIntel

要配置 SiteIntel 与 Azure AD 的集成，需要从库中将 SiteIntel 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧窗格中，选择“Azure Active Directory”。
1. 转到“企业应用程序”，并选择“所有应用程序”。 
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”框中，输入“SiteIntel” 。
1. 在结果列表中选择“SiteIntel”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-siteintel"></a>配置和测试 SiteIntel 的 Azure AD 单一登录

使用名为 B. Simon 的测试用户配置和测试 SiteIntel 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 SiteIntel 中的相关用户之间建立关联。

若要配置和测试 SiteIntel 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。  

    a. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，使用 B.Simon 用户测试 Azure AD 单一登录。  

    b. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使用户 B.Simon 能够使用 Azure AD 单一登录。

1. [配置 SiteIntel SSO](#configure-siteintel-sso)，在应用程序端配置单一登录设置。

    * [创建 SiteIntel 测试用户](#create-a-siteintel-test-user)，以在 SiteIntel 中创建用户 B.Simon 的对应用户，并将其关联到该用户的 Azure AD 表示形式。

1. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

若要在 Azure 门户中启用 Azure AD SSO，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com/)的“SiteIntel”应用程序集成页上，转到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，选择“基本 SAML 配置”旁边的“编辑”（铅笔）图标  。

   ![“设置 SAML 单一登录”窗格的屏幕截图](common/edit-urls.png)

1. 若要在 IdP 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下操作：

    a. 在“标识符”框中，键入采用以下格式的 URL：`urn:amazon:cognito:sp:<REGION>_<USERPOOLID>`

    b. 在“回复 URL”框中，键入采用以下格式的 URL：`https://<CLIENT>.auth.siteintel.com/saml2/idpresponse`

    c. 在“中继状态”框中，键入采用以下格式的 URL：`https://<CLIENT>.siteintel.com`

1. 若要在 SP 发起的模式下配置应用程序，请选择“设置其他 URL”，然后执行以下操作：

   * 在“登录 URL”框中，键入采用以下格式的 URL：`https://<CLIENT>.siteintel.com`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的标识符、回复 URL、登录 URL 和中继状态更新这些值。 若要获取这些值，请联系 [SiteIntel 客户端支持团队](mailto:support@intalytics.com)。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“设置 SAML 单一登录”页面的“SAML 签名证书”部分，选择“复制”按钮，以复制“应用联合元数据 URL”框中的 URL   。

    ![“应用联合元数据 URL”复制按钮的屏幕截图](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，你将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory” > “用户” > “所有用户”  。
1. 选择窗格顶部的“新建用户”。
1. 在“用户”属性中，执行以下操作：

   a. 在“名称”框中，输入 **B.Simon**。  

   b. 在“用户名”框中，按以下格式输入用户名：`username@companydomain.extension`（例如 `B.Simon@contoso.com`）。

   c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。 

   d. 选择“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予用户 B.Simon 访问 SiteIntel 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。 
1. 在应用程序列表中，选择“SiteIntel” 。
1. 在应用概述页上的“管理”部分，选择“用户和组” 。

   ![“用户和组”链接的屏幕截图](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”窗格中选择“用户和组”  。

    ![“添加用户”按钮的屏幕截图](common/add-assign-user.png)

1. 在“用户和组”窗格中，选择“B.Simon”，然后选择屏幕底部的“选择”按钮  。
1. 如果希望在 SAML 断言中使用任何角色值，请在“选择角色”窗格的列表中为用户选择相应的角色，然后勾选“选择”按钮 。
1. 在“添加分配”窗格中选择“分配”按钮 。

## <a name="configure-siteintel-sso"></a>配置 SiteIntel SSO

若要在 SiteIntel 端配置单一登录，请将从“应用联合元数据 URL”框复制的 URL 发送到 [SiteIntel 支持团队](mailto:support@intalytics.com)。 他们会设置该值，以在两端正确建立 SAML SSO 连接。

### <a name="create-a-siteintel-test-user"></a>创建 SiteIntel 测试用户

在本部分，我们将在 SiteIntel 中创建名为 Britta Simon 的用户。 与  [SiteIntel 支持团队](mailto:support@intalytics.com)协作，将用户添加到 SiteIntel 平台中。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中选择“SiteIntel”磁贴时，应会自动登录到设置了 SSO 的 SiteIntel。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)
- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [通过 Azure AD 试用 SiteIntel](https://aad.portal.azure.com/)
- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [如何通过高级可见性和控制保护 SiteIntel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
