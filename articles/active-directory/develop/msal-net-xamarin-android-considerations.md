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
ms.openlocfilehash: 256bee26ec6aef464d9ee838ffca4d8c1ecbbb4e
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047780"
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

*AndroidManifest.xml*文件应包含以下值：

```XML
  <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
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

用在 Azure 门户中注册的包名称替换 `android:host=` 该值。 用在 Azure 门户中注册的密钥哈希替换 `android:path=` 该值。 签名哈希 *不* 应进行 URL 编码。 请确保 `/` 签名哈希开头出现 () 前导正斜杠。

或者， [在代码中创建活动](/xamarin/android/platform/android-manifest#the-basics) ，而不是手动编辑 *AndroidManifest.xml*。 若要在代码中创建活动，请首先创建一个包含 `Activity` 特性和特性的类 `IntentFilter` 。

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

### <a name="xamarinforms-43x-manifest"></a>Xamarin Forms 4.3. x 清单

Xamarin 4.3. x 生成 `package` `com.companyname.{appName}` 在 *AndroidManifest.xml*中将属性设置为的代码。 如果使用 `DataScheme` as `msal{client_id}` ，则可能需要更改值以匹配 `MainActivity.cs` 命名空间的值。

## <a name="use-the-embedded-web-view-optional"></a>使用嵌入式 Web 视图（可选）

默认情况下，MSAL.NET 使用系统 web 浏览器。 此浏览器使你可以通过使用 web 应用程序和其他应用来 (SSO) 进行单一登录。 在某些极少数情况下，你可能希望系统使用嵌入的 web 视图。

此代码示例演示如何设置嵌入的 web 视图：

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

有关详细信息，请参阅 [将 web 浏览器用于 MSAL.NET](msal-net-web-browsers.md) 和 [Xamarin Android 系统浏览器注意事项](msal-net-system-browser-android-considerations.md)。

## <a name="troubleshooting"></a>故障排除

### <a name="general-tips"></a>一般技巧

- 将现有的 MSAL.NET NuGet 包更新到 [最新版本的 MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/)。
- 验证 Xamarin 是否处于最新版本。
- 验证 Xamarin. v4 是否处于最新版本。
- 确保所有 Xamarin. 支持包都以最新版本为目标。
- 清理或重新生成应用程序。
- 在 Visual Studio 中，尝试将最大并行项目生成数设置为 **1**。 为此，**请选择 "**  >  **项目和解决方案**" "  >  **生成和运行**  >  **最大并行项目生成数**"。
- 如果要从命令行生成，并且命令使用，请 `/m` 尝试从命令中删除此元素。

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>错误：当前上下文中不存在名称 AuthenticationContinuationHelper

如果错误指示 `AuthenticationContinuationHelper` 当前上下文中不存在，则 Visual Studio 可能会错误地更新了*Android .csproj \* *文件。 有时，元素中的文件路径 `<HintPath>` 不正确地包含 `netstandard13` 而不是 `monoandroid90` 。

此示例包含正确的文件路径：

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [使用 Microsoft 标识平台的 Xamarin 移动应用程序](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)的示例。 下表汇总了自述文件中的相关信息。

| 示例 | 平台 | 说明 |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin、Android、UWP | 一个简单的 Xamarin 应用程序，演示如何使用 MSAL 通过 Azure AD 2.0 端点对 Microsoft 个人帐户和 Azure AD 进行身份验证。 该应用还演示了如何访问 Microsoft Graph 并显示生成的令牌。 <br>![身份验证流示意图](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
