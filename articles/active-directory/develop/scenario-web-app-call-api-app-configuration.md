---
title: 调用 Web API 的 Web 应用（代码配置）- Microsoft 标识平台
description: 了解如何生成调用 Web API 的 Web 应用（应用的代码配置）
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c78a951258e3c279f96f44ceac469e4c38cf22c
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785562"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>调用 Web API 的 Web 应用 - 代码配置

如 [Web 应用登录用户方案](scenario-web-app-sign-user-overview.md)中所述，假设正在登录的用户已委托到 Open ID Connect (OIDC) 中间件，而你想要挂接到 OIDC 进程。 实现此目的的方式根据所用的框架（此处使用 ASP.NET 和 ASP.NET Core）而有所不同，但最终，你都要订阅中间件 OIDC 事件。 原则是：

- 让 ASP.NET 或 ASP.NET Core 请求授权代码。 这样，ASP.NET/ASP.NET Core 就会让用户登录并提供许可。
- 订阅 Web 应用接收的授权代码。
- 收到身份验证代码后，使用 MSAL 库兑换该代码，生成的访问令牌和刷新令牌将存储在令牌缓存中。 此处，可在应用程序的其他组成部分使用缓存来以静默方式获取其他令牌。

## <a name="libraries-supporting-web-app-scenarios"></a>支持 Web 应用方案的库

支持 Web 应用授权代码流的库包括：

| MSAL 库 | 描述 |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 支持的平台包括 .NET Framework 和 .NET Core 平台（不包括 UWP、Xamarin.iOS 和 Xamarin.Android，因为这些平台用于生成公共客户端应用程序） |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | 开发中 -目前为公共预览版 |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | 开发中 -目前为公共预览版 |

## <a name="aspnet-core-configuration"></a>ASP.NET Core 配置

ASP.NET Core 中的配置在 `Startup.cs` 文件中发生。 需要订阅 `OnAuthorizationCodeReceived` Open ID Connect 事件，并通过此事件调用 MSAL.NET 的方法 `AcquireTokenFromAuthorizationCode`，这会影响到令牌缓存中的存储行为、所请求范围的访问令牌、在访问令牌即将过期时用于刷新访问令牌的刷新令牌，或者代表相同用户获取令牌（但为不同资源的令牌）的刷新令牌。

