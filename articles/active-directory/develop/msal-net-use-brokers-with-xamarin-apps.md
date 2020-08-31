---
title: 将中介与 Xamarin、iOS 和 Android 配合使用 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何设置可使用 Microsoft Authenticator 以及适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 的 Xamarin iOS 应用程序。 此外，了解如何从适用于 .NET 的 Azure AD 身份验证库 (ADAL.NET) 迁移到适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET)。
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 7fa13a328a55b0e9eaa546e70bf0711f4f011cf1
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89068520"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>在 Xamarin 应用程序中使用 Microsoft Authenticator 或 Intune 公司门户

在 Android 和 iOS 上，Microsoft Authenticator 和特定于 Android 的 Microsoft Intune 公司门户等中介可以启用：

- **单一登录 (SSO)** ：用户无需登录到每个应用程序。
- **设备标识**：中介访问设备证书。 当设备加入工作区时，将在设备上创建此证书。
- **应用程序标识验证**：应用程序在调用中介时会传递其重定向 URL。 中介将验证该 URL。

若要启用这些功能之一，请在调用 `PublicClientApplicationBuilder.CreateApplication` 方法时使用 `WithBroker()` 参数。 `.WithBroker()` 参数默认设置为 true。

适用于 .NET (MSAL.NET) 的 Microsoft 身份验证库中的中转身份验证设置因平台而异：

