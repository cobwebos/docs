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
ms.openlocfilehash: 38df99f0a4932f477e900382c7ff1ae7b50febe9
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702464"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>用于调用 web Api 的守护程序应用-获取令牌

构造机密客户端应用程序后，可以通过调用 ``AcquireTokenForClient``、传递范围并强制或不刷新令牌来获取应用程序的令牌。

## <a name="scopes-to-request"></a>要请求的范围

请求客户端凭据流的范围是资源的名称，后跟 `/.default`。 此表示法告诉 Azure AD 使用在应用程序注册过程中以静态方式声明的**应用程序级权限**。 另外，如前文所述，必须由租户管理员授予这些 API 权限

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

在 MSAL Python 中，配置文件将类似于以下代码片段：

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="case-of-azure-ad-v10-resources"></a>Azure AD （1.0）资源的情况

用于客户端凭据的作用域应始终为 resourceId + "/.default"

> [!IMPORTANT]
> 若要让 MSAL 对接受 v1.0 访问令牌的资源进行访问令牌，Azure AD 通过将所有内容放在最后一个斜杠之前，并将其用作资源标识符，来分析请求范围内的所需访问群体。
> 因此，如 Azure SQL （ **https://database.windows.net** ）资源需要以斜杠（对于 Azure SQL： `https://database.windows.net/` ）结尾的受众，则需要请求 `https://database.windows.net//.default` 范围（请注意双斜杠）。 另请参阅 MSAL.NET 问题[#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)：省略了资源 url 的尾随斜杠，这导致了 sql 身份验证失败。

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

若要获取应用程序的令牌，你将使用 `AcquireTokenForClient` 或等效项（具体取决于平台）。

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows the scopes is ALWAYS of the shape "resource/.default", as the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application does not have sufficient permissions
    // - did you declare enough app permissions in during the app creation?
    // - did the tenant admin needs to grant permissions to the application.
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be of the form "https://resourceurl/.default"
    // Mitigation: change the scope to be as expected !
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You may need this when reporting a bug
```

# <a name="javatabjava"></a>[Java](#tab/java)

这是[MSAL Java dev 示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)的摘录。

```Java
ClientCredentialParameters clientCredentialParam = ClientCredentialParameters.builder(
        Collections.singleton(GRAPH_DEFAULT_SCOPE))
        .build();

CompletableFuture<IAuthenticationResult> future = app.acquireToken(clientCredentialParam);

BiConsumer<IAuthenticationResult, Throwable> processAuthResult = (res, ex) -> {
    if (ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
    }
    System.out.println("Returned ok - " + res);
    System.out.println("ID Token - " + res.idToken());

    /* call a protected API with res.accessToken() */
};

future.whenCompleteAsync(processAuthResult);
future.join();
```

---

### <a name="protocol"></a>协议

如果你还没有用于所选语言的库，则可能想要直接使用该协议：

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>第一种情况：使用共享密钥访问令牌请求

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>第二种情况：使用证书访问令牌请求

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
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

在 MSAL.NET 中，`AcquireTokenForClient` 使用**应用程序令牌缓存**（所有其他 AcquireTokenXX 方法使用用户令牌缓存）不要在调用 `AcquireTokenForClient` 之前调用 `AcquireTokenSilent`，`AcquireTokenSilent` 使用**用户**令牌缓存。 `AcquireTokenForClient` 检查**应用程序**令牌缓存本身并对其进行更新。

## <a name="troubleshooting"></a>故障排除

### <a name="did-you-use-the-resourcedefault-scope"></a>是否使用了资源/. 默认范围？

如果你收到一条错误消息，指出你使用了无效的范围，则你可能没有使用 `resource/.default` 范围。

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>您是否忘记了提供管理员同意？ 后台应用程序需要！

如果在调用 API 时遇到错误，**无法完成该操作**，租户管理员需要向应用程序授予权限。 请参阅上面的注册客户端应用程序的步骤6。
通常会看到和错误，如以下错误说明所示：

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

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [后台应用程序-调用 web API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [后台应用程序-调用 web API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [后台应用程序-调用 web API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
