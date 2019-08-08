---
title: 适用于 Azure Cosmos DB 的 ASP.NET Core MVC 教程：Web 应用程序开发
description: ASP.NET Core MVC 教程介绍如何创建使用 Azure Cosmos DB 的 MVC Web 应用程序。 我们将存储 JSON 并从 Azure 应用服务上托管的待办事项应用中访问数据 - ASP NET Core MVC 教程分步说明。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: sngun
ms.openlocfilehash: b1d8d2539ae89dfdb8feb2e38f00bf4440411d8a
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815138"
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
> * 创建 Azure Cosmos 帐户
> * 创建 ASP.NET Core MVC 应用
> * 将应用连接到 Azure Cosmos DB 
> * 对数据执行 CRUD 操作

> [!TIP]
> 本教程假定你先前有使用 ASP.NET Core MVC 和 Azure 应用服务的经验。 如果不熟悉 ASP.NET Core 或[必备工具](#prerequisites)，建议从 [GitHub][GitHub] 下载完整的示例项目，然后添加所需的 NuGet 包并运行它。 生成项目之后，可以回顾本文以深入了解项目上下文中的代码。

## <a name="prerequisites"></a>先决条件 

在按照本文中的说明操作之前，请确保具备以下资源：

* **有效的 Azure 帐户：** 如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

本文中的所有屏幕截图都是使用 Microsoft Visual Studio Community 2019 获取的。 如果系统配置了不同的版本，那么，屏幕和选项可能不会完全相符，但只要符合上述先决条件，本解决方案应该还是有效的。

## <a name="create-an-azure-cosmos-account"></a>步骤 1：创建 Azure Cosmos 帐户

让我们首先创建一个 Azure Cosmos 帐户。 如果已有一个 Azure Cosmos DB SQL API 帐户，或者要在本教程中使用 Azure Cosmos DB 模拟器，可以跳到[创建新的 ASP.NET MVC 应用程序](#create-a-new-mvc-application)部分。

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

在下一部分，将新建一个 ASP.NET Core MVC 应用程序。 

## <a name="create-a-new-mvc-application"></a>步骤 2：新建 ASP.NET Core MVC 应用程序

1. 在 Visual Studio 的“文件”  菜单中，选择“新建”  ，然后选择“项目”  。 将显示“新建项目”对话框  。

2. 在“新建项目”窗口中，使用“搜索模板”输入框来搜索“Web”，然后选择“ASP.NET Core Web 应用程序”。    

   ![新建 ASP.NET Core Web 应用程序项目](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. 在“名称”框中，键入项目的名称。  本教程使用名称“todo”。 如果选择使用此名称之外的其他名称，则每当本教程提及 todo 命名空间时，请调整所提供的代码示例，以便使用为应用程序命名的名称。 

4. 选择“浏览”，导航到要在其中创建项目的文件夹。  选择“创建”  。 

5. 此时会出现“新建 ASP.NET Core Web 应用程序”对话框。  在模板列表中，选择“Web 应用程序(模型-视图-控制器)”  。

6. 选择“创建”，让 Visual Studio 围绕空白 ASP.NET Core MVC 模板执行基架操作。  

7. 待 Visual Studio 创建好样板 MVC 应用程序之后，便有了可以在本地运行的空白 ASP.NET 应用程序。

## <a name="add-nuget-packages"></a>步骤 3：向项目添加 Azure Cosmos DB NuGet 包

有了此解决方案所需的大多数 ASP.NET Core MVC 框架代码以后，即可添加连接到 Azure Cosmos DB 所需的 NuGet 包。

1. Azure Cosmos DB .NET SDK 将打包并以 NuGet 包的形式分发。 若要在 Visual Studio 中获取 NuGet 包，请使用 Visual Studio 中的 NuGet 包管理器，方法是右键单击“解决方案资源管理器”中的项目，然后选择“管理 NuGet 包”。  
   
   ![屏幕截图：解决方案资源管理器中 Web 应用程序项目的右键单击选项，其中突出显示了“管理 NuGet 包”。](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
2. 此时会显示“管理 NuGet 包”对话框  。 在 NuGet 的“浏览”框中，键入 **Microsoft.Azure.Cosmos**。  从结果中安装 **Microsoft.Azure.Cosmos** 包。 它会下载并安装 Azure Cosmos DB 包及其依赖项。 在“接受许可证”窗口中选择“我接受”，以完成安装。  
   
   也可使用包管理器控制台来安装 NuGet 包。 为此，请在“工具”  菜单中选择“NuGet 包管理器”  ，然后选择“包管理器控制台”  。 在提示符处键入以下命令：
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos
   ```        

3. 安装该包之后，Visual Studio 项目就会包含对 Microsoft.Azure.Cosmos 的库引用。
  
## <a name="set-up-the-mvc-application"></a>步骤 4：设置 ASP.NET Core MVC 应用程序

现在可以向此 MVC 应用程序添加模型、视图和控制器了：

* [添加模型](#add-a-model)。
* [添加控制器](#add-a-controller)。
* [添加视图](#add-views)。

### <a name="add-a-model"></a> 添加模型

1. 在“解决方案资源管理器”中，右键单击“模型”文件夹，选择“添加”，然后选择“类”。     此时会显示“添加新项”对话框。 

1. 将新类命名为 **Item.cs**，然后选择“添加”。  

1. 接下来，将“Item.cs”类中的代码替换为以下代码：

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]
   
   Azure Cosmos DB 中存储的数据都会通过线路传递，并存储为 JSON。 若要控制通过 JSON.NET 进行的对象序列化/反序列化方式，可以使用已创建的 **Item** 类中展示的 **JsonProperty** 属性。 不但可以控制进入 JSON 的属性名称的格式，而且可以像重命名 **Completed** 属性一样重命名 .NET 属性。 

### <a name="add-a-controller"></a>添加控制器

1. 在“解决方案资源管理器”中，右键单击“控制器”文件夹，选择“添加”，然后选择“控制器”。     此时会显示“添加基架”对话框。 

1. 选择“MVC 控制器 - 空”，然后选择“添加”。  

   ![突出显示“MVC 控制器 - 空”选项的“添加基架”对话框屏幕截图](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. 将新控制器命名为 **ItemController**，并将该文件中的代码替换为以下代码：

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

   此处使用的 **ValidateAntiForgeryToken** 属性可帮助此应用程序防止跨站点请求伪造攻击。 不仅需添加此属性，还应确保视图也适用于此防伪令牌。 有关此主题的详细信息，以及如何正确实施此操作的示例，请参阅[防止跨站点请求伪造][Preventing Cross-Site Request Forgery]。 [GitHub][GitHub] 上提供的源代码已有完整实现。

   我们还会在方法参数中使用 **Bind** 属性，帮助防范过度提交攻击。 有关更多详细信息，请参阅 [ASP.NET MVC 中的基本 CRUD 操作][Basic CRUD Operations in ASP.NET MVC]。

### <a name="add-views"></a>添加视图

接下来，请创建以下三个视图： 

* [添加“列表项”视图](#AddItemIndexView)。
* [添加“新建项”视图](#AddNewIndexView)。
* [添加“编辑项”视图](#AddEditIndexView)。

#### <a name="AddItemIndexView"></a>添加“列表项”视图

1. 在“解决方案资源管理器”中，展开“视图”文件夹，右键单击先前在添加 **ItemController** 时 Visual Studio 创建的空白“项”文件夹，单击“添加”，然后单击“视图”。     
   
   ![解决方案资源管理器的屏幕截图，显示了 Visual Studio 创建的项文件夹，并突出显示了“添加视图”命令](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)

2. 在“添加视图”对话框中，更新以下值： 
   
   * 在“视图名称”框中，键入“索引”。  
   * 在“模板”框中，选择“列表”。  
   * 在“模型类”框中，选择“项(todo.Models)”。  
   * 在“布局页”框中，键入 ***~/Views/Shared/_Layout.cshtml***。
     
   ![屏幕截图：显示“添加视图”对话框](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)

3. 在添加这些值之后，选择“添加”，让 Visual Studio 创建新的模板视图。  完成之后，它会打开创建的 cshtml 文件。 可以在 Visual Studio 中关闭该文件，因为稍后会回头使用它。

#### <a name="AddNewIndexView"></a>添加“新建项”视图

与创建视图来列出项一样，请执行以下步骤，以便创建新视图来创建项：

1. 在“解决方案资源管理器”中，请再次右键单击“项”文件夹，选择“添加”，然后选择“视图”。    

1. 在“添加视图”对话框中，更新以下值： 
   
   * 在“视图名称”框中，键入“创建”。  
   * 在“模板”框中，选择“创建”。  
   * 在“模型类”框中，选择“项(todo.Models)”。  
   * 在“布局页”框中，键入 ***~/Views/Shared/_Layout.cshtml***。
   * 选择 **添加** 。
   
#### <a name="AddEditIndexView"></a>添加“编辑项”视图

最后，通过以下步骤添加一个用于编辑项目的视图：

1. 在“解决方案资源管理器”中，请再次右键单击“项”文件夹，选择“添加”，然后选择“视图”。    

1. 在“添加视图”对话框中，执行以下操作： 
   
   * 在“视图名称”框中，键入“编辑”。  
   * 在“模板”框中，选择“编辑”。  
   * 在“模型类”框中，选择“项(todo.Models)”。  
   * 在“布局页”框中，键入 ***~/Views/Shared/_Layout.cshtml***。
   * 选择 **添加** 。

完成此操作之后，请关闭 Visual Studio 中的所有 cshtml 文档，因为稍后会回头使用这些视图。

## <a name="connect-to-cosmosdb"></a>步骤 5：连接到 Azure Cosmos DB 

创建标准的 MVC 项目后，现在可以添加代码来连接到 Azure Cosmos DB 并执行 CRUD 操作了。 

### <a name="perform-crud-operations"></a> 对数据执行 CRUD 操作

在这里，首先要执行的操作是添加类，其中包含连接并使用 Azure Cosmos DB 所需的逻辑。 在本教程中，我们会将该逻辑封装到名为 `CosmosDBService` 的类和名为 `ICosmosDBService` 的接口中。 此服务执行 CRUD 和读取源操作，例如列出不完整的项以及创建、编辑和删除项。 

1. 在“解决方案资源管理器”的项目中创建名为 **Services** 的新文件夹。 

1. 右键单击 **Services** 文件夹，选择“添加”，然后选择“类”。   将新类命名为 **CosmosDBService**，然后选择“添加”。 

1. 将以下代码添加到 **CosmosDBService** 类，并将该文件中的代码替换为以下代码：

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. 重复步骤 2-3，但这次是针对名为 **ICosmosDBService** 的类进行操作，并添加以下代码：

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]
 
1. 前面的代码接收 `CosmosClient` 作为构造函数的一部分。 我们需要沿着 ASP.NET Core 管道转到项目的 **Startup.cs**，并根据配置将客户端初始化为将要通过[依赖项注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)注入的单一实例。 在 **ConfigureServices** 处理程序中，我们定义：

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

1. 在同一文件中，我们定义帮助程序方法 **InitializeCosmosClientInstanceAsync**，用于读取配置并初始化客户端。

    [!code-csharp[](~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs?name=InitializeCosmosClientInstanceAsync)] 

1. 配置在项目的 **appsettings.json** 文件中定义。 打开该文件，并添加名为 **CosmosDb** 的节：

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```
 
现在，如果运行此应用程序，ASP.NET Core 的管道会实例化 **CosmosDbService** 并将单个实例作为单一实例保留；当使用 **ItemController** 处理客户端请求时，它会收到此单个实例并且能够用它来执行 CRUD 操作。

现在，如果构建并立即运行此项目，则会看到如下所示内容：

![按本数据库教程创建的待办事项列表 Web 应用程序屏幕截图](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>步骤 6：在本地运行应用程序

若要在本地计算机中测试应用程序，请使用以下步骤：

1. 在 Visual Studio 中按 F5 即可在调试模式下构建应用程序。 这样应该可以构建应用程序，并启动包含先前看到的空白网格页面的浏览器：
   
   ![按本教程创建的待办事项列表 Web 应用程序屏幕截图](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. 单击“新建”链接，并在“名称”和“描述”字段中添加值。    让“已完成”复选框保持未选中状态，否则，新项会以已完成状态添加，不出现在初始列表中。 
   
3. 单击“创建”，此时会重定向回“索引”视图，创建的项会出现在列表中。   可以向待办事项列表添加更多项。

    ![屏幕截图：“索引”视图](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
4. 单击列表上某个**项**旁边的“编辑”将转到“编辑”视图，可以在此视图中更新对象的任何属性（包括“已完成”标志）。    如果标记“已完成”标志并单击“保存”，该**项**会在列表中显示为已完成。  
   
   ![勾选了“已完成”框的“索引”视图屏幕截图](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

5. 可以随时使用 [Cosmos 资源管理器](https://cosmos.azure.com)或 Azure Cosmos DB 模拟器的数据资源管理器来验证 Azure Cosmos DB 服务中数据的状态。

6. 完成应用测试后，按 Ctrl+F5 停止调试应用。 可以开始部署了！

## <a name="deploy-the-application-to-azure"></a>步骤 7：部署应用程序 
现在，已经拥有了可以使用 Azure Cosmos DB 正常工作的完整应用程序，接下来我们要将此 Web 应用部署到 Azure 应用服务。  

1. 若要发布此应用程序，请右键单击“解决方案资源管理器”中的项目，然后选择“发布”。  
   
2. 在“发布”对话框中选择“应用服务”，然后选择“新建”以创建应用服务配置文件，或选择“选择现有”以使用现有配置文件     。

3. 如果具有现有的 Azure 应用服务配置文件，请从下拉列表中选择“订阅”。  使用“视图”筛选器按资源组或资源类型进行筛选  。 接下来搜索必需的 Azure 应用服务，然后选择“确定”。 
   
   ![Visual Studio 中的“应用服务”对话框](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. 若要创建新的 Azure 应用服务配置文件，请单击“发布”对话框中的“新建”   。 在“创建应用服务”对话框中，输入 Web 应用名称和相应的订阅、资源组和应用服务计划，然后选择“创建”   。

   ![Visual Studio 中的“创建应用服务”对话框](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

在几秒钟内，Visual Studio 会发布 Web 应用程序并启动浏览器，方便你查看在 Azure 中运行的项目！

## <a name="next-steps"></a>后续步骤
本教程介绍了如何生成能够访问 Azure Cosmos DB 中存储的数据的 ASP.NET Core MVC Web 应用程序。 你现在可以继续学习下一篇文章：

* [了解如何在 Azure Cosmos DB 中将数据分区](./partitioning-overview.md)
* [了解如何在 Azure Cosmos DB 中执行更高级的查询](./how-to-sql-query.md)
* [了解如何在更高级的方案中为数据建模](./how-to-model-partition-example.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
