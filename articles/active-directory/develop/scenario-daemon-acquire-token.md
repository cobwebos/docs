---
title: 获取用于调用 Web API（守护程序应用）的令牌 - Microsoft 标识平台 | Azure
description: 了解如何构建调用 Web API 的守护程序应用（获取令牌）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d755573b53eb63d85165fb73fe4b97298dbeff09
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "81869000"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>用于调用 Web API 的守护程序应用 - 获取令牌

在构建机密客户端应用程序以后，可以获取应用的令牌，方法是调用 `AcquireTokenForClient`，传递作用域，然后强制刷新令牌。

## <a name="scopes-to-request"></a>请求的作用域

请求客户端凭据流时，其作用域是资源的名称后跟 `/.default`。 此表示法告知 Azure Active Directory (Azure AD) 使用在应用程序注册过程中静态声明的*应用程序级权限*。 另外，这些 API 权限必须由租户管理员授予。

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="python"></a>[Python](#tab/python)

在 MSAL Python 中，该配置文件应该如以下代码片段所示：

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

### <a name="azure-ad-v10-resources"></a>Azure AD (v1.0) 资源

用于客户端凭据的作用域应该始终为资源 ID 后跟 `/.default`。

> [!IMPORTANT]
> 当 MSAL 向接受 1.0 版访问令牌的资源请求访问令牌时，Azure AD 将获取最后一个斜杠前面的所有内容并将其用作资源标识符，从请求的范围内分析所需的受众。
> 因此，如 Azure SQL Database （**https： \/ /database.windows.net**），资源期望以斜杠（对于 Azure sql 数据库，）结尾的受众 `https://database.windows.net/` ，需要请求的作用域 `https://database.windows.net//.default` 。 （请注意双斜杠。）另请参阅 MSAL.NET 问题 [#747：将省略资源 URL 的尾部斜杠，因为该斜杠会导致 SQL 身份验证失败](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)。

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

为了获取应用的令牌，你将使用 `AcquireTokenForClient` 或其等效命令，具体取决于平台。

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

此代码摘自 [MSAL Java 开发示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)。

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

如果还没有适用于所选语言的库，你可能希望直接使用协议：

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>第一种情况：使用共享机密访问令牌请求

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>第二种情况：使用证书访问令牌请求

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

有关详细信息，请参阅协议文档：[Microsoft 标识平台和 OAuth 2.0 客户端凭据流](v2-oauth2-client-creds-grant-flow.md)。

## <a name="application-token-cache"></a>应用程序令牌缓存

在 MSAL.NET 中，`AcquireTokenForClient` 使用应用程序令牌缓存。 （所有其他 AcquireToken*XX* 方法都使用用户令牌缓存。）不要在调用 `AcquireTokenForClient` 之前调用 `AcquireTokenSilent`，因为 `AcquireTokenSilent` 使用“用户”  令牌缓存。 `AcquireTokenForClient` 会检查*应用程序*令牌缓存本身并对其进行更新。

## <a name="troubleshooting"></a>故障排除

### <a name="did-you-use-the-resourcedefault-scope"></a>你是否使用过 resource/.default 作用域？

如果出现一条错误消息，指出所使用的作用域无效，则表明你并未使用 `resource/.default` 作用域。

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>你是否忘记提供管理员许可？ 守护程序应用需要它！

如果在调用 API 时出现错误“权限不足，无法完成该操作”，  则租户管理员需要授予对应用程序的权限。 请查看上面的步骤 6：注册客户端应用。
通常会看到如下错误：

```json
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
> [守护程序应用 - 调用 Web API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [守护程序应用 - 调用 Web API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [守护程序应用 - 调用 Web API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
