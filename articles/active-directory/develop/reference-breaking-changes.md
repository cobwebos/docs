---
title: Azure Active Directory 中断性变更参考
description: 了解对可能会影响应用程序的 Azure AD 协议所做的更改。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 5/4/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 09f27c922df4a15858236b2635b962f4bc92811b
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871534"
---
# <a name="whats-new-for-authentication"></a>身份验证的新增功能

>获取有关此页更新的通知。 只需将[此 URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) 添加到 RSS 源阅读器即可。

身份验证系统会持续更改和添加功能，以增强安全性并提高标准符合性。 为了让大家随时了解最新的开发成果，本文将提供有关以下详细信息的信息：

- 最新功能
- 已知问题
- 协议更改
- 已弃用的功能

> [!TIP]
> 此页会定期更新，因此请经常访问。 除非另有说明，否则这些更改仅适用于新注册的应用程序。

## <a name="upcoming-changes"></a>即将推出的更改

目前没有计划。  请参阅下面的内容，了解已经进入或即将进入生产环境中的变更。

## <a name="may-2020"></a>2020 年 5 月

### <a name="azure-government-endpoints-are-changing"></a>Azure 政府版终结点正在更改

**生效日期**：5月5日（完成2020年6月） 

**受影响的终结点**：全部

**受影响的协议**：所有流

2018年6月1日，Azure 政府版的官方 Azure Active Directory （AAD）机构`https://login-us.microsoftonline.com`已`https://login.microsoftonline.us`从更改为。 此更改也适用于 Microsoft 365 GCC 高和 DoD，Azure 政府版 AAD 也服务。 如果你拥有美国政府租户中的应用程序，则必须更新应用程序，以便登录到`.us`终结点上的用户。  

从5月5日开始，Azure AD 将开始强制终结点更改，阻止政府用户使用公共终结点（`microsoftonline.com`）登录美国政府租户中托管的应用。  受影响的应用将开始显示`AADSTS900439`  -  `USGClientNotSupportedOnPublicEndpoint`错误。 此错误表示应用正尝试在公有云终结点上登录美国政府用户。 如果你的应用处于公有云租户中，并旨在支持我们的政府用户，则需要[更新你的应用以明确支持它们](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)。 这可能需要在美国政府云中创建新的应用注册。 

此更改的执行将通过以下方式进行：使用逐步推出：美国政府版中的用户登录到应用程序时，不经常会看到强制执行，而美国政府用户经常使用的应用将在最后应用强制实施。 预计会在6月2020的所有应用中完成强制。 

有关更多详细信息，请参阅[此迁移中的 Azure 政府博客文章](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)。 

## <a name="march-2020"></a>2020 年 3 月

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>用户密码将限制为 256 个字符。

**生效日期**：2020 年 3 月 13 日

**受影响的终结点**：全部

**受影响的协议**：所有用户流。

从 2020 年 3 月 13 日开始，直接登录 Azure AD（而不是像 ADFS 这样的联合 IDP）的密码超过 256 个字符的用户将无法登录，并被要求重置其密码。  管理员可能会收到要求帮助重置用户密码的请求。

登录日志中的错误将为 AADSTS 50052：InvalidPasswordExceedsMaxLength

