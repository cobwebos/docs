---
title: 重定向 URI 和回复 URL 限制 - Microsoft 标识平台 | Azure
description: 回复 URL/重定向 URI 限制和局限
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 07/17/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 4fdeb0018e27a2557161b2ec1c4794d975403523
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87311613"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>重定向 URI/答复 URL 限制和局限

重定向 URI（或回复 URL）是在为应用成功授权并为其授予授权代码或访问令牌后，授权服务器将用户发送到的位置。 代码或令牌包含在重定向 URI 或回复令牌中，因此在应用注册过程中注册正确的位置非常重要。

 下面的限制适用于回复 URL：

* 回复 URL 必须以方案 `https` 开头。

* 回复 URL 区分大小写。 其大小写必须与正在运行的应用程序的 URL 路径的大小写匹配。 例如，如果应用程序在其路径中包括 `.../abc/response-oidc`，请不要在回复 URL 中指定 `.../ABC/response-oidc`。 由于 Web 浏览器将路径视为区分大小写，因此在重定向到大小写不匹配的 `.../ABC/response-oidc` URL 时，可能会排除与 `.../abc/response-oidc` 关联的 cookie。
    
## <a name="maximum-number-of-redirect-uris"></a>重定向 URI 的最大数量

下表显示了注册应用时可添加的最大重定向 URI 数。

| 正在登录的帐户 | 最大重定向 URI 数 | 说明 |
|--------------------------|---------------------------------|-------------|
| 任何组织的 Azure Active Directory (Azure AD) 租户中的 Microsoft 工作或学校帐户 | 256 | 应用程序清单中的 `signInAudience` 字段设置为 *AzureADMyOrg* 或 *AzureADMultipleOrgs* |
| 个人 Microsoft 帐户以及工作和学校帐户 | 100 | 应用程序清单中的 `signInAudience` 字段设置为 AzureADandPersonalMicrosoftAccount |

## <a name="maximum-uri-length"></a>最大 URI 长度

对于向应用注册添加的每个重定向 URI，最多可以使用 256 个字符。

## <a name="supported-schemes"></a>支持的方案

Azure AD 应用程序模型目前支持 HTTP 和 HTTPS 方案，用于在任何组织的 Azure Active Directory (Azure AD) 租户中登录 Microsoft 工作或学校帐户的应用程序。 即，应用程序清单中的 `signInAudience` 字段设置为 AzureADMyOrg 或 AzureADMultipleOrgs。 对于登录个人 Microsoft 帐户以及工作和学校帐户的应用（即，`signInAudience` 设置为 AzureADandPersonalMicrosoftAccount），只允许使用 HTTPS 方案。

> [!NOTE]
> 新的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验不允许开发人员在 UI 上添加使用 HTTP 方案的 URI。 对于登录工作或学校帐户的应用，仅支持通过应用清单编辑器添加 HTTP URI。 今后，新应用将无法在重定向 URI 中使用 HTTP 方案。 但是，在重定向 URI 中包含 HTTP 方案的早期应用将继续正常运行。 开发人员必须在重定向 URI 中使用 HTTPS 方案。

## <a name="localhost-exceptions"></a>Localhost 例外

