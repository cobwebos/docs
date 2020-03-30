---
title: Azure Active Directory 中的 Web 应用
description: 介绍什么是 Web 应用，以及有关此应用类型的协议流、注册和令牌到期的基础知识。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: e4a7fb72d40f5db65e8e30264e9d68b2727749e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154400"
---
# <a name="web-apps"></a>Web 应用

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Web 应用是在 Web 浏览器到 Web 应用方案中对用户进行身份验证的应用程序。 在这种情况下，Web 应用程序指示用户的浏览器将其登录到 Azure AD。 Azure AD 通过用户的浏览器返回一个登录响应，该响应在一个安全令牌中包含了关于用户的声明。 此方案支持使用 OpenID Connect、SAML 2.0 和 WS 联合身份验证协议进行登录。

## <a name="diagram"></a>图表

![浏览器到 Web 应用程序的身份验证流](./media/authentication-scenarios/web-browser-to-web-api.png)

## <a name="protocol-flow"></a>协议流

1. 当用户访问应用程序并需要登录时，系统会通过一个登录请求将其重定向到 Azure AD 中的身份验证终结点。
1. 用户在登录页面上进行登录。
1. 如果身份验证成功，Azure AD 将创建身份验证令牌，并返回对在 Azure 门户中配置的应用程序 Reply URL 的登录响应。 对于生产应用程序，此回复 URL 应当采用 HTTPS 格式。 返回的令牌包括应用程序对该令牌进行验证所需的关于用户和 Azure AD 的声明。
1. 应用程序使用 Azure AD 的联合元数据文档中提供的公用签名密钥和颁发者信息对令牌进行验证。 验证令牌后，应用程序会启动与用户的新会话。 该会话允许用户访问应用程序，直到会话过期。

## <a name="code-samples"></a>代码示例

请参阅 Web 浏览器到 Web 应用程序方案的代码示例。 另外，请经常回来查看，因为我们会经常添加新示例。

## <a name="app-registration"></a>应用注册

若要注册 Web 应用，请参阅[注册应用](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

* 单个租户 - 如果只为组织构建应用程序，则必须使用 Azure 门户在公司目录中注册该应用程序。
* 多租户 - 如果要构建一个应用程序，该应用程序可以由组织外部的用户使用，则必须在公司的目录中注册，但也必须在将使用该应用程序的每个组织的目录中注册。 要使应用程序在客户的目录中可用，可以提供一个供客户使用的注册流程，让客户许可应用程序的要求。 当他们针对应用程序进行注册时，系统会向他们显示一个对话框，其中显示了应用程序要求的权限，之后是表示许可的选项。 可能会要求其他组织中的管理员表示许可，具体取决于所需的权限。 当用户或管理员表示许可后，会在其目录中注册该应用程序。

## <a name="token-expiration"></a>令牌过期

当 Azure AD 颁发的令牌的生存期过期时，用户的会话将过期。 如果需要，应用程序可以缩短此时段，例如，根据用户处于不活动状态的时长注销用户。 当会话过期时，会提示用户重新登录。

## <a name="next-steps"></a>后续步骤

* 详细了解其他[应用程序类型和方案](app-types.md)
* 了解 Azure AD[身份验证基础知识](v1-authentication-scenarios.md)
