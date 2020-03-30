---
title: 配置调用 Web API 的移动应用 |蔚蓝
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: fc25f13d0b0b8a264dcd47a5fdebb0533e93fb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132487"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>配置调用 Web API 的移动应用

创建应用程序后，您将学习如何使用应用注册参数配置代码。 移动应用程序在安装到其创建框架中提出了一些复杂性。

## <a name="find-msal-support-for-mobile-apps"></a>查找移动应用的 MSAL 支持

以下 Microsoft 身份验证库 （MSAL） 类型支持移动应用。

MSAL | 描述
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 用于开发便携式应用程序。 MSAL.NET支持以下构建移动应用程序的平台：通用 Windows 平台 （UWP）、Xamarin.iOS 和 Xamarin.Android。
![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | 用于使用目标 C 或 Swift 开发本机 iOS 应用程序。
![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | 用于在 Java 中为 Android 开发本机 Android 应用程序。

## <a name="instantiate-the-application"></a>实例化应用程序

### <a name="android"></a>Android

移动应用程序使用 `PublicClientApplication` 类。 下面了解如何实例化：

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

iOS 上的移动应用程序需实例化 `MSALPublicClientApplication` 类。 要实例化类，请使用以下代码。 

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[其他 MSALPublicClientClientConfig 属性](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options)可以覆盖默认权限、指定重定向 URI 或更改 MSAL 令牌缓存的行为。 

### <a name="xamarin-or-uwp"></a>Xamarin 或 UWP

本节介绍如何实例化 Xamarin.iOS、Xamarin.Android 和 UWP 应用程序的应用程序。

#### <a name="instantiate-the-application"></a>实例化应用程序

在 Xamarin 或 UWP 中，实例化应用程序的最简单方法是使用以下代码。 在此代码中，`ClientId`是已注册应用的 GUID。

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

其他`With<Parameter>`方法设置 UI 父级，覆盖默认权限，为遥测指定客户端名称和版本，指定重定向 URI，并指定要使用的 HTTP 工厂。 例如，可以使用 HTTP 工厂来处理代理并指定遥测和日志记录。 

以下各节提供有关实例化应用程序的详细信息。

##### <a name="specify-the-parent-ui-window-or-activity"></a>指定父 UI、窗口或活动

在 Android 上，需要在执行交互式身份验证之前传递父活动。 在 iOS 上，当您使用代理时，您需要转接`ViewController`。 在 UWP 上，可以相同的方式传入父窗口。 获取令牌时将其传递。 但是，在创建应用时，还可以指定回调作为返回`UIParent`的委托。

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

在 Android 上，我们建议您使用[`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin)。 生成的`PublicClientApplication`生成器代码如下所示：

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>查找更多应用构建参数

有关 上`PublicClientApplicationBuilder`所有可用的方法的列表，请参阅[方法列表](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)。

有关 中`PublicClientApplicationOptions`公开的所有选项的说明，请参阅[参考文档](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)。

## <a name="tasks-for-xamarin-ios"></a>Xamarin iOS 的任务

如果您在 Xamarin iOS 上使用MSAL.NET，则执行以下任务。

* [覆盖和实现`OpenUrl`函数`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [启用钥匙串组](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [启用令牌缓存共享](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [启用密钥链访问](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

有关详细信息，请参阅[Xamarin iOS 注意事项](msal-net-xamarin-ios-considerations.md)。

## <a name="tasks-for-msal-for-ios-and-macos"></a>适用于 iOS 和 macOS 的 MSAL 任务

当您将 MSAL 用于 iOS 和 macOS 时，这些任务是必需的：

* [实现 `openURL` 回调](#brokered-authentication-for-msal-for-ios-and-macos)
* [启用密钥链访问组](howto-v2-keychain-objc.md)
* [自定义浏览器和 WebView](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Xamarin.安卓的任务

如果您使用 Xamarin.Android，则执行以下任务：

- [确保在身份验证流的交互部分结束后将控制返回 MSAL](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [更新 Android 清单](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [使用嵌入式 Web 视图（可选）](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [根据需要进行故障排除](msal-net-xamarin-android-considerations.md#troubleshoot)

有关详细信息，请参阅[Xamarin.安卓注意事项](msal-net-xamarin-android-considerations.md)。

有关 Android 上的浏览器的注意事项，请参阅[Xamarin.Android 特定于MSAL.NET的注意事项](msal-net-system-browser-android-considerations.md)。

#### <a name="tasks-for-uwp"></a>UWP 的任务

在 UWP 上，可以使用企业网络。 以下各节介绍了应在公司方案中完成的任务。

有关详细信息，请参阅有关[MSAL.NET 的特定于 UWP 的注意事项](msal-net-uwp-considerations.md)。

## <a name="configure-the-application-to-use-the-broker"></a>将应用程序配置为使用代理

在 Android 和 iOS 上，中介可以实现：

- **单一登录 （SSO）：** 对于在 Azure 活动目录 （Azure AD） 中注册的设备，可以使用 SSO。 使用 SSO 时，用户无需登录到每个应用程序。
- **设备标识**：此设置启用与 Azure AD 设备相关的条件访问策略。 身份验证过程使用设备加入工作区时创建的设备证书。
- **应用程序标识验证**：当应用程序调用代理时，它将传递其重定向 URL。 然后，经纪人验证它。

### <a name="enable-the-broker-on-xamarin"></a>在 Xamarin 上启用中介

要启用 Xamarin 上的代理，`WithBroker()`在调用`PublicClientApplicationBuilder.CreateApplication`方法时使用 参数。 默认情况下，`.WithBroker()`设置为 true。 

要为 Xamarin.iOS 启用代理身份验证，请按照本文中的[Xamarin.iOS 部分](#enable-brokered-authentication-for-xamarin-ios)中的步骤操作。

### <a name="enable-the-broker-for-msal-for-android"></a>为用于 Android 的 MSAL 启用中介

有关在 Android 上启用代理的信息，请参阅[Android 上的代理身份验证](brokered-auth.md)。 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>为用于 iOS 和 macOS 的 MSAL 启用中介

默认情况下，对于适用于 iOS 和 macOS 的 MSAL 中的 Azure AD 方案启用代理身份验证。 

以下各节提供了用于为 Xamarin.iOS 或 msAL 配置应用程序的指令，用于 Xamarin.iOS 的 MSAL 或针对 iOS 和 macOS 的 MSAL。 在两组说明中，某些步骤不同。

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>为 Xamarin iOS 启用代理身份验证

按照本节中的步骤，使 Xamarin.iOS 应用能够与 Microsoft[身份验证器](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)应用对话。

#### <a name="step-1-enable-broker-support"></a>第 1 步：启用代理支持

默认情况下禁用代理支持。 为单个`PublicClientApplication`类启用它。 通过`WithBroker()`创建`PublicClientApplication`类时使用 参数`PublicClientApplicationBuilder`。 默认情况下`WithBroker()`，参数设置为 true。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>第 2 步：更新应用委托以处理回调

当MSAL.NET调用代理时，代理然后调用回您的应用程序。 它使用`AppDelegate.OpenUrl`方法回电。 由于 MSAL 会等待来自中介的响应，因此应用程序需要进行协作才能回调 MSAL.NET。 通过更新`AppDelegate.cs`文件以重写方法来设置此行为，如下代码所示。

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

每次启动应用程序都会调用此方法。 这是一个处理来自代理的响应并完成MSAL.NET启动的身份验证过程的机会。

#### <a name="step-3-set-a-uiviewcontroller"></a>第 3 步：设置 UIView 控制器（）

对于 Xamarin iOS，您通常不需要设置对象窗口。 但是在这种情况下，您应该设置它，以便您可以发送和接收来自代理的响应。 要在 中`AppDelegate.cs`设置对象窗口，在 中`ViewController`设置 。

要设置对象窗口，请按照以下步骤操作：

1. 在 `AppDelegate.cs` 中，将 `App.RootViewController` 设置为新的 `UIViewController()`。 此设置可确保对代理的调用包括`UIViewController`。 如果未正确设置此参数，可能会收到以下错误：

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. 在通话`AcquireTokenInteractive`中，使用`.WithParentActivityOrWindow(App.RootViewController)`。 将引用传递给要使用的对象窗口。 下面是一个示例：

    在 `App.cs` 中：
    ```csharp
       public static object RootViewController { get; set; }
    ```
    在 `AppDelegate.cs` 中：
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    在呼叫`AcquireToken`中：
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```
    
#### <a name="step-4-register-a-url-scheme"></a>第 4 步：注册 URL 方案

MSAL.NET 使用 URL 调用中介，然后将中介响应返回到应用。 要完成往返行程，请在`Info.plist`文件中注册应用的 URL 方案。 

要注册应用的 URL 方案，请按照以下步骤操作：

1. 前`CFBundleURLSchemes`缀`msauth`为 。 
1. 添加到`CFBundleURLName`末尾。 遵循以下模式： 

   `$"msauth.(BundleId)"`

   在这里，`BundleId`唯一标识您的设备。 例如，如果`BundleId`为`yourcompany.xforms`，则 URL`msauth.com.yourcompany.xforms`方案为 。
    
   > [!NOTE]
   > 此 URL 方案将成为重定向 URI 的一部分，该 URI 在收到代理的响应时唯一标识应用。
    
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
    
#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>第 5 步：添加到 LS 应用程序查询计划部分

MSAL 使用 `–canOpenURL:` 来检查是否在设备上安装了中介。 在 iOS 9 中，Apple 锁定了应用程序可以查询的方案。

添加到`msauthv2`文件的`LSApplicationQueriesSchemes`部分，`Info.plist`如以下代码示例所示：

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>针对 iOS 和 macOS 的 MSAL 的代理身份验证

默认情况下，为 Azure AD 方案启用代理身份验证。

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>第 1 步：更新应用委托以处理回调

当适用于 iOS 和 macOS 的 MSAL 调用代理时，代理使用`openURL`方法回调您的应用程序。 由于 MSAL 等待来自代理的响应，因此您的应用程序需要合作来回拨 MSAL。 通过更新`AppDelegate.m`文件以覆盖该方法来设置此功能，如下代码示例所示。

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
> 如果您在 iOS 13 或更高版本中采用`UISceneDelegate`，则将 MSAL 回调`scene:openURLContexts:`放在`UISceneDelegate` 只能对每个 URL 调用 MSAL `handleMSALResponse:sourceApplication:` 一次。
>
> 有关详细信息，请参阅 Apple[文档](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)。

#### <a name="step-2-register-a-url-scheme"></a>第 2 步：注册 URL 方案

适用于 iOS 和 macOS 的 MSAL 使用 URL 调用代理，然后将代理响应返回到你的应用。 若要完成往返过程，请在 `Info.plist` 文件中注册应用的 URL 方案。

要为应用注册方案，请： 

1. 使用 `msauth` 作为自定义 URL 方案的前缀。 

1. 将捆绑包标识符添加到方案的末尾。 遵循以下模式： 

   `$"msauth.(BundleId)"`

   在这里，`BundleId`唯一标识您的设备。 例如，如果`BundleId`为`yourcompany.xforms`，则 URL`msauth.com.yourcompany.xforms`方案为 。
  
   > [!NOTE]
   > 此 URL 方案将成为重定向 URI 的一部分，该 URI 在收到代理的响应时唯一标识应用。 确保格式`msauth.(BundleId)://auth`中的重定向 URI 已为应用程序在[Azure 门户](https://portal.azure.com)中注册。
  
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

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>第 3 步：添加 LS 应用程序查询方案

添加`LSApplicationQueriesSchemes`以允许对 Microsoft 身份验证器应用的调用（如果已安装）。

> [!NOTE]
> 使用`msauthv3`Xcode 11 及更高版本编译应用时，需要该方案。 

下面是如何添加`LSApplicationQueriesSchemes`的示例：

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>适用于 Xamarin.Android 的中介身份验证

MSAL.NET不支持安卓的经纪人。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [获取令牌](scenario-mobile-acquire-token.md)
