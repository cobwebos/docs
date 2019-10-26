---
title: 桌面应用程序调用 web Api （获取应用程序的令牌）-Microsoft 标识平台
description: 了解如何构建一个可调用 web Api （为应用程序获取令牌）的桌面应用程序
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
ms.date: 10/24/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0eaeaf915ad480306c114d7ab79e88e95c336eb
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893906"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>用于调用 web Api 的桌面应用-获取令牌

构建 `IPublicClientApplication`后，你将使用它来获取用于调用 web API 的令牌。

## <a name="recommended-pattern"></a>推荐的模式

Web API 由其 `scopes`定义。 无论你在应用程序中提供何种体验，你都需要使用的模式如下：

- 通过调用 `AcquireTokenSilent`，系统尝试从令牌缓存获取令牌
- 如果此调用失败，请使用要使用的 `AcquireToken` 流（此处表示 `AcquireTokenXX`）

### <a name="in-msalnet"></a>在 MSAL.NET 中

```CSharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account  
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```
### <a name="in-msal-for-ios-and-macos"></a>适用于 iOS 和 macOS 的 MSAL

Objective-C：

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];
    
MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {
    
    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Swift：

```swift
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in
            
    guard let authResult = result, error == nil else {
                
    let nsError = error! as NSError
                
        if (nsError.domain == MSALErrorDomain &&
            nsError.code == MSALError.interactionRequired.rawValue) {
                    
            // Interactive auth will be required, call acquireToken()
            return
        }
        return
    }
}
```

下面是在桌面应用程序中获取令牌的各种方式的详细信息

## <a name="acquiring-a-token-interactively"></a>以交互方式获取令牌

下面的示例演示了使用 Microsoft Graph 以交互方式获取用于读取用户配置文件的令牌的最小代码。

### <a name="in-msalnet"></a>在 MSAL.NET 中

```CSharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="in-msal-for-ios-and-macos"></a>适用于 iOS 和 macOS 的 MSAL

Objective-C：

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error) 
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        NSString *accessToken = result.accessToken;
    }
}];
```

Swift：

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in
                
    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }
                
    // Get access token from result
    let accessToken = authResult.accessToken
})
```

### <a name="mandatory-parameters"></a>必需参数

`AcquireTokenInteractive` 只有一个必需参数 ``scopes``，该参数包含用于定义需要令牌的范围的字符串的枚举。 如果令牌用于 Microsoft Graph，则可以在名为 "权限" 的部分中的每个 Microsoft Graph API 的 api 引用中找到所需的作用域。 例如，若要[列出用户的联系人](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)，则需要使用作用域 "User. read"、"contacts"。 另请参阅[Microsoft Graph 权限参考](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)。

在 Android 上，还需要指定父活动（使用 `.WithParentActivityOrWindow`，请参阅下图），以便在交互后令牌返回到该父活动。 如果不指定它，则在调用 `.ExecuteAsync()`时将引发异常。

### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET 中的特定可选参数

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

UI 非常重要。 `AcquireTokenInteractive` 具有一个特定的可选参数，该参数可用于为支持它的平台（父 UI）指定。 在桌面应用程序中使用时，`.WithParentActivityOrWindow` 具有不同的类型，具体取决于平台：

```CSharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

备注：

