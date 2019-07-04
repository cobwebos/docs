---
title: 生成一个用于在 Azure Cosmos DB SQL API 帐户中管理数据的 .NET 控制台应用
description: 有关使用 SQL API 创建联机数据库和 C# 控制台应用的教程。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: sngun
ms.openlocfilehash: 60c7e6b32f60d6f42d706489c41dbeea4af0d15d
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342122"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>生成一个用于在 Azure Cosmos DB SQL API 帐户中管理数据的 .NET 控制台应用

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET（预览版）](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core（预览版）](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [异步 Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

欢迎使用 Azure Cosmos DB SQL API 入门教程。 完成本教程后，将拥有一个可创建并查询 Azure Cosmos DB 资源的控制台应用。

本教程演示如何：

> [!div class="checklist"]
>
> - 创建并连接到 Azure Cosmos DB 帐户
> - 配置 Visual Studio 解决方案
> - 创建数据库
> - 创建集合
> - 创建 JSON 文档
> - 查询集合
> - 更新 JSON 文档
> - 删除文档
> - 删除数据库

## <a name="prerequisites"></a>先决条件

已安装包含 Azure 开发工作流的 Visual Studio 2017：
- 可以下载并使用**免费的** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。  

Azure 订阅，或免费的 Cosmos DB 试用帐户：
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  
  
- 如果使用 Azure Cosmos DB 模拟器，请按照 [Azure Cosmos DB 模拟器](local-emulator.md)中的步骤设置该模拟器。 然后从[设置 Visual Studio 解决方案](#SetupVS)开始使用教程。
  
## <a name="get-the-completed-solution"></a>获取已完成的解决方案

如果没有时间完成本教程，或者只想下载代码示例，则可从 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) 下载完整的解决方案。 

运行已下载的完整解决方案： 

1. 请确保已安装[必备项](#prerequisites)。 
1. 在 Visual Studio 中打开下载的 GetStarted.sln 解决方案文件  。
1. 在“解决方案资源管理器”中，右键单击 GetStarted 项目，然后选择“管理 NuGet 包”    。
1. 在“NuGet”选项卡上，选择“还原”，以还原对 Azure Cosmos DB .NET SDK 的引用   。
1. 在 App.config 文件中，按照[连接到 Azure Cosmos DB 帐户](#Connect)部分中的说明更新 `EndpointUrl` 和 `PrimaryKey` 值  。
1. 选择“调试” > “开始执行(不调试)”或按 Ctrl+F5 生成并运行应用     。

## <a name="create-an-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

按照以下说明在 Azure 门户中创建 Azure Cosmos DB 帐户。 如果已有一个要使用的 Azure Cosmos DB 帐户，可直接跳到[设置 Visual Studio 解决方案](#SetupVS)。 

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>设置 Visual Studio 解决方案

1. 在 Visual Studio 2017 中，选择“文件” > “新建” > “项目”    。
   
1. 在“新建项目”对话框中，选择“Visual C#” > “控制台应用(.NET Framework)”，将项目命名为 AzureCosmosDBApp，然后选择“确定”      。
   
   ![“新建项目”窗口的屏幕截图](./media/sql-api-get-started/nosql-tutorial-new-project-2.png)
   
1. 在“解决方案资源管理器”中，右键单击 AzureCosmosDBApp 项目，然后选择“管理 NuGet 包”    。
   
   ![项目上下文菜单](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges.png)
   
1. 在“NuGet”选项卡上，选择“浏览”，并在搜索框中输入 azure documentdb    。
   
1. 找到并选择 Microsoft.Azure.DocumentDB，然后选择“安装”（如果尚未安装）   。
   
   Azure Cosmos DB SQL API 客户端库的程序包 ID 是 [Microsoft Azure Cosmos DB 客户端库](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)。

   > [!NOTE]
   > 若正使用 .NET Core，请参阅 [.NET Core 文档](./sql-api-dotnetcore-get-started.md)。

   ![用于查找 Azure Cosmos DB 客户端 SDK 的 NuGet 菜单的屏幕截图](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)
   
   如果收到有关查看解决方案更改的消息，请选择“确定”  。 如果收到有关接受许可证的消息，请选择“我接受”  。

## <a id="Connect"></a>连接到 Azure Cosmos DB 帐户

现可开始编写代码。 本教程的完整 Project.cs 文件位于 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs) 中  。

1. 在“解决方案资源管理器”中，选择“Program.cs”，然后在代码编辑器中，将以下引用添加到文件的开头   ：
   
   ```csharp
   using System.Net;
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Newtonsoft.Json;
   ```
   
1. 接下来，将以下两个常量和 `client` 变量添加到 `public class Program`。
   
   ```csharp
   
   public class Program
   {
      private const string EndpointUrl = "<your endpoint URL>";
      private const string PrimaryKey = "<your primary key>";
      private DocumentClient client;
   ```
   
1. 终结点 URL 和主密钥允许应用连接到 Azure Cosmos DB 帐户，并允许 Azure Cosmos DB 帐户信任该连接。 从 [Azure 门户](https://portal.azure.com)复制密钥，并将其粘贴到代码中。 

   
   1. 在 Azure Cosmos DB 帐户的左侧导航栏中，选择“密钥”。 
      
      ![在 Azure 门户中查看并复制访问密钥](./media/sql-api-get-started/nosql-tutorial-keys.png)
      
   1. 在“读/写密钥”下，使用右侧的复制按钮复制“URI”值，并将其粘贴到 Program.cs 的 `<your endpoint URL>` 中    。 例如： 
      
      `private const string EndpointUrl = "https://mysqlapicosmosdb.documents.azure.com:443/";`
      
   1. 复制“主密钥”值，并将其粘贴到 Program.cs 的 `<your primary key>` 中   。 例如： 
      
      `private const string PrimaryKey = "19ZDNJAiYL26tmnRvoez6hmtIfBGwjun50PWRjNYMC2ig8Ob9hYk7Fq1RYSv8FcIYnh1TdBISvCh7s6yyb0000==";`
   
1. 在 `Main` 方法之后，添加一个名为 `GetStartedDemo` 的新异步任务，用来实例化名为 `client` 的新 `DocumentClient`。
   
   ```csharp
      private async Task GetStartedDemo()
      {
        client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
      }
   ```

   如果要使用代理对象连接到 Azure Cosmos DB，则应改用以下代码块创建 DocumentClient 对象。 本文档中的示例不使用代理对象，因此下面的示例仅供参考：

   ```csharp
   HttpClientHandler handler = new HttpClientHandler()
   {
     Proxy = proxyObject
     UseProxy = true,
   };

   //Pass handler to the constructor of DocumentClient.
   DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey, handler);
   
   ```
   
1. 将以下代码添加到 `Main` 方法中，以运行 `GetStartedDemo` 任务。 `Main` 方法可捕获异常并将它们写入控制台。
   
   ```csharp
      static void Main(string[] args)
      {
        try
        {
           Program p = new Program();
           p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine($"{de.StatusCode} error occurred: {de.Message}, Message: {baseException.Message}");
        }
        catch (Exception e)
        {
           Exception baseException = e.GetBaseException();
           Console.WriteLine($"Error: {e.Message}, Message: {baseException.Message}");
        }
        finally
        {
           Console.WriteLine("End of demo, press any key to exit.");
           Console.ReadKey();
        }
      }
   ```
   
1. 按 F5 运行应用  。 
   
1. 如果控制台窗口中显示“演示结束，按任意键退出”消息，则表示连接成功  。 按任意键关闭控制台窗口。 

已成功连接到 Azure Cosmos DB 帐户。 现在，可使用某些 Azure Cosmos DB 资源。  

## <a name="create-a-database"></a>创建数据库

Azure Cosmos DB [数据库](databases-containers-items.md#azure-cosmos-databases)是跨集合分区的 JSON 文档存储的逻辑容器。 可使用 `DocumentClient` 类的 [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) 方法创建数据库。 

1. 在添加创建数据库的代码之前，添加一个用于向控制台写入的帮助器方法。 复制以下 `WriteToConsoleAndPromptToContinue` 方法，并将其粘贴到代码中的 `GetStartedDemo` 方法之后。
   
   ```csharp
   private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
   {
      Console.WriteLine(format, args);
      Console.WriteLine("Press any key to continue...");
      Console.ReadKey();
   }
   ```
   
1. 复制以下行并将其粘贴到 `GetStartedDemo` 方法的 `client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);` 行之后。 此代码将创建一个名为 `FamilyDB` 的数据库。
   
   ```csharp
      await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
   ```
   
1. 按 F5 运行应用  。

已成功创建 Azure Cosmos DB 数据库。 在 Azure Cosmos DB 帐户左侧导航中选择“数据资源管理器”，即可在 [Azure 门户](https://portal.azure.com)中查看数据库  。 

## <a id="CreateColl"></a>创建集合

集合是 JSON 文档和相关联的 JavaScript 应用程序逻辑的容器。 使用 `DocumentClient` 类的 [CreateDocumentCollectionIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync#overloads) 方法可创建集合。 

> [!IMPORTANT]
> CreateDocumentCollectionIfNotExistsAsync 可创建一个具有保留吞吐量的新集合，它牵涉定价  。 有关详细信息，请访问[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。
> 

1. 复制以下代码并将其粘贴到 `GetStartedDemo` 方法的 `await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });` 行之后。 此代码会创建一个名为 `FamilyCollection` 的文档集合。
   
   ```csharp
      await client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });
   ```
   
1. 按 F5 运行应用  。

已成功创建 Azure Cosmos DB 文档集合。 在 Azure 门户中，可在“数据资源管理器”的“FamilyDB”数据库下看到该集合   。  

## <a id="CreateDoc"></a>创建 JSON 文档

文档是用户定义的任意 JSON 内容。 文档必须将 ID 属性序列化为 JSON 格式的 `id`。 可使用 `DocumentClient` 类的 [CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync#overloads) 方法创建文档。 

> [!TIP]
> 如果已有要在数据库中存储的数据，则可以使用 Azure Cosmos DB 的[数据迁移工具](import-data.md)导入数据。
>

以下代码可创建两个文档并将其插入到数据库集合中。 首先，创建一个 `Family` 类，以及 `Parent`、`Child`、`Pet` 和 `Address` 子类，以便在 `Family` 中使用。 然后，创建一个 `CreateFamilyDocumentIfNotExists` 方法，同时创建并插入两个文档。 

1. 复制以下 `Family`、`Parent`、`Child`、`Pet` 和 `Address` 类，并将其粘贴到代码中的 `WriteToConsoleAndPromptToContinue` 方法之后。
   
   ```csharp
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
   ```
   
1. 复制以下 `CreateFamilyDocumentIfNotExists` 方法，并将其粘贴到刚刚添加的 `Address` 类之后。
   
   ```csharp
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            WriteToConsoleAndPromptToContinue($"Found {family.Id}");
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                WriteToConsoleAndPromptToContinue($"Created Family {family.Id}");
            }
            else
            {
                throw;
            }
        }
    }
   ```
   
1. 复制以下代码，并将其粘贴到 `GetStartedDemo` 方法末尾的 `await client.CreateDocumentCollectionIfNotExistsAsync` 行之后。 此代码创建并插入两个文档，分别用于 Andersen 和 Wakefield 系列。
   
   ```csharp
    Family andersenFamily = new Family
    {
        Id = "AndersenFamily",
        LastName = "Andersen",
        Parents = new Parent[]
        {
            new Parent { FirstName = "Thomas" },
            new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
            new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = true
    };

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

    Family wakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
        LastName = "Wakefield",
        Parents = new Parent[]
        {
            new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
            new Parent { FamilyName = "Miller", FirstName = "Ben" }
        },
        Children = new Child[]
        {
            new Child
            {
                FamilyName = "Merriam",
                FirstName = "Jesse",
                Gender = "female",
                Grade = 8,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Goofy" },
                    new Pet { GivenName = "Shadow" }
                }
            },
            new Child
            {
                FamilyName = "Miller",
                FirstName = "Lisa",
                Gender = "female",
                Grade = 1
            }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
    };

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);
   ```
   
1. 按 F5 运行应用  。

已成功创建两个 Azure Cosmos DB 文档。 在 Azure 门户中，可在“数据资源管理器”的“FamilyDB”数据库和“FamilyCollection”集合下看到这些文档    。   

![演示帐户、联机数据库、集合和文档之间的层次关系的示意图](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>查询 Azure Cosmos DB 资源

Azure Cosmos DB 支持对存储在集合中的 JSON 文档进行各种[查询](how-to-sql-query.md)。 以下示例代码使用 LINQ 和 Azure Cosmos DB SQL 语法对示例文档运行查询。

1. 复制以下 `ExecuteSimpleQuery` 方法，并将其粘贴到代码中的 `CreateFamilyDocumentIfNotExists` 方法之后。
   
   ```csharp
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options.
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Find the Andersen family by its LastName.
        IQueryable<Family> familyQuery = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
            .Where(f => f.LastName == "Andersen");

        // Execute the query synchronously. 
        // You could also execute it asynchronously using the IDocumentQuery<T> interface.
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine($"\tRead {family}");
        }

        // Now execute the same query using direct SQL.
        IQueryable<Family> familyQueryInSql = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
            "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
            queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
            Console.WriteLine($"\tRead {family}");
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }
   ```
   
1. 复制以下代码，并将其粘贴到 `GetStartedDemo` 方法末尾的 `await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);` 行之后。
   
   ```csharp
      ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. 按 F5 运行应用  。

前面的查询返回 Andersen 系列的完整项。 已成功完成对 Azure Cosmos DB 集合的查询。

下图说明了如何针对集合调用 Azure Cosmos DB SQL 查询语法。 同样的逻辑也适用于 LINQ 查询。

![说明 NoSQL 教程创建 C# 控制台应用程序所用查询的范围和意义的图表。](./media/sql-api-get-started/nosql-tutorial-collection-documents.png)

SQL 查询中的 [FROM](sql-query-from.md) 关键字是可选项，因为 Azure Cosmos DB 查询已限制为单个集合。 可将 `FROM Families f` 替换为 `FROM root r` 或所选的任何其他变量名称。 Azure Cosmos DB 将推断表示当前集合的所选 `Families`、`root` 或变量名。

## <a id="ReplaceDocument"></a>更新 JSON 文档

Azure Cosmos DB SQL API 支持更新和替换 JSON 文档。  

1. 复制以下 `ReplaceFamilyDocument` 方法，并将其粘贴到代码中的 `ExecuteSimpleQuery` 方法之后。
   
   ```csharp
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
       await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
       WriteToConsoleAndPromptToContinue($"Replaced Family {familyName}");
    }
   ```
   
1. 复制以下代码，并将其粘贴到 `GetStartedDemo` 方法末尾的 `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` 行之后。 该代码更新其中一个文档中的数据，然后再次运行查询以显示已更改的文档。
   
   ```csharp
   // Update the Grade of the Andersen Family child
   andersenFamily.Children[0].Grade = 6;
   await ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily", andersenFamily);
   ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. 按 F5 运行应用  。

查询输出显示 Andersen Family 的子项 `Grade` 从 `5` 更新为 `6`。 已成功更新并替换 Azure Cosmos DB 文档。 

## <a id="DeleteDocument"></a>删除 JSON 文档

Azure Cosmos DB SQL API 支持删除 JSON 文档。  

1. 复制以下 `DeleteFamilyDocument` 方法，并将其粘贴到 `ReplaceFamilyDocument` 方法之后。
   
   ```csharp
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
        Console.WriteLine($"Deleted Family {documentName}");
    }
   ```
   
1. 复制以下代码，并将其粘贴到 `GetStartedDemo` 方法末尾的第二个 `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` 行之后。
   
   ```csharp
   await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");
   ```
   
1. 按 F5 运行应用  。

已成功删除 Azure Cosmos DB 文档。 

## <a id="DeleteDatabase"></a>删除数据库

删除所创建的数据库，以将它及其所有子资源（包括集合和文档）删除。 

1. 复制以下代码，并将其粘贴到 `GetStartedDemo` 方法末尾的 `await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");` 行之后。 
   
   ```csharp
   // Clean up - delete the database
   await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));
   ```
   
1. 按 F5 运行应用  。

已成功删除 Azure Cosmos DB 数据库。 在“数据资源管理器”中，可看到 Azure Cosmos DB 帐户删除了 FamilyDB 数据库  。 

## <a id="Run"></a>运行整个 C# 控制台应用

在 Visual Studio 中按 F5，即可在调试模式下生成并运行完整的 C# 控制台应用  。 控制台窗口中应该会显示以下输出：

```bash
Created Family AndersenFamily
Press any key to continue ...
 Created Family WakefieldFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Replaced Family AndersenFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Deleted Family AndersenFamily
End of demo, press any key to exit.
```

祝贺你！ 你已完成本教程，并拥有一个能够正常运行的 C# 控制台应用，可用于创建、查询、更新和删除 Azure Cosmos DB 资源。  

## <a name="next-steps"></a>后续步骤
* 若要了解有关 Azure Cosmos DB 的详细信息，请参阅[欢迎使用 Azure Cosmos DB](introduction.md)。
* 如需更复杂的 ASP.NET MVC 教程，请参阅 [ASP.NET MVC 教程：使用 Azure Cosmos DB 进行 Web 应用程序开发](sql-api-dotnet-application.md)。
* 若要使用 Azure Cosmos DB 进行缩放和性能测试，请参阅[使用 Azure Cosmos DB 进行性能和缩放测试](performance-testing.md)。
* 要了解如何监视 Azure Cosmos DB 请求、使用情况和存储，请参阅[监视帐户](monitor-accounts.md)。
* 在[查询板块](https://www.documentdb.com/sql/demo)中针对示例数据集运行查询。

