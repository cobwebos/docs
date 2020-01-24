---
title: MSAL.NET 中的 AD FS 支持 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）中的 Active Directory 联合身份验证服务（AD FS）支持。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 6e40a03e70fa04855037165462761c56f13acc58
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695613"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>MSAL.NET 中的 Active Directory 联合身份验证服务支持
Windows Server 中的 Active Directory 联合身份验证服务（AD FS）可用于向正在开发的应用程序添加 OpenID Connect 和基于 OAuth 2.0 的身份验证和授权。 然后，这些应用程序可以根据 AD FS 直接对用户进行身份验证。 有关详细信息，请阅读[开发人员 AD FS 方案](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios)。

适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）支持两种针对 AD FS 进行身份验证的方案：

- MSAL.NET 与 Azure Active Directory 进行通信，后者本身与 AD FS*联合*。
- MSAL.NET**直接**与 ADFS 机构讨论。 仅 AD FS 2019 及更高版本支持此版本。 这里重点介绍的一个方案是[Azure Stack](https://azure.microsoft.com/overview/azure-stack/)支持


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL 连接到与 AD FS 联合的 Azure AD
MSAL.NET 支持连接到 Azure AD，这会在托管用户（Azure AD 中管理的用户）或联合用户（由其他标识提供者管理的用户，如 AD FS）上进行登录。 MSAL.NET 不知道用户是联合的。 至于这一点，它会与 Azure AD 进行通信。

在这种情况下，您使用的[颁发机构](msal-client-application-configuration.md#authority)是通常的授权机构（颁发机构主机名称 + 租户、公用或组织）。

### <a name="acquiring-a-token-interactively"></a>以交互方式获取令牌
调用 `AcquireTokenInteractive` 方法时，用户体验通常是：

1. 用户输入其帐户 ID。
2. Azure AD 会短暂显示 "将你带到组织的页面" 的消息。
3. 将用户重定向到标识提供者的登录页。 登录页通常是用组织徽标进行自定义的。

此联合方案中支持的 AD FS 版本 AD FS v2、AD FS v3 （Windows Server 2012 R2）和 AD FS v4 （AD FS 2016）。

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>使用 AcquireTokenByIntegratedAuthentication 或 AcquireTokenByUsernamePassword 获取令牌
使用 `AcquireTokenByIntegratedAuthentication` 或 `AcquireTokenByUsernamePassword` 方法获取令牌时，MSAL.NET 会根据用户名获取要联系的标识提供者。  MSAL.NET 在与标识提供者联系后接收[SAML 1.1 令牌](reference-saml-tokens.md)。  然后，MSAL.NET 提供 SAML 令牌，作为用户断言 Azure AD （类似于代理[流](msal-authentication-flows.md#on-behalf-of)）来获取回退 JWT。

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL 直接连接到 AD FS
MSAL.NET 支持连接到 AD FS 2019，这是打开 ID Connect 相容并了解 PKCE 和作用域。 此支持要求将 Service Pack [KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481)应用于 Windows Server。 直接连接到 AD FS 时，要用于生成应用程序的权限与 `https://mysite.contoso.com/adfs/`类似。

目前没有计划支持直接连接到：

- AD FS 16，因为它不支持 PKCE，仍使用资源，而不是范围
- AD FS v2，不符合 OIDC。

 如果需要支持直接连接到 AD FS 2016 的方案，请使用最新版本的[Azure Active Directory 身份验证库](active-directory-authentication-libraries.md#microsoft-supported-client-libraries)。 将本地系统升级到 AD FS 2019 后，便可以使用 MSAL.NET。

## <a name="next-steps"></a>后续步骤

对于联合用例，请参阅[使用主领域发现策略为应用程序配置 Azure Active Directory 登录行为](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)
