---
title: Azure Active Directory 中的 Web 应用
description: 介绍什么是 Web 应用，以及有关此应用类型的协议流、注册和令牌到期的基础知识。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0337f3de40e75ada86c0a5265fc99b34f1c2ecae
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58367909"
---
# <a name="web-apps"></a>Web 应用

Web 应用是在 Web 浏览器到 Web 应用方案中对用户进行身份验证的应用程序。 在此方案中，Web 应用程序指示用户的浏览器让用户登录到 Azure AD 中。 Azure AD 通过用户的浏览器返回一个登录响应，该响应在一个安全令牌中包含了关于用户的声明。 此方案支持使用 OpenID Connect、SAML 2.0 和 WS 联合身份验证协议进行登录。

## <a name="diagram"></a>图表

![浏览器到 Web 应用程序的身份验证流](./media/authentication-scenarios/web_browser_to_web_api.png)

## <a name="protocol-flow"></a>协议流

1. 当用户访问应用程序并需要登录时，系统会通过一个登录请求将其重定向到 Azure AD 中的身份验证终结点。
1. 用户在登录页面上进行登录。
1. 如果身份验证成功，则 Azure AD 将创建一个身份验证令牌并将登录响应返回到应用程序的回复 URL（已在 Azure 门户中配置）。 对于生产应用程序，此回复 URL 应当采用 HTTPS 格式。 返回的令牌包括应用程序对该令牌进行验证所需的关于用户和 Azure AD 的声明。
1. 应用程序使用 Azure AD 的联合元数据文档中提供的公用签名密钥和颁发者信息对令牌进行验证。 验证令牌后，应用程序会启动与用户的新会话。 该会话允许用户访问应用程序，直到会话过期。

## <a name="code-samples"></a>代码示例

请参阅 Web 浏览器到 Web 应用程序方案的代码示例。 另外，请经常回来查看，因为我们会经常添加新示例。

## <a name="app-registration"></a>应用注册

若要注册 Web 应用，请参阅[向 Azure AD v1.0 终结点注册应用](quickstart-v1-add-azure-ad-app.md)。

* 单租户 - 如果在构建仅供组织使用的应用程序，则必须使用 Azure 门户在公司的目录中注册该应用程序。
* 多租户 - 如果在构建可以由组织外部用户使用的应用程序，则必须在公司的目录中注册该应用程序，并且还必须在将使用该应用程序的每个组织的目录中注册该应用程序。 要使应用程序在客户的目录中可用，可以提供一个供客户使用的注册流程，让客户许可应用程序的要求。 当他们针对应用程序进行注册时，系统会向他们显示一个对话框，其中显示了应用程序要求的权限，之后是表示许可的选项。 可能会要求其他组织中的管理员表示许可，具体取决于所需的权限。 当用户或管理员表示许可后，会在其目录中注册该应用程序。

## <a name="token-expiration"></a>令牌过期

当 Azure AD 颁发的令牌的生存期过期时，用户的会话便过期。 如果需要，应用程序可以缩短此时段，例如，根据用户处于不活动状态的时长注销用户。 当会话过期时，会提示用户重新登录。

## <a name="next-steps"></a>后续步骤

* 详细了解其他[应用程序类型和方案](app-types.md)
* 了解 Azure AD [身份验证基础知识](authentication-scenarios.md)
