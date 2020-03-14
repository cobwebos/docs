---
title: 获取用于调用 web API 的令牌（桌面应用） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何构建一个桌面应用程序，该应用程序调用 web Api 以获取应用程序的令牌
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
ms.openlocfilehash: 636c7c654b98ced5f93c3ace0e4a99bfc9bf7def
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262602"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>用于调用 web Api 的桌面应用：获取令牌

生成公共客户端应用程序的实例后，你将使用它来获取用于调用 web API 的令牌。

## <a name="recommended-pattern"></a>推荐的模式

Web API 由其 `scopes`定义。 无论你在应用程序中提供何种体验，要使用的模式如下：

- 通过调用 `AcquireTokenSilent`，系统尝试从令牌缓存获取令牌。
- 如果此调用失败，请使用想要使用的 `AcquireToken` 流，`AcquireTokenXX`在此处表示。

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>在 MSAL.NET 中

```csharp
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

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macos"></a>[MacOS](#tab/macOS)

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
---

下面是在桌面应用程序中获取令牌的各种方法。

## <a name="acquire-a-token-interactively"></a>以交互方式获取令牌

下面的示例演示了使用 Microsoft Graph 以交互方式获取用于读取用户配置文件的令牌的最小代码。

# <a name="net"></a>[.NET](#tab/dotnet)
### <a name="in-msalnet"></a>在 MSAL.NET 中

```csharp
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

### <a name="mandatory-parameters"></a>必需参数

`AcquireTokenInteractive` 只有一个必需参数，``scopes``，它包含定义需要令牌的范围的字符串的枚举。 如果令牌用于 Microsoft Graph，则可以在 "权限" 一节中的每个 Microsoft Graph API 的 API 引用中找到所需的作用域。 例如，若要[列出用户的联系人](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)，必须使用范围 "User. read"、"contacts"。 有关详细信息，请参阅[Microsoft Graph 权限参考](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)。

在 Android 上，你还需要使用 `.WithParentActivityOrWindow`指定父活动，如下所示，以便在交互后将令牌返回到该父活动。 如果不指定它，则在调用 `.ExecuteAsync()`时将引发异常。

### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET 中的特定可选参数

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

UI 非常重要，因为它是交互式的。 `AcquireTokenInteractive` 具有一个特定的可选参数，该参数可为支持该参数的平台（父 UI）指定。 在桌面应用程序中使用时，`.WithParentActivityOrWindow` 具有不同的类型，具体取决于平台。

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

备注：

