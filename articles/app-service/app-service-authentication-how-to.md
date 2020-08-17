---
title: 身份验证/AuthZ 的高级用法
description: 了解如何针对不同情况自定义应用服务中的身份验证和授权功能，并获取用户声明和不同令牌。
ms.topic: article
ms.date: 07/08/2020
ms.custom: seodec18
ms.openlocfilehash: 7ec16b5de6053256fa6565db510ee94776def2c4
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272308"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Azure 应用服务中的身份验证和授权的高级用法

本文介绍了如何自定义[应用服务中的内置身份验证和授权](overview-authentication-authorization.md)，以及如何从应用程序管理标识。 

若要快速入门，请参阅以下教程之一：

* [教程：在 Azure 应用服务中对用户进行端到端身份验证和授权](tutorial-auth-aad.md)
* [How to configure your app to use Azure Active Directory login](configure-authentication-provider-aad.md)
* [如何将应用配置为使用 Facebook 登录](configure-authentication-provider-facebook.md)
* [如何将应用配置为使用 Google 登录](configure-authentication-provider-google.md)
* [How to configure your app to use Microsoft Account login](configure-authentication-provider-microsoft.md)
* [如何将应用配置为使用 Twitter 登录](configure-authentication-provider-twitter.md)
* [如何将应用配置为使用 OpenID Connect 提供程序登录 (预览) ](configure-authentication-provider-openid-connect.md)

## <a name="use-multiple-sign-in-providers"></a>使用多个登录提供程序

门户配置不会向用户全面提供多个登录提供程序（例如 Facebook 和 Twitter）。 但是，将此功能添加到应用并不困难。 步骤概括如下：

首先，在 Azure 门户中的“身份验证/授权”页上，配置想要启用的每个标识提供者。 

在“请求未经身份验证时需执行的操作”中，选择“允许匿名请求(无操作)”。  

在登录页、导航栏或应用的其他任何位置中，将一个登录链接添加到已启用的每个提供程序 (`/.auth/login/<provider>`)。 例如：

```html
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

当用户单击其中一个链接时，系统会打开相应的登录页让用户登录。

若要将登录后用户重定向到自定义 URL，请使用 `post_login_redirect_url` 查询字符串参数（不要与标识提供者配置中的重定向 URI 混淆）。 例如，若要在登录后将用户导航至 `/Home/Index`，使用以下 HTML 代码：

```html
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

```html
<a href="/.auth/logout">Sign out</a>
```

默认情况下，成功注销后，客户端会重定向到 URL `/.auth/logout/done`。 通过添加 `post_logout_redirect_uri` 查询参数可更改注销后的重定向页面。 例如：

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

