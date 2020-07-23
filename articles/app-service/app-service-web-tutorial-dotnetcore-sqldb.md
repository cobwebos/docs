---
title: 教程：将 ASP.NET Core 与 SQL 数据库配合使用
description: 了解如何在 Azure 应用服务中运行 .NET Core 应用，同时使其连接到 SQL 数据库。
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/27/2020
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: c020e49b12784e628661bff61fe344df0ac6049a
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905976"
---
# <a name="tutorial-build-an-aspnet-core-and-sql-database-app-in-azure-app-service"></a>教程：在 Azure 应用服务中生成 ASP.NET Core 和 SQL 数据库应用

> [!NOTE]
> 本文将应用部署到 Windows 上的应用服务。 若要部署到 _Linux_ 上的应用服务，请参阅[在 Linux 上的 Azure 应用服务中生成 .NET Core 和 SQL 数据库应用](./containers/tutorial-dotnetcore-sqldb-app.md)。
>

[应用服务](overview.md)在 Azure 中提供高度可缩放、自修补的 Web 托管服务。 本教程演示如何创建 .NET Core 应用，并将其连接至 SQL 数据库。 完成操作后，将拥有一个在应用服务中运行的 .NET Core MVC 应用。

![在应用服务中运行的应用](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure 中创建 SQL 数据库。
> * 将 .NET Core 应用连接到 SQL 数据库
> * 将应用部署到 Azure
> * 更新数据模型并重新部署应用
> * 从 Azure 流式传输诊断日志
> * 在 Azure 门户中管理应用

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

为完成此教程：

* <a href="https://git-scm.com/" target="_blank">安装 Git</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">安装最新的 .NET Core 3.1 SDK</a>

## <a name="create-local-net-core-app"></a>创建本地 .NET Core 应用

在此步骤中，你将设置本地 .NET Core 项目。

### <a name="clone-the-sample-application"></a>克隆示例应用程序

在终端窗口中，通过 `cd` 转到工作目录。

运行以下命令来克隆示例存储库，并转到其根目录。

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

此示例项目包含使用 [Entity Framework Core](https://docs.microsoft.com/ef/core/) 的基本 CRUD（创建-读取-更新-删除）应用。

### <a name="run-the-application"></a>运行应用程序

运行以下命令，安装所需的包，运行数据库迁移并启动应用程序。

```bash
dotnet tool install -g dotnet-ef
dotnet ef database update
dotnet run
```

在浏览器中导航至 `http://localhost:5000` 。 选择“新建”链接，创建几个待办事项。

![已成功连接到 SQL 数据库](./media/app-service-web-tutorial-dotnetcore-sqldb/local-app-in-browser.png)

在终端按 `Ctrl+C`，随时停止 .NET Core。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>创建生产环境 SQL 数据库

此步骤在 Azure 中创建一个 SQL 数据库。 应用部署到 Azure 后，它将使用该云数据库。

对于 SQL 数据库，本教程使用 [Azure SQL 数据库](/azure/sql-database/)。

### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>创建 SQL 数据库逻辑服务器

在 Cloud Shell 中，使用 [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) 命令创建 SQL 数据库逻辑服务器。

将 \<server-name> 占位符替换为唯一的 SQL 数据库名称 。 此名称将用作全局唯一的 SQL 数据库终结点 (`<server-name>.database.windows.net`) 的一部分。 有效字符为 `a`-`z`、`0`-`9` 和 `-`。 此外，将 \<db-username> 和 \<db-password> 替换为所选的用户名和密码 。 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

创建 SQL 数据库逻辑服务器后，Azure CLI 会显示类似于以下示例的信息：

<pre>
{
  "administratorLogin": "&lt;db-username&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;server-name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/&lt;server-name&gt;",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "&lt;server-name&gt;",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
</pre>

### <a name="configure-a-server-firewall-rule"></a>配置服务器防火墙规则

使用 [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create) 命令创建 [Azure SQL 数据库服务器级防火墙规则](../sql-database/sql-database-firewall-configure.md)。 若同时将起始 IP 和结束 IP 设置为 0.0.0.0，防火墙将仅对其他 Azure 资源开启。 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> 你甚至可以让防火墙规则更严格，即[只使用应用所使用的出站 IP 地址](overview-inbound-outbound-ips.md#find-outbound-ips)。
>

在 Cloud Shell 中再次运行该命令（将 \<your-ip-address> 替换为[本地 IPv4 IP 地址](https://www.whatsmyip.org/)），以便从本地计算机进行访问。

```azurecli-interactive
az sql server firewall-rule create --name AllowLocalClient --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="create-a-database"></a>创建数据库

使用 [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create) 命令在服务器中创建 [S0 性能级别](../sql-database/sql-database-service-tiers-dtu.md)的数据库。

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>创建连接字符串

使用 [`az sql db show-connection-string`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-show-connection-string) 命令获取连接字符串。

```azurecli-interactive
az sql db show-connection-string --client ado.net --server cephalin-core --name coreDB
```

在命令输出中，将 \<username> 和 \<password> 替换为你先前使用的数据库管理员凭据 。

这是 .NET Core 应用的连接字符串。 将其进行复制，留待稍后使用。

### <a name="configure-app-to-connect-to-production-database"></a>配置应用以连接到生产数据库

在本地存储库中，打开 Startup.cs 并查找下列代码：

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

将其替换为以下代码。

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
```

> [!IMPORTANT]
> 对于需要横向扩展的生产应用，请遵循[在生产中应用迁移](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production)中的最佳做法。
> 

### <a name="run-database-migrations-to-the-production-database"></a>运行到生产数据库的数据库迁移

应用当前连接到本地 Sqlite 数据库。 配置 Azure SQL 数据库后，请重新创建以其为目标的初始迁移。 

在存储库根目录中运行以下命令。 将 \<connection-string> 替换为之前创建的连接字符串。

```
# Delete old migrations
rm Migrations -r
# Recreate migrations
dotnet ef migrations add InitialCreate

# Set connection string to production database
# PowerShell
$env:ConnectionStrings:MyDbConnection="<connection-string>"
# CMD (no quotes)
set ConnectionStrings:MyDbConnection=<connection-string>
# Bash (no quotes)
export ConnectionStrings__MyDbConnection=<connection-string>

# Run migrations
dotnet ef database update
```

### <a name="run-app-with-new-configuration"></a>使用新配置运行应用

现在，数据库迁移将在生产数据库中运行，请通过运行以下命令来测试应用：

```
dotnet run
```

在浏览器中导航至 `http://localhost:5000` 。 选择“新建”链接，创建几个待办事项。 应用现在正在读取数据并将数据写入生产数据库。

提交本地更改，然后将其提交到 Git 存储库。 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

现在已准备好部署代码。

## <a name="deploy-app-to-azure"></a>将应用部署到 Azure

在此步骤中，将已连接 SQL 数据库的 .NET Core 应用程序部署到应用服务。

### <a name="configure-local-git-deployment"></a>配置本地 Git 部署

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>创建应用服务计划

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>创建 Web 应用

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="configure-connection-string"></a>配置连接字符串

若要为 Azure 应用设置连接字符串，请使用 Cloud Shell 中的 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 命令。 在下列命令中，将 \<app-name> 和 \<connection-string> 参数替换为先前创建的连接字符串 。

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app-name> --settings MyDbConnection="<connection-string>" --connection-string-type SQLAzure
```

在 ASP.NET Core 中，可以通过标准模式使用此命名连接字符串 (`MyDbConnection`)，就像在 appsettings.json 中指定的任何连接字符串一样。 在本例中，`MyDbConnection` 也在 appsettings.json 中定义。 在应用服务中运行时，应用服务中定义的连接字符串优先于 appsettings.json 中定义的连接字符串。 此代码在本地开发过程中使用 appsettings.json 值，相同的代码在部署时使用应用服务值。

若要了解如何在代码中引用连接字符串，请参阅[配置应用以连接到生产数据库](#configure-app-to-connect-to-production-database)。

### <a name="push-to-azure-from-git"></a>从 Git 推送到 Azure

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 268, done.
Counting objects: 100% (268/268), done.
Compressing objects: 100% (171/171), done.
Writing objects: 100% (268/268), 1.18 MiB | 1.55 MiB/s, done.
Total 268 (delta 95), reused 251 (delta 87), pack-reused 0
remote: Resolving deltas: 100% (95/95), done.
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '64821c3558'.
remote: Generating deployment script.
remote: Project file path: .\DotNetCoreSqlDb.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: App container will begin restart within 10 seconds.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      master -> master
</pre>

### <a name="browse-to-the-azure-app"></a>转到 Azure 应用

使用 Web 浏览器转到已部署的应用。

```bash
http://<app-name>.azurewebsites.net
```

添加几个待办事项。

![在应用服务中运行的应用](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

祝贺你！ 数据驱动的 .NET Core 应用已经在你的应用服务中运行了。

## <a name="update-locally-and-redeploy"></a>在本地更新并重新部署

在此步骤中，将对数据库架构做出更改，并将其发布至 Azure。

### <a name="update-your-data-model"></a>更新数据模型

在代码编辑器中打开 _Models\Todo.cs_。 将以下属性添加到 `ToDo` 类：

```csharp
public bool Done { get; set; }
```

### <a name="rerun-database-migrations"></a>重新运行数据库迁移

运行几个命令以更新生产数据库。

```bash
dotnet ef migrations add AddProperty
dotnet ef database update
```

### <a name="use-the-new-property"></a>使用新属性

为使用 `Done` 属性，需要对代码做一些更改。 简单起见，本教程中将仅更改 `Index` 和 `Create` 视图，以便在操作过程中查看属性。

打开 _Controllers\TodosController.cs_。

找到 `Create([Bind("ID,Description,CreatedDate")] Todo todo)` 方法，并将 `Done` 添加到 `Bind` 属性中的属性列表。 完成后，`Create()` 方法签名应如下面的代码所示：

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
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

这就是要在 `Index` 和 `Create` 视图中查看更改所需的全部操作。

### <a name="test-your-changes-locally"></a>在本地测试更改

在本地运行应用。

```bash
dotnet run
```

在浏览器中，导航到 `http://localhost:5000/`。 你现在可以添加一个待办事项，并检查''Done''。 然后，它会在主页中显示为已完成的项。 请记住，由于未更改`Edit`视图，`Edit`视图不显示`Done`字段。

### <a name="publish-changes-to-azure"></a>发布对 Azure 所做的更改

```bash
git add .
git commit -m "added done field"
git push azure master
```

`git push` 完成后，请导航至应用服务应用，尝试添加一个待办事项并选中“Done”。

![Code First 迁移后的 Azure 应用](./media/app-service-web-tutorial-dotnetcore-sqldb/this-one-is-done.png)

所有现有待办事项仍将显示。 重新发布 ASP.NET Core 应用时，SQL 数据库中的现有数据不会丢失。 此外，实体框架核心迁移仅更改数据架构，而使现有数据保持不变。

## <a name="stream-diagnostic-logs"></a>流式传输诊断日志

当 ASP.NET Core 应用在 Azure 应用服务中运行时，可以将控制台日志传输到 Cloud Shell。 如此，可以获得相同的诊断消息，以便调试应用程序错误。

示例项目已遵循了 [Azure 中的 ASP.NET Core 日志记录](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider)中的指南，并且进行了两个配置更改：

- 在 *DotNetCoreSqlDb.csproj* 中包含了对 `Microsoft.Extensions.Logging.AzureAppServices` 的引用。
- 在 *Program.cs* 中调用 `loggerFactory.AddAzureWebAppDiagnostics()`。

若要将应用服务中的 ASP.NET Core [日志级别](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-level)从默认级别 `Error` 设置为 `Information`，请在 Cloud Shell 中使用 [`az webapp log config`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config) 命令。

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --application-logging true --level information
```

> [!NOTE]
> 项目的日志级别在 *appsettings.json* 中已设置为 `Information`。
> 

若要启动日志流式处理，请在 Cloud Shell 中使用 [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) 命令。

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

启动日志流式处理后，请在浏览器中刷新 Azure 应用，以获取一些 Web 流量。 现在可以看到通过管道传送到终端的控制台日志。 如果没有立即看到控制台日志，请在 30 秒后重新查看。

若要随时停止日志流式处理，请键入 `Ctrl`+`C`。

有关自定义 ASP.NET Core 日志的详细信息，请参阅 [ASP.NET Core 中的日志记录](https://docs.microsoft.com/aspnet/core/fundamentals/logging)。

## <a name="manage-your-azure-app"></a>管理 Azure 应用

若要查看所创建的应用，请在 [Azure 门户](https://portal.azure.com)中，搜索并选择“应用服务”"。

![在 Azure 门户中选择应用服务](./media/app-service-web-tutorial-dotnetcore-sqldb/app-services.png)

在“应用服务”页上，选择 Azure 应用的名称。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-dotnetcore-sqldb/access-portal.png)

默认情况下，门户将显示应用的“概述”页。 在此页中可以查看应用的运行状况。 在此处还可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。 该页左侧的选项卡显示可以打开的不同配置页。

![Azure 门户中的应用服务页](./media/app-service-web-tutorial-dotnetcore-sqldb/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

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

继续学习下一篇教程，了解如何将自定义 DNS 名称映射到应用。

> [!div class="nextstepaction"]
> [教程：将自定义 DNS 名称映射到应用](app-service-web-tutorial-custom-domain.md)

更多资源：

> [!div class="nextstepaction"]
> [配置 ASP.NET Core 应用](configure-language-dotnetcore.md)
