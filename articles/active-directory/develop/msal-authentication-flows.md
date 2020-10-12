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
ms.openlocfilehash: 4a902ed53e92cd073d81626e80bdb3c8629ad072
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89437864"
---
# <a name="authentication-flows"></a>身份验证流

Microsoft 身份验证库 (MSAL) 支持在不同的应用程序场景中使用多种身份验证流。

| 流向 | 说明 | 适用范围 |
|--|--|--|
| [授权代码](#authorization-code) | 在设备上安装的应用中使用，以访问受保护的资源，例如 Web API。 使你能够添加对移动应用和桌面应用的登录与 API 访问权限。 | [桌面应用](scenario-desktop-overview.md)、[移动应用](scenario-mobile-overview.md)、[Web 应用](scenario-web-app-call-api-overview.md) |
| [客户端凭据](#client-credentials) | 允许你使用应用程序的标识访问 Web 托管的资源。 通常用于必须在后台运行的服务器间交互，不需要立即与用户交互。 | [守护程序应用](scenario-daemon-overview.md) |
| [设备代码](#device-code) | 允许用户登录到智能电视、IoT 设备或打印机等输入受限的设备。 | [桌面/移动应用](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [隐式授权](#implicit-grant) | 允许应用在不执行后端服务器凭据交换的情况下获取令牌。 使应用能够让用户登录、维护会话，并获取客户端 JavaScript 代码中所有其他 Web API 的令牌。 | [单页应用程序 (SPA)](scenario-spa-overview.md) |
| [代理](#on-behalf-of) | 应用程序调用某个服务或 Web API，而后者又需要调用另一个服务或 Web API。 思路是通过请求链传播委托用户标识和权限。 | [Web API](scenario-web-api-call-api-overview.md) |
| [用户名/密码](#usernamepassword) | 允许应用程序通过直接处理用户密码来登录用户。 不建议使用此流。 | [桌面/移动应用](scenario-desktop-acquire-token.md#username-and-password) |
| [Windows 集成身份验证](#integrated-windows-authentication) | 允许已加入域或已加入 Azure Active Directory (Azure AD) 的计算机上的应用程序以静默方式获取令牌（无需用户进行任何 UI 交互）。 | [桌面/移动应用](scenario-desktop-acquire-token.md#integrated-windows-authentication) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>每个流如何发出令牌和代码

客户端应用程序根据其生成方式的不同，可以使用 Microsoft 标识平台支持的一种或多种身份验证流。 这些流可以生成多种类型的令牌以及授权代码，并需要不同的令牌使其正常工作。

| 流向                                                                               | 需要            | id_token | 访问令牌 | 刷新令牌 | 授权代码 |
|------------------------------------------------------------------------------------|:-------------------:|:--------:|:------------:|:-------------:|:------------------:|
| [授权代码流](v2-oauth2-auth-code-flow.md)                             |                     | x        | x            | x             | x                  |
| [客户端凭据](v2-oauth2-client-creds-grant-flow.md)                         |                     |          | x（仅限应用） |               |                    |
| [设备代码流](v2-oauth2-device-code.md)                                       |                     | x        | x            | x             |                    |
| [隐式流](v2-oauth2-implicit-grant-flow.md)                                  |                     | x        | x            |               |                    |
| [代理流](v2-oauth2-on-behalf-of-flow.md)                                | 访问令牌        | x        | x            | x             |                    |
| [用户名/密码](v2-oauth-ropc.md) (ROPC)                                       | 用户名和密码 | x        | x            | x             |                    |
| [混合 OIDC 流](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition) |                     | x        |              |               | x                  |
| [刷新令牌兑换](v2-oauth2-auth-code-flow.md#refresh-the-access-token)   | 刷新令牌       | x        | x            | x             |                    |

### <a name="interactive-and-non-interactive-authentication"></a>交互式和非交互式身份验证

上面的其中一些流既支持交互式令牌获取，又支持非交互式令牌获取。

  - 交互式是指用户可以按提示输入信息。 例如，提示用户登录、执行多重身份验证 (MFA) 或向资源授予其他许可。
  - 非交互式（或静默式）身份验证尝试以登录服务器无法提示用户提供其他信息的方式获取令牌 。

基于 MSAL 的应用程序应该会先尝试以静默方式获取令牌，然后仅在非交互式方法失败时才采用交互方式。 若要详细了解此模式，请参阅[使用 Microsoft 身份验证库 (MSAL) 获取和缓存令牌](msal-acquire-cache-tokens.md)。

## <a name="authorization-code"></a>授权代码

[OAuth 2.0 授权代码授权](v2-oauth2-auth-code-flow.md)可用于设备上安装的应用，以获取受保护资源（如 Web API）的访问权限。 这样，就可以添加对移动应用和桌面应用的登录与 API 访问权限。

当用户登录到 Web 应用程序（网站）时，Web 应用程序会收到授权代码。 兑换该授权代码可获取用于调用 Web API 的令牌。

![授权代码流示意图](media/msal-authentication-flows/authorization-code.png)

在上图中，应用程序：

1. 请求授权代码，该代码将兑换为访问令牌。
2. 使用访问令牌调用 Web API。

### <a name="considerations"></a>注意事项

- 只能使用授权代码兑换令牌一次。 请勿尝试使用同一个授权代码多次获取令牌（因为协议标准规范明确禁止此行为）。 如果你多次用该代码兑换令牌（有意地或者由于你没有意识到框架也在为你兑换令牌），则会收到以下错误：

    `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

## <a name="client-credentials"></a>客户端凭据

利用 [OAuth 2 客户端凭据流](v2-oauth2-client-creds-grant-flow.md)，可通过使用应用程序的标识来访问 Web 托管资源。 这种授予通常用于必须在后台运行的服务器间交互，不需要立即与用户交互。 此类应用程序通常称为守护程序或服务帐户。

客户端凭据授权流允许 Web 服务（机密客户端）在调用其他 Web 服务时使用它自己的凭据（而不是模拟用户）进行身份验证。 在这种情况下，客户端通常是中间层 Web 服务、后台程序服务或网站。 为了进行更高级别的保证，Microsoft 标识平台还允许调用服务将证书（而不是共享机密）用作凭据。

> [!NOTE]
> 机密客户端流不适用于移动平台（如 UWP、Xamarin.iOS 和 Xamarin.Android），因为它们仅支持公共客户端应用程序。 公共客户端应用程序不知道如何向标识提供者证明应用程序的身份。 可以通过部署证书在 Web 应用或 Web API 后端上实现安全连接。

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

利用 [OAuth 2 设备代码流](v2-oauth2-device-code.md)，用户可以登录到输入受限的设备，如智能电视、IoT 设备和打印机。 使用 Azure AD 的交互式身份验证需要 Web 浏览器。 如果设备或操作系统不提供 Web 浏览器，设备代码流可让用户使用另一台设备（例如计算机或手机）以交互方式登录。

应用程序使用设备代码流通过为这些设备和操作系统设计的双步过程获取令牌。 此类应用程序的示例包括 IoT 设备上运行的应用程序和命令行接口 (CLI) 工具。

![设备代码流示意图](media/msal-authentication-flows/device-code.png)

在上图中：

1. 每当需要用户身份验证时，应用都将提供一个代码，并要求用户使用另一台设备（如已连接到 Internet 的智能手机）访问某个 URL（例如 `https://microsoft.com/devicelogin`）。 然后，系统会提示用户输入该代码，并转到完成正常身份验证的体验，包括许可提示和[多重身份验证](../authentication/concept-mfa-howitworks.md)（如果需要）。
1. 成功完成身份验证后，命令行应用会通过传回通道收到所需的令牌，并使用这些令牌执行所需的 Web API 调用。

### <a name="constraints"></a>约束

- 设备代码流仅在公共客户端应用程序中可用。
- 构造公共客户端应用程序时传入的颁发机构必须是下列其中一项：
  - 租户化（采用 `https://login.microsoftonline.com/{tenant}/,` 格式，其中，`{tenant}` 为表示租户 ID 的 GUID 或与该租户关联的域名）。
  - 对于工作和学校帐户，采用 `https://login.microsoftonline.com/organizations/` 格式。

## <a name="implicit-grant"></a>隐式授权

利用 [OAuth 2 隐式授权](v2-oauth2-implicit-grant-flow.md)流，应用无需执行后端服务器凭据交换即可从 Microsoft 标识平台获取令牌。 此流使应用能够让用户登录、维护会话，并获取客户端 JavaScript 代码中所有其他 Web API 的令牌。

![隐式授权流示意图](media/msal-authentication-flows/implicit-grant.svg)

许多新式 Web 应用程序都是使用 Angular、Vue.js 和 React.js 等 JavaScript 或 SPA 框架编写的客户端单页应用程序 (SPA)。 这些应用程序在 Web 浏览器中运行，与传统的服务器端 Web 应用程序相比，它们具有不同的身份验证特征。 Microsoft 标识平台可让单页应用程序使用隐式授权流将用户登录，并获取用于访问后端服务或 Web API 的令牌。 隐式流允许应用程序获取 ID 令牌来表示已经过身份验证的用户以及调用受保护 API 所需的访问令牌。

此身份验证流不包括使用 Electron 或 React-Native 之类的跨平台 JavaScript 框架的应用程序场景，因为它们需要使用其他功能才能与本机平台交互。

通过隐式流模式颁发的令牌具有长度限制，因为它们是通过 URL 返回给浏览器的（其中 `response_mode` 为 `query` 或 `fragment`）。 某些浏览器会限制浏览器栏中 URL 长度，如果 URL 过长则会失败。 因此，这些隐式流令牌不包含 `groups` 或 `wids` 声明。

## <a name="on-behalf-of"></a>代理

当应用程序调用服务或 Web API（该服务或 Web API 继而又需要调用另一个服务或 Web API）时，会使用 [OAuth 2 代理身份验证流](v2-oauth2-on-behalf-of-flow.md)。 思路是通过请求链传播委托用户标识和权限。 要使中间层服务向下游服务发出身份验证请求，该服务需要代表用户保护 Microsoft 标识平台提供的访问令牌。

![代理流示意图](media/msal-authentication-flows/on-behalf-of.png)

在上图中：

1. 应用程序获取 Web API 的访问令牌。
2. 客户端（Web、桌面、移动或单页应用程序）调用受保护的 Web API，在 HTTP 请求的身份验证标头中添加访问令牌作为持有者令牌。 Web API 对用户进行身份验证。
3. 当客户端调用 Web API 时，Web API 代表用户请求另一个令牌。
4. 受保护的 Web API 使用此令牌代表用户调用下游 Web API。 以后，Web API 也可以请求其他下游 API 的令牌（仍代表同一用户）。

## <a name="usernamepassword"></a>用户名/密码

利用 [OAuth 2 资源所有者密码凭据](v2-oauth-ropc.md) (ROPC) 授权，应用程序可以通过直接处理用户的密码来让用户登录。 在桌面应用程序中，可以使用用户名/密码流以静默方式获取令牌。 使用应用程序时无需 UI。

![用户名/密码流示意图](media/msal-authentication-flows/username-password.png)

在上图中，应用程序：

1. 通过向标识提供者发送用户名和密码来获取令牌。
2. 使用该令牌调用 Web API。

> [!WARNING]
> 不建议使用此流。 它需要较高级别的信任，并且会透露凭据信息。 仅当无法使用更安全的流时，才使用此流。 有关详细信息，请参阅[如何解决不断增多的密码问题？](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。

在已加入 Windows 域的计算机上以静默方式获取令牌的首选流是 [Windows 集成身份验证](#integrated-windows-authentication)。 在其他情况下，请使用[设备代码流](#device-code)。

尽管用户名/密码流在某些场景（如 DevOps）中可能有用，但如果要在交互式场景中（需要提供自己的 UI）使用用户名/密码，请避免使用它。

使用用户名/密码：

- 需要执行多重身份验证的用户将无法登录（因为没有交互）。
- 用户无法执行单一登录。

### <a name="constraints"></a>约束

除了 [集成 Windows 身份验证约束](#integrated-windows-authentication)以外，还存在以下约束：

- 用户名/密码流与条件访问和多重身份验证不兼容。 因此，如果应用在租户管理员要求多重身份验证的 Azure AD 租户中运行，则无法使用此流。 许多组织都会要求多重身份验证。
- ROPC 仅适用于工作帐户和学校帐户。 不能将 ROPC 用于 Microsoft (MSA) 的帐户。
- 可在 .NET Desktop 和 .NET Core 中使用该流，但不能在通用 Windows 平台中使用。
- 在 Azure AD B2C 中，ROPC 流仅适用于本地帐户。 若要了解 MSAL.NET 和 Azure AD B2C 中的 ROPC，请参阅[将 ROPC 与 Azure AD B2C 配合使用](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc)。

## <a name="integrated-windows-authentication"></a>Windows 集成身份验证

对于已加入域和已加入 Azure AD 的 Windows 计算机上运行的桌面或移动应用程序，MSAL 支持 Windows 集成身份验证 (IWA)。 这些应用程序可以使用 IWA 以静默方式获取令牌（无需要求用户进行任何 UI 交互）。

![Windows 集成身份验证示意图](media/msal-authentication-flows/integrated-windows-authentication.png)

在上图中，应用程序：

1. 使用 Windows 集成身份验证获取令牌。
2. 使用令牌发出资源请求。

### <a name="constraints"></a>约束

集成 Windows 身份验证 (IWA) 仅支持联合用户 - 在 Active Directory 中创建的并由 Azure AD 支持的用户。 直接在 Azure AD 中创建的但不是由 Active Directory 支持的用户（托管用户）不能使用此身份验证流。 此项限制不影响[用户名/密码流](#usernamepassword)。

IWA 适用于 .NET Framework、.NET Core 和通用 Windows 平台应用程序。

IWA 不会绕过多重身份验证。 如果配置了多重身份验证，需要多重身份验证质询时，IWA 可能会失败。 多重身份验证需要用户交互。

你无法控制标识提供者何时请求执行双重身份验证， 租户管理员可以对此进行控制。 通常，在以下情况下需要双因素身份验证：当你从不同国家/地区登录时；未通过 VPN 连接到公司网络时；有时甚至通过 VPN 连接也会需要双因素身份验证。 Azure AD 使用 AI 来持续了解是否需要双重身份验证。 如果 IWA 失败，你将回退到[交互式用户提示](#interactive-and-non-interactive-authentication)。

构造公共客户端应用程序时传入的颁发机构必须为以下其中一项：

- 租户化（采用 `https://login.microsoftonline.com/{tenant}/,` 格式，其中，`{tenant}` 为表示租户 ID 的 GUID 或与该租户关联的域名）。
- 适用于任何工作和学校帐户 (`https://login.microsoftonline.com/organizations/`)。 Microsoft 个人帐户 (MSA) 不受支持;不能使用 `/common` 或 `/consumers` 租户。

由于 IWA 是一个静默流，因此必须符合以下条件之一：

- 应用程序的用户必须已事先许可使用该应用程序。
- 租户管理员必须已事先许可租户中的所有用户使用该应用程序。

这意味着需要符合以下条件之一：

- 开发人员已在 Azure 门户中自行选择“授权”。
- 租户管理员已在 Azure 门户中的应用注册的“API 权限”选项卡中选择了“授予/撤销 {租户域} 的管理员许可”（请参阅[添加用于访问 Web API 的权限](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)） 。
- 你已提供某种方式让用户同意应用程序（请参阅[请求个人用户同意](v2-permissions-and-consent.md#requesting-individual-user-consent)）。
- 你已提供某种方式让租户管理员同意应用程序（请参阅[管理员同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)）。

已针对 .NET Desktop、.NET Core 和 Windows 通用平台应用启用 IWA 流。 在 .NET Core 上，必须向 IWA 提供用户名，因为 .NET Core 无法从操作系统获取用户名。

有关同意的详细信息，请参阅 [v2.0 权限和同意](v2-permissions-and-consent.md)。

## <a name="next-steps"></a>后续步骤

现在，你已经了解了 Microsoft 身份验证库 (MSAL) 支持的身份验证流，接下来了解有关获取和缓存在这些流中使用的令牌的信息：

[使用 Microsoft 身份验证库 (MSAL) 获取和缓存令牌](msal-acquire-cache-tokens.md)
