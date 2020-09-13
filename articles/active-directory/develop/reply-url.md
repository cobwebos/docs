---
title: 重定向 URI（回复 URL）限制 | Azure
titleSuffix: Microsoft identity platform
description: 说明 Microsoft 标识平台对重定向 URI（回复 URL）格式强制实施的限制和局限。
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 08/07/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: bd6f88db2b55a5f0f445659e4b5ef609d3e146e9
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90030304"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a>重定向 URI（回复 URL）限制和局限

重定向 URI（或回复 URL）是在为应用成功授权并为其授予授权代码或访问令牌后，授权服务器将用户发送到的位置。 授权服务器将代码或令牌发送到重定向 URI，因此在应用注册过程中注册正确的位置非常重要。

 重定向 URI 存在以下限制：

* 重定向 URI 必须以方案 `https` 开头。

* 重定向 URI 区分大小写。 其大小写必须与正在运行的应用程序的 URL 路径的大小写匹配。 例如，如果应用程序在其路径中包括 `.../abc/response-oidc`，请不要在重定向 URI 中指定 `.../ABC/response-oidc`。 由于 Web 浏览器将路径视为区分大小写，因此在重定向到大小写不匹配的 `.../ABC/response-oidc` URL 时，可能会排除与 `.../abc/response-oidc` 关联的 cookie。

## <a name="maximum-number-of-redirect-uris"></a>重定向 URI 的最大数量

此表显示了可以在 Microsoft 标识平台中添加到应用注册的重定向 URI 的最大数目。

| 正在登录的帐户 | 最大重定向 URI 数 | 说明 |
|--------------------------|---------------------------------|-------------|
| 任何组织的 Azure Active Directory (Azure AD) 租户中的 Microsoft 工作或学校帐户 | 256 | 应用程序清单中的 `signInAudience` 字段设置为 *AzureADMyOrg* 或 *AzureADMultipleOrgs* |
| 个人 Microsoft 帐户以及工作和学校帐户 | 100 | 应用程序清单中的 `signInAudience` 字段设置为 AzureADandPersonalMicrosoftAccount |

## <a name="maximum-uri-length"></a>最大 URI 长度

对于要添加到应用注册中的每个重定向 URI，最多可以使用 256 个字符。

## <a name="supported-schemes"></a>支持的方案

Azure Active Directory (Azure AD) 应用程序模型目前同时支持 HTTP 和 HTTPS 方案，这两种方案所针对的应用可以在任何组织的 Azure AD 租户中登录工作或学校帐户。 这些帐户类型由应用程序清单的 `signInAudience` 字段中的 `AzureADMyOrg` 和 `AzureADMultipleOrgs` 值指定。 对于登录到个人 Microsoft 帐户的应用 (MSA) *以及* 工作和学校帐户 (也就是说，将 `signInAudience` 设置为 `AzureADandPersonalMicrosoftAccount`) ，只允许使用 HTTPS 方案。

若要将具有 HTTP 方案的重定向 URI 添加到用于登录工作帐户或学校帐户的应用注册，需要使用 Azure 门户的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)中的应用程序清单编辑器。 虽然可以使用清单编辑器来设置基于 HTTP 的重定向 URI，但我们强烈建议你为重定向 URI 使用 HTTPS 方案。

## <a name="localhost-exceptions"></a>Localhost 例外

