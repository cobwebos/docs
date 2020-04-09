---
title: 配置调用 Web API 的 Web 应用 - Microsoft 标识平台 | Azure
description: 了解如何配置调用 Web API 的 Web 应用的代码
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
ms.openlocfilehash: b0f60e13ca4bc5115f9a49885c3c659ad1147fcc
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881887"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>调用 Web API 的 Web 应用：代码配置

如 [Web 应用将用户登录的方案](scenario-web-app-sign-user-overview.md)中所示，Web 应用使用 [OAuth 2.0 授权代码流](v2-oauth2-auth-code-flow.md)将用户登录。 此流包括两个步骤：

1. 请求授权代码。 此部分向 Microsoft 标识平台委托一个要显示给用户的私密对话。 在对话中，用户登录并同意使用 Web API。 当此私密对话成功结束时，Web 应用会在其重定向 URI 中收到一个授权代码。
1. 通过兑换授权代码来请求 API 的访问令牌。

[将用户登录的 Web 应用](scenario-web-app-sign-user-overview.md)方案仅包括第一个步骤。 本文介绍如何修改 Web 应用，使其不仅可将用户登录，而且现在还可调用 Web API。

## <a name="libraries-that-support-web-app-scenarios"></a>支持 Web 应用方案的库

Microsoft 身份验证库 (MSAL) 中的以下库支持 Web 应用的授权代码流：

| MSAL 库 | 说明 |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 支持 .NET Framework 和 .NET Core 平台。 不支持通用 Windows 平台 (UWP)、Xamarin.iOS 和 Xamarin.Android，因为这些平台用于生成公共客户端应用程序。 |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> 适用于 Python 的 MSAL | 支持 Python Web 应用程序。 |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> 适用于 Java 的 MSAL | 支持 Java Web 应用程序。 |

选择所需平台对应的选项卡：

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

由于用户登录已委托给 Open ID Connect (OIDC) 中间件，因此必须与 OIDC 过程交互。 交互方式取决于所用的框架。

对于 ASP.NET Core，需要订阅中间件 OIDC 事件：

- 允许 ASP.NET Core 通过 Open ID Connect 中间件请求授权代码。 ASP.NET 或 ASP.NET Core 会让用户登录并提供许可。
- 订阅 Web 应用以接收授权代码。 此订阅是使用 C# 委托实现的。
- 收到授权代码后，使用 MSAL 库兑换该代码。 生成的访问令牌和刷新令牌将存储在令牌缓存中。 可在应用程序的其他组成部分（例如控制器）中使用缓存来以静默方式获取其他令牌。

本文中的代码示例及以下内容摘自 [ASP.NET Core Web 应用增量教程第 2 章](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)。 你可能需要参考该教程，了解完整的实现细节。

