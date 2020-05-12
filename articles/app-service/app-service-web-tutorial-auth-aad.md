---
title: 教程：对用户进行 E2E 身份验证
description: 了解如何使用应用服务身份验证和授权来确保应用服务应用端到端的安全性，包括确保访问远程 API 时的安全性。
keywords: 应用服务, azure 应用服务, authN, authZ, 安全, 安全性, 多层, azure active directory, azure ad
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/29/2020
ms.custom: seodec18
ms.openlocfilehash: b95d5d6eb52806e5b43a495b875d30846297c465
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592428"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service"></a>教程：在 Azure 应用服务中对用户进行端到端身份验证和授权

[Azure 应用服务](overview.md)提供高度可缩放、自修复的 Web 托管服务。 另外，应用服务提供对[用户身份验证和授权](overview-authentication-authorization.md)的内置支持。 本教程介绍如何通过应用服务身份验证和授权来确保应用的安全性。 本教程使用带 Angular.js 前端的 ASP.NET Core 应用作为示例。 应用服务身份验证和授权支持所有的语言运行时，你可以按照本教程的说明来了解如何将其应用到首选语言。

![简单身份验证和授权](./media/app-service-web-tutorial-auth-aad/simple-auth.png)

本教程还演示了如何[通过服务器代码](#call-api-securely-from-server-code)以及[通过浏览器代码](#call-api-securely-from-browser-code)代表经身份验证的用户访问受保护的后端 API，以便确保多层应用的安全性。

![高级身份验证和授权](./media/app-service-web-tutorial-auth-aad/advanced-auth.png)

这些只是应用服务中部分可能的身份验证和授权方案。 

下面是一个更全面的列表，列出了本教程中介绍的内容：

> [!div class="checklist"]
> * 启用内置身份验证和授权
> * 确保应用能够防范未经身份验证的请求
> * 将 Azure Active Directory 用作标识提供者
> * 代表登录的用户访问远程应用
> * 使用令牌身份验证确保服务间调用的安全性
> * 使用服务器代码中的访问令牌
> * 使用客户端（浏览器）代码中的访问令牌

可以在 macOS、Linux、Windows 中执行本教程中的步骤。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

为完成此教程：

* <a href="https://git-scm.com/" target="_blank">安装 Git</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">安装最新的 .NET Core 3.1 SDK</a>

## <a name="create-local-net-core-app"></a>创建本地 .NET Core 应用

在此步骤中，你将设置本地 .NET Core 项目。 请使用同一项目来部署后端 API 应用和前端 Web 应用。

### <a name="clone-and-run-the-sample-application"></a>克隆和运行示例应用程序

运行下列命令，以便克隆并运行示例存储库。

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

导航到 `http://localhost:5000`，尝试添加、编辑和删除代办事项。 

![在本地运行的 ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/local-run.png)

在终端按 `Ctrl+C` 可随时停止 ASP.NET Core。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-apps-to-azure"></a>将应用部署到 Azure

在此步骤中，请将项目部署到两个应用服务应用。 一个是前端应用，另一个是后端应用。

### <a name="configure-a-deployment-user"></a>配置部署用户

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-azure-resources"></a>创建 Azure 资源

在 Cloud Shell 中运行以下命令，以便创建两个 Web 应用。 将 _\<front-end-app-name>_ 和 _\<back-end-app-name>_ 替换为两个全局唯一的应用名称（有效字符为 `a-z`、`0-9`、`-`）。 有关每个命令的详细信息，请参阅 [Azure 应用服务中启用了 CORS 的 RESTful API](app-service-web-tutorial-rest-api.md)。

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
```

> [!NOTE]
> 请为前端应用和后端应用保存 Git remote 的 URL（显示在 `az webapp create` 的输出中）。
>

### <a name="push-to-azure-from-git"></a>从 Git 推送到 Azure

回到本地终端窗口，  运行以下 Git 命令，以便部署到后端应用。 将 _\<deploymentLocalGitUrl-of-back-end-app>_ 替换为在[创建 Azure 资源](#create-azure-resources)中保存的 Git remote 的 URL。 当 Git 凭据管理器提示输入凭据时，请确保输入[部署凭据](deploy-configure-credentials.md)，而不是用于登录到 Azure 门户的凭据。

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

在本地终端窗口中运行以下 Git 命令，以便将相同的代码部署到前端应用。 将 _\<deploymentLocalGitUrl-of-front-end-app>_ 替换为在[创建 Azure 资源](#create-azure-resources)中保存的 Git remote 的 URL。

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-apps"></a>浏览到应用

在浏览器中导航到以下 URL，然后会看到两个应用在运行。

```
http://<back-end-app-name>.azurewebsites.net
http://<front-end-app-name>.azurewebsites.net
```

![在 Azure 应用服务中运行的 ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/azure-run.png)

> [!NOTE]
> 如果应用重启，你可能会注意到新数据已被清除。 此行为是基于设计，因为示例 ASP.NET Core 应用使用内存中数据库。
>
>

## <a name="call-back-end-api-from-front-end"></a>从前端调用后端 API

在此步骤中，请将前端应用的服务器代码指向可访问后端 API 的方向。 稍后，请启用从前端到后端的经身份验证的访问。

### <a name="modify-front-end-code"></a>修改前端代码

在本地存储库中，请打开 _Controllers/TodoController.cs_。 在 `TodoController` 类的开头添加以下行，并将 _\<back-end-app-name>_ 替换为后端应用的名称：

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back-end-app-name>.azurewebsites.net";
```

找到用 `[HttpGet]` 修饰的方法，并将大括号中的代码替换为：

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo");
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

第一行向后端 API 应用进行 `GET /api/Todo` 调用。

接下来，找到用 `[HttpGet("{id}")]` 修饰的方法，并将大括号中的代码替换为：

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}");
return Content(data, "application/json");
```

第一行向后端 API 应用进行 `GET /api/Todo/{id}` 调用。

接下来，找到用 `[HttpPost]` 修饰的方法，并将大括号中的代码替换为：

```cs
var response = await _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", todoItem);
var data = await response.Content.ReadAsStringAsync();
return Content(data, "application/json");
```

第一行向后端 API 应用进行 `POST /api/Todo` 调用。

接下来，找到用 `[HttpPut("{id}")]` 修饰的方法，并将大括号中的代码替换为：

```cs
var res = await _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", todoItem);
return new NoContentResult();
```

第一行向后端 API 应用进行 `PUT /api/Todo/{id}` 调用。

接下来，找到用 `[HttpDelete("{id}")]` 修饰的方法，并将大括号中的代码替换为：

```cs
var res = await _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}");
return new NoContentResult();
```

第一行向后端 API 应用进行 `DELETE /api/Todo/{id}` 调用。

保存所有更改。 在本地终端窗口中，使用以下 Git 命令将所做的更改部署到前端应用：

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>检查所做的更改

导航到 `http://<front-end-app-name>.azurewebsites.net` 并添加一些项目，例如 `from front end 1` 和 `from front end 2`。

