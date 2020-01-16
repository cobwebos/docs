---
title: 使用 .Net V4 SDK 管理 Azure Cosmos DB SQL API 资源
description: 关于使用 .Net V4 SDK 生成控制台应用以管理 Azure Cosmos DB SQL API 帐户资源的快速入门。
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/10/2020
ms.openlocfilehash: b69d67a5c4fc1d907f676cf4e400f9fa7df2653b
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867264"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-to-manage-azure-cosmos-db-sql-api-account-resources"></a>快速入门：使用 .Net V4 SDK 生成控制台应用以管理 Azure Cosmos DB SQL API 帐户资源。

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

开始使用适用于 .NET 的 Azure Cosmos DB SQL API 客户端库。 按照本文档中的步骤安装 .NET V4 (Azure.Cosmos) 包，生成应用，并尝试对存储在 Azure Cosmos DB 中的数据执行基本 CRUD 操作的示例代码。 

Azure Cosmos DB 是 Microsoft 提供的全球分布式多模型数据库服务。 使用 Azure Cosmos DB，可以快速创建和查询键/值、文档和图形数据库。 使用适用于 .NET 的 Azure Cosmos DB SQL API 客户端库完成以下操作：

* 创建 Azure Cosmos 数据库和容器
* 向容器添加示例数据
* 查询数据 
* 删除数据库

[库源代码](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4) | [包 (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>必备条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)或无需 Azure 订阅即可[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，也无需缴纳费用或承诺金。 
* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core)。 可以通过运行 `dotnet --version` 来验证你的环境中哪个版本可用。

## <a name="setting-up"></a>设置

本部分指导你创建 Azure Cosmos 帐户，并设置使用适用于 .NET 的 Azure Cosmos DB SQL API 客户端库以管理资源的项目。 本文所述的示例代码创建 `FamilyDatabase` 数据库，并在该数据库中创建家庭成员（每个家庭成员都是一个项）。 每个家庭成员都具有 `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` 等属性。 `LastName` 属性用作容器的分区键。 

### <a id="create-account"></a> 创建 Azure Cosmos 帐户

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

### <a id="create-dotnet-core-app"></a>创建新的 .NET 应用

在首选编辑器或 IDE 中创建新的 .NET 应用程序。 从本地计算机打开 Windows 命令提示符或终端窗口。 你将从命令提示符或终端运行接下来的部分中的所有命令。  运行以下 dotnet 新命令，创建名为 `todo` 的新应用。 --langVersion 参数在创建的项目文件中设置 LangVersion 属性。

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

将目录更改为新创建的应用文件夹。 可使用以下代码生成应用程序：

   ```bash
   cd todo
   dotnet build
   ```

内部版本的预期输出应如下所示：

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a id="install-package"></a>安装 Azure Cosmos DB 包

当仍在应用程序目录中时，使用 DotNet 添加包命令安装适用于 .NET 的 Azure Cosmos DB 客户端库。

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>从 Microsoft Azure 门户复制 Azure Cosmos 帐户凭据

此示例应用程序需对 Azure Cosmos 帐户进行身份验证。 为了进行身份验证，应将 Azure Cosmos 帐户凭据传递给应用程序。 按照以下步骤获取 Azure Cosmos 帐户凭据：

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 导航到 Azure Cosmos 帐户。

1. 打开“键”窗格，复制帐户的 URI 和主键    。 下一步需将 URI 和键值添加到某个环境变量。

## <a id="object-model"></a>对象模型

在开始生成应用程序之前，先研究 Azure Cosmos DB 中资源的层次结构以及用于创建和访问这些资源的对象模型。 Azure Cosmos DB 按以下顺序创建资源：

* Azure Cosmos 帐户 
* 数据库 
* 容器 
* Items

若要进一步了解不同实体的层次结构，请参阅[在 Azure Cosmos DB 中使用数据库、容器和项](databases-containers-items.md)。 使用以下 .NET 类与这些资源进行交互：

* CosmosClient - 此类为 Azure Cosmos DB 服务提供客户端逻辑表示。 此客户端对象用于对服务进行配置和执行请求。
* CreateDatabaseIfNotExistsAsync - 若数据库资源不存在，则此方法以异步操作的形式创建数据库资源；若数据库资源已存在，则此方法以异步操作的形式获取它。 
* CreateContainerIfNotExistsAsync - 若容器不存在，则此方法以异步操作的形式创建容器；若容器已存在，则此方法以异步操作的形式获取它。 可查看响应中的状态代码，确定是新创建了容器 (201) 还是返回了现有容器 (200)。 
* CreateItemAsync - 此方法在容器中创建项。
* UpsertItemAsync - 此方法在容器内创建一个项（如果该项尚不存在）或替换该项（如果该项已存在）。 
* GetItemQueryIterator - 此方法使用带有参数化值的 SQL 语句在 Azure Cosmos 数据库的容器下创建项查询。 
* DeleteAsync - 从 Azure Cosmos 帐户中删除指定的数据库。 `DeleteAsync` 方法只删除数据库。

 ## <a id="code-examples"></a>代码示例

本文所述的示例代码在 Azure Cosmos DB 中创建家庭数据库。 家庭数据库包含家庭详细信息，例如名称、地址、位置、关联的父母、子女和宠物。 在将数据填充到 Azure Cosmos 帐户之前，请定义家庭项的属性。 在示例应用程序的根级别创建一个名为 `Family.cs` 的新类，并向其中添加以下代码：

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>添加 using 指令并定义客户端对象

从项目目录，在编辑器中打开 `Program.cs` 文件，并在应用程序顶部添加以下 using 指令：

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


在 `Program` 类中，添加以下全局变量。 其中包括终结点和授权密钥、数据库的名称以及要创建的容器。 确保根据环境替换终结点和授权密钥值。 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

最后，替换 `Main` 方法：

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>创建数据库 

定义 `program.cs` 类中的 `CreateDatabaseAsync` 方法。 该方法创建 `FamilyDatabase`（如果尚不存在）。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>创建容器

定义 `Program` 类中的 `CreateContainerAsync` 方法。 该方法创建 `FamilyContainer`（如果尚不存在）。 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>创建项

通过使用以下代码添加 `AddItemsToContainerAsync` 方法来创建家庭项。 可以使用 `CreateItemAsync` 或 `UpsertItemAsync` 方法来创建项：

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>查询项

插入项后，可以运行查询以获取“Andersen”家庭的详细信息。 以下代码显示如何直接使用 SQL 查询来执行查询。 获取“Anderson”家庭详细信息的 SQL 查询是：`SELECT * FROM c WHERE c.LastName = 'Andersen'`。 在 `Program` 类中定义 `QueryItemsAsync` 方法，并向其中添加以下代码：

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>替换项 

通过使用以下代码添加 `ReplaceFamilyItemAsync` 方法来读取家庭项，然后对其更新。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>删除项 

通过使用以下代码添加 `DeleteFamilyItemAsync` 方法来删除家庭项。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>删除数据库 

最后，可以使用以下代码删除添加 `DeleteDatabaseAndCleanupAsync` 方法的数据库：

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

添加所有必要的方法后，保存 `Program` 文件。 

## <a name="run-the-code"></a>运行代码

接下来，生成并运行应用程序以创建 Azure Cosmos DB 资源。

   ```bash
   dotnet run
   ```

运行应用程序时生成以下输出。 还可以登录到 Microsoft Azure 门户并验证是否已创建资源：

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
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
