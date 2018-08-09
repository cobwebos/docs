---
title: Azure 应用服务中启用了 CORS 的 RESTful API | Microsoft Docs
description: 了解如何通过 Azure 应用服务来托管包含 CORS 支持的 RESTful API。
services: app-service\api
documentationcenter: dotnet
author: cephalin
manager: cfowler
editor: ''
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/28/2018
ms.author: cephalin
ms.custom: mvc, devcenter
ms.openlocfilehash: b468240d1a9aaf0511358433a8beee7e6442e145
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445015"
---
# <a name="tutorial-host-a-restful-api-with-cors-in-azure-app-service"></a>教程：在 Azure 应用服务中托管启用了 CORS 的 RESTful API

[Azure 应用服务](app-service-web-overview.md)提供高度可缩放、自修补的 Web 托管服务。 另外，应用服务还为 RESTful API 提供对[跨域资源共享 (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing) 的内置支持。 本教程介绍如何将 ASP.NET Core API 应用部署到提供 CORS 支持的应用服务。 请使用命令行工具来配置应用，使用 Git 来部署应用。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 Azure CLI 创建应用服务资源
> * 使用 Git 将 RESTful API 部署到 Azure
> * 启用应用服务 CORS 支持

可以在 macOS、Linux、Windows 中执行本教程中的步骤。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

完成本教程：

* [安装 Git](https://git-scm.com/)。
* [安装 .NET Core](https://www.microsoft.com/net/core/)。

## <a name="create-local-aspnet-core-app"></a>创建本地 ASP.NET Core 应用

在此步骤中，请设置本地 ASP.NET Core 项目。 应用服务支持以其他语言编写的适用于 API 的同一工作流。

### <a name="clone-the-sample-application"></a>克隆示例应用程序

在终端窗口中，通过 `cd` 转到工作目录。  

运行下列命令以克隆示例存储库。 

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
```

此存储库包含的应用是根据以下教程创建的：[使用 Swagger 的 ASP.NET Core Web API 帮助页](/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio)。 它使用 Swagger 生成器来提供 [Swagger UI](https://swagger.io/swagger-ui/) 和 Swagger JSON 终结点。

### <a name="run-the-application"></a>运行应用程序

运行以下命令，安装所需的包，运行数据库迁移并启动应用程序。

```bash
cd dotnet-core-api
dotnet restore
dotnet run
```

在浏览器中导航到 `http://localhost:5000/swagger`，以便使用 Swagger UI。

![在本地运行的 ASP.NET Core API](./media/app-service-web-tutorial-rest-api/local-run.png)

导航到 `http://localhost:5000/api/todo`，此时会看到 ToDo JSON 项的列表。

导航到 `http://localhost:5000` 并使用浏览器应用。 稍后请将浏览器应用指向应用服务中的远程 API，以便测试 CORS 功能。 浏览器应用的代码位于存储库的 _wwwroot_ 目录中。

在终端按 `Ctrl+C` 可随时停止 ASP.NET Core。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>将应用部署到 Azure

在此步骤中，将已连接 SQL 数据库的 .NET Core 应用程序部署到应用服务。

### <a name="configure-local-git-deployment"></a>配置本地 Git 部署

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-an-app-service-plan"></a>创建应用服务计划

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>创建 Web 应用

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="push-to-azure-from-git"></a>从 Git 推送到 Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-web-app"></a>浏览到 Azure Web 应用

在浏览器中导航到 `http://<app_name>.azurewebsites.net/swagger`，开始使用 Swagger UI。

![在 Azure 应用服务中运行的 ASP.NET Core API](./media/app-service-web-tutorial-rest-api/azure-run.png)

导航到 `http://<app_name>.azurewebsites.net/swagger/v1/swagger.json` 即可看到已部署 API 的 _swagger.json_。

导航到 `http://<app_name>.azurewebsites.net/api/todo` 即可看到已部署 API 正在运行。

## <a name="add-cors-functionality"></a>添加 CORS 功能

接下来，在适用于 API 的应用服务中启用内置的 CORS 支持。

### <a name="test-cors-in-sample-app"></a>在示例应用中测试 CORS

在本地存储库中，打开 _wwwroot/index.html_。

在第 51 中，将 `apiEndpoint` 变量设置为已部署 API 的 URL (`http://<app_name>.azurewebsites.net`)。 在应用服务中将 _\<appname>_ 替换为你的应用名称。

在本地终端窗口中，再次运行示例应用。

```bash
dotnet run
```

导航到浏览器应用 (`http://localhost:5000`)。 在浏览器中打开开发人员工具窗口（在用于 Windows 的 Chrome 中使用 `Ctrl`+`Shift`+`i`），检查“控制台”选项卡。此时会看到错误消息：`No 'Access-Control-Allow-Origin' header is present on the requested resource`。

![浏览器客户端中的 CORS 错误](./media/app-service-web-tutorial-rest-api/cors-error.png)

由于浏览器应用 (`http://localhost:5000`) 和远程资源 (`http://<app_name>.azurewebsites.net`) 的域不匹配，并且由于应用服务中的 API 未发送 `Access-Control-Allow-Origin` 标头，因此浏览器已阻止跨域内容在浏览器应用中加载。

在生产中，浏览器应用会有一个公共 URL 而不是 localhost URL，但对 localhost URL 启用 CORS 的方式与对公共 URL 相同。

### <a name="enable-cors"></a>启用 CORS 

在 Cloud Shell 中，使用 [`az resource update`](/cli/azure/resource#az-resource-update) 命令对客户端的 URL 启用 CORS。 替换 _&lt;appname>_ 占位符。

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.cors.allowedOrigins="['http://localhost:5000']" --api-version 2015-06-01
```

可以在 `properties.cors.allowedOrigins` (`"['URL1','URL2',...]"`) 中设置多个客户端 URL。 也可使用 `"['*']"` 启用所有客户端 URL。

### <a name="test-cors-again"></a>再次测试 CORS

刷新浏览器应用 (`http://localhost:5000`)。 “控制台”窗口中的错误消息现在已消失，可以看到已部署 API 中的数据并与之交互。 远程 API 现在支持对本地运行的浏览器应用使用 CORS。 

![CORS 在浏览器客户端中成功](./media/app-service-web-tutorial-rest-api/cors-success.png)

恭喜！你在包含 CORS 支持的 Azure 应用服务中运行了 API。

## <a name="app-service-cors-vs-your-cors"></a>应用服务 CORS 与你的 CORS 的比较

为了提高灵活性，可以使用自己的 CORS 实用程序而不是应用服务 CORS。 例如，可能需要针对不同的路由或方法指定不同的允许的源。 由于应用服务 CORS 允许你为所有 API 路由和方法指定一组接受的源，因此你需要使用自己的 CORS 代码。 请参阅[启用跨域请求 (CORS)](/aspnet/core/security/cors)，了解 ASP.NET Core 如何这样做。

> [!NOTE]
> 请勿尝试将应用服务 CORS 与你自己的 CORS 代码一起使用。 一起使用时，应用服务 CORS 优先级高，你自己的 CORS 代码将无效。
>
>

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>后续步骤

你已了解：

> [!div class="checklist"]
> * 使用 Azure CLI 创建应用服务资源
> * 使用 Git 将 RESTful API 部署到 Azure
> * 启用应用服务 CORS 支持

转到下一教程，了解如何对用户进行身份验证和授权。

> [!div class="nextstepaction"]
> [教程：对用户进行端到端身份验证和授权](app-service-web-tutorial-auth-aad.md)
