---
title: 适用于 .NET 的 Microsoft 身份验证库中的 AD FS 支持
titleSuffix: Microsoft identity platform
description: 了解适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 中的 Active Directory 联合身份验证服务 (AD FS) 支持。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 273a8b06b3bd3bf299b47a1cc52fd4660353cec3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473748"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>MSAL.NET 中的 Active Directory 联合身份验证服务支持
使用 Windows Server 中的 Active Directory 联合身份验证服务 (AD FS) 可将基于 OpenID Connect 和 OAuth 2.0 的身份验证与授权添加到开发中的应用程序。 然后，这些应用程序可以根据 AD FS 直接对用户进行身份验证。 有关详细信息，请阅读[面向开发人员的 AD FS 方案](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios)。

适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 支持根据 AD FS 使用两种身份验证方案：

- MSAL.NET 与 Azure Active Directory 通信，后者本身已与 AD FS 联合。
- MSAL.NET 直接与 ADFS 机构通信。 只有 AD FS 2019 及更高版本才支持此功能。 本文重点介绍的方案之一是 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 支持


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL 连接到已与 AD FS 联合的 Azure AD
MSAL.NET 支持连接到 Azure AD，后者可将托管用户（在 Azure AD 中管理的用户）或联合用户（由其他标识提供者（例如 AD FS）管理的用户）登录。 MSAL.NET 不知道用户已联合这一事实。 只要需要某种信息，它就会与 Azure AD 通信。

在本案例中使用的[机构](msal-client-application-configuration.md#authority)是普通的机构（机构主机名 + 租户、通用机构或组织）。

### <a name="acquiring-a-token-interactively"></a>以交互方式获取令牌
调用 `AcquireTokenInteractive` 方法时，用户体验通常是：

1. 用户输入其帐户 ID。
2. Azure AD 短暂显示消息“正在将你转到组织页面”。
3. 用户重定向到标识提供者的登录页。 登录页通常已使用组织的徽标进行自定义。

此联合方案支持的 AD FS 版本为 AD FS v2、AD FS v3 (Windows Server 2012 R2) 和 AD FS v4 (AD FS 2016)。

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>使用 AcquireTokenByIntegratedAuthentication 或 AcquireTokenByUsernamePassword 获取令牌
使用 `AcquireTokenByIntegratedAuthentication` 或 `AcquireTokenByUsernamePassword` 方法获取令牌时，MSAL.NET 会让标识提供者根据用户名进行联系。  MSAL.NET 在联系标识提供者后接收 [SAML 1.1 令牌](reference-saml-tokens.md)。  然后，MSAL.NET 将 SAML 令牌以用户断言的形式提供给 Azure AD（类似于[代理流](msal-authentication-flows.md#on-behalf-of)），以取回 JWT。

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL 直接连接到 AD FS
MSAL.NET 支持连接到 AD FS 2019，后者符合 Open ID Connect 规范并了解 PKCE 和范围。 此项支持要求将服务包 [KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) 应用到 Windows Server。 直接连接到 AD FS 时，用于生成应用程序的机构类似于 `https://mysite.contoso.com/adfs/`。

目前，我们尚未计划支持与以下版本建立直接连接：

- AD FS 16，因为它不支持 PKCE，仍在使用资源而不是范围
- AD FS v2，因为它不符合 OIDC 规范。

 如果需要支持直接连接 AD FS 2016 的方案，请使用最新版本的 [Azure Active Directory 身份验证库](active-directory-authentication-libraries.md#microsoft-supported-client-libraries)。 将本地系统升级到 AD FS 2019 后，即可使用 MSAL.NET。

## <a name="see-also"></a>另请参阅

对于联合用例，请参阅[使用主领域发现策略为应用程序配置 Azure Active Directory 登录行为](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)
