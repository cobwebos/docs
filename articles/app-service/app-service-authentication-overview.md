---
title: Azure 应用服务中的身份验证和授权 | Microsoft Docs
description: 概念性参考和概述：Azure 应用服务的身份验证/授权功能
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/29/2016
ms.author: mahender
ms.openlocfilehash: c180dcf5d769245f3fa2485ccee2cbc18ecf5f67
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Azure 应用服务中的身份验证和授权

Azure 应用服务提供内置的身份验证和授权支持。只需在 Web 应用、API、移动后端和 [Azure Functions](../azure-functions/functions-overview.md) 中编写少量的代码或根本无需编写代码，就能让用户登录和访问数据。 本文介绍应用服务如何帮助简化应用的身份验证和授权。 

安全身份验证和授权需要对联合身份验证、加密、[JSON Web 令牌 (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) 管理、[授权类型](https://oauth.net/2/grant-types/)等安全性方面有深度的了解。 应用服务提供这些实用工具，让你将更多的时间和精力花费在为客户提供业务价值上。

> [!NOTE]
> 无需使用应用服务进行身份验证和授权。 许多 Web 框架绑定了安全功能，你可以根据需要使用不同的功能。 如果所需的灵活性超出了应用服务能够提供的范畴，还可以编写自己的实用工具。  
>

有关特定于本机移动应用的信息，请参阅[使用 Azure 应用服务对移动应用进行用户身份验证和授权](../app-service-mobile/app-service-mobile-auth.md)。

## <a name="how-it-works"></a>工作原理

身份验证和授权模块在应用程序代码所在的同一沙盒中运行。 启用后，每个传入的 HTTP 请求将通过此模块，然后由应用程序代码处理。

![](media/app-service-authentication-overview/architecture.png)

此模块为应用处理多项操作：

- 使用指定的提供程序对用户进行身份验证
- 验证、存储和刷新令牌
- 管理经过身份验证的会话
- 将标识信息插入请求标头

此模块独立于应用程序代码运行，使用应用设置进行配置。 不需要任何 SDK、特定语言，或者对应用程序代码进行更改。 

### <a name="user-claims"></a>用户声明

对于所有语言框架，应用服务通过将用户声明注入请求标头，向代码提供这些声明。 对于 ASP.NET 4.6 应用，应用服务会在 [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) 中填充经过身份验证的用户声明，使你能够遵循标准的 .NET 代码模式（包括 `[Authorize]` 属性）。 同样，对于 PHP 应用，应用服务会填充 `_SERVER['REMOTE_USER']` 变量。

对于 [Azure Functions](../azure-functions/functions-overview.md)，.NET 代码的 `ClaimsPrincipal.Current` 不会解冻，但仍可以在请求标头中找到用户声明。

有关详细信息，请参阅[访问用户声明](app-service-authentication-how-to.md#access-user-claims)。

### <a name="token-store"></a>令牌存储

应用服务提供内置的令牌存储，这是与 Web 应用、API 或本机移动应用的用户相关联的令牌存储库。 对任何提供程序启用身份验证时，此令牌存储可立即供应用使用。 如果应用程序代码需要代表用户访问这些提供程序中的数据，例如： 

- 发布到经过身份验证的用户的 Facebook 时间线
- 从 Azure Active Directory 图形 API 甚至 Microsoft Graph 中读取用户的企业数据

为经过身份验证的会话缓存的 ID 令牌、访问令牌和刷新令牌，只能由关联的用户访问。  

通常，必须编写代码才能在应用程序中收集、存储和刷新这些令牌。 使用令牌存储，只需在需要令牌时才[检索令牌](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)；当令牌失效时，可以[告知应用服务刷新令牌](app-service-authentication-how-to.md#refresh-access-tokens)。 

如果不需要在应用中使用令牌，可以禁用令牌存储。

### <a name="logging-and-tracing"></a>日志记录和跟踪

如果[启用应用程序日志记录](web-sites-enable-diagnostic-log.md)，将在日志文件中直接看到身份验证和授权跟踪。 如果出现意外的身份验证错误，查看现有的应用程序日志即可方便找到所有详细信息。 如果启用[失败请求跟踪](web-sites-enable-diagnostic-log.md)，可以确切地查看身份验证和授权模块在失败请求中发挥的作用。 在跟踪日志中，找到对名为 `EasyAuthModule_32/64` 的模块的引用。 

## <a name="identity-providers"></a>标识提供者

应用服务使用[联合标识](https://en.wikipedia.org/wiki/Federated_identity)，在其中，第三方标识提供者会自动管理用户标识和身份验证流。 默认提供五个标识提供者： 

| 提供程序 | 登录终结点 |
| - | - |
| [Azure Active Directory](../active-directory/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft 帐户](../active-directory/develop/active-directory-appmodel-v2-overview.md) | `/.auth/login/microsoft` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/+/web/api/rest/oauth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/docs/basics/authentication) | `/.auth/login/twitter` |

对其中一个提供程序启用身份验证和授权时，其登录终结点可用于用户身份验证，以及验证来自提供程序的身份验证令牌。 可以轻松为用户提供其中任意数量的登录选项。 还可以集成其他标识提供者或[自己的自定义标识解决方案][custom-auth]。

## <a name="authentication-flow"></a>身份验证流

身份验证流对于所有提供程序是相同的，但根据是否要使用提供程序的 SDK 登录而有所差别：

- 不使用提供程序 SDK：应用程序向应用服务委托联合登录。 浏览器应用通常采用此方案，这可以防止向用户显示提供程序的登录页。 服务器代码管理登录过程，因此，此流也称为“服务器导向流”或“服务器流”。 此方案适用于 Web 应用。 它也适用于使用移动应用客户端 SDK 登录用户的本机应用，因为 SDK 会打开 Web 视图，使用应用服务身份验证将用户登录。 
- 使用提供程序 SDK：应用程序手动将用户登录，然后将身份验证令牌提交给应用服务进行验证。 无浏览器应用通常采用此方案，这可以防止向用户显示提供程序的登录页。 应用程序代码管理登录过程，因此，此流也称为“客户端导向流”或“客户端流”。 此方案适用于 REST API、[Azure Functions](../azure-functions/functions-overview.md) 和 JavaScript 浏览器客户端，以及在登录过程中需要更高灵活性的 Web 应用。 它还适用于使用提供程序 SDK 登录用户的本机移动应用。

> [!NOTE]
> 可以使用服务器导向流，对来自应用服务中受信任浏览器应用的调用，或者来自应用服务或 [Azure Functions](../azure-functions/functions-overview.md) 中另一 REST API 的调用进行身份验证。 有关详细信息，请参阅[使用 Azure 应用服务对用户进行身份验证]()。
>

下表说明了身份验证流的步骤。

| 步骤 | 不使用提供程序 SDK | 使用提供程序 SDK |
| - | - | - |
| 1.将用户登录 | 将客户端重定向到 `/.auth/login/<provider>`。 | 客户端代码直接使用提供程序的 SDK 将用户登录，并接收身份验证令牌。 有关详细信息，请参阅提供程序文档。 |
| 2.身份验证后 | 提供程序将客户端重定向到 `/.auth/login/<provider>/callback`。 | 客户端代码将来自提供程序的令牌发布到 `/.auth/login/<provider>` 进行验证。 |
| 3.建立经过身份验证的会话 | 应用服务将经过身份验证的 Cookie 添加到响应中。 | 应用服务将自身的身份验证令牌返回给客户端代码。 |
| 4.提供经过身份验证的内容 | 客户端在后续请求中包含身份验证 Cookie（由浏览器自动处理）。 | 客户端代码在 `X-ZUMO-AUTH` 标头中提供身份验证令牌（由移动应用客户端 SDK 自动处理）。 |

对于客户端浏览器，应用服务可自动将所有未经身份验证的用户定向到 `/.auth/login/<provider>`。 还可以向用户提供一个或多个 `/.auth/login/<provider>` 链接，让他们使用所选的提供程序登录到你的应用。

<a name="authorization"></a>

## <a name="authorization-behavior"></a>授权行为

在 [Azure 门户](https://portal.azure.com)中，可以使用多种行为配置应用服务授权。

![](media/app-service-authentication-overview/authorization-flow.png)

以下标题介绍了选项。

### <a name="allow-all-requests-default"></a>允许所有请求（默认设置）

身份验证和授权不由应用服务管理（禁用）。 

如果不需要身份验证和授权，或者想要编写自己的身份验证和授权代码，请选择此选项。

### <a name="allow-only-authenticated-requests"></a>仅允许经过身份验证的请求

选项为“使用 \<提供程序> 登录”。 应用服务将所有匿名请求重定向到所选提供程序的 `/.auth/login/<provider>`。 如果匿名请求来自本机移动应用，则返回的响应为 `HTTP 401 Unauthorized`。

使用此选项不需要在应用中编写任何身份验证代码。 可以通过检查用户的声明来处理精细授权，例如角色特定的授权（请参阅[访问用户声明](app-service-authentication-how-to.md#access-user-claims)）。

### <a name="allow-all-requests-but-validate-authenticated-requests"></a>允许所有请求，但验证已经过身份验证的请求

选项为“允许匿名请求”。 此选项将在应用服务中启用身份验证和授权，但会推迟对应用程序代码所做的授权决策。 对于经过身份验证的请求，应用服务还会在 HTTP 标头中一起传递身份验证信息。 

使用此选项可以更灵活地处理匿名请求。 例如，可以向用户[提供多个登录选项](app-service-authentication-how-to.md#configure-multiple-sign-in-options)。 但是，必须编写代码。 

## <a name="more-resources"></a>更多资源

[教程：在 Azure 应用服务 (Windows) 中对用户进行端到端身份验证和授权](app-service-web-tutorial-auth-aad.md)  
[教程：在适用于 Linux 的 Azure 应用服务中对用户进行端到端身份验证和授权](containers/tutorial-auth-aad.md)  
[在应用服务中自定义身份验证和授权](app-service-authentication-how-to.md)

特定于提供程序的操作方法指南：

* [如何将应用配置为使用 Azure Active Directory 登录][AAD]
* [如何将应用配置为使用 Facebook 登录][Facebook]
* [如何将应用配置为使用 Google 登录][Google]
* [如何将应用配置为使用 Microsoft 帐户登录][MSA]
* [如何将应用配置为使用 Twitter 登录][Twitter]
* [如何对应用程序使用自定义身份验证][custom-auth]

[AAD]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: app-service-mobile-how-to-configure-google-authentication.md
[MSA]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
