---
title: 将代理与 Xamarin iOS & Android |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何设置可使用 Microsoft Authenticator 的 Xamarin iOS 应用程序和适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）。 还了解如何从适用于 .NET 的 Azure AD 身份验证库（ADAL.NET）迁移到适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）。
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 25b8aa9b5e80720e9543dafce7970404a62b7d1f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377436"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>在 Xamarin 应用程序上使用 Microsoft Authenticator 或 Intune 公司门户

在 Android 和 iOS 上，代理 Microsoft Authenticator 和特定于 Android 的 Microsoft Intune 公司门户启用：

- **单一登录（SSO）** ：用户无需登录到每个应用程序。
- **设备标识**： broker 访问设备证书。 当设备加入工作区时，会在设备上创建此证书。
- **应用程序标识验证**：当应用程序调用代理时，它会传递其重定向 URL。 Broker 验证 URL。

若要启用其中一项功能，请在调用 `PublicClientApplicationBuilder.CreateApplication` 方法时使用 `WithBroker()` 参数。 默认情况下，`.WithBroker()` 参数设置为 true。 

另外，请使用以下部分中的说明为[iOS](#brokered-authentication-for-ios)应用程序或[Android](#brokered-authentication-for-android)应用程序设置中转身份验证。

## <a name="brokered-authentication-for-ios"></a>适用于 iOS 的中转身份验证

使用以下步骤使你的 Xamarin iOS 应用与[Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)的应用进行通信。

### <a name="step-1-enable-broker-support"></a>步骤1：启用代理支持
您必须为 `PublicClientApplication`的单个实例启用 broker 支持。 默认情况下禁用支持。 通过 `PublicClientApplicationBuilder`创建 `PublicClientApplication` 时，请使用 `WithBroker()` 参数，如以下示例所示。 默认情况下，`WithBroker()` 参数设置为 true。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>步骤2：启用密钥链访问

若要启用密钥链访问，你必须具有应用程序的密钥链访问组。 创建应用程序时，可以使用 `WithIosKeychainSecurityGroup()` API 设置密钥链访问组：

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

有关详细信息，请参阅[启用密钥链访问](msal-net-xamarin-ios-considerations.md#enable-keychain-access)。

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>步骤3：更新 AppDelegate 以处理回调
当适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）调用代理时，代理将通过 `AppDelegate` 类的 `OpenUrl` 方法返回到你的应用程序。 由于 MSAL 会等待 broker 的响应，因此，你的应用程序需要合作来调用 MSAL.NET。 若要启用此协作，请更新 `AppDelegate.cs` 文件以重写以下方法。

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

每次启动应用程序时，都会调用此方法。 它用作处理来自代理的响应并完成 MSAL.NET 已启动的身份验证过程的机会。

### <a name="step-4-set-uiviewcontroller"></a>步骤4：设置 UIViewController （）
仍在 `AppDelegate.cs` 文件中，需要设置一个对象窗口。 通常，对于 Xamarin iOS，无需设置 "对象" 窗口。 但需要一个对象窗口来发送和接收来自代理的响应。 

设置对象窗口： 
1. 在 `AppDelegate.cs` 文件中，将 `App.RootViewController` 设置为新的 `UIViewController()`。 此分配可确保对 broker 的调用包括 `UIViewController`。 如果此设置分配不正确，则可能会收到此错误：

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. 在 `AcquireTokenInteractive` 调用上，使用 `.WithParentActivityOrWindow(App.RootViewController)`，然后将引用传递给你将使用的对象窗口。

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

### <a name="step-5-register-a-url-scheme"></a>步骤5：注册 URL 方案
MSAL.NET 使用 Url 调用 broker，然后将 broker 响应返回到应用。 若要完成往返行程，请在 `Info.plist` 文件中注册应用的 URL 方案。

`CFBundleURLSchemes` 名称必须包含 `msauth.` 作为前缀。 将前缀跟 `CFBundleURLName`。 

在 URL 方案中，`BundleId` 唯一标识应用： `$"msauth.(BundleId)"`。 如果 `com.yourcompany.xforms``BundleId`，则 `msauth.com.yourcompany.xforms`URL 方案。

> [!NOTE]
> 此 URL 方案成为重定向 URI 的一部分，在接收来自代理的响应时，它将唯一地标识应用。

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>步骤6：将 broker 标识符添加到 LSApplicationQueriesSchemes 部分
MSAL 使用 `–canOpenURL:` 来检查设备上是否安装了 broker。 在 iOS 9 中，Apple 锁定了应用程序可以查询的方案。 

将 `msauthv2` 添加到 `Info.plist` 文件的 `LSApplicationQueriesSchemes` 部分，如以下示例中所示：

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>步骤7：在应用程序门户中注册重定向 URI
使用 broker 时，重定向 URI 具有额外的要求。 重定向 URI_必须_采用以下格式：
```csharp
$"msauth.{BundleId}://auth"
```

下面是一个示例： 

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
请注意，重定向 URI 与 `Info.plist` 文件中包含的 `CFBundleURLSchemes` 名称匹配。

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>步骤8：确保重定向 URI 已注册到你的应用

重定向 URI 需要在应用[注册门户](https://portal.azure.com)中注册为应用程序的有效重定向 uri。 

应用注册门户提供了新的体验，以帮助你从捆绑 ID 计算中转回复 URI。 

计算重定向 URI：

1. 在应用注册门户中，选择 "**身份验证** **" > 尝试新体验**。

   ![尝试新的应用注册体验](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. 选择 "**添加平台**"。

   ![添加平台](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. 支持平台列表时，选择 " **iOS**"。

   ![配置 iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. 输入所需的捆绑 ID，然后选择 "**配置**"。

   ![输入捆绑 ID](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

完成这些步骤后，将为你计算重定向 URI。

![复制重定向 URI](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>适用于 Android 的中转身份验证

MSAL.NET 仅支持 Xamarin 平台。 它尚不支持适用于 Xamarin 平台的代理。

MSAL Android 本机库已支持中转身份验证。 有关详细信息，请参阅[Android 中的中转身份验证](brokered-auth.md)。

## <a name="next-steps"></a>后续步骤

了解将[通用 Windows 平台与 MSAL.NET 一起使用时的注意事项](msal-net-uwp-considerations.md)。
