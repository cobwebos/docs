---
title: Azure Active Directory 中断性变更引用 |Microsoft Docs
description: 了解对可能会影响应用程序的 Azure AD 协议所做的更改。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b3d5aa74705d858349eaca543a7fd86e315a63e6
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702991"
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

2019年9月：根据 URL 分析规则附加了 POST 语义-重复参数将触发错误并忽略[BOM](https://www.w3.org/International/questions/qa-byte-order-mark) 。

## <a name="august-2019"></a>2019 年 8 月

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>将强制执行 POST 窗体语义，将忽略更严格的空格和引号

**生效日期**：2019年9月2日

**受影响的终结点**：v1.0 和 v2.0

**受影响的协议**：使用任何地方 POST （[客户端凭据](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)、[授权代码兑换](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)、 [ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)、 [OBO](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)和[刷新令牌兑换](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)）

从9/2 开始，使用 POST 方法的身份验证请求将使用更严格的 HTTP 标准进行验证。  具体而言，将不再从请求窗体值中删除空格和双引号（""）。 这些更改不会中断任何现有的客户端，并且将确保发送到 Azure AD 的请求可以每次可靠地处理。 将来（请参阅上文）我们计划再次拒绝重复参数，并忽略请求中的 BOM。 

示例：

目前，`?e=    "f"&g=h` 的分析方式与 `?e=f&g=h` 相同-因此 `e` == `f`。  进行这种更改后，现在将对其进行分析，以便 `e` == `    "f"`-这不太可能是有效的参数，请求现在会失败。 


## <a name="july-2019"></a>2019 年 7 月

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>仅当资源租户中存在客户端应用时，才会发出单租户应用程序的仅限应用的令牌

**生效日期**：2019年7月26日

**受影响的终结点**： [1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)版和 v2.0[版](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**受影响的协议**：[客户端凭据（仅限应用的令牌）](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

安全更改在7月26日生效，更改了应用程序令牌（通过客户端凭据授予）的颁发方式。 以前，允许应用程序获取令牌，以调用任何其他应用，无论该应用程序的租户或角色是否存在。  此行为已更新，使得资源（有时称为 Web Api）设置为单租户（默认值）时，客户端应用程序必须存在于资源租户中。  请注意，客户端与 API 之间的现有同意仍不是必需的，应用程序仍应执行其自己的授权检查，以确保 `roles` 声明存在并且包含 API 的预期值。

此方案的错误消息当前指出： 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

若要解决此问题，请使用管理员同意体验在租户中创建客户端应用程序服务主体，或手动创建。  此要求确保租户已提供应用程序在租户内操作的权限。  

#### <a name="example-request"></a>示例请求

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` 在此示例中，资源租户（颁发机构）为 contoso.com，则资源应用是一个名为 `gateway.contoso.com/api` 用于 Contoso 租户的单租户应用，并且 `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`客户端应用。  如果客户端应用在 Contoso.com 中有服务主体，此请求可以继续。  但如果不是这样，则请求将失败，并出现上面的错误。  

但是，如果 Contoso 网关应用是多租户应用程序，则无论客户端应用在 Contoso.com 中有服务主体，请求都将继续。  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>重定向 Uri 现在可以包含查询字符串参数

**生效日期**：2019年7月22日

**受影响的终结点**：v1.0 和 v2.0

**受影响的协议**：所有流

按照[RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)，Azure AD 应用程序现在可以注册并使用具有静态查询参数的重定向（回复） uri （例如，OAuth 2.0 请求 https://contoso.com/oauth2?idp=microsoft) 。  动态重定向 Uri 仍处于禁用状态，因为它们表示安全风险，这不能用于在身份验证请求中保留状态信息-对于这种情况，请使用 `state` 参数。

与重定向 URI 的任何其他部分一样，静态查询参数会服从重定向 uri 的字符串匹配-如果没有注册与 URI 解码 redirect_uri 匹配的字符串，则会拒绝该请求。  如果在应用程序注册中找到了 URI，则将使用整个字符串来重定向用户，包括静态查询参数。 

请注意，在此时间（2019年7月结束），应用注册 UX Azure 门户仍会阻止查询参数。  不过，您可以手动编辑应用程序清单，以添加查询参数并在应用程序中对其进行测试。  


## <a name="march-2019"></a>2019 年 3 月

### <a name="looping-clients-will-be-interrupted"></a>循环客户端将中断

**生效日期**：2019年3月25日

**受影响的终结点**：v1.0 和 v2.0

**受影响的协议**：所有流

通常，客户端应用程序可以错误行为，在短时间内发出数百个相同的登录请求。  这些请求可能会也可能不会成功，但它们都影响到 IDP 的用户体验和提升的工作负荷，增加了所有用户的延迟，降低了 IDP 的可用性。  这些应用程序在正常使用范围外运行，应更新为正确的行为。  

多次发出重复请求的客户端将收到 `invalid_grant` 错误： `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`。 

大多数客户端无需更改行为即可避免此错误。  只有配置错误的客户端（未使用令牌缓存的客户端或那些状态提示循环）会受到此错误的影响。  客户端在本地（通过 cookie）按实例跟踪，原因如下：

* 用户提示（如果有）

* 请求的作用域或资源

* 客户端 ID

* 重定向 URI

* 响应类型和模式

在较短的一段时间（5分钟）内发出多个请求（15 +）的应用程序将收到一个 `invalid_grant` 错误，该错误说明它们是循环的。  请求的令牌具有足够长的生存期（默认为10分钟60，默认为10分钟），因此在此时间段内重复的请求是不必要的。  

所有应用都应该通过显示交互式提示符来处理 `invalid_grant`，而不是以无提示方式请求令牌。  若要避免此错误，客户端应确保它们正确地缓存接收到的令牌。


## <a name="october-2018"></a>2018 年 10 月

### <a name="authorization-codes-can-no-longer-be-reused"></a>授权代码无法再重复使用

**生效日期**：2018 年 11 月 15 日

**受影响的终结点**：v1.0 和 v2.0

**受影响的协议**：[代码流](v2-oauth2-auth-code-flow.md)

从 2018 年 11 月 15 日起，Azure AD 不再允许对应用使用以前用过的身份验证代码。 此项安全变更有助于使 Azure AD 与 OAuth 规范保持一致，将在 v1 和 v2 终结点上强制实施。

如果应用重复使用授权代码来获取多个资源的令牌，则我们建议使用该代码获取刷新令牌，然后使用该刷新令牌获取其他资源的其他令牌。 授权代码只能使用一次，但刷新令牌可对多个资源使用多次。 尝试在 OAuth 代码流期间重用身份验证代码的任何新应用都将收到 invalid_grant 错误。

有关刷新令牌的详细信息，请参阅[刷新访问令牌](v1-protocols-oauth-code.md#refreshing-the-access-tokens)。  如果使用 ADAL 或 MSAL，则由库为你处理 - 将“AcquireTokenByAuthorizationCodeAsync”的第二个实例替换为“AcquireTokenSilentAsync”。 

## <a name="may-2018"></a>2018 年 5 月

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID 令牌不能用于 OBO 流

**日期**：2018 年 5 月 1 日

**受影响的终结点**：v1.0 和 v2.0

**受影响的协议**：隐式流和 [OBO 流](v1-oauth2-on-behalf-of-flow.md)

在 2018 年 5 月 1 日之后，id_token 不能用作新应用程序的 OBO 流中的断言。 应改为使用访问令牌来保护 API，即使在同一应用程序的客户端和中间层之间也是如此。 在 2018 年 5 月 1 日之前注册的应用将继续有效，并能够使用 id_tokens 交换访问令牌；但是，此模式并不是最佳做法。

要解决此更改，可以执行以下操作：

1. 使用一个或多个作用域为应用程序创建 Web API。 此显式入口点可实现更精细的控制和安全性。
1. 在 [Azure 门户](https://portal.azure.com)或[应用注册门户](https://apps.dev.microsoft.com)的应用清单中，确保允许应用通过隐式流发出访问令牌。 这通过 `oauth2AllowImplicitFlow` 密钥进行控制。
1. 客户端应用程序通过 `response_type=id_token` 请求 id_token 时，还会请求上面创建的 Web API 的访问令牌 (`response_type=token`)。 因此，使用 v2.0 终结点时，`scope` 参数应与 `api://GUID/SCOPE` 类似。 在 v1.0 终结点上，`resource` 参数应为 Web API 应用 URI。
1. 将此访问令牌传递到中间层，代替 id_token。  
