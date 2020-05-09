---
title: Microsoft 标识平台上的 OAuth 2.0 和 OpenID Connect 协议 |Microsoft
titleSuffix: Microsoft identity platform
description: 有关 Microsoft 标识平台终结点支持的 OAuth 2.0 和 OpenID Connect 协议的指导。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 12f5df9b644246092f0a5da2b30dc5a7187ca827
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926810"
---
# <a name="oauth-20-and-openid-connect-protocols-on-microsoft-identity-platform"></a>Microsoft 标识平台上的 OAuth 2.0 和 OpenID Connect 协议

Microsoft 标识平台终结点，适用于使用行业标准协议、OpenID Connect （OIDC）和 OAuth 2.0 的标识即服务。 尽管此服务与标准兼容，但这些协议的两个实现之间仍然存在微妙的差异。 如果你选择通过直接发送和处理 HTTP 请求或使用第三方开源库来编写代码，而不是使用我们的一个[开源](reference-v2-libraries.md)库来编写代码，此处的信息将非常有用。

## <a name="the-basics"></a>基础知识

几乎在所有的 OAuth 2.0 和 OpenID Connect 流中，都有四个参与交换的对象：

![显示 OAuth 2.0 角色的示意图](./media/active-directory-v2-flows/protocols-roles.svg)

* **授权服务器**是 Microsoft 标识平台终结点，它负责确保用户的标识、授予和吊销对资源的访问权限，以及颁发令牌。 授权服务器也称为标识提供者 - 它可安全处理与用户信息、用户访问权，以及流中各方彼此间信任关系有关的任何项目。
* 资源所有者通常是最终用户****。 它是拥有数据的一方，并且有权允许第三方访问该数据或资源。
* OAuth 客户端是应用，按照其应用程序 ID 进行标识****。 OAuth 客户端通常是与最终用户交互的对象，并向授权服务器请求令牌。 客户端必须获得资源所有者授权才能访问资源。
* **资源服务器**是资源或数据所在的位置。 它信任授权服务器安全验证和授权 OAuth 客户端，并使用持有者访问令牌来确保可以授予对资源的访问权限。

## <a name="app-registration"></a>应用注册

要接受个人和工作或学校帐户的每个应用都必须通过[Azure 门户](https://aka.ms/appregistrations)中的**应用注册**体验进行注册，然后才能使用 OAuth 2.0 或 OpenID connect 对这些用户进行签名。 应用注册进程会收集一些值并将其分配到应用：

* 用于唯一标识应用的**应用程序 ID**
* 可用于将响应定向回到应用的**重定向 URI**（可选）
* 其他一些特定于方案的值。

请了解如何 [注册应用](quickstart-register-app.md)获取详细信息。

## <a name="endpoints"></a>终结点

注册后，应用将通过向终结点发送请求来与 Microsoft 标识平台通信。

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

其中 `{tenant}` 可以接受以下四个不同值之一：

| 值 | 说明 |
| --- | --- |
| `common` | 允许用户使用个人 Microsoft 帐户和工作/学校帐户从 Azure AD 登录应用程序。 |
| `organizations` | 仅允许用户使用工作/学校帐户从 Azure AD 登录应用程序。 |
| `consumers` | 仅允许用户使用个人的 Microsoft 帐户 (MSA) 登录应用程序。 |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` 或 `contoso.onmicrosoft.com` | 仅允许用户使用工作/学校帐户从特定的 Azure AD 租户登录应用程序。 可以使用 Azure AD 租户的友好域名或租户的 GUID 标识符。 |

要了解如何与这些终结点进行交互，请在[协议](#protocols)部分中选择特定的应用类型，然后通过访问链接获取更多信息。

> [!TIP]
> 在 Azure AD 中注册的任何应用都可以使用 Microsoft 标识平台终结点，即使它们没有登录个人帐户也是如此。  这样，你便可以将现有的应用程序迁移到 Microsoft 标识平台和 [MSAL](reference-v2-libraries.md)，而无需重新创建应用程序。

## <a name="tokens"></a>令牌

OAuth 2.0 和 OpenID Connect 的 Microsoft 标识平台实现广泛使用持有者令牌，包括表示为 Jwt 的持有者令牌（JSON Web 令牌）。 持有者令牌是一种轻型安全令牌，它授予对受保护资源的“持有者”访问权限。 从这个意义上来说，“持有者”是可以提供令牌的任何一方。 虽然某一方必须首先通过 Microsoft 标识平台的身份验证才能收到持有者令牌，但如果不采取必要的步骤在传输和存储过程中对令牌进行保护，令牌可能会被意外的某一方拦截并使用。 虽然某些安全令牌具有内置机制来防止未经授权方使用它们，但是持有者令牌没有这一机制，因此必须在安全的通道（例如传输层安全 (HTTPS)）中进行传输。 如果持有者令牌以明文形式传输，则恶意方可以使用中间人攻击来获取令牌并将其用于对受保护资源进行未经授权的访问。 当存储或缓存持有者令牌供以后使用时，也应遵循同样的安全原则。 请始终确保应用以安全的方式传输和存储持有者令牌。 有关持有者令牌的更多安全注意事项，请参阅 [RFC 6750 第 5 部分](https://tools.ietf.org/html/rfc6750)。

有关 Microsoft 标识平台终结点中使用的不同令牌类型的更多详细信息，请参阅 [Microsoft 标识平台终结点令牌参考](v2-id-and-access-tokens.md)。

## <a name="protocols"></a>协议

如果已准备好查看部分示例请求，请从下列教程之一开始。 每个教程对应于特定的身份验证方案。 如果你需要帮助来确定哪个是正确的流，请查看[可以用 Microsoft 标识平台构建的应用类型](v2-app-types.md)。

* [生成具有 OAuth 2.0 的移动和本机应用程序](v2-oauth2-auth-code-flow.md)
* [使用 OpenID Connect 构建 Web 应用](v2-protocols-oidc.md)
* [使用 OAuth 2.0 隐式流构建单页应用](v2-oauth2-implicit-grant-flow.md)
* [使用 OAuth 2.0 客户端凭据流构建守护程序或服务器端进程](v2-oauth2-client-creds-grant-flow.md)
* [使用 OAuth 2.0 代理流在 Web API 中获取令牌](v2-oauth2-on-behalf-of-flow.md)
