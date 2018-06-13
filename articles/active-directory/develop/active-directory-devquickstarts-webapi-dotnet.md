---
title: Azure AD .NET Web API 入门 | Microsoft Docs
description: 如何生成一个与 Azure AD 集成以进行身份验证和授权的 .NET MVC Web API。
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: aa527f66035ce8ea95ecdf405ef307c1202a92a6
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158282"
---
# <a name="azure-ad-net-web-api-getting-started"></a>Azure AD .NET Web API 入门
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

如果正在生成的应用程序会提供对受保护资源的访问，则需要知道如何防止有人在未经授权的情况下访问这些资源。
借助 Azure Active Directory (Azure AD)，只需编写几行代码，即可使用 OAuth 2.0 持有者访问令牌简单直接地保护 Web API。

在 ASP.NET Web 应用中，可以使用 .NET Framework 4.5 中包含的社区驱动 OWIN 中间件的 Microsoft 实现来完成保护。 现在，我们将使用 OWIN 来生成“待办事项列表”Web API：

* 指定要保护哪些 API。
* 验证 Web API 调用是否包含有效的访问令牌。

若要生成到待办事项 API，首先需要：

1. 将应用程序注册到 Azure AD。
2. 将应用设置为使用 OWIN 身份验证管道。
3. 配置一个客户端应用程序以调用 Web API。

若要开始，请[下载应用框架](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip)或[下载已完成的示例](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip)。 每个下载项目都是 Visual Studio 2013 解决方案。 还需要一个用于注册应用程序的 Azure AD 租户。 如果还没有此租户，请[了解如何获取租户](active-directory-howto-tenant.md)。

## <a name="step-1-register-an-application-with-azure-ad"></a>步骤 1：向 Azure AD 注册应用程序
要帮助保护应用程序，首先需要在租户中创建一个应用程序，并为 Azure AD 提供一些关键信息。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在顶部栏上，单击帐户。 在“目录”列表中，选择要在其中注册应用程序的 Azure AD 租户。

3. 在左窗格中，单击“更多服务”，然后选择“Azure Active Directory”。

4. 单击“应用注册”，并选择“添加”。

5. 根据提示创建一个新的 **Web 应用程序和/或 Web API**。
  * **名称**向用户描述应用程序。 输入**待办事项列表服务**。
  * **重定向 URI** 是 Azure AD 用来返回应用程序请求的任何令牌的方案与字符串组合。 为此值输入 `https://localhost:44321/`。

6. 从应用程序的“设置” -> “属性”页中，更新应用 ID URI。 输入租户特定的标识符。 例如，输入 `https://contoso.onmicrosoft.com/TodoListService`。

7. 保存配置。 让门户保持打开状态，因为稍后你还需要注册客户端应用程序。

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>步骤 2：将应用设置为使用 OWIN 身份验证管道
要验证传入的请求和令牌，需要将应用程序设置为与 Azure AD 通信。

1. 要开始，请打开解决方案，然后使用 Package Manager Console 将 OWIN 中间件 NuGet 包添加到 TodoListService 项目。

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. 将名为 `Startup.cs` 的 OWIN 启动类添加到 TodoListService 项目。  右键单击项目，选择“添加” > “新建项”，并搜索“OWIN”。 当应用程序启动时，该 OWIN 中间件将调用 `Configuration(…)` 方法。

3. 将类声明更改为 `public partial class Startup`。 我们已在另一个文件中实现了此类的一部分。 在 `Configuration(…)` 方法中，调用 `ConfgureAuth(…)` 以设置 Web 应用的身份验证。

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. 打开文件 `App_Start\Startup.Auth.cs` 并实现 `ConfigureAuth(…)` 方法。 在 `WindowsAzureActiveDirectoryBearerAuthenticationOptions` 中提供的参数将充当应用程序与 Azure AD 通信时使用的坐标。

    ```csharp
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                Audience = ConfigurationManager.AppSettings["ida:Audience"],
                Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
            });
    }
    ```

5. 现在，可以使用 `[Authorize]` 属性并结合 JSON Web 令牌 (JWT) 持有者身份验证来保护控制器和操作。 使用 authorize 标记修饰 `Controllers\TodoListController.cs` 类。 这会强制用户在访问该页面之前登录。

    ```csharp
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    如果已授权的调用方成功调用了某个 `TodoListController` API，该操作可能需要访问有关调用方的信息。 OWIN 通过 `ClaimsPrincpal` 对象提供对持有者令牌中的声明的访问。  

6. Web API 的一个常见要求是验证令牌中存在的“作用域”。 这可确保用户已同意授予访问待办事项列表服务所需的权限。

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

2. 在 Azure AD 租户中创建新的应用程序，并在最终提示中选择“本机客户端应用程序”。
  * **名称**向用户描述应用程序。
  * 为“重定向 URI”值输入 `http://TodoListClient/`。

3. 完成注册后，Azure AD 将向应用分配唯一应用程序 ID。 在后面的步骤中会用到此值，因此，请从应用程序页复制此值。

4. 在“设置”页上，选择“所需权限”，并选择“添加”。 找到并选择待办事项列表服务，在“委派的权限”下添加“访问 TodoListService”权限，并单击“完成”。

5. 在 Visual Studio 中，打开 TodoListClient 项目中的 `App.config`，然后在 `<appSettings>` 节中输入配置值。

  * `ida:Tenant` 是 Azure AD 租户的名称 - 例如，contoso.onmicrosoft.com。
  * `ida:ClientId` 是从 Azure 门户复制的应用 ID。
  * `todo:TodoListResourceId` 是在 Azure 门户中为待办事项列表服务应用程序输入的应用 ID URI。

## <a name="next-steps"></a>后续步骤
最后，清理、生成并运行每个项目。 现在可以使用 *.onmicrosoft.com 域在租户中创建一个新的用户（如果尚未这样做）。 使用该用户登录到待办事项列表客户端，并向用户的待办事项列表添加一些任务。

[GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip) 中提供了已完成示例（无配置值）以供参考。 现在，可以转到更多的标识方案。

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
