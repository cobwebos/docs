---
title: 配置用于调用 web Api 的 web 应用-Microsoft 标识平台 |Microsoft
description: 了解如何配置用于调用 web Api 的 web 应用的代码
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
ms.openlocfilehash: 374b215a737efbe3d421b6dc49af01303ec54473
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759154"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>用于调用 web Api 的 web 应用：代码配置

如[web 应用](scenario-web-app-sign-user-overview.md)中的 "登录用户" 方案中所示，web 应用使用[OAuth 2.0 授权代码流](v2-oauth2-auth-code-flow.md)在中对用户进行签名。 此流程有两个步骤：

1. 请求授权代码。 此部分将向用户委派与 Microsoft 标识平台的专用对话。 在该对话框中，用户登录并同意使用 web Api。 当专用对话成功结束时，web 应用会在其重定向 URI 上收到授权代码。
1. 通过兑换授权代码来请求 API 的访问令牌。

在[用户方案中登录的 Web 应用](scenario-web-app-sign-user-overview.md)仅涉及第一步。 在这里，你将了解如何修改你的 web 应用，以便它不仅在中对用户进行签名，还会调用 web Api。

## <a name="libraries-that-support-web-app-scenarios"></a>支持 web 应用方案的库

Microsoft 身份验证库（MSAL）中的以下库支持 web 应用的授权代码流：

| MSAL 库 | Description |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 支持 .NET Framework 和 .NET Core 平台。 不支持通用 Windows 平台（UWP）、Xamarin 和 Xamarin，因为这些平台用于构建公共客户端应用程序。 |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> 适用于 Python 的 MSAL | 支持 Python web 应用程序。 |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> 适用于 Java 的 MSAL | 支持 Java web 应用程序。 |

选择你感兴趣的平台的选项卡：

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

由于用户登录被委派给 Open ID connect （OIDC）中间件，因此必须与 OIDC 进程交互。 交互的方式取决于所使用的框架。

对于 ASP.NET Core，你将订阅中间件 OIDC 事件：

- 你将让 ASP.NET Core 通过开放 ID Connect 中间件来请求授权代码。 ASP.NET 或 ASP.NET Core 将允许用户登录和同意。
- 你将订阅 web 应用以接收授权代码。 此订阅是通过使用C#委托来完成的。
- 收到授权代码后，你将使用 MSAL 库兑换。 生成的访问令牌和刷新令牌存储在标记缓存中。 可以在应用程序的其他部分（如控制器）中使用缓存，以便无提示地获取其他令牌。

本文中的代码示例以及下面的代码示例摘自[ASP.NET Core web 应用增量教程第2章](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)。 你可能想要参考该教程，了解完整的实现细节。

