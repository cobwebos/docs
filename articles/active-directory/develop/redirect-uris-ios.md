---
title: 将重定向 URI 与 MSAL (iOS/macOS) 配合使用 | Azure
titleSuffix: Microsoft identity platform
description: 了解用于 ObjectiveC 的 Microsoft 身份验证库（用于 iOS 和 macOS 的 MSAL）和用于 ObjectiveC 的 Azure AD 身份验证库 (ADAL.ObjC) 之间的差异，以及如何在其间进行迁移。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev
ms.openlocfilehash: 1291563a39e3cf3acd4b343302be8b150bf794ca
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80883502"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>将重定向 URI 与用于 iOS 和 macOS 的 Microsoft 身份验证库配合使用

当用户进行身份验证时，Azure Active Directory (Azure AD) 会将令牌发送到应用，方法是使用注册到 Azure AD 应用程序的重定向 URI。

Microsoft 身份验证库 (MSAL) 要求重定向 URI 按特定格式注册到 Azure AD 应用。 在未指定重定向 URI 的情况下，MSAL 使用默认的。 格式为 `msauth.[Your_Bundle_Id]://auth`。

默认的重定向 URI 格式适用于大多数应用和方案，包括代理身份验证和系统 Web 视图。 尽可能使用默认格式。

但是，你可能需要为高级方案更改重定向 URI，如下所述。

## <a name="scenarios-that-require-a-different-redirect-uri"></a>需要其他重定向 URI 的方案

### <a name="cross-app-single-sign-on-sso"></a>跨应用单一登录 (SSO)

如果希望 Microsoft 标识平台跨应用共享令牌，每个应用都需要有相同的客户端 ID 或应用程序 ID。 这是在门户中注册应用时系统提供的唯一标识符（不是按应用注册到 Apple 时的应用程序捆绑 ID）。

每个 iOS 应用的重定向 URI 必须是不同的。 这样 Microsoft 标识服务才能唯一标识共享某个应用程序 ID 的不同应用。 每个应用程序可以在 Azure 门户中注册多个重定向 URI。 套件中的每个应用程序具有不同的重定向 URI。 例如：

在 Azure 门户中进行以下应用程序注册时：

    Client ID: ABCDE-12345 (this is a single client ID)
    RedirectUris: msauth.com.contoso.app1://auth, msauth.com.contoso.app2://auth, msauth.com.contoso.app3://auth

App1 使用重定向 `msauth.com.contoso.app1://auth`，App2 使用 `msauth.com.contoso.app2://auth`，App3 使用 `msauth.com.contoso.app1://auth`

### <a name="migrating-from-adal-to-msal"></a>从 ADAL 迁移到 MSAL

在将使用 Azure AD 身份验证库 (ADAL) 的代码迁移到 MSAL 时，你可能已为应用配置重定向 URI。 可以持续使用同一个重定向 URI，前提是 ADAL 应用已配置为支持中转方案，且重定向 URI 满足 MSAL 重定向 URI 格式要求。

## <a name="msal-redirect-uri-format-requirements"></a>MSAL 重定向 URI 格式要求

* MSAL 重定向 URI 必须采用 `<scheme>://host` 格式

    其中的 `<scheme>` 是用于标识应用的唯一字符串。 它主要基于应用程序的捆绑标识符，目的是保证唯一性。 例如，如果应用的捆绑 ID 为 `com.contoso.myapp`，则重定向 URI 将采用 `msauth.com.contoso.myapp://auth` 格式。

    如果从 ADAL 进行迁移，则重定向 URI 可能会采用此格式：`<scheme>://[Your_Bundle_Id]`，其中的 `scheme` 是唯一字符串。 只要使用 MSAL，此格式就可以继续使用。

* `<scheme>` 必须注册到应用的 Info.plist 的 `CFBundleURLTypes > CFBundleURLSchemes` 下。  在此示例中，Info.plist 已作为源代码打开：

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>msauth.[BUNDLE_ID]</string>
            </array>
        </dict>
    </array>
    ```
    

MSAL 会验证重定向 URI 是否已正确注册，否则会返回错误。
    
* 若要将通用链接用作重定向 URI，`<scheme>` 必须为 `https`，不需在 `CFBundleURLSchemes` 中声明。 只需在通过通用链接打开应用程序后，按照[开发人员的通用链接](https://developer.apple.com/ios/universal-links/)中 Apple 的说明配置应用和域，然后调用 `MSALPublicClientApplication` 的 `handleMSALResponse:sourceApplication:` 方法即可。

## <a name="use-a-custom-redirect-uri"></a>使用自定义重定向 URI

若要使用自定义重定向 URI，请在初始化对象时，将 `redirectUri` 参数传递给 `MSALPublicClientApplicationConfig`，将该对象传递给 `MSALPublicClientApplication`。 如果重定向 URI 无效，初始化表达式会返回 `nil`，并根据其他信息来设置 `redirectURIError`。  例如：

Objective-C：

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

Swift：

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: authority)
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="handle-the-url-opened-event"></a>处理“URL 已打开”事件

应用程序在通过 URL 方案或通用链接收到任何响应时，应调用 MSAL。 当应用程序打开后，调用 `MSALPublicClientApplication` 的 `handleMSALResponse:sourceApplication:` 方法。 下面是自定义方案的一个示例：

Objective-C：

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift：

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>后续步骤

详细了解[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)
