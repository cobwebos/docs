---
title: AD FS 支持（适用于 Java 的 MSAL）
titleSuffix: Microsoft identity platform
description: 了解适用于 Java 的 Microsoft 身份验证库 (MSAL4j) 中的 Active Directory 联合身份验证服务 (AD FS) 支持。
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
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 5d01d1143563637e21aaa06a3f997c1507e4d8f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88114772"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>MSAL for Java 中的 Active Directory 联合身份验证服务支持

使用 Windows Server 中的 Active Directory 联合身份验证服务 (AD FS) 可将基于 OpenID Connect 和 OAuth 2.0 的身份验证与授权添加到适用于 Java 的 Microsoft 身份验证库 (MSAL for Java) 应用。 集成后，应用即可对通过 Azure AD 进行联合的 AD FS 中的用户进行身份验证。 有关方案的详细信息，请参阅[面向开发人员的 AD FS 方案](/windows-server/identity/ad-fs/ad-fs-development)。

使用 MSAL for Java 的应用将先与 Azure Active Directory (Azure AD) 通信，然后与 AD FS 联合。

MSAL for Java 会连接到 Azure AD，后者可将在 Azure AD 中管理的用户（托管用户）或由其他标识提供者（例如 AD FS）管理的用户（联合用户）登录。 MSAL for Java 不知道用户是联合的。 它直接与 Azure AD 通信。

在本案例中使用的[机构](msal-client-application-configuration.md#authority)是普通的机构（机构主机名 + 租户、通用机构或组织）。

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>以交互方式获取联合用户的令牌

使用 `AuthorizationCodeParameters` 或 `DeviceCodeParameters` 调用 `ConfidentialClientApplication.AcquireToken()` 或 `PublicClientApplication.AcquireToken()` 时，用户体验通常是：

1. 用户输入其帐户 ID。
2. Azure AD 简要显示“将你转到组织的页面”，然后系统就会将用户重定向到标识提供者的登录页。 登录页通常已使用组织的徽标进行自定义。

此联合方案支持的 AD FS 版本为：
- Active Directory 联合身份验证服务 FS v2
- Active Directory 联合身份验证服务 v3 (Windows Server 2012 R2)
- Active Directory 联合身份验证服务 v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>通过用户名和密码获取令牌

通过将 `ConfidentialClientApplication.AcquireToken()` 或 `PublicClientApplication.AcquireToken()` 与 `IntegratedWindowsAuthenticationParameters` 或 `UsernamePasswordParameters` 配合使用来获取令牌时，MSAL for Java 会让标识提供者根据用户名进行联系。 MSAL for Java 从标识提供者处获取 [SAML 1.1 令牌](reference-saml-tokens.md)，然后将其提供给 Azure AD，后者会返回 JSON Web 令牌 (JWT)。

## <a name="next-steps"></a>后续步骤

有关联合案例，请参阅[使用主领域发现策略为应用程序配置 Azure Active Directory 登录行为](../manage-apps/configure-authentication-for-federated-users-portal.md)