- 在 .NET Standard 上，预期的 `object` 在 Android 上 `Activity`，在 iOS 上 `UIViewController`，在 MAC 上 `NSWindow`，在 Windows 上 `IWin32Window` 或 `IntPr`。
- 在 Windows 上，必须从 UI 线程调用 `AcquireTokenInteractive`，以便嵌入浏览器获取适当的 UI 同步上下文。 不从 UI 线程调用可能会导致消息无法与 UI 一起正确抽取和死锁情况。 如果你不在 UI 线程上，从 UI 线程调用 Microsoft 身份验证库（MSALs）的一种方法是使用 WPF 上的 `Dispatcher`。
- 如果你使用的是 WPF，若要从 WPF 控件获取窗口，你可以使用 `WindowInteropHelper.Handle` 类。 然后，调用来自 WPF 控件（`this`）：

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` 用于通过指定提示来控制与用户的交互。

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

类定义以下常量：

- ``SelectAccount`` 强制 STS 显示包含用户有会话的帐户的帐户选择对话框。 当应用程序开发人员希望允许用户在不同标识之间进行选择时，此选项很有用。 此选项驱动 MSAL 将 ``prompt=select_account`` 发送到标识提供者。 此选项为默认值。 根据可用的信息（例如，用户的会话的帐户和状态）提供最佳体验，这种做法非常有用。 不要对其进行更改，除非你有充分的理由。
- ``Consent`` 使应用程序开发人员可以强制提示用户同意，即使之前已授予同意也是如此。 在这种情况下，MSAL 会将 `prompt=consent` 发送到标识提供者。 此选项可用于某些以安全为中心的应用程序，在这些应用程序中，组织监管要求用户在每次使用应用程序时都向用户显示许可对话框。
- ``ForceLogin`` 使应用程序开发人员能够通过服务提示用户提供凭据，即使不需要此用户提示也是如此。 此选项可用于让用户在获取令牌失败时再次登录。 在这种情况下，MSAL 会将 `prompt=login` 发送到标识提供者。 有时，它用于以安全为中心的应用程序，在这些应用程序中，组织的监管要求用户在每次访问应用程序的特定部分时进行重新签名。
- ``Never`` （仅适用于 .NET 4.5 和 WinRT）不会提示用户，而是尝试使用隐藏的嵌入式 web 视图中存储的 cookie。 有关详细信息，请参阅 MSAL.NET 中的 web 视图。 使用此选项可能会失败。 在这种情况下，`AcquireTokenInteractive` 引发异常，以通知需要 UI 交互。 需要使用另一个 `Prompt` 参数。
- ``NoPrompt`` 不会向标识提供程序发送任何提示。 此选项仅适用于 Azure Active Directory （Azure AD） B2C 编辑配置文件策略。 有关详细信息，请参阅[Azure AD B2C 详细](https://aka.ms/msal-net-b2c-specificities)信息。

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

如果希望用户提前预先同意多个资源，并且不希望使用在 MSAL.NET/the Microsoft 标识平台中通常使用的增量许可，则可以使用此修饰符。 有关详细信息，请参阅[让用户提前获取几个资源](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)。

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Web UI 是用于调用浏览器的机制。 此机制可以是专用的 UI WebBrowser 控件或委托打开浏览器的方法。
MSAL 为大多数平台提供 web UI 实现，但在某些情况下，你可能想要自行托管浏览器：

- MSAL 未明确覆盖的平台，例如，Blazor、Unity 和桌面上的 Mono。
- 需要对应用程序进行 UI 测试，并使用可用于 Selenium 的自动浏览器。
- 浏览器和运行 MSAL 的应用程序在单独的进程中。

##### <a name="at-a-glance"></a>速览

若要实现此目的，需要将 MSAL `start Url`，这需要在所选的浏览器中显示，以便最终用户可以输入项（如其用户名）。
身份验证完成后，应用需要传递回 MSAL `end Url`，其中包含 Azure AD 提供的代码。
始终 `redirectUri``end Url` 的主机。 若要截获 `end Url`，请执行以下操作之一：

- 监视浏览器重定向到 `redirect Url` 命中。
- 让浏览器重定向到您所监视的 URL。

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi 是一个扩展点

`WithCustomWebUi` 是一种扩展点，可用于在公共客户端应用程序中提供自己的 UI。 你还可以让用户经历标识提供者的/Authorize 终结点，并让他们登录并同意。 然后，MSAL.NET 可以兑换身份验证代码并获取令牌。 例如，在 Visual Studio 中使用它来使电子的应用程序（例如，Visual Studio 反馈）提供 web 交互，但将其保留在 MSAL.NET 以完成大部分工作。 如果要提供 UI 自动化，还可以使用此方法。 在公共客户端应用程序中，MSAL.NET 使用代码交换（PKCE）标准的证明密钥来确保遵守安全性。 只有 MSAL.NET 才能兑换代码。 有关详细信息，请参阅[RFC 7636-验证密钥以通过 OAuth 公共客户端进行代码交换](https://tools.ietf.org/html/rfc7636)。

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>使用 WithCustomWebUi

若要使用 `.WithCustomWebUI`，请执行以下步骤。

  1. 实现 `ICustomWebUi` 接口。 有关详细信息，请参阅[此网站](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)。 实现一个 `AcquireAuthorizationCodeAsync`方法，并接受由 MSAL.NET 计算的授权代码 URL。 然后，让用户经历与标识提供者的交互，并返回标识提供者与授权代码一起调用了实现的 URL。 如果遇到问题，则实现应引发 `MsalExtensionException` 异常，以便与 MSAL 完美合作。
  2. 在 `AcquireTokenInteractive` 调用中，使用 `.WithCustomUI()` 修饰符传递自定义 web UI 的实例。

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>测试自动化中 ICustomWebUi 的实现示例： SeleniumWebUI

MSAL.NET 团队已重写 UI 测试，以使用此扩展性机制。 如果感兴趣，请查看 MSAL.NET 源代码中的[SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160)类。

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>使用 SystemWebViewOptions 提供出色的体验

从 MSAL.NET 4.1 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet)中，可以指定：

- 要在系统 web 浏览器中登录或同意错误时要显示的 URI （`BrowserRedirectError`）或 HTML 片段（`HtmlMessageError`）。
- 要在成功登录或同意的情况下要显示的 URI （`BrowserRedirectSuccess`）或 HTML 片段（`HtmlMessageSuccess`）。
- 要运行以启动系统浏览器的操作。 可以通过设置 `OpenBrowserAsync` 委托来提供自己的实现。 该类还提供两个浏览器的默认实现：分别 `OpenWithEdgeBrowserAsync` 和 `OpenWithChromeEdgeBrowserAsync` 用于 Chromium 上的 Microsoft Edge 和[Microsoft edge](https://www.windowscentral.com/faq-edge-chromium)。

若要使用此结构，请编写类似于以下示例的内容：

```csharp
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