- 在 .NET Standard 上，预期 `object` 为 Android 上的 `Activity`、iOS 上的 `UIViewController`、MAC 上的 `NSWindow`，以及 Windows 上的 `IWin32Window` 或 `IntPr`。
- 在 Windows 上，必须从 UI 线程调用 `AcquireTokenInteractive`，以便嵌入浏览器获取适当的 UI 同步上下文。  不从 UI 线程调用可能会导致消息无法正确地与 UI 一起抽取和/或死锁情况。 如果你不在 UI 线程上，从 UI 线程调用 MSAL 的一种方法是使用 WPF 上的 `Dispatcher`。
- 如果你使用的是 WPF，若要从 WPF 控件获取窗口，你可以使用 `WindowInteropHelper.Handle` 类。 然后从 WPF 控件（`this`）调用该调用：
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` 用于通过指定提示来控制与用户的交互

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

类定义以下常量：

- ``SelectAccount``：将强制 STS 显示包含用户为其会话的帐户的帐户选择对话框。 当应用程序开发人员希望允许用户在不同标识之间进行选择时，此选项很有用。 此选项驱动 MSAL 将 ``prompt=select_account`` 发送到标识提供者。 此选项是默认选项，它可以根据可用信息（帐户、用户的会话状态等）提供最佳体验，从而提供最佳体验。 ...).不要对其进行更改，除非你有充分的理由。
- ``Consent``：允许应用程序开发人员强制提示用户同意，即使之前已授予同意也是如此。 在这种情况下，MSAL 会将 `prompt=consent` 发送到标识提供者。 此选项可用于某些注重安全的应用程序，在这些应用程序中，组织监管要求用户在每次使用应用程序时都显示许可对话框。
- ``ForceLogin``：允许应用程序开发人员通过服务提示用户提供凭据，即使不需要此用户提示也是如此。 如果获取令牌失败，让用户重新登录，则此选项很有用。 在这种情况下，MSAL 会将 `prompt=login` 发送到标识提供者。 同样，我们已了解到它在某些注重安全的应用程序中使用，在这些应用程序中，组织治理要求用户在每次访问应用程序的特定部分时 relogs。
- ``Never`` （仅适用于 .NET 4.5 和 WinRT）不会提示用户，而是尝试使用隐藏的嵌入式 web 视图中存储的 cookie （请参阅以下内容： MSAL.NET 中的 Web 视图）。 使用此选项可能会失败，并且在这种情况下 `AcquireTokenInteractive` 将引发异常以通知需要 UI 交互，你将需要使用另一个 `Prompt` 参数。
- ``NoPrompt``：不会向标识提供程序发送任何提示。 此选项仅适用于 Azure AD B2C 编辑配置文件策略（请参阅[B2C 细节](https://aka.ms/msal-net-b2c-specificities)）。

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

如果希望用户提前预先同意多个资源，并且不希望使用在 MSAL.NET/Microsoft 标识平台中通常使用的增量许可，则可以使用此修饰符。 有关详细信息，请参阅[操作方法：为多个资源提前许可用户](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources)。

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Web UI 是用于调用浏览器的机制。 此机制可以是专用的 UI WebBrowser 控件或委托打开浏览器的方法。
MSAL 为大多数平台提供 Web UI 实现，但在某些情况下，可能还需要自行托管浏览器： 

- MSAL 未显式覆盖的平台，例如 Blazor、Unity、单色 on desktop
- 需要对应用程序进行 UI 测试，并想要使用可用于 Selenium 的自动浏览器 
- 浏览器和运行 MSAL 的应用程序在单独的进程中

##### <a name="at-a-glance"></a>概览

若要实现此目的，你将 MSAL 一个 `start Url`，这需要在所选的浏览器中显示，以便最终用户可以输入其用户名等。身份验证完成后，你的应用将需要传回 `end Url`，其中包含 Azure AD 提供的代码。
`end Url` 的宿主始终是 `redirectUri`。 若要拦截 `end Url` 您可以： 

- 监视浏览器重定向，直到命中 `redirect Url` 或
- 让浏览器重定向到你监视的 URL

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi 是一个扩展点

`WithCustomWebUi` 是一种扩展点，允许你在公共客户端应用程序中提供自己的 UI，并让用户浏览标识提供者的/Authorize 终结点，并让他们登录和同意。 然后，MSAL.NET 可以兑换身份验证代码并获取令牌。 这适用于 Visual Studio 中使用的电子应用程序（例如，VS 反馈）提供 web 交互，但将其保留在 MSAL.NET 完成大部分工作。 如果要提供 UI 自动化，还可以使用此方法。 在公共客户端应用程序中，MSAL.NET 使用 PKCE 标准（[RFC 7636-证明密钥由 OAuth 公共客户端进行代码交换](https://tools.ietf.org/html/rfc7636)），以确保遵守安全性：只有 MSAL.NET 才能兑换代码。

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>如何使用 WithCustomWebUi

若要使用 `.WithCustomWebUI`，需要执行以下操作：
  
  1. 实现 `ICustomWebUi` 接口（请参阅[此处](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)。 你基本上需要实现一种方法 `AcquireAuthorizationCodeAsync` 接受授权代码 URL （由 MSAL.NET 计算），让用户经历与标识提供者的交互，然后返回标识提供者所用的 URL重新调用实现（包括授权代码）。 如果遇到问题，则实现应引发 `MsalExtensionException` 异常，以便与 MSAL 完美合作。
  2. 在 `AcquireTokenInteractive` 调用中，可以使用 `.WithCustomUI()` 修饰符传递自定义 web UI 的实例

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>测试自动化中 ICustomWebUi 的实现示例-SeleniumWebUI

MSAL.NET 团队已经重写了我们的 UI 测试，以利用此扩展性机制。 如果你感兴趣，可以查看 MSAL.NET 源代码中的[SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160)类

##### <a name="providing-a-great-experience-with-systemwebviewoptions"></a>使用 SystemWebViewOptions 提供出色的体验

通过 MSAL.NET 4.1 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet) ，你可以指定：

- 要导航到的 URI （`BrowserRedirectError`），或在系统 web 浏览器中出现登录/同意错误时要显示的 HTML 片段（`HtmlMessageError`）
- 要导航到的 URI （`BrowserRedirectSuccess`），或在成功登录/同意的情况下要显示的 HTML 片段（`HtmlMessageSuccess`）。
- 要运行以启动系统浏览器的操作。 为此，可以通过设置 `OpenBrowserAsync` 委托来提供自己的实现。 该类还为两个浏览器提供了默认实现： `OpenWithEdgeBrowserAsync` 和 `OpenWithChromeEdgeBrowserAsync`，分别用于 Chromium 上的 Microsoft Edge 和[Microsoft edge](https://www.windowscentral.com/faq-edge-chromium)。

若要使用此结构，可以编写如下所示的内容：

```CSharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>其他可选参数

