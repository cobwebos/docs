---
title: 用于调用 web Api 的移动应用程序（代码配置）-Microsoft 标识平台 |Microsoft
description: 了解如何生成调用 web Api 的移动应用（应用的代码配置）
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
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>用于调用 web Api-代码配置的移动应用

创建应用程序后，你将了解如何使用应用注册参数来配置代码。 移动应用程序还具有一些复杂的细节，这与在构建这些应用时使用的框架有关

## <a name="msal-libraries-supporting-mobile-apps"></a>支持移动应用的 MSAL 库

支持移动应用的 Microsoft 库包括：

  MSAL 库 | 描述
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 开发可移植应用程序。 用于构建移动应用程序的 MSAL.NET 支持平台为 UWP、Xamarin 和 Xamarin。
  ![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | 用客观-C 或 Swift 开发本机 iOS 应用程序
  ![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | 在 Java 中开发适用于 Android 的本机 Android 应用程序

## <a name="instantiating-the-application"></a>实例化应用程序

### <a name="android"></a>Android

移动应用程序使用 `PublicClientApplication` 类。 下面介绍如何对其进行实例化：

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

在 Xamarin 或 UWP 中，实例化应用程序的最简单方法如下，其中 `ClientId` 是已注册应用的 Guid。

```CSharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

还有其他一些*参数*方法用于设置 UI 父代，替代默认的颁发机构，指定客户端名称和版本（对于遥测），指定 "重定向 URI"，指定要使用的 Http 工厂（例如，要处理代理，请指定遥测和日志记录）。 这是以下段落的主题。

##### <a name="specifying-the-parent-uiwindowactivity"></a>指定父 UI/窗口/活动

在 Android 上，你需要在进行交互式身份验证之前传递父活动。 在 iOS 上，使用 broker 时，需要传递 ViewController 中的。 对于 UWP，你可能想要传入父窗口。 这在获取令牌时是可能的，但也可以在应用创建时指定返回 UIParent 的委托。

```CSharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

在 Android 上，我们建议你在[此处](https://github.com/jamesmontemagno/CurrentActivityPlugin)使用 `CurrentActivityPlugin`。  然后，`PublicClientApplication` 生成器代码将如下所示：

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="more-app-building-parameters"></a>更多应用生成参数

- 有关 `PublicClientApplicationBuilder`上可用的所有修饰符的列表，请参阅参考文档[PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- 有关 `PublicClientApplicationOptions` 中公开的所有选项的说明，请参阅参考文档中的[PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)

## <a name="xamarin-ios-specific-considerations"></a>Xamarin 特定于 iOS 的注意事项

在 Xamarin iOS 上，使用 MSAL.NET 时，必须考虑几个注意事项：

1. [重写和实现 `AppDelegate`中的 `OpenUrl` 函数](msal-net-xamarin-ios-considerations.md#implement-openurl)
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

下面是 Xamarin Android 具体内容：

- [当身份验证流的交互式部分结束后，确保控件返回到 MSAL](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [更新 Android 清单](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [使用嵌入的 web 视图（可选）](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [故障排除](msal-net-xamarin-android-considerations.md#troubleshooting)

[Xamarin Android 注意事项](msal-net-xamarin-android-considerations.md)中提供了详细信息

最后，在 Android 上了解有关浏览器的一些 specificities。 MSAL.NET 中的[Xamarin 特定于 Android 的注意事项](msal-net-system-browser-android-considerations.md)对它们进行了介绍。

#### <a name="uwp-specific-considerations"></a>UWP 特定注意事项

在 UWP 上，你可以使用公司网络。 有关将 MSAL 库与 UWP 一起使用的其他信息，请参阅[MSAL.NET 通用 Windows 平台特定的注意事项](msal-net-uwp-considerations.md)。

## <a name="configuring-the-application-to-use-the-broker"></a>将应用程序配置为使用 broker

### <a name="why-use-brokers-in-ios-and-android-applications"></a>为什么在 iOS 和 Android 应用程序中使用代理？

在 Android 和 iOS 上，代理启用：

- 向 AAD 注册设备时的单一登录（SSO）。 用户无需登录到每个应用程序。
- 设备标识。 通过访问设备上已加入工作区的设备证书，启用与 Azure AD 设备相关的条件性访问策略。
- 应用程序标识验证。 当应用程序调用代理时，它会传递其重定向 url，并且 broker 将对其进行验证。

### <a name="enable-the-broker-on-xamarin"></a>启用 Xamarin 上的代理

若要启用其中一项功能，请在调用 `PublicClientApplicationBuilder.CreateApplication` 方法时使用 `WithBroker()` 参数。 默认情况下，`.WithBroker()` 设置为 true。 对于[Xamarin](#brokered-authentication-for-xamarinios)，请遵循以下步骤。

### <a name="enable-the-broker-for-msal-for-android"></a>为适用于 Android 的 MSAL 启用代理

有关在 Android 上启用代理的信息，请参阅[android 中的中转身份验证](brokered-auth.md)。 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>为 iOS 和 macOS 启用 MSAL 代理

默认情况下，将为 iOS 和 macOS 的 MSAL 中的 AAD 方案启用中转身份验证。 按照以下步骤配置应用程序，以便对[MSAL For iOS 和 macOS](#brokered-authentication-for-msal-for-ios-and-macos)的中转身份验证支持。 请注意， [MSAL For Xamarin](#brokered-authentication-for-xamarinios)和[MSAL For ios 和 macOS](#brokered-authentication-for-msal-for-ios-and-macos)的步骤不同。

### <a name="brokered-authentication-for-xamarinios"></a>针对 Xamarin 的中转身份验证

请按照以下步骤操作，使你的 Xamarin iOS 应用与[Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)应用进行交流。

#### <a name="step-1-enable-broker-support"></a>步骤1：启用代理支持

代理支持基于每个`PublicClientApplication` 启用。 此项默认禁用。 通过 `PublicClientApplicationBuilder`创建 `PublicClientApplication` 时，必须使用 `WithBroker()` 参数（默认设置为 true）。

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>步骤2：更新 AppDelegate 以处理回调

当 MSAL.NET 调用代理时，代理将转而通过 `AppDelegate.OpenUrl` 方法返回到应用程序。 由于 MSAL 将等待来自代理的响应，因此应用程序需要合作才能调用 MSAL.NET。 为此，请更新 `AppDelegate.cs` 文件以重写以下方法。

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

每次启动应用程序时都将调用此方法，并将其用作处理来自代理的响应并完成 MSAL.NET 启动的身份验证过程的机会。

#### <a name="step-3-set-a-uiviewcontroller"></a>步骤3：设置 UIViewController （）

使用 Xamarin iOS 时，通常不需要设置对象窗口，但在这种情况下，你可以从 broker 发送和接收响应。 仍在 `AppDelegate.cs`中设置 ViewController。

执行以下操作来设置对象窗口：

1) 在 `AppDelegate.cs`中，将 `App.RootViewController` 设置为新的 `UIViewController()`。 这将确保在调用代理的 `UIViewController`。 如果未正确设置，则可能会收到此错误： `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) 在 AcquireTokenInteractive 调用中，使用 `.WithParentActivityOrWindow(App.RootViewController)`，并将引用传递给你将使用的对象窗口。

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
在获取令牌调用中：
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

#### <a name="step-4-register-a-url-scheme"></a>步骤4：注册 URL 方案

MSAL.NET 使用 Url 调用 broker，然后将 broker 响应返回到应用。 若要完成往返过程，需要在 `Info.plist` 文件中注册应用程序的 URL 方案。

为包含 `msauth`的 `CFBundleURLSchemes` 加前缀。 然后，将 `CFBundleURLName` 添加到末尾。

`$"msauth.(BundleId)"`

**例如：** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> 此 URL 方案将成为 RedirectUri 的一部分，用于在接收来自代理的响应时唯一标识应用。

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

MSAL 使用 `–canOpenURL:` 来检查设备上是否安装了 broker。 在 iOS 9 中，Apple 锁定了应用程序可以查询的方案。

**将** **`msauthv2`** 添加到 `Info.plist` 文件的 `LSApplicationQueriesSchemes` 部分。

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

适用于 iOS 和 macOS 的 MSAL 使用 Url 调用 broker，然后将 broker 响应返回到应用。 若要完成往返过程，需要在 `Info.plist` 文件中注册应用程序的 URL 方案。

将自定义 URL 方案作为前缀 `msauth`。 然后，将**捆绑标识符**添加到末尾。

`msauth.(BundleId)`

**例如：** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> 此 URL 方案将成为 RedirectUri 的一部分，用于在接收来自代理的响应时唯一标识应用。 请确保在[Azure 门户](https://portal.azure.com)中为应用程序注册 `msauth.(BundleId)://auth` 格式的 RedirectUri。

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

### <a name="brokered-authentication-for-xamarinandroid"></a>适用于 Xamarin 的中转身份验证

MSAL.NET 尚不支持适用于 Android 的代理。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [获取令牌](scenario-mobile-acquire-token.md)
