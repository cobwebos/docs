---
title: 生成一个与 Azure AD 集成以进行身份验证和授权的 .NET Web API | Microsoft Docs
description: 如何生成一个与 Azure AD 集成以进行身份验证和授权的 .NET MVC Web API。
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83f5b08e5fee17c0ea5577d4d56d4d3208a818e3
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625293"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>快速入门：生成一个与 Azure AD 集成以进行身份验证和授权的 .NET Web API

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

如果正在生成的应用程序会提供对受保护资源的访问，则需要知道如何防止有人在未经授权的情况下访问这些资源。 借助 Azure Active Directory (Azure AD)，只需编写几行代码，即可使用 OAuth 2.0 持有者访问令牌简单直接地保护 Web API。

在 ASP.NET Web 应用中，可以使用 .NET Framework 4.5 中包含的社区驱动 OWIN 中间件的 Microsoft 实现来完成保护。 现在，我们将使用 OWIN 来生成“待办事项列表”Web API：

* 指定要保护哪些 API。
* 验证 Web API 调用是否包含有效的访问令牌。

在此快速入门中，你将生成待办事项列表 API 并了解如何：

1. 将应用程序注册到 Azure AD。
2. 将应用设置为使用 OWIN 身份验证管道。
3. 配置一个客户端应用程序以调用 Web API。

## <a name="prerequisites"></a>先决条件

开始前，请完成这些先决条件：

* [下载应用框架](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip)或[下载已完成的示例](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip)。 每个下载项目都是 Visual Studio 2013 解决方案。
* 拥有一个用于注册应用程序的 Azure AD 租户。 如果还没有此租户，请[了解如何获取租户](quickstart-create-new-tenant.md)。

## <a name="step-1-register-an-application-with-azure-ad"></a>步骤 1：将应用程序注册到 Azure AD

要帮助保护应用程序，首先需要在租户中创建一个应用程序，并为 Azure AD 提供一些关键信息。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 通过以下方式选择 Azure AD 租户：在页面右上角选择你的帐户，选择“切换目录”导航，然后选择合适的租户  。
    * 如果你的帐户下只有一个 Azure AD 租户，或者已选择了合适的 Azure AD 租户，请跳过此步骤。

3. 在左侧导航窗格中，选择“Azure Active Directory”  。
4. 选择“应用注册”，然后选择“新建注册”   。
5. “注册应用程序”页显示后，请输入应用程序的名称。 
在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。  
6. 在“重定向 URI”  部分下选择  “Web”平台，并将值设置为 `https://localhost:44321/`（Azure AD 将令牌返回到的位置）。
7. 完成后，选择“注册”  。 在应用的“概述”页上，记下“应用程序(客户端) ID”值。  
6. 选择“公开 API”  ，然后通过单击“设置”来更新应用程序 ID URI。  输入租户特定的标识符。 例如，输入 `https://contoso.onmicrosoft.com/TodoListService`。
7. 保存配置。 让门户保持打开状态，因为稍后你还需要注册客户端应用程序。

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>步骤 2：将应用设置为使用 OWIN 身份验证管道

要验证传入的请求和令牌，需要将应用程序设置为与 Azure AD 通信。

1. 要开始，请打开解决方案，然后使用 Package Manager Console 将 OWIN 中间件 NuGet 包添加到 TodoListService 项目。

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. 将名为 `Startup.cs` 的 OWIN 启动类添加到 TodoListService 项目。  右键单击项目，选择“添加”>“新建项”，然后搜索“OWIN”   。 当应用程序启动时，该 OWIN 中间件将调用 `Configuration(…)` 方法。

3. 将类声明更改为 `public partial class Startup`。 我们已在另一个文件中实现了此类的一部分。 在 `Configuration(…)` 方法中，调用 `ConfigureAuth(…)` 以设置 Web 应用的身份验证。

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. 打开文件 `App_Start\Startup.Auth.cs` 并实现 `ConfigureAuth(…)` 方法。 在 `WindowsAzureActiveDirectoryBearerAuthenticationOptions` 中提供的参数将充当应用程序与 Azure AD 通信时使用的坐标。 若要使用它们，你需要使用 `System.IdentityModel.Tokens` 命名空间中的类。

    ```csharp
    using System.IdentityModel.Tokens;
    ```

    ```csharp
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                 Tenant = ConfigurationManager.AppSettings["ida:Tenant"],
                 TokenValidationParameters = new TokenValidationParameters
                 {
                    ValidAudience = ConfigurationManager.AppSettings["ida:Audience"]
                 }
            });
    }
    ```

5. 使用 `[Authorize]` 属性并结合 JSON Web 令牌 (JWT) 持有者身份验证来保护控制器和操作。 使用授权标记修饰 `Controllers\TodoListController.cs` 类，这会强制用户在访问该页面之前进行登录。

    ```csharp
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    如果已授权的调用方成功调用了某个 `TodoListController` API，该操作可能需要访问有关调用方的信息。 OWIN 通过 `ClaimsPrincpal` 对象提供对持有者令牌中的声明的访问。  

6. Web API 的一个常见要求是验证令牌中的“作用域”，以确保最终用户授予访问待办事项列表服务所需的权限。

    ```csharp
    public IEnumerable<TodoItem> Get()
    {
        // user_impersonation is the default permission exposed by applications in Azure AD
        if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
        {
            throw new HttpResponseException(new HttpResponseMessage {
              StatusCode = HttpStatusCode.Unauthorized,
              ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
            });
        }
        ...
    }
    ```

7. 打开位于 TodoListService 项目根目录中的 `web.config` 文件，并在 `<appSettings>` 节中输入配置值。
    * `ida:Tenant` 是 Azure AD 租户的名称 - 例如，contoso.onmicrosoft.com。
    * `ida:Audience` 是在 Azure 门户中输入的应用程序的应用 ID URI。

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>步骤 3：配置客户端应用程序并运行服务

需要先配置待办事项列表客户端，使它能够从 Azure AD 获取令牌并可调用服务，才能看到待办事项服务的运行情况。

1. 返回到 [Azure 门户](https://portal.azure.com)。
1. 在 Azure AD 租户中创建新的应用程序注册。  输入一个**名称**，用以向用户说明你的应用程序，对于“重定向 URI”值  ，请输入 `http://TodoListClient/`，并在下拉列表中选择“公共客户端(移动和桌面)”  。
1. 完成注册后，Azure AD 将向应用分配唯一应用程序 ID。 在后面的步骤中会用到此值，因此，请从应用程序页复制此值。
1. 选择“API 权限”  ，然后选择“添加权限”  。  找到并选择待办事项列表服务，在“委派的权限”下添加“user_impersonation Access TodoListService”权限，并选择“添加权限”    。
1. 在 Visual Studio 中，打开 TodoListClient 项目中的 `App.config`，然后在 `<appSettings>` 节中输入配置值。

    * `ida:Tenant` 是 Azure AD 租户的名称，例如，contoso.onmicrosoft.com。
    * `ida:ClientId` 是从 Azure 门户复制的应用 ID。
    * `todo:TodoListResourceId` 是在 Azure 门户中为待办事项列表服务应用程序输入的应用 ID URI。

1. 清理、生成并运行每个项目。
1. 可以使用 *.onmicrosoft.com 域在租户中创建一个新的用户（如果尚未这样做）。
1. 使用该用户登录到待办事项列表客户端，并向用户的待办事项列表添加一些任务。

## <a name="next-steps"></a>后续步骤

* 有关参考，请从 [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip) 下载已完成的示例（无配置值）。 现在，可以转到其他标识方案。
