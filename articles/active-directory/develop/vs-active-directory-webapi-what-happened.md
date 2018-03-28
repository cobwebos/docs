---
title: 连接到 Azure AD 时对 WebAPI 项目所做的更改 | Microsoft Docs
description: 介绍使用 Visual Studio 连接到 Azure AD 时，WebAPI 项目会发生什么情况
services: active-directory
documentationcenter: ''
author: kraigb
manager: ghogen
editor: ''
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 140f555d28c4d5a923b9c255d8e61d7aea9bb23f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>我的 WebAPI 项目（Visual Studio Azure Active Directory 连接服务）发生了什么情况

> [!div class="op_single_selector"]
> - [入门](vs-active-directory-webapi-getting-started.md)
> - [发生了什么情况](vs-active-directory-webapi-what-happened.md)

本文介绍添加[使用 Visual Studio 的 Azure Active Directory 连接服务](vs-active-directory-add-connected-service.md)时，对 ASP.NET WebAPI、ASP.NET 单页应用程序和 ASP.NET Azure API 项目所做的具体更改。 也适用于 Visual Studio 2015 中的 ASP.NET Azure 移动服务项目。

有关使用连接服务的信息，请参阅[入门](vs-active-directory-webapi-getting-started.md)。

## <a name="added-references"></a>已添加引用

影响项目文件 *.NET 引用）和 `packages.config`（NuGet 引用）。

| Type | 引用 |
| --- | --- |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet | Microsoft.Owin.Security.Jwt |
| .NET; NuGet | Microsoft.Owin.Security.OAuth |
| .NET; NuGet | Owin |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |

选择了“读取目录数据”选项时的其他引用：

| Type | 引用 |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer（仅限 Visual Studio 2015） |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>（仅限 Visual Studio 2015） |
| .NET; NuGet | System.Spatial |

删除了以下引用（仅限 ASP.NET 4 项目，如 Visual Studio 2015 中所示）：

| Type | 引用 |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>项目文件更改

- 将属性 `IISExpressSSLPort` 设置为不同的数字。
- 将属性 `WebProject_DirectoryAccessLevelKey` 设置为 0 或 1（如果选择了“读取目录数据”选项）。
- 将属性 `IISUrl` 设置为 `https://localhost:<port>/`，其中 `<port>` 匹配 `IISExpressSSLPort` 值。

## <a name="webconfig-or-appconfig-changes"></a>web.config 或 app.config 发生更改

- 添加了以下配置条目：

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- 仅限 Visual Studio 2017：还在 `<appSettings>` 下添加了以下条目

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- 在 `System.IdentityModel.Tokens.Jwt` 的 `<runtime><assemblyBinding>` 节点下添加了 `<dependentAssembly>` 元素。

- 如果选择了“读取目录数据”选项，在 `<appSettings>` 下添加了以下配置条目：

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>代码更改和添加

- 向 `Controllers/ValueController.cs` 和任何其他现有控制器添加了 `[Authorize]` 属性。

- 添加了身份验证启动类 `App_Start/Startup.Auth.cs`（其中包含 Azure AD 身份验证的启动逻辑）或相应地对其进行了修改。 如果选择了“读取目录数据”选项，则此文件还包含用于接收 OAuth 代码以及用 OAuth 代码交换访问令牌的代码。

- （仅限带 ASP.NET 4 应用的 Visual Studio 2015）删除了 `App_Start/IdentityConfig.cs`，并添加了 `Controllers/AccountController.cs`、`Models/IdentityModel.cs` 和 `Providers/ApplicationAuthProvider.cs`。

- 添加了 `Connected Services/AzureAD/ConnectedService.json`（Visual Studio 2017）或 `Service References/Azure AD/ConnectedService.json`（Visual Studio 2015），其中包含 Visual Studio 用来跟踪连接服务添加的信息。

### <a name="file-backup-visual-studio-2015"></a>文件备份 (Visual Studio 2015)

添加连接服务时，Visual Studio 2015 备份已更改并已删除文件。 所有受影响的文件均保存在文件夹 `Backup/AzureAD` 中。 Visual Studio 2017 不会创建备份。

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>对 Azure 的更改

- 在添加连接服务时选择的域中创建了 Azure AD 应用程序。
- 更新了应用，以便在选择了“读取目录数据”选项时包含“读取目录数据”权限。

[详细了解 Azure Active Directory](https://azure.microsoft.com/services/active-directory/)。

## <a name="next-steps"></a>后续步骤

- [Azure Active Directory 的身份验证方案](active-directory-authentication-scenarios.md)
- [向 ASP.NET Web 应用添加 Microsoft 登录功能](guidedsetups/active-directory-aspnetwebapp-v1.md)