消息：`The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

补救措施：

用户无法登录，因为其密码超过了允许的最大长度。 他们应该联系管理员以重置密码。 如果已为其租户启用了 SSPR，则他们可以通过打开“忘记密码”链接来重置其密码。



## <a name="february-2020"></a>2020 年 2 月

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>会将空片段追加到来自登录终结点的每个 HTTP 重定向。

**生效日期**：2020 年 2 月 8 日

**受影响的终结点**：v1.0 和 v2.0

**受影响的协议**：使用 response_type=query 的 OAuth 和 OIDC 流 - ，这涵盖了[授权代码流](v2-oauth2-auth-code-flow.md)（在某些情况下）和[隐式流](v2-oauth2-implicit-grant-flow.md)。

当通过 HTTP 重定向将身份验证响应从 login.microsoftonline.com 发送到应用程序时，该服务会将一个空片段追加到回复 URL。  这可以确保浏览器擦除身份验证请求中的任何现有片段，防止出现重定向攻击类。  任何应用都不应依赖于此行为。


## <a name="august-2019"></a>2019 年 8 月

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>POST 表单语义会更严格地实施 - 空格和引号会被忽略

**生效日期**：2019 年 9 月 2 日

**受影响的终结点**：v1.0 和 v2.0

**受影响的协议**：使用 POST 的任何位置（[客户端凭据](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)、[授权代码兑换](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)、[ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)、[OBO](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)和[刷新令牌兑换](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)）

从 9 月 2 日那一周开始，使用 POST 方法的身份验证请求会按更严格的 HTTP 标准进行验证。  具体说来，空格和双引号 (“) 将不再从请求表单值中删除。 这些更改不应造成任何现有客户端出现中断，将确保发送到 Azure AD 的请求每次都能够得到可靠的处理。 在将来（见上），我们计划还要拒绝重复参数并忽略请求中的 BOM。

示例：

目前，`?e=    "f"&g=h` 的分析与 `?e=f&g=h` 相同，因此 `e` == `f`。  进行此更改后，它现在可以进行分析，使得 `e` == `    "f"` - 这不可能成为一个有效的参数，请求会失败。


## <a name="july-2019"></a>2019 年 7 月

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>仅当客户端应用在资源租户中存在时，才为单租户应用程序颁发仅限应用的令牌

**生效日期**：2019 年 6 月 26 日

**受影响的终结点**：[v1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) 和 [v2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**受影响的协议**：[客户端凭据（仅限应用的令牌）](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

一项安全更改已在 7 月 26 日上线，它会更改颁发仅限应用的令牌的方式（通过客户端凭据授予）。 以前，允许应用程序获取令牌来调用任何其他应用，无论该应用程序是否在租户中存在或者是否向其许可了角色。  此行为现已更新，对于设置为单租户（默认值）的资源（有时称为 Web API），客户端应用程序必须在资源租户中存在。  请注意，仍不需要客户端与 API 之间的现有许可，应用应该仍会执行其自身的授权检查，以确保 `roles` 声明存在并且包含 API 所需的值。

此方案的错误消息目前会指出：

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

若要解决此问题，请使用管理员许可体验在租户中创建客户端应用程序服务主体，或手动创建该服务主体。  此项要求可确保租户授予应用程序在租户中操作的权限。

#### <a name="example-request"></a>示例请求

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` 在此示例中，资源租户（颁发机构）是 contoso.com，资源应用是 Contoso 租户的某个单租户应用（名为 `gateway.contoso.com/api`），客户端应用是 `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`。  如果客户端应用在 Contoso.com 中有服务主体，则此请求可以继续。  不过，如果没有服务主体，则请求将会失败并出现上面所示的错误。

但是，如果 Contoso 网关应用是多租户应用程序，则无论客户端应用是否在 Contoso.com 中有服务主体，请求都会继续。

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>重定向 URI 现在可以包含查询字符串参数

**生效日期**：2019 年 7 月 22 日

**受影响的终结点**：v1.0 和 v2.0

**受影响的协议**：所有流

根据 [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)，Azure AD 应用程序现在可以在 OAuth 2.0 请求的静态查询参数中注册并使用重定向（回复）URI（例如 `https://contoso.com/oauth2?idp=microsoft`）。  动态重定向 URI 仍旧受禁用，因为它们表示存在安全风险，不可将其用于保留身份验证请求的状态信息 - 为此，请使用 `state` 参数。

与重定向 URI 的任何其他部分一样，静态查询参数需要接受重定向 URI 的字符串匹配 - 如果未注册与 URI 解码的 redirect_uri 匹配的字符串，则会拒绝请求。  如果在应用注册中找到了 URI，则会使用整个字符串（包括静态查询参数）来重定向用户。

请注意，目前（2019 年 7 月底），Azure 门户中的应用注册 UX 仍会阻止查询参数。  不过，你可以手动编辑应用程序清单，以添加查询参数并在应用中对其进行测试。


## <a name="march-2019"></a>2019 年 3 月

### <a name="looping-clients-will-be-interrupted"></a>循环客户端将会中断

**生效日期**：2019 年 3 月 25 日

