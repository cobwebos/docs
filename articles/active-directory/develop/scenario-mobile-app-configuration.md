---
title: 配置用于调用 web Api 的移动应用 |Microsoft
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: fc25f13d0b0b8a264dcd47a5fdebb0533e93fb55
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132487"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>配置调用 web Api 的移动应用

创建应用程序后，你将了解如何使用应用注册参数来配置代码。 移动应用程序带来了一些与在其创建框架中进行调整相关的复杂性。

## <a name="find-msal-support-for-mobile-apps"></a>查找移动应用的 MSAL 支持

以下 Microsoft 身份验证库（MSAL）类型支持移动应用。

MSAL | 说明
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 用于开发可移植应用程序。 MSAL.NET 支持以下用于构建移动应用程序的平台：通用 Windows 平台（UWP）、Xamarin 和 Xamarin。
![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | 用于使用目标 C 或 Swift 开发本机 iOS 应用程序。
![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | 用于开发适用于 Android 的本机 Android 应用程序。

## <a name="instantiate-the-application"></a>实例化应用程序

### <a name="android"></a>Android

移动应用程序使用 `PublicClientApplication` 类。 下面介绍了如何对其进行实例化：

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

IOS 上的移动应用程序需要实例化 `MSALPublicClientApplication` 类。 若要实例化类，请使用以下代码。 

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[其他 MSALPublicClientApplicationConfig 属性](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options)可以覆盖默认的颁发机构，指定重定向 URI，或更改 MSAL 令牌缓存的行为。 

### <a name="xamarin-or-uwp"></a>Xamarin 或 UWP

本部分介绍如何为 Xamarin、Xamarin 和 UWP 应用程序实例化应用程序。

#### <a name="instantiate-the-application"></a>实例化应用程序

在 Xamarin 或 UWP 中，实例化应用程序的最简单方法是使用以下代码。 在此代码中，`ClientId` 是已注册应用程序的 GUID。

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

其他 `With<Parameter>` 方法设置 UI 父项，覆盖默认的颁发机构，指定遥测的客户端名称和版本，指定重定向 URI，并指定要使用的 HTTP 工厂。 例如，可以使用 HTTP 工厂处理代理并指定遥测和日志记录。 

以下各节提供了有关实例化应用程序的详细信息。

##### <a name="specify-the-parent-ui-window-or-activity"></a>指定父 UI、窗口或活动

在 Android 上，你需要在进行交互式身份验证之前传递父活动。 在 iOS 上，使用 broker 时，需要传入 `ViewController`。 对于 UWP，你可能想要传入父窗口。 在获取令牌时将其传入。 但在创建应用程序时，还可以指定回调作为返回 `UIParent`的委托。

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

在 Android 上，我们建议使用[`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin)。 生成的 `PublicClientApplication` 生成器代码如下例所示：

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>查找更多应用生成参数

有关 `PublicClientApplicationBuilder`上可用的所有方法的列表，请参阅[方法列表](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)。

有关 `PublicClientApplicationOptions`中公开的所有选项的说明，请参阅[参考文档](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)。

## <a name="tasks-for-xamarin-ios"></a>适用于 Xamarin iOS 的任务

如果在 Xamarin iOS 上使用 MSAL.NET，请执行以下任务。

* [重写和实现中的 `OpenUrl` 函数 `AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [启用密钥链组](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [启用令牌缓存共享](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [启用密钥链访问](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

有关详细信息，请参阅[Xamarin iOS 注意事项](msal-net-xamarin-ios-considerations.md)。

## <a name="tasks-for-msal-for-ios-and-macos"></a>适用于 iOS 和 macOS 的 MSAL 的任务

使用 MSAL for iOS 和 macOS 时，需要执行以下任务：

* [实现 `openURL` 回调](#brokered-authentication-for-msal-for-ios-and-macos)
* [启用密钥链访问组](howto-v2-keychain-objc.md)
* [自定义浏览器和 Webview](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>适用于 Xamarin 的任务

如果使用 Xamarin，请执行以下任务：

- [确保当身份验证流的交互式部分结束后，控件返回到 MSAL](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [更新 Android 清单](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [使用嵌入的 web 视图（可选）](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [根据需要进行故障排除](msal-net-xamarin-android-considerations.md#troubleshoot)

有关详细信息，请参阅[Xamarin Android 注意事项](msal-net-xamarin-android-considerations.md)。

有关 Android 上的浏览器的注意事项，请参阅适用[于 MSAL.NET 的 Xamarin 特定注意事项](msal-net-system-browser-android-considerations.md)。

#### <a name="tasks-for-uwp"></a>UWP 任务

在 UWP 上，你可以使用公司网络。 以下各节介绍你应在企业方案中完成的任务。

有关详细信息，请参阅[MSAL.NET 的特定于 UWP 的注意事项](msal-net-uwp-considerations.md)。

## <a name="configure-the-application-to-use-the-broker"></a>将应用程序配置为使用 broker

在 Android 和 iOS 上，代理启用：

- **单一登录（sso）** ：可以对注册到 Azure Active Directory （Azure AD）的设备使用 SSO。 使用 SSO 时，用户无需登录到每个应用程序。
- **设备标识**：此设置启用与 Azure AD 设备相关的条件访问策略。 身份验证过程使用将设备加入工作区时创建的设备证书。
- **应用程序标识验证**：当应用程序调用代理时，它会传递其重定向 URL。 然后，代理将对其进行验证。

### <a name="enable-the-broker-on-xamarin"></a>启用 Xamarin 上的代理

若要在 Xamarin 上启用代理，请在调用 `PublicClientApplicationBuilder.CreateApplication` 方法时使用 `WithBroker()` 参数。 默认情况下，`.WithBroker()` 设置为 true。 

若要为 Xamarin 启用中转身份验证，请按照本文中的[Xamarin 部分](#enable-brokered-authentication-for-xamarin-ios)中的步骤进行操作。

### <a name="enable-the-broker-for-msal-for-android"></a>为适用于 Android 的 MSAL 启用代理

有关在 Android 上启用代理的信息，请参阅[android 上的中转身份验证](brokered-auth.md)。 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>为 iOS 和 macOS 启用 MSAL 代理

默认情况下，将为 iOS 和 macOS 的 MSAL 中的 Azure AD 方案启用中转身份验证。 

以下各节提供有关将应用程序配置为针对 Xamarin 的 MSAL 或 MSAL for iOS 和 macOS 的中转身份验证支持的说明。 在两组说明中，一些步骤不同。

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>为 Xamarin iOS 启用中转身份验证

按照本部分中的步骤操作，使你的 Xamarin iOS 应用与[Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)的应用进行交流。

#### <a name="step-1-enable-broker-support"></a>步骤1：启用代理支持

默认情况下，禁用 Broker 支持。 为单个 `PublicClientApplication` 类启用此类。 通过 `PublicClientApplicationBuilder`创建 `PublicClientApplication` 类时，请使用 `WithBroker()` 参数。 默认情况下，`WithBroker()` 参数设置为 true。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>步骤2：更新 AppDelegate 以处理回调

当 MSAL.NET 调用代理时，代理会回叫你的应用程序。 它使用 `AppDelegate.OpenUrl` 方法回调。 由于 MSAL 会等待来自代理的响应，因此，你的应用程序需要通过合作来调用 MSAL.NET。 通过更新 `AppDelegate.cs` 文件来重写方法来设置此行为，如以下代码所示。

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

每次启动应用程序时，都会调用此方法。 这是一种处理来自代理的响应并完成 MSAL.NET 已启动的身份验证过程的机会。

#### <a name="step-3-set-a-uiviewcontroller"></a>步骤3：设置 UIViewController （）

对于 Xamarin iOS，通常不需要设置对象窗口。 但在这种情况下，您应该对其进行设置，以便发送和接收来自代理的响应。 若要设置对象窗口，请在 `AppDelegate.cs`中设置 `ViewController`。

若要设置对象窗口，请执行以下步骤：

1. 在 `AppDelegate.cs`中，将 `App.RootViewController` 设置为新的 `UIViewController()`。 此设置可确保对 broker 的调用包括 `UIViewController`。 如果未正确设置，则可能会收到此错误：

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. 在 `AcquireTokenInteractive` 调用上，使用 `.WithParentActivityOrWindow(App.RootViewController)`。 传入要使用的对象窗口的引用。 下面是一个示例：

    在 `App.cs` 中：
    ```csharp
       public static object RootViewController { get; set; }
    ```
    在 `AppDelegate.cs` 中：
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    在 `AcquireToken` 调用：
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```
    
#### <a name="step-4-register-a-url-scheme"></a>步骤4：注册 URL 方案

MSAL.NET 使用 Url 调用 broker，然后将 broker 响应返回到应用。 若要完成往返过程，请在 `Info.plist` 文件中注册应用的 URL 方案。 

若要注册应用的 URL 方案，请执行以下步骤：

1. 前缀 `CFBundleURLSchemes` `msauth`。 
1. 将 `CFBundleURLName` 添加到末尾。 遵循以下模式： 

   `$"msauth.(BundleId)"`

   此处 `BundleId` 唯一标识你的设备。 例如，如果 `yourcompany.xforms``BundleId`，则 `msauth.com.yourcompany.xforms`URL 方案。
    
   > [!NOTE]
   > 此 URL 方案将成为重定向 URI 的一部分，用于在收到 broker 响应时唯一标识应用。
    
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
    
#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>步骤5：添加到 LSApplicationQueriesSchemes 节

MSAL 使用 `–canOpenURL:` 来检查设备上是否安装了 broker。 在 iOS 9 中，Apple 锁定了应用程序可以查询的方案。

将 `msauthv2` 添加到 `Info.plist` 文件的 `LSApplicationQueriesSchemes` 部分，如以下代码示例所示：

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>适用于 iOS 和 macOS 的 MSAL 的中转身份验证

默认情况下，为 Azure AD 情况启用中转身份验证。

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>步骤1：更新 AppDelegate 以处理回调

当 iOS 和 macOS 的 MSAL 调用代理时，代理将使用 `openURL` 方法返回到你的应用程序。 由于 MSAL 会等待来自代理的响应，因此，你的应用程序需要合作来回调 MSAL。 通过更新 `AppDelegate.m` 文件来重写方法来设置此功能，如以下代码示例所示。

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
> 如果在 iOS 13 或更高版本中采用了 `UISceneDelegate`，请改为将 MSAL 回拨放到 `UISceneDelegate` 的 `scene:openURLContexts:` 中。 对于每个 URL，只能调用一次 MSAL `handleMSALResponse:sourceApplication:`。
>
> 有关详细信息，请参阅[Apple 文档](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)。

#### <a name="step-2-register-a-url-scheme"></a>步骤2：注册 URL 方案

适用于 iOS 和 macOS 的 MSAL 使用 Url 调用 broker，然后将 broker 响应返回到应用。 若要完成往返行程，请在 `Info.plist` 文件中注册应用的 URL 方案。

为应用注册方案： 

1. 将自定义 URL 方案作为前缀 `msauth`。 

1. 在方案末尾添加捆绑标识符。 遵循以下模式： 

   `$"msauth.(BundleId)"`

   此处 `BundleId` 唯一标识你的设备。 例如，如果 `yourcompany.xforms``BundleId`，则 `msauth.com.yourcompany.xforms`URL 方案。
  
   > [!NOTE]
   > 此 URL 方案将成为重定向 URI 的一部分，用于在收到 broker 响应时唯一标识应用。 请确保在[Azure 门户](https://portal.azure.com)中为你的应用程序注册了格式 `msauth.(BundleId)://auth` 的重定向 URI。
  
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

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>步骤3：添加 LSApplicationQueriesSchemes

添加 `LSApplicationQueriesSchemes` 以允许调用 Microsoft Authenticator 应用（如果已安装）。

> [!NOTE]
> 当使用 Xcode 11 和更高版本编译应用程序时，需要使用 `msauthv3` 方案。 

下面是如何添加 `LSApplicationQueriesSchemes`的示例：

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>适用于 Xamarin 的中转身份验证

MSAL.NET 不支持适用于 Android 的代理。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [获取令牌](scenario-mobile-acquire-token.md)
