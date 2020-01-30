---
title: 教程：从单页应用授予对 ASP.NET Core Web API 的访问权限
titleSuffix: Azure AD B2C
description: 本教程介绍如何使用 Active Directory B2C 保护 .NET Core Web API，并从单页 Node.js 应用程序调用该 API。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 80b2165b0ec652358a3eb8ac9d55b64f525e4690
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849837"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>教程：从单页应用程序使用 Azure Active Directory B2C 授予对 ASP.NET Core Web API 的访问权限

本教程介绍如何从单页应用程序调用受 Azure Active Directory B2C (Azure AD B2C) 保护的 ASP.NET Core Web API 资源。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 添加 Web API 应用程序
> * 配置 Web API 的范围
> * 授予 Web API 权限
> * 将示例配置为使用此应用程序

## <a name="prerequisites"></a>必备条件

* 完成[教程：使用 Azure Active Directory B2C 在单页应用程序中启用身份验证](tutorial-single-page-app.md)。
* Visual Studio 2019 或更高版本，或 Visual Studio Code
* .NET Core 2.2 或更高版本
* Node.js

## <a name="add-a-web-api-application"></a>添加 Web API 应用程序

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>配置范围

可通过范围控制对受保护资源的访问。 Web API 使用作用域实施基于作用域的访问控制。 例如，可以让某些用户拥有读取和写入访问权限，让另一些用户拥有只读权限。 在本教程中，你将定义 Web API 的读取和写入权限。

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

在“范围”下记录 `demo.read` 范围的值，以便在稍后配置单页应用程序的步骤中使用  。 完整范围值类似于 `https://contosob2c.onmicrosoft.com/api/demo.read`。

## <a name="grant-permissions"></a>授予权限

若要从另一应用程序调用受保护的 Web API，需授予应用程序访问该 Web API 的权限。

在先决条件教程中，你已创建名为 *webapp1* 的 Web 应用程序。 在本教程中，你要将该应用程序配置为调用在上一部分创建的 Web API：*webapi1*。

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

随即会注册该单页应用程序，它可以调用受保护的 Web API。 用户通过 Azure AD B2C 进行身份验证，以使用单页应用程序。 该单页应用从 Azure AD B2C 获取授权，以访问受保护的 Web API。

## <a name="configure-the-sample"></a>配置示例

注册 Web API 并定义范围之后，请将 Web API 代码配置为使用你的 Azure AD B2C 租户。 在本教程中，你将配置一个可从 GitHub 下载的示例 .NET Core Web 应用程序。

[下载 \*.zip 存档](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip)，或者从 GitHub 克隆示例 Web API 项目。

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>配置 Web API

1. 在 Visual Studio 或 Visual Studio Code 中打开 <em>B2C-WebApi/**appsettings.json**</em> 文件。
1. 修改 `AzureAdB2C` 块以反映租户名称、Web API 应用程序的应用程序 ID、注册/登录策略的名称，以及前面定义的范围。 该块应类似于以下示例（包含相应的 `Tenant` 和 `ClientId` 值）：

    ```json
    "AzureAdB2C": {
      "Tenant": "<your-tenant-name>.onmicrosoft.com",
      "ClientId": "<webapi-application-ID>",
      "Policy": "B2C_1_signupsignin1",

      "ScopeRead": "demo.read",
      "ScopeWrite": "demo.write"
    },
    ```

#### <a name="enable-cors"></a>启用 CORS

若要允许单页应用程序调用 ASP.NET Core Web API，需要在该 Web API 中启用 [CORS](https://docs.microsoft.com/aspnet/core/security/cors)。

1. 在 *Startup.cs* 中，将 CORS 添加到 `ConfigureServices()` 方法。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. 在 `ConfigureServices()` 方法中，将 `jwtOptions.Authority` 值设置为以下令牌颁发者 URI。

    请将 `<your-tenant-name>` 替换为你的 B2C 租户名称。

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. 在 `Configure()` 方法中配置 CORS。

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. （仅适用于 Visual Studio）在解决方案资源管理器中的“属性”下，打开 *launchSettings.json* 文件，并找到 `iisExpress` 块。 
1. （仅适用于 Visual Studio）使用在前面的步骤中注册 *webapi1* 应用程序时指定的端口号更新 `applicationURL` 值。 例如：

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>配置单页应用程序

本教程系列的[前一篇教程](tutorial-single-page-app.md)中所述的单页应用程序 (SPA) 使用 Azure AD B2C 进行用户注册和登录，并调用 *frabrikamb2c* 演示租户保护的 ASP.NET Core Web API。

在本部分，你将更新该单页应用程序，以调用你的 Azure AD B2C 租户保护的、在本地计算机上运行的 ASP.NET Core Web API。 

若要更改 SPA 中的设置：

1. 打开在上一篇教程中下载或克隆的 [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa] 项目中的 *index.html* 文件。
1. 使用前面创建的 *demo.read* 范围的 URI 以及 Web API 的 URL 配置示例。
    1. 在 `appConfig` 定义中，将 `b2cScopes` 值替换为范围（之前记录的“范围”值）的完整 URI  。
    1. 将 `webApi` 值更改为在前面的步骤中注册 Web API 应用程序时所添加的重定向 URI。

    `appConfig` 定义应类似于以下代码块（请用你的租户名称替代 `<your-tenant-name>`）：

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>运行 SPA 和 Web API

最后，请在本地计算机上同时运行 ASP.NET Core Web API 和 Node.js 单页应用程序。 然后登录到该单页应用程序，并按相应的按钮向受保护的 API 发起请求。

尽管本教程中的两个应用程序都在本地运行，但它们使用 Azure AD B2C 进行安全注册/登录并授予对受保护 Web API 的访问权限。

### <a name="run-the-aspnet-core-web-api"></a>运行 ASP.NET Core Web API

在 Visual Studio 中，按 **F5** 生成并调试 *B2C-WebAPI.sln* 解决方案。 当项目启动时，默认浏览器中会显示一个网页，告知可以使用该 Web API 发出请求。

如果你偏向于使用`dotnet` CLI 而不是 Visual Studio：

1. 打开控制台窗口，切换到包含 *\*.csproj* 文件的目录。 例如：

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. 执行 `dotnet run` 来生成并运行 Web API。

    当 API 启动并运行时，你应会看到类似于以下内容的输出（对于本教程，可以放心忽略任何 `NETSDK1059` 警告）：

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>运行单页应用

1. 打开控制台窗口，切换到包含 Node.js 示例的目录。 例如：

    `cd active-directory-b2c-javascript-msal-singlepageapp`

1. 运行以下命令：

    ```console
    npm install && npm update
    node server.js
    ```

    控制台窗口将显示该应用程序所在的端口号。

    ```console
    Listening on port 6420...
    ```

1. 在浏览器中导航到 `http://localhost:6420`，查看此应用程序。
1. 使用在[上一篇教程](tutorial-single-page-app.md)中所用的电子邮件地址和密码登录。 成功登录后，应会看到 `User 'Your Username' logged-in` 消息。
1. 选择“调用 Web API”按钮。  SPA 将获取 Azure AD B2C 授予的授权，然后访问受保护的 Web API 以显示其索引页的内容：

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 添加 Web API 应用程序
> * 配置 Web API 的范围
> * 授予 Web API 权限
> * 将示例配置为使用此应用程序

了解 SPA 如何从受保护的 Web API 请求资源后，接下来可以更深入地了解这些应用程序类型如何彼此交互以及与 Azure AD B2C 交互。

> [!div class="nextstepaction"]
> [可在 Azure Active Directory B2C 中使用的应用程序类型 >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
