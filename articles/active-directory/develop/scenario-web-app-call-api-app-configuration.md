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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 231ecdb6afae1fc36d11b2c12aa82c7e860bb708
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175312"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>调用 Web API 的 Web 应用 - 代码配置

如[web 应用登录用户方案](scenario-web-app-sign-user-overview.md)中所示，web 应用使用 oauth 2.0[授权代码流](v2-oauth2-auth-code-flow.md)来登录用户。 此流分为两部分：

1. 请求授权代码。 此部分委托给 Microsoft 标识平台，其中包含用户的专用对话框。 用户登录并同意使用 web Api。 当此私有对话框成功结束时，应用程序会在其重定向 URI 上收到授权代码。
1. 通过兑换授权代码来请求 API 的访问令牌。

[Web 应用登录用户方案](scenario-web-app-sign-user-overview.md)仅执行第一个阶段。 在此处了解如何修改登录用户的 web API，使其现在调用 web API。

## <a name="libraries-supporting-web-app-scenarios"></a>支持 Web 应用方案的库

支持 web 应用的授权代码流的库有：

| MSAL 库 | 说明 |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 支持的平台包括 .NET Framework 和 .NET Core 平台（不包括 UWP、Xamarin.iOS 和 Xamarin.Android，因为这些平台用于生成公共客户端应用程序） |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | 开发中 -目前为公共预览版 |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | 开发中 -目前为公共预览版 |

选择与你感兴趣的平台相对应的选项卡：

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

假设允许用户登录被委派到 Open ID connect （OIDC）中间件，则需要在 OIDC 进程中挂接。 执行此操作的方式因所使用的框架而异。
在 ASP.NET Core 的情况下，你将订阅中间件 OIDC 事件。 原则是：

- 通过 Open ID connect 中间件，让 ASP.NET core 请求授权代码。 这样，ASP.NET/ASP.NET Core 就会让用户登录并提供许可。
- 订阅 Web 应用接收的授权代码。 此订阅是通过委托来C#完成的。
- 接收到身份验证代码后，你将使用 MSAL 库来兑换代码，并将生成的访问令牌和刷新令牌存储在令牌缓存中。 在这里，可以在应用程序的其他部分（例如在控制器中）使用缓存，以无提示方式获取其他令牌。

本文中的代码片段和以下文章摘自[ASP.NET Core Web 应用增量教程第2章](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)。 有关完整的实现的详细信息，请参阅本教程。

