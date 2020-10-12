---
title: Azure AD FS 支持 (MSAL Python)
titleSuffix: Microsoft identity platform
description: 了解适用于 Python 的 Microsoft 身份验证库中的 Active Directory 联合身份验证服务 (AD FS) 支持
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
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: ad874a4b117b7a7ccecea0e1800ca18c6fce8aaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87846201"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>适用于 Python 的 MSAL 中的 Active Directory 联合身份验证服务支持

借助 Windows Server 中的 Active Directory 联合身份验证服务 (AD FS)，可以使用适用于 Python 的 Microsoft 身份验证库 (MSAL) 将基于 OpenID Connect 和 OAuth 2.0 的身份验证与授权添加到应用中。 使用适用于 Python 的 MSAL 库，应用可以根据 AD FS 直接对用户进行身份验证。 有关方案的详细信息，请参阅[面向开发人员的 AD FS 方案](/windows-server/identity/ad-fs/ad-fs-development)。

通常，可以通过两种方式对 AD FS 进行身份验证：

- MSAL Python 与 Azure Active Directory 通信，后者本身已与其他标识提供者联合。 联合是通过 AD FS 发生的。 MSAL Python 会连接到 Azure AD，后者可将在 Azure AD 中管理的用户（托管用户）或由其他标识提供者（例如 AD FS）管理的用户（联合用户）登录。 MSAL Python 不知道用户是联合的。 它只与 Azure AD 通信。 在本案例中使用的[机构](msal-client-application-configuration.md#authority)是普通的机构（机构主机名 + 租户、通用机构或组织）。
- MSAL Python 直接与 AD FS 机构通信。 只有 AD FS 2019 和更高版本支持此功能。

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>连接到与 AD FS 联合的 Active Directory

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>以交互方式获取联合用户的令牌

无论是直接连接到 Active Directory 联合身份验证服务 (AD FS) 还是通过 Active Directory 进行连接，以下内容都适用。

调用 `acquire_token_by_authorization_code` 或 `acquire_token_by_device_flow` 时，用户体验通常是：

1. 用户输入其帐户 ID。
2. Azure AD 短暂显示“正在将你转到组织的页面”消息，然后用户将重定向到标识提供者的登录页。 登录页通常已使用组织的徽标进行自定义。

此联合方案支持的 AD FS 版本为：
- Active Directory 联合身份验证服务 FS v2
- Active Directory 联合身份验证服务 v3 (Windows Server 2012 R2)
- Active Directory 联合身份验证服务 v4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>通过用户名和密码获取令牌

无论是直接连接到 Active Directory 联合身份验证服务 (AD FS) 还是通过 Active Directory 进行连接，以下内容都适用。

使用 `acquire_token_by_username_password` 获取令牌时，MSAL Python 会让标识提供者根据用户名进行联系。 MSAL Python 从标识提供者获取 [SAML 1.1 令牌](reference-saml-tokens.md)，然后将其提供给 Azure AD，后者会返回 JSON Web 令牌 (JWT)。

## <a name="connecting-directly-to-ad-fs"></a>直接连接到 AD FS

将目录连接到 AD FS 时，用于生成应用程序的机构类似于 `https://somesite.contoso.com/adfs/`

MSAL Python 支持 ADFS 2019。

它不支持直接与 ADFS 2016 或 ADFS v2 建立连接。 如果你的方案需要直接连接到 ADFS 2016，请使用最新版本的 ADAL Python。 将本地系统升级到 ADFS 2019 后，即可使用 MSAL Python。

## <a name="next-steps"></a>后续步骤

- 有关联合案例，请参阅[使用主领域发现策略为应用程序配置 Azure Active Directory 登录行为](../manage-apps/configure-authentication-for-federated-users-portal.md)
