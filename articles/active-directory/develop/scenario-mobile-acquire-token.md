---
title: 获取用于调用 web API （移动应用）的令牌 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何构建调用 web Api 的移动应用程序。 （获取应用的令牌。）
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
ms.openlocfilehash: 9427235f47a31da75426559a4285634ab2837577
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132448"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>获取用于调用 web Api 的移动应用的令牌

在你的应用程序可以调用受保护的 web Api 之前，它需要一个访问令牌。 本文指导你完成使用 Microsoft 身份验证库（MSAL）获取令牌的过程。

## <a name="define-a-scope"></a>定义作用域

请求令牌时，需要定义作用域。 范围决定了你的应用可以访问哪些数据。  

定义作用域的最简单方法是将所需的 web API 的 `App ID URI` 与范围 `.default`组合在一起。 此定义告诉 Microsoft 标识平台，应用需要门户中设置的所有作用域。

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>获取令牌

### <a name="acquire-tokens-via-msal"></a>通过 MSAL 获取令牌

MSAL 使应用能够以无提示方式和交互方式获取令牌。 调用 `AcquireTokenSilent()` 或 `AcquireTokenInteractive()`时，MSAL 将为请求的作用域返回访问令牌。 正确的模式是发出无提示请求，然后回退到交互式请求。

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
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

首先尝试以静默方式获取令牌：

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

如果 MSAL 返回 `MSALErrorInteractionRequired`，则尝试以交互方式获取令牌：

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

适用于 iOS 和 macOS 的 MSAL 支持使用各种修饰符以交互方式或无提示地获取令牌：
* [用于获取令牌的常见参数](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [用于获取交互式令牌的参数](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [用于获取缄默标记的参数](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

下面的示例演示了以交互方式获取令牌的最小代码。 该示例使用 Microsoft Graph 读取用户的配置文件。

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

`AcquireTokenInteractive` 只有一个必需参数： `scopes`。 `scopes` 参数枚举字符串，这些字符串定义需要令牌的作用域。 如果令牌用于 Microsoft Graph，则可以在每个 Microsoft Graph API 的 API 引用中找到所需的作用域。 在引用中，请参阅 "权限" 部分。 

例如，若要[列出用户的联系人](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)，请使用作用域 "用户读取"、"联系人"。 有关详细信息，请参阅[Microsoft Graph 权限参考](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)。

在 Android 上，你可以在使用 `PublicClientApplicationBuilder`创建应用时指定父活动。 如果此时未指定父活动，则可以在以后使用 `.WithParentActivityOrWindow` 来指定它，如以下部分中所示。 如果指定父活动，则在交互后，该令牌将返回到该父活动。 如果未指定，则 `.ExecuteAsync()` 调用将引发异常。

#### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET 中的特定可选参数

以下部分介绍了 MSAL.NET 中的可选参数。 

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()` 参数通过指定 prompt 来控制与用户的交互。

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

类定义以下常量：

- `SelectAccount` 强制 security token service （STS）显示 "帐户选择" 对话框。 此对话框包含用户为其提供会话的帐户。 如果希望允许用户在不同标识之间进行选择，则可以使用此选项。 此选项驱动 MSAL 将 `prompt=select_account` 发送到标识提供者。 
    
    `SelectAccount` 常量是默认值，它可以根据可用信息有效地提供可能的最佳体验。 可用的信息可能包括帐户、用户的会话状态等。 请勿更改此默认设置，除非你有充分的理由。
- `Consent` 使你可以提示用户同意（即使之前同意）。 在这种情况下，MSAL 会将 `prompt=consent` 发送到标识提供者。 

    你可能想要在注重安全的应用程序中使用 `Consent` 常量，其中组织监管要求用户在每次使用应用程序时都可以看到同意对话框。
- `ForceLogin` 使服务能够提示用户提供凭据，即使不需要提示也是如此。 

    如果令牌获取失败并且你想让用户重新登录，则此选项会很有用。 在这种情况下，MSAL 会将 `prompt=login` 发送到标识提供者。 你可能想要在注重安全的应用程序中使用此选项，在这种应用程序中，组织治理要求用户在每次访问应用程序的特定部分时登录。
- `Never` 仅适用于 .NET 4.5 和 Windows 运行时（WinRT）。 此常量不会提示用户，但会尝试使用存储在隐藏的嵌入式 web 视图中的 cookie。 有关详细信息，请参阅[使用 MSAL.NET 的 web 浏览器](https://docs.microsoft.com/azure/active-directory/develop/msal-net-web-browsers)。

    如果此选项失败，则 `AcquireTokenInteractive` 引发异常，通知你需要 UI 交互。 然后，需要使用另一个 `Prompt` 参数。
- `NoPrompt` 不会向标识提供程序发送提示。 

    此选项仅适用于 Azure Active Directory B2C 中的编辑配置文件策略。 有关详细信息，请参阅[B2C 细节](https://aka.ms/msal-net-b2c-specificities)。

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

如果希望用户对多个资源提供提前同意，请在高级方案中使用 `WithExtraScopeToConsent` 修饰符。 如果不想使用增量许可，则可以使用此修饰符，这通常与 MSAL.NET 或 Microsoft 标识平台2.0 一起使用。 有关详细信息，请参阅[让用户提前获取几个资源](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)。

下面是一个代码示例： 

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>其他可选参数

若要了解 `AcquireTokenInteractive`的其他可选参数，请参阅[AcquireTokenInteractiveParameterBuilder 的参考文档](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)。

### <a name="acquire-tokens-via-the-protocol"></a>通过协议获取令牌

建议不要直接使用协议来获取令牌。 如果执行此操作，则应用不支持涉及单一登录（SSO）、设备管理和条件性访问的某些方案。

使用协议获取移动应用的令牌时，请发出两个请求： 

* 获取授权代码。
* 交换令牌的代码。

#### <a name="get-an-authorization-code"></a>获取授权代码

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>获取访问权限并刷新令牌

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
