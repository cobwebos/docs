---
title: 使用 .NET Standard SDK 的 Azure Cosmos DB 表 API
description: 了解如何在 Azure Cosmos DB 表 API 帐户中存储和查询结构化数据
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 56198392f3c769837d8d672b861baa9b341d284e
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89419344"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>通过 .NET SDK 开始使用 Azure Cosmos DB 表 API 和 Azure 表存储

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

可以使用 Azure Cosmos DB 表 API 或 Azure 表存储将结构化的 NoSQL 数据存储在云中，以便通过无架构设计提供键/属性存储。 由于 Azure Cosmos DB 表 API 和表存储是无架构的，因此随着应用程序需求的发展，可以轻松调整数据。 可以使用 Azure Cosmos DB 表 API 或表存储来存储灵活的数据集，例如 Web 应用程序的用户数据、通讯簿、设备信息，或者服务需要的其他类型的元数据。 

本教程介绍了一个示例，展示如何将[适用于 .NET 的 Microsoft Azure Cosmos DB 表库](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)用于 Azure Cosmos DB 表 API 和 Azure 表存储方案。 必须使用特定于 Azure 服务的连接。 这些方案使用 C# 示例进行讨论，说明了如何创建表、插入/更新数据、查询数据和删除表。

## <a name="prerequisites"></a>先决条件

若要成功完成此示例，需要以下项：

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [适用于 .NET 的 Microsoft Azure CosmosDB 表库](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) - 此库目前仅适用于 .NET Standard 和 .NET 框架。 