[RFC 8252 8.3 节](https://tools.ietf.org/html/rfc8252#section-8.3)和 [7.3](https://tools.ietf.org/html/rfc8252#section-7.3) 节指出，“环回”或“localhost”重定向 URI 有两个特殊的注意事项：

1. `http` URI 方案是可接受的，因为重定向绝不会离开设备。 因此，下面这两个都是可接受的：
    - `http://127.0.0.1/myApp`
    - `https://127.0.0.1/myApp`
1. 由于原生应用程序经常需要临时端口范围，因此，在匹配重定向 URI 时会忽略端口组件（例如 `:5001` 或 `:443`）。 因此，下面所有这些都被视为等效项：
    - `http://127.0.0.1/MyApp`
    - `http://127.0.0.1:1234/MyApp`
    - `http://127.0.0.1:5000/MyApp`
    - `http://127.0.0.1:8080/MyApp`

从开发的角度来看，这意味着：

* 不要注册多个只有端口不同的重定向 URI。 登录服务器会任意选择一个，并使用与该重定向 URI 关联的行为（例如，是 `web` 类型的、`native` 类型的还是 `spa` 类型的重定向）。
* 如果需要在 localhost 上注册多个重定向 URI，以在开发过程中测试不同的流，请使用 URI 的 *path* 组件来区分它们。 例如，`http://127.0.0.1/MyWebApp` 与 `http://127.0.0.1/MyNativeApp` 不匹配。
* 根据 RFC 指导，不应在重定向 URI 中使用 `localhost`， 而应使用实际环回 IP 地址 `127.0.0.1`。 这可防止应用被错误配置的防火墙或重命名的网络接口破坏。

    若要将 `http` 方案与环回地址 (127.0.0.1) 而不是 localhost 一起使用，必须编辑 [应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#replyurls-attribute)。 

    当前不支持 IPv6 环回地址 (`[::1]`)。

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>重定向 URI 中对通配符的限制

类似 `https://*.contoso.com` 的通配符 URI 可能看起来很方便，但由于安全方面的影响，应避免使用它们。 根据 OAuth 2.0 规范（[RFC 6749 第 3.1.2 部分](https://tools.ietf.org/html/rfc6749#section-3.1.2)），重定向终结点 URI 必须是绝对 URI。

在配置为登录个人 Microsoft 帐户和工作或学校帐户的应用注册中，当前不支持通配符 Uri。 但是，对于组织的 Azure AD 租户中配置为仅将工作帐户或学校帐户登录的应用，允许使用通配符 URI。

若要将具有通配符的重定向 URI 添加到用于登录工作帐户或学校帐户的应用注册，需要使用 Azure 门户的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)中的应用程序清单编辑器。 尽管可以使用清单编辑器来设置具有通配符的重定向 URI，但我们强烈建议你遵循 [RFC 6749 的 3.1.2 节](https://tools.ietf.org/html/rfc6749#section-3.1.2)的要求，仅使用绝对 URI。

如果方案所需的重定向 URI 数目超过允许的最大限制，请考虑[以下方法](#use-a-state-parameter)，而不要添加通配符重定向 URI。

### <a name="use-a-state-parameter"></a>使用状态参数

如果你有多个子域，并且你的方案要求在身份验证成功时将用户重定向到开始操作时所在的页面，则使用状态参数可能有帮助。

在此方法中：

1. 为每个应用程序创建一个“共享的”重定向 URI，用于处理从授权终结点收到的安全令牌。
1. 应用程序可以在状态参数中发送应用程序特定的参数（例如用户的来源子域 URL，或品牌信息等）。 使用状态参数时，请根据 [RFC 6749 第 10.12 部分](https://tools.ietf.org/html/rfc6749#section-10.12)中的规定提供 CSRF 保护措施。
1. 应用程序特定的参数包含应用程序为用户呈现正确体验（即，构造相应的应用程序状态）所需的所有信息。 Azure AD 授权终结点会去除状态参数中的 HTML，因此请确保不要在此参数中传递 HTML 内容。
1. 当 Azure AD 向“共享的”重定向 URI 发送响应时，会将状态参数发回到应用程序。
1. 然后，应用程序可以使用状态参数中的值来确定要进一步将用户发送到哪个 URL。 确保验证 CSRF 保护措施。

> [!WARNING]
> 此方法允许遭到攻击的客户端修改状态参数中发送的其他参数，从而将用户重定向到其他 URL，这就是 RFC 6819 中所述的[开放重定向程序威胁](https://tools.ietf.org/html/rfc6819#section-4.2.4)。 因此，客户端必须对状态加密或通过其他一些方式进行验证（如根据令牌验证重定向 URI 中的域名），从而保护这些参数。

## <a name="next-steps"></a>后续步骤

了解应用注册[应用程序清单](reference-app-manifest.md)。
