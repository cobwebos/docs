---
title: 有关在 Azure 搜索中为 Azure SQL 数据库编制索引的教程 | Microsoft Docs
description: 在本教程中，对 Azure SQL 数据库进行爬网，提取可搜索的数据并填充 Azure 搜索索引。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: tutorial
ms.date: 07/10/2018
ms.author: heidist
ms.openlocfilehash: b40d3a74904d6814eb01b5d41d10632e8c9af5be
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988789"
---
# <a name="tutorial-crawl-an-azure-sql-database-using-azure-search-indexers"></a>教程：使用 Azure 搜索索引器对 Azure SQL 数据库进行爬网

本教程演示如何配置索引器，以便从示例 Azure SQL 数据库提取可搜索的数据。 [索引器](search-indexer-overview.md)是 Azure 搜索组件，用于对外部数据源进行爬网，使用内容来填充[搜索索引](search-what-is-an-index.md)。 Azure SQL 数据库的索引器是所有索引器中使用最广泛的。 

熟练掌握索引器的配置很有用，因为这样可以减少需编写和维护的代码量。 可以将索引器附加到数据源，让索引器提取数据并将其插入索引中，然后即可选择定期按计划运行索引器，以便拾取基础源中所做的更改，不必准备和推送符合架构的 JSON 数据集。

