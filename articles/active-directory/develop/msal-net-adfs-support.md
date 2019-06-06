---
title: AD FS 支持在 Microsoft 身份验证库.NET |Azure
description: 了解有关 Microsoft 身份验证库中的 Active Directory 联合身份验证服务 (AD FS) 支持的.NET (MSAL.NET)。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/03/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5963c5e83b5af3848edd934328caa1f095bd184
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676723"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Active Directory 联合身份验证服务支持中 MSAL.NET
在 Windows Server active Directory 联合身份验证服务 (AD FS)，可添加 OpenID Connect 和 OAuth 2.0 基于身份验证和授权对应用程序开发，并且具有这些应用程序进行直接针对 AD FS 对用户进行身份验证。 有关详细信息，请阅读[面向开发人员的 AD FS 方案](/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers)。

Microsoft 身份验证库.NET (MSAL.NET) 中进行身份验证 AD FS 支持两种方案：

- 将与 Azure Active Directory，其本身进行 MSAL.NET 会话是*联合*与 AD FS。
- MSAL.NET 访谈*直接*到 AD FS 颁发机构，其中的 AD FS 的版本号是符合 OpenID Connect （从 AD FS 2019 年）。 直接连接到 AD FS 允许使用运行的应用进行身份验证的 MSAL.NET [Azure Stack](https://azure.microsoft.com/overview/azure-stack/)。

## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL 将连接到 Azure AD，这与 AD FS 联合
MSAL.NET 支持连接到 Azure AD 中管理用户 （在 Azure AD 中管理用户） 进行签名或联合用户 （用户管理的其他标识提供程序，例如 AD FS）。 MSAL.NET 不知道用户联合的这一事实。 就而言，其中介绍了为期到 Azure AD。

[机构](msal-client-application-configuration.md#authority)你使用这种情况下是常用的颁发机构 （颁发机构的主机名称 + 租户、 common、 或组织）。

### <a name="acquiring-a-token-interactively"></a>以交互方式获取令牌
当您调用`AcquireTokenInteractive`方法，用户体验通常是：

1. 用户输入其帐户 id。
2. Azure AD 会短暂地显示"转到你组织的页面"的消息。
3. 用户定向到标识提供者的登录页。 通常与组织的徽标自定义登录页。

支持的 AD FS 版本，在此联合方案中为 AD FS v2，AD FS v3 (Windows Server 2012 R2) 和 AD FS v4 (AD FS 2016)。

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>使用 AcquireTokenByIntegratedAuthentication 或 AcquireTokenByUsernamePassword 获取令牌
获取令牌的使用时`AcquireTokenByIntegratedAuthentication`或`AcquireTokenByUsernamePassword`方法，MSAL.NET 获取要联系的标识提供程序基于用户名。  接收 MSAL.NET [SAML 1.1 令牌](reference-saml-tokens.md)后联系标识提供程序。  MSAL.NET 然后提供给 Azure AD 作为用户断言的 SAML 令牌 (类似于[上的代理流](msal-authentication-flows.md#on-behalf-of)) 若要返回 JWT。

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL 会直接连接到 AD FS
MSAL.NET 支持连接到 AD FS 2019 是 Open ID Connect 符合。 直接连接到 AD FS，你将想要用于生成你的应用程序的颁发机构将类似于`https://mysite.contoso.com/adfs/`。

目前，没有计划以支持直接连接到 AD FS 2016 或 AD FS v2 （这是符合 OpenID Connect）。 如果你需要支持需要直接连接到 AD FS 2016 方案，请使用最新版本[Azure Active Directory 身份验证库](active-directory-authentication-libraries.md#microsoft-supported-client-libraries)。 当具有到 AD FS 2019 升级你的本地系统时，您将能够使用 MSAL.NET。