若要详细了解 `AcquireTokenInteractive`的其他可选参数，请参阅[AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)。

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python 不会直接提供交互式获取令牌方法。 相反，它要求应用程序在其用户交互流实现中发送授权请求，以获取授权代码。 然后，可以将此代码传递到 `acquire_token_by_authorization_code` 方法以获取令牌。

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])    

```

# <a name="macos"></a>[MacOS](#tab/macOS)

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
---

## <a name="integrated-windows-authentication"></a>Windows 集成身份验证

若要在域中或 Azure AD 加入的计算机上登录域用户，请使用集成 Windows 身份验证（IWA）。

### <a name="constraints"></a>约束

- 集成的 Windows 身份验证仅适用于*联合 +* 用户，也就是说，在 Active Directory 中创建的用户和 Azure AD 提供支持的用户。 直接在 Active Directory Azure AD 中创建的用户（称为*托管*用户）不能使用此身份验证流。 此限制不会影响 "用户名" 和 "密码" 流。
- IWA 适用于针对 .NET Framework、.NET Core 和通用 Windows 平台（UWP）平台编写的应用。
- IWA 不会绕过多重身份验证（MFA）。 如果已配置 MFA，则在需要 MFA 质询的情况下，IWA 可能失败，因为 MFA 需要用户交互。
  > [!NOTE]
  > 这一点很难。 IWA 是非交互式的，但 MFA 需要用户交互。 不控制标识提供者请求执行 MFA 的时间，租户管理员会执行此工作。 从我们的观察，当你从不同的国家/地区（未通过 VPN 连接到公司网络，有时甚至是通过 VPN 连接时）登录时，需要进行 MFA。 不需要确定的一组规则。 Azure AD 使用 AI 持续了解是否需要 MFA。 如果 IWA 失败，则回退到用户提示（如交互身份验证或设备代码流）。

- 传入的授权机构 `PublicClientApplicationBuilder` 需要：
  - 格式 `https://login.microsoftonline.com/{tenant}/`的租户，其中 `tenant` 为表示租户 ID 的 GUID 或与租户关联的域。
  - 对于工作和学校帐户： `https://login.microsoftonline.com/organizations/`。
  - 不支持 Microsoft 个人帐户。 不能使用/common 或/consumers 租户。

- 因为集成的 Windows 身份验证是一个无提示流：
  - 应用程序的用户必须事先同意使用该应用程序。
  - 或者，租户管理员以前必须同意租户中的所有用户使用该应用程序。
  - 换句话说：
    - 作为开发人员，你可以在自己的 Azure 门户中选择 "**授权**" 按钮。
    - 或者，租户管理员在应用程序注册的 " **API 权限**" 选项卡上选择了 "**授予/撤消管理员许可**" 按钮。 有关详细信息，请参阅[添加访问 Web api 的权限](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis)。
    - 或者，你为用户提供了同意应用程序的方式。 有关详细信息，请参阅[请求单个用户同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent)。
    - 或者，你为租户管理员提供了一种同意应用程序的方式。 有关详细信息，请参阅[管理员同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant)。

- 对于 .NET 桌面、.NET Core 和 UWP 应用，会启用此流。

有关许可的详细信息，请参阅[Microsoft 标识平台权限和许可](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)。

### <a name="learn-how-to-use-it"></a>了解其用法

# <a name="net"></a>[.NET](#tab/dotnet)

在 MSAL.NET 中，需要使用：

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

通常只需要一个参数（`scopes`）。 Windows 计算机上的应用程序可能无法在 Windows 计算机上查找已登录的用户，具体取决于 Windows 管理员设置策略的方式。 在这种情况下，请使用第二种方法，`.WithUsername()`，并传入作为 UPN 格式的已登录用户的用户名，例如 `joe@contoso.com`。 在 .NET Core 中，仅使用带有用户名的重载是可用的，因为 .NET Core 平台无法请求用户名到 OS。

下面的示例显示了最新的情况，并说明了可获取的异常类型及其缓解措施。