* [Azure Cosmos DB 表 API 帐户](create-table-dotnet.md#create-a-database-account)。

## <a name="create-an-azure-cosmos-db-table-api-account"></a>创建 Azure Cosmos DB 表 API 帐户

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>创建 .NET 控制台项目

在 Visual Studio 中，创建新的 .NET 控制台应用程序。 以下步骤演示了如何在 Visual Studio 2019 中创建控制台应用程序。 可以在任意类型的 .NET 应用程序（包括 Azure 云服务或 Web 应用，以及桌面和移动应用程序）中使用 Azure Cosmos DB 表库。 为简单起见，我们在本指南中使用控制台应用程序。

1. 选择“文件” > “新建” > “项目”  。

1. 选择“控制台应用程序 (.NET Core)”，然后选择“下一个” 。

1. 在“项目名称”字段中，输入应用程序的名称，例如“CosmosTableSamples” 。 （可根据需要提供其他名称。）

1. 选择“创建”。

本示例中的所有代码示例都可以添加到控制台应用程序的 Program.cs 文件的 Main() 方法。

## <a name="install-the-required-nuget-package"></a>安装所需的 NuGet 包

要获取 NuGet 包，请执行以下步骤：

1. 在“解决方案资源管理器”中，右键单击项目并选择“管理 NuGet 包”。

1. 联机搜索 [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)、[`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration)、[`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json)、[`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder)，并选择“安装”以安装 Microsoft Azure Cosmos DB 表库。

## <a name="configure-your-storage-connection-string"></a>配置存储连接字符串

1. 从 [Azure 门户](https://portal.azure.com/)，导航到 Azure Cosmos 帐户或表存储帐户。 

1. 打开“连接字符串”或“访问密钥”窗格 。 使用窗口右侧的复制按钮复制“主连接字符串”。

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="“连接字符串”窗格中的“主连接字符串”":::
   
1. 要配置连接字符串，请在 Visual Studio 中右键单击项目“CosmosTableSamples”。

1. 依次选择“添加”、“新项” 。 创建新文件“Settings.json”，其文件类型为“TypeScript JSON 配置”文件 。 

1. 将 Settings.json 文件中的代码替换为以下代码并分配主连接字符串：

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. 右键单击项目“CosmosTableSamples”。 选择“添加”、“新项”，并添加名为“AppSettings.cs”的类  。

1. 将以下代码添加到 AppSettings.cs 文件。 此文件从 Settings.json 文件中读取连接字符串，并将其分配给配置参数：

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/AppSettings.cs":::

## <a name="parse-and-validate-the-connection-details"></a>分析和验证连接详细信息

1. 右键单击项目“CosmosTableSamples”。 选择“添加”、“新项”，并添加名为“Common.cs”的类  。 将编写代码来验证连接详细信息并在此类中创建表。

1. 按如下所示定义方法 `CreateStorageAccountFromConnectionString`。 此方法将分析连接字符串详细信息，并验证“Settings.json”文件中提供的帐户名称和帐户密钥详细信息是否有效。

   :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="createStorageAccount":::

## <a name="create-a-table"></a>创建表 

[CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) 类用于检索存储在表存储中的表和实体。 由于 Cosmos DB 表 API 帐户中没有任何表，请将 `CreateTableAsync` 方法添加到 Common.cs 类来创建表：

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="CreateTable":::

如果收到“503 服务不可用异常”错误，则连接模式所需的端口可能已被防火墙阻止。 若要解决此问题，打开所需端口或使用网关模式连接，如以下代码所示：

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>定义实体 

实体使用派生自 [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity)的自定义类映射到 C# 对象。 要将实体添加到表，请创建用于定义实体的属性的类。

右键单击项目“CosmosTableSamples”。 选择“添加”、“新建文件夹”并将其命名为“模型”  。 在“模型”文件夹中添加名为“CustomerEntity.cs”的类，并向其添加以下代码。

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Model/CustomerEntity.cs":::

此代码定义将客户的名字和姓氏分别用作行键和分区键的实体类。 实体的分区键和行键共同唯一地标识表中的实体。 分区键相同的实体可以实现比分区键不同的实体更快的查询速度，但使用不同的分区键可实现更高的并行操作可伸缩性。 需要存储在表中的实体必须是受支持的类型，例如，派生自 [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity) 类。 要存储在表中的实体属性必须是相应类型的公共属性，并且允许获取和设置值。 此外，实体类型必须公开无参数构造函数。

## <a name="insert-or-merge-an-entity"></a>插入或合并实体

以下代码示例创建实体对象并将其添加到表中。 [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) 类中的 InsertOrMerge 方法用于插入或合并实体。 调用 [CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) 方法来执行此操作。 

右键单击项目“CosmosTableSamples”。 选择“添加”、“新项”，并添加名为“SamplesUtils.cs”的类  。 此类存储对实体执行 CRUD 操作所需的全部代码。 

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="InsertItem":::

## <a name="get-an-entity-from-a-partition"></a>从分区获取实体

可以通过使用 [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) 类下的 Retrieve 方法从分区获取实体。 以下代码示例获取客户实体的分区键、行键、电子邮件和电话号码。 此示例还打印出用于查询实体的请求单元。 要查询实体，请将以下代码追加到“SamplesUtils.cs”文件：

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="QueryData":::

## <a name="delete-an-entity"></a>删除实体

在检索实体之后，可使用更新实体的相同演示模式轻松删除该实体。 以下代码检索并删除一个客户实体。 要删除实体，请将以下代码追加到“SamplesUtils.cs”文件： 

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="QueryData":::

## <a name="execute-the-crud-operations-on-sample-data"></a>对示例数据执行 CRUD 操作

定义创建表、插入或合并实体的方法后，对示例数据运行这些方法。 要执行此操作，请右键单击项目“CosmosTableSamples”。 选择“添加”、“新项”，然后添加名为“BasicSamples.cs”的类，并向其添加以下代码  。 此代码将创建表并向该表添加实体。

如果不希望删除项目末尾的实体和表，请在以下代码中注释 `await table.DeleteIfExistsAsync()` 和 `SamplesUtils.DeleteEntityAsync(table, customer)` 方法。 最好在删除表之前注释掉这些方法并验证数据。

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/BasicSamples.cs":::

上述代码会创建以“demo”开头的表并将生成的 GUID 追加到表名。 然后，它会添加姓名为“Harp Walter”的客户实体，并稍后更新此用户的电话号码。 

在本教程中，你生成了对表 API 帐户中存储的数据执行基本 CRUD 操作的代码。 还可以执行高级操作，例如批量插入数据、查询分区中的所有数据、查询分区中的一系列数据、列出名称以指定前缀开头的帐户中的表。 可以从 [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) GitHub 存储库中下载完整的示例。 [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) 类具有可对数据执行的更多操作。  

## <a name="run-the-project"></a>运行项目

从项目“CosmosTableSamples”， 打开名为“Program.cs”的类。然后将以下代码添加到其中，以便在项目运行时调用 BasicSamples。

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Program.cs":::

立即生成解决方案并按 F5 运行该项目。 运行项目时，将在命令提示符中看到以下输出：

:::image type="content" source="./media/tutorial-develop-table-standard/output-from-sample.png" alt-text="来自命令提示符的输出":::

如果收到说明在运行项目时无法找到 Settings.json 文件的错误，可以通过将以下 XML 条目添加到项目设置来解决该问题。 右键单击 CosmosTableSamples，选择“编辑 CosmosTableSamples.csproj”并添加以下 itemGroup： 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
现在可以登录到 Azure 门户，并验证表中是否存在数据。 

:::image type="content" source="./media/tutorial-develop-table-standard/results-in-portal.png" alt-text="门户中的结果":::

## <a name="next-steps"></a>后续步骤

现在可以继续学习下一教程，了解如何将数据迁移到 Azure Cosmos DB 表 API 帐户。 

> [!div class="nextstepaction"]
>[如何查询数据](../cosmos-db/table-import.md)
