---
title: 快速入门 - 生成 .NET 控制台应用以管理 Azure Cosmos DB SQL API 资源
description: 本快速入门介绍如何生成 .NET 控制台应用以管理 Azure Cosmos DB SQL API 帐户资源。
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/12/2019
ms.openlocfilehash: 0981ed30c6bcd9d4246ce1eb047aa66168e3884a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "79215275"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>快速入门：生成 .NET 控制台应用以管理 Azure Cosmos DB SQL API 资源

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

开始使用适用于 .NET 的 Azure Cosmos DB SQL API 客户端库。 按照本文档中的步骤安装 .NET 包，生成应用，并尝试对存储在 Azure Cosmos DB 中的数据执行基本 CRUD 操作的示例代码。 

Azure Cosmos DB 是 Microsoft 提供的全球分布式多模型数据库服务。 使用 Azure Cosmos DB，可以快速创建和查询键/值、文档和图形数据库。 使用适用于 .NET 的 Azure Cosmos DB SQL API 客户端库完成以下操作：

* 创建 Azure Cosmos 数据库和容器
* 向容器添加示例数据
* 查询数据 
* 删除数据库

[API 参考文档](/dotnet/api/microsoft.azure.cosmos?view=azure-dotnet) | [库源代码](https://github.com/Azure/azure-cosmos-dotnet-v3) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)或无需 Azure 订阅即可[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，也无需缴纳费用或承诺金。 
* [.NET Core 2.1 SDK 或更高版本](https://dotnet.microsoft.com/download/dotnet-core/2.1)。

## <a name="setting-up"></a>设置

本部分指导你创建 Azure Cosmos 帐户，并设置使用适用于 .NET 的 Azure Cosmos DB SQL API 客户端库以管理资源的项目。 本文所述的示例代码创建 `FamilyDatabase` 数据库，并在该数据库中创建家庭成员（每个家庭成员都是一个项）。 每个家庭成员都具有 `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` 等属性。 `LastName` 属性用作容器的分区键。 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a> 创建 Azure Cosmos 帐户

如果使用[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 选项创建 Azure Cosmos 帐户，你必须创建 **SQL API** 类型的 Azure Cosmos DB 帐户。 已为你创建 Azure Cosmos DB 测试帐户。 无需显式创建帐户，因此可以跳过此部分并转到下一部分。

如果你有自己的 Azure 订阅或者免费创建了订阅，则应显式创建 Azure Cosmos 帐户。 以下代码将创建具有会话一致性的 Azure Cosmos 帐户。 该帐户在 `South Central US` 和 `North Central US` 中复制。  

你可以使用 Azure Cloud Shell 创建 Azure Cosmos 帐户。 Azure Cloud Shell 是一个用于管理 Azure 资源的、可通过浏览器访问的交互式经验证 shell。 它使用户能够灵活选择最适合自己工作方式的 shell 体验，无论是 Bash 还是 PowerShell。 对于本快速入门教程，请选择“Bash”模式。  Azure Cloud Shell 还需要一个存储帐户，你可以在系统提示时创建一个。

选择以下代码旁边的“试用”按钮，  依次选择“Bash”  模式和“创建存储帐户”  ，然后登录到 Cloud Shell。 接下来，将以下代码复制并粘贴到 Azure cloud shell 并运行该代码。 Azure Cosmos 帐户名必须是全局唯一的，请确保在运行该命令之前更新 `mysqlapicosmosdb` 值。

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-master enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

创建 Azure Cosmos 帐户需要一段时间，操作成功后，可以看到确认输出。 该命令成功完成后，登录到 [Azure 门户](https://portal.azure.com/)，验证是否存在指定名称的 Azure Cosmos 帐户。 创建资源后，可以关闭 Azure Cloud Shell 窗口。 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>创建新的 .NET 应用

在首选编辑器或 IDE 中创建新的 .NET 应用程序。 从本地计算机打开 Windows 命令提示符或终端窗口。 你将从命令提示符或终端运行接下来的部分中的所有命令。  运行以下 dotnet 新命令，创建名为 `todo` 的新应用。 --langVersion 参数在创建的项目文件中设置 LangVersion 属性。

```console
dotnet new console --langVersion 7.1 -n todo
```

将目录更改为新创建的应用文件夹。 可使用以下代码生成应用程序：

```console
cd todo
dotnet build
```

内部版本的预期输出应如下所示：

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>安装 Azure Cosmos DB 包

当仍在应用程序目录中时，使用 DotNet 添加包命令安装适用于 .NET 的 Azure Cosmos DB 客户端库。

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>从 Microsoft Azure 门户复制 Azure Cosmos 帐户凭据

此示例应用程序需对 Azure Cosmos 帐户进行身份验证。 为了进行身份验证，应将 Azure Cosmos 帐户凭据传递给应用程序。 按照以下步骤获取 Azure Cosmos 帐户凭据：

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 导航到 Azure Cosmos 帐户。

1. 打开“键”窗格，复制帐户的 URI 和主键    。 下一步需将 URI 和键值添加到某个环境变量。

### <a name="set-the-environment-variables"></a>设置环境变量

复制帐户的 URI 和主键以后，请将其保存到运行应用程序的本地计算机的新环境变量中   。 若要设置环境变量，请打开控制台窗口，并运行以下命令。 请确保替换 `<Your_Azure_Cosmos_account_URI>` 和 `<Your_Azure_Cosmos_account_PRIMARY_KEY>` 值。

**Windows**

```console
setx EndpointUrl "<Your_Azure_Cosmos_account_URI>"
setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**Linux**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**MacOS**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

 ## <a name="object-model"></a><a id="object-model"></a>对象模型

在开始生成应用程序之前，先研究 Azure Cosmos DB 中资源的层次结构以及用于创建和访问这些资源的对象模型。 Azure Cosmos DB 按以下顺序创建资源：

* Azure Cosmos 帐户 
* 数据库 
* 容器 
* Items

若要进一步了解不同实体的层次结构，请参阅[在 Azure Cosmos DB 中使用数据库、容器和项](databases-containers-items.md)。 使用以下 .NET 类与这些资源进行交互：

* [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient?view=azure-dotnet) - 此类为 Azure Cosmos DB 服务提供客户端逻辑表示。 此客户端对象用于对服务进行配置和执行请求。

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) - 若数据库资源不存在，则此方法以异步操作的形式创建数据库资源；若数据库资源已存在，则此方法以异步操作的形式获取它。 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet) - 若容器不存在，则此方法以异步操作的形式创建容器；若容器已存在，则此方法以异步操作的形式获取它。 可查看响应中的状态代码，确定是新创建了容器 (201) 还是返回了现有容器 (200)。 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet) - 此方法在容器中创建项。 

* [UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet) - 此方法在容器内创建一个项（如果该项尚不存在）或替换该项（如果该项已存在）。 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator?view=azure-dotnet
) - 此方法使用带有参数化值的 SQL 语句在 Azure Cosmos 数据库的容器下创建项查询。 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) - 从 Azure Cosmos 帐户中删除指定的数据库。 `DeleteAsync` 方法只删除数据库。 应单独处理 `Cosmosclient` 实例（DeleteDatabaseandCleanupAsync 方法中如此操作）。 

 ## <a name="code-examples"></a><a id="code-examples"></a>代码示例

本文所述的示例代码在 Azure Cosmos DB 中创建家庭数据库。 家庭数据库包含家庭详细信息，例如名称、地址、位置、关联的父母、子女和宠物。 在将数据填充到 Azure Cosmos 帐户之前，请定义家庭项的属性。 在示例应用程序的根级别创建一个名为 `Family.cs` 的新类，并向其中添加以下代码：

```csharp
using Newtonsoft.Json;

namespace todo
{
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        // The ToString() method is used to format the output, it's used for demo purpose only. It's not required by Azure Cosmos DB
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
}
```

### <a name="add-the-using-directives--define-the-client-object"></a>添加 using 指令并定义客户端对象

从项目目录，在编辑器中打开 `Program.cs` 文件，并在应用程序顶部添加以下 using 指令：

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

在 **Program.cs** 文件中添加代码以读取上一步设置的环境变量。 定义 `CosmosClient`、`Database` 和 `Container` 对象。 接下来，向调用 `GetStartedDemoAsync` 方法的主方法添加代码，在该方法中管理 Azure Cosmos 帐户资源。 

```csharp
namespace todo
{
public class Program
{

    /// The Azure Cosmos DB endpoint for running this GetStarted sample.
    private string EndpointUrl = Environment.GetEnvironmentVariable("EndpointUrl");

    /// The primary key for the Azure DocumentDB account.
    private string PrimaryKey = Environment.GetEnvironmentVariable("PrimaryKey");

    // The Cosmos client instance
    private CosmosClient cosmosClient;

    // The database we will create
    private Database database;

    // The container we will create.
    private Container container;

    // The name of the database and container we will create
    private string databaseId = "FamilyDatabase";
    private string containerId = "FamilyContainer";

    public static async Task Main(string[] args)
    {
       try
       {
           Console.WriteLine("Beginning operations...\n");
           Program p = new Program();
           await p.GetStartedDemoAsync();

        }
        catch (CosmosException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine("{0} error occurred: {1}", de.StatusCode, de);
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: {0}", e);
        }
        finally
        {
            Console.WriteLine("End of demo, press any key to exit.");
            Console.ReadKey();
        }
    }
}
}
```

### <a name="create-a-database"></a>创建数据库 

定义 `program.cs` 类中的 `CreateDatabaseAsync` 方法。 该方法创建 `FamilyDatabase`（如果尚不存在）。

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>创建容器

定义 `program.cs` 类中的 `CreateContainerAsync` 方法。 该方法创建 `FamilyContainer`（如果尚不存在）。 

```csharp
/// Create the container if it does not exist. 
/// Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
private async Task CreateContainerAsync()
{
    // Create a new container
    this.container = await this.database.CreateContainerIfNotExistsAsync(containerId, "/LastName");
    Console.WriteLine("Created Container: {0}\n", this.container.Id);
}
```

### <a name="create-an-item"></a>创建项

通过使用以下代码添加 `AddItemsToContainerAsync` 方法来创建家庭项。 可以使用 `CreateItemAsync` 或 `UpsertItemAsync` 方法来创建项：

```csharp
private async Task AddItemsToContainerAsync()
{
    // Create a family object for the Andersen family
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
        IsRegistered = false
    };

    try
    {
        // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen".
        ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));
        // Note that after creating the item, we can access the body of the item with the Resource property of the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
        Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
    }
    catch (CosmosException ex) when (ex.StatusCode == HttpStatusCode.Conflict)
    {
        Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamily.Id);                
    }
}

