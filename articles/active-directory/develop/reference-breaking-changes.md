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
ms.date: 07/26/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38383685f74020f5208d42df4428f896931fbe2a
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931789"
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

2019年8月:根据 URL 分析规则强制执行 POST 语义-重复参数将触发错误, 不会再忽略跨参数的引号, 并忽略[BOM](https://www.w3.org/International/questions/qa-byte-order-mark) 。

## <a name="july-2019"></a>2019年7月

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>仅当资源租户中存在客户端应用时, 才会发出单租户应用程序的仅限应用的令牌

**生效日期**：2019年7月26日

**受影响的终结点**：[1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)版和 v2.0[版](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**受影响的协议**：[客户端凭据 (仅限应用的令牌)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

安全更改在7月26日生效, 更改了应用程序令牌 (通过客户端凭据授予) 的颁发方式。 以前, 允许应用程序获取令牌, 以调用任何其他应用, 无论该应用程序的租户或角色是否存在。  此行为已更新, 使得资源 (有时称为 Web Api) 设置为单租户 (默认值) 时, 客户端应用程序必须存在于资源租户中。  请注意, 客户端与 API 之间的现有同意仍不是必需的, 并且应用程序仍应执行其自己的授权检查, `roles`以确保声明存在并且包含 API 所需的值。

此方案的错误消息当前指出: 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

若要解决此问题, 请使用管理员同意体验在租户中创建客户端应用程序服务主体, 或手动创建。  此要求确保租户已提供应用程序在租户内操作的权限。  

#### <a name="example-request"></a>示例请求

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...`在此示例中, 资源租户 (颁发机构) 为 contoso.com, 资源应用是为 contoso 租户调用`gateway.contoso.com/api`的单租户应用程序, 而客户端应用程序为。 `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`  如果客户端应用在 Contoso.com 中有服务主体, 此请求可以继续。  但如果不是这样, 则请求将失败, 并出现上面的错误。  

但是, 如果 Contoso 网关应用是多租户应用程序, 则无论客户端应用在 Contoso.com 中有服务主体, 请求都将继续。  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>重定向 Uri 现在可以包含查询字符串参数

**生效日期**：2019 年 7 月 22 日

**受影响的终结点**：v1.0 和 v2.0

**受影响的协议**：所有流

按照[RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2), Azure AD 应用程序现在可以注册并使用具有静态查询参数 ( https://contoso.com/oauth2?idp=microsoft) 例如 OAuth 2.0 请求) 的重定向 (回复) uri。  动态重定向 uri 仍处于禁用状态, 因为它们表示安全风险, 这不能用于在身份验证请求中保留状态信息-对于这种情况`state` , 请使用参数。

与重定向 URI 的任何其他部分一样, 静态查询参数会服从重定向 uri 的字符串匹配-如果没有注册与 URI 解码的 redirect_uri 匹配的字符串, 则会拒绝该请求。  如果在应用程序注册中找到了 URI, 则将使用整个字符串来重定向用户, 包括静态查询参数。 

请注意, 在此时间 (2019 年7月结束), 应用注册 UX Azure 门户仍会阻止查询参数。  不过, 您可以手动编辑应用程序清单, 以添加查询参数并在应用程序中对其进行测试。  


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