每个[RFC 8252 部分 8.3](https://tools.ietf.org/html/rfc8252#section-8.3)和[7.3](https://tools.ietf.org/html/rfc8252#section-7.3)、"环回" 或 "Localhost" 重定向 uri 都具有两个特殊的注意事项：

1. `http`URI 方案是可接受的，因为重定向绝不会离开设备。  这意味着 `http://127.0.0.1/myApp` 可接受以及 `https://127.0.0.1/myApp` 。 
1. 由于本机应用程序通常需要临时端口范围，因此将忽略端口组件（如 `:5001` 或 `:443` ）以匹配重定向 URI。  因此， `http://127.0.0.1:5000/MyApp` `http://127.0.0.1:1234/MyApp` 这两个匹配项 `http://127.0.0.1/MyApp` 以及`http://127.0.0.1:8080/MyApp`

从开发的角度来看，这意味着：

1. 不要在仅端口不同的位置注册多个答复 Uri。  登录服务器将任意选择一个，并使用与该回复 URI 相关联的行为（例如，它是 `web` 、 `native` 、和 `spa` 类型的重定向）。
1. 如果需要在本地主机上注册多个重定向 Uri 以在开发过程中测试不同的流，请使用 URI 的*路径*组件来区分它们。  `http://127.0.0.1/MyWebApp`不匹配 `http://127.0.0.1/MyNativeApp` 。  
1. 根据 RFC 指南，不应 `localhost` 在重定向 URI 中使用。  相反，请使用实际环回 IP 地址- `127.0.0.1` 。 这可防止应用程序被错误配置的防火墙或重命名的网络接口中断。

>[!NOTE]
> 目前，不支持 IPv6 环回（ `[::1]` ）。  这会在以后添加。

## <a name="restrictions-using-a-wildcard-in-uris"></a>在 URI 中使用通配符的限制

通配符 URI（例如 `https://*.contoso.com`）可提供便利，但应避免使用。 在重定向 URI 中使用通配符会影响安全性。 根据 OAuth 2.0 规范（[RFC 6749 的第 3.1.2 节](https://tools.ietf.org/html/rfc6749#section-3.1.2)），重定向终结点 URI 必须为绝对 URI。

对于配置为登录个人 Microsoft 帐户和工作或学校帐户的应用，Azure AD 应用程序模型不支持通配符 URI。 但是，对于配置为在组织的 Azure AD 租户中登录工作或学校帐户的应用，现在允许使用通配符 URI。

> [!NOTE]
> 新的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验不允许开发人员在 UI 中添加通配符 URI。 对于使用工作或学校帐户登录的应用，仅支持通过应用清单编辑器添加通配符 URI。 今后，新应用无法在重定向 URI 中使用通配符。 但是，在重定向 URI 中包含通配符的旧应用程序将继续正常工作。

如果方案所需的重定向 URI 数目超过允许的最大限制，请不要添加通配符重定向 URI，而是考虑以下方法。

### <a name="use-a-state-parameter"></a>使用状态参数

如果你有多个子域，并且你的方案要求在对用户开始操作时所在的同一页面成功完成身份验证后重定向用户，则使用状态参数可能有帮助。

在此方法中：

1. 为每个应用程序创建一个“共享的”重定向 URI，用于处理从授权终结点收到的安全令牌。
1. 应用程序可以在状态参数中发送应用程序特定的参数（例如用户的来源子域 URL，或品牌信息等）。 使用状态参数时，请根据 [RFC 6749 第 10.12 部分](https://tools.ietf.org/html/rfc6749#section-10.12)中的规定提供 CSRF 保护措施。 
1. 应用程序特定的参数包含应用程序为用户呈现正确体验（即，构造相应的应用程序状态）所需的所有信息。 Azure AD 授权终结点会去除状态参数中的 HTML，因此请确保不要在此参数中传递 HTML 内容。
1. 当 Azure AD 向“共享的”重定向 URI 发送响应时，会将状态参数发回到应用程序。
1. 然后，应用程序可以使用状态参数中的值来确定要进一步将用户发送到哪个 URL。 确保验证 CSRF 保护措施。

> [!NOTE]
> 此方法允许遭到攻击的客户端修改状态参数中发送的其他参数，从而将用户重定向到其他 URL，这就是 RFC 6819 中所述的[开放重定向程序威胁](https://tools.ietf.org/html/rfc6819#section-4.2.4)。 因此，客户端必须对状态加密或通过其他一些方式进行验证（如根据令牌验证重定向 URI 中的域名），从而保护这些参数。

## <a name="next-steps"></a>后续步骤

- 了解[应用程序清单](reference-app-manifest.md)
