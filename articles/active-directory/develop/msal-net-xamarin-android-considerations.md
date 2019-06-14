---
title: Xamarin Android 注意事项 （适用于.NET 的 Microsoft 身份验证库） |Azure
description: 使用 Xamarin Android Microsoft 身份验证库和.NET (MSAL.NET) 时，了解有关特定注意事项。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb0cfb06e95cadbb549f669e5d59bdb0d795c896
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545859"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>使用 MSAL.NET 特定于 Xamarin Android 的注意事项
使用 Xamarin Android Microsoft 身份验证库和.NET (MSAL.NET) 时，本文将介绍特定注意事项。

本文适用于 MSAL.NET 3.x。 如果您有兴趣 MSAL.NET 2.x，请参阅[Xamarin Android MSAL.NET 的具体信息 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics-2x)。

## <a name="set-the-parent-activity"></a>设置父活动

在 Xamarin.Android 中，您需要设置父活动，以便在令牌获取返回后发生的交互。

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>确保控件都可以回到 MSAL 一次身份验证流结束的交互部分
在 Android 上，您需要重写`OnActivityResult`方法的`Activity`调用 SetAuthenticationContinuationEventArgs AuthenticationContinuationHelper MSAL 类的方法。

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
该行可确保，在控件返回到曾经 MSAL 的身份验证流的交互部分结束后。

## <a name="update-the-android-manifest"></a>更新 Android 清单
`AndroidManifest.xml`应包含以下值：
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

## <a name="use-the-embedded-web-view-optional"></a>使用嵌入式的 web 视图 （可选）

默认情况下 MSAL.NET 使用系统 web 浏览器，使你能够获取与 Web 应用程序的 SSO 和其他应用。 在某些极少数情况下，你可能想要指定你想要使用嵌入式的 web 视图。 有关详细信息，请参阅[MSAL.NET 使用 Web 浏览器](msal-net-web-browsers.md)并[Android 系统浏览器](msal-net-system-browser-android-considerations.md)。

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>故障排除
如果您创建新的 Xamarin.Forms 应用程序，并添加对 MSAL.Net NuGet 包的引用，这将会正常工作。
但是，如果你想要升级现有的 Xamarin.Forms 应用程序到 MSAL.NET 预览 1.1.2 或更高版本，你可能会遇到生成问题。

若要解决这些问题，您应该：
- 现有 MSAL.NET NuGet 包更新到 MSAL.NET 预览 1.1.2 或更高版本
- 检查 Xamarin.Forms 自动更新为版本 2.5.0.122203 （否则，更新到此版本）
- 检查 Xamarin.Android.Support.v4 自动更新为版本 25.4.0.2 （否则，更新到此版本）
- 所有 Xamarin.Android.Support 包应都面向版本 25.4.0.2
- 清除/重新生成
- 请尝试设置最大并行项目生成为 Visual Studio 中的 1 （选项-> 项目和解决方案-> 生成和运行-> 最大并行项目生成数）
- 或者，如果要从命令行生成，如果尝试删除 /m 在命令中使用它。


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>错误：当前上下文中不存在 AuthenticationContinuationHelper 名称

这可能是因为 Visual Studio 未正确更新 Android.csproj* 文件。 有时 **<HintPath>** filepath 错误地包含而不是 netstandard13 **monoandroid90**。

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>后续步骤

中提供了更多详细信息和示例[Android 特定注意事项](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)段落的以下示例的 readme.md 文件：

| 示例 | 平台 | 描述 |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | 简单的 Xamarin Forms 应用，它展示了如何使用 MSAL MSA 和 AADD v2.0 终结点，通过 Azure AD 进行身份验证和访问 Microsoft Graph 使用生成的令牌。 <br>![拓扑](media/msal-net-xamarin-android-considerations/topology.png) |