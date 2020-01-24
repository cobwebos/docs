---
title: AD FS 支持（Java 的 MSAL）
titleSuffix: Microsoft identity platform
description: 了解适用于 Java 的 Microsoft 身份验证库（MSAL4j）中的 Active Directory 联合身份验证服务（AD FS）支持。
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 3d834a8d1524595304c22fed9897094622dfd93f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696208"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>MSAL for Java 中的 Active Directory 联合身份验证服务支持

Windows Server 中的 Active Directory 联合身份验证服务（AD FS）使你能够将基于 OpenID Connect 和 OAuth 2.0 的身份验证和授权添加到适用于 Java 的 Microsoft 身份验证库（MSAL for Java）应用。 集成后，你的应用可以通过 Azure AD 在 AD FS 中对用户进行身份验证。 有关方案的详细信息，请参阅面向[开发人员的 AD FS 方案](/windows-server/identity/ad-fs/ad-fs-development)。

使用 MSAL for Java 的应用程序将与 Azure Active Directory （Azure AD）通信，后者随后将每家到 AD FS 中。

适用于 Java 的 MSAL 连接到 Azure AD，这些用户登录 Azure AD （托管用户）或其他标识提供者（例如，AD FS 联合用户）管理的用户。 MSAL for Java 不知道用户是联合的。 它只是与 Azure AD 进行通信。

在这种情况下，您使用的[颁发机构](msal-client-application-configuration.md#authority)是通常的授权机构（颁发机构主机名称 + 租户、公用或组织）。

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>以交互方式为联合用户获取令牌

当你通过 `AuthorizationCodeParameters` 或 `DeviceCodeParameters`调用 `ConfidentialClientApplication.AcquireToken()` 或 `PublicClientApplication.AcquireToken()` 时，用户体验通常是：

1. 用户输入其帐户 ID。
2. Azure AD 简短地显示 "将你转到组织的页面"，并将用户重定向到标识提供者的登录页。 登录页通常是用组织徽标进行自定义的。

此联合方案中受支持的 AD FS 版本如下：
- Active Directory 联合身份验证服务 FS v2
- Active Directory 联合身份验证服务 v3 （Windows Server 2012 R2）
- Active Directory 联合身份验证服务 v4 （AD FS 2016）

## <a name="acquire-a-token-via-username-and-password"></a>通过用户名和密码获取令牌

使用 `ConfidentialClientApplication.AcquireToken()` 或 `PublicClientApplication.AcquireToken()` `IntegratedWindowsAuthenticationParameters` 或 `UsernamePasswordParameters`获取令牌时，MSAL for Java 将根据用户名获取要联系的标识提供者。 MSAL for Java 从标识提供程序获取[SAML 1.1 令牌](reference-saml-tokens.md)令牌，然后将该令牌提供给返回 JSON Web 令牌（JWT） Azure AD。

## <a name="next-steps"></a>后续步骤

对于联合用例，请参阅[使用主领域发现策略为应用程序配置 Azure Active Directory 登录行为](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)