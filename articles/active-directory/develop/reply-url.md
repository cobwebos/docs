---
title: " (回复 URL 重定向 URI) 限制 |Microsoft"
titleSuffix: Microsoft identity platform
description: 有关重定向 URI 的限制和限制 (回复 URL 的限制和限制) 格式由 Microsoft 标识平台强制执行。
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 08/07/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 8be13a299de0fc3de0acaf0001722d8c96a460e6
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88205938"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a> (回复 URL 重定向 URI) 限制和限制

"重定向 URI" 或 "回复 URL" 是授权服务器在应用成功授权并被授予授权代码或访问令牌后发送用户的位置。 授权服务器将代码或令牌发送到重定向 URI，因此，在应用注册过程中注册正确的位置非常重要。

 重定向 URI 存在以下限制：

* 重定向 URI 必须以方案开头 `https` 。

* 重定向 URI 区分大小写。 其大小写必须与正在运行的应用程序的 URL 路径的大小写匹配。 例如，如果你的应用程序包括在其路径中 `.../abc/response-oidc` ，则不要 `.../ABC/response-oidc` 在重定向 URI 中指定。 由于 Web 浏览器将路径视为区分大小写，因此在重定向到大小写不匹配的 `.../ABC/response-oidc` URL 时，可能会排除与 `.../abc/response-oidc` 关联的 cookie。

## <a name="maximum-number-of-redirect-uris"></a>重定向 URI 的最大数量

此表显示了可添加到 Microsoft 标识平台中的应用注册的重定向 Uri 的最大数目。

| 正在登录的帐户 | 最大重定向 URI 数 | 说明 |
|--------------------------|---------------------------------|-------------|
| 任何组织的 Azure Active Directory (Azure AD) 租户中的 Microsoft 工作或学校帐户 | 256 | 应用程序清单中的 `signInAudience` 字段设置为 *AzureADMyOrg* 或 *AzureADMultipleOrgs* |
| 个人 Microsoft 帐户以及工作和学校帐户 | 100 | 应用程序清单中的 `signInAudience` 字段设置为 AzureADandPersonalMicrosoftAccount |

## <a name="maximum-uri-length"></a>最大 URI 长度

对于添加到应用注册的每个重定向 URI，最多可以使用256个字符。

## <a name="supported-schemes"></a>支持的方案

Azure Active Directory (Azure AD) 应用程序模型目前支持在任何组织的 Azure AD 租户中登录工作或学校帐户的应用的 HTTP 和 HTTPS 方案。 这些帐户类型由 `AzureADMyOrg` 和 `AzureADMultipleOrgs` 应用程序清单的字段中的值指定 `signInAudience` 。 对于登录到个人 Microsoft 帐户的应用 (MSA) *以及* 工作和学校帐户 (也就是说，将 `signInAudience` 设置为 `AzureADandPersonalMicrosoftAccount`) ，只允许使用 HTTPS 方案。