```

### <a name="query-the-items"></a>查询项

插入项后，可以运行查询以获取“Andersen”家庭的详细信息。 以下代码显示如何直接使用 SQL 查询来执行查询。 获取“Anderson”家庭详细信息的 SQL 查询是：`SELECT * FROM c WHERE c.LastName = 'Andersen'`。 在 `program.cs` 类中定义 `QueryItemsAsync` 方法，并向其中添加以下代码：


```csharp
private async Task QueryItemsAsync()
{
    var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";

    Console.WriteLine("Running query: {0}\n", sqlQueryText);

    QueryDefinition queryDefinition = new QueryDefinition(sqlQueryText);
    FeedIterator<Family> queryResultSetIterator = this.container.GetItemQueryIterator<Family>(queryDefinition);

    List<Family> families = new List<Family>();

    while (queryResultSetIterator.HasMoreResults)
    {
        FeedResponse<Family> currentResultSet = await queryResultSetIterator.ReadNextAsync();
        foreach (Family family in currentResultSet)
        {
            families.Add(family);
            Console.WriteLine("\tRead {0}\n", family);
        }
    }
}

```

### <a name="delete-the-database"></a>删除数据库 

最后，可以使用以下代码删除添加 `DeleteDatabaseAndCleanupAsync` 方法的数据库：

```csharp
private async Task DeleteDatabaseAndCleanupAsync()
{
   DatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
   // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

   Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

   //Dispose of CosmosClient
    this.cosmosClient.Dispose();
}
```

### <a name="execute-the-crud-operations"></a>执行 CRUD 操作

定义完所有必需方法后，在 `GetStartedDemoAsync` 方法中执行。 此代码中注释掉了 `DeleteDatabaseAndCleanupAsync` 方法，因此如果执行该方法，你将看不到任何资源。 在 Microsoft Azure 门户中验证已创建 Azure Cosmos DB 资源后，可以取消对其的注释。 

```csharp
public async Task GetStartedDemoAsync()
{
    // Create a new instance of the Cosmos Client
    this.cosmosClient = new CosmosClient(EndpointUrl, PrimaryKey);
    await this.CreateDatabaseAsync();
    await this.CreateContainerAsync();
    await this.AddItemsToContainerAsync();
    await this.QueryItemsAsync();
}
```

添加所有必要的方法后，保存 `Program.cs` 文件。 

## <a name="run-the-code"></a>运行代码

接下来，生成并运行应用程序以创建 Azure Cosmos DB 资源。 请确保打开新的命令提示符窗口，请勿使用用于设置环境变量的实例。 因为未在当前打开的窗口中设置环境变量。 需要打开新的命令提示符以查看更新。 

```console
dotnet build
```

```console
dotnet run
```

运行应用程序时生成以下输出。 还可以登录到 Microsoft Azure 门户并验证是否已创建资源：

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

通过登录到 Microsoft Azure 门户来验证是否已创建数据，并查看 Azure Cosmos 帐户中的必需项。 

## <a name="clean-up-resources"></a>清理资源

若不再需要资源，可以使用 Azure CLI 或 Azure PowerShell 删除 Azure Cosmos 帐户和相应的资源组。 以下命令显示如何使用 Azure CLI 删除资源组：

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建 Azure Cosmos 帐户、如何使用 .NET Core 应用创建数据库和容器。 现在可以使用以下文章中的说明将其他数据导入到 Azure Cosmos 帐户。 

> [!div class="nextstepaction"]
> [将数据导入 Azure Cosmos DB](import-data.md)
