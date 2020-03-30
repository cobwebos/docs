---
title: 使用经纪人与Xamarin iOS&安卓 |蔚蓝
titleSuffix: Microsoft identity platform
description: 了解如何设置 Xamarin iOS 应用程序，这些应用程序可以使用 Microsoft 身份验证器和 Microsoft 身份验证库进行 .NET （MSAL.NET）。 还了解如何从 .NET （ADAL.NET） 的 Azure AD 身份验证库迁移到 .NET （MSAL.NET） 的 Microsoft 身份验证库。
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
ms.openlocfilehash: 1a57173311278c5e3e0304aeb12d4d6999379eb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262784"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>在 Xamarin 应用程序中使用 Microsoft 身份验证器或 Intune 公司门户

在 Android 和 iOS 上，微软身份验证器和特定于 Android 的 Microsoft Intune 公司门户等代理支持：

- **单一登录 （SSO）：** 用户不需要登录到每个应用程序。
- **设备标识**：代理访问设备证书。 此证书在设备加入工作区时在设备上创建。
- **应用程序标识验证**：当应用程序调用代理时，它将传递其重定向 URL。 代理验证 URL。

要启用这些功能之一，`WithBroker()`请调用 方法时使用`PublicClientApplicationBuilder.CreateApplication`参数。 默认情况下`.WithBroker()`，参数设置为 true。 

