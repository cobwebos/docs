---
title: Azure AD FS 支持（MSAL Python）
titleSuffix: Microsoft identity platform
description: 了解适用于 Python 的 Microsoft 身份验证库中的 Active Directory 联合身份验证服务（AD FS）支持
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 01d4cb626aabc83117e864b75b49eec63a6c0af0
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699540"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>用于 Python 的 MSAL 中的 Active Directory 联合身份验证服务支持

Windows Server 中的 Active Directory 联合身份验证服务（AD FS）允许使用适用于 Python 的 Microsoft 身份验证库（MSAL）向应用添加 OpenID Connect 和 OAuth 2.0 身份验证和授权。 使用用于 Python 库的 MSAL，应用可以直接对用户进行身份验证，AD FS。 有关方案的详细信息，请参阅面向[开发人员的 AD FS 方案](/windows-server/identity/ad-fs/ad-fs-development)。

通常有两种方法可以对 AD FS 进行身份验证：

- MSAL Python 与 Azure Active Directory 进行通信，后者本身与其他标识提供者联合。 联合通过 AD FS 发生。 MSAL Python 连接到 Azure AD，这些用户以 Azure AD （托管用户）或其他标识提供者（例如 AD FS，联合用户）管理的用户身份登录。 MSAL Python 不知道用户是联合的。 它只是与 Azure AD 进行通信。 在这种情况下，您使用的[颁发机构](msal-client-application-configuration.md#authority)是通常的授权机构（颁发机构主机名称 + 租户、公用或组织）。
- MSAL Python 直接与 AD FS 机构讨论。 仅 AD FS 2019 及更高版本支持此版本。

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>连接到 Active Directory 与 AD FS 联合

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>以交互方式为联合用户获取令牌

无论是直接连接到 Active Directory 联合身份验证服务（AD FS）还是通过 Active Directory 进行连接，都适用以下情况。

调用 `acquire_token_by_authorization_code` 或 `acquire_token_by_device_flow`时，用户体验通常如下所示：

1. 用户输入其帐户 ID。
2. Azure AD 会短暂显示 "将你转到组织的页面" 消息，并将用户重定向到标识提供者的登录页。 登录页通常是用组织徽标进行自定义的。

此联合方案中受支持的 AD FS 版本如下：
- Active Directory 联合身份验证服务 FS v2
- Active Directory 联合身份验证服务 v3 （Windows Server 2012 R2）
- Active Directory 联合身份验证服务 v4 （AD FS 2016）

### <a name="acquire-a-token-via-username-and-password"></a>通过用户名和密码获取令牌

无论是直接连接到 Active Directory 联合身份验证服务（AD FS）还是通过 Active Directory 进行连接，都适用以下情况。

使用 `acquire_token_by_username_password`获取令牌时，MSAL Python 会根据用户名获取要联系的标识提供者。 MSAL Python 从标识提供程序获取[SAML 1.1 令牌](reference-saml-tokens.md)，然后将其提供给返回 JSON Web 令牌（JWT） Azure AD。

## <a name="connecting-directly-to-ad-fs"></a>直接连接到 AD FS

将目录连接到 AD FS 时，需要用来生成应用程序的机构如下所示 `https://somesite.contoso.com/adfs/`

MSAL Python 支持 ADFS 2019。

它不支持与 ADFS 2016 或 ADFS v2 的直接连接。 如果需要支持需要直接连接到 ADFS 2016 的方案，请使用最新版本的 ADAL Python。 将本地系统升级到 ADFS 2019 后，可以使用 MSAL Python。

## <a name="next-steps"></a>后续步骤

- 对于联合用例，请参阅[使用主领域发现策略为应用程序配置 Azure Active Directory 登录行为](../manage-apps/configure-authentication-for-federated-users-portal.md)