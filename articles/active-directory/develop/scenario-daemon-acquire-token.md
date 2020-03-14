---
title: 获取用于调用 web API （守护程序应用）的令牌-Microsoft 标识平台 |Microsoft
description: 了解如何生成可调用 web Api 的后台应用程序（获取令牌）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7f1010949a72f95ef2836c43666e6cea9281e04d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262641"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>用于调用 web Api 的守护程序应用-获取令牌

构造机密客户端应用程序后，可以通过调用 `AcquireTokenForClient`，传递范围，并选择性地强制刷新令牌来获取应用的令牌。

## <a name="scopes-to-request"></a>要请求的范围

请求客户端凭据流的范围是资源的名称，后跟 `/.default`。 此表示法指示 Azure Active Directory （Azure AD）使用在应用程序注册过程中以静态方式声明的*应用程序级权限*。 此外，这些 API 权限必须由租户管理员授予。

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="python"></a>[Python](#tab/python)

在 MSAL Python 中，配置文件如以下代码片段所示：

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="azure-ad-v10-resources"></a>Azure AD （1.0）资源

用于客户端凭据的范围应始终为资源 ID，后跟 `/.default`。

> [!IMPORTANT]
> 当 MSAL 请求接受版本1.0 访问令牌的资源的访问令牌时，Azure AD 会通过使用最后一个斜杠之前的所有内容并将其用作资源标识符，从请求的作用域分析所需的受众。
> 因此，如 Azure SQL Database （**https：\//database.windows.net**），资源期望以斜杠结尾的受众（对于 Azure SQL 数据库，`https://database.windows.net/`），需要请求 `https://database.windows.net//.default`范围。 （请注意双斜杠。）另请参阅 MSAL.NET 问题[#747：省略了资源 url 的尾随斜杠，这导致了 sql 身份验证失败](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)。

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

若要获取应用程序的令牌，你将使用 `AcquireTokenForClient` 或其等效的，具体取决于平台。

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

# <a name="python"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

# <a name="java"></a>[Java](#tab/java)

此代码从[MSAL Java dev 示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)中提取。

```Java
private static IAuthenticationResult acquireToken() throws Exception {

     // Load token cache from file and initialize token cache aspect. The token cache will have
     // dummy data, so the acquireTokenSilently call will fail.
     TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

     IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
     ConfidentialClientApplication cca =
             ConfidentialClientApplication
                     .builder(CLIENT_ID, credential)
                     .authority(AUTHORITY)
                     .setTokenCacheAccessAspect(tokenCacheAspect)
                     .build();

     IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
 }
```

---

### <a name="protocol"></a>协议

如果你还没有用于所选语言的库，你可能想要直接使用该协议：

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>第一种情况：使用共享机密访问令牌请求

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>第二种情况：通过使用证书访问令牌请求

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

有关详细信息，请参阅协议文档： [Microsoft 标识平台和 OAuth 2.0 客户端凭据流](v2-oauth2-client-creds-grant-flow.md)。

## <a name="application-token-cache"></a>应用程序令牌缓存

在 MSAL.NET 中，`AcquireTokenForClient` 使用应用程序令牌缓存。 （其他所有 AcquireToken*XX*方法都使用用户令牌缓存。）调用 `AcquireTokenForClient`之前，请勿调用 `AcquireTokenSilent`，因为 `AcquireTokenSilent` 使用*用户*令牌缓存。 `AcquireTokenForClient` 检查*应用程序*令牌缓存本身并对其进行更新。

## <a name="troubleshooting"></a>故障排除

### <a name="did-you-use-the-resourcedefault-scope"></a>是否使用了资源/. 默认范围？

如果你收到一条错误消息，指出你使用了无效的范围，则你可能没有使用 `resource/.default` 范围。

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>您是否忘记了提供管理员同意？ 后台应用程序需要！

如果在调用 API 时获得的**权限不足，无法完成操作**错误，租户管理员需要向应用程序授予权限。 请参阅上面的注册客户端应用程序的步骤6。
通常会看到如下错误：

```JSon
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>后续步骤

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [后台应用程序-调用 web API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [后台应用程序-调用 web API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [后台应用程序-调用 web API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
