---
title: Azure AD Connect：无缝单一登录 | Microsoft 文档
description: 本主题将介绍 Azure Active Directory (Azure AD) 无缝单一登录，以及如何使用它来为企业网络中的企业桌面用户提供真正的单一登录。
services: active-directory
keywords: 什么是 Azure AD Connect, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 11a04d40a403231db728d6bf0caade5969bba84d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593653"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory 无缝单一登录

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>什么是 Azure Active Directory 无缝单一登录？

Azure Active Directory 无缝单一登录（Azure AD 无缝 SSO）可使连接到企业网络的企业设备上的用户自动登录。 启用此功能后，用户无需键入其密码即可登录到 Azure AD；通常情况下，甚至无需键入其用户名。 此功能可让用户轻松访问基于云的应用程序，而无需使用其他任何本地组件。

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

无缝 SSO 可与[密码哈希同步](active-directory-aadconnectsync-implement-password-hash-synchronization.md)或[传递身份验证](active-directory-aadconnect-pass-through-authentication.md)登录方法结合使用。

![无缝单一登录](./media/active-directory-aadconnect-sso/sso1.png)

>[!IMPORTANT]
>无缝 SSO _不_适用于 Active Directory 联合身份验证服务 (ADFS)。

## <a name="key-benefits"></a>主要优点

- 出色的用户体验
  - 用户将自动登录到本地和基于云的应用程序。
  - 用户无需重复输入其密码。
- 易于部署和管理
  - 不需要本地任何其他组件来完成此操作。
  - 与云身份验证的任何方法 - [密码哈希同步](active-directory-aadconnectsync-implement-password-hash-synchronization.md)或[直通身份验证](active-directory-aadconnect-pass-through-authentication.md)结合使用。
  - 可以分发给某些或所有使用组策略的用户。
  - 使用 Azure AD 注册非 Windows 10 设备，无需任何 AD FS 基础结构。 此功能需要使用 2.1 版或更高版本的 [Workplace Join 客户端](https://www.microsoft.com/download/details.aspx?id=53554)。

## <a name="feature-highlights"></a>功能特点

- 登录用户名可以是本地默认用户名 (`userPrincipalName`)，也可以是 Azure AD Connect 中配置的另一个属性 (`Alternate ID`)。 两种用例均可运行，因为无缝 SSO 使用 Kerberos 票证中的 `securityIdentifier` 声明，在 Azure AD 中查找相应的用户对象。
- 无缝 SSO 是个机会型功能。 如果由于任何原因失败，用户登录体验将回退到其常规行为 - 即用户将需要在登录页面上输入其密码。
- 如果应用程序 (例如 https://myapps.microsoft.com/contoso.com) 在其 Azure AD 登录请求中转发 `domain_hint` (OpenID Connect) 或 `whr` (SAML) 参数（标识租户），或 `login_hint` 参数（标识用户），用户将会自动登录，而无需输入用户名或密码。
- 如果应用程序 (例如 https://contoso.sharepoint.com) 向 Azure AD 的租用终结点（即 https://login.microsoftonline.com/contoso.com/<..> 或 https://login.microsoftonline.com/<tenant_ID>/<..>）而不是 Azure AD 的普通终结点（即 https://login.microsoftonline.com/common/<...>）发送登录请求，用户可获得无提示登录体验。
- 支持注销。 这可以让用户选择另一个 Azure AD 帐户进行登录，而不是自动使用无缝 SSO 自动登录。
- 支持使用非交互式流的 Office 365 客户端（16.0.8730.xxxx 和更高版本）。
- 可通过 Azure AD Connect 启用它。
- 这是一项免费功能，不需要拥有任何付费版本的 Azure AD 即可使用此功能。
- 在能够进行 Kerberos 身份验证的平台和浏览器上，支持[新式身份验证](https://aka.ms/modernauthga)的基于 Web 浏览器的客户端和 Office 客户端支持此功能：

| 操作系统\浏览器 |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|是|否|是|是\*|不适用
|Windows 8.1|是|不适用|是|是\*|不适用
|Windows 8|是|不适用|是|是\*|不适用
|Windows 7|是|不适用|是|是\*|不适用
|Mac OS X|不适用|不适用|是\*|是\*|是\*

\*需要[额外的配置](active-directory-aadconnect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>对于 Windows 10，建议使用 [Azure AD join](../active-directory-azureadjoin-overview.md)，以获得最佳的 Azure AD 单一登录体验。

## <a name="next-steps"></a>后续步骤

- [快速入门](active-directory-aadconnect-sso-quick-start.md) - 启动并运行 Azure AD 无缝 SSO。
- [深入技术探究](active-directory-aadconnect-sso-how-it-works.md) - 了解此功能如何运作。
- [**常见问题**](active-directory-aadconnect-sso-faq.md) - 常见问题解答。
- [故障排除](active-directory-aadconnect-troubleshoot-sso.md) - 了解如何解决使用此功能时遇到的常见问题。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用于填写新功能请求。