**受影响的终结点**：v1.0 和 v2.0

**受影响的协议**：所有流

客户端应用程序有时可能会出现行为异常，在短时间内发出数百个相同的登录请求。  这些请求不一定会成功，但会导致用户体验变得糟糕，增大 IDP 的工作负荷，增大所有用户的延迟，并降低 IDP 的可用性。  这些应用程序的工作范围超过了正常的使用边界，应予以更新才能让其保持正常的行为。

将为多次发出重复请求的客户端设置 `invalid_grant` 错误：`AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`。

大多数客户端无需改变行为即可避免此错误。  此错误只会影响配置不当的客户端（没有令牌缓存的客户端，或已经出现提示循环的客户端）。  根据以下因素，在本地按实例跟踪客户端（通过 Cookie）：

* 用户提示（如果有）

* 请求的范围或资源

* 客户端 ID

* 重定向 URI

* 响应类型和模式

在短时间（5 分钟）内发出多个请求（15 个以上）的应用将会收到 `invalid_grant` 错误，指出它们正在循环。  所请求的令牌具有足够长的生存期（最短 10 分钟，默认为 60 分钟），因此，在此时间段内发出的重复请求都是没有必要的。

所有应用应该通过显示交互式提示来处理 `invalid_grant`，而不是以静默方式请求令牌。  若要避免此错误，客户端应确保正确缓存它们收到的令牌。


## <a name="october-2018"></a>2018 年 10 月

### <a name="authorization-codes-can-no-longer-be-reused"></a>授权代码无法再重复使用

**生效日期**：2018 年 11 月 15 日

**受影响的终结点**：v1.0 和 v2.0

**受影响的协议**：[代码流](v2-oauth2-auth-code-flow.md)

从 2018 年 11 月 15 日起，Azure AD 不再允许对应用使用以前用过的身份验证代码。 此项安全变更有助于使 Azure AD 与 OAuth 规范保持一致，将在 v1 和 v2 终结点上强制实施。

如果应用重复使用授权代码来获取多个资源的令牌，则我们建议使用该代码获取刷新令牌，然后使用该刷新令牌获取其他资源的其他令牌。 授权代码只能使用一次，但刷新令牌可对多个资源使用多次。 尝试在 OAuth 代码流期间重用身份验证代码的任何新应用都将收到 invalid_grant 错误。

有关刷新令牌的详细信息，请参阅[刷新访问令牌](v2-oauth2-auth-code-flow.md#refresh-the-access-token)。  如果使用 ADAL 或 MSAL，则由库为你处理 - 将“AcquireTokenByAuthorizationCodeAsync”的第二个实例替换为“AcquireTokenSilentAsync”。

## <a name="may-2018"></a>2018 年 5 月

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID 令牌不能用于 OBO 流

**日期**：2018 年 5 月 1 日

**受影响的终结点**：v1.0 和 v2.0

**受影响的协议**：隐式流和[代理流](v2-oauth2-on-behalf-of-flow.md)

在 2018 年 5 月 1 日之后，id_token 不能用作新应用程序的 OBO 流中的断言。 应改为使用访问令牌来保护 API，即使在同一应用程序的客户端和中间层之间也是如此。 在 2018 年 5 月 1 日之前注册的应用将继续有效，并能够使用 id_tokens 交换访问令牌；但是，此模式并不是最佳做法。

要解决此更改，可以执行以下操作：

1. 使用一个或多个作用域为应用程序创建 Web API。 此显式入口点可实现更精细的控制和安全性。
1. 在 [Azure 门户](https://portal.azure.com)或[应用注册门户](https://apps.dev.microsoft.com)的应用清单中，确保允许应用通过隐式流发出访问令牌。 这通过 `oauth2AllowImplicitFlow` 密钥进行控制。
1. 客户端应用程序通过 `response_type=id_token` 请求 id_token 时，还会请求上面创建的 Web API 的访问令牌 (`response_type=token`)。 因此，使用 v2.0 终结点时，`scope` 参数应与 `api://GUID/SCOPE` 类似。 在 v1.0 终结点上，`resource` 参数应为 Web API 应用 URI。
1. 将此访问令牌传递到中间层，代替 id_token。
