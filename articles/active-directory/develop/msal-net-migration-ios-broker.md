---
title: 将使用 Microsoft Authenticator 的 Xamarin iOS 应用程序迁移到 MSAL.NET
titleSuffix: Microsoft identity platform
description: 了解如何将使用 Microsoft Authenticator 的 Xamarin iOS 应用程序从适用于 .NET 的 Azure AD 身份验证库（ADAL.NET）迁移到适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）。
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
ms.openlocfilehash: 3c64f9f371424eddc7295b6ec40bda1ebdaaafd5
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175587"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>将使用 Microsoft Authenticator 的 iOS 应用程序从 ADAL.NET 迁移到 MSAL.NET

你使用的是适用于 .NET 的 Azure Active Directory 身份验证库（ADAL.NET）和 iOS 代理。 现在可以迁移到适用于 .NET 的[Microsoft 身份验证库](msal-overview.md)（MSAL.NET），它支持从版本4.3 开始的 iOS 上的代理。 

你应从何处着手？ 本文可帮助你将 Xamarin iOS 应用从 ADAL 迁移到 MSAL。

## <a name="prerequisites"></a>必备组件
本文假设已有一个集成了 iOS 代理的 Xamarin iOS 应用。 如果不这样做，请直接转到 MSAL.NET，并在其中开始执行 broker 实现。 有关如何使用新的应用程序在 MSAL.NET 中调用 iOS 代理的信息，请参阅[此文档](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications)。

## <a name="background"></a>背景

### <a name="what-are-brokers"></a>什么是代理？

代理是 Microsoft 在 Android 和 iOS 上提供的应用程序。 （请参阅 iOS 和 Android 上的[Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)应用和 android 上的 Intune 公司门户应用。） 

它们启用：

- 单一登录。
- 设备标识，这是某些[条件访问策略](../conditional-access/overview.md)所必需的。 有关详细信息，请参阅[设备管理](../conditional-access/conditions.md#device-platforms)。
- 应用程序身份验证，在某些企业方案中也是必需的。 有关详细信息，请参阅[Intune 移动应用程序管理（MAM）](https://docs.microsoft.com/intune/mam-faq)。

## <a name="migrate-from-adal-to-msal"></a>从 ADAL 迁移到 MSAL

### <a name="step-1-enable-the-broker"></a>步骤1：启用代理

<table>
<tr><td>当前 ADAL 代码：</td><td>MSAL 对应项：</td></tr>
<tr><td>
在 ADAL.NET 中，基于每个身份验证上下文启用了 broker 支持。 此项默认禁用。 必须设置 

`PlatformParameters` 构造函数中 `useBroker` 的标志设置为 true，以便调用 broker：

```CSharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
此外，在此示例的特定于平台的代码中，在适用于 iOS 的页面呈现器中，设置 `useBroker` 
标记为 true：
```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

然后，在获取令牌调用中包含参数：
```CSharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource, 
                              ClientId, 
                              new Uri(RedirectURI), 
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
在 MSAL.NET 中，代理支持按 PublicClientApplication 启用。 此项默认禁用。 若要启用它，请使用 

`WithBroker()` 参数（默认设置为 true），以便调用 broker：

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
在获取令牌调用中：
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>步骤2：设置 UIViewController （）
在 ADAL.NET 中，你作为 `PlatformParameters`的一部分传入了 UIViewController。 （请参阅步骤1中的示例。）在 MSAL.NET 中，为开发人员提供更大的灵活性，可以使用对象窗口，但在常规的 iOS 使用中不需要此窗口。 若要使用 broker，请设置对象窗口，以便发送和接收来自代理的响应。 
<table>
<tr><td>当前 ADAL 代码：</td><td>MSAL 对应项：</td></tr>
<tr><td>
将 UIViewController 传递到 

`PlatformParameters` 在 iOS 特定平台中。

```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
在 MSAL.NET 中，你可以执行以下两项操作来设置适用于 iOS 的对象窗口：

1. 在 `AppDelegate.cs`中，将 `App.RootViewController` 设置为新的 `UIViewController()`。 此分配可确保有 UIViewController 调用 broker。 如果未正确设置，则可能会收到此错误： `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. 在 AcquireTokenInteractive 调用中，使用 `.WithParentActivityOrWindow(App.RootViewController)`，并传入将使用的对象窗口的引用。

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

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>步骤3：更新 AppDelegate 以处理回调
ADAL 和 MSAL 都调用 broker，而 broker 又通过 `AppDelegate` 类的 `OpenUrl` 方法返回到应用程序。 有关详细信息，请参阅[此文档](msal-net-use-brokers-with-xamarin-apps.md#step-2-update-appdelegate-to-handle-the-callback)。

在 ADAL.NET 和 MSAL.NET 之间没有任何更改。

### <a name="step-4-register-a-url-scheme"></a>步骤4：注册 URL 方案
ADAL.NET 和 MSAL.NET 使用 Url 来调用 broker，并将 broker 响应返回给应用程序。 在应用的 `Info.plist` 文件中注册 URL 方案，如下所示：

<table>
<tr><td>当前 ADAL 代码：</td><td>MSAL 对应项：</td></tr>
<tr><td>
此 URL 方案对你的应用程序是唯一的。
</td><td>
我们的 

`CFBundleURLSchemes` 名称必须包括 

`msauth.`

作为前缀，后跟 `CFBundleURLName`

例如： `$"msauth.(BundleId")`

```CSharp
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

> [!NOTE]
> 此 URL 方案成为重定向 URI 的一部分，用于在应用接收来自代理的响应时唯一标识该应用。

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>步骤5：将 broker 标识符添加到 LSApplicationQueriesSchemes 部分

ADAL.NET 和 MSAL.NET 都使用 `-canOpenURL:` 来检查设备上是否安装了代理。 将 iOS 代理的正确标识符添加到 info.plist 文件的 LSApplicationQueriesSchemes 节，如下所示：

<table>
<tr><td>当前 ADAL 代码：</td><td>MSAL 对应项：</td></tr>
<tr><td>
使用 

`msauth`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
使用 

`msauthv2`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>步骤6：在门户中注册重定向 URI

ADAL.NET 和 MSAL.NET 在针对代理的重定向 URI 上添加额外的要求。 在门户中向你的应用程序注册重定向 URI。
<table>
<tr><td>当前 ADAL 代码：</td><td>MSAL 对应项：</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

示例： 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

示例：

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

有关如何在门户中注册重定向 URI 的详细信息，请参阅[在 Xamarin iOS 应用程序中利用代理](msal-net-use-brokers-with-xamarin-apps.md#step-7-make-sure-the-redirect-uri-is-registered-with-your-app)。

## <a name="next-steps"></a>后续步骤

了解有关[MSAL.NET 的 Xamarin iOS 特定注意事项](msal-net-xamarin-ios-considerations.md)。 
