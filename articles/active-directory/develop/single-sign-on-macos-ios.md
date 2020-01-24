---
title: 在 macOS 和 iOS 上配置 SSO
titleSuffix: Microsoft identity platform
description: 了解如何在 macOS 和 iOS 上配置单一登录（SSO）。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: ecc55c0d41f552d2c29fe5c964a7c40ab9e382ba
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701376"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>如何：在 macOS 和 iOS 上配置 SSO

适用于 macOS 和 iOS 的 Microsoft 身份验证库（MSAL）支持 macOS/iOS 应用与浏览器之间的单一登录（SSO）。 本文介绍以下 SSO 方案：

- [在多个应用之间无提示 SSO](#silent-sso-between-apps)

这种类型的 SSO 适用于同一 Apple 开发人员分发的多个应用程序。 它提供无提示 SSO （即，不提示用户输入凭据），方法是从密钥链读取其他应用程序写入的刷新令牌，并以无提示方式为其交换访问令牌。  

- [通过身份验证代理的 SSO](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> 此流在 macOS 上不可用。

Microsoft 提供称为代理的应用程序，只要移动设备注册到 Azure Active Directory （AAD），就可以在不同供应商的应用程序之间实现 SSO。 这种类型的 SSO 要求在用户的设备上安装一个代理应用程序。

- **MSAL 和 Safari 之间的 SSO**

SSO 是通过[ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)类实现的。 它使用其他应用程序和 Safari 浏览器的现有登录状态。 它并不局限于同一个 Apple 开发人员分发的应用程序，但它需要一些用户交互。

如果你在应用中使用默认的 web 视图来登录用户，则会在基于 MSAL 的应用程序和 Safari 之间自动建立 SSO。 若要了解有关 MSAL 支持的 web 视图的详细信息，请访问[自定义浏览器和 webview](customize-webviews.md)。

> [!IMPORTANT]
> 此类型的 SSO 当前在 macOS 上不可用。 MacOS 上的 MSAL 仅支持 WKWebView，这对 Safari 没有 SSO 支持。 

- **ADAL 和 MSAL macOS/iOS 应用之间的无提示 SSO**

MSAL 目标-C 支持迁移和 SSO 与基于 ADAL 目标-C 的应用。 应用必须由同一 Apple 开发人员分发。

有关基于 ADAL 和 MSAL 的应用之间的跨应用 SSO 的说明，请参阅[macOS 和 iOS 上的 ADAL 与 MSAL 应用之间的 SSO](sso-between-adal-msal-apps-macos-ios.md) 。

## <a name="silent-sso-between-apps"></a>应用之间无提示 SSO

MSAL 支持通过 iOS 密钥链访问组进行 SSO 共享。

若要在应用程序中启用 SSO，需要执行以下步骤，详细说明如下：

1. 确保所有应用程序使用相同的客户端 ID 或应用程序 ID。
1. 确保所有应用程序共享来自 Apple 的相同签名证书，以便可以共享密钥链。
1. 请求每个应用程序的相同密钥链授权。
1. 告诉 MSAL Sdk 你希望我们使用的共享密钥链（如果它不同于默认值）。

### <a name="use-the-same-client-id-and-application-id"></a>使用相同的客户端 ID 和应用程序 ID

为了让 Microsoft 标识平台知道哪些应用程序可以共享令牌，这些应用程序需要共享相同的客户端 ID 或应用程序 ID。 这是在门户中注册第一个应用程序时提供的唯一标识符。

Microsoft 标识平台告诉使用同一个应用程序 ID 的应用程序的**重定向 uri**。 每个应用程序可以在登记门户中注册多个重定向 URI。 套件中的每个应用程序具有不同的重定向 URI。 例如：

App1 重定向 URI： `msauth.com.contoso.mytestapp1://auth` App2 重定向 uri： `msauth.com.contoso.mytestapp2://auth` App3 重定向 URI： `msauth.com.contoso.mytestapp3://auth`

> [!IMPORTANT]
> 重定向 uri 的格式必须兼容 MSAL 支持的格式，如[MSAL 重定向 URI 格式要求](redirect-uris-ios.md#msal-redirect-uri-format-requirements)中所述。

### <a name="setup-keychain-sharing-between-applications"></a>设置应用程序之间的密钥链共享

若要启用密钥链共享，请参阅 Apple[添加功能](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html)一文。 重要的是，你需要确定要调用的密钥链，并将该功能添加到将涉及 SSO 的所有应用程序中。

设置正确的权利后，你将在项目目录中看到一个 `entitlements.plist` 文件，其中包含类似于以下示例的内容：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

在每个应用程序中启用密钥链授权，并准备好使用 SSO 后，请使用密钥链访问组配置 `MSALPublicClientApplication`，如以下示例中所示：

Objective-C：

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Swift：

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"
        
do {
    let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



> [!WARNING]
> 在应用程序之间共享密钥链时，任何应用程序都可以删除应用程序中的用户或甚至所有令牌。
> 如果你的应用程序依赖于标记来执行后台工作，则这一点特别有影响力。
> 共享密钥链意味着在应用使用 Microsoft 标识 SDK 删除操作时必须非常小心。

就这么简单！ Microsoft 标识 SDK 现在会在所有应用程序之间共享凭据。 帐户列表还将在应用程序实例之间共享。

## <a name="sso-through-authentication-broker-on-ios"></a>IOS 上的 SSO 到身份验证代理

MSAL 通过 Microsoft Authenticator 提供对中转身份验证的支持。 Microsoft Authenticator 为 AAD 注册的设备提供 SSO，还可以帮助你的应用程序遵循条件性访问策略。

以下步骤介绍如何使用应用的身份验证代理启用 SSO：

1. 为应用程序的 info.plist 中的应用程序注册 broker 兼容的重定向 URI 格式。 与 broker 兼容的重定向 URI 格式为 `msauth.<app.bundle.id>://auth`。 用应用程序的捆绑 ID 替换 "< >" "。 例如：

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. 将以下方案添加到 `LSApplicationQueriesSchemes`下的应用 info.plist：

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. 将以下内容添加到 `AppDelegate.m` 文件以处理回调：

    Objective-C：
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift：
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**如果使用的是 Xcode 11**，应改为将 MSAL 回拨放入 `SceneDelegate` 文件中。
如果支持兼容旧版 iOS 的 UISceneDelegate 和 UIApplicationDelegate，则需将 MSAL 回叫置于这两个文件中。

Objective-C：

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift：

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```
    
## <a name="next-steps"></a>后续步骤

详细了解[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)
