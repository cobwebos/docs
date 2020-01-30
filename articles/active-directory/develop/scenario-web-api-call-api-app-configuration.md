---
title: 配置用于调用 web Api 的 web API |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何生成可调用 web Api 的 web API （应用程序的代码配置）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 82b5e1d9753fbb65fd81f24b06016d302457144e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834087"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>用于调用 web Api 的 web API：代码配置

注册 web API 后，可以为该应用程序配置代码。

用于配置 web API 的代码，以使其调用下游 web Api 基于用于保护 web API 的代码构建。 有关详细信息，请参阅[受保护的 WEB API：应用配置](scenario-protected-web-api-app-configuration.md)。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>已订阅 OnTokenValidated 的代码

在任何受保护的 web Api 的代码配置的基础上，你需要订阅在调用 API 时收到的持有者令牌的验证：

```csharp
/// <summary>
/// Protects the web API with the Microsoft identity platform, or Azure Active Directory (Azure AD) developer platform
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">The service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it
        // to the MSAL.NET cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache and handles the incremental consent
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>代理流

AddAccountToCacheFromJwt （）方法需要：

- 实例化 Microsoft 身份验证库（MSAL）的机密客户端应用程序。
- 调用 `AcquireTokenOnBehalf` 方法。 此调用会根据相同用户的持有者令牌，与 web API 获取的持有者令牌进行交换，但它具有 API 调用下游 API。

### <a name="instantiate-a-confidential-client-application"></a>实例化机密客户端应用程序

此流仅在机密客户端流中提供，因此受保护的 web API 通过 `WithClientSecret` 或 `WithCertificate` 方法向[ConfidentialClientApplicationBuilder 类](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)提供客户端凭据（客户端机密或证书）。

![IConfidentialClientApplication 方法列表](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```csharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

最后，机密客户端应用程序可以使用客户端断言来证明其身份，而不是通过客户端机密或证书证明其身份。
有关此高级方案的详细信息，请参阅[机密客户端断言](msal-net-client-assertions.md)。

### <a name="how-to-call-on-behalf-of"></a>如何代表调用

通过在 `IConfidentialClientApplication` 接口上调用[AcquireTokenOnBehalf 方法](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder)，可以进行代表（OBO）调用。

`UserAssertion` 类是从 web API 从其自己的客户端接收的持有者令牌生成的。 有[两个构造函数](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)：
* 一个获取 JSON Web 令牌（JWT）持有者令牌
* 一个采用任何类型的用户断言，另一种安全令牌，然后在名为 `assertionType` 的附加参数中指定该类型的安全令牌。

![UserAssertion 属性和方法](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

实际上，OBO 流通常用于获取下游 API 的令牌并将其存储在 MSAL.NET 用户令牌缓存中。 这样做是为了使 web API 的其他部分以后可以调用 ``AcquireTokenOnSilent`` 的[替代](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet)来调用下游 api。 如果需要，此调用会对标记进行刷新。

```csharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```
# <a name="javatabjava"></a>[Java](#tab/java)

代表（OBO）流用于获取用于调用下游 web API 的令牌。 在此流中，你的 web API 从客户端应用程序接收具有用户委托权限的持有者令牌，然后交换另一个访问令牌的此令牌以调用下游 web API。

下面的代码使用 web API 中的弹簧 Security framework `SecurityContextHolder` 来获取经验证的持有者令牌。 然后，它使用 MSAL Java 库通过 `OnBehalfOfParameters``acquireToken` 调用来获取下游 API 的令牌。 MSAL 缓存令牌，以便对 API 的后续调用可以使用 `acquireTokenSilently` 获取缓存的令牌。

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

# <a name="pythontabpython"></a>[Python](#tab/python)

代表（OBO）流用于获取用于调用下游 web API 的令牌。 在此流中，你的 web API 从客户端应用程序接收具有用户委托权限的持有者令牌，然后交换另一个访问令牌的此令牌以调用下游 web API。

Python web API 需要使用一些中间件来验证从客户端接收的持有者令牌。 然后，web API 可以通过调用[`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of)方法，使用 MSAL Python 库获取下游 API 的访问令牌。 使用 MSAL Python 演示此流的示例尚不可用。

---

你还可以在[node.js 和 Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61)中查看 OBO flow 实现的示例。

## <a name="protocol"></a>协议

有关 OBO 协议的详细信息，请参阅[Microsoft 标识平台和 OAuth 2.0 代理流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [用于调用 web Api 的 web API：获取应用的令牌](scenario-web-api-call-api-acquire-token.md)
