---
title: 生成一个用于在 Azure Cosmos DB SQL API 帐户中管理数据的 .NET 控制台应用
description: 了解如何使用 C# 控制台应用程序创建 Azure Cosmos DB SQL API 资源。
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: kirankk
ms.openlocfilehash: 36d172daed487372401691c7046215fb6c4a63ee
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384939"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>生成一个用于在 Azure Cosmos DB SQL API 帐户中管理数据的 .NET 控制台应用

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [异步 Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

欢迎使用 Azure Cosmos DB SQL API 入门教程。 学习本教程后，将拥有一个可创建并查询 Azure Cosmos DB 资源的控制台应用程序。 本教程使用 [3.0+ 版](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) Azure Cosmos DB .NET SDK，后者以 [.NET Framework](https://dotnet.microsoft.com/download) 或 [.NET Core](https://dotnet.microsoft.com/download) 为目标。

本教程涉及：

> [!div class="checklist"]
> * 创建并连接到 Azure Cosmos 帐户
> * 在 Visual Studio 中配置项目
> * 创建数据库和容器
> * 向容器添加项
> * 查询容器
> * 针对项的 CRUD 操作
> * 删除数据库

没有时间？ 不必担心！ 可在 [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)上获取完整的解决方案。 有关快速说明，请转到[“获取完整的教程解决方案”部分](#GetSolution)。

现在，让我们开始吧！

## <a name="prerequisites"></a>先决条件

* 有效的 Azure 帐户。 如果没有，可以注册 [免费帐户](https://azure.microsoft.com/free/)。

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>步骤 1：创建 Azure Cosmos DB 帐户
让我们创建一个 Azure Cosmos DB 帐户。 如果已经有一个想要使用的帐户，可以跳到 [设置 Visual Studio 解决方案](#SetupVS)。 如果使用 Azure Cosmos DB 模拟器，请遵循 [Azure Cosmos DB 模拟器](local-emulator.md)中的步骤设置该模拟器，并直接跳到[设置 Visual Studio 项目](#SetupVS)。

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount-preview.md)]

## <a id="SetupVS"></a>步骤 2：设置 Visual Studio 项目
1. 在计算机上打开 **Visual Studio 2017**。
1. 在“文件”菜单中，选择“新建”，并选择“项目”。   
1. 在“新建项目”  对话框中，选择“Visual C#” / “控制台应用(.NET Framework)”，为项目命名，并单击“确定”。   
    ![“新建项目”窗口的屏幕截图](./media/sql-api-get-started/dotnet-tutorial-visual-studio-new-project.png)

    > [!NOTE]
    > 对于 .NET Core 目标，请在“新建项目”对话框中，选择“Visual C#” / “控制台应用(.NET Core)”，为项目命名，然后单击“确定”    

1. 在“解决方案资源管理器”  中，右键单击 Visual Studio 解决方案下方的新控制台应用程序，并单击“管理 NuGet 包...” 

    ![项目的右键菜单的屏幕截图](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget.png)
1. 在“NuGet”选项卡上，单击“浏览”，并在搜索框中键入 **Microsoft.Azure.Cosmos**。  
1. 在结果中找到“Microsoft.Azure.Cosmos”  ，并单击“安装”  。
   Azure Cosmos DB SQL API 客户端库的程序包 ID 是 [Microsoft Azure Cosmos DB 客户端库](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)。
   ![用于查找 Azure Cosmos DB 客户端 SDK 的 NuGet 菜单屏幕截图](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

    如果获得有关查看解决方案更改的消息，请单击“确定”  。 如果获得有关接受许可证的消息，请单击“我接受”。 

很好！ 现在，我们已完成安装，让我们开始编写一些代码。 可以在 [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)上找到本教程的完整代码项目。

## <a id="Connect"></a>步骤 3：连接到 Azure Cosmos DB 帐户
1. 首先，将 **Program.cs** 文件中 C# 应用程序开头的引用替换为以下引用：

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. 现在，请将这些常量和变量添加到公共类 ``Program`` 中。

    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

    注意，如果你熟悉旧版 .NET SDK，则可能已看惯了术语“集合”和“文档”。 由于 Azure Cosmos DB 支持多 API 模型，因此 3.0+ 版的 .NET SDK 使用通用术语“容器”和“项”。 容器可以是集合、图或表。 项可以是文档、边缘/顶点或行，是容器中的内容。 [详细了解数据库、容器和项。](databases-containers-items.md)

1. 从 [Azure 门户](https://portal.azure.com)检索终结点 URL 和主密钥。

    在 Azure 门户中，导航到 Azure Cosmos DB 帐户，然后单击“密钥”  。

    从门户中复制该 URI 并将它粘贴到 ```Program.cs``` 文件中的 `<your endpoint URL>`。 从门户中复制“主密钥”并将它粘贴到 `<your primary key>`。

   ![显示从 Azure 门户获取 Azure Cosmos DB 密钥的屏幕截图](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. 接下来，我们将创建 ```CosmosClient``` 的新实例并为程序设置一些基架。

    在 **Main** 方法下面，添加名为 **GetStartedDemoAsync** 的新异步任务，将新的 ```CosmosClient``` 实例化。 我们将使用 **GetStartedDemoAsync** 作为入口点，该入口点调用在 Azure Cosmos DB 资源上运行的方法。

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

1. 添加以下代码，从 **Main** 方法中运行 **GetStartedDemoAsync** 异步任务。 **Main** 方法将捕获异常并将它们写到控制台上。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. 选择 **F5** 来运行应用程序。 控制台窗口输出会显示消息`End of demo, press any key to exit.`，确认已建立到 Azure Cosmos DB 的连接。 然后即可关闭控制台窗口。

祝贺你！ 已成功连接到 Azure Cosmos DB 帐户。 

## <a name="step-4-create-a-database"></a>步骤 4：创建数据库
可以使用 ``CosmosClient`` 类的 [**CreateDatabaseIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) 或 [**CreateDatabaseAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) 函数创建一个数据库。 数据库是跨容器分区的项的逻辑容器。

1. 将 **CreateDatabaseAsync** 方法复制并粘贴到 **GetStartedDemoAsync** 方法下面。 “CreateDatabaseAsync”会使用通过 ``databaseId`` 字段指定的 ID ``FamilyDatabase`` 来创建新数据库（如果不存在）  。 

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

1. 复制并粘贴以下代码，其中实例化了 CosmosClient 来调用刚添加的 **CreateDatabaseAsync** 方法。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    目前的代码应如下所示，其中填充了终结点和主密钥。

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStarted
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

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
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

选择 **F5** 来运行应用程序。

祝贺你！ 已成功创建了 Azure Cosmos DB 数据库。  

## <a id="CreateColl"></a>步骤 5：创建容器
> [!WARNING]
> 调用方法 **CreateContainerIfNotExistsAsync** 会创建新的容器，牵涉到定价。 有关详细信息，请访问 [定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。
>
>

可以使用 **CosmosDatabase** 类中的 [**CreateContainerIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) 或 [**CreateContainerAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) 函数创建容器。 容器包含项（在使用 SQL API 的情况下为 JSON 文档）和关联的 JavaScript 服务器端应用程序逻辑，例如存储过程、用户定义的函数以及触发器。

1. 将 **CreateContainerAsync** 方法复制并粘贴到 **CreateDatabaseAsync** 方法下面。 “CreateContainerAsync”会使用通过 ``containerId`` 字段指定的 ID ``FamilyContainer`` 来创建按 ``LastName`` 属性分区的新容器（如果不存在）  。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. 复制并粘贴以下代码，其中实例化了 CosmosClient 来调用刚添加的 **CreateContainer** 方法。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

   选择 **F5** 来运行应用程序。

祝贺你！ 已成功创建 Azure Cosmos DB 容器。  

## <a id="CreateDoc"></a>步骤 6：向容器添加项
可以使用“CosmosContainer”类的 [CreateItemAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmoscontainer) 函数创建项   。 使用 SQL API 时，项会投射为文档，后者是用户定义的（任意）JSON 内容。 现在，可以将项插入 Azure Cosmos DB 容器中。

在本例中，让我们首先创建 **Family** 类来表示存储在 Azure Cosmos DB 中的对象。 此外还将创建 **Family** 中使用的 **Parent**、**Child**、**Pet** 和 **Address** 子类。 注意，项必须将 **ID** 属性序列化为 JSON 格式的 **ID**。

1. 选择“Ctrl + Shift + A”以打开“添加新项”对话框   。 将新类“Family.cs”添加到项目中  。

    ![显示向项目添加新的 Family.cs 类的屏幕截图](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1. 将“Family”、“Parent”、“Child”、“Pet”和“Address”类复制并粘贴到“Family.cs”       。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]

1. 导航回 **Program.cs**，将 **AddItemsToContainerAsync** 方法添加到 **CreateContainerAsync** 方法下。
代码会进行检查，确保在创建项之前不存在具有相同 ID 的项。 我们会插入两个项，Andersen Family 和 Wakefield Family 各一个。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]

1. 在 ``GetStartedDemoAsync`` 方法中添加对 ``AddItemsToContainerAsync`` 的调用。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

选择 **F5** 来运行应用程序。

祝贺你！ 已成功创建了两个 Azure Cosmos DB 项。  

## <a id="Query"></a>步骤 7：查询 Azure Cosmos DB 资源
Azure Cosmos DB 支持对存储在每个集合中的 JSON 文档进行[各种查询](sql-api-sql-query.md)。 以下示例代码演示了如何针对我们在上一步插入的项来运行查询。

1. 将 **QueryItemsAsync** 方法复制并粘贴到 **AddItemsToContainerAsync** 方法下面。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. 在 ``GetStartedDemoAsync`` 方法中添加对 ``QueryItemsAsync`` 的调用。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

选择 **F5** 来运行应用程序。

祝贺你！ 已成功完成了对 Azure Cosmos DB 容器的查询。

## <a id="ReplaceItem"></a>步骤 8：替换 JSON 项
现在，我们将更新 Azure Cosmos DB 中的一个项。

1. 将 **ReplaceFamilyItemAsync** 方法复制并粘贴到 **QueryItemsAsync** 方法下面。 请注意，我们要更改 Family 的 ``IsRegistered`` 属性以及某个子项的 ``Grade``。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. 在 ``GetStartedDemoAsync`` 方法中添加对 ``ReplaceFamilyItemAsync`` 的调用。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

   选择 **F5** 来运行应用程序。

祝贺你！ 已成功替换了 Azure Cosmos DB 项。

## <a id="DeleteDocument"></a>步骤 9：删除项目
现在，我们将删除 Azure Cosmos DB 中的一个项。

1. 将 **DeleteFamilyItemAsync** 方法复制并粘贴到 **ReplaceFamilyItemAsync** 方法下面。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. 在 ``GetStartedDemoAsync`` 方法中添加对 ``DeleteFamilyItemAsync`` 的调用。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

选择 **F5** 来运行应用程序。

祝贺你！ 已成功删除了 Azure Cosmos DB 项。

## <a id="DeleteDatabase"></a>步骤 10：删除数据库
现在，我们将删除数据库。 删除已创建的数据库会删除该数据库及其所有子资源（容器、项以及任何存储过程、用户定义的函数、触发器）。 我们还会释放 **CosmosClient** 实例。

1. 将 **DeleteDatabaseAndCleanupAsync** 方法复制并粘贴到 **DeleteFamilyItemAsync** 方法下面。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. 在 ``GetStartedDemoAsync`` 方法中添加对 ``DeleteDatabaseAndCleanupAsync`` 的调用。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

选择 **F5** 来运行应用程序。

祝贺你！ 已成功删除了 Azure Cosmos DB 数据库。

## <a id="Run"></a>步骤 11：一起运行 C# 控制台应用程序！
在 Visual Studio 中选择 F5，即可在调试模式下生成并运行应用程序。

应会在控制台窗口中看到整个应用的输出。 输出会显示我们所添加的查询的结果，并且应与下面的示例文本相匹配。

```
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

祝贺你！ 已经完成了本教程，并且获得了一个正常工作的 C# 控制台应用程序！

## <a id="GetSolution"></a>获取完整的教程解决方案
如果没有时间完成本教程中的步骤，或者只需下载代码示例，则可从 [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) 获取。 

若要生成 GetStarted 解决方案，需备齐以下各项：

* 有效的 Azure 帐户。 如果没有，可以注册 [免费帐户](https://azure.microsoft.com/free/)。
* 一个 [Azure Cosmos DB 帐户][cosmos-db-create-account]。
* GitHub 上提供的 [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) 解决方案。

若要在 Visual Studio 中还原 Azure Cosmos DB .NET SDK 的引用，请在解决方案资源管理器中右键单击“GetStarted”解决方案，并单击“还原 NuGet 包”   。 接下来，按照[连接到 Azure Cosmos DB 帐户](#Connect)中所述的方法在 App.config 文件中更新 EndPointUri 和 PrimaryKey 值。

就这么简单，生成以后即可开始操作！

## <a name="next-steps"></a>后续步骤
* 需要更复杂的 ASP.NET MVC 教程？ 请参阅 [ASP.NET MVC 教程：使用 Azure Cosmos DB 进行 Web 应用程序开发](sql-api-dotnet-application-preview.md)。
* 希望使用 Azure Cosmos DB 执行规模和性能测试？ 请参阅[使用 Azure Cosmos DB 执行性能和扩展测试](performance-testing.md)
* 了解如何[监视 Azure Cosmos DB 请求、使用情况和存储](monitor-accounts.md)。
* 在 [Query Playground](https://www.documentdb.com/sql/demo)中对示例数据集运行查询。
* 若要了解有关 Azure Cosmos DB 的详细信息，请参阅[欢迎使用 Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction)。

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
