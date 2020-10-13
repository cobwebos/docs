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
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9fe4d5cfb4b0b31a257dfecdeeb191a9c7920772
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91403155"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>调用 Web API 的 Web API：代码配置

注册 Web API 后，可以配置应用程序的代码。

用于配置 Web API 的代码，配置后它就可以调用下游 Web API，而后者又基于用来保护 Web API 的代码。 有关详细信息，请参阅[受保护的 Web API：应用配置](scenario-protected-web-api-app-configuration.md)。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="microsoftidentityweb"></a>Web.config

Microsoft 建议你在开发 ASP.NET Core 受保护的 API 调用下游 web Api 时使用 [microsoft. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) NuGet 包。 请参阅 [受保护的 WEB API：代码配置 |](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) 用于在 WEB API 的上下文中快速显示该库的 web。

## <a name="client-secrets-or-client-certificates"></a>客户端密码或客户端证书

鉴于 Web API 现在调用了下游 Web API，你需要在 appsettings.json 文件中提供客户端密码或客户端证书。 你还可以添加一个指定以下内容的部分：

- 下游 web API 的 URL
- 调用 API 所需的范围

在下面的示例中， `GraphBeta` 节指定了这些设置。

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
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

你可以提供客户端证书，而不是客户端密码。 以下代码片段演示如何使用存储在 Azure Key Vault 中的证书。

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
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

Microsoft.Identity.Web 提供了多种通过配置或代码描述证书的方法。 有关详细信息，请参阅 GitHub 上的 [Microsoft.Identity.Web wiki - 使用证书](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates)。

## <a name="startupcs"></a>Startup.cs

Web API 需要获取下游 API 的令牌。 可以通过在后添加行来指定它 `.EnableTokenAcquisitionToCallDownstreamApi()` `.AddMicrosoftIdentityWebApi(Configuration)` 。 此行公开 `ITokenAcquisition` 服务，可在控制器/页面操作中使用。 不过，正如您将在接下来的两个项目符号点中看到的那样，您可以执行更简单的操作。 还需要选择一个令牌缓存实现，例如 `.AddInMemoryTokenCaches()` ，在 *Startup.cs*中：

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

如果你不想亲自获取令牌，则 *Microsoft* 将提供两种机制用于从另一个 api 调用下游 Web API。 选择的选项取决于您是要调用 Microsoft Graph 还是调用另一个 API。

### <a name="option-1-call-microsoft-graph"></a>选项1：调用 Microsoft Graph

如果要调用 Microsoft Graph，则可以 `GraphServiceClient` 在 API 操作中直接使用由 MICROSOFT GRAPH SDK) 公开的 (。 公开 Microsoft Graph：

1. 将 [Microsoft.azure.webjobs.extensions.microsoftgraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) NuGet 包添加到项目。
1. `.AddMicrosoftGraph()` `.EnableTokenAcquisitionToCallDownstreamApi()` 在*Startup.cs*文件中添加 after。 `.AddMicrosoftGraph()` 具有多个重写。 使用将配置节作为参数的替代，代码变为：

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>选项2：调用下游 web API，而不是 Microsoft Graph

若要调用 Microsoft Graph 以外的下游 *API，* `.AddDownstreamWebApi()` 请提供，它会请求令牌并调用下游 Web API。

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

与 Web 应用一样，你可以选择各种令牌缓存实现。 有关详细信息，请参阅 GitHub 上的 [Microsoft 标识 Web 令牌缓存序列化](https://aka.ms/ms-id-web/token-cache-serialization) 。

下图显示了*Startup.cs* *的各种可能性及其对*文件的影响：

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="显示启动点 C 中的服务配置选项的框图，用于调用 web API 并指定令牌缓存实现":::

> [!NOTE]
> 若要完全理解本文中的代码示例，需要熟悉 [ASP.NET Core 基础知识](/aspnet/core/fundamentals)，尤其是[依赖关系注入](/aspnet/core/fundamentals/dependency-injection)和[选项](/aspnet/core/fundamentals/configuration/options)。

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

有关 OBO 协议的详细信息，请参阅 [Microsoft 标识平台和 OAuth 2.0 代理流](./v2-oauth2-on-behalf-of-flow.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [调用 Web API 的 Web API：获取应用的令牌](scenario-web-api-call-api-acquire-token.md)
