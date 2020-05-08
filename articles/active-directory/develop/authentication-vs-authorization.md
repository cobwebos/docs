---
title: 身份验证与授权 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 标识平台（v2.0）中身份验证和授权的基本知识。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 61c0202d12756201d45fe829078d84382e44b54e
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584293"
---
# <a name="authentication-vs-authorization"></a>身份验证与授权

本文定义了身份验证和授权，并简要介绍了如何使用 Microsoft 标识平台在 web 应用、web Api 或调用受保护的 web Api 的应用中对用户进行身份验证和授权。 如果你看到不熟悉的术语，请尝试我们的[术语表](developer-glossary.md)或[Microsoft 标识平台视频](identity-videos.md)，其中介绍了基本概念。

## <a name="authentication"></a>身份验证

**身份验证**就是证明自己的身份的过程。 身份验证有时缩写为 AuthN。 Microsoft 标识平台实现了用于处理身份验证的[OpenID connect](https://openid.net/connect/)协议。

## <a name="authorization"></a>授权

**授权**是指授予经过身份验证的一方执行某项操作的权限的措施。 它指定了你可以访问的数据，以及可对该数据执行的操作。 授权有时缩写为 AuthZ。 Microsoft 标识平台实现了[OAuth 2.0](https://oauth.net/2/)协议来处理授权。

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>使用 Microsoft 标识平台进行身份验证和授权

无需创建每个需要维护自身用户名和密码信息的应用（需要在多个应用中添加或删除用户时，这会产生较高的管理负担），而可以让应用将这种责任委托给一个集中式标识提供者。

Azure Active Directory (Azure AD) 是云中的集中式标识提供者。 向其委托身份验证和授权可以实现条件访问策略等方案以要求用户位于特定位置、使用多重身份验证，以及允许用户登录一次后自动登录到共享同一集中式目录的所有 Web 应用。 此功能称为**单一登录（SSO）**。

Microsoft 标识平台通过提供标识即服务简化了应用程序开发人员的身份验证和授权，并支持行业标准协议（例如 OAuth 2.0 和 OpenID Connect）以及用于不同平台的开源库来帮助你快速开始编码。 开发人员可以通过它来生成应用程序，以便进行所有 Microsoft 标识的登录，以及获取令牌来调用 [Microsoft Graph](https://developer.microsoft.com/graph/)、其他 Microsoft API 或者开发人员生成的 API。 有关详细信息，请参阅 [Microsoft 标识平台的发展](about-microsoft-identity-platform.md)。

## <a name="next-steps"></a>后续步骤

有关涉及身份验证和授权的其他主题，请执行以下操作：

* 请参阅[安全令牌](security-tokens.md)，了解身份验证和授权中如何使用访问令牌、刷新令牌和 ID 令牌。
* 请参阅[应用程序模型](application-model.md)，了解注册应用程序的过程，使其能够与 Microsoft 标识平台集成。
* 请参阅[应用登录流](app-sign-in-flow.md)，了解 Microsoft 标识平台中的 web、桌面和移动应用的登录流。

若要了解有关 Microsoft 标识平台实现的协议的详细信息，请执行以下操作：

* 有关 OpenID Connect 和 OAuth 2.0 标准的详细信息，请参阅[Microsoft 标识平台上的 OAuth 2.0 和 OpenID connect 协议](active-directory-v2-protocols.md)。
* 有关 Microsoft 标识平台如何支持单一登录的详细信息，请参阅[单一登录 SAML 协议](single-sign-on-saml-protocol.md)。
