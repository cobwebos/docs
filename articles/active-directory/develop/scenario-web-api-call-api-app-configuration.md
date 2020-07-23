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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 38e319efb100d326d55f6f821e7c903306a7c7d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80991001"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>调用 Web API 的 Web API：代码配置

注册 Web API 后，可以配置应用程序的代码。

用于配置 Web API 的代码，配置后它就可以调用下游 Web API，而后者又基于用来保护 Web API 的代码。 有关详细信息，请参阅[受保护的 Web API：应用配置](scenario-protected-web-api-app-configuration.md)。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>订阅 OnTokenValidated 的代码

在任何受保护的 Web API 的代码配置的基础上，需订阅持有者令牌的验证，该令牌是在调用 API 时接收的：

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

AddAccountToCacheFromJwt() 方法需要执行以下操作：

- 实例化 Microsoft 身份验证库 (MSAL) 机密客户端应用程序。
- 调用 `AcquireTokenOnBehalf` 方法。 此调用将通过 Web API 的客户端获取的持有者令牌与同一用户的持有者令牌进行交换，但它会让该 API 调用某个下游 API。

### <a name="instantiate-a-confidential-client-application"></a>实例化机密客户端应用程序

此流仅在机密客户端流中可用，使受保护的 Web API 通过 `WithClientSecret` 或 `WithCertificate` 方法将客户端凭据（客户端机密或证书）提供给 [ConfidentialClientApplicationBuilder 类](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)。

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

最后，机密客户端应用程序无需通过客户端机密或证书来提供自身的标识，而可以使用客户端断言来证明其身份。
有关此高级方案的详细信息，请参阅[机密客户端断言](msal-net-client-assertions.md)。

### <a name="how-to-call-on-behalf-of"></a>如何调用代理

可以通过对 `IConfidentialClientApplication` 接口调用 [AcquireTokenOnBehalf 方法](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder)来发出代理 (OBO) 调用。

`UserAssertion` 类是从持有者令牌生成的，该令牌是 Web API 从其自身的客户端接收的。 有[两个构造函数](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)：
* 一个构造函数采用 JSON Web 令牌 (JWT) 持有者令牌
* 一个构造函数采用任何类型的用户断言（另一种安全令牌，随后会在名为 `assertionType` 的附加参数中指定其类型）

![UserAssertion 属性和方法](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

在实践中，往往使用 OBO 流来获取下游 API 的令牌，并将其存储在 MSAL.NET 用户令牌缓存中。 这样做的目的是使 Web API 的其他部分随后可以调用 ``AcquireTokenOnSilent`` 的[重写](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet)来调用下游 API。 此调用的作用是根据需要刷新令牌。

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