```csharp
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

有关 AcquireTokenByIntegratedWindowsAuthentication 上可能的修饰符的列表，请参阅[AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)。

# <a name="java"></a>[Java](#tab/java)

此摘录来自[MSAL Java dev 示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)。

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

此流尚不受 MSAL Python 支持。

# <a name="macos"></a>[MacOS](#tab/macOS)

此流不适用于 MacOS。

---

## <a name="username-and-password"></a>用户名和密码

还可以通过提供用户名和密码获取令牌。 此流程受到限制，不建议这样做，但仍有必要用到它。

### <a name="this-flow-isnt-recommended"></a>不建议使用此流

*不建议使用*此流，因为应用程序要求用户提供其密码并不安全。 有关详细信息，请参阅[不断增长的密码问题的解决方案？](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。 在已加入 Windows 域的计算机上以无提示方式获取令牌的首选流程是[集成的 Windows 身份验证](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication)。 你还可以使用[设备代码流](https://aka.ms/msal-net-device-code-flow)。

> [!NOTE]
> 在某些情况下，使用用户名和密码非常有用，例如 DevOps 方案。 但是，如果你想要在提供自己的 UI 的交互式方案中使用用户名和密码，请考虑如何从该位置移走。 通过使用用户名和密码，你将获得多项内容：
>
> - 新式标识的核心原则。 密码可以钓鱼和重播，因为共享机密可以被截取。 它与无密码不兼容。
> - 需要进行 MFA 的用户无法登录，因为没有交互。
> - 用户不能执行单一登录（SSO）。

### <a name="constraints"></a>约束

以下约束也适用：

- 用户名和密码流与条件性访问和多重身份验证不兼容。 因此，如果你的应用程序在租户管理员需要多重身份验证的 Azure AD 租户中运行，则无法使用此流。 许多组织都会这样做。
- 它仅适用于工作和学校帐户（而不是 MSA）。
- 流在 .NET desktop 和 .NET Core 中可用，但在 UWP 上不可用。

### <a name="b2c-specifics"></a>B2C 细节

有关详细信息，请参阅[具有 B2C 的资源所有者密码凭据（ROPC）](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c)。

### <a name="use-it"></a>使用

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`包含 `AcquireTokenByUsernamePassword`的方法。

下面的示例演示了一个简化的情况。

```csharp
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

下面的示例显示了最新的情况，并说明了可获取的异常类型及其缓解措施。

```csharp
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

有关可应用于 `AcquireTokenByUsernamePassword`的所有修饰符的详细信息，请参阅[AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)。

# <a name="java"></a>[Java](#tab/java)

以下摘录来自[MSAL Java dev 示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)。

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

此摘录来自[MSAL Python 开发人员示例](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)。

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

# <a name="macos"></a>[MacOS](#tab/macOS)

MacOS 的 MSAL 不支持此流程。

---

## <a name="command-line-tool-without-a-web-browser"></a>无需 web 浏览器的命令行工具

### <a name="device-code-flow"></a>设备代码流

如果你正在编写的命令行工具没有 web 控件，并且你不能或不想使用以前的流，则需要使用设备代码流。

