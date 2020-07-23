---
title: 在 macOS 和 iOS 上配置 SSO
titleSuffix: Microsoft identity platform
description: 了解如何在 macOS 和 iOS 上配置单一登录 (SSO)。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 25389348476552298ddb947ccb59acb8b3d5bc57
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80881242"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>如何：在 macOS 和 iOS 上配置 SSO

适用于 macOS 和 iOS 的 Microsoft 身份验证库 (MSAL) 支持 macOS/iOS 应用与浏览器之间的单一登录 (SSO)。 本文介绍以下 SSO 方案：

- [多个应用之间的静默 SSO](#silent-sso-between-apps)

此类 SSO 在同一 Apple 开发商分发的多个应用之间进行。 它提供静默 SSO（即，不提示用户输入凭据）：从密钥链读取其他应用写入的刷新令牌，并以静默方式使用这些刷新令牌交换访问令牌。  

- [通过身份验证中介实现的 SSO](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> 此流在 macOS 上不可用。

Microsoft 提供称作“中介”的应用，只要移动设备已注册到 Azure Active Directory (AAD)，这些应用就能在不同供应商提供的应用程序之间实现 SSO。 此类 SSO 要求在用户设备上安装一个中介应用程序。

- **MSAL 与 Safari 之间的 SSO**

SSO 是通过 [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) 类实现的。 它使用其他应用程序和 Safari 浏览器中的现有登录状态。 它并不局限于同一个 Apple 开发商分发的应用，但需要用户进行一些交互。

如果在应用中使用默认 Web 视图将用户登录，基于 MSAL 的应用程序与 Safari 之间会自动实现 SSO。 若要详细了解 MSAL 支持的 Web 视图，请访问[自定义浏览器和 WebView](customize-webviews.md)。

> [!IMPORTANT]
> 此类 SSO 目前在 macOS 上不可用。 macOS 上的 MSAL 仅支持 WKWebView，但 Safari 并不提供 WKWebView 的 SSO 支持。 

- **ADAL 与 MSAL macOS/iOS 应用之间的静默 SSO**

MSAL Objective-C 支持基于 ADAL Objective-C 的应用的迁移和 SSO。 这些应用必须由同一家 Apple 开发商分发。

有关基于 ADAL 与基于 MSAL 的应用之间的跨应用 SSO 说明，请参阅 [macOS 和 iOS 上 ADAL 与 MSAL 应用之间的 SSO](sso-between-adal-msal-apps-macos-ios.md)。

## <a name="silent-sso-between-apps"></a>应用之间的静默 SSO

MSAL 支持通过 iOS 密钥链访问组进行 SSO 共享。

若要跨应用程序启用 SSO，需要执行以下步骤，后文对此做了详细说明：

1. 确保所有应用程序使用相同的客户端 ID 或应用程序 ID。
1. 确保所有应用程序共享来自 Apple 的相同签名证书，以便可以共享密钥链。
1. 请求每个应用程序的相同密钥链授权。
1. 如果你要使用的共享密钥链不同于默认设置，请告知 MSAL SDK。

### <a name="use-the-same-client-id-and-application-id"></a>使用相同的客户端 ID 和应用程序 ID

为使 Microsoft 标识平台知道哪些应用程序可以共享令牌，这些应用程序需要共享相同的客户端 ID 或应用程序 ID。 这是在门户中注册第一个应用程序时提供的唯一标识符。

Microsoft 标识平台根据应用的**重定向 URI** 来辨别使用相同应用程序 ID 的应用。 每个应用程序可以在登记门户中注册多个重定向 URI。 套件中的每个应用都具有不同的重定向 URI。 例如：

App1 重定向 URI： `msauth.com.contoso.mytestapp1://auth`  
App2 重定向 URI： `msauth.com.contoso.mytestapp2://auth`  
App3 重定向 URI： `msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> 重定向 URI 的格式必须与 MSAL 支持的格式兼容，[MSAL 重定向 URI 格式要求](redirect-uris-ios.md#msal-redirect-uri-format-requirements)中阐述了此格式。

### <a name="setup-keychain-sharing-between-applications"></a>设置在应用程序之间共享的密钥链

请参阅 Apple 的 [Adding Capabilities](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html)（添加功能）一文来启用密钥链共享。 重要的是，需要决定密钥链的调用方式，并将该功能添加到 SSO 涉及的所有应用程序。

正确设置权利后，项目目录中会出现一个 `entitlements.plist` 文件，其中包含类似于以下示例的内容：

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

#### <a name="add-a-new-keychain-group"></a>添加新密钥链组

向项目**功能**添加新密钥链组。 密钥链组应为：
* iOS 上的 `com.microsoft.adalcache` 
* macOS 上的 `com.microsoft.identity.universalstorage`。

![密钥链示例](media/single-sign-on-macos-ios/keychain-example.png)

有关详细信息，请参阅[密钥链组](howto-v2-keychain-objc.md)。

## <a name="configure-the-application-object"></a>配置应用程序对象

在每个应用程序中启用密钥链权利并准备好使用 SSO 后，请按以下示例所示在 `MSALPublicClientApplication` 中配置密钥链访问组：

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
> 在应用程序之间共享密钥链之后，任何应用程序都可以删除用户，甚至可以删除整个应用程序的所有令牌。
> 如果应用程序依赖于这些令牌来执行后台工作，后果特别严重。
> 要共享密钥链，就必须十分警惕应用使用 Microsoft 标识 SDK 删除操作的情况。

就这么简单！ Microsoft 标识 SDK 现在会在所有应用程序之间共享凭据。 此外还会在应用程序实例之间共享帐户列表。

## <a name="sso-through-authentication-broker-on-ios"></a>在 iOS 上通过身份验证中介实现 SSO

MSAL 通过 Microsoft Authenticator 提供中介身份验证支持。 Microsoft Authenticator 为 AAD 注册的设备提供 SSO，还可以帮助你的应用程序遵循条件性访问策略。

以下步骤说明如何使用身份验证中介为应用启用 SSO：

1. 为应用 Info.plist 中的应用程序注册中介兼容的重定向 URI 格式。 中介兼容的重定向 URI 格式为 `msauth.<app.bundle.id>://auth`。 请将 `<app.bundle.id>`` 替换为应用程序的捆绑 ID。 例如：

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. 将以下方案添加到应用的 Info.plist 中的 `LSApplicationQueriesSchemes` 下：

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
    
**如果使用的是 Xcode 11**，应改为将 MSAL 回调放入 `SceneDelegate` 文件。
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