导航到 `http://<back-end-app-name>.azurewebsites.net`，此时会看到从前端应用添加的项目。 另请添加一些项目（例如 `from back end 1` 和 `from back end 2`），然后刷新前端应用，看其是否反映了所做的更改。

![在 Azure 应用服务中运行的 ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/remote-api-call-run.png)

## <a name="configure-auth"></a>配置身份验证

在此步骤中，请为两个应用启用身份验证和授权。 另请配置前端应用，以便生成一个访问令牌，用于对后端应用进行经身份验证的调用。

请将 Azure Active Directory 用作标识提供者。 有关详细信息，请参阅[为应用服务应用程序配置 Azure Active Directory 身份验证](configure-authentication-provider-aad.md)。

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>启用针对后端应用的身份验证和授权

在 [Azure 门户](https://portal.azure.com)菜单上，选择“资源组”，或在任意页面中搜索并选择“资源组”   。

在“资源组”中，查找并选择资源组  。 在“概述”中，选择后端应用的管理页  。

![在 Azure 应用服务中运行的 ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/portal-navigate-back-end.png)

在后端应用的左侧菜单中，选择“身份验证/授权”，然后选择“启用”以启用应用服务身份验证   。

在“请求未经身份验证时需执行的操作”中，选择“使用 Azure Active Directory 登录”。  

在“身份验证提供程序”  下，选择“Azure Active Directory”  。

![在 Azure 应用服务中运行的 ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/configure-auth-back-end.png)

选择“快速”，接受创建新 AD 应用所需的默认设置，然后选择“确定”   。

在“身份验证/授权”页中，选择“保存”   。

看到包含消息`Successfully saved the Auth Settings for <back-end-app-name> App`的通知后，刷新门户页。

再次选择“Azure Active Directory”，然后选择“Azure AD 应用”   。

将 Azure AD 应用程序的**客户端 ID** 复制到记事本。 稍后需要用到此值。

![在 Azure 应用服务中运行的 ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/get-application-id-back-end.png)

如果到此为止，你将拥有一个已受应用服务身份验证和授权保护的独立应用。 其余部分介绍如何将经过身份验证的用户从前端“流式传输”到后端，以便保护多应用解决方案。 

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>启用针对前端应用的身份验证和授权

遵循前端应用的步骤进行操作，但跳过最后一步。 对于前端应用，不需要客户端 ID。

根据需要导航到 `http://<front-end-app-name>.azurewebsites.net`。 现在你会被定向到安全登录页。 登录后，你仍然无法从后端应用访问数据  ，因为后端应用现在要求从前端应用登录 Azure Active Directory。 你需要执行以下三项操作：

- 授予前端访问后端的权限
- 对应用服务进行配置，使之返回可用令牌
- 在代码中使用该令牌

> [!TIP]
> 如果在遇到错误后重新配置应用的身份验证/授权设置，则可能无法通过新的设置在令牌存储中重新生成令牌。 若要确保重新生成令牌，需在注销后重新登录应用。 若要这样做，一个简单的方法是在专用模式下使用浏览器，在应用中更改设置后关闭该浏览器，然后再在专用模式下将其重新打开。

### <a name="grant-front-end-app-access-to-back-end"></a>授予前端应用访问后端的权限

启用对两种应用的身份验证和授权以后，即可通过 AD 应用程序对每种应用提供支持。 在此步骤中，请为前端应用授予代表用户访问后端的权限。 （严格说来就是，请为前端的  AD 应用程序授予代表用户访问后端的  AD 应用程序的权限。）

在 [Azure 门户](https://portal.azure.com)菜单中，选择“Azure Active Directory”，或在任意页面中搜索并选择“Azure Active Directory”   。

选择“应用注册”   > “拥有的应用程序”   > “查看此目录中的所有应用程序”  。 选择前端应用名称，然后选择“API 权限”  。

![在 Azure 应用服务中运行的 ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/add-api-access-front-end.png)

选择“添加权限”  ，然后选择“我的组织使用的 API”   > “\<back-end-app-name>”  。

在后端应用的“请求 API 权限”  页中，选择“委托的权限”  和“user_impersonation”  ，然后选择“添加权限”  。

![在 Azure 应用服务中运行的 ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/select-permission-front-end.png)

### <a name="configure-app-service-to-return-a-usable-access-token"></a>对应用服务进行配置，使之返回可用的访问令牌

现在，前端应用具有以登录用户身份访问后端应用所需的权限。 在此步骤中，请配置应用服务身份验证和授权，以便获取可以用来访问后端的访问令牌。 执行此步骤时，需要后端的客户端 ID，该 ID 是从[为后端应用启用身份验证和授权](#enable-authentication-and-authorization-for-back-end-app)复制的。

在前端应用的左侧菜单中，选择“开发工具”  下的“资源浏览器”  ，然后选择“运行”  。

此时会打开 [Azure 资源浏览器](https://resources.azure.com)，前端应用在资源树中处于选中状态。 在页面顶部单击“读/写”  ，以便启用编辑 Azure 资源的功能。

![在 Azure 应用服务中运行的 ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/resources-enable-write.png)

在左侧浏览器中，向下钻取到“config”   > “authsettings”  。

在“authsettings”  视图中，单击“编辑”  。 使用复制的客户端 ID 将 `additionalLoginParams` 设置为以下 JSON 字符串。 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back-end-client-id>"],
```

![在 Azure 应用服务中运行的 ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/additional-login-params-front-end.png)

单击“PUT”，对设置进行保存。 

现在已配置好了应用。 前端现在可以通过适当的访问令牌访问后端了。

若要了解如何为其他提供程序配置访问令牌，请参阅[刷新标识提供者令牌](app-service-authentication-how-to.md#refresh-identity-provider-tokens)。

## <a name="call-api-securely-from-server-code"></a>通过服务器代码安全地调用 API

在此步骤中，请允许以前修改过的服务器代码对后端 API 进行经身份验证的调用。

现在，前端应用已经有了必需的权限，并且还将后端的客户端 ID 添加到了登录参数中， 因此可以获取访问令牌，通过后端应用进行身份验证。 应用服务可以将此令牌提供给服务器代码，方法是将 `X-MS-TOKEN-AAD-ACCESS-TOKEN` 标头注入每个经身份验证的请求（请参阅[在应用代码中检索令牌](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)）。

> [!NOTE]
> 所有支持的语言都可以注入这些标头。 对于每种相应的语言，可以使用标准模式来访问它们。

在本地存储库中，请再次打开 _Controllers/TodoController.cs_。 在 `TodoController(TodoContext context)` 构造函数中添加以下代码：

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);
}
```

此代码将标准的 HTTP 标头 `Authorization: Bearer <access-token>` 添加到所有远程 API 调用。 在 ASP.NET Core MVC 请求执行管道中，`OnActionExecuting` 就在相应的操作执行之前执行，因此每个传出 API 调用现在都提供访问令牌。

保存所有更改。 在本地终端窗口中，使用以下 Git 命令将所做的更改部署到前端应用：

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

再次登录到 `https://<front-end-app-name>.azurewebsites.net`。 在用户数据使用协议页上，单击“接受”。 

现在应该可以像以前一样通过后端应用创建、读取、更新和删除数据了。 唯一区别是，两种应用现在都受应用服务身份验证和授权的保护，包括在进行服务间调用时受到保护。

祝贺你！ 服务器代码现在可以代表经身份验证的用户访问后端数据了。

## <a name="call-api-securely-from-browser-code"></a>通过浏览器代码安全地调用 API

在此步骤中，请将前端 Angular.js 应用指向后端 API。 这样就可以了解如何检索访问令牌，并使用它向后端应用进行 API 调用。

服务器代码可以访问请求标头，而客户端代码则可以访问 `GET /.auth/me` 以获取相同的访问令牌（请参阅[在应用代码中检索令牌](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)）。

> [!TIP]
> 此部分使用标准的 HTTP 方法来演示安全的 HTTP 调用。 但是，可以使用[适用于 JavaScript 的 Microsoft 身份验证库](https://github.com/AzureAD/microsoft-authentication-library-for-js)来帮助简化 Angular.js 应用程序模式。
>

### <a name="configure-cors"></a>配置 CORS

在 Cloud Shell 中，使用 [`az webapp cors add`](/cli/azure/webapp/cors#az-webapp-cors-add) 命令对客户端的 URL 启用 CORS。 替换 _\<back-end-app-name>_ 和 _\<front-end-app-name>_ 占位符。

```azurecli-interactive
az webapp cors add --resource-group myAuthResourceGroup --name <back-end-app-name> --allowed-origins 'https://<front-end-app-name>.azurewebsites.net'
```

此步骤与身份验证和授权无关。 但是，只有执行此步骤，才能使用浏览器从 Angular.js 应用进行跨域 API 调用。 有关详细信息，请参阅[添加 CORS 功能](app-service-web-tutorial-rest-api.md#add-cors-functionality)。

### <a name="point-angularjs-app-to-back-end-api"></a>将 Angular.js 应用指向后端 API

在本地存储库中，打开 _wwwroot/index.html_。

在第 51 行中，将 `apiEndpoint` 变量设置为后端应用的 HTTPS URL (`https://<back-end-app-name>.azurewebsites.net`)。 在应用服务中将 _\<back-end-app-name>_ 替换为你的应用名称。

在本地存储库中打开 _wwwroot/app/scripts/todoListSvc.js_，然后就会看到 `apiEndpoint` 已前置到所有 API 调用。 Angular.js 应用现在可以调用后端 API 了。 

### <a name="add-access-token-to-api-calls"></a>向 API 调用添加访问令牌

在 _wwwroot/app/scripts/todoListSvc.js_ 的 API 调用列表上方（`getItems : function(){` 行上方），向列表添加以下函数：

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

调用此函数是为了使用访问令牌设置默认的 `Authorization` 标头。 将在下一步调用此函数。

在本地存储库中，打开 _wwwroot/app/scripts/app.js_ 并查找以下代码：

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

请将整个代码块替换为以下代码：

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

新更改添加 `resolve` 映射来调用 `/.auth/me` 并设置访问令牌。 这样可以确保在实例化 `todoListCtrl` 控制器之前为你提供访问令牌。 因此，由该控制器进行的所有 API 调用都包括此令牌。

### <a name="deploy-updates-and-test"></a>部署更新并进行测试

保存所有更改。 在本地终端窗口中，使用以下 Git 命令将所做的更改部署到前端应用：

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

再次导航到 `https://<front-end-app-name>.azurewebsites.net`。 现在应该可以直接在 Angular.js 应用中通过后端应用创建、读取、更新和删除数据了。

祝贺你！ 客户端代码现在可以代表经身份验证的用户访问后端数据了。

## <a name="when-access-tokens-expire"></a>当访问令牌过期时

访问令牌在一段时间后会过期。 若要了解如何在不需用户通过应用重新进行身份验证的情况下刷新访问令牌，请参阅[刷新标识提供者令牌](app-service-authentication-how-to.md#refresh-identity-provider-tokens)。

## <a name="clean-up-resources"></a>清理资源

在前面的步骤中，你在资源组中创建了 Azure 资源。 如果认为将来不需要这些资源，请在 Cloud Shell 中运行以下命令删除资源组：

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

此命令可能需要花费一点时间运行。

<a name="next"></a>
## <a name="next-steps"></a>后续步骤

你已了解：

> [!div class="checklist"]
> * 启用内置身份验证和授权
> * 确保应用能够防范未经身份验证的请求
> * 将 Azure Active Directory 用作标识提供者
> * 代表登录的用户访问远程应用
> * 使用令牌身份验证确保服务间调用的安全性
> * 使用服务器代码中的访问令牌
> * 使用客户端（浏览器）代码中的访问令牌

继续学习下一篇教程，了解如何将自定义 DNS 名称映射到应用。

> [!div class="nextstepaction"]
> [将现有的自定义 DNS 名称映射到 Azure 应用服务](app-service-web-tutorial-custom-domain.md)
