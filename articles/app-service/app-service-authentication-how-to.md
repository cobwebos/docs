---
title: 在 Azure 应用服务中自定义身份验证和授权 | Microsoft Docs
description: 介绍如何在应用服务中自定义身份验证和授权，以及获取用户声明和不同的令牌。
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2018
ms.author: cephalin
ms.openlocfilehash: c41cb3ef2939fe7271b1f8738fcf0cb95c4b1111
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="customize-authentication-and-authorization-in-azure-app-service"></a>在 Azure 应用服务中自定义身份验证和授权

本文介绍如何[在应用服务中自定义身份验证和授权](app-service-authentication-overview.md)，以及从应用程序管理标识。 

若要快速入门，请参阅以下教程之一：

* [教程：在 Azure 应用服务 (Windows) 中对用户进行端到端身份验证和授权](app-service-web-tutorial-auth-aad.md)
* [教程：在适用于 Linux 的 Azure 应用服务中对用户进行端到端身份验证和授权](containers/tutorial-auth-aad.md)
* [如何将应用配置为使用 Azure Active Directory 登录](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [如何将应用配置为使用 Facebook 登录](app-service-mobile-how-to-configure-facebook-authentication.md)
* [如何将应用配置为使用 Google 登录](app-service-mobile-how-to-configure-google-authentication.md)
* [如何将应用配置为使用 Microsoft 帐户登录](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [如何将应用配置为使用 Twitter 登录](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="configure-multiple-sign-in-options"></a>配置多个登录选项

门户配置不会向用户全面提供多个登录选项（例如 Facebook 和 Twitter）。 但是，将此功能添加到 Web 应用并不困难。 步骤概括如下：

首先，在 Azure 门户中的“身份验证/授权”页上，配置想要启用的每个标识提供者。

在“请求未经身份验证时需执行的操作”中，选择“允许匿名请求(无操作)”。

在登录页、导航栏或 Web 应用的其他任何位置中，将一个登录链接添加到已启用的每个提供程序 (`/.auth/login/<provider>`)。 例如：

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

当用户单击其中一个链接时，系统会打开相应的登录页让用户登录。

## <a name="access-user-claims"></a>访问用户声明

应用服务使用特殊的标头将用户声明传递给应用程序。 不允许外部请求设置这些标头，因此，只会提供应用服务设置的标头。 部分标头示例如下：

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

使用任何语言或框架编写的代码均可从这些标头获取所需信息。 对于 ASP.NET 4.6 应用，**ClaimsPrincipal** 会自动设置为相应的值。

应用程序也可以通过调用 `/.auth/me` 来获取有关经过身份验证的用户的其他详细信息。 移动应用服务器 SDK 提供处理该数据的帮助器方法。 有关详细信息，请参阅[如何使用 Azure 移动应用 Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)和[使用适用于 Azure 移动应用的 .NET 后端服务器 SDK](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info)。

## <a name="retrieve-tokens-in-app-code"></a>检索应用代码中的令牌

在服务器代码中，提供程序特定的令牌将注入到请求标头中，使你可以轻松访问这些令牌。 下表显示了可能的令牌标头名称：

| | |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook 令牌 | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft 帐户 | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

在客户端代码（例如移动应用或浏览器中 JavaScript）中，将 HTTP `GET` 请求发送到 `/.auth/me`。 返回的 JSON 包含提供程序特定的令牌。

> [!NOTE]
> 访问令牌用于访问提供程序资源，因此，仅当使用客户端机密配置了提供程序时，才提供这些令牌。 若要了解如何获取刷新令牌，请参阅[刷新访问令牌](#refresh-access-tokens)。

## <a name="refresh-access-tokens"></a>刷新访问令牌

当提供程序的访问令牌过期时，需要重新对用户进行身份验证。 向应用程序的 `/.auth/refresh` 终结点发出 `GET` 调用可以避免令牌过期。 调用应用服务时，应用服务会自动刷新已身份验证用户的令牌存储中的访问令牌。 应用代码发出的后续令牌请求将获取刷新的令牌。 但是，若要正常刷新令牌，令牌存储必须包含提供程序的[刷新令牌](https://auth0.com/learn/refresh-tokens/)。 每个提供程序会阐述获取刷新令牌的方式。以下列表提供了简短摘要：

- **Google**：将一个 `access_type=offline` 查询字符串参数追加到 `/.auth/login/google` API 调用。 如果使用移动应用 SDK，可将该参数添加到 `LogicAsync` 重载之一（请参阅 [Google 刷新令牌](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)）。
- **Facebook**：不提供刷新令牌。 生存期较长的令牌在 60 天后过期（请参阅 [Facebook 访问令牌的过期和延期](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)）。
- **Twitter**：访问令牌不会过期（请参阅 [Twitter OAuth 常见问题解答](https://developer.twitter.com/docs/basics/authentication/guides/oauth-faq)）。
- **Microsoft 帐户**：[配置 Microsoft 帐户身份验证设置](app-service-mobile-how-to-configure-microsoft-authentication.md)时，请选择 `wl.offline_access` 范围。
- **Azure Active Directory**：在 [https://resources.azure.com](https://resources.azure.com) 中执行以下步骤：
    1. 在页面顶部，选择“读/写”。
    1. 在左侧浏览器中，导航到 **subscriptions** > **_\<subscription\_name_** > **resourceGroups** > _**\<resource\_group\_name>**_ > **providers** > **Microsoft.Web** > **sites** > _**\<app\_name>**_ > **config** > **authsettings**。 
    1. 单击“编辑”。
    1. 修改以下属性。 将 _\<app\_id>_ 替换为要访问的服务的 Azure Active Directory 应用程序 ID。

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    1. 单击“放置”。 

配置提供程序后，可以调用 `/.auth/me` 来查看刷新令牌是否出现在令牌存储中。 

若要随时刷新访问令牌，只需以任何语言调用 `/.auth/refresh`。 以下代码片段从 JavaScript 客户端使用 jQuery 刷新访问令牌。

```JavaScript
function refreshTokens() {
  var refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

如果用户撤销了授予应用的权限，对 `/.auth/me` 的调用可能会失败并返回 `403 Forbidden` 响应。 若要诊断错误，请检查应用程序日志了解详细信息。

## <a name="extend-session-expiration-grace-period"></a>延长会话过期宽限期

经过身份验证的会话过期后，默认会提供 72 小时的宽限期。 在此宽限期内，可以使用应用服务刷新会话 Cookie 或会话令牌，而无需重新对用户进行身份验证。 会话 Cookie 或会话令牌失效后，只需调用 `/.auth/refresh`，且不需要自行跟踪令牌过期时间。 72 小时的宽限期过后，用户必须再次登录才能获取有效的会话 Cookie 或会话令牌。

如果 72 小时的时间不够，可以延长此过期期限。 大大延长过期时间可能会造成严重的安全风险（例如身份验证令牌泄密或被盗）。 因此，应将宽限期保留为默认 72 小时，或者将延期设为最小值。

若要延长默认的过期期限，请在 [Cloud Shell](../cloud-shell/overview.md) 中运行以下命令。

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> 宽限期仅适用于应用服务的已经身份验证的会话，而不适用于来自标识提供者的令牌。 已过期的提供程序令牌没有宽限期。 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>限制登录帐户的域

Microsoft 帐户和 Azure Active Directory 都允许从多个域登录。 例如，Microsoft 帐户允许 _outlook.com_、_live.com_ 和 _hotmail.com_ 帐户。 Azure Active Directory 允许对登录帐户使用任意数量的自定义域。 对于内部应用，此行为可能不符合需要，因为你不希望具有 _outlook.com_ 帐户的任何人都拥有访问权限。 若要限制登录帐户的域名，请执行以下步骤。

在 [https://resources.azure.com](https://resources.azure.com) 中，导航到 **subscriptions** > **_\<subscription\_name_** > **resourceGroups** > _**\<resource\_group\_name>**_ > **providers** > **Microsoft.Web** > **sites** > _**\<app\_name>**_ > **config** > **authsettings**。 

单击“编辑”，修改以下属性，然后单击“放置”。 请务必将 _\<domain\_name>_ 替换为所需的域。

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：对用户进行端到端身份验证和授权 (Windows)](app-service-web-tutorial-auth-aad.md)
> [教程：对用户进行端到端身份验证和授权 (Linux)](containers/tutorial-auth-aad.md)