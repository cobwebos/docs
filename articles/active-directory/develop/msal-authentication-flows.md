---
title: 身份验证流 （Microsoft 身份验证库） |Azure
description: 了解有关身份验证流和使用由 Microsoft 身份验证库 (MSAL) 授予。
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
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 821143d39f8a4c06501ee38ef598a9d06d267d72
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273110"
---
# <a name="authentication-flows"></a>身份验证流

本指南介绍了不同的身份验证流提供由 Microsoft 身份验证库 (MSAL)。  这些流可以在各种不同的应用程序方案中使用。

| 流 | 描述 | 使用位置|  
| ---- | ----------- | ------- | 
| [交互式](#interactive) | 通过一个交互式过程，会提示用户提供凭据，通过浏览器或弹出窗口中获取的令牌。 | [桌面应用程序](scenario-desktop-overview.md)，[移动应用](scenario-mobile-overview.md) |
| [隐式授权](#implicit-grant) | 允许此应用以获取令牌，而执行的后端服务器凭据交换。 这可让应用登录用户、 维护会话，并获取其他 web Api，所有可以在客户端的令牌的 JavaScript 代码。| [单页面应用程序 (SPA)](scenario-spa-overview.md) |
| [授权代码](#authorization-code) | 若要获取对受保护资源，例如 web Api 的访问权限的设备安装的应用程序中使用。 这样可将单一登录和 API 访问权限添加到你的移动和桌面应用。 | [桌面应用程序](scenario-desktop-overview.md)，[移动应用](scenario-mobile-overview.md)， [web 应用](scenario-web-app-call-api-overview.md) | 
| [On-behalf-of](#on-behalf-of) | 具有应用程序调用服务或 web API，又需要调用另一个服务或 web API。 思路是通过请求链传播委托用户标识和权限。 | [Web API](scenario-web-api-call-api-overview.md) |
| [客户端凭据](#client-credentials) | 可以使用的应用程序的标识访问 web 托管资源。 通常用于服务器到服务器必须运行在后台，而无需直接与用户交互的交互。 | [守护程序应用](scenario-daemon-overview.md) |
| [设备代码](#device-code) | 允许用户登录到输入受限的设备，例如智能电视、 IoT 设备或打印机。 | [桌面/移动应用](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Windows 集成身份验证](scenario-desktop-acquire-token.md#integrated-windows-authentication) | 允许已加入域或 Azure Active Directory (Azure AD) 的应用程序获取令牌以无提示方式 （无任何来自用户的用户界面交互） 的计算机。| [桌面/移动应用](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [用户名/密码](scenario-desktop-acquire-token.md#username--password) | 允许应用程序以登录用户，通过直接处理其密码。 不建议使用此流。 | [桌面/移动应用](scenario-desktop-acquire-token.md#username--password) | 

## <a name="interactive"></a>交互
Msal 也支持能够以交互方式提示用户输入其凭据登录，并通过使用这些凭据获取令牌。

![交互式流的关系图](media/msal-authentication-flows/interactive.png)

有关使用 MSAL.NET 获取令牌在特定平台上的，以交互方式的详细信息，请参阅：
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [通用 Windows 平台](msal-net-uwp-considerations.md)

MSAL.js 中的交互式调用的详细信息，请参阅[提示 MSAL.js 交互式请求中的行为](msal-js-prompt-behavior.md)。

## <a name="implicit-grant"></a>隐式授权

但 msal 也支持[OAuth 2 隐式授权流](v2-oauth2-implicit-grant-flow.md)，它允许应用程序以获取令牌，从 Microsoft 标识平台而执行的后端服务器凭据交换。 这可让应用登录用户、 维护会话，并获取其他 web Api，所有可以在客户端的令牌的 JavaScript 代码。

![隐式授权流的关系图](media/msal-authentication-flows/implicit-grant.svg)

许多现代 web 应用程序是作为客户端的单页面应用程序，使用 JavaScript 或如 Angular、 Vue.js 和 React.js SPA 框架编写的。 这些应用程序 web 浏览器中运行，并且具有不同的身份验证特征与传统服务器端 web 应用程序。 Microsoft 标识平台，用户登录，并获取令牌以访问后端服务或 web Api，通过使用隐式授权流的单页面应用程序。 隐式流允许应用程序获取 ID 令牌表示已经过身份验证的用户，并访问所需令牌来调用受保护的 Api。

此身份验证流不包括使用跨平台 JavaScript 框架，例如 Electron 和 React 本机应用程序方案，因为它们需要更多的功能与本机平台进行交互。

## <a name="authorization-code"></a>授权代码
但 msal 也支持[OAuth 2 授权代码授予](v2-oauth2-auth-code-flow.md)。 若要获取对受保护资源，例如 web Api 的访问权限的设备安装的应用程序中可用此授权。 这样可将单一登录和 API 访问权限添加到你的移动和桌面应用。 

当用户登录到 web 应用程序 （网站） 时，web 应用程序接收授权代码。  兑换授权代码获取令牌来调用 web Api。 ASP.NET 和 ASP.NET Core web 应用中的唯一目标`AcquireTokenByAuthorizationCode`是将令牌添加到令牌缓存。 应用程序可以然后使用该令牌 (通常在控制器中，这只是获取的令牌 API 使用`AcquireTokenSilent`)。

![授权代码流的关系图](media/msal-authentication-flows/authorization-code.png)

在前面的图中，应用程序：

1. 请求授权代码，可凭此访问令牌。
2. 使用访问令牌以调用 web API。

### <a name="considerations"></a>注意事项
- 可以使用授权代码一次只能兑换一个令牌。 请勿尝试获取令牌多次使用相同的授权代码 （它显式禁止协议标准规范）。 如果你兑换代码几次有意，或者因为您不知道，一种框架还会为用户，您将收到以下错误： `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- 如果您要编写 ASP.NET 或 ASP.NET Core 应用程序，这可能会发生您不告知框架，如果你已经兑换授权代码。 为此，您需要调用`context.HandleCodeRedemption()`方法的`AuthorizationCodeReceived`事件处理程序。

- 避免使用 ASP.NET，这可能会防止正确发生的增量许可共享访问令牌。 有关详细信息，请参阅[发出 #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693)。

## <a name="on-behalf-of"></a>上代表的

但 msal 也支持[OAuth 2 上代表的身份验证流](v2-oauth2-on-behalf-of-flow.md)。  当应用程序调用服务或 web API，又需要调用另一个服务或 web API 时使用此流。 思路是通过请求链传播委托用户标识和权限。 要使中间层服务向下游服务发出身份验证请求，该服务需要代表用户保护 Microsoft 标识平台提供的访问令牌。

![关系图上的代理流](media/msal-authentication-flows/on-behalf-of.png)

在上图中：

1. 应用程序获取 web API 的访问令牌。
2. 在客户端 （web、 桌面、 移动、 或单页面应用程序） 调用受保护的 web API，将访问令牌添加为 HTTP 请求的身份验证标头中的持有者令牌。 Web API 对用户进行身份验证。
3. 当客户端调用 web API 时，web API 请求另一个令牌上代表的用户。  
4. 受保护的 web API 使用此令牌上代表的用户调用下游 web API。  对于其他下游 Api （但仍代表相同的用户），web API 还可以更高版本的请求令牌。

## <a name="client-credentials"></a>客户端凭据

但 msal 也支持[OAuth 2 客户端凭据流](v2-oauth2-client-creds-grant-flow.md)。 此流，可使用的应用程序的标识访问 web 托管资源。 这种授予通常用于必须在后台运行的服务器间交互，不需要立即与用户交互。 这些类型的应用程序通常称为守护程序或服务帐户。 

客户端凭据授予流允许 web 服务 （机密客户端） 以使用其自己的凭据，而不是模拟用户时调用另一个 web 服务进行身份验证。 在此方案中，客户端通常是中间层 web 服务、 后台程序服务或网站。 为了进行更高级别的保证，Microsoft 标识平台还允许调用服务将证书（而不是共享机密）用作凭据。

> [!NOTE]
> 机密客户端流上不可用的移动平台 （UWP、 Xamarin.iOS 和 Xamarin.Android），因为它们仅支持公共客户端应用程序。 公共客户端应用程序不知道如何向标识提供程序验证应用程序的标识。 可以在 web 应用上实现安全连接或通过将证书部署 web API 后结束。

MSAL.NET 支持两种类型的客户端凭据。 这些客户端凭据需要注册到 Azure AD。 凭据将传递到你的代码中的机密客户端应用程序的构造函数。

### <a name="application-secrets"></a>应用程序密钥 

![具有密码的机密客户端的关系图](media/msal-authentication-flows/confidential-client-password.png)

在前面的图中，应用程序：

1. 获取一个令牌通过使用应用程序机密或密码凭据。
2. 使用令牌发出请求的资源。

### <a name="certificates"></a>证书 

![使用证书的机密客户端的关系图](media/msal-authentication-flows/confidential-client-certificate.png)

在前面的图中，应用程序：

1. 获取一个令牌通过使用证书凭据。
2. 使用令牌发出请求的资源。

需要将这些客户端凭据：
- 注册到 Azure AD。
- 在你的代码中的机密客户端应用程序的构造时传入。


## <a name="device-code"></a>设备代码
但 msal 也支持[OAuth 2 设备代码流](v2-oauth2-device-code.md)，它允许用户登录到输入受限的设备，例如智能电视、 IoT 设备或打印机。 使用 Azure AD 的交互式身份验证需要 web 浏览器。 设备代码流使用户可以使用另一台设备 （例如，另一台计算机或移动电话） 登录以交互方式，其中的设备或操作系统不提供 web 浏览器。

通过使用设备代码流，应用程序获取令牌通过专门设计用于这些设备或操作系统一个两步过程。 此类应用程序的示例包括命令行工具 (CLI) 或 IoT 设备上运行。 

![设备代码流的关系图](media/msal-authentication-flows/device-code.png)

在上图中：

1. 需要用户身份验证时，应用提供的代码，并要求用户使用其他设备 （如连接到 internet 的智能手机） 转到 URL (例如， https://microsoft.com/devicelogin) 。 用户然后提示你输入的代码，并继续进行正常身份验证体验，包括许可提示和多重身份验证，如有必要。

2. 身份验证成功后的命令行应用接收通过后通道，所需的标记，并使用它们来执行其所需的 web API 调用。

### <a name="constraints"></a>约束

- 设备代码流是仅适用于公共客户端应用程序。
- 构造公共客户端应用程序必须是以下值之一时传递颁发机构：
  - 租户 (窗体`https://login.microsoftonline.com/{tenant}/`其中`{tenant}`是 GUID 表示的租户 ID 或与租户关联的域)。
  - 对于任何工作和学校帐户 (`https://login.microsoftonline.com/organizations/`)。
- Microsoft 个人帐户尚不受 Azure AD v2.0 终结点 (不能使用`/common`或`/consumers`租户)。

## <a name="integrated-windows-authentication"></a>Windows 集成身份验证
为桌面但 msal 也支持集成 Windows 身份验证 (IWA)，或在已加入域或 Azure AD 运行的移动应用程序已加入 Windows 计算机。 使用 IWA，这些应用程序可以获取令牌以无提示方式 （无任何来自用户的用户界面交互）。 

![集成的 Windows 身份验证的关系图](media/msal-authentication-flows/integrated-windows-authentication.png)

在前面的图中，应用程序：

1. 使用集成 Windows 身份验证获取令牌。
2. 使用令牌发出请求的资源。

### <a name="constraints"></a>约束

IWA 支持联合的用户，这意味着用户在 Active Directory 中创建并由 Azure AD 支持。 用户没有支持的 Active Directory 在 Azure AD 中直接创建 （托管用户） 不能使用此身份验证流。 此限制不会影响[用户名/密码流](#usernamepassword)。

用于为.NET Framework、.NET Core 和通用 Windows 平台的平台编写的应用程序是 IWA。

IWA 不是绕过多重身份验证。 如果配置多重身份验证，如果需要多重身份验证质询，则可能会失败 IWA。 多重身份验证需要用户交互。

您不控制当标识提供者请求双因素身份验证来执行。 租户管理员执行。 通常情况下，双因素身份验证是必需的登录时从不同国家/地区、 你在未连接时通过 VPN 到公司网络和有时甚至当你将通过 VPN 连接。 Azure AD 使用 AI 来不断了解是否需要双因素身份验证。 如果 IWA 失败，应回退到用户提示 (https://aka.ms/msal-net-interactive) 。

构造公共客户端应用程序必须是以下值之一时传递颁发机构：
- 租户 (窗体`https://login.microsoftonline.com/{tenant}/`其中`tenant`是 guid 表示的租户 ID 或与租户关联的域)。
- 对于任何工作和学校帐户 (`https://login.microsoftonline.com/organizations/`)。 不支持 Microsoft 个人帐户 (不能使用`/common`或`/consumers`租户)。

由于 IWA 是无提示的流，必须是以下之一：
- 应用程序的用户必须具有此前已表示同意使用应用程序。 
- 租户管理员必须以前已同意使用应用程序的租户中的所有用户。

这意味着以下项之一为 true:
- 作为开发人员所选**授予**自己在 Azure 门户上。
- 租户管理员已选择了**Grant/revoke {租户域} 的管理员同意**中**API 权限**选项卡上的应用程序的注册 (请参阅[添加权限以访问 web Api](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- 提供便于用户同意该应用程序 (请参阅[请求单个用户同意](v2-permissions-and-consent.md#requesting-individual-user-consent))。
- 提供了一种方法为租户管理员同意使用应用程序 (请参阅[管理员同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant))。

IWA 流可用于.NET 桌面、.NET Core 和 Windows 通用平台应用。 .NET Core 上只获取用户名的重载是可用的。 .NET Core 平台不能请求对操作系统的用户名。
  
有关许可的详细信息，请参阅[v2.0 权限和许可](v2-permissions-and-consent.md)。

## <a name="usernamepassword"></a>用户名/密码 
但 msal 也支持[OAuth 2 资源所有者密码凭据授予](v2-oauth-ropc.md)，让应用程序以登录用户，通过直接处理其密码。 在桌面应用程序，可以使用用户名/密码流以无提示方式获取令牌。 使用应用程序时，没有用户界面是必需的。

![用户名/密码流的关系图](media/msal-authentication-flows/username-password.png)

在前面的图中，应用程序：

1. 通过将用户名和密码发送到标识提供者获取一个令牌。
2. 通过使用该令牌调用 web API。

> [!WARNING]
> 不建议使用此流。 它需要高级别的信任和用户风险。  不能使用其他、 更安全的流时，只应使用此流。 有关详细信息，请参阅[什么是密码的不断增长的问题的解决方案？](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。 

用于获取令牌以无提示方式在 Windows 已加入域的计算机上的首选流程[集成 Windows 身份验证](#integrated-windows-authentication)。 否则，您还可以使用[设备代码流](#device-code)。

如果你想要在其中提供自己的 UI 在交互式方案中使用用户名/密码，这是在某些情况下 （DevOps 方案） 很有用，尝试避免它。 通过使用用户名/密码：
- 需要执行多重身份验证的用户将无法登录 （因为无需交互）。
- 用户将无法进行单一登录。

### <a name="constraints"></a>约束

除了[集成 Windows 身份验证约束](#integrated-windows-authentication)，以下约束也适用：

- 用户名/密码流都不允许使用条件性访问和多重身份验证。 因此，如果您的应用程序运行在 Azure AD 租户中的租户管理员需要多重身份验证，不能使用此流。 许多组织执行该操作。
- 它仅适用于工作和学校帐户 （而不是 Microsoft 帐户）。
- .NET 桌面和.NET Core，但不是在通用 Windows 平台上提供了流。

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C 的详细信息

有关使用 MSAL.NET 和 Azure AD B2C 的详细信息，请参阅[使用 ROPC 与 Azure AD B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c)。
