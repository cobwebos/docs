---
title: 教程：通过 .Net 预览版 SDK 开发使用 Azure Cosmos DB 的 ASP.NET MVC Web 应用程序。
description: 本教程介绍如何创建使用 Azure Cosmos DB 的 ASP.NET MVC Web 应用程序。 将通过在 Azure 上托管的待办事项应用存储和访问 JSON 数据。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2018
ms.author: dech
ms.openlocfilehash: e3ad852246b4b78d5ed7ac938348e59e9b7e6ce0
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037117"
---
# <a name="tutorial-develop-an-aspnet-mvc-web-application-with-azure-cosmos-db-by-using-net-preview-sdk"></a>教程：通过 .Net 预览版 SDK 开发使用 Azure Cosmos DB 的 ASP.NET MVC Web 应用程序 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [.NET 预览版](sql-api-dotnet-application-preview.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


本教程介绍如何使用 Azure Cosmos DB 通过 Azure 上托管的 ASP.NET MVC 应用程序存储和访问数据。 在本教程中，请使用目前为预览版的 .Net SDK V3。 下图显示将要使用本文中的示例生成的网页：
 
![屏幕截图：“ASP NET MVC 教程分步说明”教程创建的待办事项列表 MVC Web 应用程序](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-image01.png)

如果没有时间完成本教程，可以从 [GitHub][GitHub] 下载完整的示例项目。 

本教程涉及：

> [!div class="checklist"]
> * 创建 Azure Cosmos 帐户
> * 创建 ASP.NET MVC 应用
> * 将应用连接到 Azure Cosmos DB 
> * 对数据执行 CRUD 操作