> [!NOTE]
> 若要完全了解下面的代码片段，需要熟悉[ASP.NET Core 基础知识](https://docs.microsoft.com/aspnet/core/fundamentals)，并在特定的[依赖关系注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)和[选项](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)中

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

假设允许用户登录被委派到 Open ID connect （OIDC）中间件，则需要在 OIDC 进程中挂接。 执行此操作的方式因所使用的框架而异。
对于 ASP.NET，你将订阅中间件 OIDC 事件。 原则是：

- 通过 Open ID connect 中间件，让 ASP.NET core 请求授权代码。 这样，ASP.NET/ASP.NET Core 就会让用户登录并提供许可。
- 订阅 Web 应用接收的授权代码。 这是一个C#委托。
- 接收到身份验证代码后，你将使用 MSAL 库来兑换代码。 然后，将生成的访问令牌和刷新令牌存储在令牌缓存中。 在这里，可以在应用程序的其他部分（例如在控制器中）使用缓存，以无提示方式获取其他令牌。

本文中的代码片段和以下文章摘自[ASP.NET Web 应用示例](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)。 你可能想要参考此示例以获取完整的实现细节。

# <a name="javatabjava"></a>[Java](#tab/java)

本文中的代码片段和以下代码片段从调用 Microsoft graph MSAL Java web 应用示例的[java web 应用程序](https://github.com/Azure-Samples/ms-identity-java-webapp)中提取。
该示例当前允许 MSAL Java 生成授权代码 URL，并处理到 Microsoft 标识平台授权终结点的导航。 还可以使用冲刺（Sprint）安全性来登录用户。 你可能想要参考此示例以获取完整的实现细节。

# <a name="pythontabpython"></a>[Python](#tab/python)

本文中的代码片段和下面的代码片段从[调用 Microsoft graph MSAL 的 Python web 应用程序](https://github.com/Azure-Samples/ms-identity-python-webapp)中提取出来。Python web 应用示例。
该示例当前允许 MSAL。Python 生成授权代码 URL 并处理到 Microsoft 标识平台授权终结点的导航。 你可能想要参考此示例以获取完整的实现细节。

---

## <a name="code-that-redeems-the-authorization-code"></a>兑换授权代码的代码

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

在 ASP.NET Core 中，原则是在 `Startup.cs` 文件中。 你需要订阅 `OnAuthorizationCodeReceived` open ID connect 事件，并在此事件中调用 MSAL。NET 的方法 `AcquireTokenFromAuthorizationCode`，这种方法可以在令牌缓存中存储、所请求 `scopes`的访问令牌和刷新令牌，该令牌将用于在接近过期时刷新访问令牌，或代表同一个用户获取令牌，但对于不同的资源。

在实践中， [ASP.NET Core web 应用教程](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)试图为你的 web 应用提供可重复使用的代码。

下面是[启动 .cs # L40 L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42)代码，它提供对 `AddMicrosoftIdentityPlatformAuthentication` 方法的调用，该方法将身份验证添加到 web 应用，以及添加调用 web api 的功能 `AddMsal`。 对 `AddInMemoryTokenCaches` 的调用涉及到在可能的中选择令牌缓存实现：

```CSharp
public class Startup
{
  // Code not show here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not show here
}
```

`Constants.ScopeUserRead` 在常量中定义[。 cs # L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5)

```CSharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

你已经学习了 Web 应用中登录用户的 `AddMicrosoftIdentityPlatformAuthentication` 内容[-代码配置](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code)

### <a name="the-addmsal-method"></a>AddMsal 方法

`AddMsal` 的代码位于 WebAppServiceCollectionExtensions [/L108 #-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159)中。

```CSharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization.
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the Web App or Web API
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign-in with their Microsoft personal accounts
          // (it's required by MSAL.NET and automatically provided when users sign-in with work or school accounts)
          options.Scope.Add("offline_access");
          if (initialScopes != null)
          {
              foreach (string scope in initialScopes)
              {
                  if (!options.Scope.Contains(scope))
                  {
                      options.Scope.Add(scope);
                  }
              }
          }

          // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
          // where it will be usable from Controllers later (through the TokenAcquisition service)
          var handler = options.Events.OnAuthorizationCodeReceived;
          options.Events.OnAuthorizationCodeReceived = async context =>
          {
              var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
              await tokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync(context, options.Scope).ConfigureAwait(false);
              await handler(context).ConfigureAwait(false);
          };
      });
      return services;
  }
}
```

`AddMsal` 方法可确保：

- ASP.NET Core Web 应用请求用户的 IDToken 和身份验证代码（`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`）
- 添加 `offline_access` 范围。 这是必需的，以便用户同意允许应用程序获取刷新令牌。
- 应用程序订阅 OIDC `OnAuthorizationCodeReceived` 事件，并使用 MSAL.NET 兑换调用，此调用将封装到实现 `ITokenAcquisition`的可重用组件。

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>TokenAcquisition. AddAccountToCacheFromAuthorizationCodeAsync 方法

`TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` 方法位于 TokenAcquisition [/L101 #-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145)中。 它可确保：

- ASP.NET 不会尝试以并行方式将身份验证代码兑换为 MSAL.NET （`context.HandleCodeRedemption();`）
- IDToken 中的声明可用于 MSAL 计算用户帐户的令牌缓存密钥
- MSAL.NET 应用程序在需要时进行实例化
- 此代码由 MSAL.NET 应用程序兑换
- 新 ID 令牌与 ASP.NET Core 共享（在调用 `context.HandleCodeRedemption(null, result.IdToken);`的过程中）。 访问令牌不与 ASP.NET Core 共享。 它保留在与用户关联的 MSAL.NET 令牌缓存中，并已准备好在 ASP.NET Core 控制器中使用。

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };

  // Code omitted here for clarity


  public async Task AddAccountToCacheFromAuthorizationCodeAsync(AuthorizationCodeReceivedContext context, IEnumerable<string> scopes)
  {
   // Code omitted here for clarity

    try
    {
      // As AcquireTokenByAuthorizationCodeAsync is asynchronous we want to tell ASP.NET core that we are handing the code
      // even if it's not done yet, so that it does not concurrently call the Token endpoint. (otherwise there will be a
      // race condition ending-up in an error from Azure AD telling "code already redeemed")
      context.HandleCodeRedemption();

      // The cache will need the claims from the ID token.
      // If they are not yet in the HttpContext.User's claims, so adding them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it and will not send the OAuth 2.0 request in
      // case a further call to AcquireTokenByAuthorizationCodeAsync in the future is required for incremental consent (getting a code requesting more scopes)
      // Share the ID Token though
      var result = await application
          .AcquireTokenByAuthorizationCode(scopes.Except(_scopesRequestedByMsalNet), context.ProtocolMessage.Code)
          .ExecuteAsync()
          .ConfigureAwait(false);

      context.HandleCodeRedemption(null, result.IdToken);
  }
  catch (MsalException ex)
  {
      Debug.WriteLine(ex.Message);
      throw;
  }
 }
```

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>TokenAcquisition. BuildConfidentialClientApplication 方法

在 ASP.NET Core 中，生成机密客户端应用程序会用到 HttpContext 中的信息。 通过与请求关联的 `CurrentHttpContext` 属性、HttpContext 知道 Web 应用的 URL 以及已登录用户（在 `ClaimsPrincipal`中）进行访问。 `BuildConfidentialClientApplication` 还使用 ASP.NET Core 配置，该配置包含 "AzureAD" 部分，并同时绑定到：

- 类型为 `_applicationOptions`ConfidentialClientApplicationOptions[ 的 ](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet) 数据结构
- ASP.NET Core `azureAdOptions` 中定义的 [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) 类型的 `Authentication.AzureAD.UI` 实例。 最后，应用程序需要维护令牌缓存。 你将在下一部分中了解有关此内容的详细信息。

`GetOrBuildConfidentialClientApplication()` 方法的代码位于 TokenAcquisition [/# L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333)中。 它使用由依赖关系注入注入的成员（在 TokenAcquisition [/TokenAcquisition # L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)中传递到的构造函数中）

```CSharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL Confidential client application if needed
  /// </summary>
  private IConfidentialClientApplication GetOrBuildConfidentialClientApplication()
  {
    if (application == null)
    {
        application = BuildConfidentialClientApplication();
    }
    return application;
  }

  /// <summary>
  /// Creates an MSAL Confidential client application
  /// </summary>
  /// <param name="claimsPrincipal"></param>
  /// <returns></returns>
  private IConfidentialClientApplication BuildConfidentialClientApplication()
  {
    var request = CurrentHttpContext.Request;
    var azureAdOptions = _azureAdOptions;
    var applicationOptions = _applicationOptions;
    string currentUri = UriHelper.BuildAbsolute(
        request.Scheme,
        request.Host,
        request.PathBase,
        azureAdOptions.CallbackPath ?? string.Empty);

    string authority = $"{applicationOptions.Instance}{applicationOptions.TenantId}/";

    var app = ConfidentialClientApplicationBuilder
        .CreateWithApplicationOptions(applicationOptions)
        .WithRedirectUri(currentUri)
        .WithAuthority(authority)
        .Build();

    // Initialize token cache providers
    _appTokenCacheProvider?.InitializeAsync(app.AppTokenCache);
    _userTokenCacheProvider?.InitializeAsync(app.UserTokenCache);

    return app;
  }

```

### <a name="summary"></a>摘要

若要求和，`AcquireTokenByAuthorizationCode` 确实兑换了 ASP.NET 请求的授权代码，并获取添加到 MSAL.NET 用户令牌缓存中的令牌。 然后在 ASP.NET Core 控制器中使用这些令牌。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 处理内容的方式与 ASP.NET Core 类似，不同之处在于 OpenIdConnect 的配置以及对 `OnAuthorizationCodeReceived` 事件的订阅发生在[App_Start\Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs)文件中。 你将找到类似于 ASP.NET Core 中的类似概念，只是在 ASP.NET 中，你需要在[web.config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15)中指定 RedirectUri。 此配置比 ASP.NET Core 中所执行的配置低一点，因为你需要在部署应用程序时对其进行更改。

```CSharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the querystring (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // a basic set of permissions for user sign in & profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application you would use IssuerValidator for additional checks, like making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign in, get the access token & cache it using MSAL
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

请参阅登录[用户代码配置中的 Web 应用](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code)中的，了解 Java 示例如何获取授权代码。 应用收到后， [AuthFilter # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56)将委托给[authhelper.java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97)中的 `AuthHelper.processAuthenticationCodeRedirect` 方法，然后调用 `getAuthResultByAuthCode`：

```Java
class AuthHelper {
  // code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// code omitted
  }
}
```

`getAuthResultByAuthCode` 方法是在[authhelper.java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176)中定义的。 它将创建一个 MSAL `ConfidentialClientApplication`，并使用从授权代码创建的 `AuthorizationCodeParameters` 调用 `acquireToken()`。

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

在[登录用户代码配置的 Web 应用](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code)中，请求授权代码流后，就会在 `authorized` 函数上收到授权代码，该函数将从/GetAToken URL Flask 路由。 请参阅[py # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44)

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

机密客户端应用程序还可以使用客户端证书或客户端断言来证明其身份，而不是客户端机密。
使用客户端断言是一种高级方案，详见[客户端断言](msal-net-client-assertions.md)

## <a name="token-cache"></a>令牌缓存

> [!IMPORTANT]
> 在 web 应用（或 web Api）中，令牌缓存实现不同于桌面应用程序令牌缓存实现（通常是[基于文件](scenario-desktop-acquire-token.md#file-based-token-cache)的）。
> 出于安全和性能方面的原因，确保对于 web 应用和 web Api，每个用户应有一个令牌缓存（每个帐户），这一点很重要。 需要为每个帐户序列化令牌缓存。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET 核心教程使用依赖关系注入来确定应用程序的 Startup.cs 文件中的令牌缓存实现。 Microsoft 提供了大量预建的令牌缓存序列化程序，如[令牌缓存序列化](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization)中所述。 一种有趣的可能性是选择 ASP.NET Core[分布式内存缓存](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)：

```csharp
// or use a distributed Token Cache by adding
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// and then choose your implementation

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache()

// Or a Redis cache
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

有关令牌缓存提供程序的详细信息，另请参阅[ASP.NET Core Web 应用教程 |教程的令牌缓存](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)阶段

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 Web 应用（实际上是 Web API）中，令牌缓存实现不同于桌面应用程序令牌缓存实现（通常[基于文件](scenario-desktop-acquire-token.md#file-based-token-cache)）。 它可以使用 ASP.NET 会话或服务器内存。 有关在[MsalAppBuilder # L39](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)中创建 MSAL.NET 应用程序后如何挂钩缓存实现的示例，请参阅。

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java 提供用于序列化和反序列化令牌缓存的方法。 Java 示例处理来自会话的序列化，如[authhelper.java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)中的 `getAuthResultBySilentFlow` 方法中所示。

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  //update session with latest token cache
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

`SessionManagementHelper`[中提供了 ](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java) 类的详细信息

# <a name="pythontabpython"></a>[Python](#tab/python)

在 Python 示例中，通过重新创建每个请求的机密客户端应用程序并将其序列化到 Flask 会话缓存来确保按帐户缓存：

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>后续步骤

现在，当用户登录时，会在令牌缓存中存储一个令牌。 让我们看看随后该令牌如何在 Web 应用的其他组成部分中使用。

> [!div class="nextstepaction"]
> [登录到 Web 应用](scenario-web-app-call-api-sign-in.md)
