---
title: 在 Xamarin iOS 和 Android 应用程序上使用 Microsoft Authenticator 或 Microsoft Intune 公司门户
titleSuffix: Microsoft identity platform
description: 了解如何将可使用 Microsoft Authenticator 从适用于 .NET 的 Azure AD 身份验证库（ADAL.NET）的 Xamarin iOS 应用程序迁移到适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）
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
ms.openlocfilehash: e71e9ef72e7b6caaa3894bb30c6e7e9cf762232a
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802710"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>在 Xamarin 应用程序上使用 Microsoft Authenticator 或 Microsoft Intune 公司门户

在 Android 和 iOS 上，代理 Microsoft Authenticator 或 Microsoft Intune 公司门户启用（仅限 Android）：

- 单一登录（SSO）。 用户无需登录到每个应用程序。
- 设备标识。 代理会访问设备证书，该证书是在加入工作区时在设备上创建的。
- 应用程序标识验证。 当应用程序调用代理时，它会传递其重定向 URL，并且 broker 将对其进行验证。

若要启用其中一项功能，应用程序开发人员在调用 `PublicClientApplicationBuilder.CreateApplication` 方法时需要使用 `WithBroker()` 参数。 默认情况下，`.WithBroker()` 设置为 true。 开发人员还需要执行适用于[iOS](#brokered-authentication-for-ios)或[Android](#brokered-authentication-for-android)应用程序的步骤。

## <a name="brokered-authentication-for-ios"></a>适用于 iOS 的中转身份验证

请按照以下步骤操作，使 Xamarin iOS 应用与[Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)应用进行交流。

### <a name="step-1-enable-broker-support"></a>步骤1：启用代理支持
代理支持基于每个 PublicClientApplication 启用。 此项默认禁用。 通过 PublicClientApplicationBuilder 创建 PublicClientApplication 时，请使用 `WithBroker()` 参数（默认设置为 true）。

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>步骤2：更新 AppDelegate 以处理回调
当适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）调用代理时，代理反过来会通过 `AppDelegate` 类的 `OpenUrl` 方法返回到你的应用程序。 由于 MSAL 会等待来自代理的响应，因此，你的应用程序需要通过合作来调用 MSAL.NET。 若要启用此协作，请更新 `AppDelegate.cs` 文件以重写以下方法。

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

每次启动应用程序时，都会调用此方法。 它用作处理来自代理的响应并完成 MSAL.NET 启动的身份验证过程的机会。

### <a name="step-3-set-a-uiviewcontroller"></a>步骤3：设置 UIViewController （）
仍在 `AppDelegate.cs`中，需要设置对象窗口。 通常，在 Xamarin iOS 中，无需设置 "对象" 窗口。 若要发送和接收来自代理的响应，需要对象窗口。 

为此，您需要执行两项操作。 
1. 在 `AppDelegate.cs`中，将 `App.RootViewController` 设置为新的 `UIViewController()`。 此分配可确保有 UIViewController 调用 broker。 如果未正确设置，则可能会收到此错误： `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. 在 AcquireTokenInteractive 调用中，使用 `.WithParentActivityOrWindow(App.RootViewController)`，并将引用传递给你将使用的对象窗口。

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

### <a name="step-4-register-a-url-scheme"></a>步骤4：注册 URL 方案
MSAL.NET 使用 Url 调用 broker，然后将 broker 响应返回到应用。 若要完成往返行程，请在 `Info.plist` 文件中注册应用的 URL 方案。

`CFBundleURLSchemes` 名称必须包括 `msauth.` 作为前缀，后跟 `CFBundleURLName`。

`$"msauth.(BundleId)"`

例如：

`msauth.com.yourcompany.xforms`

> [!NOTE]
> 此 URL 方案成为重定向 URI 的一部分，用于在应用接收来自代理的响应时唯一标识该应用。

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

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>步骤5：将 broker 标识符添加到 LSApplicationQueriesSchemes 部分
MSAL 使用 `–canOpenURL:` 来检查设备上是否安装了 broker。 在 iOS 9 中，Apple 锁定了应用程序可以查询的方案。 

将 `msauthv2` 添加到 `Info.plist` 文件的 `LSApplicationQueriesSchemes` 部分。

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="step-6-register-your-redirect-uri-in-the-application-portal"></a>步骤6：在应用程序门户中注册重定向 URI
使用 broker 会对重定向 URI 增加额外的要求。 重定向 URI_必须_采用以下格式：
```CSharp
$"msauth.{BundleId}://auth"
```
例如：
```CSharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
请注意，重定向 URI 与 `Info.plist` 文件中包含的 `CFBundleURLSchemes` 名称匹配。

### <a name="step-7-make-sure-the-redirect-uri-is-registered-with-your-app"></a>步骤7：确保重定向 URI 已注册到你的应用

需要在应用注册门户上注册此重定向 URI （ https://portal.azure.com) 作为应用程序的有效重定向 URI。 

门户提供了一个新的体验应用注册门户，可帮助你从包 ID 计算中转回复 URI。

1. 在应用注册中，选择 "**身份验证**"，并选择 **"试用新体验"** 。

   ![尝试新的应用注册体验](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. 选择 "**添加平台**"。

   ![添加平台](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. 支持平台列表时，选择 " **iOS**"。

   ![配置 iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. 输入所需的捆绑 ID，然后选择 "**配置**"。

   ![输入捆绑 ID](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

1. 将为你计算重定向 URI。

   ![复制重定向 URI](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>适用于 Android 的中转身份验证

对于 Android，代理支持不可用。

## <a name="next-steps"></a>后续步骤

了解有关[MSAL.NET 的特定于通用 Windows 平台的注意事项](msal-net-uwp-considerations.md)。