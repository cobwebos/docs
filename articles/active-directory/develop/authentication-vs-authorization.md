---
title: 身份验证和授权 | Azure
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 标识平台 (v2.0) 中有关身份验证和授权的基础知识。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 095bc5594cf9b6eaf0df7929ea83c25a401793e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86252107"
---
# <a name="authentication-vs-authorization"></a>身份验证和授权

本文对身份验证和授权进行了定义，还简要介绍了如何使用 Microsoft 标识平台对 Web 应用、Web API 或调用受保护的 Web API 的应用中的用户进行身份验证和授权。 如果看到不熟悉的术语，请试着查看我们的[词汇表](developer-glossary.md)或 [Microsoft 标识平台视频](identity-videos.md)，其中涵盖了基本概念。

## <a name="authentication"></a>身份验证

身份验证就是证明你是你本人的过程。 身份验证有时缩写为 AuthN。 Microsoft 标识平台实现了的 [OpenID Connect](https://openid.net/connect/) 协议来处理身份验证。

## <a name="authorization"></a>授权

授权是指向经过身份验证的参与方授予执行某项操作的权限的操作。 它指定了你可访问的数据以及可使用该数据执行的操作。 授权有时缩写为 AuthZ。 Microsoft 标识平台实现了 [OAuth 2.0](https://oauth.net/2/) 协议来处理授权。

## <a name="authentication-and-authorization-using-microsoft-identity-platform"></a>使用 Microsoft 标识平台进行身份验证和授权

当你需要在多个应用中添加或删除用户时，应用不必创建各自维护自己的用户名和密码信息的应用（这会带来很重的管理负担），而是可以将此责任委托给一个集中式标识提供程序。

Azure Active Directory (Azure AD) 就是云中的一个集中标识提供程序。 通过将身份验证和授权委托给它可实现以下场景：要求用户位于特定位置的条件访问策略、使用[多重身份验证](../authentication/concept-mfa-howitworks.md)（有时称为双因素身份验证或 2FA），以及允许用户登录一次后可自动登录到共享同一集中目录的所有 Web 应用。 此功能被称为“单一登录 (SSO)”。

Microsoft 标识平台将标识提供为一项服务、支持行业标准协议（例如 OAuth 2.0 和 OpenID Connect），还提供用于不同平台的开源库来帮助你快速开始编码，从而简化了对应用程序开发人员的授权和身份验证。 借助它，开发人员可构建这样的应用程序，即进行所有 Microsoft 标识的登录，并获取令牌来调用 [Microsoft Graph](https://developer.microsoft.com/graph/)、其他 Microsoft API 或者开发人员生成的 API。

下面是 Microsoft 标识平台使用的各种协议的简单比较：

* **OAuth 与 OpenID Connect**：OAuth 用于授权，OpenID Connect (OIDC) 用于身份验证。 OpenID Connect 构建在 OAuth 2.0 的基础之上，因此两者的术语和流很相似。 你甚至可在一个请求中对用户进行身份验证（使用 OpenID Connect），并同时获得授权来访问用户拥有的受保护资源（使用 OAuth 2.0）。 有关详细信息，请参阅 [OAuth 2.0 和 OpenID Connect 协议](active-directory-v2-protocols.md)和 [OpenID Connect 协议](v2-protocols-oidc.md)。
* **OAuth 与 SAML**：OAuth 用于授权，SAML 用于身份验证。 要详细了解如何同时使用这两种协议对用户进行身份验证（使用 SAML）并获得授权来访问受保护的资源（使用 OAuth 2.0），请参阅 [Microsoft 标识平台和 OAuth 2.0 SAML 持有者断言流](v2-saml-bearer-assertion.md)。
* **OpenID Connect 与 SAML**：OpenID Connect 和 SAML 都用于对用户进行身份验证，也用于启用单一登录。 SAML 身份验证通常与标识提供程序（例如与 Azure AD 联合的 Active Directory 联合身份验证服务 (ADFS)）一起使用，因此经常用于企业应用程序。 OpenID Connect 通常用于纯云端应用，例如移动应用、网站和 Web API。

## <a name="next-steps"></a>后续步骤

有关其他涉及身份验证和授权基础知识的主题：

* 请参阅[安全令牌](security-tokens.md)，了解如何在授权和身份验证中使用访问令牌、刷新令牌和 ID 令牌。
* 请参阅[应用程序模型](application-model.md)，了解注册应用程序的过程，以便它可以与 Microsoft 标识平台集成。
* 请参阅[应用登录流](app-sign-in-flow.md)，了解 Microsoft 标识平台中 Web、桌面和移动应用的登录流。

* 请参阅 [Microsoft 标识平台上的 OAuth 2.0 和 OpenID Connect 协议](active-directory-v2-protocols.md)，详细了解 Microsoft 标识平台实现的协议。
* 请参阅[单一登录 SAML 协议](single-sign-on-saml-protocol.md)，详细了解 Microsoft 标识平台如何支持单一登录。
* 请参阅[单一登录到 Azure Active Directory 中的应用程序](../manage-apps/what-is-single-sign-on.md)，详细了解在应用程序中实现单一登录的不同方法。
