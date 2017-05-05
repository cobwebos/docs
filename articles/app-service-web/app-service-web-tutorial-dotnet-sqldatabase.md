---
title: "使用 SQL 数据库在 Azure 中创建 ASP.NET | Microsoft Docs"
description: "了解如何在 Azure 中运行 ASP.NET 应用，同时使其连接到 SQL 数据库。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/07/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d7006a50d35412021f7e475df526661854b23dc8
ms.lasthandoff: 04/22/2017


---
# <a name="create-an-aspnet-app-in-azure-with-sql-database"></a>使用 SQL 数据库在 Azure 中创建 ASP.NET

本教程演示如何在 Azure 中开发数据驱动的 ASP.NET Web 应用、如何将其连接到 Azure SQL 数据库，以及如何启用数据驱动功能。 完成此流程后，你将能在 [Azure 应用服务](../app-service/app-service-value-prop-what-is.md)中运行 ASP.NET 应用程序，并将其连接到 SQL 数据库。

![已在 Azure Web 应用中发布 ASP.NET 应用程序](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

## <a name="before-you-begin"></a>开始之前

在运行此示例前，请[下载并安装免费 Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>下载示例
此步骤中已下载示例 ASP.NET 应用程序。

### <a name="get-the-sample-project"></a>获取示例项目

单击[此处](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip)下载示例项目。

将下载的 `dotnet-sqldb-tutorial-master.zip` 提取到工作目录中。

> [!TIP]
> 可通过克隆 GitHub 存储库获得相同的示例项目：
>
> ```bash
> git clone https://github.com/Azure-Samples/dotnet-sqldb-tutorial.git
> ```
>
>

此示例项目包含一个简单的 [ASP.NET MVC](https://www.asp.net/mvc) CRUD（创建-读取-更新-删除）应用程序，它在 [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) 上生成。

### <a name="run-the-application"></a>运行应用程序

从提取的目录中，在 Visual Studio 2017 内启动 `dotnet-sqldb-tutorial-master\DotNetAppSqlDb.sln`。

打开示例解决方案后，键入 `F5` 以在浏览器中运行它。

主页中应显示一份简单的待办事项列表。 请尝试向空列表中添加几个待办事项。

![“新建 ASP.NET 项目”对话框](./media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

数据库上下文使用名为 `MyDbConnection` 的连接字符串。 此连接字符串在 `Web.config` 中定义，并在 `Models\MyDatabaseContext.cs` 中引用。 稍后，当将 Azure Web 应用连接到 Azure SQL 数据库时，只需要使用该连接字符串名称。 

## <a name="publish-to-azure-with-sql-database"></a>使用 SQL 数据库发布到 Azure

在“解决方案资源管理器”中，右键单击 “DotNetAppSqlDb”项目，然后选择“发布”。

![从解决方案资源管理器发布](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

确保已选择“Microsoft Azure 应用服务”，然后单击“发布”。

![从项目概述页发布](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-to-app-service.png)

此时将打开“创建应用服务”对话框，帮助你创建所需的所有 Azure 资源，以便在 Azure 中运行 ASP.NET Web 应用。

### <a name="sign-in-to-azure"></a>登录 Azure

在“创建应用服务”对话框中单击“添加帐户”，然后登录到你的 Azure 订阅。 如果已登录到 Microsoft 帐户，请确保该帐户包含你的 Azure 订阅。 如果已登录的 Microsoft 帐户不包含你的 Azure 订阅，请单击该帐户以添加正确的帐户。
   
![登录 Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

登录后，可在此对话框中创建 Azure Web 应用所需的所有资源。

### <a name="create-a-resource-group"></a>创建资源组

首先需要一个_资源组_。 

> [!NOTE] 
> 资源组是在其中部署和管理 Azure 资源（如 Web 应用、数据库和存储帐户）的逻辑容器。
>
>

在“资源组”旁边单击“新建”。

将资源组命名为 **myResourceGroup**，然后单击“确定”。

### <a name="create-an-app-service-plan"></a>创建应用服务计划

Azure Web 应用还需要一个_应用服务计划_。 

> [!NOTE]
> 应用服务计划表示用于托管应用的物理资源集合。 分配到应用服务计划的所有应用将共享该计划定义的资源，在托管多个应用时可以节省成本。 
>
> 应用服务计划定义：
>
> - 区域（北欧、美国东部、东南亚）
> - 实例大小（小、中、大）
> - 规模计数（一个、两个、三个实例，等等） 
> - SKU（免费、共享、基本、标准、高级）
>
>

在“应用服务计划”旁边单击“新建”。 

在“配置应用服务计划”对话框中，使用以下设置配置新的应用服务计划：

- **应用服务计划**：键入 **myAppServicePlan**。 
- **位置**：选择“西欧”或想要使用的其他任何区域。
- **大小**：选择“免费”或想要使用的其他任何[定价层](https://azure.microsoft.com/pricing/details/app-service/)。

单击 **“确定”**。

![创建应用服务计划](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

### <a name="configure-the-web-app-name"></a>配置 Web 应用名称

在“Web 应用名称”中键入唯一的应用名称。 此名称将用作应用 (`<app_name>.azurewebsites.net`) 的默认 DNS 名称的一部分，因此，需要在 Azure 中的所有应用之间保持唯一。 稍后，可以先将自定义域名映射到应用，然后向用户公开该域名。

也可以接受自动生成的名称，这已是一个唯一的名称。

若要准备进行下一步，请单击“浏览其他 Azure 服务”。

![配置 Web 应用名称](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-name.png)

### <a name="create-a-sql-server-instance"></a>创建 SQL Server 实例

在“服务”选项卡上，单击“SQL 数据库”旁的“+”图标。 

在“配置 SQL 数据库”对话框中，单击“SQL Server”旁的“新建”。 

在“服务器名称”中，键入唯一名称。 此名称将用作数据库服务器 (`<server_name>.database.windows.net`) 的默认 DNS 名称的一部分，因此，需要在 Azure 中的所有 SQL Server 实例之间保持唯一。 

按个人喜好配置剩余字段，然后单击“确定”。

![创建 SQL Server 实例](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

### <a name="configure-the-sql-database"></a>配置 SQL 数据库

在“数据库名称”中，键入 `myToDoAppDb` 或任何喜欢的名称。

在“连接字符串名称”中，键入 `MyDbConnection`。 此名称必须与 `Models\MyDatabaseContext.cs` 中引用的连接字符串相匹配。

![配置 SQL 数据库](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

### <a name="create-and-publish-the-web-app"></a>创建并发布 Web 应用

单击“创建” 。 

向导完成创建 Azure 资源后，会自动将 ASP.NET 应用程序发布到 Azure（首次发布），然后在默认浏览器中启动发布的 Azure Web 应用。

请尝试向空列表中添加几个待办事项。

![已在 Azure Web 应用中发布 ASP.NET 应用程序](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

祝贺你！ 数据驱动 ASP.NET 应用程序当前在 Azure 应用服务中实时运行。

## <a name="access-the-sql-database-locally"></a>本地访问 SQL 数据库

使用 Visual Studio 可在“SQL Server 对象资源管理器”中轻松浏览和管理自己的新 SQL 数据库。

### <a name="create-a-database-connection"></a>创建数据库连接

键入 `Ctrl`+`\`、`Ctrl`+`S`，以打开“SQL Server 对象资源管理器”。

在“SQL Server 对象资源管理器”顶部，单击“添加 SQL Server”按钮。

### <a name="configure-the-database-connection"></a>配置数据库连接

在“连接”对话框中，展开“Azure”节点。 此处列出了 Azure 中的全部 SQL 数据库。

选择先前创建的 SQL 数据库。 底部将自动填充前面使用过的连接。

键入先前使用的数据库管理员密码，然后单击“连接”。

![通过 Visual Studio 配置数据库连接](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

### <a name="allow-client-connection-from-your-computer"></a>允许来自你的计算机的客户端连接

系统将打开“新建防火墙规则”对话框。 默认情况下，SQL Server 实例仅允许来自 Azure 服务的连接，如 Azure Web 应用。 若要直接从 Visual Studio 连接数据库，则需在 SQL Server 实例上创建防火墙规则，以允许本地计算机的公共 IP 地址。

在 Visual Studio 中，这很容易实现。 对话框中已填充了你的计算机的公共 IP 地址。

请确保选中“添加我的客户端 IP”，然后单击“确定”。 

![为 SQL Server 实例设置防火墙](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

Visual Studio 成功为 SQL Server 实例创建防火墙设置后，连接将立即显示在“SQL Server 对象资源管理器”中。

可在此处执行最常见的数据库操作，如运行查询、创建视图和存储过程等。 下面的示例演示如何查看数据库数据。 

![探索 SQL 数据库对象](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>使用 Code First 迁移更新应用

此步骤中将使用实体框架中的 Code First 迁移对数据库架构进行更改，并将其发布至 Azure。

### <a name="update-your-data-model"></a>更新数据模型

在代码编辑器中打开 `Models\Todo.cs`。 将以下属性添加到 `ToDo` 类：

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>本地运行 Code First 迁移

接下来，运行几个命令来更新 localdb 数据库。 

在“工具”菜单中，单击“NuGet 包管理器” > “包管理器控制台”。 控制台通常在底部窗口中打开。

启用 Code First 迁移，如下所示：

```PowerShell
Enable-Migrations
```

添加迁移，如下所示：

```PowerShell
Add-Migration AddProperty
```

更新 localdb 数据库，如下所示：

```PowerShell
Update-Database
```

通过按 `F5` 运行应用程序来测试更改。

如果应用程序加载未出错，则 Code First 迁移成功。 但页面看上去仍没有变化，这是因为应用程序逻辑尚未使用新属性。 

### <a name="use-the-new-property"></a>使用新属性

为了使用 `Done` 属性，需要对代码做一些更改。 简单起见，本教程中将仅更改 `Index` 和 `Create` 视图，以便在操作中查看属性。

打开 `Controllers\TodosController.cs`。

找到 `Create()` 方法，并将 `Done` 添加到 `Bind` 属性中的属性列表。 完成后，`Create()` 方法签名应如下所示：

```csharp
public ActionResult Create([Bind(Include = "id,Description,CreatedDate,Done")] Todo todo)
```

打开 `Views\Todos\Create.cshtml`。

在 Razor 代码中，应依次看见使用 `model.Description` 的 `<div class="form-group">` 标记和使用 `model.CreatedDate` 的 `<div class="form-group">` 标记。 紧跟在这两个标记之后，添加另一个使用 `model.Done` 的 `<div class="form-group">` 标记，如下所示：

```csharp
<div class="form-group">
    @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
    <div class="col-md-10">
        <div class="checkbox">
            @Html.EditorFor(model => model.Done)
            @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
        </div>
    </div>
</div>
```

打开 `Views\Todos\Index.cshtml`。

搜索空的 `<th></th>` 标记。 在此标记的正上方，添加下列 Razor 代码：

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

查找包含 `Html.ActionLink()` 帮助器方法的 `<td>` 标记。 在此标记的正上方，添加下列 Razor 代码：

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

这就是要在 `Index` 和 `Create` 视图中查看更改所需的全部操作。 

再次键入 `F5` 以运行应用程序。

现在应能够添加待办事项，勾选“完成”。 然后，它应作为已完成项在主页中显示。 请记住，这是目前能进行的所有操作，因为之前未更改 `Edit` 视图。

### <a name="enable-code-first-migrations-in-azure"></a>在 Azure 中启用 Code First 迁移

代码更改生效（包括数据库迁移）后，将其发布至 Azure Web 应用，并仍使用 Code First 迁移更新 SQL 数据库。

与先前的操作相同，右键单击项目，然后选择“发布”。

单击“设置”打开发布向导。

![打开发布设置](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

在向导中，单击“下一步”。

请确保在 **MyDatabaseContext (MyDbConnection)** 中填充 SQL 数据库的连接字符串。 可能需要从下拉列表中选择“myToDoAppDb”数据库。 

选择“执行 Code First 迁移(应用程序启动时运行)”，然后单击“保存”。

![在 Azure Web 应用中启用 Code First 迁移](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

### <a name="publish-your-changes"></a>发布更改

现已在 Azure Web 应用中启用了 Code First 迁移，可直接发布代码更改。

在发布页中单击“发布”。

请尝试再次创建新的待办事项并选择“完成”，然后，它们将作为已完成项显示在主页中。

![Code First 迁移后的 Azure Web 应用](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

> [!NOTE]
> 请注意，所有现有待办事项仍将显示。 重新发布 ASP.NET 应用程序时，SQL 数据库中的现有数据不会丢失。 此外，Code First 迁移仅更改数据架构，而使现有数据保持不变。
>
>

## <a name="stream-application-logs"></a>流式传输应用程序日志

可直接通过 Azure Web 应用将跟踪消息流式传输到 Visual Studio。

打开 `Controllers\TodosController.cs`。

请注意，每个操作都以 `Trace.WriteLine()` 方法开头。 添加此代码的目的是演示将跟踪消息添加至 Azure Web 应用非常简单。

### <a name="open-server-explorer"></a>打开服务器资源管理器

可在“服务器资源管理器”中为 Azure Web 应用配置日志记录。 

键入 `Ctrl`+`Alt`+`S` 可将它打开。

### <a name="enable-log-streaming"></a>启用日志流式传输

在“服务器资源管理器”中，展开“Azure” > “应用服务”。

展开“myResourceGroup”资源组，该资源组在首次创建 Azure Web 应用时创建。

右键单击 Azure Web 应用，然后选择“查看流式传输日志”。

![启用日志流式传输](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

现在，日志已流式传输到“输出”窗口。 

![输出窗口中的日志流式传输](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

但还无法查看任何跟踪消息。 因为当首先选择“查看流式传输日志”时，Azure Web 应用将跟踪级别设置为 `Error`，此级别只记录错误事件（使用 `Trace.TraceError()` 方法）。

### <a name="change-trace-levels"></a>更改跟踪级别

若要更改跟踪级别以输出其他跟踪消息，请返回“服务器资源管理器”。

再次右键单击 Azure Web 应用，然后选择“设置”。

在“应用程序日志记录(文件系统)”下拉列表中，选择“详细”。 单击“保存” 。

![将跟踪级别更改为详细](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

> [!TIP]
> 可试验不同的跟踪级别，以查看每个级别分别显示哪些类型的消息。 例如，“信息”级别包括 `Trace.TraceInformation()`、`Trace.TraceWarning()` 和 `Trace.TraceError()` 创建的所有日志，但不包括 `Trace.WriteLine()` 创建的日志。
>
>

在浏览器中，尝试在 Azure 中的待办事项列表应用程序周围进行单击。 现在，跟踪消息已流式传输到 Visual Studio 中的“输出”窗口。

```
Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
```

### <a name="stop-log-streaming"></a>停止日志流式传输

若要停止日志流式传输服务，请单击“输出”窗口中的“停止监视”按钮。

![停止日志流式传输](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-web-app"></a>管理 Azure Web 应用

转到 Azure 门户查看已创建的 Web 应用。 

为此，请登录到 [https://portal.azure.com](https://portal.azure.com)。

从左侧菜单中单击“应用服务”，然后单击 Azure Web 应用的名称。

![在门户中导航到 Azure Web 应用](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

现已进入 Web 应用的_边栏选项卡_（水平打开的门户页）。 

默认情况下，Web 应用的边栏选项卡显示“概述”页。 在此页中可以查看应用的运行状况。 在此处还可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。 边栏选项卡左侧的选项卡显示可以打开的不同配置页。 

![Azure 门户中的应用服务边栏选项卡](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

边栏选项卡中的这些选项卡显示了可添加到 Web 应用的许多强大功能。 以下列表只是列出了一部分可用的功能：

- 映射自定义 DNS 名称
- 绑定自定义 SSL 证书
- 配置持续部署
- 扩展和缩减
- 添加用户身份验证

## <a name="next-steps"></a>后续步骤

浏览预先创建的 [Web 应用 PowerShell 脚本](app-service-powershell-samples.md)。
