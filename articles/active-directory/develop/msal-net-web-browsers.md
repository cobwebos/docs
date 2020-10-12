---
title: 使用 Web 浏览器 (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: 了解将 Xamarin Android 与适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 一起使用时的特定注意事项。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: bf51f39a789b91a4cb0b88eb8bb1f2989bec7358
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88165815"
---
# <a name="using-web-browsers-msalnet"></a>使用 Web 浏览器 (MSAL.NET)

交互式身份验证需要使用 Web 浏览器。 默认情况下，MSAL.NET 支持 Xamarin.iOS 和 Xamarin.Android 上的[系统 Web 浏览器](#system-web-browser-on-xamarinios-xamarinandroid)。 但是，也可以根据 [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) 和 [Xamarin.Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) 应用中的要求（UX、单一登录 (SSO) 的需求、安全性），[启用嵌入式 Web 浏览器](#enable-embedded-webviews-on-ios-and-android)。 你甚至还可以根据 Android 中是否有 Chrome 或支持 Chrome 自定义选项卡的浏览器，来[动态选择](#detecting-the-presence-of-custom-tabs-on-xamarinandroid)要使用的 Web 浏览器。 MSAL.NET 仅支持 .NET Core 桌面应用程序中的系统浏览器。

## <a name="web-browsers-in-msalnet"></a>MSAL.NET 中的 Web 浏览器

### <a name="interaction-happens-in-a-web-browser"></a>交互在 Web 浏览器中发生

必须知道，在以交互方式获取令牌时，对话框的内容不是由该库提供，而是由 STS（安全令牌服务）提供。 身份验证终结点发回一些 HTML 和 JavaScript，以控制在 Web 浏览器或 Web 控件中呈现的交互。 让 STS 处理 HTML 交互可以带来诸多好处：

- 应用程序和身份验证库永远不会存储密码（如果用户键入了密码）。
- 允许重定向到其他标识提供者（例如，使用具有 MSAL 的工作学校帐户或个人帐户登录，或使用具有 Azure AD B2C 的社交帐户登录）。
- 允许 STS 控制条件访问，例如，让用户在身份验证阶段进行[多重身份验证 (MFA)](../authentication/concept-mfa-howitworks.md)（在其手机上或手机上的身份验证应用中输入 Windows Hello PIN 或接听电话）。 如果尚未设置所需的多重身份验证，用户可以在同一对话框中及时设置。  用户输入手机号码后，系统会引导他们安装身份验证应用程序并扫描 QR 标记以添加其帐户。 此服务器驱动的交互方式是一种极佳的体验！
- 用户密码过期时，可让用户在同一个对话框中更改密码（分别提供旧密码和新密码的字段）。
- 支持为 Azure AD 租户管理员/应用程序所有者控制的租户或应用程序（映像）设计品牌形象。
- 使用户能够在身份验证之后同意让应用程序以其名义访问资源/范围。

### <a name="embedded-vs-system-web-ui"></a>嵌入式与系统 Web UI

MSAL.NET 是一个多框架库，它具有特定于框架的代码，可在 UI 控件中托管浏览器（例如，在 .NET Classic 中，它使用 WinForms；在 Xamarin 中，它使用本机移动控件，等等）。 此控件称为 `embedded` Web UI。 另外，MSAL.NET 还能够启动系统 OS 浏览器。

一般情况下，我们建议使用平台默认设置（通常是系统浏览器）。 系统浏览器能够更好地记住以前登录的用户。 如果需要更改此行为，请使用 `WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>速览

| Framework        | 嵌入式 | 系统 | 默认 |
| ------------- |-------------| -----| ----- |
| .NET Classic     | 是 | 是^ | 嵌入式 |
| .NET Core     | 否 | 是^ | 系统 |
| .NET Standard | 否 | 是^ | 系统 |
| UWP | 是 | 否 | 嵌入式 |
| Xamarin.Android | 是 | 是  | 系统 |
| Xamarin.iOS | 是 | 是  | 系统 |
| Xamarin.Mac| 是 | 否 | 嵌入式 |

^ 需要“http://localhost”重定向 URI

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Xamarin.iOS 和 Xamarin.Android 上的系统 Web 浏览器

默认情况下，MSAL.NET 支持 Xamarin.iOS、Xamarin.Android 和 .NET Core 上的系统 Web 浏览器。 对于提供 UI 的所有平台（即，不是 .NET Core），该库提供一个对话框来嵌入 Web 浏览器控件。 对于 .NET Desktop 以及 UWP 平台的  WAB，MSAL.NET 还会使用嵌入式 Web 视图。 但是，它默认利用 Xamarin iOS 和 Xamarin Android 应用程序的**系统 Web 浏览器**。 在 iOS 上，它甚至根据操作系统版本（iOS12、iOS11 和更早版本）选择要使用的 Web 视图。

使用系统浏览器可获得的重要优势是，无需使用中介（公司门户/Authenticator）即可与其他应用程序和 Web 应用程序共享 SSO 状态。 默认情况下，适用于 Xamarin iOS 和 Xamarin Android 平台的 MSAL.NET 中使用了系统浏览器，因为在这些平台上，系统 Web 浏览器会占据整个屏幕，因此用户体验更佳。 系统 Web 视图与对话框并无区别。 但在 iOS 上，用户可能需要许可浏览器回调应用程序，这是一个烦人的操作。

## <a name="system-browser-experience-on-net-core"></a>.NET Core 上的系统浏览器体验

在 .NET Core 上，MSAL.NET 将以单独进程的形式启动系统浏览器。 MSAL.NET 无法控制此浏览器，但一旦用户完成身份验证，网页就会重定向，使 MSAL.NET 能够截获 URI。

你也可以将针对 .NET Classic 编写的应用配置为使用此浏览器，方法是指定

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET 无法检测用户是已导航到其他位置，还是只是关闭了浏览器。 建议在使用此技术的应用中定义超时（通过 `CancellationToken`）。 建议至少定义几分钟的超时，以考虑到提示用户更改密码或执行多重身份验证的情况。

### <a name="how-to-use-the-default-os-browser"></a>如何使用默认 OS 浏览器

MSAL.NET 需要侦听 `http://localhost:port`，并截获当用户完成身份验证时由 AAD 发送的代码（有关详细信息，请参阅[授权代码](v2-oauth2-auth-code-flow.md)）

若要启用系统浏览器：

1. 在应用注册期间，将 `http://localhost` 配置为重定向 URI（目前不受 B2C 的支持）
2. 构造 PublicClientApplication 时，请指定此重定向 URI：

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> 如果你配置 `http://localhost`，则 MSAL.NET 将在内部查找一个随机打开的端口并使用该端口。

### <a name="linux-and-mac"></a>Linux 和 MAC

在 Linux 上，MSAL.NET 将使用 xdg-open 工具打开默认 OS 浏览器。 若要进行故障排除，请从终端运行该工具，例如 `xdg-open "https://www.bing.com"`。 在 Mac 上，通过调用 `open <url>` 打开该浏览器。

### <a name="customizing-the-experience"></a>自定义体验

> [!NOTE]
> MSAL.NET 4.1.0 或更高版本中支持自定义。

收到令牌或出错时，MSAL.NET 可使用 HTTP 消息做出响应。 你可以显示 HTML 消息，或重定向到所选的 URL：

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

### <a name="opening-a-specific-browser-experimental"></a>打开特定的浏览器（试验性）

可以自定义 MSAL.NET 打开浏览器的方式。 例如，可以强制打开特定的浏览器，而不使用任何默认浏览器：

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP 不使用系统 Web 视图

但是，对于桌面应用程序，启动系统 Web 视图会导致用户体验欠佳，因为用户会看到浏览器，而他们可能已经打开了其他选项卡。 身份验证发生后，用户会获得一个页面，要求他们关闭此窗口。 用户可能会一不小心关闭的整个进程（包括与身份验证不相关的其他标签页）。 如果使用桌面上的系统浏览器，还需要打开本地端口并进行侦听，这可能需要对应用程序具有高级权限。 身为开发人员、用户或管理员的你可能不愿意遵守此要求。

## <a name="enable-embedded-webviews-on-ios-and-android"></a>在 iOS 和 Android 上启用嵌入式 Web 视图

你还可以在 Xamarin.iOS 和 Xamarin.Android 应用中启用嵌入式 Web 视图。 从 MSAL.NET 2.0.0-preview 开始，MSAL.NET 也支持使用**嵌入式** Web 视图选项。 对于 ADAL.NET 而言，嵌入式 Web 视图是唯一支持的选项。

开发人员在使用面向 Xamarin 的 MSAL.NET 时，可以选择使用嵌入式 Web 视图或系统浏览器。 你可以根据要实现的用户体验和要解决的安全问题来进行选择。

目前，MSAL.NET 尚不支持 Android 和 iOS 代理。 因此，如果需要提供单一登录 (SSO)，系统浏览器可能仍是一个更好的选择。 在嵌入式 Web 浏览器中支持代理属于 MSAL.NET 积压工作。

### <a name="differences-between-embedded-webview-and-system-browser"></a>嵌入式 Web 视图与系统浏览器之间的差异
MSAL.NET 中嵌入式 Web 视图与系统浏览器之间一些视觉差异。

**使用嵌入式 Web 视图在 MSAL.NET 中进行交互式登录：**

![嵌入式](media/msal-net-web-browsers/embedded-webview.png)

**使用系统浏览器在 MSAL.NET 中进行交互式登录：**

![系统浏览器](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>开发人员选项

开发人员在使用 MSAL.NET 时，可以通过多个选项来显示 STS 的交互式对话框：

- **系统浏览器。** 系统浏览器默认已在库中设置。 如果使用的是 Android，请阅读[系统浏览器](msal-net-system-browser-android-considerations.md)了解有关支持用于身份验证的浏览器的具体信息。 在 Android 中使用系统浏览器时，我们建议在设备中安装支持 Chrome 自定义标签页的浏览器。  否则，身份验证可能失败。
- **嵌入式 Web 视图。** 若要在 MSAL.NET 中仅使用嵌入式 Web 视图，请在 `AcquireTokenInteractively` 参数生成器中包含 `WithUseEmbeddedWebView()` 方法。

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

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>在 Xamarin.iOS 上的嵌入式 Web 浏览器与系统浏览器之间进行选择

在 iOS 应用中，可将 `AppDelegate.cs` 中的 `ParentWindow` 初始化为 `null`。 iOS 不使用该参数

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>在 Xamarin.Android 上的嵌入式 Web 浏览器或系统浏览器之间进行选择

在 Android 应用中，可在 `MainActivity.cs` 中设置父活动，以便将身份验证结果返回到其中：

```csharp
 App.ParentWindow = this;
```

然后在 `MainPage.xaml.cs` 中：

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>在 Xamarin 上检测是否存在自定义标签页

若要使用系统 Web 浏览器对浏览器中运行应用启用 SSO，但又担心 Android 设备的用户体验中不提供支持自定义标签页的浏览器，可以选择通过调用 `IPublicClientApplication` 中的 `IsSystemWebViewAvailable()` 方法来做出决定。 如果 PackageManager 检测到自定义标签页，此方法将返回 `true`；如果在设备上未检测到自定义标签页，则返回 `false`。

可以根据此方法返回的值以及自己的要求做出决定：

- 可向用户返回自定义错误消息。 例如：“请安装 Chrome 以继续进行身份验证”-或-
- 可以回退到嵌入式 Web 视图选项，并以嵌入式 Web 视图的形式启动 UI。

以下代码演示了嵌入式 Web 视图选项：

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core 不支持使用嵌入式浏览器进行交互式身份验证

对于 .NET Core，只能通过系统 Web 浏览器（而不能通过嵌入式 Web 视图）获取令牌。 实际上，.NET Core 目前并不提供 UI。
若要使用系统 Web 浏览器自定义浏览体验，可以实现 [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) 接口，甚至可以提供自己的浏览器。
