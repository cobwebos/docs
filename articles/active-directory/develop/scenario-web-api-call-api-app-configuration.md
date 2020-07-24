---
title: 配置调用 Web API 的 Web 应用 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何构建调用 Web API 的 Web API（应用的代码配置）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: eff5f68569d1878e1b802f2db4151d246bcc07c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026418"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>调用 Web API 的 Web API：代码配置

注册 Web API 后，可以配置应用程序的代码。

用于配置 Web API 的代码，配置后它就可以调用下游 Web API，而后者又基于用来保护 Web API 的代码。 有关详细信息，请参阅[受保护的 Web API：应用配置](scenario-protected-web-api-app-configuration.md)。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>客户端密码或客户端证书

假设你的 web API 现在调用下游 web API，则需要在*appsettings.js*的文件中提供客户端密码或客户端证书。

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"
  
   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 }
}
```

你可以提供客户端证书，而不是客户端密钥。 以下代码片段演示如何使用存储在 Azure Key Vault 中的证书。

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"
  
   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
  ]
 }
}
```

Microsoft 提供了多种方法来通过配置或代码描述证书。 有关详细信息，请参阅 GitHub 上的[Microsoft 使用证书](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates)。

## <a name="startupcs"></a>Startup.cs

使用 Startup.cs，如果你希望 Web API 调用下游 web Api，请在 `.AddMicrosoftWebApiCallsWebApi()` 后面添加行 `.AddMicrosoftWebApiAuthentication(Configuration)` ，然后选择令牌缓存实现，例如 `.AddInMemoryTokenCaches()` ，在*Startup.cs*中：

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  ...
  public void ConfigureServices(IServiceCollection services)
  {
   // ...
   services.AddMicrosoftWebApiAuthentication(Configuration)
           .AddMicrosoftWebApiCallsWebApi()
           .AddInMemoryTokenCaches();
  // ...
  }
  // ...
}
```

对于 web 应用，你可以选择各种令牌缓存实现。 有关详细信息，请参阅 GitHub 上的[Microsoft 标识 web wiki-令牌缓存序列化](https://aka.ms/ms-id-web/token-cache-serialization)。

如果确定 web API 需要特定范围，可以选择将其作为参数传递给 `AddMicrosoftWebApiCallsWebApi` 。

# <a name="java"></a>[Java](#tab/java)

代理 (OBO) 流用于获取所需的令牌来调用下游 Web API。 在此流中，Web API 从客户端应用程序接收拥有用户委托权限的持有者令牌，然后用此令牌交换另一个访问令牌来调用下游 Web API。

以下代码在 Web API 中使用 Spring Security 框架的 `SecurityContextHolder` 来获取已验证的持有者令牌。 然后，它通过 MSAL Java 库结合 `OnBehalfOfParameters` 使用 `acquireToken` 调用来获取下游 API 的令牌。 MSAL 将缓存令牌，使后续的 API 调用可以使用 `acquireTokenSilently` 获取缓存的令牌。

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

代理 (OBO) 流用于获取所需的令牌来调用下游 Web API。 在此流中，Web API 从客户端应用程序接收拥有用户委托权限的持有者令牌，然后用此令牌交换另一个访问令牌来调用下游 Web API。

Python Web API 需要使用一些中间件来验证从客户端接收的持有者令牌。 然后，Web API 可以通过调用 [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) 方法，使用 MSAL Python 库获取下游 API 的访问令牌。 有关使用此 API 的示例，请参阅 [GitHub 上 microsoft-authentication-library-for-python 的测试代码](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472)。 另请参阅在同一存储库中对[问题 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) 的讨论，以了解可以避免使用中间层应用程序的方法。

---

也可以参阅 [Node.js 和 Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61) 中的 OBO 流实现示例。

## <a name="protocol"></a>协议

有关 OBO 协议的详细信息，请参阅 [Microsoft 标识平台和 OAuth 2.0 代理流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [调用 Web API 的 Web API：获取应用的令牌](scenario-web-api-call-api-acquire-token.md)
