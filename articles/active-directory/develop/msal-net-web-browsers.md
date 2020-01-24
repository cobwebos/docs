---
title: 使用 web 浏览器（MSAL.NET） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解将 Xamarin Android 与适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）一起使用时的特定注意事项。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: b0b0ac05e54619af0b1dd4a68918a89681f845bd
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695069"
---
# <a name="using-web-browsers-msalnet"></a>使用 web 浏览器（MSAL.NET）

Web 浏览器是交互式身份验证所必需的。 默认情况下，MSAL.NET 在 Xamarin 和 Xamarin 上支持[系统 web 浏览器](#system-web-browser-on-xamarinios-xamarinandroid)。 不过，你也可以根据你的要求（UX，在[xamarin](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios)和[xamarin Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid)应用中使用单一登录（SSO）、安全性）来[启用嵌入的 Web 浏览器](#enable-embedded-webviews-on-ios-and-android)。 甚至还可以根据 Chrome 的存在或支持 Android 中 Chrome 自定义选项卡的浏览器，[动态地选择](#detecting-the-presence-of-custom-tabs-on-xamarinandroid)要使用的 web 浏览器。 MSAL.NET 仅支持 .NET Core 桌面应用程序中的系统浏览器。

## <a name="web-browsers-in-msalnet"></a>MSAL.NET 中的 Web 浏览器

### <a name="interaction-happens-in-a-web-browser"></a>Web 浏览器中发生交互

必须了解的是，以交互方式获取令牌时，该对话框的内容不是由库提供，而是由 STS （安全令牌服务）提供的。 身份验证终结点发送回一些 HTML 和 JavaScript，它们控制在 web 浏览器或 web 控件中呈现的交互。 允许 STS 处理 HTML 交互具有很多优点：

- 应用程序和身份验证库不存储密码（如果已键入）。
- 允许重定向到其他标识提供者（例如，使用工作学校帐户登录，或使用 MSAL 或使用带有 Azure AD B2C 的社交帐户登录）。
- 允许 STS 控制条件性访问，例如，让用户在身份验证阶段执行多因素身份验证（MFA）（输入 Windows Hello pin，或在电话上调用，或在电话上的身份验证应用上调用）。 如果尚未对所需的多重身份验证进行设置，则用户可以在同一对话框中将其设置为实时。  用户输入其移动电话号码，并指导安装身份验证应用程序并扫描 QR 标记以添加其帐户。 此服务器驱动的交互是一个不错的体验！
- 允许用户在密码过期时（为旧密码和新密码提供其他字段）在同一个对话框中更改其密码。
- 启用租户的品牌或由 Azure AD 租户管理员/应用程序所有者控制的应用程序（映像）。
- 允许用户同意允许应用程序访问其名称中的资源/作用域。

### <a name="embedded-vs-system-web-ui"></a>嵌入的 vs 系统 Web UI

MSAL.NET 是一个多框架库，它具有框架特定的代码，用于在 UI 控件中承载浏览器（例如，在 .Net 经典上，它使用 WinForms，在 Xamarin 上使用本机移动控件等）。 此控件 `embedded` web UI 中调用。 另外，MSAL.NET 还能够启动系统操作系统浏览器。

通常，建议使用平台默认设置，这通常是系统浏览器。 系统浏览器可以更好地记住以前登录的用户。 如果需要更改此行为，请使用 `WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>概览

| 框架        | 嵌入 | 系统 | 默认 |
| ------------- |-------------| -----| ----- |
| .NET 经典     | 是 | 是 ^ | 嵌入 |
| .NET Core     | 否 | 是 ^ | 系统 |
| .NET Standard | 否 | 是 ^ | 系统 |
| UWP | 是 | 否 | 嵌入 |
| Xamarin.Android | 是 | 是  | 系统 |
| Xamarin.iOS | 是 | 是  | 系统 |
| Xamarin.Mac| 是 | 否 | 嵌入 |

^ 需要 "http://localhost" 重定向 URI

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Xamarin、Xamarin 上的系统 web 浏览器

默认情况下，MSAL.NET 支持 Xamarin、Xamarin 和 .NET Core 上的系统 web 浏览器。 对于提供 UI （即，不是 .NET Core）的所有平台，由库嵌入 Web 浏览器控件提供一个对话框。 对于 UWP 平台，MSAL.NET 还使用适用于 .NET Desktop 和 WAB 的嵌入式 web 视图。 但是，它默认利用适用于 Xamarin iOS 和 Xamarin Android 应用程序的**系统 web 浏览器**。 在 iOS 上，它甚至会根据操作系统版本（iOS12、iOS11 和更早版本）选择要使用的 web 视图。

使用系统浏览器具有很大的优势，即与其他应用程序和 web 应用程序共享 SSO 状态，无需代理（公司门户/验证器）。 默认情况下，系统浏览器在 MSAL.NET 中为 Xamarin iOS 和 Xamarin Android 平台使用系统浏览器，因为在这些平台上，系统 web 浏览器占据整个屏幕，用户体验更好。 系统 web 视图与对话框并无区别。 但在 iOS 上，用户可能必须同意浏览器回调应用程序，这可能会令人讨厌。

## <a name="system-browser-experience-on-net-core"></a>.NET Core 上的系统浏览器体验

在 .NET Core 上，MSAL.NET 会将系统浏览器作为单独的进程启动。 MSAL.NET 无法控制此浏览器，但一旦用户完成身份验证后，网页将被重定向，使 MSAL.NET 能够截获 Uri。

你还可以将为 .NET 经典编写的应用配置为使用此浏览器，方法是指定

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET 无法检测用户是否离开浏览器或只是关闭浏览器。 鼓励使用此技术的应用定义超时（通过 `CancellationToken`）。 建议至少花费几分钟的时间，以便在系统提示用户更改密码或执行多重身份验证时考虑这种情况。

### <a name="how-to-use-the-default-os-browser"></a>如何使用默认 OS 浏览器

MSAL.NET 需要侦听 `http://localhost:port` 并截获在用户完成身份验证后 AAD 发送的代码（有关详细信息，请参阅[授权代码](v2-oauth2-auth-code-flow.md)）

若要启用系统浏览器：

1. 在应用程序注册期间，将 `http://localhost` 配置为重定向 uri （目前不受 B2C 支持）
2. 构造 PublicClientApplication 时，请指定此重定向 uri：

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> 如果配置 `http://localhost`，则内部 MSAL.NET 将查找随机打开端口并使用它。

### <a name="linux-and-mac"></a>Linux 和 MAC

在 Linux 上，MSAL.NET 将使用 xdg 工具打开默认 OS 浏览器。 若要进行故障排除，请从终端运行该工具，例如 `xdg-open "https://www.bing.com"`  
在 Mac 上，浏览器通过调用来打开 `open <url>`

### <a name="customizing-the-experience"></a>自定义体验

> [!NOTE]
> 自定义在 MSAL.NET 4.1.0 或更高版本中可用。

当收到令牌或发生错误时，MSAL.NET 能够使用 HTTP 消息进行响应。 可以显示 HTML 邮件或重定向到所选的 url：

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>打开特定浏览器（实验性）

您可以自定义 MSAL.NET 打开浏览器的方式。 例如，你可以强制打开特定的浏览器，而不使用任何默认浏览器：

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP 不使用系统 Web 视图

但对于桌面应用程序，启动系统 Web 视图会导致 subpar 的用户体验，因为用户会看到浏览器，而他们可能已打开其他选项卡。 身份验证发生后，用户会获得一个页面，要求他们关闭此窗口。 如果用户不需要注意，则可以关闭整个过程（包括与身份验证无关的其他选项卡）。 在桌面上利用系统浏览器还需要打开本地端口并进行侦听，这可能需要对应用程序的高级权限。 作为开发人员、用户或管理员，可能不会对此要求有任何疑问。

## <a name="enable-embedded-webviews-on-ios-and-android"></a>在 iOS 和 Android 上启用 embedded webview

你还可以在 Xamarin 和 Xamarin Android 应用中启用嵌入的 webview。 从 MSAL.NET 2.0.0-preview 开始，MSAL.NET 还支持使用**嵌入**的 web 视图选项。 对于 ADAL.NET，嵌入式 web 视图是唯一受支持的选项。

作为使用 MSAL.NET 面向 Xamarin 的开发人员，你可以选择使用嵌入的 webview 或系统浏览器。 这是你选择的，具体取决于你想要针对的用户体验和安全问题。

目前，MSAL.NET 尚不支持 Android 和 iOS 代理。 因此，如果需要提供单一登录（SSO），系统浏览器可能仍是更好的选择。 在 MSAL.NET 积压工作（backlog）上，通过嵌入式 web 浏览器支持代理。

### <a name="differences-between-embedded-webview-and-system-browser"></a>嵌入的 web 视图与系统浏览器之间的差异
MSAL.NET 中嵌入的 web 视图与系统浏览器之间有一些视觉差别。

**使用嵌入的 Web 视图进行 MSAL.NET 交互登录：**

![嵌入](media/msal-net-web-browsers/embedded-webview.png)

**使用系统浏览器通过 MSAL.NET 进行交互式登录：**

![系统浏览器](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>开发人员选项

作为使用 MSAL.NET 的开发人员，可以使用多个选项显示 STS 中的交互式对话框：

- **系统浏览器。** 默认情况下，系统浏览器是在库中设置的。 如果使用 Android，请阅读[系统浏览](msal-net-system-browser-android-considerations.md)器，了解有关支持身份验证的浏览器的具体信息。 在 Android 中使用系统浏览器时，建议设备具有支持 Chrome 自定义选项卡的浏览器。  否则，身份验证可能会失败。
- **嵌入的 web 视图。** 若要仅在 MSAL.NET 中使用嵌入的 web 视图，`AcquireTokenInteractively` 参数生成器包含 `WithUseEmbeddedWebView()` 方法。

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android：

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>在 Xamarin 上的嵌入式 web 浏览器或系统浏览器之间进行选择

在 iOS 应用中的 `AppDelegate.cs` 可以将 `ParentWindow` 初始化为 `null`。 IOS 中不使用它

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>在 Xamarin 上的嵌入式 web 浏览器或系统浏览器之间进行选择

在 Android 应用中 `MainActivity.cs` 可以设置父活动，使身份验证结果返回到该活动：

```csharp
 App.ParentWindow = this;
```

然后在 `MainPage.xaml.cs`中：

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>检测 Xamarin 上的自定义选项卡

如果你想要使用系统 web 浏览器在浏览器中运行应用程序，但担心 Android 设备的用户体验没有自定义选项卡支持，你可以选择在 `IPublicClientApplication`中调用 `IsSystemWebViewAvailable()` 方法。 如果 PackageManager 检测到自定义选项卡，并且在设备上未检测到它们 `false`，则此方法将返回 `true`。

根据此方法返回的值以及您的要求，您可以做出决定：

- 您可以向用户返回自定义错误消息。 例如： "请安装 Chrome 以继续进行身份验证"-或-
- 可以回退到嵌入的 web 视图选项，并以嵌入的 web 视图的形式启动 UI。

下面的代码显示了 embedded web 视图选项：

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core 不支持通过嵌入式浏览器进行交互式身份验证

对于 .NET Core，只能通过系统 web 浏览器（而不是使用嵌入的 web 视图）获得令牌的获取。 实际上，.NET Core 不提供 UI。
若要通过系统 web 浏览器自定义浏览体验，可以实现[IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui)接口，甚至提供自己的浏览器。