建议你对的值 [进行编码](https://wikipedia.org/wiki/Percent-encoding) `post_logout_redirect_uri` 。

使用完全限定的 URL 时，URL 必须托管在同一域中，或配置为允许应用访问的外部重定向 URL。 在以下示例中，若要重定向到未托管在同一域中的 `https://myexternalurl.com`：

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

在 [Azure Cloud Shell](../cloud-shell/quickstart.md)中运行以下命令：

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>保留 URL 片段

用户登录应用后，通常希望会重定向到同一页面的同一部分，例如 `/wiki/Main_Page#SectionZ`。 不过，由于 [URL 片段](https://wikipedia.org/wiki/Fragment_identifier) (例如， `#SectionZ`) 永远不会发送到服务器，因此，在 OAuth 登录完成后，默认情况下不保留它们，并重定向回你的应用程序。 然后，当用户需再次导航到所需定位点时，他们无法获得最佳体验。 此限制存在于所有服务器端身份验证解决方案中。

在应用服务身份验证中，可跨 OAuth 登录保留 URL 片段。 为此，请将名为 `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` 的应用设置设为 `true`。 可在 [Azure 门户](https://portal.azure.com) 中执行此操作，或只需在 [Azure Cloud Shell](../cloud-shell/quickstart.md) 中运行以下命令：

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>访问用户声明

应用服务使用特殊的标头将用户声明传递给应用程序。 不允许外部请求设置这些标头，因此，只会提供应用服务设置的标头。 部分标头示例如下：

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

使用任何语言或框架编写的代码均可从这些标头获取所需信息。 对于 ASP.NET 4.6 应用， **ClaimsPrincipal** 会自动设置为相应的值。 但是，ASP.NET Core 不提供与应用服务用户声明集成的身份验证中间件。 有关解决方法，请参阅 [MaximeRouiller.Azure.AppService.EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)。

如果已为你的应用启用 [令牌存储](overview-authentication-authorization.md#token-store) ，你还可以通过调用获取经过身份验证的用户的其他详细信息 `/.auth/me` 。 移动应用服务器 SDK 提供处理该数据的帮助器方法。 有关详细信息，请参阅[如何使用 Azure 移动应用 Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity) 和[使用适用于 Azure 移动应用的 .NET 后端服务器 SDK](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info)。

## <a name="retrieve-tokens-in-app-code"></a>检索应用代码中的令牌

在服务器代码中，提供程序特定的令牌将注入到请求标头中，使你可以轻松访问这些令牌。 下表显示了可能的令牌标头名称：

| 提供程序 | 标头名称 |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook 令牌 | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft 帐户 | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

通过客户端代码 (例如移动应用或浏览器中的 JavaScript) ，将 HTTP 请求发送 `GET` 到 `/.auth/me` ([令牌存储](overview-authentication-authorization.md#token-store) 必须) 启用。 返回的 JSON 包含提供程序特定的令牌。

> [!NOTE]
> 访问令牌用于访问提供程序资源，因此，仅当使用客户端机密配置了提供程序时，才提供这些令牌。 若要了解如何获取刷新令牌，请参阅“刷新访问令牌”。

## <a name="refresh-identity-provider-tokens"></a>刷新标识提供程序令牌

当提供程序的访问令牌（而不是[会话令牌](#extend-session-token-expiration-grace-period)）到期时，需要在再次使用该令牌之前重新验证用户。 向应用程序的 `/.auth/refresh` 终结点发出 `GET` 调用可以避免令牌过期。 调用后，应用服务会自动刷新经过身份验证的用户的 [令牌存储](overview-authentication-authorization.md#token-store) 中的访问令牌。 应用代码发出的后续令牌请求将获取刷新的令牌。 但是，若要正常刷新令牌，令牌存储必须包含提供程序的[刷新令牌](https://auth0.com/learn/refresh-tokens/)。 每个提供程序会阐述获取刷新令牌的方式。以下列表提供了简短摘要：

- **Google**：将一个 `access_type=offline` 查询字符串参数追加到 `/.auth/login/google` API 调用。 如果使用移动应用 SDK，可将该参数添加到 `LogicAsync` 重载之一（请参阅 [Google 刷新令牌](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)）。
- **Facebook**：不提供刷新令牌。 生存期较长的令牌在 60 天后过期（请参阅 [Facebook 访问令牌的过期和延期](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)）。
- **Twitter**：访问令牌不会过期（请参阅 [Twitter OAuth 常见问题解答](https://developer.twitter.com/en/docs/basics/authentication/FAQ)）。
- **Microsoft 帐户**：[配置 Microsoft 帐户身份验证设置](configure-authentication-provider-microsoft.md)时，请选择 `wl.offline_access` 范围。
- **Azure Active Directory**：在 [https://resources.azure.com](https://resources.azure.com) 中执行以下步骤：
    1. 在页面顶部，选择“读/写”。****
    2. 在左侧浏览器中，导航到 "**订阅**" > * *_ \<subscription\_name_** > **resourceGroups** > *_* \<resource\_group\_name> _>**提供商**">  >  **Microsoft.Web**  >  **sites** >。_ \<app\_name> **config**  >  **authsettings** 
    3. 单击 **“编辑”** 。
    4. 修改以下属性。 替换 _\<app\_id>_ 为要访问的服务的 Azure Active Directory 应用程序 ID。

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. 单击“放置”。**** 

配置提供程序以后，即可在令牌存储中[找到访问令牌的刷新令牌和过期时间](#retrieve-tokens-in-app-code)。 

若要随时刷新访问令牌，只需以任何语言调用 `/.auth/refresh`。 以下代码片段从 JavaScript 客户端使用 jQuery 刷新访问令牌。

```javascript
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

Microsoft 帐户和 Azure Active Directory 都允许从多个域登录。 例如，Microsoft 帐户允许 _outlook.com_、_live.com_ 和 _hotmail.com_ 帐户。 Azure AD 允许登录帐户拥有任意数量的自定义域。 不过，你可能想要将用户直接转到你自己的品牌 Azure AD 登录页面，如)  (`contoso.com` 。 若要建议登录帐户的域名，请执行以下步骤。

在中 [https://resources.azure.com](https://resources.azure.com) ，导航到 "**订阅**" > * *_ \<subscription\_name_** > **resourceGroups** > *_* \<resource\_group\_name> _>**提供商**">  >  **Microsoft.Web**  >  **sites** >。_ \<app\_name> **config**  >  **authsettings** 

单击“编辑”，修改以下属性，然后单击“放置”。******** 请确保将替换为所 _\<domain\_name>_ 需的域。

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

此设置将 `domain_hint` 查询字符串参数追加到登录重定向 URL。 

> [!IMPORTANT]
> 客户端在 `domain_hint` 接收重定向 URL 之后可以删除参数，然后使用不同的域登录。 所以虽然此功能非常方便，但它并不是一项安全功能。
>

## <a name="authorize-or-deny-users"></a>授权或拒绝用户

尽管应用服务会处理最简单的授权问题（例如，拒绝未经身份验证的请求），但应用可能需要更精细的授权行为，例如，仅将访问权限限制给特定的一组用户。 在某些情况下，需要编写自定义应用程序代码以允许或拒绝已登录用户的访问。 在其他情况下，应用服务或标识提供者可能无需进行代码更改即可提供帮助。

- [服务器级别](#server-level-windows-apps-only)
- [标识提供者级别](#identity-provider-level)
- [应用程序级别](#application-level)

### <a name="server-level-windows-apps-only"></a>服务器级别（仅限 Windows 应用）

对于任何 Windows 应用，可以通过编辑 *Web.config* 文件来定义 IIS Web 服务器的授权行为。 Linux 应用不使用 IIS，无法通过 *Web.config*进行配置。

1. 导航到 `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. 在打开应用服务文件的浏览器资源管理器中，导航到“site/wwwroot”。  如果 *Web.config* 不存在，请选择“+” > “新建文件”来创建该文件。   

1. 选择“Web.config”旁边的铅笔图标对其进行编辑。  添加以下配置代码，然后单击“保存”。  如果 *Web.config* 已存在，则只需在其中添加包含任何内容的 `<authorization>` 元素即可。 在 `<allow>` 元素中添加要允许的帐户。

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

- 对于 [Azure App Service](configure-authentication-provider-aad.md)，你可以直接在 Azure AD 中 [管理企业级访问权限](../active-directory/manage-apps/what-is-access-management.md) 。 有关说明，请参阅 [如何删除用户对应用程序的访问权限](../active-directory/manage-apps/methods-for-removing-user-access.md)。
- 对于 [google](configure-authentication-provider-google.md)，可以将属于 [组织](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) 的 Google API 项目配置为仅允许组织中的用户访问 (参阅 [Google **设置 OAuth 2.0** 支持页](https://support.google.com/cloud/answer/6158849?hl=en)) 。

### <a name="application-level"></a>应用程序级别

如果其他任何级别不提供所需的授权，或者平台或标识提供者不受支持，则必须编写自定义代码，以基于[用户声明](#access-user-claims)为用户授权。

## <a name="configure-using-a-file-preview"></a><a name="config-file"> </a>使用文件 (预览配置) 

你可以选择通过部署提供的文件来配置你的身份验证设置。 应用服务身份验证/授权的某些预览功能可能需要此功能。

> [!IMPORTANT]
> 请记住，你的应用程序负载（因此，此文件）可能在环境之间移动，与 [槽](./deploy-staging-slots.md)相同。 可能需要将不同的应用注册固定到每个槽，在这些情况下，应继续使用标准配置方法，而不是使用配置文件。

### <a name="enabling-file-based-configuration"></a>启用基于文件的配置

> [!CAUTION]
> 在预览期间，启用基于文件的配置将禁止通过某些客户端（例如 Azure 门户、Azure CLI 和 Azure PowerShell）管理应用程序的应用服务身份验证/授权功能。

1. 在项目根目录中为你的配置创建一个新的 JSON 文件， (部署到 web/函数应用) 中的 D:\home\site\wwwroot。 根据 [基于文件的配置参考](#configuration-file-reference)填写所需的配置。 如果修改现有的 Azure 资源管理器配置，请确保在配置文件中将集合中捕获的属性转换为 `authsettings` 。

2. 修改现有配置，该配置将在下的 [Azure 资源管理器](../azure-resource-manager/management/overview.md) api 中捕获 `Microsoft.Web/sites/<siteName>/config/authsettings` 。 若要进行修改，可以使用 [Azure 资源管理器模板](../azure-resource-manager/templates/overview.md) 或 [Azure 资源浏览器](https://resources.azure.com/)之类的工具。 在 authsettings 集合中，需要 (设置三个属性，并) 删除其他属性：

    1.  设置 `enabled` 为 "true"
    2.  设置 `isAuthFromFile` 为 "true"
    3.  设置 `authFilePath` 为文件的名称 (例如，"auth.json" ) 

> [!NOTE]
> `authFilePath`平台之间的格式不同。 在 Windows 上，支持相对路径和绝对路径。 建议使用相对路径。 对于 Linux，当前仅支持绝对路径，因此设置的值应为 "/home/site/wwwroot/auth.js" 或类似。

完成此配置更新后，该文件的内容将用于定义对该站点的应用服务身份验证/授权的行为。 如果希望返回到 Azure 资源管理器配置，可以通过将 `isAuthFromFile` 返回到 "false" 来执行此操作。

### <a name="configuration-file-reference"></a>配置文件参考

将从配置文件引用的任何机密都必须存储为 [应用程序设置](./configure-common.md#configure-app-settings)。 可以将设置命名为任何所需的名称。 只需确保配置文件中的引用使用相同的键。

以下用完文件中可能的配置选项：

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "requireAuthentication": <true|false>,
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|Return401|Return403",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "openIdConnectProviders": {
            "provider name": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "secretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scope": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        },
        "login": {
            "routes": {
                "logoutEndpoint": "<logout endpoint>"
            },
            "tokenStore": {
                "enabled": <true|false>,
                "tokenRefreshExtensionHours": "<double>",
                "fileSystem": {
                    "directory": "<directory to store the tokens in if using a file system token store (default)>"
                },
                "azureBlobStorage": {
                    "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
                }
            },
            "preserveUrlFragmentsForLogins": <true|false>,
            "allowedExternalRedirectUrls": [
                "https://uri1.azurewebsites.net/",
                "https://uri2.azurewebsites.net/"
            ],
            "cookieExpiration": {
                "convention": "FixedTime|IdentityProviderDerived",
                "timeToExpiration": "<timespan>"
            },
            "nonce": {
                "validateNonce": <true|false>,
                "nonceExpirationInterval": "<timespan>"
            }
        },
        "httpSettings": {
            "requireHttps": <true|false>,
            "routes": {
                "apiPrefix": "<api prefix>"
            },
            "forwardProxy": {
                "convention": "NoProxy|Standard|Custom",
                "customHostHeaderName": "<host header value>",
                "customProtoHeaderName": "<proto header value>"
            }
        }
    }
}
```

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>将应用固定到特定身份验证运行时版本

启用身份验证/授权时，会将平台中间件注入到 HTTP 请求管道中，如 [功能概述](overview-authentication-authorization.md#how-it-works)中所述。 此平台中间件会定期更新，作为常规平台更新的一部分。 默认情况下，web 或 function app 将在此平台中间件的最新版本上运行。 这些自动更新始终向后兼容。 但是，在此自动更新为 web 或函数应用引入了运行时问题的罕见情况下，你可以暂时回滚到以前的中间件版本。 本文介绍如何将应用临时固定到特定版本的身份验证中间件。

### <a name="automatic-and-manual-version-updates"></a>自动和手动版本更新 

可以通过设置应用的设置，将应用固定到平台中间件的特定版本 `runtimeVersion` 。 应用始终在最新版本上运行，除非你选择将其显式固定到特定版本。 一次将支持几个版本。 如果你锁定了不再受支持的无效版本，你的应用将改用最新版本。 若要始终运行最新版本，请将设置 `runtimeVersion` 为 ~ 1。 

### <a name="view-and-update-the-current-runtime-version"></a>查看和更新当前运行时版本

您可以更改应用程序使用的运行时版本。 新的运行时版本应在重新启动应用后生效。 

#### <a name="view-the-current-runtime-version"></a>查看当前运行时版本

你可以使用 Azure CLI 或在应用中通过 built0 版本的 HTTP 终结点之一查看平台身份验证中间件的当前版本。

##### <a name="from-the-azure-cli"></a>通过 Azure CLI

使用 Azure CLI，使用 [az webapp authentication show](https://docs.microsoft.com/cli/azure/webapp/auth?view=azure-cli-latest#az-webapp-auth-show) 命令查看当前中间件版本。

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

在此代码中， `<my_app_name>` 将替换为你的应用程序的名称。 同时，将替换为 `<my_resource_group>` 应用的资源组的名称。

你将看到 `runtimeVersion` CLI 输出中的字段。 它将类似于以下示例输出，为清楚起见，已将其截断： 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>从版本终结点

还可以在应用上点击/.auth/version 终结点，以查看应用运行所在的当前中间件版本。 它将类似于以下示例输出：
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>更新当前运行时版本

使用 Azure CLI，可以使用 `runtimeVersion` [az webapp authentication update](https://docs.microsoft.com/cli/azure/webapp/auth?view=azure-cli-latest#az-webapp-auth-update) 命令更新应用中的设置。

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

将替换 `<my_app_name>` 为您的应用程序的名称。 同时，将替换为 `<my_resource_group>` 应用的资源组的名称。 此外，将替换为版本为 1.x `<version>` 运行时的有效版本或 `~1` 最新版本。 你可以在不同的运行时版本 [此处] (中找到发行说明 https://github.com/Azure/app-service-announcements) ，以帮助确定要固定到的版本。

可以通过在前面代码示例中选择“试一试”**** 运行这个来自 [Azure Cloud Shell](../cloud-shell/overview.md) 的命令。 还可以在执行 [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login) 登录后使用 [Azure CLI 在本地](https://docs.microsoft.com/cli/azure/install-azure-cli)执行此命令。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：对用户进行端到端身份验证和授权](tutorial-auth-aad.md)
