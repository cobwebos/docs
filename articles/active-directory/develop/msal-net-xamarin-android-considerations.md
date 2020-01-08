---
title: Xamarin Android 注意事项（MSAL.NET） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解将 Xamarin Android 与适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）一起使用时的特定注意事项。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 678b581e09fe1eac49e4f2bf07eabbbc944c8d4e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424151"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>适用于 MSAL.NET 的 Xamarin 特定于 Android 的注意事项
本文介绍将 Xamarin Android 与适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）一起使用时的特定注意事项。

## <a name="set-the-parent-activity"></a>设置父活动

在 Xamarin 上，你需要设置父活动，以便在发生交互时返回令牌。

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```
还可以通过回调在 PublicClientApplication 级别（在 MSAL 4.2 + 中）设置此项。

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

建议在[此处](https://github.com/jamesmontemagno/CurrentActivityPlugin)使用 CurrentActivityPlugin。  然后，PublicClientApplication 生成器代码将如下所示：

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```


## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>当身份验证流的交互式部分结束后，确保控件返回到 MSAL
在 Android 上，需要重写 `Activity` 的 `OnActivityResult` 方法，并调用 AuthenticationContinuationHelper MSAL 类的 SetAuthenticationContinuationEventArgs 方法。

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
该行可确保在身份验证流的交互式部分结束后，控件返回到 MSAL。

## <a name="update-the-android-manifest"></a>更新 Android 清单
`AndroidManifest.xml` 应包含以下值：
```csharp
<activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="msal{client_id}" android:host="auth" />
         </intent-filter>
</activity>
```

或者，你可以[在代码中创建活动](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics)，而不是手动编辑 `AndroidManifest.xml`。 为此，你必须创建一个具有 `Activity` 和 `IntentFilter` 属性的类。 表示上述 xml 相同值的类应为：

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

### <a name="xamarinforms-43x-manifest"></a>XamarinForms 4.3. X 清单

XamarinForms 4.3 生成的代码将 `package` 特性设置为 `AndroidManifest.xml`中 `com.companyname.{appName}`。 如果你使用 `DataScheme` 作为 `msal{client_id}`，则你可能需要将此值更改为与 `MainActivity.cs` 命名空间相同。

## <a name="use-the-embedded-web-view-optional"></a>使用嵌入的 web 视图（可选）

默认情况下，MSAL.NET 使用系统 web 浏览器，可让你获取 Web 应用程序和其他应用的 SSO。 在某些极少数情况下，你可能想要指定想要使用嵌入的 web 视图。 有关详细信息，请参阅[MSAL.NET 使用 Web 浏览器](msal-net-web-browsers.md)和[Android 系统浏览器](msal-net-system-browser-android-considerations.md)。

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>故障排除
如果创建新的 Xamarin. Forms 应用程序并添加对 MSAL.Net NuGet 包的引用，则此操作将起作用。
但是，如果要将现有 Xamarin. Forms 应用程序升级到 MSAL.NET preview 1.1.2 或更高版本，则可能会遇到生成问题。

若要解决这些问题，应执行以下操作：
- 将现有的 MSAL.NET NuGet 包更新到 MSAL.NET preview 1.1.2 或更高版本
- 检查 Xamarin 是否自动更新为版本2.5.0.122203 （如果不是，则更新到此版本）
- 检查 Xamarin. v4 是否自动更新为版本25.4.0.2 （如果不是，则更新到此版本）
- 所有 Xamarin. 支持包都应针对版本25.4.0。2
- 清除/重新生成
- 尝试在 Visual Studio 中将最大并行项目版本设置为1（选项-> 项目和解决方案-> 生成和运行 > 最大并行项目生成数）
- 或者，如果要从命令行生成，请尝试从命令中删除/m （如果使用）。


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>错误：当前上下文中不存在名称 "AuthenticationContinuationHelper"

这可能是因为 Visual Studio 未能正确更新 Android * 文件。 有时 **\<HintPath >** filepath 不正确地包含 netstandard13 而不是**monoandroid90**。

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>后续步骤

以下示例的 readme.md 文件的[Android 特定注意事项](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)段落中提供了更多详细信息和示例：

| 示例 | 平台 | Description |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | 一个简单的 Xamarin Forms 应用，展示如何通过 AADD v2.0 终结点使用 MSAL 对 MSA 和 Azure AD 进行身份验证，并使用生成的令牌访问 Microsoft Graph。 <br>![拓扑](media/msal-net-xamarin-android-considerations/topology.png) |
