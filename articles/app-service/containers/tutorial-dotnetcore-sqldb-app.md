---
title: "在 Linux 上的 Azure 应用服务中生成 .NET Core 和 SQL 数据库 Web 应用 | Microsoft Docs"
description: "了解如何在 Linux 上的 Azure 应用服务中运行 .NET Core 应用，同时使其连接到 SQL 数据库。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: syntaxc4
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 10/10/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: d6c679518bfc712e6a08ffae722b0cc5d2b038aa
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2017
---
# <a name="build-a-net-core-and-sql-database-web-app-in-azure-app-service-on-linux"></a>在 Linux 上的 Azure 应用服务中生成 .NET Core 和 SQL 数据库 Web 应用

[Linux 应用服务](app-service-linux-intro.md)使用 Linux 操作系统，提供高度可缩放的自修补 Web 托管服务。 本教程演示如何创建 .NET Core Web 应用，并将其连接至 SQL 数据库。 完成操作后，将拥有一个在 Linux 应用服务中运行的 .NET Core MVC 应用。

![在 Azure 应用服务中运行的应用](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

学习如何：

> [!div class="checklist"]
> * 在 Azure 中创建 SQL 数据库。
> * 将 .NET Core 应用连接到 SQL 数据库
> * 将应用部署到 Azure
> * 更新数据模型并重新部署应用
> * 从 Azure 流式传输诊断日志
> * 在 Azure 门户中管理应用

## <a name="prerequisites"></a>先决条件

完成本教程：

1. [安装 Git](https://git-scm.com/)
1. [安装 .NET Core SDK 1.1.2](https://github.com/dotnet/core/blob/master/release-notes/download-archives/1.1.2-download.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-local-net-core-app"></a>创建本地 .NET Core 应用

在此步骤中，将设置本地 .NET Core 项目。

### <a name="clone-the-sample-application"></a>克隆示例应用程序

在终端窗口中，通过 `cd` 转到工作目录。

运行以下命令来克隆示例存储库，并更改为其根。

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

此示例项目包含使用[实体框架核心](https://docs.microsoft.com/ef/core/)的基本 CRUD（创建-读取-更新-删除）应用。

### <a name="run-the-application"></a>运行应用程序

运行以下命令，安装所需的包，运行数据库迁移并启动应用程序。

```bash
dotnet restore
dotnet ef database update
dotnet run
```

在浏览器中导航至 `http://localhost:5000`。 选择“新建”链接，创建一对待办事项。

![已成功连接到 SQL 数据库](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

在终端按 `Ctrl+C`，随时停止 .NET Core。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>创建生产 SQL 数据库

此步骤在 Azure 中创建一个 SQL 数据库。 应用部署到 Azure 后，它将使用该云数据库。

对于 SQL 数据库，本教程使用 [Azure SQL 数据库](/azure/sql-database/)。

### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>创建 SQL 数据库逻辑服务器

在 Cloud Shell 中，使用 [az sql server create](/cli/azure/sql/server?view=azure-cli-latest#az_sql_server_create) 命令创建 SQL 数据库逻辑服务器。

将 \<server_name> 占位符替换为唯一的 SQL 数据库名称。 此名称用作 SQL 数据库终结点 `<server_name>.database.windows.net` 的一部分，因此必须在 Azure 的所有逻辑服务器中具有唯一性。 它只能包含小写字母、数字及连字符(-)，长度必须为 3 到 50 个字符。 此外，将 \<db_username> 和 \<db_password> 分别替换为所选用户名和密码。 


```azurecli-interactive
az sql server create --name <server_name> --resource-group myResourceGroup --location "West Europe" --admin-user <db_username> --admin-password <db_password>
```

创建 SQL 数据库逻辑服务器后，Azure CLI 会显示类似于以下示例的信息：

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server_name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server_name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>配置服务器防火墙规则

使用 [az sql server firewall create](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az_sql_server_firewall_rule_create) 命令创建 [Azure SQL 数据库服务器级防火墙规则](../../sql-database/sql-database-firewall-configure.md)。 若同时将起始 IP 和结束 IP 设置为 0.0.0.0，防火墙将仅对其他 Azure 资源开启。 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server_name> --name AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="create-a-database"></a>创建数据库

使用 [az sql db create](/cli/azure/sql/db?view=azure-cli-latest#az_sql_db_create) 命令在服务器中创建 [S0 性能级别](../../sql-database/sql-database-service-tiers.md)的数据库。

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server_name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>创建连接字符串

将下列字符串替换为先前所使用的 \<server_name>、\<db_username> 和 \<db_password>。

```
Server=tcp:<server_name>.database.windows.net,1433;Initial Catalog=coreDB;Persist Security Info=False;User ID=<db_username>;Password=<db_password>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

这是 .NET Core 应用的连接字符串。 将其进行复制，留待稍后使用。

## <a name="deploy-app-to-azure"></a>将应用部署到 Azure

在此步骤中，将已连接 SQL 数据库的 .NET Core 应用程序部署到 Linux 应用服务。

### <a name="configure-local-git-deployment"></a>配置本地 Git 部署

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>创建应用服务计划

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>创建 Web 应用

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-no-h.md)] 

### <a name="configure-an-environment-variable"></a>配置环境变量

要为 Azure 应用设置连接字符串，请使用 Cloud Shell 中的 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) 命令。 在下列命令中，将 \<app name> 和 \<connection_string> 参数替换为先前创建的连接字符串。

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='<connection_string>' --connection-string-type SQLServer
```

接下来，将 `ASPNETCORE_ENVIRONMENT` 应用设置设置为_生产_。 由于为本地开发环境使用 SQLLite，并为 Azure 环境使用 SQL 数据库，所以通过此设置，可了解是否正在 Azure 中运行。

下面的示例在 Azure Web 应用中配置 `ASPNETCORE_ENVIRONMENT` 应用设置。 替换 \<app_name> 占位符。

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

### <a name="connect-to-sql-database-in-production"></a>在生产中连接到 SQL 数据库

在本地存储库中，打开 Startup.cs 并查找下列代码：

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

将其替换为下列代码，该代码使用之前配置的环境变量。

```csharp
// Use SQL Database if in Azure, otherwise, use SQLLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<DotNetCoreSqlDbContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<DotNetCoreSqlDbContext>(options =>
            options.UseSqlite("Data Source=MvcMovie.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<DotNetCoreSqlDbContext>().Database.Migrate();
```

如果此代码检测到当前正在生产中运行（指 Azure 环境），则会使用先前配置的连接字符串连接到 SQL 数据库。

在 Azure 中运行时可利用 `Database.Migrate()` 调用，因为它会根据迁移配置自动创建 .NET Core 应用所需的数据库。 

保存所做更改。

### <a name="push-to-azure-from-git"></a>从 Git 推送到 Azure

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

使用 Web 浏览器浏览到已部署的 Web 应用。

```bash
http://<app_name>.azurewebsites.net
```

添加几个待办事项。

![在 Azure 应用服务中运行的应用](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**祝贺你！** 正在 Linux 应用服务中运行数据驱动的 .NET Core 应用。

## <a name="update-locally-and-redeploy"></a>在本地更新并重新部署

在此步骤中，将对数据库架构做出更改，并将其发布至 Azure。

### <a name="update-your-data-model"></a>更新数据模型

在代码编辑器中打开 _Models\Todo.cs_。 将以下属性添加到 `ToDo` 类：

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>本地运行 Code First 迁移

运行几个命令更新本地数据库。

```bash
dotnet ef migrations add AddProperty
```

更新本地数据库：

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>使用新属性

为使用 `Done` 属性，需要对代码做更多更改。 简单起见，本教程中将仅更改 `Index` 和 `Create` 视图，以便在操作中查看属性。

打开 _Controllers\TodosController.cs_。

找到 `Create()` 方法，并将 `Done` 添加到 `Bind` 属性中的属性列表。 完成后，`Create()` 方法签名应如下代码所示：

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

打开 _Views\Todos\Create.cshtml_。

在 Razor 代码中，应能看到用于 `Description` 的 `<div class="form-group">` 元素，以及另一个用于 `CreatedDate` 的 `<div class="form-group">` 元素。 紧跟在这两个元素之后，添加另一个用于 `Done` 的 `<div class="form-group">` 元素：

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

打开 _Views\Todos\Index.cshtml_。

搜索空的 `<th></th>` 元素。 在此元素的正上方，添加下列 Razor 代码：

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

查找包含 `asp-action` 标记帮助程序的 `<td>` 元素。 在此元素的正上方，添加下列 Razor 代码：

```csharp
<td>
    @Html.DisplayFor(modelItem => item.CreatedDate)
</td>
```

这就是要在 `Index` 和 `Create` 视图中查看更改所需的全部操作。

### <a name="test-your-changes-locally"></a>在本地测试更改

在本地运行应用。

```bash
dotnet run
```

在浏览器中，导航到 `http://localhost:5000/`。 你现在可以添加一个待办事项，并检查“完成”。 然后，它应作为已完成项在主页中显示。 请牢记，由于未更改`Edit`视图，`Edit`视图不显示`Done`字段。

### <a name="publish-changes-to-azure"></a>发布对 Azure 所做的更改

```bash

git commit -am "added done field"
git push azure master
```

一旦 `git push` 完成，请导航至 Azure Web 应用，试用新功能。

![Code First 迁移后的 Azure Web 应用](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

所有现有待办事项仍将显示。 重新发布 .NET Core 应用时，SQL 数据库中的现有数据不会丢失。 此外，实体框架核心迁移仅更改数据架构，而使现有数据保持不变。

## <a name="manage-your-azure-web-app"></a>管理 Azure Web 应用

转到 [Azure 门户](https://portal.azure.com)查看已创建的 Web 应用。

从左侧菜单中单击“应用服务”，并单击 Azure Web 应用的名称。

![在门户中导航到 Azure Web 应用](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

默认情况下，门户将显示 Web 应用“概述”页。 在此页中可以查看应用的运行状况。 在此处还可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。 该页左侧的选项卡显示可以打开的不同配置页。

![Azure 门户中的应用服务页](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>后续步骤

你已了解：

> [!div class="checklist"]
> * 在 Azure 中创建 SQL 数据库。
> * 将 .NET Core 应用连接到 SQL 数据库
> * 将应用部署到 Azure
> * 更新数据模型并重新部署应用
> * 将日志从 Azure 流式传输到终端
> * 在 Azure 门户中管理应用

转到下一教程，了解如何向 Web 应用映射自定义 DNS 名称。

> [!div class="nextstepaction"]
> [将现有的自定义 DNS 名称映射到 Azure Web 应用](../app-service-web-tutorial-custom-domain.md)