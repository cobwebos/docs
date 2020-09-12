---
title: Xamarin Android 代码配置和故障排除 (MSAL.NET) |Microsoft
titleSuffix: Microsoft identity platform
description: 了解将 Xamarin Android 与适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 配合使用时的注意事项。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 320d48535c4792a4d610888c6a7030568ccf16bc
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89459838"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>Xamarin Android with MSAL.NET 的配置要求和故障排除提示

将 Xamarin Android 与适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 时，需要在代码中进行几项配置更改。 以下各节介绍了所需的修改，并提供了 [疑难解答](#troubleshooting) 部分来帮助你避免一些最常见的问题。

## <a name="set-the-parent-activity"></a>设置父活动

在 Xamarin Android 上，设置父活动，使令牌在交互后返回：

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

在 MSAL.NET 4.2 和更高版本中，还可以在 [PublicClientApplication][PublicClientApplication]级别设置此功能。 为此，请使用一个回调：

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

如果使用的是 [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin)， [`PublicClientApplication`][PublicClientApplication] 生成器代码应类似于以下代码片段：

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>确保控制权返回到 MSAL

当身份验证流的交互式部分结束时，通过重写将控制返回到 MSAL [`Activity`][Activity] 。[`OnActivityResult()`][OnActivityResult] 方法。

在替代中，调用 MSAL。网络 `AuthenticationContinuationHelper` 。`SetAuthenticationContinuationEventArgs()` 用于在身份验证流的交互式部分末尾将控制返回到 MSAL 的方法。

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest"></a>更新 Android 清单

*AndroidManifest.xml* 文件应包含以下值：

```XML
  <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
  <activity
        android:name="microsoft.identity.client.BrowserTabActivity">
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

将 `android:host=` 值替换为在 Azure 门户中注册的包名称。 将 `android:path=` 值替换为在 Azure 门户中注册的密钥哈希。 不应该对签名哈希进行 URL 编码。  确保前导正斜杠 (`/`) 出现在签名哈希的开头。

或者，[在代码中创建活动](/xamarin/android/platform/android-manifest#the-basics)，而不要手动编辑 *AndroidManifest.xml*。 若要在代码中创建活动，请先创建一个包含 `Activity` 属性和 `IntentFilter` 属性的类。

下面是表示 XML 文件值的类的示例：

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

### <a name="xamarinforms-43x-manifest"></a>Xamarin Forms 4.3. x 清单

Xamarin.Forms 4.3.x 会生成可在 *AndroidManifest.xml* 中将 `package` 属性设置为 `com.companyname.{appName}` 的代码。 如果使用 `DataScheme` 作为 `msal{client_id}`，则可能需要更改该值，使之与 `MainActivity.cs` 命名空间的值匹配。

## <a name="use-the-embedded-web-view-optional"></a>使用嵌入式 Web 视图（可选）

默认情况下，MSAL.NET 使用系统 Web 浏览器。 在此浏览器中，可以使用 Web 应用程序和其他应用来实现单一登录 (SSO)。 在罕见的情况下，你可能希望系统使用嵌入式 Web 视图。

此代码示例演示如何设置嵌入式 Web 视图：

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

有关详细信息，请参阅[使用适用于 MSAL.NET 的 Web 浏览器](msal-net-web-browsers.md)和 [Xamarin Android 系统浏览器注意事项](msal-net-system-browser-android-considerations.md)。

## <a name="troubleshooting"></a>疑难解答

### <a name="general-tips"></a>一般技巧

- 将现有的 MSAL.NET NuGet 包更新到 [最新版本的 MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/)。
- 验证 Xamarin 是否处于最新版本。
- 验证 Xamarin. v4 是否处于最新版本。
- 确保所有 Xamarin. 支持包都以最新版本为目标。
- 清理或重新生成应用程序。
- 在 Visual Studio 中，尝试将最大并行项目生成数设置为 **1**。 为此，请选择“选项” > “项目和解决方案” > “生成和运行” > “最大并行项目生成数”。    
- 如果通过命令行生成并且命令使用 `/m`，请尝试从命令中删除此元素。

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>错误：名称 AuthenticationContinuationHelper 不存在于当前上下文中

如果错误指示 `AuthenticationContinuationHelper` 当前上下文中不存在，则 Visual Studio 可能会错误地更新了*Android .csproj \* *文件。 有时，元素中的文件路径 `<HintPath>` 不正确地包含 `netstandard13` 而不是 `monoandroid90` 。

此示例包含正确的文件路径：

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[使用 Microsoft 标识平台的 Xamarin 移动应用程序](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)示例。 下表汇总了自述文件中的相关信息。

| 示例 | 平台 | 说明 |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS、Android、UWP | 一个简单的 Xamarin 应用程序，演示如何使用 MSAL 通过 Azure AD 2.0 端点对 Microsoft 个人帐户和 Azure AD 进行身份验证。 该应用还展示了如何访问 Microsoft Graph 并显示了生成的令牌。 <br>![身份验证流示意图](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
