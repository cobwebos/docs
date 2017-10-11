---
title: "了解 Azure AD v2.0 支持的授权协议 |Microsoft 文档"
description: "支持 Azure AD v2.0 终结点协议指南。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 3750f975600575349e5ea9de249cf4521636fd2f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# v2.0 协议-OAuth 2.0 和 OpenID 连接
V2.0 终结点可以使用 Azure AD 进行标识作为-服务使用行业标准协议，OpenID Connect 和 OAuth 2.0。  符合标准的服务时，可以有这些协议的任何两个实现之间的细微差别。  如果您选择编写你的代码通过直接发送和处理 HTTP 请求或使用第三方开放源代码库，而不是使用我们的开放源代码库之一，此处的信息将很有用。
<!-- TODO: Need link to libraries above -->

> [!NOTE]
> 并非所有 Azure Active Directory 方案和功能都支持 v2.0 终结点。  若要确定是否应使用 v2.0 终结点，阅读有关[v2.0 限制](active-directory-v2-limitations.md)。
>
>

## 基础知识
在几乎所有的 OAuth 和 OpenID Connect 流，有四个参与方在交换中涉及：

![OAuth 2.0 角色](../../media/active-directory-v2-flows/protocols_roles.png)

* **授权服务器**是 v2.0 终结点。  它负责确保用户的标识，授予和吊销访问资源，并颁发令牌。  它也称为是标识提供程序-安全地处理用户的信息、 其访问权限，与流中的参与方之间的信任关系任何关系。
* **资源所有者**通常是最终用户。  它是拥有此数据，以及有权允许第三方访问该数据或共享资源的当事方。
* **OAuth 客户端**是你的应用，由其应用程序 id 标识。  它通常是最终用户交互，一方，它将从授权服务器请求令牌。  客户端必须由资源所有者授予访问该资源的权限。
* **资源服务器**是资源或数据所在的位置。  它信任授权服务器便可以安全地进行身份验证和授权 OAuth 客户端，并使用持有者 access_tokens 来确保可以授予对资源的访问权限。

## 应用程序注册
使用 v2.0 终结点的每个应用程序需要在要注册[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)它可以交互使用 OAuth 或 OpenID Connect 前。  应用程序注册过程将收集 （&) 将包含少量值分配给你的应用程序：

* **应用程序 Id**唯一标识你的应用程序
* A**重定向 URI**或**包标识符**可以用于定向回你的应用程序的响应
* 几个其他特定于方案的值。

有关详细信息，了解如何[将应用注册到](active-directory-v2-app-registration.md)。

## 终结点
注册后，应用程序与 Azure AD 的通信将请求发送到 v2.0 终结点：

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

其中`{tenant}`可以执行四个不同的值之一：

| 值 | 描述 |
| --- | --- |
| `common` |从 Azure Active Directory 来登录应用程序中允许使用个人 Microsoft 帐户和工作/学校帐户的用户。 |
| `organizations` |仅允许具有用户工作/学校帐户从 Azure Active Directory 来登录应用程序。 |
| `consumers` |仅允许用户使用个人 Microsoft 帐户 (MSA) 登录到应用程序。 |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`或`contoso.onmicrosoft.com` |仅允许具有用户工作/学校帐户登录到应用程序特定 Azure Active Directory 租户中。  可以使用 Azure AD 租户的友好域名，或者租户的 guid 标识符。 |

有关如何与这些终结点进行交互的详细信息，选择下的特定应用程序类型。

## 令牌
OAuth 2.0 和 OpenID Connect 的 v2.0 实现请广泛使用持有者令牌，包括表示为 Jwt 的持有者令牌。 持有者令牌是一种轻型安全令牌，授予对受保护资源的"持有者"访问权限。 在这个意义上来说，"持有者"是任意一方都可以提供令牌。 尽管方必须首先进行身份验证与 Azure AD 才能收到持有者令牌中，如果不采取必要的步骤来保护传输和存储中的令牌，它可以被截获和由非预期方。 虽然某些安全令牌具有内置机制来防止未经授权的方使用它们，持有者令牌没有这种机制，例如传输层安全 (HTTPS) 的安全通道中必须进行传输。 如果持有者令牌以明文传输，一个 man 接中间人攻击可恶意方来获得令牌并使用它来对受保护资源进行未经授权的访问。 当存储或缓存持有者令牌供以后使用时，将应用相同的安全原则。 始终确保你的应用传输和存储持有者令牌以安全的方式。 有关持有者令牌的更多安全注意事项，请参阅[RFC 6750 第 5 部分](http://tools.ietf.org/html/rfc6750)。

不同类型的令牌 v2.0 终结点中使用的更多详细信息可用于[v2.0 终结点的令牌引用](active-directory-v2-tokens.md)。

## 协议
如果你已准备好查看一些示例的请求，开始使用之一下面教程。  每个对应于特定的身份验证方案。  如果你需要帮助确定这你的右流，请查看[的类型的应用能够使用 v2.0 生成](active-directory-v2-flows.md)。

* [生成移动和本机应用程序使用 OAuth 2.0](active-directory-v2-protocols-oauth-code.md)
* [构建 Web 应用打开 id 为连接](active-directory-v2-protocols-oidc.md)
* [生成使用 OAuth 2.0 隐式流的单页面应用程序](active-directory-v2-protocols-implicit.md)
* [生成守护程序或服务器端进程的 OAuth 2.0 客户端凭据流](active-directory-v2-protocols-oauth-client-creds.md)
* [Web API 与 OAuth 2.0 代表流中获取令牌](active-directory-v2-protocols-oauth-on-behalf-of.md)