还使用以下部分中的说明为[iOS](#brokered-authentication-for-ios)应用程序或[Android](#brokered-authentication-for-android)应用程序设置代理身份验证。

## <a name="brokered-authentication-for-ios"></a>适用于 iOS 的中介身份验证

使用以下步骤使 Xamarin.iOS 应用能够与[Microsoft 身份验证器](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)应用对话。

### <a name="step-1-enable-broker-support"></a>第 1 步：启用代理支持
您必须对`PublicClientApplication`的各个实例启用代理支持。 默认情况下，支持处于禁用状态。 创建`PublicClientApplication`时，`PublicClientApplicationBuilder`请使用`WithBroker()`参数，如下例所示。 默认情况下`WithBroker()`，参数设置为 true。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>第 2 步：启用钥匙串访问

要启用钥匙串访问，您必须为应用程序设置钥匙串访问组。 创建应用程序时，可以使用 `WithIosKeychainSecurityGroup()` API 来设置密钥链访问组：

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

有关详细信息，请参阅[启用密钥链访问](msal-net-xamarin-ios-considerations.md#enable-keychain-access)。

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>第 3 步：更新应用委托以处理回调
当 .NET （MSAL.NET） 的 Microsoft 身份验证库调用代理时，代理会通过`OpenUrl``AppDelegate`类的方法回调您的应用程序。 由于 MSAL 等待代理的响应，因此您的应用程序需要合作来回拨MSAL.NET。 若要启用此协作，请更新 `AppDelegate.cs` 文件以重写以下方法。

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

每次启动应用程序时都会调用此方法。 它用作处理来自代理的响应并完成MSAL.NET启动的身份验证过程的机会。

### <a name="step-4-set-uiviewcontroller"></a>第 4 步：设置 UIView 控制器（）
仍然在`AppDelegate.cs`文件中，您需要设置一个对象窗口。 通常，对于 Xamarin iOS，您不需要设置对象窗口。 但是，您确实需要一个对象窗口来发送和接收来自代理的响应。 

要设置对象窗口： 
1. 在文件中`AppDelegate.cs`，设置为`App.RootViewController`新的`UIViewController()`。 此分配可确保对代理的调用包括`UIViewController`。 如果此设置分配不正确，您可能会收到此错误：

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. 在通话`AcquireTokenInteractive`时，使用`.WithParentActivityOrWindow(App.RootViewController)`并传递对要使用的对象窗口的引用。

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

### <a name="step-5-register-a-url-scheme"></a>第 5 步：注册 URL 方案
MSAL.NET使用 URL 调用代理，然后将代理响应返回到你的应用。 要完成往返行程，请在文件中注册应用的`Info.plist`URL 方案。

名称`CFBundleURLSchemes`必须作为前`msauth.`缀包含。 按照 前缀`CFBundleURLName`一起。 

在 URL 方案中`BundleId`，唯一标识应用： `$"msauth.(BundleId)"`。 因此，`BundleId`如果是`com.yourcompany.xforms`，则 URL`msauth.com.yourcompany.xforms`方案为 。

> [!NOTE]
> 此 URL 方案将成为重定向 URI 的一部分，该 URI 在收到来自代理的响应时唯一标识应用。

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>步骤 6：将代理标识符添加到 LS应用程序查询计划部分

MSAL`–canOpenURL:`用于检查代理是否安装在设备上。 在 iOS 9 中，Apple 锁定了应用程序可以查询的方案。 

添加到`msauthv2`文件的`LSApplicationQueriesSchemes`部分，`Info.plist`如以下示例所示：

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>第 7 步：在应用程序门户中注册重定向 URI

当您使用代理时，重定向 URI 有额外的要求。 重定向 URI __ 必须采用以下格式：

```csharp
$"msauth.{BundleId}://auth"
```

下面是一个示例：

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

请注意，重定向 URI 与您`CFBundleURLSchemes`包含在`Info.plist`文件中的名称匹配。

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>第 8 步：确保重定向 URI 已注册到应用

重定向 URI 需要在[应用注册门户](https://portal.azure.com)上注册为应用程序的有效重定向 URI。 

应用注册门户提供了新的体验，可帮助您从捆绑包 ID 计算代理回复 URI。 

要计算重定向 URI：

1. 在应用注册门户中，选择 **"身份验证** > **试用"新体验**。

   ![尝试新的应用注册体验](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. 选择“添加平台”。****

   ![添加平台](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. 如果支持平台列表，请选择“iOS”。****

   ![配置 iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. 依请求输入捆绑 ID，然后选择“配置”。****

   ![输入捆绑包 ID](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

完成这些步骤后，将为您计算重定向 URI。

![复制重定向 URI](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>适用于 Android 的中介身份验证

### <a name="step-1-enable-broker-support"></a>第 1 步：启用代理支持

中介支持是按 PublicClientApplication 启用的。 此项默认禁用。 在创建`WithBroker()``IPublicClientApplication`通过`PublicClientApplicationBuilder`时，使用 参数（默认情况下设置为 true）。

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>第 2 步：更新应用委托以处理回调

当MSAL.NET调用代理时，代理将反过来使用 OnActivityResult（） 方法回电回您的应用程序。 由于 MSAL 将等待来自代理的响应，因此应用程序需要将结果路由到MSAL.NET。
这可以通过重写 OnActivityResult（）`SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)`方法将结果路由到 实现，如下所示

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

每次启动代理应用程序时都会调用此方法，并用作处理代理响应并完成由 MSAL.NET 启动的身份验证过程的机会。

### <a name="step-3-set-an-activity"></a>第 3 步：设置活动

要使代理身份验证正常工作，您需要设置活动，以便 MSAL 可以从代理发送和接收响应。

为此，您需要将活动（通常是 MainActivity）提供给 作为父对象的 活动`WithParentActivityOrWindow(object parent)`。 

**例如：**

在“获取令牌”调用中：

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>第 4 步：在应用程序门户中注册重定向 Uri

MSAL 使用 URL 调用代理，然后返回到你的应用。 要完成该往返行程，您需要为应用注册 URL 方案。 此重定向 URI 需要在 Azure AD 应用注册门户上注册为应用程序的有效重定向 URI。


应用程序所需的重定向 URI 取决于用于对 APK 进行签名的证书。

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

URI`hgbUYHVBYUTvuvT&Y6tr554365466=`的最后一部分是 APK 签名的签名，base64 编码。
但是，在使用 Visual Studio 的应用程序开发阶段，如果您调试代码而不使用特定证书对 apk 进行签名，Visual Studio 将为您签名以进行调试，为 APK 提供机器，它是建立在。 因此，每次在不同的计算机上构建应用时，都需要更新应用程序代码中的重定向 URI 以及应用程序在 Azure 门户中的注册，以便对 MSAL 进行身份验证。 

调试时，可能会遇到 MSAL 异常（或日志消息），指出提供的重定向 URI 不正确。 **此异常还将为您提供与**正在调试的当前计算机一起使用的重定向 URI。 您可以使用此重定向 URI 继续开发。

准备好完成代码后，请确保在代码中更新重定向 URI，并在应用程序在 Azure 门户中的注册中更新，以使用要签名 APK 的证书的签名。

实际上，这意味着您必须为团队的每个成员注册重定向 URI，并为 APK 的生产签名版本注册重定向 URI。

您也可以自己计算此签名，类似于 MSAL 如何计算： 

```CSharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

您还可以选择使用带有以下命令的 keytool 获取包的签名：

对于 Windows：`keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

对于 Mac：`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>后续步骤

了解[将通用 Windows 平台与MSAL.NET 的注意事项](msal-net-uwp-considerations.md)。
