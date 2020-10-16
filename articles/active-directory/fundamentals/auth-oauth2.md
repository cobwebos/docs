---
title: Azure Active Directory 的 OAUTH 2.0 身份验证
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
ms.openlocfilehash: ea22c4e5b363eaa3ecc2a736dfef714666310062
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114086"
---
# <a name="oauth-20-authentication-with-azure-active-directory"></a>Azure Active Directory 的 OAuth 2.0 身份验证

OAuth 2.0 是用于授权的行业协议。 它允许用户授予对其受保护资源的有限访问权限。 专用于专用于超文本传输协议 (HTTP) ，OAuth 将客户端角色与资源所有者区分开来。 客户端请求对资源所有者控制的资源的访问权限，并请求资源服务器托管的资源。 资源服务器使用资源所有者的批准来颁发访问令牌。 客户端使用访问令牌访问资源服务器托管的受保护资源。 

OAuth 2.0 直接与 OpenID Connect (OIDC) 相关。 由于 OIDC 是在 OAuth 2.0 基础之上构建的身份验证和授权层，因此它并不与 OAuth 1.0 向后兼容。 Azure Active Directory (Azure AD) 支持所有 OAuth 2.0 流。 

## <a name="use-when"></a>何时使用：

对于富客户端 & 新式应用方案和 RESTful Web API 访问权限。

![体系结构示意图](./media/authentication-patterns/oauth.png)

## <a name="components-of-system"></a>系统组件

* **用户**：通过 web 应用程序 (应用程序) 请求服务。 用户通常是拥有数据的资源所有者，可以允许客户端访问数据或资源。 

* **Web 浏览器**：用户与之交互的 web 浏览器是 OAuth 客户端。 

* **Web 应用**： web 应用或资源服务器是资源或数据所在的位置。 它信任授权服务器，以安全地对 OAuth 客户端进行验证和授权。 

* **Azure AD**： Azure AD 是授权服务器，也称为标识提供程序 (IdP) 。 它安全地处理与用户信息、访问权限和信任关系相关的任何内容。 它负责颁发授予和撤销对资源的访问权限的令牌。

## <a name="implement-oauth-20-with-azure-ad"></a>通过 Azure AD 实现 OAuth 2。0

* [将应用程序与 Azure AD 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [Microsoft 标识平台上的 OAuth 2.0 和 OpenID Connect 协议](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) 

* [应用程序类型和 OAuth2](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) 

 
