---
title: Microsoft 标识 Web 身份验证库概述
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 标识 Web、用于与 Azure Active Directory、Azure AD B2C 和 Microsoft Graph 以及其他 Web Api 集成的 ASP.NET Core 应用程序的身份验证和授权库。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/09/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4d915cee962546e355ee9d53c683581730bbbf36
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91778843"
---
# <a name="microsoft-identity-web-authentication-library"></a>Microsoft 标识 Web 身份验证库

Microsoft 标识 Web 是一组 ASP.NET Core 库，可简化向与 Microsoft 标识平台集成的 web 应用程序和 web Api 添加身份验证和授权支持。 它提供单个 surface API 便利性层，该层将 ASP.NET Core、其身份验证中间件和 [Microsoft 身份验证库 (MSAL) 用于 .net](https://github.com/azuread/microsoft-authentication-library-for-dotnet)。

## <a name="supported-application-scenarios"></a>支持的应用程序方案

如果要构建 ASP.NET Core web 应用或 web Api，并希望使用 Azure Active Directory (Azure AD) 或 Azure AD B2C 用于标识和访问管理 (IAM) ，则建议在所有这些情况下使用 Microsoft 标识 Web：

- [用于登录用户的 Web 应用](scenario-web-app-sign-user-overview.md)
- [用于登录用户并代表用户调用 web API 的 web 应用](scenario-web-app-call-api-overview.md)
- [仅经过身份验证的用户可以访问的受保护 web API](scenario-protected-web-api-overview.md)
- [受保护的 web API，它代表登录用户调用另一个 (下游) web API](scenario-web-api-call-api-overview.md)

## <a name="get-the-library"></a>获取该库

你可以从 [NuGet](#nuget)、 [.net Core 项目模板](#project-templates)和 [GitHub](#github)获取 Microsoft 标识 Web。

#### <a name="nuget"></a>NuGet

Microsoft 标识 Web 在 NuGet 上以一组包的形式提供，这些包提供基于你的应用需要的模块化功能。 使用 .NET CLI 的 `dotnet add` 命令或 Visual Studio 的 **NuGet 包管理器** 安装适合你的项目的包：

- " [Web](https://www.nuget.org/packages/Microsoft.Identity.Web) "-主包。 使用 Microsoft 标识 Web 的所有应用程序所必需的。
- [（可选）。](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) 添加用户登录和注销的用户界面，以及用于 web 应用的关联控制器。
- [Microsoft.azure.webjobs.extensions.microsoftgraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) -可选。 提供与 Microsoft Graph API 的简化交互。

#### <a name="project-templates"></a>项目模板

Microsoft 标识 Web 项目模板包含在 .NET 5.0 中，可用于下载 ASP.NET Core 3.1 项目。

如果使用的是 ASP.NET Core 3.1，请使用 .NET CLI 安装模板：

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::1.0.0
```

下图显示了受支持的应用类型及其相关参数的概要视图：

:::image type="content" source="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" lightbox="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" alt-text="Microsoft 标识 Web 的可用点网络 CLI 项目模板图示&quot;:::
<br /><sup><b>*</b></sup>`MultiOrg`不支持 `webapi2` ，但可通过将租户设置为或在*appsettings.js*中启用 `common``organizations`
<br /><sup><b>**</b></sup>`--calls-graph`Azure AD B2C 不支持

本 [Blazor server 教程](tutorial-blazor-server.md)中的此示例 .net CLI 命令生成一个新的 Blazor 服务器项目，其中包含正确的包和起始代码 (占位符值) ：

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph --client-id &quot;00000000-0000-0000-0000-000000000000&quot; --tenant-id &quot;11111111-1111-1111-1111-111111111111" --output my-blazor-app
```

#### <a name="github"></a>GitHub

Microsoft 标识 Web 是在 GitHub 上托管的开放源代码项目： [AzureAD/microsoft 标识-Web](https://github.com/AzureAD/microsoft-identity-web?azure-portal=true)

[存储库 wiki](https://github.com/AzureAD/microsoft-identity-web/wiki)包含其他文档，如果需要帮助或发现 bug，则可以提交[问题](https://github.com/AzureAD/microsoft-identity-web/issues)。

## <a name="features"></a>功能

如果使用默认的 ASP.NET 3.1 项目模板，Microsoft 标识 Web 包括不提供的多项功能。

| 功能                                                                                  | ASP.NET Core 3.1                                                     | Microsoft 标识 Web                                                                                  |
|------------------------------------------------------------------------------------------|----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| 在 web 应用中[登录用户](scenario-web-app-sign-user-app-configuration.md)             | <li>工作或学校帐户<li>社交标识与 Azure AD B2C 的 ()  | <li>工作或学校帐户<li>Microsoft 个人帐户<li>社交标识与 Azure AD B2C 的 ()      |
| [保护 web Api](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) | <li>工作或学校帐户<li>社交标识与 Azure AD B2C 的 ()  | <li>工作或学校帐户<li>Microsoft 个人帐户<li>社交标识与 Azure AD B2C 的 ()      |
| 多租户应用中的颁发者验证                                                   | 否                                                                   | 是，适用于 [所有云和](authentication-national-cloud.md) [Azure AD B2C](/azure/active-directory-b2c) |
| Web 应用/API [调用 Microsoft graph] [方案-API 调用-graph]                             | 否                                                                   | 是                                                                                                     |
| Web 应用/API [调用 web API] [方案 api 调用 api]                                       | 否                                                                   | 是                                                                                                     |
| 支持证书凭据                                                         | 否                                                                   | 是，包括 Azure Key Vault                                                                          |
| Web 应用中的增量许可和条件访问支持                           | 否                                                                   | 是，在 MVC、Razor 页面和 Blazor 中                                                                    |
| Web Api 中的令牌加密证书                                                | 否                                                                   | 是                                                                                                     |
| [范围/应用角色验证]web Api 中的 [方案-验证]                        | 否                                                                   | 是                                                                                                     |
| `WWW-Authenticate` web Api 中的标头生成                                         | 否                                                                   | 是                                                                                                     |

## <a name="next-steps"></a>后续步骤

若要在操作中查看 Microsoft 标识 Web，请尝试 Blazor Server 教程：

[教程：创建使用 Microsoft 标识平台进行身份验证的 Blazor 服务器应用](tutorial-blazor-server.md)

GitHub 上的 Microsoft 标识 Web wiki 包含有关库各个方面的广泛参考文档。 例如，可以在此处找到证书使用情况、增量许可和条件访问参考：

- 将[证书与 web.config (GitHub 一起使用](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates?azure-portal=true)) 
- GitHub)  ([增量许可和条件性访问](https://github.com/AzureAD/microsoft-identity-web/wiki/Managing-incremental-consent-and-conditional-access?azure-portal=true)

<!-- LINKS -->
<!--  [miw-certs]: microsoft-identity-web-certificates.md  -->
<!--  [miw-certs-decrypt]: microsoft-identity-web-certificates.md#decryption-certificates  -->
<!--  [miw-inc-consent-ca-header]: microsoft-identity-web-consent-conditional-access.md#handling-incremental-consent-or-conditional-access-in-web-apis  -->
<!--  [miw-inc-consent-ca]: microsoft-identity-web-consent-conditional-access.md  -->
[方案 api 调用-api]：方案-web api 调用 api。 md # 选项-1-调用---sdk [方案 api 调用---------------------------------------------------------------scenario-protected-web-api-verification-scope-app-roles.md