若要将具有 HTTP 方案的重定向 Uri 添加到登录工作帐户或学校帐户的应用注册，需要使用 Azure 门户中 [应用注册](https://go.microsoft.com/fwlink/?linkid=2083908) 的应用程序清单编辑器。 但是，虽然可以使用清单编辑器来设置基于 HTTP 的重定向 URI，但我们 *强烈* 建议使用 HTTPS 方案作为重定向 uri。

## <a name="localhost-exceptions"></a>Localhost 例外

每个 [RFC 8252 部分 8.3](https://tools.ietf.org/html/rfc8252#section-8.3) 和 [7.3](https://tools.ietf.org/html/rfc8252#section-7.3)、"环回" 或 "Localhost" 重定向 uri 都具有两个特殊的注意事项：

1. `http` URI 方案是可接受的，因为重定向绝不会离开设备。 因此，这两个都是可接受的：
    - `http://127.0.0.1/myApp`
    - `https://127.0.0.1/myApp`
1. 由于本机应用程序通常需要临时端口范围，因此 (`:5001` 或 `:443`) 会被忽略，以匹配重定向 URI。 因此，所有这些都被视为等效项：
    - `http://127.0.0.1/MyApp`
    - `http://127.0.0.1:1234/MyApp`
    - `http://127.0.0.1:5000/MyApp`
    - `http://127.0.0.1:8080/MyApp`

从开发的角度来看，这意味着：

* 不要注册多个重定向 Uri，其中只有端口不同。 登录服务器将任意选择一个，并使用与该重定向 URI 关联的行为 (例如，它是 `web` -、 `native` -或 `spa` -类型重定向) 。
* 如果需要在本地主机上注册多个重定向 Uri 以在开发过程中测试不同的流，请使用 URI 的 *路径* 组件来区分它们。 例如， `http://127.0.0.1/MyWebApp` 不匹配 `http://127.0.0.1/MyNativeApp` 。
* 根据 RFC 指南，不应 `localhost` 在重定向 URI 中使用。 相反，请使用实际环回 IP 地址 `127.0.0.1` 。 这可防止应用程序被错误配置的防火墙或重命名的网络接口中断。

    当前不支持 () 的 IPv6 环回地址 `[::1]` 。

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>重定向 Uri 中对通配符的限制

通配符 Uri `https://*.contoso.com` 可能看起来很方便，但由于安全方面的影响，应避免这样做。 根据 OAuth 2.0 规范（[RFC 6749 第 3.1.2 部分](https://tools.ietf.org/html/rfc6749#section-3.1.2)），重定向终结点 URI 必须是绝对 URI。

在配置为登录个人 Microsoft 帐户和工作或学校帐户的应用注册中，当前不支持通配符 Uri。 但是，对于配置为仅登录组织的 Azure AD 租户中的工作或学校帐户的应用，允许使用通配符 Uri。

若要将包含通配符的重定向 Uri 添加到登录工作帐户或学校帐户的应用注册，需要使用 Azure 门户中 [应用注册](https://go.microsoft.com/fwlink/?linkid=2083908) 的应用程序清单编辑器。 尽管可以通过使用清单编辑器来设置重定向 URI 和通配符，但 *强烈* 建议遵循 [RFC 6749 的3.1.2 部分](https://tools.ietf.org/html/rfc6749#section-3.1.2) ，并仅使用绝对 uri。

如果方案需要的重定向 Uri 比允许的最大限制多，请考虑使用 [以下方法](#use-a-state-parameter) ，而不是添加通配符重定向 URI。

### <a name="use-a-state-parameter"></a>使用状态参数

如果你有多个子域，并且你的方案要求在身份验证成功后，将用户重定向到他们开始使用的同一页面，使用状态参数可能会有所帮助。

在此方法中：

1. 为每个应用程序创建一个“共享的”重定向 URI，用于处理从授权终结点收到的安全令牌。
1. 您的应用程序可以发送特定于应用程序的参数， (例如用户起源的子域 URL，或状态参数) 的商标信息。 使用状态参数时，请根据 [RFC 6749 第 10.12 部分](https://tools.ietf.org/html/rfc6749#section-10.12)中的规定提供 CSRF 保护措施。
1. 应用程序特定的参数包含应用程序为用户呈现正确体验（即，构造相应的应用程序状态）所需的所有信息。 Azure AD 授权终结点会去除状态参数中的 HTML，因此请确保不要在此参数中传递 HTML 内容。
1. 当 Azure AD 向“共享的”重定向 URI 发送响应时，会将状态参数发回到应用程序。
1. 然后，应用程序可以使用状态参数中的值来确定要进一步将用户发送到哪个 URL。 确保验证 CSRF 保护措施。

> [!WARNING]
> 此方法允许遭到攻击的客户端修改状态参数中发送的其他参数，从而将用户重定向到其他 URL，这就是 RFC 6819 中所述的[开放重定向程序威胁](https://tools.ietf.org/html/rfc6819#section-4.2.4)。 因此，客户端必须通过加密状态或通过其他一些方式来验证这些参数，如使用令牌验证重定向 URI 中的域名。

## <a name="next-steps"></a>后续步骤

了解应用注册 [应用程序清单](reference-app-manifest.md)。
