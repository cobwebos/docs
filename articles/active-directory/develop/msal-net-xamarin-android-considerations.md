---
title: Xamarin Android 注意事项 (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: 了解有关将 Xamarin Android 与 Microsoft 身份验证库配合 .NET （MSAL.NET） 的注意事项。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132502"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>使用 Xamarin Android 与MSAL.NET的注意事项
本文讨论了在将 Xamarin Android 与 Microsoft 身份验证库结合为 .NET （MSAL.NET） 时应考虑的事项。

## <a name="set-the-parent-activity"></a>设置父活动

在 Xamarin Android 上，设置父活动，以便令牌在交互后返回。 下面是代码示例：

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

在 MSAL 4.2 及更高版本中，您还可以将此功能设置为`PublicClientApplication`的级别。 为此，请使用回调：

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

如果使用[CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin)，则`PublicClientApplication`生成器代码如下所示。

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>确保控件返回到 MSAL 
当身份验证流的交互式部分结束时，请确保控件返回 MSAL。 在 Android 上`OnActivityResult`，重写`Activity`方法。 然后调用`SetAuthenticationContinuationEventArgs``AuthenticationContinuationHelper`MSAL 类的方法。 

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

此行可确保控件在身份验证流的交互部分结束时返回到 MSAL。

## <a name="update-the-android-manifest"></a>更新 Android 清单
*AndroidManifest.xml*文件应包含以下值：

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

替换您在 Azure 门户中注册的包名称的值`android:host=`。 替换在 Azure 门户中注册的键哈希值`android:path=`。 签名哈希*不应*对 URL 进行编码。 确保前斜杠 （`/`） 出现在签名哈希的开头。

或者，[在代码中创建活动，](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics)而不是手动编辑*AndroidManifest.xml*。 要在代码中创建活动，请先创建包含`Activity`属性和属性的`IntentFilter`类。 

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

### <a name="xamarinforms-43x-manifest"></a>Xamarin.表格 4.3.X 清单

Xamarin.forms 4.3.x 生成代码，`package`将属性`com.companyname.{appName}`设置在*AndroidManifest.xml*中。 如果使用 为`DataScheme``msal{client_id}`，则可能需要更改该值以匹配`MainActivity.cs`命名空间的值。

## <a name="use-the-embedded-web-view-optional"></a>使用嵌入式 Web 视图（可选）

默认情况下，MSAL.NET使用系统 Web 浏览器。 此浏览器使您能够使用 Web 应用程序和其他应用程序获得单一登录 （SSO）。 在某些极少数情况下，您可能希望系统使用嵌入式 Web 视图。 

此代码示例演示如何设置嵌入式 Web 视图：

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

有关详细信息，请参阅使用[web 浏览器MSAL.NET](msal-net-web-browsers.md)和[Xamarin Android 系统浏览器注意事项](msal-net-system-browser-android-considerations.md)。


## <a name="troubleshoot"></a>疑难解答
您可以创建新的 Xamarin.Forms 应用程序，并添加对 nuGet 包MSAL.NET的引用。
但是，如果将现有的 Xamarin.Forms 应用程序升级到预览版 1.1.2 或更高版本MSAL.NET，则可能存在生成问题。

要解决生成问题：：

- 将现有MSAL.NET NuGet 包更新为预览 1.1.2 或更高版本MSAL.NET。
- 检查 Xamarin.窗体自动更新到版本 2.5.0.122203。 如有必要，将 Xamarin.Forms 更新为此版本。
- 检查 Xamarin.Android.Support.v4 自动更新到版本 25.4.0.2。 如有必要，请更新至版本 25.4.0.2。
- 确保所有 Xamarin.Android.支持包目标版本 25.4.0.2。
- 清理或重建应用程序。
- 在 Visual Studio 中，尝试将并行项目生成的最大数量设置为 1。 为此，请选择**选项** > **项目和解决方案** > **生成和运行** > **最大数量的并行项目生成**。
- 如果要从命令行生成，并且命令使用`/m`，请尝试从 命令中删除此元素。

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>错误：名称身份验证延续帮助程序在当前上下文中不存在

如果错误指示当前上下文中`AuthenticationContinuationHelper`不存在，Visual Studio 可能错误地更新了 Android.csproj® 文件。 有时*\<HintPath>* 文件路径错误地包含*netstandard13*而不是*monoandroid90*。

此示例包含正确的文件路径：

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[使用 Microsoft 标识平台的 Xamarin 移动应用程序](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)的示例。 下表总结了 README 文件中的相关信息。

| 示例 | Platform | 描述 |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS， 安卓， UWP | 一个简单的 Xamarin.Forms 应用，演示如何使用 MSAL 通过 Azure AD 2.0 终结点对 Microsoft 个人帐户和 Azure AD 进行身份验证。 该应用程序还显示如何访问 Microsoft 图形并显示生成的令牌。 <br>![拓扑](media/msal-net-xamarin-android-considerations/topology.png) |
