---
title: 适用于 Java 的 Microsoft 身份验证库中的 AD FS 支持
titleSuffix: Microsoft identity platform
description: 了解适用于 Java 的 Microsoft 身份验证库（MSAL4j）中的 Active Directory 联合身份验证服务（AD FS）支持。
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c286ed7467560c90f9cf9594e75af06d6cdcc5d1
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482096"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>MSAL for Java 中的 Active Directory 联合身份验证服务支持

Windows Server 中的 Active Directory 联合身份验证服务（AD FS）使你能够将基于 OpenID Connect 和 OAuth 2.0 的身份验证和授权添加到适用于 Java 的 Microsoft 身份验证库（MSAL for Java）应用。 集成后，你的应用可以通过 Azure AD 在 AD FS 中对用户进行身份验证。 有关方案的详细信息，请参阅面向[开发人员的 AD FS 方案](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers)。

使用 MSAL for Java 的应用程序将与 Azure Active Directory （Azure AD）通信，后者随后将每家到 AD FS 中。

适用于 Java 的 MSAL 连接到 Azure AD，这些用户登录 Azure AD （托管用户）或其他标识提供者（例如，AD FS 联合用户）管理的用户。 MSAL for Java 不知道用户是联合的。 它只是与 Azure AD 进行通信。

在本案例中使用的[机构](msal-client-application-configuration.md#authority)是普通的机构（机构主机名 + 租户、通用机构或组织）。

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>以交互方式为联合用户获取令牌

当你通过 `AuthorizationCodeParameters` 或 `DeviceCodeParameters`调用 `ConfidentialClientApplication.AcquireToken()` 或 `PublicClientApplication.AcquireToken()` 时，用户体验通常是：

1. 用户输入其帐户 ID。
2. Azure AD 简短地显示 "将你转到组织的页面"，并将用户重定向到标识提供者的登录页。 登录页通常已使用组织的徽标进行自定义。

此联合方案中受支持的 AD FS 版本如下：
- Active Directory 联合身份验证服务 FS v2
- Active Directory 联合身份验证服务 v3 （Windows Server 2012 R2）
- Active Directory 联合身份验证服务 v4 （AD FS 2016）

## <a name="acquire-a-token-via-username-and-password"></a>通过用户名和密码获取令牌

使用 `ConfidentialClientApplication.AcquireToken()` 或 `PublicClientApplication.AcquireToken()` `IntegratedWindowsAuthenticationParameters` 或 `UsernamePasswordParameters`获取令牌时，MSAL for Java 将根据用户名获取要联系的标识提供者。 MSAL for Java 从标识提供程序获取[SAML 1.1 令牌](reference-saml-tokens.md)令牌，然后将该令牌提供给返回 JSON Web 令牌（JWT） Azure AD。

## <a name="next-steps"></a>后续步骤

对于联合用例，请参阅[使用主领域发现策略为应用程序配置 Azure Active Directory 登录行为](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)