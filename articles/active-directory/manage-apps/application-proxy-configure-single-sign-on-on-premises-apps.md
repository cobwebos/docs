---
title: 使用 Azure AD 应用代理的本地应用的 SAML 单一登录
description: 了解如何为通过 SAML 身份验证保护的本地应用程序提供单一登录。 使用应用程序代理提供对本地应用的远程访问。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccf34b52e06e369fe4dd459ff9dfa2880596fb35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481341"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>具有应用程序代理的本地应用程序的 SAML 单登录

您可以将单一登录 （SSO） 提供给通过 SAML 身份验证保护的本地应用程序，并通过应用程序代理对这些应用程序提供远程访问。 使用 SAML 单一登录，Azure 活动目录 （Azure AD） 使用用户的 Azure AD 帐户对应用程序进行身份验证。 Azure AD 通过连接协议将登录信息传递给应用程序。 您还可以根据在 SAML 声明中定义的规则将用户映射到特定的应用程序角色。 通过启用除 SAML SSO 之外的应用程序代理，您的用户将具有对应用程序的外部访问权限和无缝的 SSO 体验。

应用程序必须能够使用**Azure 活动目录**颁发的 SAML 令牌。 此配置不适用于使用本地标识提供程序的应用程序。 对于这些方案，我们建议查看[资源以将应用程序迁移到 Azure AD](migration-resources.md)。

具有应用程序代理的 SAML SSO 也适用于 SAML 令牌加密功能。 有关详细信息，请参阅配置[Azure AD SAML 令牌加密](howto-saml-token-encryption.md)。

下面的协议图描述了服务提供商启动（SP 启动）流和标识提供程序启动（IdP 启动）流的单一登录顺序。 应用程序代理通过缓存 SAML 请求和响应来与 SAML SSO 配合使用。

  ![SAML SP 流](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![SAML SP 流](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>创建应用程序并设置 SAML SSO

1. 在 Azure 门户中，选择**Azure 活动目录>企业应用程序**，然后选择 **"新应用程序**"。

2. 输入新应用程序的显示名称，选择 **"集成未在库中找到的任何其他应用程序**"，然后选择"**创建**"。

3. 在应用的 **"概述"** 页上，选择 **"单一登录**"。

4. 选择**SAML**作为单一登录方法。

5. 首先设置 SAML SSO，以便在公司网络上工作。 在"**使用 SAML 设置单一登录"页中**，转到**基本 SAML 配置**标题并选择其**编辑**图标（铅笔）。 按照[输入基本 SAML 配置](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration)中的步骤为应用程序配置基于 SAML 的身份验证。

6. 至少向应用程序添加一个用户，并确保测试帐户有权访问该应用程序。 连接到公司网络时，请使用测试帐户查看应用程序是否单一登录。 

   > [!NOTE]
   > 设置应用程序代理后，您将回来更新 SAML**回复 URL**。

## <a name="publish-the-on-premises-application-with-application-proxy"></a>使用应用程序代理发布本地应用程序

在为本地应用程序提供 SSO 之前，需要启用应用程序代理并安装连接器。 请参阅教程[在 Azure AD 中通过应用程序代理添加用于远程访问的本地应用程序](application-proxy-add-on-premises-application.md)，了解如何准备本地环境、安装和注册连接器以及测试连接器。 然后按照以下步骤使用应用程序代理发布新应用程序。 有关下面未提及的其他设置，请参阅本教程中的"[将本地应用添加到 Azure AD"](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)部分。

1. 当应用程序在 Azure 门户中仍然打开时，请选择**应用程序代理**。 为应用程序提供**内部 URL。** 如果您使用的是自定义域，则还需要上传应用程序的 TLS/SSL 证书。 
   > [!NOTE]
   > 最佳做法是尽可能使用自定义域来优化用户体验。 了解有关在[Azure AD 应用程序代理 中使用自定义域的更多详细信息](application-proxy-configure-custom-domain.md)。

2. 选择**Azure 活动目录**作为应用程序的**预身份验证**方法。

3. 复制应用程序**的外部 URL。** 您需要此 URL 才能完成 SAML 配置。

4. 使用测试帐户，请尝试使用**外部 URL**打开应用程序，以验证应用程序代理设置是否正确。 如果有问题，请参阅[排除应用程序代理问题和错误消息](application-proxy-troubleshoot.md)。

## <a name="update-the-saml-configuration"></a>更新 SAML 配置

1. 在 Azure 门户中应用程序仍然打开时，选择**单一登录**。 

2. 在"**使用 SAML 设置单一登录"页中**，转到**基本 SAML 配置**标题并选择其**编辑**图标（铅笔）。 请确保在应用程序代理中配置**的外部 URL**填充在**标识符**、回复**URL**和**注销 URL**字段中。 应用程序代理正常工作需要这些 URL。 

3. 编辑较早配置的**回复 URL，** 以便应用程序代理可以覆盖其域。 例如，如果您的外部**URL**和`https://contosotravel-f128.msappproxy.net`原始回复**URL**是`https://contosotravel.com/acs`，则需要将原始**回复 URL**更新到`https://contosotravel-f128.msappproxy.net/acs`。 

    ![输入基本的 SAML 配置数据](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. 选择更新的 **"回复 URL"** 旁边的复选框，将其标记为默认值。

   * 如果已列出所需的**回复 URL，** 请将其**回复 URL**标记为默认值，并删除以前配置的回复**URL**。

   * 对于 SP 启动的流，请确保后端应用程序指定用于接收身份验证令牌的正确**回复 URL**或断言使用者服务 URL。

    > [!NOTE]
    > 如果后端应用程序希望**回复 URL**是内部 URL，则需要使用[自定义域](application-proxy-configure-custom-domain.md)来匹配内部和外部 URLS，或者在用户设备上安装"我的应用"安全登录扩展。 此扩展将自动重定向到相应的应用程序代理服务。 要安装扩展，请参阅[我的应用程序安全登录扩展](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)。
    
## <a name="test-your-app"></a>测试应用

完成所有这些步骤后，应用应会启动并运行。 要测试应用程序：

1. 打开浏览器并导航到发布应用时创建的 **"外部 URL"。** 
1. 使用分配给应用的测试帐户登录。 您应该能够加载应用程序并将 SSO 引入应用程序。

## <a name="next-steps"></a>后续步骤

- [Azure AD 应用程序代理如何提供单一登录？](application-proxy-single-sign-on.md)
- [排查应用程序代理问题](application-proxy-troubleshoot.md)
