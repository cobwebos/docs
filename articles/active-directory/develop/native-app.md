---
title: Azure Active Directory 中的本机应用
description: 介绍什么是本机应用，以及有关此应用类型的协议流、注册和令牌到期的基础知识。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 09/24/2018
ms.date: 12/29/2018
ms.author: v-junlch
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: 4c15890a5693235e8f2554ba8d0fdefc161770f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60443887"
---
# <a name="native-apps"></a>本机应用

本机应用是代表用户调用 Web API 的应用程序。 此方案是基于带有公共客户端的 OAuth 2.0 授权代码授予类型构建的，如 [OAuth 2.0 规范](https://tools.ietf.org/html/rfc6749)的第 4.1 部分所述。 本机应用程序使用 OAuth 2.0 协议为用户获取访问令牌。 然后会在请求中将此访问令牌发送到 Web API，后者对用户进行授权并返回所需的资源。

## <a name="diagram"></a>图表

![本机应用程序到 Web API 图示](./media/authentication-scenarios/native_app_to_web_api.png)

## <a name="protocol-flow"></a>协议流

如果使用 AD 身份验证库，则会替你处理下面所述的大多数协议细节，例如浏览器弹出窗口、令牌缓存以及对刷新令牌的处理。

1. 本机应用程序使用浏览器弹出窗口向 Azure AD 中的授权终结点发出请求。 此请求包括本机应用程序的应用程序 ID 和重定向 URI（如 Azure 门户中所示），以及 Web API 的应用程序 ID URI。 如果用户尚未登录，同样会提示他们登录
1. Azure AD 对用户进行身份验证。 如果它是一个多租户应用程序并且需要许可才能使用应用程序，则用户将需要表示许可（如果他们尚未如此做）。 在用户表示许可并成功进行身份验证后，Azure AD 会将一个授权代码响应发回客户端应用程序的重定向 URI。
1. 当 Azure AD 将授权代码响应发回重定向 URI 时，客户端应用程序将停止浏览器交互并从响应中提取授权代码。 使用此授权代码，客户端应用程序向 Azure AD 的令牌终结点发送请求，请求中包括授权代码、关于客户端应用程序的详细信息（应用程序 ID 和重定向 URI）以及所需的资源（Web API 的应用程序 ID URI）。
1. Azure AD 对授权代码和关于客户端应用程序和 Web API 的信息进行验证。 当验证成功时，Azure AD 返回两个令牌：一个 JWT 访问令牌和一个 JWT 刷新令牌。 此外，Azure AD 还将返回关于用户的基本信息，例如其显示名称和租户 ID。
1. 通过 HTTPS，客户端应用程序使用返回的 JWT 访问令牌在发往 Web API 的请求的 Authorization 标头中添加一个具有“Bearer”限定符的 JWT 字符串。 然后，Web API 对 JWT 令牌进行验证，并且如果验证成功，则返回所需的资源。
1. 当访问令牌过期时，客户端应用程序将收到一个错误，指出用户需要重新进行身份验证。 如果应用程序具有有效的刷新令牌，则可以使用它来获取新的访问令牌，系统不会提示用户重新登录。 如果刷新令牌过期，则应用程序将再次需要以交互方式对用户进行身份验证。

> [!NOTE]
> Azure AD 颁发的刷新令牌可以用来访问多个资源。 例如，如果某个客户端应用程序有权调用两个 Web API，则同样可以使用刷新令牌来获取其他 Web API 的访问令牌。

## <a name="code-samples"></a>代码示例

请参阅本机应用程序到 Web API 方案的代码示例。 另外，请经常回来查看 - 我们会经常添加新示例。 [本机应用程序到 Web API](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api)。

## <a name="app-registration"></a>应用注册

若要向 Azure AD v1.0 终结点注册应用程序，请参阅[向 Azure AD v1.0 终结点注册应用](quickstart-v1-add-azure-ad-app.md)。

- 单租户 - 本机应用程序和 Web API 必须在 Azure AD 的同一个目录中进行注册。 可以对 Web API 进行配置以公开一组权限，并使用这些权限来限制本机应用程序对其资源的访问。 然后，客户端应用程序从 Azure 门户的“对其他应用程序的权限”下拉菜单中选择所需的权限。
- 多租户 - 首先，本机应用程序只在开发人员或发布者的目录中进行注册。 其次，本机应用程序在配置后会指示它在正常运行时所需的权限。 当目标目录中的用户或管理员表示许可应用程序的要求时（这会使应用程序可供其组织使用），此必需权限列表会显示在一个对话框中。 某些应用程序只需要用户级权限，组织中的任何用户都可以表示许可。 另外一些应用程序需要管理员级权限，组织中的用户无法表示许可。 只有目录管理员可以对需要此级别的权限的应用程序表示许可。 当用户或管理员表示许可后，才会在其目录中注册该 Web API。 

## <a name="token-expiration"></a>令牌过期

当本机应用程序使用其授权代码来获取 JWT 访问令牌时，它还会收到一个 JWT 刷新令牌。 访问令牌过期时，可以使用刷新令牌来重新对用户进行身份验证，不需要他们重新登录。 然后将使用此刷新令牌对用户进行身份验证，这会生成新的访问令牌和刷新令牌。

## <a name="next-steps"></a>后续步骤

- 详细了解其他[应用程序类型和方案](app-types.md)
- 了解 Azure AD [身份验证基础知识](authentication-scenarios.md)

<!-- Update_Description: link update -->