使用 Azure AD 进行交互式身份验证需要使用 web 浏览器。 有关详细信息，请参阅[web 浏览器的使用情况](https://aka.ms/msal-net-uses-web-browser)。 若要在不提供 web 浏览器的设备或操作系统上对用户进行身份验证，设备代码流允许用户使用其他设备（如计算机或移动电话）以交互方式登录。 通过使用设备代码流，应用程序将通过为这些设备或操作系统设计的由两个步骤组成的过程获取令牌。 此类应用程序的示例是在 iOT 或命令行工具（CLI）上运行的应用程序。 其思路是：

1. 只要需要用户身份验证，应用程序就会为用户提供代码。 要求用户使用其他设备（如连接 internet 的智能手机）来访问 URL，例如 `https://microsoft.com/devicelogin`。 然后，系统会提示用户输入代码。 完成此操作后，网页将通过普通的身份验证体验，其中包括同意提示和多重身份验证（如有必要）。

2. 身份验证成功后，命令行应用程序通过后端通道接收所需的令牌，并使用它们来执行所需的 web API 调用。

### <a name="use-it"></a>使用

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`包含一个名为 `AcquireTokenWithDeviceCode`的方法。

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

此方法采用作为参数：

- 要为其请求访问令牌的 `scopes`。
- 接收 `DeviceCodeResult`的回调。

  ![DeviceCodeResult 属性](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

下面的示例代码演示了最新的情况，并说明了可获取的异常类型及其缓解措施。

```csharp
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
# <a name="java"></a>[Java](#tab/java)

此摘录来自[MSAL Java dev 示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)。

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

此摘录来自[MSAL Python 开发人员示例](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)。

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

# <a name="macos"></a>[MacOS](#tab/macOS)

此流不适用于 MacOS。

---

## <a name="file-based-token-cache"></a>基于文件的令牌缓存

在 MSAL.NET 中，默认会提供内存中令牌缓存。

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>序列化可自定义 Windows 桌面应用和 web 应用或 web Api

对于 .NET Framework 和 .NET Core，如果不执行任何其他操作，内存中令牌缓存将在应用程序持续时间内持续。 若要了解为何不提供序列化，请记住，MSAL .NET desktop 或 .NET Core 应用程序可以是控制台或 Windows 应用程序（这会有权访问文件系统），*也*可以是 web 应用程序或 web api。 这些 web 应用和 web Api 可能使用某些特定的缓存机制，如数据库、分布式缓存和 Redis 缓存。 若要在 .NET desktop 或 .NET Core 中使用永久性令牌缓存应用程序，则需要自定义序列化。

令牌缓存序列化中涉及的类和接口是以下类型：

- ``ITokenCache``，用于定义订阅令牌缓存序列化请求的事件，以及用于序列化或反序列化缓存的方法（ADAL 3.0、MSAL 2.x 和 MSAL）。
- ``TokenCacheCallback`` 是传递给事件的回调，可让你处理序列化。 将结合 ``TokenCacheNotificationArgs`` 类型的参数调用它们。
- ``TokenCacheNotificationArgs`` 仅向应用程序提供 ``ClientId`` 以及对该令牌可用的用户的引用。

  ![令牌缓存序列化关系图](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET 将为你创建令牌缓存，当你调用应用程序的 `IToken` 和 `UserTokenCache` 属性时，它会提供 `AppTokenCache` 缓存。 您不应自行实现接口。 实现自定义令牌缓存序列化时，你的责任是：
>
> - 响应 `BeforeAccess` 和 `AfterAccess` 事件或其*异步*对应项。 `BeforeAccess` 委托负责反序列化缓存。 `AfterAccess` 委托负责序列化缓存。
> - 了解这些事件的一部分存储或加载 blob，它们通过事件参数传递到所需的任何存储。

根据你是为公共客户端应用程序（如桌面）或机密客户端应用程序（如 web 应用程序、web API 或守护程序应用程序）编写令牌缓存序列化，这些策略会有所不同。

从 MSAL v2. x 开始，有几个选项。 你的选择取决于你是否希望将缓存序列化到 MSAL.NET 格式，这是一种与 MSAL 常用但跨平台的统一格式缓存。 或者，你可能还需要支持 ADAL v3 的[旧](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)令牌缓存序列化。

用于在 ADAL.NET 1.x、ADAL.NET 1.x 和 MSAL.NET 之间共享 SSO 状态的令牌缓存序列化的自定义在示例[active-dotnet-v1 到 v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)中进行了介绍。

### <a name="simple-token-cache-serialization-msal-only"></a>简单令牌缓存序列化（仅限 MSAL）

下面的示例是对桌面应用程序的令牌缓存的自定义序列化的简单实现。 此处，用户令牌缓存位于与应用程序相同的文件夹中的文件中。

生成应用程序后，可以通过调用 ``TokenCacheHelper.EnableSerialization()`` 并将应用程序传递 `UserTokenCache`来启用序列化。

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

此帮助器类类似于以下代码片段：

```csharp
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

针对 Windows、Mac 和 Linux 上运行的桌面应用程序的适用于公用客户端应用程序的产品质量令牌缓存基于文件的序列化程序的预览可从[Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal)开放源代码库获取。 可以将其包含在以下 NuGet 包中的应用程序中： [Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/)。

> [!NOTE]
> 免责声明： Msal 库是 MSAL.NET 上的一个扩展。 这些库中的类可能会在将来的 MSAL.NET 中，或在发生重大更改时进行。

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>双重令牌缓存序列化（MSAL 统一缓存 + ADAL v3）

你可能想要采用统一缓存格式实现令牌缓存序列化。 此格式在同一平台上 ADAL.NET 4.x 和 MSAL.NET 2.x 以及同一代或更低版本的其他 MSALs 通用。 通过以下代码获得灵感：

```csharp
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

```csharp
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
