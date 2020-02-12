---
title: Xamarin Android 注意事项（MSAL.NET） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解将 Xamarin Android 与适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）配合使用时的注意事项。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81b55253d757f641979c6f72001803d7d38d9af3
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132502"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>使用 Xamarin Android 和 MSAL.NET 时的注意事项
本文讨论在将 Xamarin Android 与用于 .NET 的 Microsoft 身份验证库（MSAL.NET）一起使用时应考虑的事项。

## <a name="set-the-parent-activity"></a>设置父活动

在 Xamarin Android 上，设置父活动，使令牌在交互后返回。 下面是一个代码示例：

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

在 MSAL 4.2 和更高版本中，还可以在 `PublicClientApplication`级别设置此功能。 为此，请使用回叫：

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

如果使用的是[CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin)，则 `PublicClientApplication` 生成器代码将类似于以下示例。

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>确保控件返回到 MSAL 
当身份验证流的交互式部分结束时，请确保该控件返回到 MSAL。 在 Android 上，覆盖 `Activity`的 `OnActivityResult` 方法。 然后调用 `AuthenticationContinuationHelper` MSAL 类的 `SetAuthenticationContinuationEventArgs` 方法。 

下面是一个示例：

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```

该行确保控件在身份验证流的交互式部分结束时返回到 MSAL。

## <a name="update-the-android-manifest"></a>更新 Android 清单
*Androidmanifest.xml*文件应包含以下值：

<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
```
  <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
     <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
     </intent-filter>
 </activity>
```

将 Azure 门户中注册的包名称替换为 `android:host=` 值。 将 Azure 门户中注册的密钥哈希替换为 `android:path=` 值。 签名哈希*不*应进行 URL 编码。 请确保签名哈希开头出现前导正斜杠（`/`）。

或者，[在代码中创建活动](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics)，而不是手动编辑*androidmanifest.xml*。 若要在代码中创建活动，请首先创建一个包含 `Activity` 属性和 `IntentFilter` 属性的类。 

下面是表示 XML 文件的值的类的示例：

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="xamarinforms-43x-manifest"></a>Xamarin Forms 4.3. X 清单

Xamarin 4.3. x 生成将 `package` 特性设置为在*androidmanifest.xml*中 `com.companyname.{appName}` 的代码。 如果使用 `DataScheme` 作为 `msal{client_id}`，则可能需要更改该值以匹配 `MainActivity.cs` 命名空间的值。

## <a name="use-the-embedded-web-view-optional"></a>使用嵌入的 web 视图（可选）

默认情况下，MSAL.NET 使用系统 web 浏览器。 此浏览器使你可以通过使用 web 应用程序和其他应用来获取单一登录（SSO）。 在某些极少数情况下，你可能希望系统使用嵌入的 web 视图。 

此代码示例演示如何设置嵌入的 web 视图：

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

有关详细信息，请参阅[将 web 浏览器用于 MSAL.NET](msal-net-web-browsers.md)和[Xamarin Android 系统浏览器注意事项](msal-net-system-browser-android-considerations.md)。


## <a name="troubleshoot"></a>故障排除
你可以创建新的 Xamarin. Forms 应用程序并添加对 MSAL.NET NuGet 包的引用。
但是，如果将现有的 Xamarin 窗体应用程序升级到 MSAL.NET preview 1.1.2 或更高版本，则可能会遇到生成问题。

解决生成问题：

- 将现有的 MSAL.NET NuGet 包更新到 MSAL.NET preview 1.1.2 或更高版本。
- 检查 Xamarin 是否自动更新为版本2.5.0.122203。 如有必要，请将 Xamarin 更新到此版本。
- 检查 Xamarin. v4 是否自动更新为版本25.4.0.2。 如有必要，请更新到版本25.4.0.2。
- 确保所有 Xamarin. 支持包的目标版本为25.4.0.2。
- 清理或重新生成应用程序。
- 在 Visual Studio 中，尝试将最大并行项目生成数设置为1。 为此，请选择 "**选项**" > **项目和解决方案** > "**生成和运行"，**  > **最大并行项目生成数**。
- 如果要从命令行生成，并且命令使用 `/m`，请尝试从命令中删除此元素。

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>错误：当前上下文中不存在名称 AuthenticationContinuationHelper

如果错误指示当前上下文中不存在 `AuthenticationContinuationHelper`，Visual Studio 可能会错误地更新了 Android .csproj * 文件。 有时 *\<HintPath >* 文件路径不正确地包含*netstandard13*而不是*monoandroid90*。

此示例包含正确的文件路径：

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[使用 Microsoft 标识平台的 Xamarin 移动应用程序](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)的示例。 下表汇总了自述文件中的相关信息。

| 示例 | 平台 | 说明 |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin、Android、UWP | 一个简单的 Xamarin 应用程序，演示如何使用 MSAL 通过 Azure AD 2.0 端点对 Microsoft 个人帐户和 Azure AD 进行身份验证。 该应用还演示了如何访问 Microsoft Graph 并显示生成的令牌。 <br>![拓扑](media/msal-net-xamarin-android-considerations/topology.png) |
