---
title: 教程：生成 .NET Core 应用以管理 Azure Cosmos DB 的 SQL API 帐户中存储的数据
description: 本教程使用 Azure Cosmos DB 的 SQL API .NET Core SDK 创建联机数据库和 C# 控制台应用程序。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 03/12/2018
ms.author: sngun
Customer intent: As a developer, I want to build a .NET Core application to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities that Azure Cosmos DB offers.
ms.openlocfilehash: 1aad68a3248561c86e195b55c1d509ab7a15964e
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54035672"
---
# <a name="tutorial-build-a-net-core-app-to-manage-data-stored-in-a-sql-api-account"></a>教程：生成 .NET Core 应用以管理 SQL API 帐户中存储的数据

> [!div class="op_single_selector"]
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core（预览版）](sql-api-dotnet-core-get-started-preview.md)
> * [.NET](sql-api-get-started.md)
> * [.NET（预览版）](sql-api-dotnet-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [异步 Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

作为开发人员，你可能具有使用 NoSQL 文件数据的应用程序。 可以使用 Azure Cosmos DB 中的 SQL API 帐户存储和访问此文档数据。 本教程介绍如何生成 .Net Core 应用以创建和查询 Azure Cosmos DB 的 SQL API 帐户中存储的数据。 

本教程涵盖以下任务：

> [!div class="checklist"]
> * 创建并连接到 Azure Cosmos 帐户
> * 配置 Visual Studio 解决方案
> * 创建联机数据库
> * 创建集合
> * 创建 JSON 文档
> * 对项、容器和数据库执行 CRUD 操作

没有时间创建应用程序？ 可在 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started)上获取完整的解决方案。 

## <a name="prerequisites"></a>先决条件

* 有效的 Azure 帐户。 如果没有，可以注册 [免费帐户](https://azure.microsoft.com/free/)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* 下载和使用免费的 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 如果要开发通用 Windows 平台 (UWP) 应用，则应使用 **Visual Studio 2017 版本 15.4** 或更高版本。 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。

    * 对于 MacOS 或 Linux，可以通过安装适用于所选平台的 [.NET Core SDK](https://www.microsoft.com/net/core#macos)，从命令行开发 .NET Core 应用。 

    * 对于 Windows，可以通过安装 [.NET Core SDK](https://www.microsoft.com/net/core#windows)，从命令行开发 .NET Core 应用。 

## <a name="create-an-azure-cosmos-account"></a>创建 Azure Cosmos 帐户

使用以下步骤创建 Azure Cosmos 帐户：

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>设置 Visual Studio 解决方案

1. 在计算机上打开 **Visual Studio 2017**。

2. 在“文件”菜单中，选择“新建”，并选择“项目”。

3. 在“新建项目”对话框中，选择“模板” > “Visual C#” > “.NET Core” > “控制台应用程序(.NET Core)”，将项目命名为 **DocumentDBGettingStarted**，然后选择“确定”。

   ![“新建项目”窗口的屏幕截图](./media/sql-api-dotnetcore-get-started/nosql-tutorial-new-project-2.png)

4. 在“解决方案资源管理器”中，右键单击“DocumentDBGettingStarted”。

5. 在同一菜单中，选择“管理 NuGet 包”。

   ![项目的右键单击菜单的屏幕截图](./media/sql-api-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)

6. 在“NuGet”选项卡上，选择窗口顶部的“浏览”，并在搜索框中输入 azure documentdb。

7. 在结果中找到 Microsoft.Azure.DocumentDB.Core，然后选择“安装”。

   该库的程序包 ID 是 [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)。 如果用户的目标是不受此 .NET Core NuGet 包支持的 .NET Framework 版本（例如 net461），则请使用 [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)，后者支持自 .NET Framework 4.5 以来的所有 .NET Framework 版本。

8. 出现提示时，接受 NuGet 包安装和许可协议。

现在，安装程序已完成，让我们开始编写一些代码。 可以在 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started) 上找到本教程的完整代码项目。

## <a id="Connect"></a>连接到 Azure Cosmos 帐户

通过导入所需依赖项，连接到 Azure Cosmos 帐户。 要导入依赖项，请将以下代码添加到 Program.cs 文件的开头处：

```csharp
using System;

// ADD THIS PART TO YOUR CODE
using System.Linq;
using System.Threading.Tasks;
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

接下来，在公共类 Program 下面添加这两个常量和 client 变量。

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

接下来，转到 [Azure 门户](https://portal.azure.com)，检索 URI 和主密钥。 Azure Cosmos DB URI 和主密钥是必需的，可让应用程序知道要连接的对象，使 Azure Cosmos DB 信任应用程序的连接。

在 Azure 门户中，转到 Azure Cosmos 帐户，然后选择“密钥”。

从门户中复制该 URI 并将它粘贴到 program.cs 文件中的 `<your endpoint URI>`。 然后从门户中复制“主密钥”并将它粘贴到 `<your key>`。 请务必删除 < 和 >，但保留终结点和密钥的双引号。

![从 Azure 门户获取密钥][keys]

开始使用入门应用程序时，请首先创建一个新的 **DocumentClient**实例。

在 Main 方法下面，添加名为 GetStartedDemo 的新异步任务，将新的 DocumentClient 实例化。

```csharp
static void Main(string[] args)
{
}

// ADD THIS PART TO YOUR CODE
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
}
```

添加以下代码，从 **Main** 方法中运行异步任务。 **Main** 方法将捕获异常并将它们写到控制台上。

```csharp
static void Main(string[] args)
{
        // ADD THIS PART TO YOUR CODE
        try
        {
                Program p = new Program();
                p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
                Exception baseException = de.GetBaseException();
                Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
        }
        catch (Exception e)
        {
                Exception baseException = e.GetBaseException();
                Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
        }
        finally
        {
                Console.WriteLine("End of demo, press any key to exit.");
                Console.ReadKey();
        }
```

选择“DocumentDBGettingStarted”按钮，生成和运行应用程序。

## <a id="CreateDatabase"></a>创建数据库

在添加创建数据库的代码之前，添加一个用于向控制台写入的帮助器方法。 将 **WriteToConsoleAndPromptToContinue** 方法复制并粘贴到 **GetStartedDemo** 方法下面。

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

使用 DocumentClient 类的 `CreateDatabaseAsync` 方法创建 Azure Cosmos DB 数据库。 数据库是跨集合分区的 JSON 文档存储的逻辑容器。 将以下代码复制并粘贴到客户端创建下方的 **GetStartedDemo** 方法。 这会创建一个名为 *FamilyDB* 的数据库。

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

选择“DocumentDBGettingStarted”按钮，运行应用程序。

## <a id="CreateColl"></a>创建集合

使用 DocumentClient 类的 `CreateDocumentCollectionAsync` 方法创建集合。 集合是 JSON 文档和相关联的 JavaScript 应用程序逻辑的容器。

> [!WARNING]
> **CreateDocumentCollectionAsync** 将创建一个具有保留吞吐量的新集合，它牵涉定价。 有关详细信息，请转到[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。

将以下代码复制并粘贴到数据库创建下方的 **GetStartedDemo** 方法。 此代码会创建一个名为 *FamilyCollection_oa* 的文档集合。

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.client.CreateDatabaseIfNotExistsAsync("FamilyDB_oa");

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

选择“DocumentDBGettingStarted”按钮，运行应用程序。

## <a id="CreateDoc"></a>创建 JSON 文档

使用 DocumentClient 类的 `CreateDocumentAsync` 方法创建文档。 文档是用户定义的（任意）JSON 内容。 现在，可以插入一个或多个文档。 

首先，创建 Family 类，表示在 Azure Cosmos DB 中存储的对象。 此外还将创建 Family 中使用的 Parent、Child、Pet 和 Address 子类。 文档必须将 **ID** 属性序列化为 JSON 格式的 **ID**。 通过在 **GetStartedDemo** 方法后添加以下内部子类来创建这些类。 将 **Family**、**Parent**、**Child**、**Pet** 和 **Address** 类复制并粘贴到 **WriteToConsoleAndPromptToContinue** 方法下面。

```csharp
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
    Console.WriteLine(format, args);
    Console.WriteLine("Press any key to continue ...");
    Console.ReadKey();
}

// ADD THIS PART TO YOUR CODE
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

将 **CreateFamilyDocumentIfNotExists** 方法复制并粘贴到 **CreateDocumentCollectionIfNotExists** 方法下面。

```csharp
// ADD THIS PART TO YOUR CODE
private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
{
    try
    {
        await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
        this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
    }
    catch (DocumentClientException de)
    {
        if (de.StatusCode == HttpStatusCode.NotFound)
        {
            await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
            this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
        }
        else
        {
            throw;
        }
    }
}
```

然后插入两个文档，Andersen Family 和 Wakefield Family 各一个。 将 `// ADD THIS PART TO YOUR CODE` 后面的代码复制并粘贴到文档集合创建下的 **GetStartedDemo** 方法。

```csharp
await this.CreateDatabaseIfNotExistsAsync("FamilyDB_oa");

await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
Family andersenFamily = new Family
{
        Id = "Andersen.1",
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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

Family wakefieldFamily = new Family
{
        Id = "Wakefield.7",
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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);
```

选择“DocumentDBGettingStarted”按钮，运行应用程序。

![帐户、联机数据库、集合之间的层次关系](./media/sql-api-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>查询 Azure Cosmos DB 资源

Azure Cosmos DB 支持对存储在每个集合中的 JSON 文档进行各种查询。 下面的示例代码使用 Azure Cosmos DB SQL 语法以及 LINQ 演示了各种查询，你可以针对上一步中插入的文档执行这些查询。

将 **ExecuteSimpleQuery** 方法复制并粘贴到 **CreateFamilyDocumentIfNotExists** 方法下面。

```csharp
// ADD THIS PART TO YOUR CODE
private void ExecuteSimpleQuery(string databaseName, string collectionName)
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Here we find the Andersen family via its LastName
        IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                .Where(f => f.LastName == "Andersen");

        // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine("\tRead {0}", family);
        }

        // Now execute the same query via direct SQL
        IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
                Console.WriteLine("\tRead {0}", family);
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

将以下代码复制并粘贴到第二次文档创建下的 **GetStartedDemo** 方法。

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

// ADD THIS PART TO YOUR CODE
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

选择“DocumentDBGettingStarted”按钮，运行应用程序。

下图说明了如何针对所创建的集合调用 Azure Cosmos DB SQL 查询语法。 同样的逻辑也适用于 LINQ 查询。

![说明 NoSQL 教程创建 C# 控制台应用程序所用查询的范围和意义的图表。](./media/sql-api-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

查询中的关键字 `FROM` 是可选项，因为 Azure Cosmos DB 查询已限制为单个集合。 因此，“FROM Families f”可与“FROM root r”或者任何其他所选变量名进行交换。 Azure Cosmos DB 将推断所选 Families、root 或变量名会默认引用当前集合。

## <a id="ReplaceDocument"></a>替换 JSON 文档

Azure Cosmos DB 支持替换 JSON 文档。  

将 **ReplaceFamilyDocument** 方法复制并粘贴到 **ExecuteSimpleQuery** 方法下面。

```csharp
// ADD THIS PART TO YOUR CODE
private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
{
    await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
    this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
}
```

将以下代码复制并粘贴到查询执行下的 **GetStartedDemo** 方法。 替换文档之后，将再次运行相同查询以查看更改后的文档。

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
// Update the Grade of the Andersen Family child
andersenFamily.Children[0].Grade = 6;

await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

选择“DocumentDBGettingStarted”按钮，运行应用程序。

## <a id="DeleteDocument"></a>删除 JSON 文档

Azure Cosmos DB 支持删除 JSON 文档。  

将 **DeleteFamilyDocument** 方法复制并粘贴到 **ReplaceFamilyDocument** 方法下面。

```csharp
// ADD THIS PART TO YOUR CODE
private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
{
    await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
    Console.WriteLine("Deleted Family {0}", documentName);
}
```

将以下代码复制并粘贴到第二次查询执行下的 **GetStartedDemo** 方法。

```csharp
await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO CODE
await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");
```

选择“DocumentDBGettingStarted”按钮，运行应用程序。

## <a id="DeleteDatabase"></a>删除数据库

删除已创建的数据库将删除该数据库及其所有子资源（集合、文档等）。 将以下代码复制并粘贴到文档下的 GetStartedDemo 方法，以删除整个数据库和所有子资源。

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

选择“DocumentDBGettingStarted”按钮，运行应用程序。

## <a id="Run"></a>运行 C# 控制台应用程序

在 Visual Studio 中选择“DocumentDBGettingStarted”按钮，以调试模式生成应用程序。 应该在控制台窗口中看到已启动应用的输出。 输出会显示我们所添加的查询的结果，并且应与下面的示例文本相匹配。

```bash
Created FamilyDB_oa
Press any key to continue ...
Created FamilyCollection_oa
Press any key to continue ...
Created Family Andersen.1
Press any key to continue ...
Created Family Wakefield.7
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Replaced Family Andersen.1
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Deleted Family Andersen.1
End of demo, press any key to exit.
```

现在已经完成了本教程，并且获得了一个正常工作的 C# 控制台应用程序。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组、Azure Cosmos 帐户和所有相关的资源时，可将其删除。 为此，请选择虚拟机的资源组，选择“删除”，然后确认要删除的资源组的名称。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何生成 .Net Core 应用以管理 Azure Cosmos DB 的 SQL API 帐户中存储的数据。 你现在可以继续学习下一篇文章：

> [!div class="nextstepaction"]
> [使用 Azure Cosmos DB 的 SQL API 帐户生成 Java 控制台应用](sql-api-java-get-started.md)

[create-sql-api-dotnet.md#create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-dotnetcore-get-started/nosql-tutorial-keys.png
