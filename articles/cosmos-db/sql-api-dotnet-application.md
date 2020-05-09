---
title: 使用 Azure Cosmos DB 的 ASP.NET Core MVC Web 应用教程
description: ASP.NET Core MVC 教程介绍如何创建使用 Azure Cosmos DB 的 MVC Web 应用程序。 我们将存储 JSON 并从 Azure 应用服务上托管的待办事项应用中访问数据 - ASP NET Core MVC 教程分步说明。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: sngun
ms.openlocfilehash: 1f2051addfa1266b754d230c3804834c63f89002
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "78274076"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>教程：通过 .NET SDK 开发使用 Azure Cosmos DB 的 ASP.NET Core MVC Web 应用程序

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

本教程介绍如何使用 Azure Cosmos DB 通过 Azure 上托管的 ASP.NET MVC 应用程序存储和访问数据。 在本教程中，请使用 .NET SDK V3。 下图显示将要使用本文中的示例生成的网页：

![按本教程创建的待办事项列表 MVC Web 应用程序屏幕截图 - ASP NET Core MVC 分步教程](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

如果没有时间完成本教程，可以从 [GitHub][GitHub] 下载完整的示例项目。

本教程涉及：

> [!div class="checklist"]
>
> * 创建 Azure Cosmos 帐户
> * 创建 ASP.NET Core MVC 应用
> * 将应用连接到 Azure Cosmos DB
> * 对数据执行创建、读取、更新和删除 (CRUD) 操作

> [!TIP]
> 本教程假定你先前有使用 ASP.NET Core MVC 和 Azure 应用服务的经验。 如果你不熟悉 ASP.NET Core 或[必备工具](#prerequisites)，我们建议从 [GitHub][GitHub] 下载完整的示例项目，然后添加所需的 NuGet 包并运行它。 生成项目之后，可以回顾本文以深入了解项目上下文中的代码。

## <a name="prerequisites"></a><a name="prerequisites"></a>先决条件

在按照本文中的说明操作之前，请确保具备以下资源：

* 有效的 Azure 帐户。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019。 [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

本文中的所有屏幕截图都是在 Microsoft Visual Studio Community 2019 中截取的。 如果你使用其他版本，则屏幕截图和选项可能不与此完全相同。 如果满足先决条件，解决方案应可正常运行。

## <a name="step-1-create-an-azure-cosmos-account"></a><a name="create-an-azure-cosmos-account"></a>步骤 1：创建 Azure Cosmos 帐户

让我们首先创建一个 Azure Cosmos 帐户。 如果你已有一个 Azure Cosmos DB SQL API 帐户，或者使用的是 Azure Cosmos DB 仿真器，请跳到[步骤 2：创建新的 ASP.NET MVC 应用程序](#create-a-new-mvc-application)。

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

在下一部分，将新建一个 ASP.NET Core MVC 应用程序。

## <a name="step-2-create-a-new-aspnet-core-mvc-application"></a><a name="create-a-new-mvc-application"></a>步骤 2：新建 ASP.NET Core MVC 应用程序

1. 打开 Visual Studio 并选择“创建新项目”  。

1. 在“创建新项目”中，找到并选择适用于 C# 的“ASP.NET Core Web 应用程序”。   选择“下一步”  继续。

   ![新建 ASP.NET Core Web 应用程序项目](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

1. 在“配置新项目”中，将项目命名为 *todo*，然后选择“创建”。  

1. 在“创建新的 ASP.NET Core Web 应用程序”中，选择“Web 应用程序(模型-视图-控制器)”。   选择“创建”  继续操作。

   Visual Studio 将创建一个空的 MVC 应用程序。

1. 选择“调试” > “开始调试”或按 F5，在本地运行 ASP.NET 应用程序。  

## <a name="step-3-add-azure-cosmos-db-nuget-package-to-the-project"></a><a name="add-nuget-packages"></a>步骤 3：向项目添加 Azure Cosmos DB NuGet 包

有了此解决方案所需的大多数 ASP.NET Core MVC 框架代码以后，即可添加连接到 Azure Cosmos DB 所需的 NuGet 包。

1. 在“解决方案资源管理器”中，右键单击项目并选择“管理 NuGet 包”。  

1. 在“NuGet 包管理器”中，搜索并选择“Microsoft.Azure.Cosmos”。   选择“安装”  。

   ![安装 NuGet 包](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png)

   Visual Studio 会下载并安装 Azure Cosmos DB 包及其依赖项。

   你也可以使用“包管理器控制台”来安装 NuGet 包。  为此，请选择“工具” > “NuGet 包管理器” > “包管理器控制台”。    在提示符处键入以下命令：

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="step-4-set-up-the-aspnet-core-mvc-application"></a><a name="set-up-the-mvc-application"></a>步骤 4：设置 ASP.NET Core MVC 应用程序

现在，让我们向此 MVC 应用程序添加模型、视图和控制器。

### <a name="add-a-model"></a><a name="add-a-model"></a> 添加模型

1. 在“解决方案资源管理器”中，右键单击“模型”文件夹并选择“添加” > “类”。    

1. 在“添加新项”中，将新类命名为 *Item.cs*，然后选择“添加”。  

1. 将 *Item.cs* 类的内容替换为以下代码：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

Azure Cosmos DB 使用 JSON 来移动和存储数据。 可以使用 `JsonProperty` 属性来控制 JSON 序列化和反序列化对象的方式。 `Item` 类演示 `JsonProperty` 属性。 此代码控制进入 JSON 的属性名称的格式。 它还将 .NET 属性重命名为 `Completed`。

### <a name="add-views"></a><a name="add-views"></a>添加视图

接下来，让我们创建以下三个视图。

* 添加“列出项”视图
* 添加“新建项”视图
* 添加“编辑项”视图

#### <a name="add-a-list-item-view"></a><a name="AddItemIndexView"></a>添加“列表项”视图

1. 在“解决方案资源管理器”中，右键单击“视图”文件夹并选择“添加” > “新建文件夹”。     将文件夹命名为“项”。 

1. 右键单击空的“项”文件夹，并选择“添加” > “视图”。   

1. 在“添加 MVC 视图”中提供以下值： 

   * 在“视图名称”中，输入“索引”。  
   * 在“模板”中，选择“列出”。  
   * 在“模型类”中，选择“项(todo.Models)”。  
   * 选择“使用布局页”并输入 *~/Views/Shared/_Layout.cshtml*。 

   ![显示“添加 MVC 视图”对话框的屏幕截图](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png)

1. 在添加这些值之后，选择“添加”，让 Visual Studio 创建新的模板视图。 

完成后，Visual Studio 会打开它所创建的 *cshtml* 文件。 可以在 Visual Studio 中关闭该文件。 稍后我们将返回到该文件。

#### <a name="add-a-new-item-view"></a><a name="AddNewIndexView"></a>添加“新建项”视图

与创建视图来列出项一样，请执行以下步骤，以便创建新视图来创建项：

1. 在“解决方案资源管理器”中，再次右键单击“项”文件夹，并选择“添加” > “视图”。    

1. 在“添加 MVC 视图”中进行以下更改： 

   * 在“视图名称”中，输入“创建”。  
   * 在“模板”中选择“创建”。  
   * 在“模型类”中，选择“项(todo.Models)”。  
   * 选择“使用布局页”并输入 *~/Views/Shared/_Layout.cshtml*。 
   * 选择 **添加** 。

#### <a name="add-an-edit-item-view"></a><a name="AddEditIndexView"></a>添加“编辑项”视图

最后，通过以下步骤添加一个用于编辑项目的视图：

1. 在“解决方案资源管理器”中，再次右键单击“项”文件夹，并选择“添加” > “视图”。    

1. 在“添加 MVC 视图”中进行以下更改： 

   * 在“视图名称”框中，键入“编辑”。  
   * 在“模板”框中，选择“编辑”。  
   * 在“模型类”框中，选择“项(todo.Models)”。  
   * 选择“使用布局页”并输入 *~/Views/Shared/_Layout.cshtml*。 
   * 选择 **添加** 。

完成这些步骤后，请关闭 Visual Studio 中的所有 *cshtml* 文档，因为稍后要返回到这些视图。

### <a name="declare-and-initialize-services"></a><a name="initialize-services"></a>声明并初始化服务

首先我们添加一个类，其中包含用于连接和使用 Azure Cosmos DB 的逻辑。 在本教程中，我们会将该逻辑封装到名为 `CosmosDBService` 的类和名为 `ICosmosDBService` 的接口中。 此服务执行 CRUD 操作。 此外，它还执行读取源操作，例如列出不完整的项以及创建、编辑和删除项。

1. 在“解决方案资源管理器”中，右键单击项目并选择“添加” > “新建文件夹”。    将文件夹命名为“服务”。 

1. 右键单击“服务”文件夹，并选择“添加” > “类”。    将新类命名为 *CosmosDBService*，然后选择“添加”。 

1. 将 *CosmosDBService.cs* 的内容替换为以下代码：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. 右键单击“服务”文件夹，并选择“添加” > “类”。    将新类命名为“ICosmosDBService”  ，并选择“添加”。 

1. 将以下代码添加到 ICosmosDBService  类：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. 打开解决方案中的 Startup.cs  文件，并将 `ConfigureServices` 方法替换为：

    :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="ConfigureServices":::

    此步骤中的代码会根据配置，将客户端初始化为要通过 [ASP.NET Core 中的依赖项注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)功能注入的单一实例。

1. 在同一文件中添加以下 **InitializeCosmosClientInstanceAsync** 方法，用于读取配置并初始化客户端。

   [!code-csharp[](~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs?name=InitializeCosmosClientInstanceAsync)]

1. 在项目的 appsettings.json  文件中定义配置，如以下代码片段所示：

   :::code language="json" source="~/samples-cosmosdb-dotnet-core-web-app/src/appsettings.json":::

### <a name="add-a-controller"></a><a name="add-a-controller"></a>添加控制器

1. 在“解决方案资源管理器”中，右键单击“控制器”文件夹，并选择“添加” > “控制器”。    

1. 在“添加基架”中，依次选择“MVC 控制器 - 空”、“添加”。   

   ![在“添加基架”中选择“MVC 控制器 - 空”](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. 将新控制器命名为 *ItemController*。

1. 将 *ItemController.cs* 的内容替换为以下代码：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

此处使用的 **ValidateAntiForgeryToken** 属性可帮助此应用程序防止跨站点请求伪造攻击。 你的视图应该也可以使用此防伪令牌。 有关详细信息和示例，请参阅[在 ASP.NET MVC 应用程序中防止跨站点请求伪造 (CSRF) 攻击][Preventing Cross-Site Request Forgery]。 [GitHub][GitHub] 上提供的源代码已有完整实现。

我们还会在方法参数中使用 **Bind** 属性，帮助防范过度提交攻击。 有关详细信息，请参阅[教程：使用 ASP.NET MVC 中的实体框架实现 CRUD 功能][Basic CRUD Operations in ASP.NET MVC]。

## <a name="step-5-run-the-application-locally"></a><a name="run-the-application"></a>步骤 5：在本地运行应用程序

若要在本地计算机中测试应用程序，请使用以下步骤：

1. 在 Visual Studio 中按 F5 即可在调试模式下构建应用程序。 这样应该可以构建应用程序，并启动包含先前看到的空白网格页面的浏览器：

   ![按本教程创建的待办事项列表 Web 应用程序屏幕截图](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
   如果应用程序改为打开到主页，请将 `/Item` 追加到 url。

1. 选择“新建”链接，并在“名称”和“说明”字段中添加值。    将“已完成”复选框保留未选中状态。  如果选中此复选框，应用会添加处于已完成状态的新项。 该项不再会显示在初始列表中。

1. 选择“创建”  。 应用会将你返回到“索引”视图，项将显示在列表中。  可以在 **To-Do** 列表中额外添加几个项。

    ![“索引”视图的屏幕截图](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
1. 在列表中“项”的旁边选择“编辑”。   应用将会打开“编辑”视图，可在其中更新对象的任何属性，包括“已完成”标志。   如果你依次选择“已完成”、“保存”，则应用会在列表中显示处于已完成状态的“项”。   

   ![勾选了“已完成”框的“索引”视图屏幕截图](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

1. 使用 [Cosmos 资源管理器](https://cosmos.azure.com)或 Azure Cosmos DB 仿真器的数据资源管理器来验证 Azure Cosmos DB 服务中数据的状态。

1. 测试应用后，按 Ctrl+F5 停止调试应用。 可以开始部署了！

## <a name="step-6-deploy-the-application"></a><a name="deploy-the-application-to-azure"></a>步骤 6：部署应用程序

现在，已经拥有了可以使用 Azure Cosmos DB 正常工作的完整应用程序，接下来我们要将此 Web 应用部署到 Azure 应用服务。  

1. 若要发布此应用程序，请右键单击“解决方案资源管理器”中的项目并选择“发布”。  

1. 在“选取发布目标”中选择“应用服务”。  

1. 若要使用现有的应用服务配置文件，请依次选择“选择现有项”、“发布”。  

1. 在“应用服务”中选择“订阅”。   使用“视图”筛选器按资源组或资源类型进行筛选  。

1. 找到你的配置文件，然后选择“确定”。  接下来搜索必需的 Azure 应用服务，然后选择“确定”。 

   ![Visual Studio 中的“应用服务”对话框](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png)

另一个选项是创建新的配置文件：

1. 与在前面的过程中一样，在“解决方案资源管理器”右键单击该项目并选择“发布”。  
  
1. 在“选取发布目标”中选择“应用服务”。  

1. 在“选取发布目标”中，依次选择“新建”、“发布”。   

1. 在“应用服务”中，输入 Web 应用名称以及相应的订阅、资源组和托管计划，然后选择“创建”。  

   ![Visual Studio 中的“创建应用服务”对话框](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png)

在几秒钟内，Visual Studio 会发布 Web 应用程序并启动浏览器，方便你查看在 Azure 中运行的项目！

## <a name="next-steps"></a>后续步骤

本教程介绍了如何生成 ASP.NET Core MVC Web 应用程序。 该应用程序可以访问存储在 Azure Cosmos DB 中的数据。 接下来，可以继续学习以下资源：

* [Azure Cosmos DB 中的分区](./partitioning-overview.md)
* [SQL 查询入门](./how-to-sql-query.md)
* [如何使用真实示例为 Azure Cosmos DB 中的数据建模和分区](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
