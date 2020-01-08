---
title: 获取用于调用 web API （移动应用）的令牌 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何生成调用 web Api （获取应用的令牌）的移动应用
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
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d584dc2eef27747627133511af3defe085068ab
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423823"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>用于调用 web Api 的移动应用-获取令牌

在开始调用受保护的 web Api 之前，你的应用将需要一个访问令牌。 本文指导完成使用 Microsoft 身份验证库（MSAL）获取令牌的过程。

## <a name="scopes-to-request"></a>要请求的范围

请求令牌时，需要定义作用域。 范围决定了你的应用可以访问哪些数据。  

最简单的方法是将所需的 web API `App ID URI` 与范围 `.default`组合在一起。 这样做会告诉 Microsoft 标识平台，你的应用需要门户中设置的所有作用域。

#### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

#### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

#### <a name="xamarin"></a>Xamarin
```csharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>获取令牌

### <a name="acquire-tokens-via-msal"></a>通过 MSAL 获取令牌

MSAL 使应用能够以无提示方式和交互方式获取令牌。 只需调用这些方法，MSAL 将为请求的作用域返回一个访问令牌。 正确的模式是执行无提示请求，并回退到交互式请求。

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});    

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed request.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

**首先尝试以静默方式获取令牌：**

Objective-C：

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";
    
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];
    
MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {
        
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        // Access token to call the Web API
        NSString *accessToken = result.accessToken;
    }
        
    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```
 
Swift：

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"
        
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
            
    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier
            
    // Access token to call the Web API
    let accessToken = authResult.accessToken
}
```

**然后，如果 MSAL 返回 `MSALErrorInteractionRequired`，请尝试以交互方式获取令牌：**

Objective-C：

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithParentViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
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
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(parentViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in
                
    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }
                
    // Get access token from result
    let accessToken = authResult.accessToken
})
```

MSAL for iOS 和 macOS 支持以交互方式或无提示方式获取令牌时使用各种修饰符。
* [获取令牌时的通用参数](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [用于获取交互式令牌的参数](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [用于无提示令牌获取的参数](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

下面的示例演示了使用 Microsoft Graph 以交互方式获取用于读取用户配置文件的令牌的最小代码。

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

#### <a name="mandatory-parameters-in-msalnet"></a>MSAL.NET 中的强制参数

`AcquireTokenInteractive` 只有一个必需参数 ``scopes``，该参数包含用于定义需要令牌的范围的字符串的枚举。 如果令牌用于 Microsoft Graph，则可以在名为 "权限" 的部分中的每个 Microsoft Graph API 的 api 引用中找到所需的作用域。 例如，若要[列出用户的联系人](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)，则需要使用作用域 "User. read"、"contacts"。 另请参阅[Microsoft Graph 权限参考](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)。

如果在生成应用时未指定此项，则在 Android 上，你还需要指定父活动（使用 `.WithParentActivityOrWindow`，请参阅下文），以便在交互后将令牌返回到该父活动。 如果不指定它，则在调用 `.ExecuteAsync()`时将引发异常。

#### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET 中的特定可选参数

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()` 用于通过指定提示来控制与用户的交互

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

类定义以下常量：

- ``SelectAccount``：将强制 STS 显示包含用户为其会话的帐户的帐户选择对话框。 当应用程序开发人员希望允许用户在不同标识之间进行选择时，此选项很有用。 此选项驱动 MSAL 将 ``prompt=select_account`` 发送到标识提供者。 此选项是默认选项，它可以根据可用信息（帐户、用户的会话状态等）提供最佳体验，从而提供最佳体验。 ...).不要对其进行更改，除非你有充分的理由。
- ``Consent``：允许应用程序开发人员强制提示用户同意，即使之前已授予同意也是如此。 在这种情况下，MSAL 会将 `prompt=consent` 发送到标识提供者。 此选项可用于某些注重安全的应用程序，在这些应用程序中，组织监管要求用户在每次使用应用程序时都显示许可对话框。
- ``ForceLogin``：允许应用程序开发人员通过服务提示用户提供凭据，即使不需要此用户提示也是如此。 如果获取令牌失败，让用户重新登录，则此选项很有用。 在这种情况下，MSAL 会将 `prompt=login` 发送到标识提供者。 同样，我们已了解到它在某些注重安全的应用程序中使用，在这些应用程序中，组织治理要求用户在每次访问应用程序的特定部分时 relogs。
- ``Never`` （仅适用于 .NET 4.5 和 WinRT）不会提示用户，而是尝试使用隐藏的嵌入式 web 视图中存储的 cookie （请参阅以下内容： MSAL.NET 中的 Web 视图）。 使用此选项可能会失败，并且在这种情况下 `AcquireTokenInteractive` 将引发异常以通知需要 UI 交互，你将需要使用另一个 `Prompt` 参数。
- ``NoPrompt``：不会向标识提供程序发送任何提示。 此选项仅适用于 Azure AD B2C 编辑配置文件策略（请参阅[B2C 细节](https://aka.ms/msal-net-b2c-specificities)）。

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

此修饰符用于您希望用户提前预先同意多个资源的高级方案（并且不想使用通常与 MSAL.NET/Microsoft identity platform v2.0 一起使用的增量许可）。 有关详细信息，请参阅[操作方法：为多个资源提前许可用户](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources)。

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>其他可选参数

详细了解[AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)的参考文档中 `AcquireTokenInteractive` 的所有其他可选参数

### <a name="acquire-tokens-via-the-protocol"></a>通过协议获取令牌

建议不要直接使用协议。 如果执行此操作，则应用不支持某些单一登录（SSO）、设备管理和条件性访问方案。

使用协议获取移动应用的令牌时，需要发出两个请求：获取授权代码，并将其与令牌交换。

#### <a name="get-authorization-code"></a>获取授权代码

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-token"></a>获取访问和刷新令牌

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [调用 web API](scenario-mobile-call-api.md)