* [iOS 应用程序](#brokered-authentication-for-ios)
* [Android 应用程序](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>适用于 iOS 的中介身份验证

使用以下步骤使你的 Xamarin iOS 应用与 [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) 的应用进行通信。 如果面向的是 iOS 13，请考虑阅读 [Apple 的重大 API 更改](./msal-net-xamarin-ios-considerations.md)。

### <a name="step-1-enable-broker-support"></a>步骤 1：启用中介支持

您必须为的单个实例启用代理支持 `PublicClientApplication` 。 默认情况下禁用支持。 通过创建时 `PublicClientApplication` `PublicClientApplicationBuilder` ，请使用 `WithBroker()` 参数，如下例所示。 `WithBroker()` 参数默认设置为 true。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>步骤2：启用密钥链访问

若要启用密钥链访问，你必须具有应用程序的密钥链访问组。 创建应用程序时，可以使用 `WithIosKeychainSecurityGroup()` API 来设置密钥链访问组：

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

有关详细信息，请参阅[启用密钥链访问](msal-net-xamarin-ios-considerations.md#enable-keychain-access)。

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>步骤 3：更新 AppDelegate 以处理回调

当 MSAL.NET 调用代理时，代理将通过类的方法回调到你的应用程序 `OpenUrl` `AppDelegate` 。 由于 MSAL 会等待 broker 的响应，因此，你的应用程序需要合作来调用 MSAL.NET。 若要启用此协作，请更新 *AppDelegate.cs* 文件以重写以下方法。

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

### <a name="step-4-set-uiviewcontroller"></a>步骤4：设置 UIViewController ( # A1

仍在 *AppDelegate.cs* 文件中，需要设置一个对象窗口。 通常不需要为 Xamarin iOS 设置对象窗口，但需要使用对象窗口来发送和接收来自代理的响应。

设置对象窗口：

1. 在 *AppDelegate.cs* 文件中，将设置 `App.RootViewController` 为新的 `UIViewController()` 。 此分配可确保对 broker 的调用包括 `UIViewController` 。 如果此设置分配不正确，则可能会收到此错误：

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. 在 `AcquireTokenInteractive` 调用时使用， `.WithParentActivityOrWindow(App.RootViewController)` 然后将引用传递给你将使用的对象窗口。

    在 *App.cs*中：

    ```csharp
       public static object RootViewController { get; set; }
    ```

    在 *AppDelegate.cs*中：

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

### <a name="step-5-register-a-url-scheme"></a>步骤5：注册 URL 方案

MSAL.NET 使用 Url 调用 broker，然后将 broker 响应返回到应用。 若要完成往返行程，请在 *info.plist* 文件中注册应用的 URL 方案。

该 `CFBundleURLSchemes` 名称必须包含 `msauth.` 作为前缀。 前缀后跟 `CFBundleURLName` 。

在 URL 方案中， `BundleId` 唯一标识应用： `$"msauth.(BundleId)"` 。 因此 `BundleId` ，如果为 `com.yourcompany.xforms` ，则 URL 方案为 `msauth.com.yourcompany.xforms` 。

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

MSAL 使用 `–canOpenURL:` 检查是否在设备上安装了 broker。 在 iOS 9 中，Apple 锁定了应用程序可以查询的方案。

将添加 `msauthv2` 到 `LSApplicationQueriesSchemes` *info.plist* 文件的节中，如以下示例中所示：

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-add-a-redirect-uri-to-your-app-registration"></a>步骤7：将重定向 URI 添加到应用注册

使用 broker 时，重定向 URI 具有额外的要求。 重定向 URI __ 必须采用以下格式：

```csharp
$"msauth.{BundleId}://auth"
```

下面是一个示例：

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth";
```

请注意，重定向 URI 与 `CFBundleURLSchemes` *info.plist* 文件中包含的名称相匹配。

将重定向 URI 添加到 [Azure 门户](https://portal.azure.com)中的应用注册。 若要生成格式正确的重定向 URI，请使用 Azure 门户中 **应用注册** 从捆绑 ID 生成中转重定向 uri。

**生成重定向 URI：**

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 选择**Azure Active Directory**  >  注册应用 > Azure Active Directory**应用注册**
1. 选择**身份验证**  >  **添加平台**  >  **iOS/macOS**
1. 输入你的捆绑 ID，然后选择 " **配置**"。

    复制 " **重定向 uri** " 文本框中显示的已生成重定向 uri，使其包含在代码中：

    :::image type="content" source="media/msal-net-use-brokers-with-xamarin-apps/portal-01-ios-platform-settings.png" alt-text="在 Azure 门户中生成了重定向 URI 的 iOS 平台设置":::
1. 选择 **"完成"** 以完成重定向 URI 的生成。

## <a name="brokered-authentication-for-android"></a>适用于 Android 的中介身份验证

### <a name="step-1-enable-broker-support"></a>步骤 1：启用中介支持

已按 `PublicClientApplication` 启用中介支持。 此项默认禁用。 `WithBroker()`默认情况下，在通过创建时) 使用参数 (设置为 true `IPublicClientApplication` `PublicClientApplicationBuilder` 。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) // See step #4
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>步骤 2：更新 AppDelegate 以处理回调

当 MSAL.NET 调用代理时，代理将转而用方法回调到应用程序 `OnActivityResult()` 。 由于 MSAL 将等待来自代理的响应，因此应用程序需要将结果路由到 MSAL.NET。

`SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)`通过重写方法将结果路由到方法， `OnActivityResult()` 如下所示：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

每次启动代理应用程序时都将调用此方法，并将其用作处理来自代理的响应并完成 MSAL.NET 启动的身份验证过程的机会。

### <a name="step-3-set-an-activity"></a>步骤3：设置活动

若要启用中转身份验证，请设置活动，使 MSAL 可以在代理和代理之间发送和接收响应。 为此，请将活动 (通常是 `MainActivity` `WithParentActivityOrWindow(object parent)` 父对象) 提供。

例如，在对的调用中 `AcquireTokenInteractive()` ：

```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-add-a-redirect-uri-to-your-app-registration"></a>步骤4：将重定向 URI 添加到应用注册

MSAL 使用 Url 调用 broker，然后返回到应用。 若要完成这种往返过程，需要使用[Azure 门户](https://portal.azure.com)注册应用程序的**重定向 URI** 。

应用程序的重定向 URI 的格式取决于用于对 APK 进行签名的证书。 例如：

```
msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

URI 的最后一部分 `hgbUYHVBYUTvuvT&Y6tr554365466=` 是 APK 签名时所用的 Base64 编码版本的签名。 在 Visual Studio 中开发应用程序时，如果你在调试代码时未使用特定证书对 APK 进行签名，则 Visual Studio 会为你签署 APK，以便进行调试。 当 Visual Studio 以这种方式为你签署 APK 时，它将为它生成的计算机提供唯一的签名。 因此，每次在不同的计算机上生成应用时，需要更新应用程序代码中的重定向 URI 和应用程序在 Azure 门户中的注册，以便使用 MSAL 进行身份验证。

调试时，你可能会遇到 (或日志消息) 的 MSAL 异常，指出提供的重定向 URI 不正确。 **异常或日志消息还指明了应** 用于正在调试的当前计算机的重定向 URI。 只要更新代码中的重定向 URI 并将提供的重定向 URI 添加到 Azure 门户中的应用注册，就可以使用提供的重定向 URI 继续开发应用。

准备好完成代码后，请更新代码中的重定向 URI 和应用程序在 Azure 门户中的注册，以使用为 APK 签名的证书的签名。

在实践中，这意味着应考虑为开发团队中的每个成员添加一个重定向 URI， *以及* APK 的生产签名版本的重定向 uri。

你可以自行计算签名，类似于 MSAL 的作用：

```csharp
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

你还可以选择使用 **keytool** 和以下命令获取包的签名：

* Windows：
    ```console
    keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
    ````
* macOS：
    ```console
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ````

### <a name="step-5-optional-fall-back-to-the-system-browser"></a>步骤 5 (可选) ：回退到系统浏览器

如果将 MSAL 配置为使用 broker，但未安装 broker，则 MSAL 将回退为使用 (浏览器) 的 web 视图。 MSAL 将尝试使用设备上的默认系统浏览器进行身份验证，因为重定向 URI 是为 broker 配置的，并且系统浏览器不知道如何使用它导航到 MSAL。 若要避免失败，可以使用在步骤4中使用的 broker 重定向 URI 配置 *意向筛选器* 。

修改应用程序的清单以添加意向筛选器：

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="Package Name"
                    android:path="/Package Signature"/>
```

例如，如果你具有的重定向 URI `msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=` ，则清单应类似于以下 XML 代码段。

值中的签名前面的正斜杠 (`/`) `android:path` 是 **必需**的。

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="com.microsoft.xforms.testApp"
                    android:path="/hgbUYHVBYUTvuvT&Y6tr554365466="/>
```

作为替代方法，你可以将 MSAL 配置为回退到嵌入浏览器，而不依赖于重定向 URI：

```csharp
.WithUseEmbeddedWebUi(true)
```

## <a name="troubleshooting-tips-for-android-brokered-authentication"></a>Android 中转身份验证的故障排除提示

下面是有关在 Android 上实施中转身份验证时避免问题的一些提示：

- **重定向 uri** -将重定向 uri 添加到 [Azure 门户](https://portal.azure.com/)中的应用程序注册。 缺少或不正确的重定向 URI 是开发人员遇到的常见问题。
- **代理版本** -安装所需的代理应用的最低版本。 这两个应用可用于 Android 上的中转身份验证。
  - [Intune 公司门户](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) (版本5.0.4689.0 或更高版本) 
  - [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) (版本6.2001.0140 或更高版本) 。
- **Broker 优先级** -在安装了多个代理时，MSAL 与设备上 *安装的第一个代理* 通信。

    示例：如果首先安装 Microsoft Authenticator 然后安装 Intune 公司门户，则 *只* 会在 Microsoft Authenticator 上进行中转身份验证。
- **日志** -如果你遇到中转身份验证问题，查看 broker 日志可能会帮助你诊断原因。
  - 查看 Microsoft Authenticator 日志：

    1. 选择应用程序右上角的菜单按钮。
    1. 选择 "**帮助**" "  >  **发送日志**"  >  **View Logs**。
    1. 选择 " **全部复制** "，将代理日志复制到设备的剪贴板上。

    用这些日志进行调试的最佳方式是通过电子邮件向自己发送电子邮件，并在开发计算机上查看它们。 您可能会发现，在您的计算机上（而不是在设备本身）分析日志会更容易。 你还可以使用 Android 上的测试编辑器将日志另存为文本文件，然后使用 USB 电缆将该文件复制到计算机上。

  - 查看 Intune 公司门户日志：

    1. 在应用程序的左上角选择菜单按钮
    1. 选择**设置**  >  **诊断数据**
    1. 选择 " **复制日志** "，将 broker 日志复制到设备的 SD 卡。
    1. 使用 USB 电缆将设备连接到计算机，以便查看开发计算机上的日志。

    有了日志后，可以通过相关 ID 搜索它们，以进行身份验证尝试。 相关 ID 附加到每个身份验证请求。 若要查找 Microsoft 标识平台身份验证终结点返回的错误，请搜索 `AADSTS` 。

## <a name="next-steps"></a>后续步骤

了解将 [通用 Windows 平台与 MSAL.NET 一起使用时的注意事项](msal-net-uwp-considerations.md)。