> [!NOTE]
> 若要完全了解此处的代码示例，需要熟悉[ASP.NET Core 基础知识](https://docs.microsoft.com/aspnet/core/fundamentals)，尤其是[依赖项注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)和[选项](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

由于用户登录被委派给 Open ID connect （OIDC）中间件，因此必须与 OIDC 进程交互。 交互的方式取决于所使用的框架。

对于 ASP.NET，你将订阅中间件 OIDC 事件：

- 你将让 ASP.NET Core 通过开放 ID Connect 中间件来请求授权代码。 ASP.NET 或 ASP.NET Core 将允许用户登录和同意。
- 你将订阅 web 应用以接收授权代码。 此订阅是通过使用C#委托来完成的。
- 收到授权代码后，你将使用 MSAL 库兑换。 生成的访问令牌和刷新令牌存储在标记缓存中。 可以在应用程序的其他部分（如控制器）中使用缓存，以便无提示地获取其他令牌。

本文中的代码示例以及下面的代码示例是从[ASP.NET Web 应用示例](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)中提取的。 你可能想要参考该示例以获得完整的实现细节。

# <a name="javatabjava"></a>[Java](#tab/java)

本文中的代码示例以及下面的代码示例从[调用 Microsoft Graph 的 java web 应用程序](https://github.com/Azure-Samples/ms-identity-java-webapp)（即使用 MSAL for Java 的 web 应用程序示例）中提取。
该示例当前允许 MSAL for Java 生成授权代码 URL，并处理 Microsoft 标识平台的授权终结点导航。 还可以使用冲刺（Sprint）安全性在中对用户进行签名。 你可能想要参考示例以获取完整的实现细节。

# <a name="pythontabpython"></a>[Python](#tab/python)

本文中的代码示例以及下面的代码示例从[调用 Microsoft Graph 的 Python web 应用程序](https://github.com/Azure-Samples/ms-identity-python-webapp)（使用 MSAL 的 web 应用示例）中提取。Python.
该示例当前允许 MSAL。Python 生成授权代码 URL，并处理到 Microsoft 标识平台的授权终结点的导航。 你可能想要参考示例以获取完整的实现细节。

---

## <a name="code-that-redeems-the-authorization-code"></a>兑换授权代码的代码

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

在 ASP.NET Core 的 `Startup.cs` 文件中，你订阅了 `OnAuthorizationCodeReceived` OpenID Connect 事件。 在此事件中，请调用 MSAL.NET `AcquireTokenFromAuthorizationCode` 方法。 此方法将以下令牌存储在令牌缓存中：

- 请求的 `scopes`的*访问令牌*。
- *刷新标记*。 此令牌将用于在接近过期时刷新访问令牌，或代表同一用户或不同资源获取另一个令牌。

[ASP.NET Core web 应用教程](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)可为你的 web 应用提供可重复使用的代码。

下面是 L40 中的代码： [.cs # L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42)。 It 功能调用：

- `AddMicrosoftIdentityPlatformAuthentication` 方法，该方法将身份验证添加到 web 应用。
- `AddMsal` 方法，该方法可添加调用 Web Api 的功能。
- `AddInMemoryTokenCaches` 方法，该方法用于选择令牌缓存实现。

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

`Constants.ScopeUserRead` 在常量中定义[。 cs # L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5)：

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

你已经学习了在[Web 应用中登录用户](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code)的 `AddMicrosoftIdentityPlatformAuthentication` 内容。

### <a name="the-addmsal-method"></a>AddMsal 方法

`AddMsal` 的代码位于 WebAppServiceCollectionExtensions [/L108 #-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159)中。

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

`AddMsal` 方法可确保：

- ASP.NET Core web 应用请求用户的 ID 令牌和身份验证代码（`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`）。
- 添加 `offline_access` 范围。 此作用域允许应用程序获取刷新令牌的用户同意。
- 该应用程序订阅 OIDC `OnAuthorizationCodeReceived` 事件，并使用 MSAL.NET 将其兑换调用，此方法将封装在一个可重用的组件中，该组件实现 `ITokenAcquisition`。

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>TokenAcquisition. AddAccountToCacheFromAuthorizationCodeAsync 方法

`TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` 方法位于 TokenAcquisition [/L101 #-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145)中。 它可确保：

- ASP.NET 不会尝试以并行方式将身份验证代码兑换为 MSAL.NET （`context.HandleCodeRedemption();`）。
- ID 令牌中的声明可用于 MSAL 计算用户帐户的令牌缓存密钥。
- 如果需要，创建 MSAL.NET 应用程序的实例。
- 此代码由 MSAL.NET 应用程序兑换。
- 在调用 `context.HandleCodeRedemption(null, result.IdToken);`的过程中，将与 ASP.NET Core 共享新的 ID 标记。 访问令牌不与 ASP.NET Core 共享。 它保留在与用户关联的 MSAL.NET 令牌缓存中，并已准备好在 ASP.NET Core 控制器中使用。

下面是 `TokenAcquisition`的相关代码：

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

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>TokenAcquisition. BuildConfidentialClientApplication 方法

在 ASP.NET Core 中，生成机密客户端应用程序使用 `HttpContext`中的信息。 使用 `CurrentHttpContext` 属性访问与请求关联的 `HttpContext`。 `HttpContext` 包含有关 web 应用的 URL 以及已登录用户（在 `ClaimsPrincipal`中）的信息。 

`BuildConfidentialClientApplication` 方法还使用 ASP.NET Core 配置。 此配置包含 "AzureAD" 节，还绑定到以下两个元素：

- [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet)类型的 `_applicationOptions` 数据结构。
- ASP.NET Core `Authentication.AzureAD.UI`中定义的[AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs)类型的 `azureAdOptions` 实例。

最后，应用程序需要维护令牌缓存。 你将在下一节中详细了解相关信息。

`GetOrBuildConfidentialClientApplication()` 方法的代码位于 TokenAcquisition [/# L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333)中。 它使用通过依赖关系注入注入的成员（在 TokenAcquisition [//L47 #-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)中通过构造 `TokenAcquisition` 函数中传递）。

下面是 `GetOrBuildConfidentialClientApplication`的代码：

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

### <a name="summary"></a>摘要

`AcquireTokenByAuthorizationCode` 实际上是兑换 ASP.NET 请求的授权代码，并获取添加到 MSAL.NET 用户令牌缓存的令牌的方法。 在缓存中，将在 ASP.NET Core 控制器中使用标记。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 处理与 ASP.NET Core 类似的情况，不同之处在于 OpenID Connect 的配置和 `OnAuthorizationCodeReceived` 事件的订阅发生在[App_Start \startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs)文件中。 概念也类似于 ASP.NET Core 中的概念，只是在 ASP.NET 中，你必须在[web.config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15)中指定 `RedirectUri`。 此配置比 ASP.NET Core 中的配置低一点，因为你需要在部署应用程序时对其进行更改。

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

# <a name="javatabjava"></a>[Java](#tab/java)

请参阅[登录用户的 Web 应用：代码配置](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code)以了解 Java 示例如何获取授权代码。 应用收到代码后， [AuthFilter # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56)：

1. 委托给[authhelper.java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97)中的 `AuthHelper.processAuthenticationCodeRedirect` 方法。
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

`getAuthResultByAuthCode` 方法是在[authhelper.java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176)中定义的。 它将创建一个 MSAL `ConfidentialClientApplication`，然后使用从授权代码创建的 `AuthorizationCodeParameters` 调用 `acquireToken()`。

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

请求授权代码流，如[用于登录用户的 Web 应用：代码配置](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code)中所示。 然后，将在 `authorized` 函数上收到代码，该函数 Flask 从 `/getAToken` URL 进行路由。 有关此代码的完整上下文，请参阅[py # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) ：

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

机密客户端应用程序还可以使用客户端证书或客户端断言来证明其身份，而不是客户端机密。
使用客户端断言是一种高级方案，详细信息是[客户端断言](msal-net-client-assertions.md)。

## <a name="token-cache"></a>令牌缓存

> [!IMPORTANT]
> Web 应用或 web Api 的令牌缓存实现不同于桌面应用程序的实现，这通常是[基于文件](scenario-desktop-acquire-token.md#file-based-token-cache)的。
> 出于安全和性能方面的原因，确保对于 web 应用和 web Api，每个用户帐户都有一个令牌缓存，这一点很重要。 必须序列化每个帐户的令牌缓存。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET 核心教程使用依赖关系注入来确定应用程序的 Startup.cs 文件中的令牌缓存实现。 在[令牌缓存序列化](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization)中介绍了预生成的令牌缓存的序列化程序。 一种有趣的可能性是选择 ASP.NET Core[分布式内存缓存](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)：

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

有关令牌缓存提供程序的详细信息，另请参阅[ASP.NET Core Web 应用教程 |标记缓存](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)阶段。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Web 应用或 web Api 的令牌缓存实现不同于桌面应用程序的实现，这通常是[基于文件](scenario-desktop-acquire-token.md#file-based-token-cache)的。

Web 应用实现可以使用 ASP.NET 会话或服务器内存。 例如，请参阅在[MsalAppBuilder # L39](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)中创建 MSAL.NET 应用程序后如何挂钩缓存实现：

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

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java 提供用于序列化和反序列化令牌缓存的方法。 Java 示例处理来自会话的序列化，如[authhelper.java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)中的 `getAuthResultBySilentFlow` 方法所示：

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

# <a name="pythontabpython"></a>[Python](#tab/python)

在 Python 示例中，通过重新创建每个请求的机密客户端应用程序，然后将其序列化到 Flask 会话缓存，可以确保每个帐户一个缓存：

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

此时，当用户登录时，令牌将存储在令牌缓存中。 让我们看看它如何在 web 应用程序的其他部分中使用。

> [!div class="nextstepaction"]
> [用于调用 web Api 的 web 应用：在全局注销时从缓存中删除帐户](scenario-web-app-call-api-sign-in.md)
