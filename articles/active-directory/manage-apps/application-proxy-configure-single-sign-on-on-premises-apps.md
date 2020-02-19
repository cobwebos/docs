---
title: 具有 Azure AD 应用 Proxy 的本地应用的 SAML 单一登录
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
ms.openlocfilehash: 1046c11e064e69ed0ddb18c77bf5935ba60fb5aa
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461277"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>具有应用程序代理的本地应用程序的 SAML 单一登录

你可以向使用 SAML 身份验证保护的本地应用程序提供单一登录（SSO），并通过应用程序代理提供对这些应用程序的远程访问。 使用 SAML 单一登录，Azure Active Directory （Azure AD）使用用户的 Azure AD 帐户对应用程序进行身份验证。 Azure AD 通过连接协议将登录信息传递给应用程序。 还可以根据在 SAML 声明中定义的规则将用户映射到特定应用程序角色。 除了启用 SAML SSO 外，还可以通过启用应用程序代理来访问应用程序和无缝 SSO 体验。

应用程序必须能够使用**Azure Active Directory**颁发的 SAML 令牌。 此配置不适用于使用本地标识提供程序的应用程序。 对于这些方案，建议查看[将应用程序迁移到 Azure AD 的资源](migration-resources.md)。

具有应用程序代理的 SAML SSO 还适用于 SAML 令牌加密功能。 有关详细信息，请参阅[Configure AZURE AD SAML 令牌 encryption](howto-saml-token-encryption.md)。

下面的协议关系图描述了服务提供程序启动的（SP 发起的）流和标识提供程序启动的（IdP 启动）流的单一登录序列。 应用程序代理通过在本地应用程序中缓存 SAML 请求和响应，来与 SAML SSO 一起工作。

  ![SAML SP Flow](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![SAML SP Flow](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>创建应用程序并设置 SAML SSO

1. 在 Azure 门户中，选择 " **Azure Active Directory" > 企业应用程序**"，然后选择"**新建应用程序**"。

2. 输入新应用程序的 "显示名称"，选择 **"集成"** ，然后选择 "**创建**"。

3. 在应用的 "**概述**" 页上，选择 "**单一登录**"。

4. 选择 " **SAML** " 作为 "单一登录方法"。

5. 首先，将 SAML SSO 设置为在公司网络上工作。 在 "**设置 SAML 的单一登录**" 页中，选择 "**基本 SAML 配置**" 标题，并选择其**编辑**图标（铅笔）。 按照[输入基本 SAML 配置](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration)中的步骤为应用程序配置基于 SAML 的身份验证。

6. 至少将一个用户添加到应用程序，并确保测试帐户有权访问该应用程序。 连接到企业网络时，请使用测试帐户查看应用程序的单一登录。 

   > [!NOTE]
   > 设置应用程序代理后，会返回并更新 "SAML**回复 URL**"。

## <a name="publish-the-on-premises-application-with-application-proxy"></a>用应用程序代理发布本地应用程序

在为本地应用程序提供 SSO 之前，你需要启用应用程序代理并安装连接器。 请参阅教程[添加本地应用程序以在 Azure AD 中通过应用程序代理进行远程访问](application-proxy-add-on-premises-application.md)，了解如何准备本地环境、安装和注册连接器，以及如何测试连接器。 然后执行以下步骤，通过应用程序代理发布新应用程序。 对于下面未提及的其他设置，请参阅教程中的[将本地应用添加到 Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)部分。

1. 在 Azure 门户中，如果应用程序仍处于打开状态，请选择 "**应用程序代理**"。 提供应用程序的**内部 URL** 。 如果你使用的是自定义域，还需要为你的应用程序上传 SSL 证书。 
   > [!NOTE]
   > 最佳做法是尽可能使用自定义域来实现优化的用户体验。 详细了解如何使用[Azure AD 应用程序代理中的自定义域](application-proxy-configure-custom-domain.md)。

2. 选择 " **Azure Active Directory**作为应用程序的**预身份验证**方法。

3. 复制应用程序的**外部 URL** 。 需要此 URL 来完成 SAML 配置。

4. 使用测试帐户，尝试使用**外部 URL**打开应用程序以验证是否正确设置了应用程序代理。 如果出现问题，请参阅[排查应用程序代理问题和错误消息](application-proxy-troubleshoot.md)。

## <a name="update-the-saml-configuration"></a>更新 SAML 配置

1. 在 Azure 门户中，如果应用程序仍处于打开状态，请选择 "**单一登录**"。 

2. 在 "**设置 SAML 的单一登录**" 页中，选择 "**基本 SAML 配置**" 标题，并选择其**编辑**图标（铅笔）。 请确保在 "**标识符**"、"**回复 url**" 和 "**注销 url** " 字段中填充了在应用程序代理中配置的**外部 URL** 。 这些 Url 是应用程序代理正常工作所必需的。 

3. 编辑前面配置的**回复 URL** ，使其域可由应用程序代理访问。 例如，如果您的**外部 URL**是 `https://contosotravel-f128.msappproxy.net` 的，并且原始**回复 url**是 `https://contosotravel.com/acs`的，则需要将原始**回复 url**更新为 `https://contosotravel-f128.msappproxy.net/acs`。 

    ![输入基本 SAML 配置数据](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. 选中已更新的**回复 URL**旁边的复选框以将其标记为默认值。

   * 如果已列出必需的**回复 url** ，请将此**回复 url**标记为默认值，并删除以前配置的**回复 url**。

   * 对于 SP 启动的流，请确保后端应用程序指定了正确的**回复 URL**或断言使用者服务 URL 以便接收身份验证令牌。

    > [!NOTE]
    > 如果后端应用程序需要**回复 url**作为内部 url，则需要使用[自定义域](application-proxy-configure-custom-domain.md)来匹配内部和外部 url，或在用户的设备上安装 "我的应用" 安全登录扩展。 此扩展将自动重定向到相应的应用程序代理服务。 若要安装该扩展，请参阅[我的应用安全登录扩展](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)。
    
## <a name="test-your-app"></a>测试应用

完成所有这些步骤后，应用应会启动并运行。 测试应用：

1. 打开浏览器并导航到在发布应用程序时创建的**外部 URL** 。 
1. 使用分配给应用的测试帐户登录。 应该能够加载应用程序并将 SSO 引入应用程序。

## <a name="next-steps"></a>后续步骤

- [Azure AD 应用程序代理如何提供单一登录？](application-proxy-single-sign-on.md)
- [排查应用程序代理问题](application-proxy-troubleshoot.md)
