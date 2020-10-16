---
title: Azure Active Directory 的 OpenID Connect 身份验证
description: 实现此身份验证模式的体系结构指南
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f2ad3b5e86eebfc2d6f1f42f8a2ab0520144b5
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114085"
---
# <a name="openid-connect-authentication-with-azure-active-directory"></a>Azure Active Directory 的 OpenID Connect 身份验证

OpenID Connect (OIDC) 是基于 OAuth2 协议的身份验证协议 (用于授权) 。 OIDC 使用来自 OAuth2 的标准化消息流来提供标识服务。 

OIDC 的设计目标是 "尽可能简单、复杂的"。 OIDC 使开发人员能够跨网站和应用程序进行身份验证，而无需拥有和管理密码文件。 这为应用生成器提供了一种安全的方法，用于验证当前使用浏览器的用户身份或连接到应用程序的本机应用。

用户的身份验证必须在将检查用户的会话或凭据的标识提供程序上进行。 为此，需要一个受信任的代理。 本机应用通常会出于此目的启动系统浏览器。 嵌入的视图被视为不受信任，因为没有任何内容可阻止应用程序对用户密码进行侦听。 

除了身份验证外，还可以要求用户提供许可。 许可是用户允许应用程序访问受保护资源的显式权限。 同意与身份验证不同，因为只需为资源提供一次同意。 许可保持有效，直到用户或管理员手动撤消授予。 

## <a name="use-when"></a>何时使用

需要用户同意和 web 登录。

![体系结构图](./media/authentication-patterns/oidc-auth.png)

## <a name="components-of-system"></a>系统组件

* **User**：从应用程序请求服务。

* **受信任的代理**：用户与之交互的组件。 此受信任的代理通常是 web 浏览器。

* **应用**程序：应用程序或资源服务器是资源或数据所在的位置。 它信任标识提供程序来安全地对受信任的代理进行身份验证和授权。 

* **Azure AD**： OIDC 提供程序，也称为标识提供程序，安全管理与用户的信息、其访问以及流中各方之间的信任关系有关的任何内容。 它将对用户的身份进行身份验证、授予和吊销对资源的访问权限，以及颁发令牌。 

## <a name="implement-oidc-with-azure-ad"></a>通过 Azure AD 实现 OIDC

* [将应用程序与 Azure AD 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [Microsoft 标识平台上的 OAuth 2.0 和 OpenID Connect 协议](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) 

* [Microsoft 标识平台和 OpenID Connect 协议](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) 

* [在 Azure Active Directory B2C 中使用 OpenID Connect 进行 Web 登录](https://docs.microsoft.com/azure/active-directory-b2c/openid-connect) 

* [使用 OpenID Connect 和 Azure AD 保护应用程序的安全](https://docs.microsoft.com/learn/modules/secure-app-with-oidc-and-azure-ad/) 

 