> [!NOTE]
> 若要完全理解以下代码示例，需要熟悉 [ASP.NET Core 基础知识](https://docs.microsoft.com/aspnet/core/fundamentals)，尤其是[依赖项注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)和[选项](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

由于用户登录已委托给 Open ID Connect (OIDC) 中间件，因此必须与 OIDC 过程交互。 交互方式取决于所用的框架。

对于 ASP.NET，需要订阅中间件 OIDC 事件：

- 允许 ASP.NET Core 通过 Open ID Connect 中间件请求授权代码。 ASP.NET 或 ASP.NET Core 会让用户登录并提供许可。
- 订阅 Web 应用以接收授权代码。 此订阅是使用 C# 委托实现的。
- 收到授权代码后，使用 MSAL 库兑换该代码。 生成的访问令牌和刷新令牌将存储在令牌缓存中。 可在应用程序的其他组成部分（例如控制器）中使用缓存来以静默方式获取其他令牌。

本文中的代码示例及以下内容摘自 [ASP.NET Web 应用示例](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)。 可能需要参考该示例来了解完整的实现细节。

# <a name="java"></a>[Java](#tab/java)

本文中的代码示例及以下内容摘自[调用 Microsoft Graph 的 Java Web 应用程序](https://github.com/Azure-Samples/ms-identity-java-webapp)（使用适用于 Java 的 MSAL 的 Web 应用示例）。
该示例目前允许适用于 Java 的 MSAL 生成授权代码 URL，并处理到 Microsoft 标识平台授权终结点的导航。 还可以使用 Sprint 安全性将用户登录。 可能需要参考该示例来了解完整的实现细节。

# <a name="python"></a>[Python](#tab/python)

本文中的代码示例及以下内容摘自[调用 Microsoft Graph 的 Python Web 应用程序](https://github.com/Azure-Samples/ms-identity-python-webapp)（使用 MSAL.Python 的 Web 应用示例）。
该示例目前允许 MSAL.Python 生成授权代码 URL，并处理到 Microsoft 标识平台授权终结点的导航。 可能需要参考该示例来了解完整的实现细节。

---

## <a name="code-that-redeems-the-authorization-code"></a>用于兑换授权代码的代码

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

在 ASP.NET Core 中，通过 `Startup.cs` 文件订阅 `OnAuthorizationCodeReceived` OpenID Connect 事件。 通过此事件调用 MSAL.NET `AcquireTokenFromAuthorizationCode` 方法。 此方法将以下令牌存储在令牌缓存中：

- 所请求的 `scopes` 的访问令牌。**
- 刷新令牌。** 此令牌在访问令牌即将过期时用于刷新访问令牌，或者代表相同用户获取另一个令牌（但为不同资源的令牌）。

[ASP.NET Core Web 应用教程](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)提供了 Web 应用的可重用代码。

下面是摘自 [Startup.cs#L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42) 的代码。 其中包含对以下方法的调用：

- `AddMicrosoftIdentityPlatformAuthentication` 方法，用于向 Web 应用添加身份验证。
- `AddMsal` 方法，用于添加调用 Web API 的功能。
- `AddInMemoryTokenCaches` 方法，用于选择令牌缓存实现。

```csharp
public class Startup
{
  // Code not shown here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token-cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not shown here
}
```

`Constants.ScopeUserRead` 在 [Constants.cs#L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5) 中定义：

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

你已学习了[将用户登录的 Web 应用 - 代码配置](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code)中 `AddMicrosoftIdentityPlatformAuthentication` 的内容。

### <a name="the-addmsal-method"></a>AddMsal 方法

`AddMsal` 的代码位于 [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159) 中。

```csharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the web app or web API.
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection.
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign in by using their personal Microsoft accounts.
          // (It's required by MSAL.NET and automatically provided when users sign in by using work or school accounts.)
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

          // Handle the auth redemption by MSAL.NET so that a token is available in the token cache,
          // where it will be usable from controllers later (by means of the TokenAcquisition service).
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

`AddMsal` 方法确保：

- ASP.NET Core Web 应用既请求用户的 ID 令牌，也请求身份验证代码 (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`)。
- 添加 `offline_access` 范围。 此范围获取用户的许可，使应用程序能够获取刷新令牌。
- 应用订阅 OIDC `OnAuthorizationCodeReceived` 事件，并使用 MSAL.NET 兑换调用，此调用在此处封装到实现 `ITokenAcquisition` 的可重用组件中。

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync 方法

`TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` 方法位于 [Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145) 中。 它确保：

- ASP.NET 不会与 MSAL.NET 一同尝试兑换身份验证代码 (`context.HandleCodeRedemption();`)。
- ID 令牌中的声明供 MSAL 用来计算用户帐户的令牌缓存密钥。
- 如果需要，将创建 MSAL.NET 应用程序的实例。
- 代码由 MSAL.NET 应用程序兑换。
- 在调用 `context.HandleCodeRedemption(null, result.IdToken);` 过程中，新 ID 令牌将与 ASP.NET Core 共享。 访问令牌不会与 ASP.NET Core 共享。 访问令牌保留在与用户关联的 MSAL.NET 令牌缓存中，随时可在 ASP.NET Core 控制器中使用。

下面是 `TokenAcquisition` 的相关代码：

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
      // Because AcquireTokenByAuthorizationCodeAsync is asynchronous, we tell ASP.NET core that we're handing the code
      // even if it's not done yet, so that it doesn't concurrently call the token endpoint. Otherwise, there will be a
      // race condition that causes an Azure AD error message ("code already redeemed").
      context.HandleCodeRedemption();

      // The cache needs the claims from the ID token.
      // If they're not yet in the HttpContext.User's claims, add them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Don't share the access token with ASP.NET Core. If we share it, ASP.NET will cache it and won't send the OAuth 2.0 request if
      // a further call to AcquireTokenByAuthorizationCodeAsync is required later for incremental consent (getting a code requesting more scopes).
      // Do share the ID token, however.
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

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>TokenAcquisition.BuildConfidentialClientApplication 方法

在 ASP.NET Core 中，生成机密客户端应用程序会用到 `HttpContext` 中的信息。 使用 `CurrentHttpContext` 属性访问与请求关联的 `HttpContext`。 `HttpContext` 包含有关 Web 应用 URL 的信息，以及有关已登录用户的信息（在 `ClaimsPrincipal` 中）。 

`BuildConfidentialClientApplication` 方法还使用 ASP.NET Core 配置。 此配置包含“AzureAD”节，同时已绑定到以下两个元素：

- `_applicationOptions`[类型机密客户端应用程序选项](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet)的数据结构。
- ASP.NET Core `Authentication.AzureAD.UI` 中定义的 [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) 类型的 `azureAdOptions` 实例。

最后，应用程序需要维护令牌缓存。 下一部分将详细介绍相关信息。

`GetOrBuildConfidentialClientApplication()` 方法的代码位于 [Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333) 中。 它使用依赖项注入功能注入的成员（传入到 [Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59) 中 `TokenAcquisition` 的构造函数）。

下面是 `GetOrBuildConfidentialClientApplication` 的代码：

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor.
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL confidential client application, if needed.
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

### <a name="summary"></a>总结

`AcquireTokenByAuthorizationCode` 实际上是兑换 ASP.NET 请求的授权代码并获取已添加到 MSAL.NET 用户令牌缓存的令牌的方法。 然后通过缓存在 ASP.NET Core 控制器中使用这些令牌。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 中的处理方式类似于 ASP.NET Core，不同之处在于 OpenID Connect 的配置，并且 `OnAuthorizationCodeReceived` 事件订阅是在 [App_Start\Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) 文件中发生的。 ASP.NET Core 中也有类似的概念，不过，在 ASP.NET 中，必须在 [Web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15) 中指定 `RedirectUri`。 与在 ASP.NET Core 中相比，此配置相对不太可靠，因为需要在部署应用程序时对其进行更改。

下面是 Startup.Auth.cs 的代码：

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
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
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
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
      // Upon successful sign-in, get the access token and cache it by using MSAL.
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

# <a name="java"></a>[Java](#tab/java)

请参阅[在用户中签名的 Web 应用：代码配置](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code)以了解 Java 示例如何获取授权代码。 在应用收到代码后，[AuthFilter.java#L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56) 将会：

1. 委托给 [AuthHelper.java#L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97) 中的 `AuthHelper.processAuthenticationCodeRedirect` 方法。
1. 调用 `getAuthResultByAuthCode`。

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

`getAuthResultByAuthCode` 方法在 [AuthHelper.java#L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176) 中定义。 它创建 MSAL `ConfidentialClientApplication`，然后使用基于授权代码创建的 `AuthorizationCodeParameters` 调用 `acquireToken()`。

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

# <a name="python"></a>[Python](#tab/python)

请求授权代码流，如[在用户中签名的 Web 应用中所示：代码配置](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code)。 然后，在 Flask 从 `/getAToken` URL 路由的 `authorized` 函数中接收该代码。 有关此代码的完整上下文，请参阅 [app.py#L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44)：

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

机密客户端应用程序还可以使用客户端证书或客户端断言（而不是客户端密码）来证明其身份。
使用客户端断言是一种高级方案，详见[客户端断言](msal-net-client-assertions.md)。

## <a name="token-cache"></a>令牌缓存

> [!IMPORTANT]
> Web 应用或 Web API 的令牌缓存实现不同于桌面应用程序的实现，后者通常[基于文件](scenario-desktop-acquire-token.md#file-based-token-cache)。
> 出于安全和性能原因，对于 Web 应用和 Web API，必须确保每个用户帐户有一个令牌缓存。 必须为每个帐户序列化令牌缓存。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core 教程使用依赖项注入来让你确定应用程序的 Startup.cs 文件中的令牌缓存实现。 Microsoft.Identity.Web 随附了[令牌缓存序列化](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization)中所述的预生成令牌缓存序列化程序。 一个有意思的可能选项是 ASP.NET Core [分布式内存缓存](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)：

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// Then, choose your implementation.

// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache()

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

有关令牌缓存提供程序的详细信息，另请参阅本教程的 [ASP.NET Core Web 应用教程 | 令牌缓存](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)部分。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Web 应用或 Web API 的令牌缓存实现不同于桌面应用程序的实现，后者通常[基于文件](scenario-desktop-acquire-token.md#file-based-token-cache)。

Web 应用实现可以使用 ASP.NET 会话或服务器内存。 例如，在 [MsalAppBuilder.cs#L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51) 中可以了解创建 MSAL.NET 应用程序后如何挂接缓存实现：

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

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="java"></a>[Java](#tab/java)

MSAL Java 提供用于序列化和反序列化令牌缓存的方法。 该 Java 示例处理来自会话的序列化，如 [AuthHelper.java#L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122) 中的 `getAuthResultBySilentFlow` 方法所示：

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

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

[适用于 Java 的 MSAL 示例](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)中提供了 `SessionManagementHelper` 类的详细信息。

# <a name="python"></a>[Python](#tab/python)

在该 Python 示例中，通过重新创建每个请求的机密客户端应用程序并在 Flask 会话缓存中将其序列化，来确保为每个帐户提供一个缓存：

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
> [调用 Web API 的 Web 应用：从全局注销时从缓存中删除帐户](scenario-web-app-call-api-sign-in.md)
