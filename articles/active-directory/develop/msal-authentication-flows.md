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
ms.date: 07/08/2020
ms.author: marsma
ms.reviewer: saeeda
ms.openlocfilehash: 0dbd7eb9203f31b580f586a8a7d1fa216533628c
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170507"
---
# <a name="authentication-flows"></a>身份验证流

 (MSAL) 的 Microsoft 身份验证库支持多种身份验证流，以便在不同的应用程序方案中使用。

| 流 | 说明 | 适用范围 |
|--|--|--|
| [授权代码](#authorization-code) | 用于安装在设备上以访问受保护资源（如 web API）的应用中。 使你能够向你的移动和桌面应用程序添加登录和 API 访问权限。 | [桌面应用](scenario-desktop-overview.md)、[移动应用](scenario-mobile-overview.md)、[Web 应用](scenario-web-app-call-api-overview.md) |
| [客户端凭据](#client-credentials) | 允许你使用应用程序的标识访问 Web 托管的资源。 通常用于必须在后台运行的服务器间交互，不需要立即与用户交互。 | [守护程序应用](scenario-daemon-overview.md) |
| [设备代码](#device-code) | 允许用户登录到智能电视、IoT 设备或打印机等输入受限的设备。 | [桌面/移动应用](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [隐式授权](#implicit-grant) | 允许应用在不执行后端服务器凭据交换的情况下获取令牌。 使应用能够在客户端 JavaScript 代码中登录用户、维护会话并获取其他 web Api 的令牌。 | [单页应用程序 (SPA)](scenario-spa-overview.md) |
| [代理](#on-behalf-of) | 应用程序调用某个服务或 Web API，而后者又需要调用另一个服务或 Web API。 思路是通过请求链传播委托用户标识和权限。 | [Web API](scenario-web-api-call-api-overview.md) |
| [用户名/密码](#usernamepassword) | 允许应用程序通过直接处理用户密码来登录用户。 不建议使用此流。 | [桌面/移动应用](scenario-desktop-acquire-token.md#username-and-password) |
| [Windows 集成身份验证](#integrated-windows-authentication) | 允许已加入域或已加入 Azure Active Directory (Azure AD) 的计算机上的应用程序以静默方式获取令牌（无需用户进行任何 UI 交互）。 | [桌面/移动应用](scenario-desktop-acquire-token.md#integrated-windows-authentication) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>每个流如何发出令牌和代码

根据客户端应用程序的构建方式，它可以使用 Microsoft 标识平台支持的一个或多个身份验证流。 这些流可以生成多种类型的令牌和授权代码，并需要不同的令牌使其正常工作。

| 流                                                                               | 需要            | id_token | 访问令牌 | 刷新令牌 | 授权代码 |
|------------------------------------------------------------------------------------|:-------------------:|:--------:|:------------:|:-------------:|:------------------:|
| [授权代码流](v2-oauth2-auth-code-flow.md)                             |                     | x        | x            | x             | x                  |
| [客户端凭据](v2-oauth2-client-creds-grant-flow.md)                         |                     |          | x（仅限应用） |               |                    |
| [设备代码流](v2-oauth2-device-code.md)                                       |                     | x        | x            | x             |                    |
| [隐式流](v2-oauth2-implicit-grant-flow.md)                                  |                     | x        | x            |               |                    |
| [代理流](v2-oauth2-on-behalf-of-flow.md)                                | 访问令牌        | x        | x            | x             |                    |
| [用户名/密码](v2-oauth-ropc.md) (ROPC)                                        | 用户名 & 密码 | x        | x            | x             |                    |
| [混合 OIDC 流](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition) |                     | x        |              |               | x                  |
| [刷新令牌兑换](v2-oauth2-auth-code-flow.md#refresh-the-access-token)   | 刷新令牌       | x        | x            | x             |                    |

### <a name="interactive-and-non-interactive-authentication"></a>交互式和非交互式身份验证

其中的几个流同时支持交互式和非交互式标记获取。

  - **交互式**表示用户可以提示输入。 例如，提示用户登录、执行多重身份验证 (MFA) ，或向资源授予其他同意。
  - **非交互式**或*无提示*身份验证将尝试以登录服务器*无法*提示用户提供其他信息的方式获取令牌。

基于 MSAL 的应用程序应该首先尝试以*无提示*方式获取令牌，并仅在非交互式方法失败时以交互方式交互。 有关此模式的详细信息，请参阅[使用 Microsoft 身份验证库获取和缓存令牌 (MSAL) ](msal-acquire-cache-tokens.md)。

## <a name="authorization-code"></a>授权代码

[OAuth 2 授权代码授予](v2-oauth2-auth-code-flow.md)可用于设备上安装的应用，以获取对受保护资源（如 web api）的访问权限。 这样，就可以添加对移动应用和桌面应用的登录与 API 访问权限。

当用户登录到 Web 应用程序（网站）时，Web 应用程序会收到授权代码。 授权代码会被兑换为获取用于调用 web API 的令牌。

![授权代码流示意图](media/msal-authentication-flows/authorization-code.png)

在上图中，应用程序：

1. 请求授权代码，该代码将兑换为访问令牌。
2. 使用访问令牌调用 Web API。

### <a name="considerations"></a>注意事项

- 只能使用授权代码兑换令牌一次。 不要尝试使用相同的授权代码多次获取令牌，因为协议标准规范明确禁止了该令牌。 如果你有意或因为你不知道某个框架还能为你执行此代码，你会收到以下错误：

    `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

## <a name="client-credentials"></a>客户端凭据

使用[OAuth 2 客户端凭据流](v2-oauth2-client-creds-grant-flow.md)，可以通过使用应用程序的标识访问 web 承载的资源。 这种授予通常用于必须在后台运行的服务器间交互，不需要立即与用户交互。 此类应用程序通常称为守护程序或服务帐户。

客户端凭据授权流允许 Web 服务（机密客户端）在调用其他 Web 服务时使用它自己的凭据（而不是模拟用户）进行身份验证。 在这种情况下，客户端通常是中间层 Web 服务、后台程序服务或网站。 为了进行更高级别的保证，Microsoft 标识平台还允许调用服务将证书（而不是共享机密）用作凭据。

> [!NOTE]
> 由于 UWP、Xamarin 和 Xamarin 等移动平台上无法使用机密客户端流，因为它们仅支持公共客户端应用程序。 公共客户端应用程序不知道如何向标识提供程序证明应用程序的身份。 可以通过部署证书在 Web 应用或 Web API 后端上实现安全连接。

### <a name="application-secrets"></a>应用程序密钥

![使用密码的机密客户端示意图](media/msal-authentication-flows/confidential-client-password.png)

在上图中，应用程序：

1. 使用应用程序机密或密码凭据获取令牌。
2. 使用令牌发出资源请求。

### <a name="certificates"></a>证书

![使用证书的机密客户端示意图](media/msal-authentication-flows/confidential-client-certificate.png)

在上图中，应用程序：

1. 使用证书凭据获取令牌。
2. 使用令牌发出资源请求。

这些客户端凭据需要：

- 注册到 Azure AD。
- 在代码中构造机密客户端应用程序对象时传入。

## <a name="device-code"></a>设备代码

[OAuth 2 设备代码流](v2-oauth2-device-code.md)允许用户登录到受输入约束的设备，如智能电视、IoT 设备和打印机。 使用 Azure AD 进行交互式身份验证需要 Web 浏览器。 如果设备或操作系统未提供 web 浏览器，则设备代码流允许用户使用其他设备（如计算机或移动电话）以交互方式登录。

通过使用设备代码流，应用程序将通过为这些设备和操作系统设计的两步过程获取令牌。 此类应用程序的示例包括 IoT 设备上运行的应用程序和命令行界面 (CLI) 工具。

![设备代码流示意图](media/msal-authentication-flows/device-code.png)

在上图中：

1. 只要需要用户身份验证，应用程序就会提供代码，并要求用户使用其他设备（如连接 internet 的智能手机）访问 URL (例如 `https://microsoft.com/devicelogin`) 。 然后，将提示用户输入代码，并在必要时通过一般的身份验证体验（包括同意提示和[多重身份验证](../authentication/concept-mfa-howitworks.md)）进行操作。
1. 身份验证成功后，命令行应用通过后端通道接收所需的令牌，并使用它们来执行所需的 Web API 调用。

### <a name="constraints"></a>约束

- 设备代码流仅在公共客户端应用程序中可用。
- 构造公共客户端应用程序时传入的颁发机构必须是下列其中一项：
  - 租户，格式为， `https://login.microsoftonline.com/{tenant}/,` 其中 `{tenant}` 是表示租户 ID 的 GUID 或与租户关联的域名。
  - 对于窗体中的工作和学校帐户 `https://login.microsoftonline.com/organizations/` 。

## <a name="implicit-grant"></a>隐式授权

[OAuth 2 隐式授权](v2-oauth2-implicit-grant-flow.md)流允许应用从 Microsoft 标识平台获取令牌，而无需执行后端服务器凭据交换。 此流允许应用在客户端 JavaScript 代码中登录用户、维护会话并获取其他 web Api 的令牌。

![隐式授权流示意图](media/msal-authentication-flows/implicit-grant.svg)

许多新式 web 应用程序构建为客户端、单个页面应用程序 (SPA) 使用 JavaScript 或 SPA 框架编写，如角度、Vue.js 和 React.js。 这些应用程序在 Web 浏览器中运行，与传统的服务器端 Web 应用程序相比，它们具有不同的身份验证特征。 Microsoft 标识平台可让单页应用程序使用隐式授权流将用户登录，并获取用于访问后端服务或 Web API 的令牌。 隐式流允许应用程序获取 ID 令牌来表示已经过身份验证的用户以及调用受保护 API 所需的访问令牌。

此身份验证流不包括使用跨平台 JavaScript 框架（如 Electron 或响应本机）的应用程序方案，因为它们需要更多的功能来与本机平台交互。

通过隐式流模式颁发的令牌具有**长度限制**，因为它们按 URL 返回到浏览器 (其中 `response_mode` ， `query` 或 `fragment`) 。 某些浏览器会限制浏览器栏中 URL 的长度，并且当过长时，它会失败。 因此，这些隐式流标记不包含 `groups` 或 `wids` 声明。

## <a name="on-behalf-of"></a>代理

当应用程序调用服务或 web API，而该服务或 web API 又需要调用另一个服务或 web API 时，将使用[OAuth 2 代理身份验证流](v2-oauth2-on-behalf-of-flow.md)流。 思路是通过请求链传播委托用户标识和权限。 为了使中间层服务向下游服务发出身份验证请求，需要*代表*用户保护来自 Microsoft 标识平台的访问令牌。

![代理流示意图](media/msal-authentication-flows/on-behalf-of.png)

在上图中：

1. 应用程序获取 Web API 的访问令牌。
2. 客户端（Web、桌面、移动或单页应用程序）调用受保护的 Web API，在 HTTP 请求的身份验证标头中添加访问令牌作为持有者令牌。 Web API 对用户进行身份验证。
3. 当客户端调用 Web API 时，Web API 代表用户请求另一个令牌。
4. 受保护的 Web API 使用此令牌代表用户调用下游 Web API。 以后，Web API 也可以请求其他下游 API 的令牌（仍代表同一用户）。

## <a name="usernamepassword"></a>用户名/密码

[OAuth 2 资源所有者密码凭据](v2-oauth-ropc.md) (ROPC) grant 允许应用程序通过直接处理密码来登录用户。 在桌面应用程序中，可以使用用户名/密码流以静默方式获取令牌。 使用应用程序时无需 UI。

![用户名/密码流示意图](media/msal-authentication-flows/username-password.png)

在上图中，应用程序：

1. 通过向标识提供者发送用户名和密码来获取令牌。
2. 使用该令牌调用 Web API。

> [!WARNING]
> 不建议使用此流。 它需要高度的信任和凭据。 仅当不能使用更安全的流时，*才*应使用此流。 有关详细信息，请参阅[如何解决不断增多的密码问题？](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。

在已加入 Windows 域的计算机上以静默方式获取令牌的首选流是 [Windows 集成身份验证](#integrated-windows-authentication)。 在其他情况下，请使用[设备代码流](#device-code)。

尽管在某些情况下，用户名/密码流可能有用，但如果您希望在提供自己的 UI 的交互式方案中使用用户名/密码，则应避免使用用户名/密码。

用户名/密码的使用注意事项：

- 需要执行多重身份验证的用户无法登录，因为没有交互。
- 用户无法进行单一登录。

### <a name="constraints"></a>约束

除了 [集成 Windows 身份验证约束](#integrated-windows-authentication)以外，还存在以下约束：

- 用户名/密码流与条件访问和多重身份验证不兼容。 因此，如果应用在租户管理员要求多重身份验证的 Azure AD 租户中运行，则无法使用此流。 许多组织都要求进行多重身份验证。
- ROPC 仅适用于工作和学校帐户。 不能将 ROPC 用于 Microsoft (MSA) 的帐户。
- 可在 .NET Desktop 和 .NET Core 中使用该流，但不能在通用 Windows 平台中使用。
- 在 Azure AD B2C 中，ROPC 流仅适用于本地帐户。 有关 MSAL.NET 和 Azure AD B2C 中的 ROPC 的信息，请参阅将[ROPC 与 Azure AD B2C 配合使用](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc)。

## <a name="integrated-windows-authentication"></a>Windows 集成身份验证

对于在加入域或 Azure AD 加入 Windows 计算机上运行的桌面和移动应用程序，MSAL 支持集成的 Windows 身份验证 (IWA) 。 使用 IWA，这些应用程序可以无提示地获取令牌，而无需用户进行 UI 交互。

![Windows 集成身份验证示意图](media/msal-authentication-flows/integrated-windows-authentication.png)

在上图中，应用程序：

1. 使用 Windows 集成身份验证获取令牌。
2. 使用令牌发出资源请求。

### <a name="constraints"></a>约束

集成的 Windows 身份验证 (IWA) *仅*支持联合用户-在 Active Directory 中创建的用户和 Azure AD 支持的用户。 直接在 Azure AD 中创建的用户无需 Active Directory 支持 (托管用户) 不能使用此身份验证流。 此项限制不影响[用户名/密码流](#usernamepassword)。

IWA 适用于 .NET Framework、.NET Core 和通用 Windows 平台应用程序。

IWA 不会绕过多重身份验证。 如果配置了多重身份验证，需要多重身份验证质询时，IWA 可能会失败。 多重身份验证需要用户交互。

你无法控制标识提供者何时请求执行双重身份验证， 租户管理员可以对此进行控制。 通常，在以下情况下需要双因素身份验证：当你从不同国家/地区登录时；未通过 VPN 连接到公司网络时；有时甚至通过 VPN 连接也会需要双因素身份验证。 Azure AD 使用 AI 来持续了解是否需要双重身份验证。 如果 IWA 失败，你将回退到[交互式用户提示](#interactive-and-non-interactive-authentication)。

构造公用客户端应用程序时传入的授权必须是以下其中之一：

- 租户，格式为， `https://login.microsoftonline.com/{tenant}/,` 其中 `{tenant}` 是表示租户 ID 的 GUID 或与租户关联的域名。
- 适用于任何工作和学校帐户 (`https://login.microsoftonline.com/organizations/`)。 Microsoft 个人帐户 (MSA) 不受支持;不能使用 `/common` 或 `/consumers` 租户。

由于 IWA 是一个静默流，因此必须符合以下条件之一：

- 应用程序的用户必须事先同意使用该应用程序。
- 租户管理员必须已事先许可租户中的所有用户使用该应用程序。

这意味着需要符合以下条件之一：

- 作为开发人员，你已经在 Azure 门户中选择了 "**授予**"。
- 租户管理员已在 Azure 门户的应用注册的 " **API 权限**" 选项卡中选择了 "**授予/撤消管理员许可**" (请参阅[添加访问 web api](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)) 的权限。
- 你为用户提供了同意应用程序的方式;请参阅[请求单个用户同意](v2-permissions-and-consent.md#requesting-individual-user-consent)。
- 你为租户管理员提供了向应用程序授予许可的方式;请参阅[管理员同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)。

已为 .NET 桌面应用、.NET Core 应用和 Windows 通用平台应用启用 IWA 流。 在 .NET Core 上，必须向 IWA 提供用户名，因为 .NET Core 无法从操作系统获取用户名。

有关同意的详细信息，请参阅 [v2.0 权限和同意](v2-permissions-and-consent.md)。

## <a name="next-steps"></a>后续步骤

现在，你已查看 Microsoft 身份验证库支持的身份验证流 (MSAL) ，了解如何获取和缓存这些流中使用的令牌：

[使用 Microsoft 身份验证库 (MSAL) 获取和缓存令牌](msal-acquire-cache-tokens.md)