详细了解[AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)的参考文档中 `AcquireTokenInteractive` 的所有其他可选参数

## <a name="integrated-windows-authentication"></a>集成的 Windows 身份验证

如果要在域或 Azure AD 联接的计算机上登录域用户，则需要使用：

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>约束

- AcquireTokenByIntegratedWindowsAuth （IWA）仅适用于**联合**用户，即，在 Active Directory 中创建并由 Azure Active Directory 提供支持的用户。 直接在 AAD 中创建的用户无需 AD 支持**托管**的用户-不能使用此身份验证流。 此限制不会影响用户名/密码流。
- IWA 适用于为 .NET Framework、.NET Core 和 UWP 平台编写的应用程序
- IWA 不会绕过 MFA （多重身份验证）。 如果已配置 MFA，则在需要 MFA 质询的情况下，IWA 可能失败，因为 MFA 需要用户交互。
  > [!NOTE]
  > 这一点很难。 IWA 是非交互式的，但 MFA 需要用户交互。 不控制标识提供者请求执行 MFA 的时间，租户管理员会执行此操作。 从观察值开始，当你从不同国家/地区进行登录时，需要进行 MFA，而不是通过 VPN 连接到公司网络，有时甚至是通过 VPN 进行连接时。 不需要确定一组规则，Azure Active Directory 使用 AI 来持续了解是否需要 MFA。 如果 IWA 失败，应回退到用户提示（交互式身份验证或设备代码流）。

- 在 `PublicClientApplicationBuilder` 中传递的授权机构需要：
  - 租户-ed （格式 `https://login.microsoftonline.com/{tenant}/`，其中 `tenant` 为表示租户 ID 的 guid 或与租户关联的域。
  - 对于工作和学校帐户（`https://login.microsoftonline.com/organizations/`）
  - 不支持 Microsoft 个人帐户（不能使用/common 或/consumers 租户）

- 因为集成的 Windows 身份验证是一个无提示流：
  - 应用程序的用户必须事先同意使用该应用程序
  - 或者，租户管理员必须事先同意租户中的所有用户使用该应用程序。
  - 换句话说：
    - 作为开发人员，你可以在自己的 Azure 门户上按 "**授权**" 按钮，
    - 或者租户管理员已在应用程序注册的 " **API 权限**" 选项卡中按了 "**授予/撤消管理员许可**" 按钮（请参阅[添加访问 web api 的权限](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis)）
    - 或者你为用户提供了同意应用程序的方式（请参阅[请求单个用户同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent)）
    - 或者你为租户管理员提供了向应用程序授予许可的方式（请参阅[管理员同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant)）

- 对于 .net 桌面、.net core 和 Windows 通用（UWP）应用，将启用此流。 在 .NET core 中，仅可使用带有用户名的重载，因为 .NET Core 平台无法请求提供操作系统的用户名。
  