下面的代码中的注释将帮助你了解编辑 MSAL.NET 和 ASP.NET Core 的某些困难方面。 中提供了完整的详细信息[ASP.NET Core Web 应用增量教程，第 2 章](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add(OidcConstants.ScopeOfflineAccess);
   options.Scope.Add("user.read"); // for instance

   // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
   // where it will be usable from Controllers later (through the TokenAcquisition service)
   var handler = options.Events.OnAuthorizationCodeReceived;
   options.Events.OnAuthorizationCodeReceived = async context =>
   {
    // As AcquireTokenByAuthorizationCode is asynchronous we want to tell ASP.NET core
    // that we are handing the code even if it's not done yet, so that it does 
    // not concurrently call the Token endpoint.
    context.HandleCodeRedemption();

    // Call MSAL.NET AcquireTokenByAuthorizationCode
    var application = BuildConfidentialClientApplication(context.HttpContext,
                                                         context.Principal);
    var result = await application.AcquireTokenByAuthorizationCode(scopes.Except(scopesRequestedByMsalNet), context.ProtocolMessage.Code)
                                  .ExecuteAsync();

    // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it
    // and will not send the OAuth 2.0 request in case a further call to
    // AcquireTokenByAuthorizationCodeAsync in the future for incremental consent 
    // (getting a code requesting more scopes)
    // Share the ID Token so that the identity of the user is known in the application (in 
    // HttpContext.User)
    context.HandleCodeRedemption(null, result.IdToken);

    // Call the previous handler if any
    await handler(context);
   };
```

在 ASP.NET Core 中，生成机密客户端应用程序会用到 HttpContext 中的信息。 此 HttpContext 了解 Web 应用的 URL 和已登录的用户（在 `ClaimsPrincipal` 中）。 它还使用 ASP.NET Core 配置，该配置包含已绑定到 `_applicationOptions` 数据结构的“AzureAD”节。 最后，应用程序需要维护令牌缓存。

```CSharp
/// <summary>
/// Creates an MSAL Confidential client application
/// </summary>
/// <param name="httpContext">HttpContext associated with the OIDC response</param>
/// <param name="claimsPrincipal">Identity for the signed-in user</param>
/// <returns></returns>
private IConfidentialClientApplication BuildConfidentialClientApplication(HttpContext httpContext, ClaimsPrincipal claimsPrincipal)
{
 var request = httpContext.Request;

 // Find the URI of the application)
 string currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, azureAdOptions.CallbackPath ?? string.Empty);

 // Updates the authority from the instance (including national clouds) and the tenant
 string authority = $"{azureAdOptions.Instance}{azureAdOptions.TenantId}/";

 // Instantiates the application based on the application options (including the client secret)
 var app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
               .WithRedirectUri(currentUri)
               .WithAuthority(authority)
               .Build();

 // Initialize token cache providers. In the case of Web applications, there must be one
 // token cache per user (here the key of the token cache is in the claimsPrincipal which
 // contains the identity of the signed-in user)
 if (this.UserTokenCacheProvider != null)
 {
  this.UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (this.AppTokenCacheProvider != null)
 {
  this.AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

`AcquireTokenByAuthorizationCode` 真正兑换 ASP.NET 请求的授权代码，并获取已添加到 MSAL.NET 用户令牌缓存的令牌。 然后在 ASP.NET Core 控制器中使用这些令牌。

## <a name="aspnet-configuration"></a>ASP.NET 配置

ASP.NET 的处理方式类似，不同之处在于 OpenIdConnect 的配置，并且 `OnAuthorizationCodeReceived` 事件订阅是在 `App_Start\Startup.Auth.cs` 文件中发生的。 你会发现概念也是类似的，不过，此处需要在配置文件中指定 RedirectUri，这是一个相对不太可靠的设置：

```CSharp
private void ConfigureAuth(IAppBuilder app)
{
 app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

 app.UseCookieAuthentication(new CookieAuthenticationOptions { });

 app.UseOpenIdConnectAuthentication(
 new OpenIdConnectAuthenticationOptions
 {
  Authority = Globals.Authority,
  ClientId = Globals.ClientId,
  RedirectUri = Globals.RedirectUri,
  PostLogoutRedirectUri = Globals.RedirectUri,
  Scope = Globals.BasicSignInScopes, // a basic set of permissions for user sign in & profile access
  TokenValidationParameters = new TokenValidationParameters
  {
   NameClaimType = "name",
  },
  Notifications = new OpenIdConnectAuthenticationNotifications()
  {
   AuthorizationCodeReceived = OnAuthorizationCodeReceived,
  }
 });
}
```

```CSharp
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
{
 // Upon successful sign in, get & cache a token using MSAL
 string userId = context.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
 IConfidentialClientApplication clientapp;
 clientApp = ConfidentialClientApplicationBuilder.Create(Globals.ClientId)
                  .WithClientSecret(Globals.ClientSecret)
                  .WithRedirectUri(Globals.RedirectUri)
                  .WithAuthority(new Uri(Globals.Authority))
                  .Build();

  EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);

 AuthenticationResult result = await clientapp.AcquireTokenByAuthorizationCode(scopes, context.Code)
                                              .ExecuteAsync();
}
```

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>ASP.NET (Core) Web 应用的 MSAL.NET 令牌缓存

在 Web 应用（实际上是 Web API）中，令牌缓存实现不同于桌面应用程序令牌缓存实现（通常[基于文件](scenario-desktop-acquire-token.md#file-based-token-cache)）。 它可以使用 ASP.NET/ASP.NET Core 会话、Redis 缓存、数据库，甚至 Azure Blob 存储。 在上面的代码片段中，这是绑定缓存服务的 `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` 方法调用的对象。 本方案指南不会介绍相关的详细信息，但提供了链接。

> [!IMPORTANT]
> 对于 Web 应用和 Web API，要认识到的一个要点是，每个用户（每个帐户）应有一个令牌缓存。 需要为每个帐户序列化令牌缓存。

[ASP.NET Core Web 应用教程](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)的阶段 [2-2 令牌缓存](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)中提供了有关如何对 Web 应用和 Web API 使用令牌缓存的示例。 有关实现，请查看 [microsoft-authentication-extensions-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) 库中的以下 [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) 文件夹（在 [Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) 文件夹中）。

## <a name="next-steps"></a>后续步骤

现在，当用户登录时，会在令牌缓存中存储一个令牌。 让我们看看随后该令牌如何在 Web 应用的其他组成部分中使用。

> [!div class="nextstepaction"]
> [登录到 Web 应用](scenario-web-app-call-api-sign-in.md)
