---
title: Azure Active Directory 中的服务到服务应用
description: 介绍什么是服务到服务应用，以及有关此应用类型的协议流、注册和令牌到期的基础知识。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 683664b3172cb12ba6adf6c8006e9685a6d1ec35
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540300"
---
# <a name="service-to-service-apps"></a>服务到服务应用

服务到服务应用程序可以是需要通过 Web API 获取资源的守护程序或服务器应用程序。 有两个适用于本部分的子方案：

- 需要调用 Web API 的守护程序，基于 OAuth 2.0 客户端凭据授予类型构建

    在此方案中，务必了解一些事项。 首先，用户无法与后台应用程序进行交互，因为这要求应用程序具有其自己的标识。 在后台运行的批处理作业或操作系统服务都是后台应用程序的示例。 此类型的应用程序通过以下方式来请求访问令牌：使用其应用程序标识并向 Azure AD 提供其应用程序 ID、凭据（密码或证书）以及应用程序 ID URI。 在身份验证成功后，后台应用程序会从 Azure AD 收到一个访问令牌，然后将使用该令牌来调用 Web API。

- 需要调用 Web API 的服务器应用程序（如 Web API），基于 OAuth 2.0 代理草案规范构建

    在此方案中，假定用户已在某个本机应用程序上通过了身份验证，并且此本机应用程序需要调用 Web API。 Azure AD 颁发一个 JWT 访问令牌来调用 Web API。 如果 Web API 需要调用另一个下游 Web API，则它可以使用 on-behalf-of 流来委托用户的标识并通过第二层 Web API 进行身份验证。

## <a name="diagram"></a>图表

![后台或服务器应用程序到 Web API 图示](./media/authentication-scenarios/daemon_server_app_to_web_api.png)

## <a name="dprotocol-flow"></a>DProtocol 流

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>带有 OAuth 2.0 客户端凭据授权的应用程序标识

1. 首先，服务器应用程序需要自行通过 Azure AD 进行身份验证，不涉及任何人为交互（例如交互式登录对话框）。 它向 Azure AD 的令牌终结点发出一个请求，在其中提供凭据、应用程序 ID 以及应用程序 ID URI。
1. Azure AD 对应用程序进行身份验证并返回用来调用 Web API 的 JWT 访问令牌。
1. 通过 HTTPS，Web 应用程序使用返回的 JWT 访问令牌在发往 Web API 的请求的 Authorization 标头中添加一个具有“Bearer”限定符的 JWT 字符串。 然后，Web API 对 JWT 令牌进行验证，并且如果验证成功，则返回所需的资源。

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>采用 OAuth 2.0 代理草案规范的委托用户标识

下面讨论的流假定用户已在另一应用程序（例如本机应用程序）上通过了身份验证，并且已使用其用户标识来获取第一层 Web API 的访问令牌。

1. 本机应用程序将访问令牌发送到第一层 Web API。
1. 第一层 Web API 向 Azure AD 的令牌终结点发送一个请求，其中提供了其应用程序 ID 和凭据以及用户的访问令牌。 此外，将随请求发送一个 on_behalf_of 参数，此参数指示 Web API 是在代表原始用户请求新令牌以调用下游 Web API。
1. Azure AD 验证第一层 Web API 是否有权访问第二层 Web API 并对请求进行验证，并将一个 JWT 访问令牌和一个 JWT 刷新令牌返回给第一层 Web API。
1. 然后，第一层 Web API 使用 HTTPS 通过将令牌字符串附加到请求的 Authorization 标头中来调用第二层 Web API。 只要访问令牌和刷新令牌有效，第一层 Web API 就可以继续调用第二层 Web API。

## <a name="code-samples"></a>代码示例

请参阅后台或服务器应用程序到 Web API 方案的代码示例。 另外，请经常回来查看，因为我们会经常添加新示例。 [服务器或后台应用程序到 Web API](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>应用注册

* 单租户 - 对于应用程序标识和委托用户标识这两种情况，守护程序或服务器应用程序都必须在 Azure AD 的同一个目录中进行注册。 可以对 Web API 进行配置以公开一组权限，并使用这些权限来限制后台或服务器对其资源的访问。 如果使用的是委托用户标识类型，则服务器应用程序需要从 Azure 门户的“对其他应用程序的权限”下拉菜单中选择所需的权限。 如果使用的是应用程序标识类型，则不需要此步骤。
* 多租户 - 首先，守护程序或服务器应用程序在配置后会指示它在正常运行时所需的权限。 当目标目录中的用户或管理员表示许可应用程序的要求时（这会使应用程序可供其组织使用），此必需权限列表会显示在一个对话框中。 某些应用程序只需要用户级权限，组织中的任何用户都可以表示许可。 另外一些应用程序需要管理员级权限，组织中的用户无法表示许可。 只有目录管理员可以对需要此级别的权限的应用程序表示许可。 当用户或管理员表示许可后，会在其目录中注册这两个 Web API。

## <a name="token-expiration"></a>令牌过期

当第一个应用程序使用其授权代码来获取 JWT 访问令牌时，它还会收到一个 JWT 刷新令牌。 当访问令牌过期时，可以使用刷新令牌来重新对用户进行身份验证，不会提示他们输入凭据。 然后将使用此刷新令牌对用户进行身份验证，这会生成新的访问令牌和刷新令牌。

## <a name="next-steps"></a>后续步骤

- 详细了解其他[应用程序类型和方案](app-types.md)
- 了解 Azure AD [身份验证基础知识](authentication-scenarios.md)