有关许可的详细信息，请参阅[Microsoft 标识平台权限和同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="how-to-use-it"></a>如何使用

通常只需要一个参数（`scopes`）。 但是，可能不允许 windows 计算机上的应用程序查找已登录的用户，具体取决于 Windows 管理员设置这些策略的方式。 在这种情况下，请使用第二种方法 `.WithUsername()`，并将已登录用户的用户名作为 UPN 格式 `joe@contoso.com`。

下面的示例显示了最新的情况，并说明了可获取的异常类型及其缓解措施

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

有关 AcquireTokenByIntegratedWindowsAuthentication 上可能的修饰符的列表，请参阅[AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="username--password"></a>用户名/密码

还可以通过提供用户名和密码获取令牌。 此流程受到限制，不建议这样做，但仍有必要用到它。

### <a name="this-flow-isnt-recommended"></a>不建议使用此流

**不建议使用**此流，因为你的应用程序要求用户提供其密码并不安全。 有关此问题的详细信息，请参阅[此文](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。 在已加入 Windows 域的计算机上以无提示方式获取令牌的首选流程是[集成的 Windows 身份验证](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication)。 否则，还可以使用[设备代码流](https://aka.ms/msal-net-device-code-flow)

> [!NOTE] 
> 尽管这在某些情况下（DevOps 方案）很有用，但如果你想要在提供自己的 UI 的交互方案中使用用户名/密码，则确实应考虑如何从该位置移走。 通过使用用户名/密码，你将获得多项内容：
>
> - 新式标识的核心租户： password 获取 fished，重播。 因为我们有一个可以被截取的共享机密的概念。
> 这与无密码不兼容。
> - 需要进行 MFA 的用户将无法登录（因为没有交互）
> - 用户无法进行单一登录

### <a name="constraints"></a>约束

以下约束也适用：

- 用户名/密码流与条件性访问和多重身份验证不兼容：因此，如果应用在租户管理员需要多重身份验证的 Azure AD 租户中运行，则不能使用此流。 许多组织都会这样做。
- 它仅适用于工作和学校帐户（而不是 MSA）
- 流在 .net desktop 和 .net core 中可用，但在 UWP 上不可用

### <a name="b2c-specifics"></a>B2C 细节

有关将[ROPC 与 B2C 一起使用的详细信息](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c)。

### <a name="how-to-use-it"></a>如何使用它？

`IPublicClientApplication`包含方法 `AcquireTokenByUsernamePassword`

下面的示例演示简化的情况

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

下面的示例显示了最新的情况，并说明了可获取的异常类型及其缓解措施

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

有关可应用于 `AcquireTokenByUsernamePassword`的所有修饰符的详细信息，请参阅[AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="command-line-tool-without-web-browser"></a>命令行工具（没有 web 浏览器）

### <a name="device-code-flow-why-and-how"></a>设备代码流的原因如何操作？

如果你正在编写命令行工具（没有 Web 控件），并且不能或不想使用以前的流，则需要使用 `AcquireTokenWithDeviceCode`。

使用 Azure AD 进行交互式身份验证需要 web 浏览器（有关详细信息，请参阅[web 浏览器的使用情况](https://aka.ms/msal-net-uses-web-browser)）。 但是，若要在不提供 Web 浏览器的设备或操作系统上对用户进行身份验证，设备代码流允许用户使用其他设备（例如另一台计算机或移动电话）以交互方式登录。 通过使用设备代码流，应用程序将通过两个步骤来获取令牌，此过程尤其适用于这些设备/操作系统。 此类应用程序的示例包括 iOT 上运行的应用程序或命令行工具（CLI）。 其思路是：

1. 只要需要用户身份验证，应用程序就会提供代码，并要求用户使用其他设备（如连接 internet 的智能手机）导航到 URL （例如 `https://microsoft.com/devicelogin`），用户将在其中提示用户输入代码。 完成此操作后，网页将通过一般的身份验证体验（如有必要）来引导用户完成身份验证。

2. 身份验证成功后，命令行应用将通过返回通道接收所需的令牌，并使用它来执行所需的 web API 调用。

### <a name="code"></a>代码

`IPublicClientApplication`包含一个名为 `AcquireTokenWithDeviceCode` 的方法

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

此方法采用作为参数：

- 要为其请求访问令牌的 `scopes`
- 将接收 `DeviceCodeResult` 的回调

  ![image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

下面的示例代码演示了最新的情况，并说明了可获取的异常类型及其缓解措施。

```CSharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] Scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();
           
    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification 

        return await AcquireByDeviceCodeAsync(pca);
    }         
}

private async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using 
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the 
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }
    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are 
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the 
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled. 
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads 
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```

## <a name="file-based-token-cache"></a>基于文件的令牌缓存

在 MSAL.NET 中，默认会提供内存中令牌缓存。

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>序列化可自定义 Windows 桌面应用和 web 应用/web Api

对于 .NET Framework 和 .NET core，如果不执行任何其他操作，内存中令牌缓存将在应用程序持续时间内持续。 若要了解为何不提供序列化，请记住 MSAL .NET 桌面/核心应用程序可以是控制台或 Windows 应用程序（这会有权访问文件系统），**也**可以是 web 应用程序或 web API。 这些 Web 应用和 web Api 可能使用某些特定的缓存机制，如数据库、分布式缓存、redis 缓存等。 若要在 .NET 桌面或核心中使用永久性令牌缓存应用程序，则需要自定义序列化。

令牌缓存序列化中涉及的类和接口是以下类型：

- ``ITokenCache``，用于定义订阅令牌缓存序列化请求的事件，以及用于序列化或反序列化缓存的方法（ADAL 3.0、MSAL 2.x 和 MSAL）。 x = ADAL v 5.0）
- ``TokenCacheCallback`` 是传递给事件的回调，可让你处理序列化。 它们将用 ``TokenCacheNotificationArgs``类型的参数调用。
- ``TokenCacheNotificationArgs`` 仅提供应用程序的 ``ClientId`` 和对其提供令牌的用户的引用

  ![image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET 将为你创建令牌缓存，当你调用应用程序的 `UserTokenCache` 和 `AppTokenCache` 属性时，它会提供 `IToken` 缓存。 您不应自行实现接口。 实现自定义令牌缓存序列化时，你的责任是：
>
> - 对 `BeforeAccess` 和 `AfterAccess` "事件" （或*异步*对应项）做出反应。 `BeforeAccess` 委托负责反序列化缓存，而 `AfterAccess` 一个用于序列化缓存。
> - 其中的一部分事件存储或加载 Blob，这些 Blob 将通过事件参数传递到所需的任何存储。

根据你是为公用客户端应用程序（桌面）还是机密客户端应用程序（web 应用/web API、后台应用）编写令牌缓存序列化，这些策略会有所不同。

由于 MSAL V2. x 可以使用多个选项，具体取决于是否要将缓存仅序列化到 MSAL.NET 格式（与 MSAL 共有的统一格式缓存，以及跨平台），或者是否也要支持[旧](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)的令牌缓存ADAL V3 的序列化。

在以下示例中，将对令牌缓存序列化进行自定义，以共享 ADAL.NET 1.x、ADAL.NET 1.x 和 MSAL.NET 之间的 SSO 状态： [active-dotnet-v1 到 v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>简单令牌缓存序列化（仅限 MSAL）

下面是适用于桌面应用程序的令牌缓存的自定义序列化的简单实现示例。 此处的用户令牌缓存位于与应用程序相同的文件夹中的文件中。

生成应用程序后，可以通过调用 ``TokenCacheHelper.EnableSerialization()`` 传递应用程序来启用序列化 `UserTokenCache`

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

此帮助器类类似于以下代码片段：

```CSharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

有关适用于公用客户端应用程序（适用于 Windows、Mac 和 linux 上运行的桌面应用程序）的产品质量令牌缓存基于文件的序列化程序的预览，请访问[Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal)开源库。 可以将其包含在以下 nuget 包中的应用程序中： [Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/)。

> [!NOTE]
> 否认. Msal 库是 MSAL.NET 上的一个扩展。 这些库中的类可能会在将来的 MSAL.NET 中，或在发生重大更改时进行。

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>双重令牌缓存序列化（MSAL 统一缓存 + ADAL V3）

如果要使用统一缓存格式（通用于 ADAL.NET 4.x 和 MSAL.NET 2.x）实现令牌缓存序列化，并在相同的平台上使用同一代或更早版本的其他 MSALs，可以通过以下代码获得灵感：:

```CSharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

这一次，帮助器类如以下代码所示：

```CSharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [从桌面应用程序调用 web API](scenario-desktop-call-api.md)
