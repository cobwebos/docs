---
title: MSA 身份验证流 | Azure
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 身份验证库 (MSAL) 使用的身份验证流和授权。
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
ms.openlocfilehash: ce81af90baeeda519f1b56d1e10a46923ebd22c2
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772125"
---
# <a name="authentication-flows"></a>身份验证流

本文介绍 Microsoft 身份验证库 (MSAL) 提供的不同身份验证流。  这些流可用于各种不同的应用方案。

| 流向 | 说明 | 适用范围|  
| ---- | ----------- | ------- | 
| [交互式](#interactive) | 通过交互式进程获取令牌，该进程通过浏览器或弹出窗口提示用户输入凭据。 | [桌面应用](scenario-desktop-overview.md)、[移动应用](scenario-mobile-overview.md) |
| [隐式授权](#implicit-grant) | 允许应用在不执行后端服务器凭据交换的情况下获取令牌。 这可让应用登录用户、维护会话，并获取客户端 JavaScript 代码中所有其他 Web API 的令牌。| [单页应用程序 (SPA)](scenario-spa-overview.md) |
| [授权代码](#authorization-code) | 用于安装在设备上以访问受保护资源（如 web API）的应用中。 这使你能够向移动应用和桌面应用添加登录和 API 访问权限。 | [桌面应用](scenario-desktop-overview.md)、[移动应用](scenario-mobile-overview.md)、[web 应用](scenario-web-app-call-api-overview.md) | 
| [代理](#on-behalf-of) | 应用程序调用服务或 web API，而服务或 web API 又需要调用另一个服务或 web API。 思路是通过请求链传播委托用户标识和权限。 | [Web API](scenario-web-api-call-api-overview.md) |
| [客户端凭据](#client-credentials) | 允许使用应用程序的标识访问 web 托管的资源。 通常用于必须在后台运行的服务器间交互，不需要立即与用户交互。 | [守护程序应用](scenario-daemon-overview.md) |
| [设备代码](#device-code) | 允许用户登录到智能电视、IoT 设备或打印机等输入受限的设备。 | [桌面/移动应用](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Windows 集成身份验证](scenario-desktop-acquire-token.md#integrated-windows-authentication) | 允许联接域或 Azure Active Directory (Azure AD) 的计算机上的应用程序以无提示的方式获取令牌（无需用户进行任何 UI 交互）。| [桌面/移动应用](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [用户名/密码](scenario-desktop-acquire-token.md#username-and-password) | 允许应用程序通过直接处理用户密码来登录用户。 不建议使用此流。 | [桌面/移动应用](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>每个流如何发出令牌和代码
 
根据客户端的构建方式，它可以使用一个（或多个）Microsoft 标识平台支持的身份验证流。  这些流可以生成各种令牌（id_令牌、刷新令牌、访问令牌）以及授权代码，并需要不同的令牌才能正常使用。 此图表提供了概述：
 
|流向 | 需要 | id_token | 访问令牌 | 刷新令牌 | 授权代码 | 
|-----|----------|----------|--------------|---------------|--------------------|
|[授权代码流](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[隐式流](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[混合 OIDC 流](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[刷新令牌兑换](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 刷新令牌 | x | x | x| |
|[代理流](v2-oauth2-on-behalf-of-flow.md) | 访问令牌| x| x| x| |
|[设备代码流](v2-oauth2-device-code.md) | | x| x| x| |
|[客户端凭据](v2-oauth2-client-creds-grant-flow.md) | | | x（仅限应用）| | |
 
由于通过 URL 传回浏览器（其中 `response_mode` 是 `query` 或 `fragment`），通过隐式模式发出的令牌具有长度限制。  有些浏览器对 URL 长度有限制，如果 URL 太长，则会出现错误。  因此，这些令牌没有 `groups` 或 `wids` 声明。

## <a name="interactive"></a>交互

MSAL 支持以交互方式提示用户输入其登录凭据并使用这些凭据获取令牌。

![交互式流示意图](media/msal-authentication-flows/interactive.png)

有关使用 MSAL.NET 在特定平台上交互获取令牌的详细信息，请参阅以下文章：
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [通用 Windows 平台](msal-net-uwp-considerations.md)

有关 MSAL.js 中交互式调用的详细信息，请参阅 [MSAL.js 交互请求提示行为](msal-js-prompt-behavior.md)。

## <a name="implicit-grant"></a>隐式授权

MSAL 支持 [OAuth 2 隐式授权流](v2-oauth2-implicit-grant-flow.md)，该流允许应用程序从 Microsoft 标识平台获取令牌，而无需执行后端服务器凭据交换。 这可让应用登录用户、维护会话，并获取客户端 JavaScript 代码中所有其他 Web API 的令牌。

![隐式授权流示意图](media/msal-authentication-flows/implicit-grant.svg)

许多现代 Web 应用程序是作为客户端单页应用程序构建的，此类应用程序使用 JavaScript 或 SPA 框架（例如 Angular、Vue.js 和 React.js）编写。 这些应用程序在 Web 浏览器中运行，其身份验证特征不同于传统的服务器端 Web 应用程序。 通过使用隐式授权流，Microsoft 标识平台使单页应用程序能够登录用户，并获取令牌以访问后端服务或 web API。 隐式流允许应用程序获取表示经过身份验证的用户的 ID 令牌，并访问调用受保护 API 所需的令牌。

此身份验证流不包括使用跨平台 JavaScript 框架（如 Electron 和 React Native）的应用程序方案，因为它们需要与本机平台交互的更多功能。

## <a name="authorization-code"></a>授权代码

MSAL 支持 [OAuth 2.0 授权代码授予](v2-oauth2-auth-code-flow.md)。 此授权可用于安装在设备上以访问受保护资源（如 web API）的应用中。 这使你能够向移动应用和桌面应用添加登录和 API 访问权限。 

当用户登录到 web 应用程序（网站）时，web 应用程序将收到一个授权代码。  授权代码会被兑换为获取用于调用 web API 的令牌。 在 ASP.NET 以及 ASP.NET Core web 应用中，`AcquireTokenByAuthorizationCode` 的唯一目标是将令牌添加到令牌缓存。 然后，应用程序可以使用令牌（通常用于控制器中，控制器只是通过使用 `AcquireTokenSilent` 获取 API 的令牌）。

![授权代码流示意图](media/msal-authentication-flows/authorization-code.png)

在上图中，应用程序：

1. 请求授权代码，该代码将兑换为访问令牌。
2. 使用访问令牌调用 Web API。

### <a name="considerations"></a>注意事项

- 只能使用授权码兑换令牌一次。 不要尝试使用相同的授权代码多次获取令牌（协议标准规范明确禁止这样做）。 如果你故意多次兑换代码，或者没有注意到框架执行此操作，将收到以下错误：`AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- 在编写 ASP.NET 或者 ASP.NET Core 应用程序时，如果你未告知框架授权代码已经兑换，则可能会发生这种情况。 为此，需要调用 `AuthorizationCodeReceived` 事件处理程序的 `context.HandleCodeRedemption()` 方法。

- 避免与 ASP.NET 共享访问令牌，这可能会阻止增量许可的正常运行。 有关详细信息，请参阅[问题 #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693)。

## <a name="on-behalf-of"></a>代理

MSAL 支持 [OAuth 2 代理身份验证流](v2-oauth2-on-behalf-of-flow.md)。  当应用程序调用服务或 web API 时使用此流，而服务或 web API 又需要调用另一个服务或 web API。 思路是通过请求链传播委托用户标识和权限。 要使中间层服务向下游服务发出身份验证请求，该服务需要代表用户保护 Microsoft 标识平台提供的访问令牌。

![代理流的示意图](media/msal-authentication-flows/on-behalf-of.png)

从上图可以看出：

1. 应用程序获取 Web API 的访问令牌。
2. 客户端（web、桌面、移动或单页应用程序）调用受保护的 web API，将访问令牌作为持有者令牌添加到 HTTP 请求的身份验证标头中。 Web API 对用户进行身份验证。
3. 当客户端调用 web API 时，Web API 代表用户请求另一个令牌。  
4. 受保护的 Web API 使用此令牌代表用户调用下游 Web API。  web API 还可以稍后为其他下游 API 请求令牌（但仍然代表同一用户）。

## <a name="client-credentials"></a>客户端凭据

MSAL 支持 [OAuth 2 客户端凭据流](v2-oauth2-client-creds-grant-flow.md)。 该流允许使用应用程序的标识访问 web 托管的资源。 这种授予通常用于必须在后台运行的服务器间交互，不需要立即与用户交互。 此类应用程序通常称为守护程序或服务帐户。 

客户端凭据授权流允许 Web 服务（机密客户端）在调用其他 Web 服务时使用它自己的凭据（而不是模拟用户）进行身份验证。 在这种情况下，客户端通常是中间层 Web 服务、后台程序服务或网站。 为了进行更高级别的保证，Microsoft 标识平台还允许调用服务将证书（而不是共享机密）用作凭据。

> [!NOTE]
> 机密客户端流在移动平台（UWP、Xamarin.iOS 和 Xamarin.Android）上不可用，因为它们只支持公共客户端应用程序。 公共客户端应用程序不知道如何向标识提供者证明应用程序的标识。 通过部署证书，可以在 web 应用或 web API 后端上实现安全连接。

MSAL.NET 支持两种类型的客户端凭据。 需要在 Azure AD 中注册这些客户端凭据。 凭据将传入代码中机密客户端应用程序的构造函数。

### <a name="application-secrets"></a>应用程序密钥

![具有密码的机密客户端示意图](media/msal-authentication-flows/confidential-client-password.png)

在上图中，应用程序：

1. 使用应用程序机密或密码凭据获取令牌。
2. 使用令牌发出资源请求。

### <a name="certificates"></a>证书

![具有证书的机密客户端示意图](media/msal-authentication-flows/confidential-client-certificate.png)

在上图中，应用程序：

1. 通过使用证书凭据获取令牌。
2. 使用令牌发出资源请求。

这些客户端凭据需要：
- 注册到 Azure AD。
- 传入代码中机密客户端应用程序的构造函数。

## <a name="device-code"></a>设备代码

MSAL 支持 [OAuth 2 设备代码流](v2-oauth2-device-code.md)，可让用户登录到智能电视、IoT 设备或打印机等输入受限的设备。 使用 Azure AD 进行交互式身份验证需要 Web 浏览器。 设备代码流允许用户使用另一个设备（例如，另一台计算机或移动电话）以交互方式登录，而设备或操作系统不提供 web 浏览器。

通过使用设备代码流，应用程序将通过专为这些设备或操作系统设计的两个步骤获取令牌。 在 IoT 设备或命令行工具 (CLI) 上运行的应用程序便属于这类应用程序。 

![设备代码流示意图](media/msal-authentication-flows/device-code.png)

从上图可以看出：

1. 每当需要用户进行身份验证时，应用程序都会提供一个代码，并要求用户使用另一个设备（如连接 Internet 的智能手机）转到 URL（如 `https://microsoft.com/devicelogin`）。 然后系统会提示用户输入代码和继续执行正常的身份验证操作，包括同意提示和[多重身份验证](../authentication/concept-mfa-howitworks.md)（如果需要）。

2. 身份验证成功后，命令行应用通过后端通道接收所需的令牌，并使用它们来执行所需的 Web API 调用。

### <a name="constraints"></a>约束

- 设备代码流仅在公共客户端应用程序上可用。
- 构造公共客户端应用程序时传入的机构必须是以下其中一个：
  - 格式为 `https://login.microsoftonline.com/{tenant}/` 的租户，其中 `{tenant}` 为表示租户 ID 的 GUID 或与租户关联的域。
  - 对于工作和学校帐户 (`https://login.microsoftonline.com/organizations/`)。
- Azure AD v2.0 终结点尚不支持 Microsoft 个人帐户（不能使用 `/common` 或 `/consumers` 租户）。

## <a name="integrated-windows-authentication"></a>Windows 集成身份验证

对于在加入域或 Azure AD 的 Windows 计算机上运行的桌面应用程序或移动应用程序，MSAL 支持集成 Windows 身份验证 (IWA)。 使用 IWA，这些应用程序可以以无提示的方式获取令牌（无需用户进行任何 UI 交互）。 

![集成 Windows 身份验证的示意图](media/msal-authentication-flows/integrated-windows-authentication.png)

在上图中，应用程序：

1. 通过使用集成 Windows 身份验证获取令牌。
2. 使用令牌发出资源请求。

### <a name="constraints"></a>约束

IWA 只支持联合用户，即在 Active Directory 中创建并由 Azure AD 支持的用户。 直接在 Azure AD 中创建但不受 Active Directory 支持的用户（托管用户）不能使用此身份验证流。 此限制不会影响[用户名和密码流](#usernamepassword)。

IWA 适用于针对 .NET Framework、.NET Core 和通用 Windows 平台等平台编写的应用。

IWA 不会绕过多重身份验证。 如果配置了多重身份验证，则如果需要多重身份验证质询，IWA 可能会失败。 多重身份验证需要用户交互。

你无法控制标识提供者何时请求执行双因素身份验证。 租户管理员可以对此进行控制。 通常，在以下情况下需要双因素身份验证：当你从不同国家/地区登录时；未通过 VPN 连接到公司网络时；有时甚至通过 VPN 连接也会需要双因素身份验证。 Azure AD 使用 AI 来持续了解是否需要双因素身份验证。 如果 IWA 失败，则应该返回查看[交互用户提示] (#interactive)。

构造公共客户端应用程序时传入的机构必须是以下其中一个：
- 格式为 `https://login.microsoftonline.com/{tenant}/` 的租户，其中 `tenant` 为表示租户 ID 的 GUID 或与租户关联的域。
- 对于工作和学校帐户 (`https://login.microsoftonline.com/organizations/`)。 不支持 Microsoft 个人帐户（不能使用 `/common` 或 `/consumers` 租户）。

由于 IWA 是无提示流，因此必须满足以下条件之一：
- 应用程序的用户必须事先同意使用该应用程序。 
- 或者，租户管理员必须事先同意租户中的所有用户使用该应用程序。

这意味着下列情况之一存在：
- 作为开发人员，你已经在 Azure 门户上为自己选择了“授权”。
- 租户管理员在该应用程序注册的“API 权限”选项卡上选择了“为租户域授权/撤消管理员同意”（请参阅[添加对 Web API 的访问权限](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)） 。
- 提供了一种让用户同意使用应用程序的方法（请参阅[请求单个用户的同意](v2-permissions-and-consent.md#requesting-individual-user-consent)）。
- 你为租户管理员提供了一种同意使用应用程序的方式（请参阅[管理员同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)）。

已为 .NET 桌面应用、.NET Core 应用和 Windows 通用平台应用启用 IWA 流。 在 .NET Core 上，必须向 IWA 提供用户名，因为 .NET Core 无法从操作系统获取用户名。
  
有关同意的详细信息，请参阅 [v2.0 权限和同意](v2-permissions-and-consent.md)。

## <a name="usernamepassword"></a>用户名/密码

MSA 标识平台支持 [OAuth 2.0 资源所有者密码凭据授予](v2-oauth-ropc.md)，后者允许应用程序通过直接处理用户的密码来登录用户。 在桌面应用程序中，可以使用用户名/密码流以无提示方式获取令牌。 使用应用程序时无需 UI。

![用户名/密码流示意图](media/msal-authentication-flows/username-password.png)

在上图中，应用程序：

1. 通过向标识提供者发送用户名和密码来获取令牌。
2. 使用令牌来调用 web API。

> [!WARNING]
> 不建议使用此流。 它需要高度的信任，具有更大的用户信息泄露风险。  仅当不能使用其他更安全的流时，才应使用此流。 有关详细信息，请参阅[如何解决日益增多的密码问题？](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。 

在已加入 Windows 域的计算机上以无提示方式获取令牌的首选流程是[集成的 Windows 身份验证](#integrated-windows-authentication)。 此外，你还可以使用[设备代码流](#device-code)。

虽然此流对某些情况（DevOps 方案）很有用，但是如果你想在自己提供 UI 的交互方案中使用用户名/密码，请尽量避免使用该流。 用户名/密码的使用注意事项：
- 需要进行多重身份验证的用户将无法登录（因为没有交互）。
- 用户无法进行单一登录。

### <a name="constraints"></a>约束

除了 [集成 Windows 身份验证约束](#integrated-windows-authentication)以外，还存在以下约束：

- 用户名/密码流与条件访问和多重身份验证不兼容。 因此，如果应用在租户管理员要求多重身份验证的 Azure AD 租户中运行，则无法使用此流。 许多组织都会要求多重身份验证。
- 它仅适用于工作和学校帐户（而不是 Microsoft 帐户）。
- 流在 .NET 桌面和 .NET Core 中可用，但在通用 Windows 平台上不可用。

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C 详细信息

有关在 MSAL.NET 和 Azure AD B2C 中使用 ROPC 的更多信息，请参阅[将 ROPC 与 Azure AD B2C 一起使用](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc)。
