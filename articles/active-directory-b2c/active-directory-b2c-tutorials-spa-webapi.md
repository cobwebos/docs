---
title: 教程 - 从单页应用使用 Azure Active Directory B2C 授予对 ASP.NET Core Web API 的访问权限 | Microsoft Docs
description: 有关如何从单页应用使用 Active Directory B2C 保护 .NET Core Web API 并调用该 API 的教程。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/02/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 5b99f60c1bd81b77a5fc2be5575f65fc63eb0c11
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34711087"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-app-using-azure-active-directory-b2c"></a>教程：从单页应用使用 Azure Active Directory B2C 授予对 ASP.NET Core Web API 的访问权限

本教程展示了如何从单页应用调用受 Azure Active Directory (Azure AD) B2C 保护的 ASP.NET Core Web API 资源。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure AD B2C 租户中注册 Web API
> * 定义并配置 Web API 的作用域
> * 授予应用访问 Web API 的权限
> * 更新示例代码，以便使用 Azure AD B2C 来保护 Web API

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 完成[在单页应用程序中使用 Azure Active Directory B2C 对用户进行身份验证](active-directory-b2c-tutorials-spa.md)教程。
* 安装带有 ASP.NET 和 Web 开发工作负荷的 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。
* [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 或更高版本
* 安装 [Node.js](https://nodejs.org/en/download/)

## <a name="register-web-api"></a>注册 Web API

Web API 资源需要先在租户中注册，然后才能接受并响应通过 Azure Active Directory 提供[访问令牌](../active-directory/develop/active-directory-dev-glossary.md#access-token)的[客户端应用程序](../active-directory/develop/active-directory-dev-glossary.md#client-application)所提出的[受保护资源请求](../active-directory/develop/active-directory-dev-glossary.md#resource-server)。 注册时，会在租户中构建[应用程序和服务主体对象](../active-directory/develop/active-directory-dev-glossary.md#application-object)。 

以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. 从 Azure 门户的服务列表中选择“Azure AD B2C”。

2. 在 B2C 设置中，单击“应用程序”，然后单击“添加”。

    若要在租户中注册示例 Web API，请使用以下设置。
    
    ![添加新 API](media/active-directory-b2c-tutorials-spa-webapi/web-api-registration.png)
    
    | 设置      | 建议的值  | 说明                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | Hello Core API | 输入一个**名称**，描述面向开发人员的 Web API。 |
    | 包括 Web 应用/Web API | 是 | 对于 Web API，请选择“是”。 |
    | 允许隐式流 | 是 | 选择“是”，因为 API 使用 [OpenID Connect 登录](active-directory-b2c-reference-oidc.md)。 |
    | 回复 URL | `http://localhost:44332` | 回复 URL 属于终结点，允许 Azure AD B2C 在其中返回 API 请求的任何令牌。 在本教程中，示例 Web API 在本地 (localhost) 运行，并在端口 5000 上进行侦听。 |
    | 应用 ID URI | HelloCoreAPI | 此 URI 可唯一标识租户中的 API。 这样即可每个租户注册多个 API。 [作用域](../active-directory/develop/active-directory-dev-glossary.md#scopes)控制对受保护 API 资源的访问，是按 App ID URI 来定义的。 |
    | 本机客户端 | 否 | 由于这是 Web API，不是本机客户端，因此请选择“否”。 |
    
3. 单击“创建”以注册 API。

注册的 API 显示在 Azure AD B2C 租户的应用程序列表中。 从列表中选择 Web API。 此时会显示 Web API 的属性窗格。

![Web API 属性](./media/active-directory-b2c-tutorials-spa-webapi/b2c-web-api-properties.png)

请记下“应用程序客户端 ID”。 此 ID 用于唯一标识 API，是稍后在本教程中配置 API 所必需的。

通过 Azure AD B2C 注册 Web API 即可定义一种信任关系。 此 API 是通过 B2C 注册的，因此现在可以信任从其他应用程序收到的 B2C 访问令牌。

## <a name="define-and-configure-scopes"></a>定义并配置作用域

可以通过[作用域](../active-directory/develop/active-directory-dev-glossary.md#scopes)控制对受保护资源的访问。 Web API 使用作用域实施基于作用域的访问控制。 例如，可以让某些用户拥有读取和写入访问权限，让另一些用户拥有只读权限。 在本教程中，你将定义对 Web API 的读取权限。

### <a name="define-scopes-for-the-web-api"></a>定义 Web API 的作用域

注册的 API 显示在 Azure AD B2C 租户的应用程序列表中。 从列表中选择 Web API。 此时会显示 Web API 的属性窗格。

单击“发布的作用域(预览)”。

若要配置 API 的作用域，请添加以下条目。 

![在 Web API 中定义的作用域](media/active-directory-b2c-tutorials-spa-webapi/scopes-web-api.png)

| 设置      | 建议的值  | 说明                                        |
| ------------ | ------- | -------------------------------------------------- |
| **范围** | demo.read | 对 demo API 的读取访问权限 |

单击“ **保存**”。

可以使用发布的作用域向客户端应用授予对 Web API 的权限。

### <a name="grant-app-permissions-to-web-api"></a>授予应用访问 Web API 的权限

若要从应用调用受保护的 Web API，需授予应用访问该 API 的权限。 在本教程中，请使用在[在单页应用程序 (JavaScript) 中使用 Azure Active Directory B2C 对用户进行身份验证](active-directory-b2c-tutorials-spa.md)中创建的单页应用。

1. 从 Azure 门户的服务列表中选择“Azure AD B2C”，然后单击“应用程序”，查看已注册应用的列表。

2. 从应用列表中选择“我的示例单页应用”，单击“API 访问权限(预览版)”，然后单击“添加”。

3. 在“选择 API”下拉列表中，选择已注册的 Web API **Hello Core API**。

4. 在“选择作用域”下拉列表中，选择在 Web API 注册中定义的作用域。

    ![选择应用的作用域](media/active-directory-b2c-tutorials-spa-webapi/selecting-scopes-for-app.png)

5. 单击“确定”。

这将注册“我的示例单页应用”，以便调用受保护的 **Hello Core API**。 用户通过 Azure AD B2C 进行[身份验证](../active-directory/develop/active-directory-dev-glossary.md#authentication)，以便使用 WPF 桌面应用。 桌面应用从 Azure AD B2C 获取[授权](../active-directory/develop/active-directory-dev-glossary.md#authorization-grant)，以便访问受保护的 Web API。

## <a name="update-code"></a>更新代码

注册 Web API 并定义作用域以后，需配置 Web API 代码，以便使用 Azure AD B2C 租户。 在本教程中，将配置一个可从 GitHub 下载的示例 .NET Core Web 应用。 

从 GitHub [下载 zip 文件](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip)或克隆示例 Web 应用。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>配置 Web API

1. 在 Visual Studio 中打开 **B2C-WebAPI.sln** 解决方案。

2. 打开 **appsettings.json** 文件。 更新以下值来将 Web API 配置为使用你的租户：

    ```javascript
    "AzureAdB2C": 
      {
        "Tenant": "<your tenant name>.onmicrosoft.com", 
        "ClientId": "<The Application ID for your web API obtained from the Azure portal>",
        "Policy": "<Your sign up sign in policy e.g. B2C_1_SiUpIn>",
        "ScopeRead": "demo.read"  
      },
    ```

#### <a name="enable-cors"></a>启用 CORS

若要允许单页应用调用 ASP.NET Core Web API，需要启用 [CORS](https://docs.microsoft.com/aspnet/core/security/cors)。

1. 在 **Startup.cs** 中，将 CORS 添加到 `ConfigureServices()` 方法。

    ```C#
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. 在 **Startup.cs** 中，在 `Configure()` 方法中配置 CORS。

    ```C#
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

3. 在“属性”下打开 **launchSettings.json** 文件，找到 *applicationURL* 设置，然后记录在下一部分使用的值。

### <a name="configure-the-single-page-app"></a>配置单页应用

该单页应用使用 Azure AD B2C 完成用户注册、登录，并调用受保护的 ASP.NET Core Web API。 需要更新单页应用来调用 .NET Core Web API。
若要更改应用设置，请执行以下操作：

1. 打开 Node.js 单页应用示例中的 `index.html` 文件。
2. 使用 Azure AD B2C 租户注册信息来配置示例。 在以下代码中，将租户名称添加到 **b2cScopes**，并将 **webApi** 值更改为以前记录的 *applicationURL* 值：

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_SiUpIn",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/HelloCoreAPI/demo.read"],
        webApi: 'http://localhost:64791/api/values',
    };
    ```

## <a name="run-the-spa-app-and-web-api"></a>运行 SPA 应用和 Web API

你需要同时运行 Node.js 单页应用和 .NET Core Web API。

### <a name="run-the-aspnet-core-web-api"></a>运行 ASP.NET Core Web API 

在 Visual Studio 中按 **F5** 来调试 **B2C-WebAPI.sln** 解决方案。

当项目启动时，会在你的默认浏览器中打开一个网页，宣告该 Web API 已可供请求使用。

### <a name="run-the-single-page-app"></a>运行单页应用

1. 启动 Node.js 命令提示符。
2. 切换到包含 Node.js 示例的目录。 例如 `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. 运行以下命令：
    ```
    npm install && npm update
    node server.js
    ```

    控制台窗口将显示承载着该应用的位置的端口号。
    
    ```
    Listening on port 6420...
    ```

4. 使用浏览器导航到地址 `http://localhost:6420` 来查看该应用。
5. 使用[在单页应用程序 (JavaScript) 中使用 Azure Active Directory B2C 对用户进行身份验证](active-directory-b2c-tutorials-spa.md)中使用的电子邮件地址和密码进行登录。
6. 单击“调用 API”按钮。

在使用用户帐户进行注册或登录后，该示例会调用受保护的 Web API 并返回一个结果。

## <a name="clean-up-resources"></a>清理资源

如果打算尝试其他 Azure AD B2C 教程，可以使用 Azure AD B2C 租户。 可以在不再需要时[删除 Azure AD B2C 租户](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)。

## <a name="next-steps"></a>后续步骤

本文已引导你完成了通过在 Azure AD B2C 中注册和定义作用域来保护 Web API。 接下来请通过浏览可用的 Azure AD B2C 代码示例来了解更多内容。

> [!div class="nextstepaction"]
> [Azure AD B2C 代码示例](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
