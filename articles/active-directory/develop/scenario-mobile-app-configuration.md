---
title: 配置调用 Web API 的移动应用 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何构建调用 Web API 的移动应用（应用的代码配置）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/16/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9fe084d931ee735f9eaecc58ca5445ae56a951cc
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121045"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>配置调用 Web API 的移动应用

创建应用程序后，可以了解如何使用应用注册参数配置代码。 将移动应用程序装入其创建框架时存在一定的复杂性。

## <a name="find-msal-support-for-mobile-apps"></a>查找移动应用的 MSAL 支持

以下 Microsoft 身份验证库 (MSAL) 类型支持移动应用。

MSAL | 说明
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 用于开发可移植的应用程序。 MSAL.NET 支持在以下平台中生成移动应用程序：通用 Windows 平台 (UWP)、Xamarin.iOS 和 Xamarin.Android。
![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | 用于通过 Objective-C 或 Swift 开发本机 iOS 应用程序。
![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | 用于在 Java for Android 中开发本机 Android 应用程序。

## <a name="instantiate-the-application"></a>实例化应用程序

### <a name="android"></a>Android

移动应用程序使用 `PublicClientApplication` 类。 下面是该类的实例化方式：

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

iOS 上的移动应用程序需实例化 `MSALPublicClientApplication` 类。 若要实例化该类，请使用以下代码。 

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[其他 MSALPublicClientApplicationConfig 属性](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options)可以重写默认的颁发机构、指定重定向 URI 或更改 MSAL 令牌缓存的行为。 

### <a name="xamarin-or-uwp"></a>Xamarin 或 UWP

本部分说明如何实例化 Xamarin.iOS、Xamarin.Android 和 UWP 应用的应用程序。

#### <a name="instantiate-the-application"></a>实例化应用程序

在 Xamarin 或 UWP 中，实例化应用程序的最简单方法是使用以下代码。 在此代码中，`ClientId` 是注册的应用的 GUID。

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

其他 `With<Parameter>` 方法可以设置 UI 父级、重写默认的颁发机构、指定遥测的客户端名称和版本、指定重定向 URI 以及指定要使用的 HTTP 工厂。 例如，可以使用 HTTP 工厂来处理代理以及指定遥测和日志记录。 

以下部分提供了有关实例化应用程序的详细信息。

##### <a name="specify-the-parent-ui-window-or-activity"></a>指定父 UI、窗口或活动

在 Android 上，需要在执行交互式身份验证之前传递父活动。 在 iOS 上使用中介时，需要传入 `ViewController`。 在 UWP 上，可以相同的方式传入父窗口。 请在获取令牌时将其传入。 但在创建应用程序时，还可以指定一个回调作为返回 `UIParent` 的委托。

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

在 Android 上，建议使用 [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin)。 最终的 `PublicClientApplication` 生成器代码如以下示例所示：

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>查找其他应用生成参数

有关可在 `PublicClientApplicationBuilder` 中使用的所有方法列表，请参阅[方法列表](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)。

有关 `PublicClientApplicationOptions` 中公开的所有选项的说明，请参阅[参考文档](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)。

## <a name="tasks-for-xamarin-ios"></a>Xamarin iOS 的任务

如果在 Xamarin iOS 上使用 MSAL.NET，请执行以下任务。

* [重写并实现 `AppDelegate` 中的 `OpenUrl` 函数](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [启用密钥链组](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [启用令牌缓存共享](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [启用密钥链访问](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

有关详细信息，请参阅 [Xamarin iOS 注意事项](msal-net-xamarin-ios-considerations.md)。

## <a name="tasks-for-msal-for-ios-and-macos"></a>适用于 iOS 和 macOS 的 MSAL 的任务

使用适用于 iOS 和 macOS 的 MSAL 时，需要执行以下任务：

* [实现 `openURL` 回调](#brokered-authentication-for-msal-for-ios-and-macos)
* [启用密钥链访问组](howto-v2-keychain-objc.md)
* [自定义浏览器和 WebView](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Xamarin.Android 的任务

如果使用 Xamarin.Android，请执行以下任务：

- [确保身份验证流的交互部分结束后控制返回到 MSAL](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [更新 Android 清单](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [使用嵌入式 Web 视图（可选）](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [根据需要进行故障排除](msal-net-xamarin-android-considerations.md#troubleshoot)

有关详细信息，请参阅 [Xamarin.Android 注意事项](msal-net-xamarin-android-considerations.md)。

有关 Android 上的浏览器的注意事项，请参阅 [MSAL.NET 的 Xamarin.Android 特定注意事项](msal-net-system-browser-android-considerations.md)。

#### <a name="tasks-for-uwp"></a>UWP 的任务

在 UWP 上，可以使用企业网络。 以下部分说明应在企业方案中完成的任务。

有关详细信息，请参阅 [MSAL.NET 的 UWP 特定注意事项](msal-net-uwp-considerations.md)。

## <a name="configure-the-application-to-use-the-broker"></a>将应用程序配置为使用中介

在 Android 和 iOS 上，中介可以实现：

- **单一登录 (SSO)** ：对于已注册到 Azure Active Directory (Azure AD) 的设备，可以使用 SSO。 使用 SSO 时，用户无需登录到每个应用程序。
- **设备标识**：此设置启用与 Azure AD 设备相关的条件访问策略。 身份验证过程使用将设备加入工作区时创建的设备证书。
- **应用程序标识验证**：应用程序在调用中介时会传递其重定向 URL。 然后中介验证该 URL。

### <a name="enable-the-broker-on-xamarin"></a>在 Xamarin 上启用中介

若要在 Xamarin 上启用中介，请在调用 `PublicClientApplicationBuilder.CreateApplication` 方法时使用 `WithBroker()` 参数。 `.WithBroker()` 默认设置为 true。 

若要为 Xamarin.iOS 启用中介身份验证，请遵循本文的 [Xamarin.iOS 部分](#enable-brokered-authentication-for-xamarin-ios)中的步骤。

### <a name="enable-the-broker-for-msal-for-android"></a>为用于 Android 的 MSAL 启用中介

有关在 Android 上启用中介的信息，请参阅 [Android 上的中介身份验证](brokered-auth.md)。 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>为用于 iOS 和 macOS 的 MSAL 启用中介

默认已为适用于 iOS 和 macOS 的 MSAL 中的 Azure AD 方案启用中介身份验证。 

以下部分提供有关在应用程序中为适用于 Xamarin.iOS 的 MSAL 或适用于 iOS 和 macOS 的 MSAL，配置中介身份验证支持的说明。 这两套说明中的某些步骤有所不同。

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>为 Xamarin iOS 启用中介身份验证

按照本部分中的步骤操作，使你的 Xamarin iOS 应用与[Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)的应用进行交流。

#### <a name="step-1-enable-broker-support"></a>步骤 1：启用中介支持

默认已禁用中介支持。 为单个 `PublicClientApplication` 类启用中介。 通过 `PublicClientApplicationBuilder` 创建 `PublicClientApplication` 类时，请使用 `WithBroker()` 参数。 `WithBroker()` 参数默认设置为 true。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>步骤 2：更新 AppDelegate 以处理回调

当 MSAL.NET 调用中介时，中介将回调应用程序。 它使用 `AppDelegate.OpenUrl` 方法进行回调。 由于 MSAL 会等待来自中介的响应，因此应用程序需要进行协作才能回调 MSAL.NET。 若要设置此行为，可以更新 `AppDelegate.cs` 文件以重写该方法，如以下代码所示。

```csharp
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

每次启动应用程序都会调用此方法。 可以借此机会处理中介的响应，并完成 MSAL.NET 启动的身份验证过程。

#### <a name="step-3-set-a-uiviewcontroller"></a>步骤 3：设置 UIViewController()

在 Xamarin iOS 中，通常不需设置对象窗口。 但在本例中，应该设置对象窗口，以便能够发送请求并接收中介的响应。 若要设置对象窗口，请在 `AppDelegate.cs` 中设置 `ViewController`。

若要设置对象窗口，请执行以下步骤：

1. 在 `AppDelegate.cs` 中，将 `App.RootViewController` 设置为新的 `UIViewController()`。 此设置确保对中介的调用包含 `UIViewController`。 如果未正确设置此参数，可能会收到以下错误：

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. 在 `AcquireTokenInteractive` 调用中使用 `.WithParentActivityOrWindow(App.RootViewController)`。 传入对所要使用的对象窗口的引用。 下面是一个示例：

    在 `App.cs`中：
    ```csharp
       public static object RootViewController { get; set; }
    ```
    在 `AppDelegate.cs`中：
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    在 `AcquireToken` 调用中：
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```
    
#### <a name="step-4-register-a-url-scheme"></a>步骤 4：注册 URL 方案

MSAL.NET 使用 URL 调用中介，然后将中介响应返回到应用。 若要完成往返过程，请在 `Info.plist` 文件中注册应用的 URL 方案。 

若要注册应用的 URL 方案，请执行以下步骤：

1. 为 `CFBundleURLSchemes` 加上 `msauth` 前缀。 
1. 将 `CFBundleURLName` 添加到末尾。 遵循以下模式： 

   `$"msauth.(BundleId)"`

   此处，`BundleId` 用于唯一标识设备。 例如，如果 `BundleId` 是 `yourcompany.xforms`，则 URL 方案是 `msauth.com.yourcompany.xforms`。
    
   > [!NOTE]
   > 接收中介的响应时，此 URL 方案将成为用于唯一标识应用的重定向 URI 的一部分。
    
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
    
#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>步骤 5：在 LSApplicationQueriesSchemes 节中添加代码

MSAL 使用 `–canOpenURL:` 来检查是否在设备上安装了中介。 在 iOS 9 中，Apple 锁定了应用程序可以查询的方案。

将 `msauthv2` 添加到 `Info.plist` 文件的 `LSApplicationQueriesSchemes` 节，如以下代码示例所示：

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>适用于 iOS 和 macOS 的 MSAL 的中介身份验证

默认已为 Azure AD 方案启用了中介身份验证。

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>步骤 1：更新 AppDelegate 以处理回调

当适用于 iOS 和 macOS 的 MSAL 调用中介时，中介将使用 `openURL` 方法回调应用程序。 由于 MSAL 会等待来自中介的响应，因此应用程序需要进行协作才能回调 MSAL。 若要设置此功能，可以更新 `AppDelegate.m` 文件以重写该方法，如以下代码示例所示。

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

> [!NOTE]
> 如果在 iOS 13 或更高版本中采用了 `UISceneDelegate`，请改为将 MSAL 回调放入 `UISceneDelegate` 的 `scene:openURLContexts:` 中。 只能对每个 URL 调用 MSAL `handleMSALResponse:sourceApplication:` 一次。
>
> 有关详细信息，请参阅 [Apple 文档](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)。

#### <a name="step-2-register-a-url-scheme"></a>步骤 2：注册 URL 方案

适用于 iOS 和 macOS 的 MSAL 使用 URL 调用中介，然后将中介响应返回到应用。 若要完成往返过程，请在 `Info.plist` 文件中注册应用的 URL 方案。

为应用注册方案： 

1. 使用 `msauth` 作为自定义 URL 方案的前缀。 

1. 将捆绑标识符添加到方案的末尾。 遵循以下模式： 

   `$"msauth.(BundleId)"`

   此处，`BundleId` 用于唯一标识设备。 例如，如果 `BundleId` 是 `yourcompany.xforms`，则 URL 方案是 `msauth.com.yourcompany.xforms`。
  
   > [!NOTE]
   > 接收中介的响应时，此 URL 方案将成为用于唯一标识应用的重定向 URI 的一部分。 确保在 [Azure 门户](https://portal.azure.com)中为应用程序注册 `msauth.(BundleId)://auth` 格式的重定向 URI。
  
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

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>步骤 3：添加 LSApplicationQueriesSchemes

添加 `LSApplicationQueriesSchemes` 以允许调用 Microsoft Authenticator 应用（如果已安装）。

> [!NOTE]
> 如果应用是使用 Xcode 11 和更高版本编译的，则需要 `msauthv3` 方案。 

下面是如何添加 `LSApplicationQueriesSchemes` 的示例：

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>适用于 Xamarin.Android 的中介身份验证

有关在 Android 上启用中介的信息，请参阅 [Xamarin.Android 上的中介身份验证](msal-net-use-brokers-with-xamarin-apps.md#brokered-authentication-for-android)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [获取令牌](scenario-mobile-acquire-token.md)