> [!TIP]
> 本教程假定先前已有使用 ASP.NET MVC 和 Azure 网站的经验。 如果不熟悉 ASP.NET 或[必备工具](#prerequisites)，建议从 [GitHub][GitHub] 下载完整的示例项目，然后添加所需的 NuGet 包并运行它。 生成项目之后，可以回顾本文以深入了解项目上下文中的代码。

## <a name="prerequisites"></a>先决条件 

在按照本文中的说明操作之前，请确保具备以下资源：

* **有效的 Azure 帐户：** 如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

* 针对 Visual Studio 2017 的用于 .NET 的 Microsoft Azure SDK，可通过 Visual Studio 安装程序获得。

本文中的所有屏幕截图均使用 Microsoft Visual Studio Community 2017 采集。 如果系统配置了不同的版本，那么，屏幕和选项可能不会完全相符，但只要符合上述先决条件，本解决方案应该还是有效的。

## <a name="create-an-azure-cosmos-account"></a>步骤 1：创建 Azure Cosmos 帐户

让我们首先创建一个 Azure Cosmos 帐户。 如果已有一个 Azure Cosmos DB SQL API 帐户，或者要在本教程中使用 Azure Cosmos DB 模拟器，可以跳到[创建新的 ASP.NET MVC 应用程序](#create-a-new-mvc-application)部分。

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

在下一部分，将新建一个 ASP.NET MVC 应用程序。 

## <a name="create-a-new-mvc-application"></a>步骤 2：新建 ASP.NET MVC 应用程序

1. 在 Visual Studio 的“文件”菜单中，选择“新建”，然后选择“项目”。 将显示“新建项目”对话框。

2. 在“新建项目”窗口中，依次展开“已安装”模板、“Visual C#”、“Web”，然后选择“ASP.NET Web 应用程序”。 

   ![新建 ASP.NET Web 应用程序项目](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-new-project-dialog.png)

3. 在“名称”框中，键入项目的名称。 本教程使用名称“todo”。 如果选择使用其他名称，则每当本教程提及 todo 命名空间时，请调整所提供的代码示例，以便使用为应用程序命名的名称。 

4. 选择“浏览”，导航到要在其中创建项目的文件夹，然后选择 **.Net Framework 4.6.1** 或更高版本。 选择“确定”。 

5. 将出现“新建 ASP.NET Web 应用程序”对话框。 在模板窗格中，选择“MVC”。

6. 选择“确定”，让 Visual Studio 围绕空白 ASP.NET MVC 模板执行基架操作。 

7. 待 Visual Studio 创建好样板 MVC 应用程序之后，便有了可以在本地运行的空白 ASP.NET 应用程序。

## <a name="add-nuget-packages"></a>步骤 3：向项目添加 Azure Cosmos DB NuGet 包

有了此解决方案所需的大多数 ASP.NET MVC 框架代码以后，即可添加连接到 Azure Cosmos DB 所需的 NuGet 包。

1. Azure Cosmos DB .NET SDK 将打包并以 NuGet 包的形式分发。 若要在 Visual Studio 中获取 NuGet 包，请使用 Visual Studio 中的 NuGet 包管理器，方法是右键单击“解决方案资源管理器”中的项目，然后选择“管理 NuGet 包”。
   
   ![屏幕截图：解决方案资源管理器中 Web 应用程序项目的右键单击选项，其中突出显示了“管理 NuGet 程序包”。](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-manage-nuget.png)
   
2. 此时会显示“管理 NuGet 包”对话框。 在 NuGet 的“浏览”框中，键入 **Microsoft.Azure.Cosmos**。 安装结果中显示的 **Microsoft.Azure.Cosmos** 3.0.0.1-preview 版本。 它会下载并安装 Azure Cosmos DB 包及其依赖项，例如 Newtonsoft.Json。 在“预览”窗口中选择“确定”，在“接受许可证”窗口中选择“我接受”，以完成安装。
   
   也可使用包管理器控制台来安装 NuGet 包。 为此，请在“工具”菜单中选择“NuGet 包管理器”，然后选择“包管理器控制台”。 在提示符处键入以下命令：
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos -Version 3.0.0.1-preview
   ```        

3. 安装该包之后，Visual Studio 解决方案应该包含两个新的库引用，分别针对 Microsoft.Azure.Cosmos.Client 和 Newtonsoft.Json。
  
## <a name="set-up-the-mvc-application"></a>步骤 4：设置 ASP.NET MVC 应用程序

现在可以向此 MVC 应用程序添加模型、视图和控制器了：

* [添加模型](#add-a-model)。
* [添加控制器](#add-a-controller)。
* [添加视图](#add-views)。

### <a name="add-a-model"></a> 添加模型

1. 在“解决方案资源管理器”中，右键单击“模型”文件夹，选择“添加”，然后选择“类”。 此时会显示“添加新项”对话框。

1. 将新类命名为 **TodoItem.cs**，然后选择“添加”。 

1. 接下来，将“Todoitem”类中的代码替换为以下代码：

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Models/TodoItem.cs)]
   
   Azure Cosmos DB 中存储的数据都会通过线路传递，并存储为 JSON。 若要控制通过 JSON.NET 进行的对象序列化/反序列化方式，可以使用已创建的 **TodoItem** 类中展示的 **JsonProperty** 属性。 不但可以控制进入 JSON 的属性名称的格式，而且可以像命名 **Description** 属性一样重命名 .NET 属性。 

### <a name="add-a-controller"></a>添加控制器

1. 在“解决方案资源管理器”中，右键单击“控制器”文件夹，选择“添加”，然后选择“控制器”。 此时会显示“添加基架”对话框。

1. 选择“MVC 5 控制器 - 空”，然后选择“添加”。

   ![屏幕截图：突出显示“MVC 5 控制器 - 空”选项的“添加基架”对话框](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. 将新控制器命名为 **ItemController，并将该文件中的代码替换为以下代码：

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Controllers/ItemController.cs)]

   此处使用的 **ValidateAntiForgeryToken** 属性可帮助此应用程序防止跨站点请求伪造攻击。 不仅需添加此属性，还应确保视图也适用于此防伪令牌。 有关此主题的详细信息以及如何正确实施此操作的示例，请参阅[防止跨站点请求伪造][Preventing Cross-Site Request Forgery]。 [GitHub][GitHub] 上提供的源代码已有完整实现。
   
   我们还会在方法参数中使用 **Bind** 属性，帮助防范过度提交攻击。 有关更多详细信息，请参阅 [ASP.NET MVC 中的基本 CRUD 操作][Basic CRUD Operations in ASP.NET MVC]。
    
### <a name="add-views"></a>添加视图

接下来，请创建以下三个视图： 

* [添加“列表项”视图](#AddItemIndexView)。
* [添加“新建项”视图](#AddNewIndexView)。
* [添加“编辑项”视图](#AddEditIndexView)。

#### <a name="AddItemIndexView"></a>添加“列表项”视图

1. 在“解决方案资源管理器”中，展开“视图”文件夹，右键单击先前在添加 **ItemController** 时 Visual Studio 创建的空白“项”文件夹，单击“添加”，然后单击“视图”。
   
   ![屏幕截图：显示 Visual Studio 使用突出显示的“添加视图”命令创建的 Item 文件夹的解决方案资源管理器](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view.png)

2. 在“添加视图”对话框中，更新以下值：
   
   * 在“视图名称”框中，键入“索引”。
   * 在“模板”框中，选择“列表”。
   * 在“模型类”框中，选择“项(todo.Models)”。
   * 在“布局页”框中，键入 ***~/Views/Shared/_Layout.cshtml***。
     
   ![屏幕截图：显示“添加视图”对话框](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view-dialog.png)

3. 在添加这些值之后，选择“添加”，让 Visual Studio 创建新的模板视图。 完成之后，它会打开创建的 cshtml 文件。 可以在 Visual Studio 中关闭该文件，因为稍后会回头使用它。

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

在这里，首先要执行的操作是添加类，其中包含连接并使用 Azure Cosmos DB 所需的逻辑。 在本教程中，我们会将该逻辑封装到名为 TodoItemService.cs 的类中。 以下代码从配置文件读取 Azure Cosmos DB 终结点值并执行 CRUD 操作，例如列出不完全的项目以及创建、编辑和删除项目。 

1. 在“解决方案资源管理器”的项目中创建名为 **Services** 的新文件夹。

1. 右键单击 **Services** 文件夹，选择“添加”，然后选择“类”。 将新类命名为 **TodoItemService**，然后选择“添加”。

1. 将以下代码添加到 **TodoItemService** 类，并将该文件中的代码替换为以下代码：

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Services/TodoItemService.cs)]
 
1. 上面的代码从配置文件读取连接字符串值。 若要更新 Azure Cosmos 帐户的连接字符串值，请打开项目中的 **Web.config** 文件，然后在 `<AppSettings>` 节下面添加以下行：

   ```csharp
    <add key="endpoint" value="<enter the URI from the Keys blade of the Azure Portal>" />
    <add key="primaryKey" value="<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>" />
    <add key="database" value="Tasks" />
    <add key="container" value="Items" />
   ```
 
1. 使用从 Azure 门户的“密钥”边栏选项卡中检索的值更新终结点和 primarykey 的值。 使用“密钥”边栏选项卡中的“URI”作为终结点设置的值，使用“密钥”边栏选项卡中的“主密钥”或“辅助密钥”作为密钥设置。 这样会将 Azure Cosmos DB 连接到应用程序，现在让我们添加应用程序逻辑。

1. 打开 **Global.asax.cs** 并将以下行添加到 **Application_Start** 方法 
   
   ```csharp
   TodoItemService.Initialize().GetAwaiter().GetResult();
   ```

   此时，解决方案应该可以生成项目而不发生任何错误。 如果现在运行应用程序，则会打开 **HomeController** 以及该控制器的“索引”视图。 这是我们一开始就选定的 MVC 模板项目的默认行为。 让我们更改此 MVC 应用程序上的路由以改变此行为。

1. 打开 ***App\_Start\RouteConfig.cs***，找到以“defaults:”开头的行，使用以下代码对其进行更新：

   ```csharp
   defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }
   ```

  若未在 URL 中指定控制路由行为的值，此代码会让 ASP.NET MVC 知道改用“项”（而不是“主页”）作为控制器，并使用“索引”作为视图。

若运行应用程序，它现在会调用 **ItemController**，后者通过在下一部分定义的 TodoItemService 类调用 GetItems 方法。 

如果构建并立即运行此项目，现在应该会看到如下内容。    

![屏幕截图：本数据库教程创建的待办事项列表 Web 应用程序](./media/sql-api-dotnet-application-preview/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>步骤 6：在本地运行应用程序

若要在本地计算机中测试应用程序，请使用以下步骤：

1. 在 Visual Studio 中按 F5 即可在调试模式下构建应用程序。 这样应该可以构建应用程序，并启动包含先前看到的空白网格页面的浏览器：
   
   ![屏幕截图：本数据库教程创建的待办事项列表 Web 应用程序](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. 单击“新建”链接，并在“名称”和“描述”字段中添加值。 让“已完成”复选框保持未选中状态，否则，新项会以已完成状态添加，不出现在初始列表中。
   
3. 单击“创建”，此时会重定向回“索引”视图，创建的项会出现在列表中。 可以向待办事项列表添加更多项。

    ![屏幕截图：“索引”视图](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item.png)
  
4. 单击列表上某个**项**旁边的“编辑”将转到“编辑”视图，可以在此视图中更新对象的任何属性（包括“已完成”标志）。 如果标记“已完成”标志并单击“保存”，该**项**将从未完成任务列表中删除。
   
   ![屏幕截图：选中了“已完成”框的“索引”视图](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-completed-item.png)

5. 完成应用测试后，按 Ctrl+F5 停止调试应用。 可以开始部署了！

## <a name="deploy-the-application-to-azure"></a>步骤 7：部署应用程序 
现在，已经拥有了可以使用 Azure Cosmos DB 正常工作的完整应用程序，接下来我们要将此 Web 应用部署到 Azure 应用服务。  

1. 若要发布此应用程序，请右键单击“解决方案资源管理器”中的项目，然后选择“发布”。
   
2. 在“发布”对话框中选择“Microsoft Azure 应用服务”，然后选择“新建”以创建应用服务配置文件，或选择“选择现有”使用现有配置文件。

3. 如果具有现有的 Azure 应用服务配置文件，请从下拉列表中选择“订阅”。 使用“视图”筛选器按资源组或资源类型进行筛选。 接下来搜索必需的 Azure 应用服务，然后选择“确定”。 
   
   ![Visual Studio 中的“应用服务”对话框](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-app-service.png)

4. 若要创建新的 Azure 应用服务配置文件，请单击“发布”对话框中的“新建”。 在“创建应用服务”对话框中，输入 Web 应用名称和相应的订阅、资源组和应用服务计划，然后选择“创建”。

   ![Visual Studio 中的“创建应用服务”对话框](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-app-service.png)

在几秒钟内，Visual Studio 会发布 Web 应用程序并启动浏览器，方便你查看在 Azure 中运行的项目！

## <a name="next-steps"></a>后续步骤
本教程介绍了如何生成能够访问 Azure Cosmos DB 中存储的数据的 ASP.NET MVC Web 应用程序。 你现在可以继续学习下一篇文章：

> [!div class="nextstepaction"]
> [生成 Java 应用程序以访问在 Azure Cosmos DB 的 SQL API 帐户中存储的数据]( sql-api-java-application.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-todo-app
