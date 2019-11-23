---
title: 调用 Web API 的移动应用（代码配置）- Microsoft 标识平台 | Azure
description: 了解如何构建调用 Web API 的移动应用（应用的代码配置）
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
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0d0550dd92b786ec540bae6ae6da7322d4fb629
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175480"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>调用 Web API 的移动应用 - 代码配置

创建应用程序后，你将了解如何使用应用注册参数配置代码。 移动应用程序还附带一些复杂的细节，必须处理好这些细节才能适应用于生成这些应用的框架

## <a name="msal-libraries-supporting-mobile-apps"></a>支持移动应用的 MSAL 库

支持移动应用的 Microsoft 库包括：

  MSAL 库 | 说明
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 用于开发可移植的应用程序。 MSAL.NET 支持的用于生成移动应用程序的平台为 UWP、Xamarin.iOS 和 Xamarin.Android。
  ![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | 用客观-C 或 Swift 开发本机 iOS 应用程序
  ![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | 用于在 Java for Android 中开发本机 Android 应用程序

## <a name="instantiating-the-application"></a>实例化应用程序

### <a name="android"></a>Android

移动应用程序使用 `PublicClientApplication` 类。 下面是该类的实例化方式：

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

IOS 上的移动应用程序需要实例化 `MSALPublicClientApplication` 类。

Objective-C：

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift：
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

还有[其他 MSALPublicClientApplicationConfig 属性](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options)可替代默认权限，指定重定向 URI 或更改 MSAL 令牌缓存行为。 

### <a name="xamarin-or-uwp"></a>Xamarin 或 UWP

下一段说明如何为 Xamarin、Xamarin 和 UWP 应用程序实例化应用程序。

#### <a name="instantiating-the-application"></a>实例化应用程序

在 Xamarin 或 UWP 中，实例化应用程序的最简单方法如下（其中的 `ClientId` 是注册的应用的 Guid）。

```CSharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

还有其他一些 With*parameter* 方法，这些方法用于设置 UI 父级、替代默认颁发机构、指定客户端名称和版本（适用于遥测）、指定重定向 URI 以及指定要使用的 Http 工厂（例如，若要处理代理，可指定遥测和日志记录）。 这是以下段落的主题。

##### <a name="specifying-the-parent-uiwindowactivity"></a>指定父 UI/窗口/活动

在 Android 上，需要在执行交互式身份验证之前传递父活动。 在 iOS 上使用中介时，需要传入 ViewController。 在 UWP 上，可以相同的方式传入父窗口。 可以在获取令牌时执行此操作，不过，也可以在创建应用时在回调中指定一个用于返回 UIParent 的委托。

```CSharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

在 Android 上，我们建议按`CurrentActivityPlugin`此处[所述使用 ](https://github.com/jamesmontemagno/CurrentActivityPlugin)。  然后，`PublicClientApplication` 生成器代码将如下所示：

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="more-app-building-parameters"></a>其他应用生成参数

- 如需 `PublicClientApplicationBuilder` 上提供的所有修饰符的列表，请参阅参考文档 [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- 如需 `PublicClientApplicationOptions` 中公开的所有选项的说明，请参阅参考文档中的 [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)

## <a name="xamarin-ios-specific-considerations"></a>特定于 Xamarin iOS 的注意事项

在 Xamarin iOS 上使用 MSAL.NET 时必须考虑的几个注意事项：

1. [重写并实现 `OpenUrl` 中的 `AppDelegate` 函数](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [启用密钥链组](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [启用令牌缓存共享](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [启用密钥链访问](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

[Xamarin iOS 注意事项](msal-net-xamarin-ios-considerations.md)中提供了详细信息

## <a name="msal-for-ios-and-macos-specific-considerations"></a>适用于 iOS 和 macOS 的特定注意事项 MSAL

使用适用于 iOS 和 macOS 的 MSAL 时，请注意以下事项：

1. [实现 `openURL` 回调](#brokered-authentication-for-msal-for-ios-and-macos)
2. [启用密钥链访问组](howto-v2-keychain-objc.md)
3. [自定义浏览器和 Webview](customize-webviews.md)

## <a name="xamarin-android-specific-considerations"></a>Xamarin Android 特有注意事项

下面是有关 Xamarin Android 的具体注意事项：

- [确保身份验证流的交互部分结束后控制返回到 MSAL](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [更新 Android 清单](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [使用嵌入式 Web 视图（可选）](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [故障排除](msal-net-xamarin-android-considerations.md#troubleshooting)

[Xamarin Android 注意事项](msal-net-xamarin-android-considerations.md)中提供了详细信息

最后，需要了解有关 Android 上的浏览器的一些细节。 [与 MSAL.NET 配合使用时特定于 Xamarin Android 的注意事项](msal-net-system-browser-android-considerations.md)中对此做了介绍

#### <a name="uwp-specific-considerations"></a>特定于 UWP 的注意事项

在 UWP 上，可以使用企业网络。 有关将 MSAL 库与 UWP 配合使用的其他信息，请参阅 [MSAL.NET 的特定于 Windows 平台的通用注意事项](msal-net-uwp-considerations.md)。

## <a name="configuring-the-application-to-use-the-broker"></a>将应用程序配置为使用中介

### <a name="why-use-brokers-in-ios-and-android-applications"></a>为什么在 iOS 和 Android 应用程序中使用代理？

在 Android 和 iOS 上，中介可以实现：

- 向 AAD 注册设备时的单一登录（SSO）。 用户无需登录到每个应用程序。
- 设备标识。 通过访问设备上已加入工作区的设备证书，启用与 Azure AD 设备相关的条件性访问策略。
- 应用程序标识验证。 当应用程序调用中介时，它会传递其重定向 URL，而中介会验证该 URL。

### <a name="enable-the-broker-on-xamarin"></a>启用 Xamarin 上的代理

若要启用这些功能之一，请在调用 `WithBroker()` 方法时使用 `PublicClientApplicationBuilder.CreateApplication` 参数。 `.WithBroker()` 默认设置为 true。 对于[Xamarin](#brokered-authentication-for-xamarinios)，请遵循以下步骤。

### <a name="enable-the-broker-for-msal-for-android"></a>为适用于 Android 的 MSAL 启用代理

有关在 Android 上启用代理的信息，请参阅[android 中的中转身份验证](brokered-auth.md)。 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>为 iOS 和 macOS 启用 MSAL 代理

默认情况下，将为 iOS 和 macOS 的 MSAL 中的 AAD 方案启用中转身份验证。 按照以下步骤配置应用程序，以便对[MSAL For iOS 和 macOS](#brokered-authentication-for-msal-for-ios-and-macos)的中转身份验证支持。 请注意， [MSAL For Xamarin](#brokered-authentication-for-xamarinios)和[MSAL For ios 和 macOS](#brokered-authentication-for-msal-for-ios-and-macos)的步骤不同。

### <a name="brokered-authentication-for-xamarinios"></a>适用于 Xamarin.iOS 的中介身份验证

请按照以下步骤操作，使你的 Xamarin iOS 应用与[Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)应用进行交流。

#### <a name="step-1-enable-broker-support"></a>步骤1：启用代理支持

已按 `PublicClientApplication` 启用中介支持。 此项默认禁用。 通过 `WithBroker()` 创建 `PublicClientApplication` 时，必须使用 `PublicClientApplicationBuilder` 参数（默认设置为 true）。

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>步骤2：更新 AppDelegate 以处理回调

当 MSAL.NET 调用中介时，中介将通过 `AppDelegate.OpenUrl` 方法回调应用程序。 由于 MSAL 将等待来自中介的响应，因此应用程序需要与中介配合才能回调 MSAL.NET。 为此，请更新 `AppDelegate.cs` 文件以重写以下方法。

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

每次启动应用程序都会调用此方法，你可以借此机会处理中介的响应，并完成 MSAL.NET 发起的身份验证过程。

#### <a name="step-3-set-a-uiviewcontroller"></a>步骤3：设置 UIViewController （）

在 Xamarin iOS 中，通常不需要设置对象窗口，但在这种情况下，需要发送请求并接收中转站的响应。 仍在 `AppDelegate.cs` 中，设置 ViewController。

执行以下操作设置对象窗口：

1) 在 `AppDelegate.cs` 中，将 `App.RootViewController` 设置为新的 `UIViewController()`。 这可以确保提供一个 `UIViewController` 来调用中介。 如果未正确设置此参数，可能会收到以下错误：`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) 在 AcquireTokenInteractive 调用中，使用 `.WithParentActivityOrWindow(App.RootViewController)` 并传入对你要使用的对象窗口的引用。

例如：

在 `App.cs` 中：
```CSharp
   public static object RootViewController { get; set; }
```
在 `AppDelegate.cs` 中：
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
在“获取令牌”调用中：
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

#### <a name="step-4-register-a-url-scheme"></a>步骤4：注册 URL 方案

MSAL.NET 使用 URL 调用中介，然后将中介响应返回到应用。 若要完成往返过程，需要在 `Info.plist` 文件中注册应用的 URL 方案。

为 `CFBundleURLSchemes` 加上 `msauth` 前缀。 然后将 `CFBundleURLName` 添加到末尾。

`$"msauth.(BundleId)"`

**例如：** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> 接收中介的响应时，此 URL 方案将成为用于唯一标识应用的 RedirectUri 的一部分。

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

#### <a name="step-5-lsapplicationqueriesschemes"></a>步骤5： LSApplicationQueriesSchemes

MSAL 使用 `–canOpenURL:` 来检查是否在设备上安装了中介。 在 iOS 9 中，Apple 锁定了应用程序可以查询的方案。

将 **`msauthv2`添加**到 `LSApplicationQueriesSchemes` 文件的 `Info.plist` 节。

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>适用于 iOS 和 macOS 的 MSAL 的中转身份验证

默认情况下，为 AAD 方案启用中转身份验证。

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>步骤1：更新 AppDelegate 以处理回调

当 iOS 和 macOS 的 MSAL 调用代理时，代理将转而通过 `openURL` 方法回调到你的应用程序。 由于 MSAL 将等待来自代理的响应，因此应用程序需要合作才能调用 MSAL。 为此，请更新 `AppDelegate.m` 文件以重写以下方法。

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
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

请注意，如果在 iOS 13 + 上采用了 UISceneDelegate，则 MSAL 回调需改为 UISceneDelegate 的 `scene:openURLContexts:` （请参阅[Apple 文档](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)）。 对于每个 URL，只能调用一次 MSAL `handleMSALResponse:sourceApplication:`。

#### <a name="step-2-register-a-url-scheme"></a>步骤2：注册 URL 方案

适用于 iOS 和 macOS 的 MSAL 使用 Url 调用 broker，然后将 broker 响应返回到应用。 若要完成往返过程，需要在 `Info.plist` 文件中注册应用的 URL 方案。

将自定义 URL 方案作为前缀 `msauth`。 然后，将**捆绑标识符**添加到末尾。

`msauth.(BundleId)`

**例如：** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> 接收中介的响应时，此 URL 方案将成为用于唯一标识应用的 RedirectUri 的一部分。 请确保在[Azure 门户](https://portal.azure.com)中为应用程序注册 `msauth.(BundleId)://auth` 格式的 RedirectUri。

```XML
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

#### <a name="step-3-lsapplicationqueriesschemes"></a>步骤3： LSApplicationQueriesSchemes

**添加 `LSApplicationQueriesSchemes`** 以允许调用 Microsoft Authenticator （如果已安装）。
请注意，在用 Xcode 11 和更高版本编译应用程序时，需要 "msauthv3" 方案。 

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>适用于 Xamarin.Android 的中介身份验证

MSAL.NET 尚不支持适用于 Android 的中介。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [获取令牌](scenario-mobile-acquire-token.md)
