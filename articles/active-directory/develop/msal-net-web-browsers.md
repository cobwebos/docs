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
ms.custom: aaddev
ms.openlocfilehash: 4e62536b610595c7a53eb8333f06f147e628dec7
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772040"
---
# <a name="using-web-browsers-msalnet"></a>使用 Web 浏览器 (MSAL.NET)

交互式身份验证需要使用 Web 浏览器。 默认情况下，MSAL.NET 支持 Xamarin.iOS 和 Xamarin.Android 上的[系统 Web 浏览器](#system-web-browser-on-xamarinios-xamarinandroid)。 不过，你也可以根据自己的需求（UX、需要使用单一登录 (SSO)、安全性），在 [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) 和 [Xamarin.Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) 应用中[启用嵌入式 Web 浏览器](#enable-embedded-webviews-on-ios-and-android)。 你甚至还可以根据 Android 中是否有 Chrome 或支持 Chrome 自定义选项卡的浏览器，来[动态选择](#detecting-the-presence-of-custom-tabs-on-xamarinandroid)要使用的 Web 浏览器。 在 .NET Core 桌面应用程序中，MSAL.NET 仅支持系统浏览器。

## <a name="web-browsers-in-msalnet"></a>MSAL.NET 中的 Web 浏览器

### <a name="interaction-happens-in-a-web-browser"></a>在 Web 浏览器中发生交互

必须了解的是，以交互方式获取令牌时，对话框的内容不是由库提供，而是由 STS（安全令牌服务）提供。 身份验证终结点发回一些 HTML 和 JavaScript，以控制在 Web 浏览器或 Web 控件中呈现的交互。 允许 STS 处理 HTML 交互具有许多优点：

- 应用程序或身份验证库绝不会存储密码（如果键入了密码）。
- 允许重定向到其他标识提供者（例如，使用具有 MSAL 的工作学校帐户或个人帐户登录，或使用具有 Azure AD B2C 的社交帐户登录）。
- 允许 STS 控制条件访问，例如，让用户在身份验证阶段进行[多重身份验证 (MFA)](../authentication/concept-mfa-howitworks.md)（在其手机上或手机上的身份验证应用中输入 Windows Hello PIN 或接听电话）。 如果尚未设置所需的多重身份验证，用户可以在同一对话框中及时设置。  用户输入手机号码后，系统会引导他们安装身份验证应用程序并扫描 QR 标记以添加其帐户。 这种服务器驱动的交互会带来很棒的体验！
- 密码过期后，允许用户在同一对话框中更改密码（为旧密码和新密码提供附加字段）。
- 允许对租户或由 Azure AD 租户管理员/应用程序所有者控制的应用程序（图像）进行品牌推广。
- 使用户能够在身份验证之后同意让应用程序以其名义访问资源/范围。

### <a name="embedded-vs-system-web-ui"></a>嵌入式与系统 Web UI

MSAL.NET 是一个多框架库，它具有特定于框架的代码，可在 UI 控件中托管浏览器（例如，在 .NET Classic 中，它使用 WinForms；在 Xamarin 中，它使用本机移动控件，等等）。 此控件称为 `embedded` Web UI。 另外，MSAL.NET 还能够启动系统 OS 浏览器。

通常，建议使用平台默认值（通常为系统浏览器）。 系统浏览器可以更好地记住以前登录的用户。 如果需要更改此行为，请使用 `WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>速览

| 框架        | 嵌入 | 系统 | 默认 |
| ------------- |-------------| -----| ----- |
| .NET Classic     | 是 | 是^ | 嵌入 |
| .NET Core     | 否 | 是^ | 系统 |
| .NET Standard | 否 | 是^ | 系统 |
| UWP | 是 | 否 | 嵌入 |
| Xamarin.Android | 是 | 是  | 系统 |
| Xamarin.iOS | 是 | 是  | 系统 |
| Xamarin.Mac| 是 | 否 | 嵌入 |

^ 需要“http://localhost”重定向 URI

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Xamarin.iOS、Xamarin.Android 上的系统 Web 浏览器

默认情况下，MSAL.NET 支持 Xamarin.iOS、Xamarin.Android 和 .NET Core 上的系统 Web 浏览器。 对于提供 UI 的所有平台（即，不是 .NET Core），该库提供一个对话框来嵌入 Web 浏览器控件。 MSAL.NET 还将嵌入式 Web 视图用于 .NET 桌面，将 WAB 用于 UWP 平台。 但是，默认情况下，它会为 Xamarin iOS 和 Xamarin Android 应用程序使用**系统 Web 浏览器**。 在 iOS 上，它甚至根据操作系统版本（iOS12、iOS11 和更早版本）选择要使用的 Web 视图。

使用系统浏览器的显著优势在于，无需代理（公司门户/验证器）即可与其他应用程序和 Web 应用程序共享 SSO 状态。 默认情况下，MSAL.NET 为 Xamarin iOS 和 Xamarin Android 平台使用系统浏览器，因为在这些平台上，系统 Web 浏览器占据整个屏幕，并且用户体验更好。 系统 Web 视图与对话框并无区别。 但是，在 iOS 上，用户可能必须同意浏览器回调应用程序，这可能很烦人。

## <a name="system-browser-experience-on-net-core"></a>.NET Core 上的系统浏览器体验

在 .NET Core 上，MSAL.NET 会将系统浏览器作为单独的进程启动。 MSAL.NET 无法控制此浏览器，但是，一旦用户完成身份验证，系统便会以 MSAL.NET 可以拦截 URI 的方式重定向网页。

你还可以通过指定以下命令，将为 .NET Classic 编写的应用配置为使用此浏览器

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET 无法检测用户是已经跳转还是只是关闭浏览器。 建议使用此技术的应用定义超时（通过 `CancellationToken`）。 建议将超时设置为至少几分钟，以考虑到提示用户更改密码或执行多重身份验证的情况。

### <a name="how-to-use-the-default-os-browser"></a>如何使用默认 OS 浏览器

MSAL.NET 需要侦听 `http://localhost:port`，并截获用户完成身份验证后 AAD 发送的代码（有关详细信息，请参阅[授权代码](v2-oauth2-auth-code-flow.md)）

若要启用系统浏览器，请执行以下操作：

1. 在应用注册期间，将 `http://localhost` 配置为重定向 URI（目前不受 B2C 支持）
2. 构造 PublicClientApplication 时，指定此重定向 URI：

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> 如果配置 `http://localhost`，MSAL.NET 将在内部查找随机开放端口并使用它。

### <a name="linux-and-mac"></a>Linux 和 MAC

在 Linux 上，MSAL.NET 将使用 xdg-open 工具打开默认 OS 浏览器。 若要进行故障排除，请从终端运行该工具，例如 `xdg-open "https://www.bing.com"`。 在 Mac 上，通过调用 `open <url>` 打开该浏览器。

### <a name="customizing-the-experience"></a>自定义体验

> [!NOTE]
> MSAL.NET 4.1.0 或更高版本提供自定义功能。

当收到令牌或发生错误时，MSAL.NET 能够使用 HTTP 消息进行响应。 你可以显示 HTML 消息或重定向到所选的 URL：

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

### <a name="opening-a-specific-browser-experimental"></a>打开特定浏览器（试验）

可以自定义 MSAL.NET 打开浏览器的方式。 例如，可以强制打开特定的浏览器，而不是使用默认的浏览器：

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP 不使用系统 Web 视图

但是，对于桌面应用程序，启动系统 Web 视图会导致用户体验欠佳，因为用户会看到浏览器，而他们可能已经打开了其他选项卡。 身份验证发生后，用户会获得一个页面，要求他们关闭此窗口。 如果用户不注意，他们可能会关闭整个进程（包括与身份验证无关的其他选项卡）。 如果使用桌面上的系统浏览器，还需要打开本地端口并进行侦听，这可能需要对应用程序具有高级权限。 身为开发人员、用户或管理员的你可能不愿意遵守此要求。

## <a name="enable-embedded-webviews-on-ios-and-android"></a>在 iOS 和 Android 上启用嵌入式 Web 视图

你还可以在 Xamarin.iOS 和 Xamarin.Android 应用中启用嵌入式 Web 视图。 从 MSAL.NET 2.0.0-preview 开始，MSAL.NET 还支持使用**嵌入式** Web 视图选项。 对于 ADAL.NET，嵌入式 Web 视图是唯一受支持的选项。

作为使用面向 Xamarin 的 MSAL.NET 的开发人员，你可以选择使用嵌入式 Web 视图或系统浏览器。 你可以根据要实现的用户体验和要解决的安全问题来进行选择。

目前，MSAL.NET 尚不支持 Android 和 iOS 代理。 因此，如果需要提供单一登录 (SSO)，系统浏览器可能仍是一个更好的选择。 在嵌入式 Web 浏览器中支持代理属于 MSAL.NET 积压工作。

### <a name="differences-between-embedded-webview-and-system-browser"></a>嵌入式 Web 视图和系统浏览器之间的区别
MSAL.NET 中的嵌入式 Web 视图和系统浏览器在视觉上有所区别。

**通过 MSAL.NET 使用嵌入式 Web 视图进行交互式登录：**

![嵌入式](media/msal-net-web-browsers/embedded-webview.png)

**通过 MSAL.NET 使用系统浏览器进行交互式登录：**

![系统浏览器](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>开发人员选项

作为使用 MSAL.NET 的开发人员，可以使用多个选项来显示 STS 的交互式对话框：

- **系统浏览器。** 默认情况下，库中已设置系统浏览器。 如果使用 Android，请阅读[系统浏览器](msal-net-system-browser-android-considerations.md)，了解有关支持身份验证的浏览器的具体信息。 在 Android 中使用系统浏览器时，建议设备使用支持 Chrome 自定义选项卡的浏览器。  否则，身份验证可能会失败。
- **嵌入式 Web 视图。** 若要在 MSAL.NET 中仅使用嵌入式 Web 视图，`AcquireTokenInteractively` 参数生成器包含 `WithUseEmbeddedWebView()` 方法。

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

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>在 Xamarin.iOS 上的嵌入式 Web 浏览器或系统浏览器之间进行选择

在 iOS 应用中，可以在 `AppDelegate.cs` 中将 `ParentWindow` 初始化为 `null`。 它不适用于 iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>在 Xamarin.Android 上的嵌入式 Web 浏览器或系统浏览器之间进行选择

在 Android 应用中，可以在 `MainActivity.cs` 中设置父活动，以便身份验证结果返回到该活动：

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

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>检测 Xamarin.Android 上是否存在自定义选项卡

如果想使用系统 Web 浏览器来启用浏览器中运行的应用的 SSO，但又担心 Android 设备没有支持自定义选项卡的浏览器会导致用户体验不佳，则可以选择通过在 `IPublicClientApplication` 中调用 `IsSystemWebViewAvailable()` 方法来做出决定。 如果 PackageManager 检测到自定义选项卡，则此方法返回 `true`；如果在设备上未检测到它们，则返回 `false`。

你可以根据此方法返回的值和你的要求做出决定：

- 可以将自定义错误消息返回给用户。 例如：“请安装 Chrome 以继续进行身份验证”-或者-
- 可以回退到嵌入式 Web 视图选项，然后将 UI 作为嵌入式 Web 视图启动。

以下代码显示了嵌入式 Web 视图选项：

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core 不支持使用嵌入式浏览器进行交互式身份验证

对于 .NET Core，若要以交互方式获取令牌，只能使用系统 Web 浏览器，而不能使用嵌入式 Web 视图。 实际上，.NET Core 尚未提供 UI。
若要使用系统 Web 浏览器自定义浏览体验，可以实现 [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) 接口，甚至提供自己的浏览器。
