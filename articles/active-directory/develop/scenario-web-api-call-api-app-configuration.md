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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2082265b96388b4fbf860118efc3eefd4c5c67af
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423593"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>用于调用 web Api 的 web API-代码配置

注册 web API 后，可以为该应用程序配置代码。

用于配置 web API 的代码，使其调用下游 web Api 基于用于保护 web API 的代码构建。 有关详细信息，请参阅[受保护的 WEB API-应用配置](scenario-protected-web-api-app-configuration.md)。

## <a name="code-subscribed-to-ontokenvalidated"></a>已订阅 OnTokenValidated 的代码

在任何受保护的 web Api 的代码配置的基础上，你需要订阅在调用 API 时收到的持有者令牌的验证：

```csharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform (a.k.k AAD v2.0)
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">Service collection to which to add authentication</param>
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
        // to MSAL.NET's cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache, and also handles the incremental consent 
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

- 实例化 MSAL 的机密客户端应用程序。
- 对于同一用户的持有者令牌，调用 `AcquireTokenOnBehalf` 来交换客户端为 web API 获取的持有者令牌，但对于我们的 API 调用下游 API。

### <a name="instantiate-a-confidential-client-application"></a>实例化机密客户端应用程序

此流仅在机密客户端流中可用，因此受保护的 web API 会分别通过 `WithClientSecret` 或 `WithCertificate` 方法将客户端凭据（客户端机密或证书）提供给[ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) 。

![image](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

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

最后，机密客户端应用程序还可以使用客户端断言来证明其身份，而不是客户端机密或证书。
[客户端断言](msal-net-client-assertions.md)详细介绍了这一高级方案

### <a name="how-to-call-on-behalf-of"></a>如何代表调用

通过在 `IConfidentialClientApplication` 接口上调用[AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder)方法来执行代表（OBO）调用。

该 `UserAssertion` 是从 web API 从其自己的客户端接收的持有者令牌生成的。 有[两个构造函数](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)，一个用于采用 JWT 持有者令牌，另一个采用任何类型的用户断言（另一种安全令牌，然后在名为 `assertionType`的附加参数中指定）。

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

在实践中，OBO 流通常用于获取下游 API 的令牌并将其存储在 MSAL.NET 用户令牌缓存中，以便 web API 的其他部分以后可以调用 ``AcquireTokenOnSilent`` 的[替代](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet)来调用下游 api。 如果需要，此调用会对标记进行刷新。

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

        // .Result to make sure that the cache is filled-in before the controller tries to get access tokens
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

你还可以在[NodeJS 和 Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61)中代表流实现的示例。

## <a name="protocol"></a>协议

有关代表协议的详细信息，请参阅[Microsoft 标识平台和 OAuth 2.0 代理流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [正在获取应用的令牌](scenario-web-api-call-api-acquire-token.md)
