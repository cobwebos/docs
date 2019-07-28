---
title: 教程 - 从单页应用程序授予对 ASP.NET Core Web API 的访问权限 - Azure Active Directory B2C
description: 了解如何使用 Active Directory B2C 保护 .NET Core Web API，并从单页 Node.js 应用程序调用该 API。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b53ce30f4c49580bcd8ad3e259adf0300d8bd4a6
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68369295"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>教程：从单页应用程序使用 Azure Active Directory B2C 授予对 ASP.NET Core Web API 的访问权限

本教程介绍如何从单页应用程序调用受 Azure Active Directory (Azure AD) B2C 保护的 ASP.NET Core Web API 资源。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 添加 Web API 应用程序
> * 配置 Web API 的范围
> * 授予 Web API 权限
> * 将示例配置为使用此应用程序

## <a name="prerequisites"></a>先决条件

* 完成[教程：使用 Azure Active Directory B2C 在单页应用程序中启用身份验证](active-directory-b2c-tutorials-spa.md)。
* Visual Studio 2019 或更高版本，或 Visual Studio Code
* .NET Core 2.2 或更高版本
* Node.js

## <a name="add-a-web-api-application"></a>添加 Web API 应用程序

Web API 资源需要先在租户中注册，然后才能接受并响应提供访问令牌的客户端应用程序所提出的受保护资源请求。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录  。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”   。
1. 选择“应用程序”，然后选择“添加”   。
1. 输入应用程序的名称。 例如，“webapi1”  。
1. 对于“包括 Web 应用/Web API”和“允许隐式流”，请选择“是”。   
1. 对于“回复 URL”，请输入 Azure AD B2C 要将应用程序请求的任何令牌返回到的终结点  。 本教程中的示例在本地运行并在 `https://localhost:5000` 上进行侦听。
1. 对于“应用 ID URI”，请在所示的 URI 中输入一个 API 终结点标识符。  对于本教程，请输入 `api`，因此，完整的 URI 类似于 `https://contosotenant.onmicrosoft.com/api`。
1. 单击“创建”。 
1. 选择“webapi1”应用程序以打开其属性页。 
1. 记下属性页上显示的“应用程序 ID”。  在稍后的步骤中配置 Web 应用程序时，需要使用此 ID。

## <a name="configure-scopes"></a>配置范围

可通过范围控制对受保护资源的访问。 Web API 使用作用域实施基于作用域的访问控制。 例如，可以让某些用户拥有读取和写入访问权限，让另一些用户拥有只读权限。 在本教程中，你将定义 Web API 的读取和写入权限。

1. 选择“应用程序”，然后选择“webapi1”以打开其属性页（如果尚未打开）。  
1. 选择“已发布的范围”  。
1. 在“范围”中输入 `Hello.Read`，在“说明”中输入 `Read access to hello`。  
1. 在“范围”中输入 `Hello.Write`，在“说明”中输入 `Write access to hello`。  
1. 选择“保存”。 
1. 记下 `Hello.Read` 范围的“完整范围值”，以便在稍后的步骤中配置单页应用程序时使用。  完整范围值类似于 `https://yourtenant.onmicrosoft.com/api/Hello.Read`。

可以使用发布的作用域向客户端应用授予对 Web API 的权限。

## <a name="grant-permissions"></a>授予权限

若要从另一应用程序调用受保护的 Web API，需授予应用程序访问该 Web API 的权限。

在先决条件教程中，你已创建名为 *webapp1* 的 Web 应用程序。 在本教程中，你要将该应用程序配置为调用在上一部分创建的 Web API：*webapi1*。

1. 在 Azure 门户中导航到你的 B2C 租户
1. 依次选择“应用程序”、“webapp1”   。
1. 依次选择“API 访问”、“添加”   。
1. 在“选择 API”下拉列表中，选择“webapi1”   。
1. 在“选择范围”下拉列表中，选择之前定义的“Hello.Read”和“Hello.Write”范围    。
1. 单击“确定”。 

随即会注册该单页应用程序，它可以调用受保护的 Web API。 用户通过 Azure AD B2C 进行身份验证，以使用单页应用程序。 该单页应用从 Azure AD B2C 获取授权，以访问受保护的 Web API。

## <a name="configure-the-sample"></a>配置示例

注册 Web API 并定义范围之后，请将 Web API 代码配置为使用你的 Azure AD B2C 租户。 在本教程中，你将配置一个可从 GitHub 下载的示例 .NET Core Web 应用程序。

[下载 \*.zip 存档](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip)，或者从 GitHub 克隆示例 Web API 项目。

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>配置 Web API

1. 在 Visual Studio 或 Visual Studio Code 中打开 *B2C-WebApi/**appsettings.json*** 文件。
1. 修改 `AzureAdB2C` 块以反映租户名称、Web API 应用程序的应用程序 ID、注册/登录策略的名称，以及前面定义的范围。 该块应类似于以下示例（包含相应的 `Tenant` 和 `ClientId` 值）：

    ```json
    "AzureAdB2C": {
      "Tenant": "<your-tenant-name>.onmicrosoft.com",
      "ClientId": "<webapi-application-ID>",
      "Policy": "B2C_1_signupsignin1",

      "ScopeRead": "Hello.Read",
      "ScopeWrite": "Hello.Write"
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

本教程系列的[前一篇教程](active-directory-b2c-tutorials-spa.md)中所述的单页应用程序 (SPA) 使用 Azure AD B2C 进行用户注册和登录，并调用 *frabrikamb2c* 演示租户保护的 ASP.NET Core Web API。

在本部分，你将更新该单页应用程序，以调用你的 Azure AD B2C 租户保护的、在本地计算机上运行的 ASP.NET Core Web API。 

若要更改 SPA 中的设置：

1. 打开在上一篇教程中下载或克隆的 [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa] 项目中的 *index.html* 文件。
1. 使用前面创建的 *Hello.Read* 范围的 URI 以及 Web API 的 URL 配置示例。
    1. 在 `appConfig` 定义中，将 `b2cScopes` 值替换为范围的完整 URI（前面记下的“完整范围值”）。 
    1. 将 `webApi` 值更改为在上一部分指定的 `applicationURL` 值。

    `appConfig` 定义应类似于以下代码块（请用你的租户名称替代 `<your-tenant-name>`）：

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/Hello.Read"],
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
1. 使用在[上一篇教程](active-directory-b2c-tutorials-spa.md)中所用的电子邮件地址和密码登录。 成功登录后，应会看到 `User 'Your Username' logged-in` 消息。
1. 选择“调用 Web API”按钮。  SPA 将获取 Azure AD B2C 授予的授权，然后访问受保护的 Web API 以显示其索引页的内容：

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 添加 Web API 应用程序
> * 配置 Web API 的范围
> * 授予 Web API 权限
> * 将示例配置为使用此应用程序

了解 SPA 如何从受保护的 Web API 请求资源后，接下来可以更深入地了解这些应用程序类型如何彼此交互以及与 Azure AD B2C 交互。

> [!div class="nextstepaction"]
> [可在 Azure Active Directory B2C 中使用的应用程序类型 >](active-directory-b2c-apps.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
