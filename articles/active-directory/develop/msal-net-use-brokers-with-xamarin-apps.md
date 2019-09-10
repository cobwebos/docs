---
title: 在 Xamarin iOS 和 Android 应用程序上使用 Microsoft Authenticator 或 Microsoft Intune 公司门户 |Microsoft
description: 了解如何迁移 Xamarin iOS 应用程序可以使用适用于 .NET 的 Azure AD 身份验证库中的 Microsoft Authenticator （ADAL.NET）到适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e16ad801b3b691d942c2ba0dc723ba72e24cf4c0
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875635"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>在 Xamarin 应用程序上使用 Microsoft Authenticator 或 Microsoft Intune 公司门户

在 Android 和 iOS 上，代理 Microsoft Authenticator 或 Microsoft Intune 公司门户启用（仅限 Android）：

- 单一登录（SSO）。 用户无需登录到每个应用程序。
- 设备标识。 通过访问设备证书，该证书是在加入工作区时在设备上创建的。
- 应用程序标识验证。 当应用程序调用中介时，它会传递其重定向 URL，而中介会验证该 URL。

若要启用其中一项功能，应用程序开发人员需要`WithBroker()`在`PublicClientApplicationBuilder.CreateApplication`调用方法时使用参数。 `.WithBroker()` 默认设置为 true。 开发人员还需要遵循适用于[iOS](#brokered-authentication-for-ios)或[Android](#brokered-authentication-for-android)应用程序的步骤。

## <a name="brokered-authentication-for-ios"></a>适用于 iOS 的中转身份验证

请按照以下步骤操作，使你的 Xamarin iOS 应用与[Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)应用进行交流。

### <a name="step-1-enable-broker-support"></a>步骤 1：启用中介支持
代理支持基于每个 PublicClientApplication 启用。 此项默认禁用。 通过 PublicClientApplicationBuilder `WithBroker()`创建 PublicClientApplication 时，请使用参数（默认设置为 true）。

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>步骤 2：更新 AppDelegate 以处理回调
当 MSAL.NET 调用代理时，代理将依次通过`OpenUrl` `AppDelegate`类的方法回调到你的应用程序。 由于 MSAL 将等待来自中介的响应，因此应用程序需要与中介配合才能回调 MSAL.NET。 若要启用此协作，请`AppDelegate.cs`更新文件以重写以下方法。

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

### <a name="step-3-set-a-uiviewcontroller"></a>步骤 3：设置 UIViewController()
仍在`AppDelegate.cs`中，需要设置对象窗口。 通常，使用 Xamarin iOS 时，无需设置对象窗口，但要发送和接收来自 broker 的响应，你将需要一个对象窗口。 

为此，需要执行两项操作。 
1) 在 `AppDelegate.cs` 中，将 `App.RootViewController` 设置为新的 `UIViewController()`。 此分配将确保有 UIViewController 调用 broker。 如果未正确设置此参数，可能会收到以下错误：`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
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

### <a name="step-4-register-a-url-scheme"></a>步骤 4：注册 URL 方案
MSAL.NET 使用 URL 调用中介，然后将中介响应返回到应用。 若要完成往返过程，需要在 `Info.plist` 文件中注册应用的 URL 方案。

名称必须包含`msauth.` 作为`CFBundleURLName`前缀，后跟。 `CFBundleURLSchemes`

`$"msauth.(BundleId)"`

**例如：** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> 这将成为 RedirectUri 的一部分，用于在接收来自代理的响应时唯一标识应用。

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

### <a name="step-5-lsapplicationqueriesschemes"></a>步骤 5：LSApplicationQueriesSchemes
MSAL 使用 `–canOpenURL:` 来检查是否在设备上安装了中介。 在 iOS 9 中，Apple 锁定了应用程序可以查询的方案。 

将 **`msauthv2`** **添加**到 `Info.plist` 文件的 `LSApplicationQueriesSchemes` 节。

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="step-6-register-your-redirecturi-in-the-application-portal"></a>步骤 6：在应用程序门户中注册 RedirectUri
使用代理会在 redirectUri 上添加额外的要求。 RedirectUri _**必须**_ 具有以下格式：
```CSharp
$"msauth.{BundleId}://auth"
```
例如：
```CSharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
**你会注意到，RedirectUri 与`CFBundleURLSchemes`你`Info.plist`在文件中包含的名称相匹配。**

### <a name="step-7-make-sure-the-redirect-uri-is-registered-with-your-app"></a>步骤7：确保重定向 URI 已注册到你的应用

需要在应用注册门户上注册此重定向 uri （ https://portal.azure.com) 作为应用程序的有效重定向 uri）。 

门户提供了一个新的体验应用注册门户，可帮助你从包 ID 计算中转回复 URI：

1. 在应用注册中，选择 "**身份验证**"，并选择 **"试用新体验"** 。
   ![尝试新的应用注册体验](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. 选择 "**添加平台**"。
   ![添加平台](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. 支持平台列表时，选择 " **iOS**"。
   ![配置 iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. 输入所需的捆绑 ID，然后按 "**注册**"。
   ![输入捆绑 ID](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. 此时会为你计算重定向 URI。
   ![复制重定向 URI](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>适用于 Android 的中转身份验证

Broker 支持不适用于 Android

## <a name="next-steps"></a>后续步骤

[MSAL.NET 的特定于通用 Windows 平台的注意事项](msal-net-uwp-considerations.md)