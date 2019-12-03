---
title: 身份验证/AuthO 的高级用法
description: 了解如何在应用服务中为不同方案自定义身份验证和授权功能，以及获取用户声明和不同令牌。
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: d57b196bf95ebdf31bc459ad4b9d718fd32ca495
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672229"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Azure 应用服务中的身份验证和授权的高级用法

本文介绍了如何自定义[应用服务中的内置身份验证和授权](overview-authentication-authorization.md)，以及如何从应用程序管理标识。 

若要快速入门，请参阅以下教程之一：

* [教程：在 Azure 应用服务 (Windows) 中对用户进行端到端身份验证和授权](app-service-web-tutorial-auth-aad.md)
* [教程：在适用于 Linux 的 Azure 应用服务中对用户进行端到端身份验证和授权](containers/tutorial-auth-aad.md)
* [如何将应用配置为使用 Azure Active Directory 登录](configure-authentication-provider-aad.md)
* [如何将应用配置为使用 Facebook 登录](configure-authentication-provider-facebook.md)
* [如何将应用配置为使用 Google 登录](configure-authentication-provider-google.md)
* [如何将应用配置为使用 Microsoft 帐户登录](configure-authentication-provider-microsoft.md)
* [如何将应用配置为使用 Twitter 登录](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>使用多个登录提供程序

门户配置不会向用户全面提供多个登录提供程序（例如 Facebook 和 Twitter）。 但是，将此功能添加到应用并不困难。 步骤概括如下：

首先，在 Azure 门户中的“身份验证/授权”页上，配置想要启用的每个标识提供者。

在“请求未经身份验证时需执行的操作”中，选择“允许匿名请求(无操作)”。

在登录页、导航栏或应用的其他任何位置中，将一个登录链接添加到已启用的每个提供程序 (`/.auth/login/<provider>`)。 例如：

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

当用户单击其中一个链接时，系统会打开相应的登录页让用户登录。

若要将登录后用户重定向到自定义 URL，请使用 `post_login_redirect_url` 查询字符串参数（不要与标识提供者配置中的重定向 URI 混淆）。 例如，若要在登录后将用户导航至 `/Home/Index`，使用以下 HTML 代码：

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>验证来自提供程序的令牌

在客户端定向的登录中，应用程序手动将用户登录到提供程序，然后将身份验证令牌提交给应用服务进行验证（请参阅[身份验证流](overview-authentication-authorization.md#authentication-flow)）。 此验证本身不实际向你授予对所需应用资源的访问权限，但成功的验证会向你提供一个会话令牌，可以使用该令牌来访问应用资源。 

若要验证提供程序令牌，必须首先为应用服务应用配置所需的提供程序。 在运行时，从你的提供程序检索身份验证令牌后，将令牌发布到 `/.auth/login/<provider>` 进行验证。 例如： 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

令牌格式根据提供程序而略有不同。 有关详细信息，请参阅下表：

| 提供程序值 | 请求正文中必需的 | 注释 |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | `expires_in` 属性为可选。 <br/>从 Live 服务请求令牌时，将始终请求 `wl.basic` 作用域。 |
| `google` | `{"id_token":"<id_token>"}` | `authorization_code` 属性为可选。 指定它时，还可以选择同时指定 `redirect_uri` 属性。 |
| `facebook`| `{"access_token":"<user_access_token>"}` | 使用来自 Facebook 的有效[用户访问令牌](https://developers.facebook.com/docs/facebook-login/access-tokens)。 |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

如果提供程序令牌成功通过验证，则 API 将在响应正文中返回一个 `authenticationToken`，这是你的会话令牌。 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

获得此会话令牌后，你可以通过向 HTTP 请求中添加 `X-ZUMO-AUTH` 标头来访问受保护的应用资源。 例如： 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>注销会话

用户可通过向应用的 `/.auth/logout` 终结点发送 `GET` 请求来启动注销。 `GET` 请求可执行以下操作：

- 清除当前会话中的身份验证 Cookie。
- 从令牌存储中删除当前用户的令牌。
- 对于 Azure Active Directory 和 Google，请对标识提供程序执行服务器端的注销。

以下是网页中一个简单的注销链接：

```HTML
<a href="/.auth/logout">Sign out</a>
```

默认情况下，成功注销后，客户端会重定向到 URL `/.auth/logout/done`。 通过添加 `post_logout_redirect_uri` 查询参数可更改注销后的重定向页面。 例如：

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

建议对 `post_logout_redirect_uri` 的值进行[编码](https://wikipedia.org/wiki/Percent-encoding)。

使用完全限定的 URL 时，URL 必须托管在同一域中，或配置为允许应用访问的外部重定向 URL。 在以下示例中，若要重定向到未托管在同一域中的 `https://myexternalurl.com`：

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

在[Azure Cloud Shell](../cloud-shell/quickstart.md)中运行以下命令：

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>保留 URL 片段

用户登录应用后，通常希望会重定向到同一页面的同一部分，例如 `/wiki/Main_Page#SectionZ`。 然而，由于从未向服务器发送 [URL 片段](https://wikipedia.org/wiki/Fragment_identifier)（例如，`#SectionZ`），因此默认情况下，OAuth 登录完成并重定向回应用后，会保留这些片段。 然后，当用户需再次导航到所需定位点时，他们无法获得最佳体验。 此限制存在于所有服务器端身份验证解决方案中。

在应用服务身份验证中，可跨 OAuth 登录保留 URL 片段。 为此，请将名为 `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` 的应用设置设为 `true`。 可在 [Azure 门户](https://portal.azure.com) 中执行此操作，或只需在 [Azure Cloud Shell](../cloud-shell/quickstart.md) 中运行以下命令：

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>访问用户声明

应用服务使用特殊的标头将用户声明传递给应用程序。 不允许外部请求设置这些标头，因此，只会提供应用服务设置的标头。 部分标头示例如下：

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

使用任何语言或框架编写的代码均可从这些标头获取所需信息。 对于 ASP.NET 4.6 应用，**ClaimsPrincipal** 会自动设置为相应的值。 但 ASP.NET Core 不提供与应用服务用户声明集成的身份验证中间件。 有关解决方法，请参阅[MaximeRouiller. AppService. EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)。

应用程序也可以通过调用 `/.auth/me` 来获取有关经过身份验证的用户的其他详细信息。 移动应用服务器 SDK 提供处理该数据的帮助器方法。 有关详细信息，请参阅[如何使用 Azure 移动应用 Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)和[使用适用于 Azure 移动应用的 .NET 后端服务器 SDK](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info)。

## <a name="retrieve-tokens-in-app-code"></a>检索应用代码中的令牌

在服务器代码中，提供程序特定的令牌将注入到请求标头中，使你可以轻松访问这些令牌。 下表显示了可能的令牌标头名称：

| 提供商 | 标头名称 |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook 令牌 | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft 帐户 | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

在客户端代码（例如移动应用或浏览器中 JavaScript）中，将 HTTP `GET` 请求发送到 `/.auth/me`。 返回的 JSON 包含提供程序特定的令牌。

> [!NOTE]
> 访问令牌用于访问提供程序资源，因此，仅当使用客户端机密配置了提供程序时，才提供这些令牌。 若要了解如何获取刷新令牌，请参阅“刷新访问令牌”。

## <a name="refresh-identity-provider-tokens"></a>刷新标识提供程序令牌

当提供程序的访问令牌（而不是[会话令牌](#extend-session-token-expiration-grace-period)）到期时，需要在再次使用该令牌之前重新验证用户。 向应用程序的 `/.auth/refresh` 终结点发出 `GET` 调用可以避免令牌过期。 调用应用服务时，应用服务会自动刷新已身份验证用户的令牌存储中的访问令牌。 应用代码发出的后续令牌请求将获取刷新的令牌。 但是，若要正常刷新令牌，令牌存储必须包含提供程序的[刷新令牌](https://auth0.com/learn/refresh-tokens/)。 每个提供程序会阐述获取刷新令牌的方式。以下列表提供了简短摘要：

- **Google**：将一个 `access_type=offline` 查询字符串参数追加到 `/.auth/login/google` API 调用。 如果使用移动应用 SDK，可将该参数添加到 `LogicAsync` 重载之一（请参阅 [Google 刷新令牌](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)）。
- **Facebook**：不提供刷新令牌。 生存期较长的令牌在 60 天后过期（请参阅 [Facebook 访问令牌的过期和延期](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)）。
- **Twitter**：访问令牌不会过期（请参阅 [Twitter OAuth 常见问题解答](https://developer.twitter.com/en/docs/basics/authentication/FAQ)）。
- **Microsoft 帐户**：[配置 Microsoft 帐户身份验证设置](configure-authentication-provider-microsoft.md)时，请选择 `wl.offline_access` 范围。
- **Azure Active Directory**：在 [https://resources.azure.com](https://resources.azure.com) 中执行以下步骤：
    1. 在页面顶部，选择“读/写”。
    2. 在左侧浏览器中，导航到 **subscriptions** >  **_\<subscription\_name_**  > **resourceGroups** >  **_\<resource\_group\_name>_**  > **providers** > **Microsoft.Web** > **sites** >  **_\<app\_name>_**  > **config** > **authsettings**。 
    3. 单击“编辑”。
    4. 修改以下属性。 将 _\<app\_id>_ 替换为要访问的服务的 Azure Active Directory 应用程序 ID。

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. 单击“放置”。 

配置提供程序后，可以在令牌存储区[查找刷新令牌和访问令牌的过期时间](#retrieve-tokens-in-app-code)。 

若要随时刷新访问令牌，只需以任意语言调用 `/.auth/refresh`。 以下代码片段从 JavaScript 客户端使用 jQuery 刷新访问令牌。

```JavaScript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

如果用户撤销了授予应用的权限，对 `/.auth/me` 的调用可能会失败并返回 `403 Forbidden` 响应。 若要诊断错误，请查看应用程序日志了解详细信息。

## <a name="extend-session-token-expiration-grace-period"></a>延长会话令牌过期宽限期

经过身份验证的会话会在 8 小时后过期。 经过身份验证的会话过期后，默认会提供 72 小时的宽限期。 在此宽限期内，可以使用应用服务刷新会话令牌，而无需重新对用户进行身份验证。 会话令牌失效后，只需调用 `/.auth/refresh`，而不需要自行跟踪令牌过期时间。 72 小时的宽限期过后，用户必须重新登录才能获取有效的会话令牌。

如果 72 小时的时间不够，可以延长此过期期限。 大大延长过期时间可能会造成严重的安全风险（例如身份验证令牌泄密或被盗）。 因此，应将宽限期保留为默认 72 小时，或者将延期设为最小值。

若要延长默认的过期期限，请在 [Cloud Shell](../cloud-shell/overview.md) 中运行以下命令。

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> 宽限期仅适用于应用服务的已经身份验证的会话，而不适用于来自标识提供者的令牌。 已过期的提供程序令牌没有宽限期。 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>限制登录帐户的域

Microsoft 帐户和 Azure Active Directory 都允许从多个域登录。 例如，Microsoft 帐户允许 _outlook.com_、_live.com_ 和 _hotmail.com_ 帐户。 Azure AD 允许登录帐户拥有任意数量的自定义域。 不过，你可能想要将用户直接转到你自己的品牌 Azure AD 登录页面（如 `contoso.com`）。 若要建议登录帐户的域名，请执行以下步骤。

在 [https://resources.azure.com](https://resources.azure.com) 中，导航到 **subscriptions** >  **_\< subscription\_ name_**  > **resourceGroups** >  **_\< resource\_ group\_ name>_**  > **providers** > **Microsoft.Web** > **sites** >  **_\< app\_ name>_**  > **config** > **authsettings**。 

单击“编辑”，修改以下属性，然后单击“放置”。 请务必将 _\<domain\_name>_ 替换为所需的域。

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

此设置将 `domain_hint` 查询字符串参数追加到登录重定向 URL。 

> [!IMPORTANT]
> 在接收重定向 URL 之后，客户端可能会删除 `domain_hint` 参数，然后使用不同的域登录。 所以虽然此功能非常方便，但它并不是一项安全功能。
>

## <a name="authorize-or-deny-users"></a>授权或拒绝用户

应用服务负责处理最简单的授权情况（即拒绝未经身份验证的请求），应用可能需要更精细的授权行为，例如仅限特定用户组的访问权限。 在某些情况下，你需要编写自定义应用程序代码以允许或拒绝对已登录用户的访问。 在其他情况下，应用服务或标识提供者可能能够帮助，而无需更改代码。

- [服务器级别](#server-level-windows-apps-only)
- [标识提供者级别](#identity-provider-level)
- [应用程序级别](#application-level)

### <a name="server-level-windows-apps-only"></a>服务器级别（仅限 Windows 应用）

对于任何 Windows 应用，都可以通过编辑*web.config*文件来定义 IIS web 服务器的授权行为。 Linux 应用不使用 IIS，因此不*能通过 web.config 进行配置。*

1. 导航到 `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. 在应用服务文件的浏览器资源管理器中，导航到*site/wwwroot*。 如果*web.config*不存在，请通过选择 " **+** " > **新文件**来创建它。 

1. 选择用于*web.config*的铅笔以对其进行编辑。 添加以下配置代码，并单击 "**保存**"。 如果*web.config*已经存在，只需添加包含其中所有内容的 `<authorization>` 元素。 在 `<allow>` 元素中添加想要允许的帐户。

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>标识提供者级别

标识提供者可能会提供某些密钥授权。 例如：

- 对于[Azure App Service](configure-authentication-provider-aad.md)，你可以直接在 Azure AD 中[管理企业级访问权限](../active-directory/manage-apps/what-is-access-management.md)。 有关说明，请参阅[如何删除用户对应用程序的访问权限](../active-directory/manage-apps/methods-for-removing-user-access.md)。
- 对于[google](configure-authentication-provider-google.md)，可以将属于某个[组织](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations)的 Google API 项目配置为仅允许你的组织中的用户访问（请参阅[Google 的**设置 OAuth 2.0**支持页面](https://support.google.com/cloud/answer/6158849?hl=en)）。

### <a name="application-level"></a>应用程序级别

如果任何一个级别未提供所需的授权，或者如果平台或标识提供者不受支持，则必须编写自定义代码，以根据[用户声明](#access-user-claims)向用户授权。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：对用户进行端到端身份验证和授权 (Windows)](app-service-web-tutorial-auth-aad.md)
> [教程：对用户进行端到端身份验证和授权 (Linux)](containers/tutorial-auth-aad.md)