在本教程中，请使用 [Azure 搜索 .NET 客户端库](https://aka.ms/search-sdk)和 .NET Core 控制台应用程序执行以下任务：

> [!div class="checklist"]
> * 下载并配置解决方案
> * 将搜索服务信息添加到应用程序设置
> * 在 Azure SQL 数据库中准备外部数据集 
> * 查看示例代码中的索引和索引器定义
> * 运行索引器代码以导入数据
> * 搜索索引
> * 查看门户中的索引器配置

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* Azure 搜索服务。 如需设置方面的帮助，请参阅[创建搜索服务](search-create-service-portal.md)。

* Azure SQL 数据库，提供索引器使用的外部数据源。 示例解决方案提供了一个用于创建表的 SQL 数据文件。

* Visual Studio 2017。 可以使用免费的 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 

> [!Note]
> 如果使用免费的 Azure 搜索服务，则仅限使用三个索引、三个索引器和三个数据源。 本教程每样创建一个。 请确保服务中有空间来接受新资源。

## <a name="download-the-solution"></a>下载解决方案

本教程中使用的索引器解决方案来自一个 Azure 搜索示例集合，只需进行一次主下载即可获得。 用于本教程的解决方案为 DotNetHowToIndexers。

1. 转到 Azure 示例 GitHub 存储库中的 [**Azure-Samples/search-dotnet-getting-started**](https://github.com/Azure-Samples/search-dotnet-getting-started)。

2. 单击“克隆或下载” > “下载 ZIP”。 默认情况下，该文件下载到 Downloads 文件夹。

3. 在“文件资源管理器” > “下载”中，右键单击该文件，然后选择“全部提取”。

4. 关闭只读权限。 右键单击文件夹名称 >“属性” > “常规”，然后清除当前文件夹、子文件夹和文件的“只读”属性。

5. 在 **Visual Studio 2017** 中打开解决方案 DotNetHowToIndexers.sln。

6. 在“解决方案资源管理器”中，右键单击顶级节点父解决方案 >“还原 NuGet 包”。

## <a name="set-up-connections"></a>设置连接
所需服务的连接信息在解决方案的 **appsettings.json** 文件中指定。 

在解决方案资源管理器中打开 **appsettings.json**，以便根据本教程中的说明填充每项设置。  

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

### <a name="get-the-search-service-name-and-admin-api-key"></a>获取搜索服务名称和管理 API 密钥

可以在门户中找到搜索服务终结点和密钥。 密钥用于访问服务操作。 管理密钥允许写入访问，是在服务中创建和删除对象（例如索引和索引器）所必需的。

1. 登录到 [Azure 门户](https://portal.azure.com/)，查找[订阅的搜索服务](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

2. 打开服务页。

3. 在顶部的主页中查找服务名称。 在下面的屏幕截图中，该名称为 azs-tutorial。

   ![服务名称](./media/search-indexer-tutorial/service-name.png)

4. 在 Visual Studio 中将其作为第一个条目复制并粘贴到 **appsettings.json**。

  > [!Note]
  > 服务名称是包含 search.windows.net 的终结点的一部分。 如果有兴趣，可以在“概述”页的“概要”中查看完整 URL。 URL 如以下示例所示： https://your-service-name.search.windows.net

5. 在左侧的“设置” > “密钥”中，复制其中一个管理密钥并将其作为第二个条目粘贴到 **appsettings.json**。 密钥是在预配期间为服务生成的字母数字字符串，是对服务操作进行授权访问所必需的。 

  添加两项设置以后，文件应如以下示例所示：

  ```json
  {
    "SearchServiceName": "azs-tutorial",
    "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
    . . .
  }
  ```

## <a name="prepare-an-external-data-source"></a>准备外部数据源

在此步骤中，请创建一个可供索引器爬网的外部数据源。 本教程的数据文件为 hotels.sql，在 \DotNetHowToIndexers 解决方案文件夹中提供。 

### <a name="azure-sql-database"></a>Azure SQL 数据库

可以使用 Azure 门户和示例中的 hotels.sql 文件，在 Azure SQL 数据库中创建数据集。 Azure 搜索使用平展行集，例如从视图或查询生成的行集。 示例解决方案中的 SQL 文件创建并填充单个表。

以下练习假定没有现成的服务器或数据库，因此会指导你在步骤 2 中创建这两项。 （可选）如果有现成的资源，可以向其添加 hotels 表，从步骤 4 开始。

1. 登录到 [Azure 门户](https://portal.azure.com/)。 

2. 单击“创建资源” > “SQL 数据库”，创建数据库、服务器和资源组。 可以使用默认设置和最低级别的定价层。 创建服务器的一大优势是可以指定管理员用户名和密码，这是在后面的步骤中创建和加载表所必需的。

   ![“新建数据库”页](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. 单击“创建”，部署新的服务器和数据库。 等待服务器和数据库部署。

4. 打开新数据库的“SQL 数据库”页（如果尚未打开）。 资源名称应显示为“SQL 数据库”而非“SQL Server”。

  ![“SQL 数据库”页](./media/search-indexer-tutorial/hotels-db.png)

4. 在命令栏中单击“工具” > “查询编辑器”。

5. 单击“登录”，输入服务器管理员的用户名和密码。

6. 单击“打开查询”，导航到 hotels.sql 所在位置。 

7. 选择该文件，然后单击“打开”。 此脚本应与以下屏幕截图类似：

  ![SQL 脚本](./media/search-indexer-tutorial/sql-script.png)

8. 单击“运行”以执行查询。 在“结果”窗格中，应看到一条 3 行内容的消息，指示查询成功。

9. 若要从此表返回一个行集，可执行以下充当验证步骤的查询：

   ```sql
   SELECT HotelId, HotelName, Tags FROM Hotels
   ```
   原型查询 `SELECT * FROM Hotels` 在查询编辑器中不起作用。 示例数据包括“位置”字段的地理坐标，目前在编辑器中无法处理。 如需要查询的其他列的列表，可执行以下语句：`SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')`

10. 有了外部数据集以后，请复制数据库的 ADO.NET 连接字符串。 在数据库的“SQL 数据库”页，转到“设置” > “连接字符串”，然后复制 ADO.NET 连接字符串。
 
  ADO.NET 连接字符串类似于以下示例，经修改后可使用有效的数据库名称、用户名和密码。

  ```sql
  Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
  ```
11. 在 Visual Studio 中将连接字符串粘贴到“AzureSqlConnectionString”中，作为 **appsettings.json** 文件中的第三个条目。

    ```json
    {
      "SearchServiceName": "azs-tutorial",
      "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-index-and-indexer-code"></a>了解索引和索引器代码

代码现已就绪，可以生成并运行了。 执行该操作之前，请抽时间了解此示例的索引和索引器定义。 相关代码在两个文件中：

  + **hotel.cs**，包含的架构用于定义索引
  + **Program.cs**，包含的函数用于创建和管理服务中的结构

### <a name="in-hotelcs"></a>在 hotel.cs 中

索引架构定义字段集合，包含的属性用于指定允许的操作，例如字段是否可以进行全文搜索、筛选或排序，如以下针对 HotelName 的字段定义所示。 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

架构还可以包含其他元素，包括用于提高搜索得分的计分配置文件、自定义分析器和其他构造。 但对于我们来说，架构只需进行稀疏定义，只包含在示例数据集中发现的字段。

在本教程中，索引器从一个数据源拉取数据。 在实践中，可以将多个索引器附加到同一索引，从多个数据源和索引器创建合并的可搜索索引。 可以使用同一索引-索引器对，仅数据源有所不同，也可以使用一个索引加多个索引器和数据源的组合，具体取决于你对灵活性的要求如何。

### <a name="in-programcs"></a>在 Program.cs 中

主程序包括所有三个具有代表性的数据源的函数。 只考虑 Azure SQL 数据库时，以下对象是重点：

  ```csharp
  private const string IndexName = "hotels";
  private const string AzureSqlHighWaterMarkColumnName = "RowVersion";
  private const string AzureSqlDataSourceName = "azure-sql";
  private const string AzureSqlIndexerName = "azure-sql-indexer";
  ```

在 Azure 搜索中，可以独立查看、配置或删除的对象包括索引、索引器和数据源（分别为 hotels、azure-sql-indexer、azure-sql）。 

值得特别一提的是 AzureSqlHighWaterMarkColumnName 列，因为它提供的更改检测信息可供索引器用来确定某个行自上次索引工作负荷以来是否已更改。 [更改检测策略](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)只能在索引器中使用，因数据源而异。 对于 Azure SQL 数据库，可以从两项策略中进行选择，具体取决于数据库要求。

以下代码演示了在 Program.cs 中用于创建数据源和索引器的方法。 此代码检查是否存在同一名称的资源，如果存在则会将其删除，所依据的假设是此程序可能多次运行。

  ```csharp
  private static string SetupAzureSqlIndexer(SearchServiceClient serviceClient, IConfigurationRoot configuration)
  {
    Console.WriteLine("Deleting Azure SQL data source if it exists...");
    DeleteDataSourceIfExists(serviceClient, AzureSqlDataSourceName);

    Console.WriteLine("Creating Azure SQL data source...");
    DataSource azureSqlDataSource = CreateAzureSqlDataSource(serviceClient, configuration);

    Console.WriteLine("Deleting Azure SQL indexer if it exists...");
    DeleteIndexerIfExists(serviceClient, AzureSqlIndexerName);

    Console.WriteLine("Creating Azure SQL indexer...");
    Indexer azureSqlIndexer = CreateIndexer(serviceClient, AzureSqlDataSourceName, AzureSqlIndexerName);

    return azureSqlIndexer.Name;
  }
  ```

请注意，索引器 API 调用与平台无关，但 [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 除外，后者指定要调用的爬网程序的类型。

## <a name="run-the-indexer"></a>运行索引器

在此步骤中，请编译并运行程序。 

1. 在解决方案资源管理器中右键单击“DotNetHowToIndexers”，然后选择“生成”。
2. 再次右键单击“DotNetHowToIndexers”，接着单击“调试” > “启动新实例”。

程序在调试模式下执行。 控制台窗口报告每项操作的状态。

  ![SQL 脚本](./media/search-indexer-tutorial/console-output.png)

代码在 Visual Studio 中以本地方式运行，连接到 Azure 中的搜索服务，后者又会使用连接字符串连接到 Azure SQL 数据库并检索数据集。 由于需要执行如此多的操作，因此存在多个可能的故障点。不过，如果出现错误，请首先查看以下情况：

+ 在本教程中，提供的搜索服务连接信息只能使用服务名称。 如果输入了完整 URL，则操作会停在索引创建阶段，出现“无法连接”错误。

+ **appsettings.json** 中的数据库连接信息。 它应该是从门户获得的 ADO.NET 连接字符串，经修改后包括了适用于数据库的用户名和密码。 用户帐户必须有权检索数据。

+ 资源限制。 回想一下，共享（免费）服务仅限 3 个索引、索引器和数据源。 达到最大限制的服务不能创建新的对象。

## <a name="search-the-index"></a>搜索索引 

在 Azure 门户的搜索服务“概览”页，单击顶部的“搜索浏览器”，提交一些有关新索引的查询。

1. 单击顶部的“更改索引”，选择 hotels 索引。

2. 单击“搜索”按钮，发出一个空的搜索指令。 

  索引中的三个条目以 JSON 文档的形式返回。 搜索浏览器返回 JSON 格式的文档，方便你查看整个结构。

3. 接下来，输入搜索字符串：`search=river&$count=true`。 

  此查询调用 `river` 一词的全文搜索，结果包含匹配文档的计数。 在索引很大且文档成千上万甚至数百万的测试方案中，返回匹配文档的计数很有用。 在本示例中，只有一个文档与查询匹配。

4. 最后，输入一个搜索字符串，将 JSON 输出限制为感兴趣的字段：`search=river&$count=true&$select=hotelId, baseRate, description`。 

  查询响应范围缩小为选定字段，使输出更简洁。

## <a name="view-indexer-configuration"></a>查看索引器配置

所有索引器（包括刚刚以编程方式创建的）都列在门户中。 可以打开一个索引器定义，然后查看其数据源，也可以配置一个刷新计划来拾取新行和已更改行。

1. 打开 Azure 搜索服务的服务“概览”页。
2. 向下滚动，找到对应于“索引器”和“数据源”的磁贴。
3. 单击一个磁贴，打开相应资源的列表。 可以选择单个索引器或数据源来查看或修改配置设置。

  ![索引器和数据源磁贴](./media/search-indexer-tutorial/tiles-portal.png)


## <a name="clean-up-resources"></a>清理资源

完成本教程后，最快的清理方式是删除包含 Azure 搜索服务的资源组。 现在，你可以删除资源组以永久删除其中的所有内容。 在门户中，资源组名称显示在 Azure 搜索服务的“概述”页上。

## <a name="next-steps"></a>后续步骤

可以将支持 AI 的算法附加到索引器管道中。 下一步，继续学习下面的教程。

> [!div class="nextstepaction"]
> [为 Azure Blob 存储中的文档编制索引](search-howto-indexing-azure-blob-storage.md)