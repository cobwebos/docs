---
title: "适用于 Azure Cosmos DB 的 ASP.NET MVC 教程：Web 应用程序开发 | Microsoft Docs"
description: "介绍如何创建使用 Azure Cosmos DB 的 MVC Web 应用程序的 ASP.NET MVC 教程。 将存储 JSON 并从 Azure 网站上托管的待办事项应用程序中访问数据 — ASP NET MVC 教程分步说明。"
keywords: "asp.net mvc 教程, web 应用程序开发, mvc web 应用程序, asp net mvc 教程分步说明"
services: cosmos-db
documentationcenter: .net
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 52532d89-a40e-4fdf-9b38-aadb3a4cccbc
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/03/2017
ms.author: mimig
ms.openlocfilehash: 985962c4f2db21526291981ad5180fc0a16e2ab8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="_Toc395809351"></a>ASP.NET MVC 教程：使用 Azure Cosmos DB 开发 Web 应用程序
> [!div class="op_single_selector"]
> * [.NET](documentdb-dotnet-application.md)
> * [Node.js](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [Python](documentdb-python-application.md)
> 
> 

为了特别说明可以如何有效地利用 Azure Cosmos DB 来存储和查询 JSON 文档，本文提供了演示如何使用 Azure Cosmos DB 构建待办事项应用的完整演练。 任务将存储为 Azure Cosmos DB 中的 JSON 文档。

![屏幕截图：“ASP NET MVC 教程分步说明”教程创建的待办事项列表 MVC Web 应用程序](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image01.png)

本演练演示如何使用 Azure Cosmos DB 服务从 Azure 上托管的 ASP.NET MVC Web 应用程序来存储和访问数据。 如果正在寻找只侧重于 Azure Cosmos DB 而不是 ASP.NET MVC 组件的教程，请参阅 [构建 Azure Cosmos DB C# 控制台应用程序](documentdb-get-started.md)。

> [!TIP]
> 本教程假定先前已有使用 ASP.NET MVC 和 Azure 网站的经验。 如果不熟悉 ASP.NET 或[必备工具](#_Toc395637760)，我们建议从 [GitHub][GitHub] 下载完整的示例项目，并按照此示例中的说明操作。 构建之后，可以回顾本文以深入了解项目上下文中的代码。
> 
> 

## <a name="_Toc395637760"></a>本数据库教程的先决条件
在按照本文中的说明操作之前，应确保已拥有下列项：

* 有效的 Azure 帐户。  如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  
* 针对 Visual Studio 2017 的用于 .NET 的 Microsoft Azure SDK，可通过 Visual Studio 安装程序获得。

本文中的所有屏幕截图均使用 Microsoft Visual Studio Community 2017 采集。 如果系统配备了不同的版本，那么，屏幕和选项可能不会完全相符，但只要符合上述先决条件，本解决方案应该还是有效。

## <a name="_Toc395637761"></a>步骤 1：创建 Azure Cosmos DB 数据库帐户
让我们首先创建一个 Azure Cosmos DB 帐户。 如果已有一个 Azure Cosmos DB SQL (DocumentDB) 帐户，或者要在本教程中使用 Azure Cosmos DB 模拟器，可以跳到[创建新的 ASP.NET MVC 应用程序](#_Toc395637762)。

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

<br/>
现在，我们将演练如何从头开始创建新的 ASP.NET MVC 应用程序。 

## <a name="_Toc395637762"></a>步骤 2：新建 ASP.NET MVC 应用程序

1. 在 Visual Studio 的“文件”菜单中，指向“新建”，并单击“项目”。 将显示“新建项目”对话框。

2. 在“项目类型”窗格中，依次展开“模板”、“Visual C#”、“Web”，并选择“ASP.NET Web 应用程序”。

      ![屏幕截图：突出显示 ASP.NET Web 应用程序项目类型的“新建项目”对话框](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. 在“名称”框中，键入项目的名称。 本教程使用名称“todo”。 如果选择使用其他名称，则每当本教程提及 todo 命名空间时，必须调整所提供的代码示例，以便使用为应用程序命名的名称。 
4. 单击“浏览”导航到要在其中创建项目的文件夹，并单击“确定”。
   
      将出现“新建 ASP.NET Web 应用程序”对话框。
   
    ![“新建 ASP.NET Web 应用程序”对话框屏幕截图，其中突出显示了 MVC 应用程序模板](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-MVC.png)
5. 在模板窗格中，选择“MVC”。

6. 单击“确定”，让 Visual Studio 围绕空白 ASP.NET MVC 模板基架的搭建执行操作。 

          
7. Visual Studio 创建好样板 MVC 应用程序之后，便拥有可以在本地运行的空白 ASP.NET 应用程序。
   
    我们会跳过在本地运行项目，因为我确定我们都已看过 ASP.NET“Hello World”应用程序。 让我们直接跳到将 Azure Cosmos DB 添加到此项目并构建应用程序的步骤。

## <a name="_Toc395637767"></a>步骤 3：将 Azure Cosmos DB 添加到 MVC Web 应用程序项目
我们已经完成了此解决方案的大部分 ASP.NET MVC 琐事，现在可以开始本教程的真正目的，也就是将 Azure Cosmos DB 添加到 MVC Web 应用程序。

1. Azure Cosmos DB .NET SDK 将打包并以 NuGet 包的形式分发。 要在 Visual Studio 中获取 NuGet 包，请使用 Visual Studio 中的 NuGet 包管理器，方法是右键单击“解决方案资源管理器”中的项目，并单击“管理 NuGet 包”。
   
    ![屏幕截图：解决方案资源管理器中 Web 应用程序项目的右键单击选项，其中突出显示了“管理 NuGet 程序包”。](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
    此时会显示“管理 NuGet 包”对话框。
2. 在 NuGet“浏览”框中，键入 ***Azure DocumentDB***。 （包名称尚未更新为 Azure Cosmos DB。）
   
    从结果中安装“Microsoft 提供的 Microsoft.Azure.DocumentDB”包。 这会下载并安装 Azure Cosmos DB 程序包，以及所有依赖项（例如 Newtonsoft.Json）。 在“预览”窗口中单击“确定”，并在“许可证接受”窗口中单击“我接受”，以完成安装。
   
    ![屏幕截图：突出显示 Microsoft Azure DocumentDB 客户端库的“管理 NuGet 程序包”窗口](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-install-nuget.png)
   
      或者，也可以使用程序包管理器控制台来安装程序包。 为此，请在“工具”菜单中，单击“NuGet 包管理器”，并单击“包管理器控制台”。 在提示符处键入以下命令。
   
        Install-Package Microsoft.Azure.DocumentDB
        
3. 安装程序包之后，Visual Studio 解决方案应该类似于下列添加了两个新引用（Microsoft.Azure.Documents.Client 和 Newtonsoft.Json）的解决方案。
   
    ![屏幕截图：解决方案资源管理器中添加到 JSON 数据项目的两个引用](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-added-references.png)

## <a name="_Toc395637763"></a>步骤 4：设置 ASP.NET MVC 应用程序
现在我们可以开始向此 MVC 应用程序添加模型、视图和控制器：

* [添加模型](#_Toc395637764)。
* [添加控制器](#_Toc395637765)。
* [添加视图](#_Toc395637766)。

### <a name="_Toc395637764"></a>添加 JSON 数据模型
首先，让我们在 MVC 中创建 **M** （模型）。 

1. 在“解决方案资源管理器”中，右键单击“模型”文件夹，单击“添加”，并单击“类”。
   
      此时会显示“添加新项”对话框。
2. 将新类命名为 **Item.cs**，并单击“添加”。 
3. 在这个新的 **Item.cs** 文件中，将下列代码添加到最后一个 *using 语句*后面。
   
        using Newtonsoft.Json;
4. 现在将此代码 
   
        public class Item
        {
        }
   
    替换为以下代码。
   
        public class Item
        {
            [JsonProperty(PropertyName = "id")]
            public string Id { get; set; }
   
            [JsonProperty(PropertyName = "name")]
            public string Name { get; set; }
   
            [JsonProperty(PropertyName = "description")]
            public string Description { get; set; }
   
            [JsonProperty(PropertyName = "isComplete")]
            public bool Completed { get; set; }
        }
   
    Azure Cosmos DB 中的所有数据都会通过线路传递，并存储为 JSON。 若要通过 JSON.NET 控制对象的序列化/反序列化方式，可以使用刚才创建的 **Item** 类中所示的 **JsonProperty** 属性。 **不一定** 要这样做，但我想确保所有属性都按照 JSON camelCase 命名约定命名。 
   
    使用 JSON 时，不但可以控制属性名称的格式，还可以像我命名 **Description** 属性一样重命名 .NET 属性。 

### <a name="_Toc395637765"></a>添加控制器
现已创建 **M**，接下来创建 MVC 中的 **C**（控制器类）。

1. 在“解决方案资源管理器”中，右键单击“控制器”文件夹，单击“添加”，并单击“控制器”。
   
    此时会显示“添加基架”对话框。
2. 选择“MVC 5 控制器 - 空”，并单击“添加”。
   
    ![屏幕截图：突出显示“MVC 5 控制器 - 空”选项的“添加基架”对话框](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)
3. 将新控制器命名为 **ItemController**
   
    ![屏幕截图：“添加控制器”对话框](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-add-controller.png)
   
    创建文件之后，Visual Studio 解决方案应该类似于下列在“解决方案资源管理器”中包含有新 ItemController.cs 文件的解决方案。 系统还会显示先前创建的新 Item.cs 文件。
   
    ![屏幕截图：Visual Studio 解决方案 — 突出显示新 ItemController.cs 文件和 Item.cs 文件的解决方案资源管理器](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-new-item-solution-explorer.png)
   
    可以关闭 ItemController.cs，我们稍后会回头使用此文件。 

### <a name="_Toc395637766"></a>添加视图
现在，我们可以开始创建 MVC 中的 **V** （视图）：

* [添加“项索引”视图](#AddItemIndexView)
* [添加“新建项”视图](#AddNewIndexView)。
* [添加“编辑项”视图](#_Toc395888515)。

#### <a name="AddItemIndexView"></a>添加“项索引”视图
1. 在“解决方案资源管理器”中，展开“视图”文件夹，右键单击先前在添加 **ItemController** 时 Visual Studio 创建的空白“项”文件夹，单击“添加”，并单击“视图”。
   
    ![屏幕截图：显示 Visual Studio 使用突出显示的“添加视图”命令创建的 Item 文件夹的解决方案资源管理器](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-add-view.png)
2. 在“添加视图”对话框中，执行以下操作： 
   
   * 在“视图名称”框中，键入“索引”。
   * 在“模板”框中，选择“列表”。
   * 在“模型类”框中，选择“项(todo.Models)”。
   * 在“布局页”框中，键入 ***~/Views/Shared/_Layout.cshtml***。
     
   ![屏幕截图：显示“添加视图”对话框](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)
3. 设置完所有这些值之后，单击“添加”，让 Visual Studio 创建新的模板视图。 完成之后，它会打开刚创建的 cshtml 文件。 我们可以在 Visual Studio 中关闭该文件，我们稍后会回头使用此文件。

#### <a name="AddNewIndexView"></a>添加“新建项”视图
与创建“项索引”视图的方式类似，我们现在可以开始创建新的视图，以供创建新**项**使用。

1. 在“解决方案资源管理器”中，再次右键单击“Item”文件夹，单击“添加”，并单击“视图”。
2. 在“添加视图”对话框中，执行以下操作： 
   
   * 在“视图名称”框中，键入“创建”。
   * 在“模板”框中，选择“创建”。
   * 在“模型类”框中，选择“项(todo.Models)”。
   * 在“布局页”框中，键入 ***~/Views/Shared/_Layout.cshtml***。
   * 单击 **“添加”**。
   
#### <a name="_Toc395888515"></a>添加“编辑项”视图
最后，采用与之前相同的方式添加最后一个视图，以供编辑 **项** 使用。

1. 在“解决方案资源管理器”中，再次右键单击“Item”文件夹，单击“添加”，并单击“视图”。
2. 在“添加视图”对话框中，执行以下操作： 
   
   * 在“视图名称”框中，键入“编辑”。
   * 在“模板”框中，选择“编辑”。
   * 在“模型类”框中，选择“项(todo.Models)”。
   * 在“布局页”框中，键入 ***~/Views/Shared/_Layout.cshtml***。
   * 单击 **“添加”**。

完成此操作之后，关闭 Visual Studio 中的所有 cshtml 文档，我们稍后会回头使用这些视图。

## <a name="_Toc395637769"></a>步骤 5：连接 Azure Cosmos DB
我们已经创建了标准的 MVC 项目，现在我们可以开始添加 Azure Cosmos DB 的代码。 

在本节中，我们将添加代码来处理下列操作：

* [列出未完成的项](#_Toc395637770)。
* [添加项](#_Toc395637771)。
* [编辑项](#_Toc395637772)。

### <a name="_Toc395637770"></a>列出 MVC Web 应用程序中未完成的项
首先要执行的操作是添加类，其中包含要连接并使用 Azure Cosmos DB 的所有逻辑。 在本教程中，我们会将所有逻辑封装到名为 DocumentDBRepository 的存储库类中。 

1. 在“解决方案资源管理器”中，右键单击该项目，单击“添加”，并单击“类”。 将新类命名为 **DocumentDBRepository**，并单击“添加”。
2. 在刚刚创建的 **DocumentDBRepository** 类中，在*命名空间*声明上方添加以下 *using 语句*
   
        using Microsoft.Azure.Documents; 
        using Microsoft.Azure.Documents.Client; 
        using Microsoft.Azure.Documents.Linq; 
        using System.Configuration;
        using System.Linq.Expressions;
        using System.Threading.Tasks;
        using System.Net
        
    现在将此代码 
   
        public class DocumentDBRepository
        {
        }
   
    替换为以下代码。
   
        public static class DocumentDBRepository<T> where T : class
        {
            private static readonly string DatabaseId = ConfigurationManager.AppSettings["database"];
            private static readonly string CollectionId = ConfigurationManager.AppSettings["collection"];
            private static DocumentClient client;
   
            public static void Initialize()
            {
                client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
                CreateDatabaseIfNotExistsAsync().Wait();
                CreateCollectionIfNotExistsAsync().Wait();
            }
   
            private static async Task CreateDatabaseIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
   
            private static async Task CreateCollectionIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDocumentCollectionAsync(
                            UriFactory.CreateDatabaseUri(DatabaseId),
                            new DocumentCollection { Id = CollectionId },
                            new RequestOptions { OfferThroughput = 1000 });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
        }
   
    
3. 我们打算从配置中读取部分值，因此请打开应用程序的 **Web.config** 文件，并在 `<AppSettings>` 节下面添加下列几行。
   
        <add key="endpoint" value="enter the URI from the Keys blade of the Azure Portal"/>
        <add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal"/>
        <add key="database" value="ToDoList"/>
        <add key="collection" value="Items"/>
4. 现在，使用 Azure 门户的“密钥”边栏选项卡来更新*终结点*和 *authKey* 的值。 使用“密钥”边栏选项卡中的“URI”作为终结点设置的值，使用“密钥”边栏选项卡中的“主密钥”或“辅助密钥”作为 authKey 设置的值。

    我们已经连接了 Azure Cosmos DB 存储库，现在让我们添加应用程序逻辑。

1. 我们想要用待办事项列表应用程序做的第一件事就是显示未完成的项。  在 **DocumentDBRepository** 类中的任意位置复制并粘贴以下代码片段。
   
        public static async Task<IEnumerable<T>> GetItemsAsync(Expression<Func<T, bool>> predicate)
        {
            IDocumentQuery<T> query = client.CreateDocumentQuery<T>(
                UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId))
                .Where(predicate)
                .AsDocumentQuery();
   
            List<T> results = new List<T>();
            while (query.HasMoreResults)
            {
                results.AddRange(await query.ExecuteNextAsync<T>());
            }
   
            return results;
        }
2. 打开我们先前添加的 **ItemController** ，并在命名空间声明上方添加以下 *using 语句* 。
   
        using System.Net;
        using System.Threading.Tasks;
        using todo.Models;
   
    如果项目名称不是“todo”，则必须使用“todo. Models”进行更新，才能反映项目名称。
   
    现在将此代码
   
        //GET: Item
        public ActionResult Index()
        {
            return View();
        }
   
    替换为以下代码。
   
        [ActionName("Index")]
        public async Task<ActionResult> IndexAsync()
        {
            var items = await DocumentDBRepository<Item>.GetItemsAsync(d => !d.Completed);
            return View(items);
        }
3. 打开 **Global.asax.cs** 并将以下行添加到 **Application_Start** 方法 
   
        DocumentDBRepository<todo.Models.Item>.Initialize();

此时，应该可以构建解决方案，而不会发生任何错误。

如果现在运行应用程序，则可转到 **HomeController** 以及该控制器的“索引”视图。 这是我们一开始就选定的 MVC 模板项目默认行为，但是我们不想要这样的行为！ 让我们更改此 MVC 应用程序上的路由以改变此行为。

打开 ***App\_Start\RouteConfig.cs***，找到以“defaults:”开头的行，并将它更改为如下行。

        defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

若未在 URL 中指定控制路由行为的值，这会让 ASP.NET MVC 知道改用“项”（而不是“主页”）作为控制器，并使用“索引”作为视图。

若运行应用程序，它现在会调用到 **ItemController**，进而调用到存储库类，并使用 GetItems 方法将所有未完成的项返回到“视图\\项\\索引”视图。 

如果构建并立即运行此项目，现在应该会看到如下内容。    

![屏幕截图：本数据库教程创建的待办事项列表 Web 应用程序](./media/documentdb-dotnet-application/build-and-run-the-project-now.png)

### <a name="_Toc395637771"></a>添加项
我们可以开始将一些项放入数据库中，所以除了空白网格以外，我们还可以看到其他内容。

让我们将一些代码添加到 Azure Cosmos DBRepository 和 ItemController，以在 Azure Cosmos DB 中持久保存记录。

1. 将下列方法添加到 **DocumentDBRepository** 类。
   
       public static async Task<Document> CreateItemAsync(T item)
       {
           return await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId), item);
       }
   
   此方法只接受传递给它的对象，并将对象保留在 Azure Cosmos DB 中。
2. 打开 ItemController.cs 文件，并在类中添加下列代码段。 这是 ASP.NET MVC 得知如何执行 **创建** 操作的方式。 在此情况下，只需呈现先前创建的关联 Create.cshtml 视图。
   
        [ActionName("Create")]
        public async Task<ActionResult> CreateAsync()
        {
            return View();
        }
   
    现在此控制器需要更多代码，以接受“创建”视图所提交的数据。
3. 将下一个代码块添加到 ItemController.cs 类，以告诉 ASP.NET MVC 如何使用窗体 POST 来执行此控制器的操作。
   
        [HttpPost]
        [ActionName("Create")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> CreateAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.CreateItemAsync(item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
    此代码会调用到 DocumentDBRepository，并使用 CreateItemAsync 方法将新的待办事项保存到数据库。 
   
    **安全说明**：此处所使用的 **ValidateAntiForgeryToken** 属性可帮助此应用程序防止跨站点请求伪造攻击。 这不仅仅是添加此属性，视图也必须使用此防伪令牌。 有关此主题的详细信息以及如何正确实施此操作的示例，请参阅[防止跨站点请求伪造][Preventing Cross-Site Request Forgery]。 [GitHub][GitHub] 上提供的源代码已有完整实现。
   
    **安全说明**：我们还会在方法参数中使用 **Bind** 属性，帮助防范 over-posting 攻击。 有关更多详细信息，请参阅 [ASP.NET MVC 中的基本 CRUD 操作][Basic CRUD Operations in ASP.NET MVC]。

将新项添加到数据库所需的代码至此结束。

### <a name="_Toc395637772"></a>编辑项
我们最后还要做一件事，那就是添加在数据库中编辑 **项** 并将它们标记为已完成的功能。 编辑视图已添加到项目中，因此，我们只需重新将某些代码添加到控制器和 **DocumentDBRepository** 类。

1. 将下列代码添加到 **DocumentDBRepository** 类。
   
        public static async Task<Document> UpdateItemAsync(string id, T item)
        {
            return await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id), item);
        }
   
        public static async Task<T> GetItemAsync(string id)
        {
            try
            {
                Document document = await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id));
                return (T)(dynamic)document;
            }
            catch (DocumentClientException e)
            {
                if (e.StatusCode == HttpStatusCode.NotFound)
                {
                    return null;
                }
                else
                {
                    throw;
                }
            }
        }
   
    这些方法中的第一个方法 (**GetItem**) 会从 Azure Cosmos DB 中提取某个项，此项会被传递回 **ItemController**，接着传到“编辑”视图。
   
    刚刚添加的第二个方法使用从 **ItemController** 传入的**文档**版本取代 Azure Cosmos DB 中的**文档**。
2. 将下列代码添加到 **ItemController** 类。
   
        [HttpPost]
        [ActionName("Edit")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> EditAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
        [ActionName("Edit")]
        public async Task<ActionResult> EditAsync(string id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
   
            Item item = await DocumentDBRepository<Item>.GetItemAsync(id);
            if (item == null)
            {
                return HttpNotFound();
            }
   
            return View(item);
        }
   
    第一个方法会处理当用户单击“索引”视图中的“编辑”链接时所发生的 Http GET。 此方法会从 Azure Cosmos DB 中提取[**文档**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx)，并将它传递给“编辑”视图。
   
    “编辑”视图会接着对 **IndexController** 执行 Http POST 操作。 
   
    添加的第二个方法会处理此操作：将更新对象传递到 Azure Cosmos DB 以便持久保存在数据库中。

这样便大功告成了，这些就是我们必须运行应用程序的所有操作：列出未完成的**项**，添加新**项**，最后是编辑**项**。

## <a name="_Toc395637773"></a>步骤 6：在本地运行应用程序
若要在本地计算机上测试应用程序，请执行以下操作：

1. 在 Visual Studio 中按 F5，即可在调试模式下构建应用程序。 这样应该可以构建应用程序，并启动包含先前看到的空白网格页面的浏览器：
   
    ![屏幕截图：本数据库教程创建的待办事项列表 Web 应用程序](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
     
2. 单击“新建”链接，并在“名称”和“描述”字段中添加值。 将“已完成”复选框保持为未选中状态，否则，新**项**将以已完成状态添加，不会出现在初始列表中。
   
    ![屏幕截图：“创建”视图](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-create-new-item.png)
3. 单击“创建”，随后将重定向回到“索引”视图，创建的**项**会出现在列表中。
   
    ![屏幕截图：“索引”视图](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
   
    向待办事项列表任意添加更多 **项** 。
    
4. 单击列表上某个**项**旁边的“编辑”将转到“编辑”视图，可以在此视图中更新对象的任何属性（包括“已完成”标志）。 如果标记“已完成”标志并单击“保存”，该**项**将从未完成任务列表中删除。
   
    ![屏幕截图：选中了“已完成”框的“索引”视图](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-completed-item.png)
5. 完成应用测试后，按 Ctrl+F5 停止调试应用。 可以开始部署了！

## <a name="_Toc395637774"></a>步骤 7：将应用程序部署到 Azure 应用服务 
现在，已经拥有了可以使用 Azure Cosmos DB 正常工作的完整应用程序，接下来我们要将此 Web 应用部署到 Azure 应用服务。  

1. 要发布此应用程序，只需要右键单击“解决方案资源管理器”中的项目，单击“发布”即可。
   
    ![屏幕截图：解决方案资源管理器中的“发布”选项](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-publish.png)

2. 在“发布”对话框中，单击“Microsoft Azure App Service”，然后选择“新建”以创建应用服务配置文件，或单击“选择现有项”使用现有配置文件。

    ![Visual Studio 中的“发布”对话框](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-publish-to-existing.png)

3. 如果具有现有的 Azure 应用服务配置文件，请输入订阅名称。 使用“视图”按资源组或资源类型进行筛选，然后选择 Azure 应用服务。 
   
    ![Visual Studio 中的“应用服务”对话框](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. 若要创建新的 Azure 应用服务配置文件，请单击“发布”对话框中的“新建”。 在“创建应用服务”对话框中，输入 Web 应用名称和相应的订阅、资源组和应用服务计划，然后单击“创建”。

    ![Visual Studio 中的“创建应用服务”对话框](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

在几秒钟内，Visual Studio 将完成 Web 应用程序发布并启动浏览器，可从中查看在 Azure 中运行的简单作品！



## <a name="_Toc395637775"></a>后续步骤
祝贺你！ 刚才构建了第一个使用 Azure Cosmos DB 的 ASP.NET MVC Web 应用程序并将其发布到了 Azure。 可以从 [GitHub][GitHub] 下载或克隆完整应用程序（包括本教程未涵盖的详细信息和删除功能）的源代码。 因此，如果想将代码添加到应用中，请捕捉代码，再将它添加到此应用中。

若要向应用程序添加其他功能，请查看 [Azure Cosmos DB .NET 库](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)中提供的 API，并欢迎在 [GitHub][GitHub] 上的 Azure Cosmos DB .NET 库中补充内容。 

[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: http://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/documentdb-net-todo-app
