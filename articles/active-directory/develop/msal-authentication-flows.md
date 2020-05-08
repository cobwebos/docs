---
title: MSAL 身份验证流 | Azure
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 身份验证库 (MSAL) 使用的身份验证流和授权。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/30/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: db9937d87692a1221d72bd27cfd653d803b9a1c6
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82883237"
---
# <a name="authentication-flows"></a>身份验证流

本文介绍 Microsoft 身份验证库 (MSAL) 提供的不同身份验证流。  可在各种不同的应用程序方案中使用这些流。

| 流向 | 说明 | 适用范围|  
| ---- | ----------- | ------- | 
| [交互式](#interactive) | 通过一个提示用户在浏览器或弹出窗口中提供凭据的交互式过程获取令牌。 | [桌面应用](scenario-desktop-overview.md)、[移动应用](scenario-mobile-overview.md) |
| [隐式授权](#implicit-grant) | 允许应用在不执行后端服务器凭据交换的情况下获取令牌。 这可让应用登录用户、维护会话，并获取客户端 JavaScript 代码中所有其他 Web API 的令牌。| [单页应用程序 (SPA)](scenario-spa-overview.md) |
| [授权代码](#authorization-code) | 在设备上安装的应用中使用，以访问受保护的资源，例如 Web API。 这样，就可以添加对移动应用和桌面应用的登录与 API 访问权限。 | [桌面应用](scenario-desktop-overview.md)、[移动应用](scenario-mobile-overview.md)、[Web 应用](scenario-web-app-call-api-overview.md) | 
| [代理](#on-behalf-of) | 应用程序调用某个服务或 Web API，而后者又需要调用另一个服务或 Web API。 思路是通过请求链传播委托用户标识和权限。 | [Web API](scenario-web-api-call-api-overview.md) |
| [客户端凭据](#client-credentials) | 允许你使用应用程序的标识访问 Web 托管的资源。 通常用于必须在后台运行的服务器间交互，不需要立即与用户交互。 | [守护程序应用](scenario-daemon-overview.md) |
| [设备代码](#device-code) | 允许用户登录到智能电视、IoT 设备或打印机等输入受限的设备。 | [桌面/移动应用](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Windows 集成身份验证](scenario-desktop-acquire-token.md#integrated-windows-authentication) | 允许已加入域或已加入 Azure Active Directory (Azure AD) 的计算机上的应用程序以静默方式获取令牌（无需用户进行任何 UI 交互）。| [桌面/移动应用](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [用户名/密码](scenario-desktop-acquire-token.md#username-and-password) | 允许应用程序通过直接处理用户的密码将用户登录。 不建议使用此流。 | [桌面/移动应用](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>每个流如何发出令牌和代码
 
根据客户端的生成方式，客户端可以使用 Microsoft 标识平台支持的一种（或几种）身份验证流。  这些流可以生成各种令牌（id_tokens、刷新令牌、访问令牌）以及授权代码，并需要不同的令牌使其正常工作。 此图表提供概述：
 
|流向 | 需要 | id_token | 访问令牌 | 刷新令牌 | 授权代码 | 
|-----|----------|----------|--------------|---------------|--------------------|
|[授权代码流](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[隐式流](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[混合 OIDC 流](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[刷新令牌兑换](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 刷新令牌 | x | x | x| |
|[代理流](v2-oauth2-on-behalf-of-flow.md) | 访问令牌| x| x| x| |
|[设备代码流](v2-oauth2-device-code.md) | | x| x| x| |
|[客户端凭据](v2-oauth2-client-creds-grant-flow.md) | | | x（仅限应用）| | |
 
通过隐式模式颁发的令牌由于通过 URL（其中 `response_mode` 是 `query` 或 `fragment`）传回浏览器而具有长度限制。  有些浏览器对可以放在浏览器栏中的 URL 的大小有限制，当 URL 太长时会失败。  因此，这些令牌没有 `groups` 或 `wids` 声明。

## <a name="interactive"></a>交互

MSAL 支持以交互方式提示用户输入其凭据，以使用这些凭据登录并获取令牌。

![交互式流示意图](media/msal-authentication-flows/interactive.png)

有关使用 MSAL.NET 以交互方式获取特定平台上的令牌的详细信息，请参阅：
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [通用 Windows 平台](msal-net-uwp-considerations.md)

有关 MSAL.js 中的交互式调用的详细信息，请参阅 [MSAL.js 交互式请求中的提示行为](msal-js-prompt-behavior.md)。

## <a name="implicit-grant"></a>隐式授权

MSAL 支持 [OAuth 2 隐式授权流](v2-oauth2-implicit-grant-flow.md)，可让应用从 Microsoft 标识平台获取令牌，无需要执行后端服务器凭据交换。 这可让应用登录用户、维护会话，并获取客户端 JavaScript 代码中所有其他 Web API 的令牌。

![隐式授权流示意图](media/msal-authentication-flows/implicit-grant.svg)

许多新式 Web 应用程序都是使用 Angular、Vue.js 和 React.js 等 JavaScript 或 SPA 框架编写的客户端单页应用程序。 这些应用程序在 Web 浏览器中运行，与传统的服务器端 Web 应用程序相比，它们具有不同的身份验证特征。 Microsoft 标识平台可让单页应用程序使用隐式授权流将用户登录，并获取用于访问后端服务或 Web API 的令牌。 隐式流允许应用程序获取 ID 令牌来表示已经过身份验证的用户以及调用受保护 API 所需的访问令牌。

此身份验证流不包括使用 Electron 和 React-Native 之类的跨平台 JavaScript 框架的应用程序方案，因为它们需要使用其他功能才能与本机平台交互。

## <a name="authorization-code"></a>授权代码

MSAL 支持 [OAuth 2.0 授权代码授予](v2-oauth2-auth-code-flow.md)。 可在设备上安装的应用中使用此授权，以访问受保护的资源，例如 Web API。 这样，就可以添加对移动应用和桌面应用的登录与 API 访问权限。 

当用户登录到 Web 应用程序（网站）时，Web 应用程序会收到授权代码。  兑换该授权代码可获取用于调用 Web API 的令牌。 在 ASP.NET 和 ASP.NET Core Web 应用中，`AcquireTokenByAuthorizationCode` 的唯一目的是将令牌添加到令牌缓存。 然后，应用程序可以使用该令牌（通常在控制器中，只需使用 `AcquireTokenSilent` 即可获取 API 的令牌）。

![授权代码流示意图](media/msal-authentication-flows/authorization-code.png)

在上图中，应用程序：

1. 请求用于兑换访问令牌的授权代码。
2. 使用访问令牌调用 Web API。

### <a name="considerations"></a>注意事项

- 只能使用授权代码兑换令牌一次。 不要尝试使用同一个授权代码多次获取令牌（协议标准规范明确禁止此行为）。 如果你有意地多次用该代码兑换令牌，或者你没有意识到框架也在为你兑换令牌，则会收到以下错误：`AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- 编写 ASP.NET 或 ASP.NET Core 应用程序时，如果未告知框架你已用授权代码兑换令牌，则可能会发生此错误。 为此，需要调用 `AuthorizationCodeReceived` 事件处理程序的 `context.HandleCodeRedemption()` 方法。

- 避免与 ASP.NET 共享访问令牌，否则可能无法正常进行增量许可。 有关详细信息，请参阅[问题 #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693)。

## <a name="on-behalf-of"></a>代理

MSAL 支持 [OAuth 2 代理身份验证流](v2-oauth2-on-behalf-of-flow.md)。  当应用程序调用某个服务或 Web API，而后者又需要调用另一个服务或 Web API 时，会使用此流。 思路是通过请求链传播委托用户标识和权限。 要使中间层服务向下游服务发出身份验证请求，该服务需要代表用户保护 Microsoft 标识平台提供的访问令牌。

![代理流示意图](media/msal-authentication-flows/on-behalf-of.png)

在上图中：

1. 应用程序获取 Web API 的访问令牌。
2. 客户端（Web、桌面、移动或单页应用程序）调用受保护的 Web API，在 HTTP 请求的身份验证标头中添加访问令牌作为持有者令牌。 Web API 对用户进行身份验证。
3. 当客户端调用 Web API 时，Web API 代表用户请求另一个令牌。  
4. 受保护的 Web API 使用此令牌代表用户调用下游 Web API。  以后，Web API 也可以请求其他下游 API 的令牌（仍代表同一用户）。

## <a name="client-credentials"></a>客户端凭据

MSAL 支持 [OAuth 2 客户端凭据流](v2-oauth2-client-creds-grant-flow.md)。 此流允许你使用应用程序的标识访问 Web 托管的资源。 这种授予通常用于必须在后台运行的服务器间交互，不需要立即与用户交互。 此类应用程序通常称为守护程序或服务帐户。 

客户端凭据授权流允许 Web 服务（机密客户端）在调用其他 Web 服务时使用它自己的凭据（而不是模拟用户）进行身份验证。 在这种情况下，客户端通常是中间层 Web 服务、后台程序服务或网站。 为了进行更高级别的保证，Microsoft 标识平台还允许调用服务将证书（而不是共享机密）用作凭据。

> [!NOTE]
> 机密客户端流不适用于移动平台（UWP、Xamarin.iOS 和 Xamarin.Android），因为它们仅支持公共客户端应用程序。 公共客户端应用程序不知道如何向标识提供者证明应用程序的身份。 可以通过部署证书在 Web 应用或 Web API 后端上实现安全连接。

MSAL.NET 支持两种类型的客户端凭据。 这些客户端凭据需要注册到 Azure AD。 凭据将传入代码中机密客户端应用程序的构造函数。

### <a name="application-secrets"></a>应用程序密钥

![使用密码的机密客户端示意图](media/msal-authentication-flows/confidential-client-password.png)

在上图中，应用程序：

1. 使用应用程序机密或密码凭据获取令牌。
2. 使用该令牌发出资源请求。

### <a name="certificates"></a>证书

![使用证书的机密客户端示意图](media/msal-authentication-flows/confidential-client-certificate.png)

在上图中，应用程序：

1. 使用证书凭据获取令牌。
2. 使用该令牌发出资源请求。

这些客户端凭据需要：
- 注册到 Azure AD。
- 传入代码中机密客户端应用程序的构造。

## <a name="device-code"></a>设备代码

MSAL 支持 [OAuth 2 设备代码流](v2-oauth2-device-code.md)，可让用户登录到智能电视、IoT 设备或打印机等输入受限的设备。 使用 Azure AD 的交互式身份验证需要 Web 浏览器。 如果设备或操作系统不提供 Web 浏览器，设备代码流可让用户使用另一台设备（例如另一台计算机或手机）以交互方式登录。

应用程序使用设备代码流通过专门为这些设备或操作系统设计的双步过程获取令牌。 此类应用程序的例子包括 IoT 设备上运行的应用程序，或命令行工具 (CLI)。 

![设备代码流示意图](media/msal-authentication-flows/device-code.png)

在上图中：

1. 每当需要用户身份验证时，应用将提供一个代码，并要求用户使用另一台设备（例如，已连接到 Internet 的智能手机）访问某个 URL（例如 `https://microsoft.com/devicelogin`）。 然后，系统会提示用户输入该代码，并转到完成正常身份验证的体验，包括许可提示和多重身份验证（如果需要）。

2. 成功完成身份验证后，命令行应用会通过传回通道收到所需的令牌，并使用这些令牌执行所需的 Web API 调用。

### <a name="constraints"></a>约束

- 设备代码流仅适用于公共客户端应用程序。
- 构造公共客户端应用程序时传入的颁发机构必须是下列其中一项：
  - 租户化（采用 `https://login.microsoftonline.com/{tenant}/` 格式，其中，`{tenant}` 是表示租户 ID 或者与该租户关联的域的 GUID）。
  - 适用于任何工作和学校帐户 (`https://login.microsoftonline.com/organizations/`)。
- Azure AD v2.0 终结点尚不支持 Microsoft 个人帐户（不能使用`/common`或`/consumers`租户）。

## <a name="integrated-windows-authentication"></a>Windows 集成身份验证

对于已加入域或已加入 Azure AD 的 Windows 计算机上运行的桌面或移动应用程序，MSAL 支持 Windows 集成身份验证 (IWA)。 这些应用程序可以使用 IWA 以静默方式获取令牌（无需用户进行任何 UI 交互）。 

![Windows 集成身份验证示意图](media/msal-authentication-flows/integrated-windows-authentication.png)

在上图中，应用程序：

1. 使用 Windows 集成身份验证获取令牌。
2. 使用该令牌发出资源请求。

### <a name="constraints"></a>约束

IWA 仅支持联合用户（在 Active Directory 中创建的并由 Azure AD 支持的用户）。 直接在 Azure AD 中创建的但不是由 Active Directory 支持的用户（托管用户）不能使用此身份验证流。 此项限制不影响[用户名/密码流](#usernamepassword)。

IWA 适用于针对 .NET Framework、.NET Core 和通用 Windows 平台编写的应用。

IWA 不会绕过多重身份验证。 如果配置了多重身份验证，需要多重身份验证质询时，IWA 可能会失败。 多重身份验证需要用户交互。

你无法控制标识提供者何时请求执行双重身份验证， 而租户管理员可以控制。 通常，在未通过 VPN 连接到企业网络的情况下（有时，甚至已通过 VPN 连接到企业网络的情况下），从不同的国家/地区登录时都需要执行双重身份验证。 Azure AD 使用 AI 来持续了解是否需要双重身份验证。 如果 IWA 失败，则应回退到 [交互用户提示] （#interactive）。

构造公共客户端应用程序时传入的颁发机构必须是下列其中一项：
- 租户化（采用 `https://login.microsoftonline.com/{tenant}/` 格式，其中，`tenant` 是表示租户 ID 或者与该租户关联的域的 GUID）。
- 适用于任何工作和学校帐户 (`https://login.microsoftonline.com/organizations/`)。 不支持 Microsoft 个人帐户（你不能使用`/common`或`/consumers`租户）。

由于 IWA 是一个静默流，因此必须符合以下条件之一：
- 应用程序的用户必须已事先许可使用该应用程序。 
- 租户管理员必须已事先许可租户中的所有用户使用该应用程序。

这意味着需要符合以下条件之一：
- 开发人员已在 Azure 门户上自行选择“授权”。 
- 租户管理员已在应用程序注册的“API 权限”选项卡中选择“授予/撤销 {租户域} 的管理员许可”（请参阅[添加用于访问 Web API 的权限](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)）。  
- 你已提供某种方式让用户许可应用程序（请参阅[请求单个用户的许可](v2-permissions-and-consent.md#requesting-individual-user-consent)）。
- 你已提供某种方式让租户管理员许可应用程序（请参阅[管理员许可](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)）。

已针对 .NET Desktop、.NET Core 和 Windows 通用平台应用启用 IWA 流。 在 .NET Core 上，必须向 IWA 提供用户名，因为 .NET Core 无法从操作系统获取用户名。
  
有关许可的详细信息，请参阅 [v2.0 权限和许可](v2-permissions-and-consent.md)。

## <a name="usernamepassword"></a>用户名/密码

MSAL 支持 [OAuth 2 资源所有者密码凭据授予](v2-oauth-ropc.md)，后者允许应用程序通过直接处理用户的密码来登录用户。 在桌面应用程序中，可以使用用户名/密码流以静默方式获取令牌。 使用应用程序时无需 UI。

![用户名/密码流示意图](media/msal-authentication-flows/username-password.png)

在上图中，应用程序：

1. 通过将用户名和密码发送到标识提供者来获取令牌。
2. 使用该令牌调用 Web API。

> [!WARNING]
> 不建议使用此流。 它需要较高级别的信任，并且会透露用户信息。  仅当无法使用其他更安全的流时，才使用此流。 有关详细信息，请参阅[如何解决不断增多的密码问题？](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。 

在已加入 Windows 域的计算机上以静默方式获取令牌的首选流是 [Windows 集成身份验证](#integrated-windows-authentication)。 否则，也可以使用[设备代码流](#device-code)。

尽管这在某些情况下（DevOps 方案）很有用，但如果你想要在提供自己的 UI 的交互方案中使用用户名/密码，请尝试避免出现这种情况。 使用用户名/密码：
- 需要执行多重身份验证的用户将无法登录（因为没有交互）。
- 用户无法执行单一登录。

### <a name="constraints"></a>约束

除了 [Windows 集成身份验证约束](#integrated-windows-authentication)以外，以下约束也适用：

- 用户名/密码流与条件访问和多重身份验证不兼容。 因此，如果应用在 Azure AD 租户中运行，而该租户中的租户管理员需要多重身份验证，则你无法使用此流。 许多组织都会提出这种要求。
- 它仅适用于工作和学校帐户（而不是 Microsoft 帐户）。
- 可在 .NET Desktop 和 .NET Core 中使用该流，但不能在通用 Windows 平台中使用。

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C 细节

有关在 MSAL.NET 和 Azure AD B2C 中使用 ROPC 的详细信息，请参阅将[ROPC 与 Azure AD B2C 配合使用](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc)。
