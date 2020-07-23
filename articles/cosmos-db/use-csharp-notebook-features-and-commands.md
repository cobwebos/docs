---
title: 使用 Azure Cosmos DB C# 笔记本中的内置笔记本命令和功能（预览版）
description: 了解如何通过 Azure Cosmos DB 内置 C# 笔记本使用内置命令和功能来执行常见操作。
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.custom: tracking-python
ms.openlocfilehash: d9d48e825adeecd54375ce13c612d4a0c6eaaa18
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85263409"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-c-notebooks-preview"></a>使用 Azure Cosmos DB C# 笔记本中的内置笔记本命令和功能（预览版）

使用 Azure Cosmos DB 中的内置 Jupyter 笔记本可以从 Azure 门户分析和可视化数据。 本文介绍如何在 C# 笔记本中使用内置笔记本命令和功能执行常见操作。

## <a name="install-a-new-nuget-package"></a>安装新的 NuGet 包
为 Azure Cosmos 帐户启用笔记本支持后，可以打开新的笔记本并安装一个包。

在新的代码单元中，插入并运行以下代码，将 ``PackageToBeInstalled`` 替换为所需的 NuGet 包，如果需要，将 ``optionalVersion`` 替换为特定版本的包。 

```csharp
#r "nuget: PackageToBeInstalled, optionalVersion"
```

可以在同一单元中安装多个 NuGet 包。 可以从 Azure Cosmos 帐户工作区中的任何笔记本使用此包。 

目前，C# 笔记本工作区不支持 NuGet 包的递归解析。 如果 NuGet 包对当前未安装的其他 NuGet 包具有依赖关系，则必须显式引用它们以及父包。

> [!TIP]
> 如果你的笔记本需要某个自定义包，我们建议在笔记本中添加一个单元来安装该包，并使其成为第一个单元。 这减少了与 Azure Cosmos DB 默认加载的其他包发生冲突的可能性。 即使[重置工作区](#reset-notebooks-workspace)（这会删除所有包），也可以轻松地重新安装包。 

## <a name="use-the-built-in-azure-cosmos-db-net-sdk"></a>使用内置的 Azure Cosomos DB .NET SDK
已安装 [Azure Cosmos DB .NET SDK for SQL API](https://github.com/Azure/azure-cosmos-dotnet-v3) 版本 3，并且它已包含在 Azure Cosmos 帐户的笔记本环境中。

创建 ``CosmosClient`` 实例以运行任何 SDK 操作。 

例如：

```csharp
// Include usings
using System;
using Microsoft.Azure.Cosmos; //namespace for Azure Cosmos DB .NET V3 SDK
using System.Collections;

// Initialize a new instance of CosmosClient using the built-in account endpoint and key parameters
CosmosClient cosmosClient = new CosmosClient(Cosmos.Endpoint, Cosmos.Key);

// Create a new database and container with 400 RU/s
Database database = await cosmosClient.CreateDatabaseIfNotExistsAsync("DatabaseName");
Container container = await database.CreateContainerIfNotExistsAsync("ContainerName", "/partitionKey", 400);
```
要了解详细信息，请参阅 [.NET V3 SDK 示例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage)。 

> [!IMPORTANT]
> SQL (Core) API 帐户仅支持内置 Azure Cosmos DB .NET SDK。 对于其他 API，需要[安装对应于 API 的相关 .NET 驱动程序](#install-a-new-nuget-package)。 

## <a name="set-custom-options-using-cosmosclientoptions"></a>使用 ``CosmosClientOptions`` 设置自定义选项
为了更灵活，可以设置自定义 ``CosmosClientOptions`` 属性，并将其传递到 ``CosmosClient`` 实例中。 你可以使用此属性执行以下操作：

- 在用户代理后缀中设置应用程序名称，以将其包含在每个请求中。
- 设置对服务运行操作时要使用的首选区域。
- 设置自定义重试策略以处理速率限制的请求。

有关所有支持的设置，请参阅 [CosmosClientOptions API 参考](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions)一文。 下面是演示如何设置属性 `cosmosClientOptions` 的示例：

```csharp
using Microsoft.Azure.Cosmos;

// Configure CosmosClientOptions
var cosmosClientOptions = new CosmosClientOptions
{
    ApplicationName = "ContosoNotebookAppName",
    ApplicationRegion = Regions.RegionName, // By default, this is the region you first created your account in
    MaxRetryAttemptsOnRateLimitedRequests = 9, // By default, this value is 9
};

var client = new CosmosClient(Cosmos.Endpoint, Cosmos.Key, cosmosClientOptions);
```

## <a name="access-the-account-endpoint-and-primary-key-variables"></a>访问帐户终结点和主密钥变量
可以访问你的笔记本所在的 Azure Cosmos 帐户的内置终结点和密钥。

```csharp
var key = Cosmos.Key;
var endpoint = Cosmos.Endpoint;
```

> [!IMPORTANT]
> ``Cosmos.Key`` 和 ``Cosmos.Endpoint`` 变量仅适用于 SQL API。 对于其他 API，可在 Azure Cosmos 帐户的“连接字符串”或“密钥”边栏选项卡中找到该终结点和密钥 。  

## <a name="print-console-output-in-c-code"></a>以 C# 代码输出控制台输出
在 C# 代码中，可以使用带有[字符串内插](/dotnet/csharp/language-reference/tokens/interpolated)的 Display.AsMarkdown() 语法来输出运行单元格时将显示的控制台输出。 

例如： 

```csharp
// Print text in the output
Display.AsMarkdown($"Hello world!");

// Print a variable in the output
for (int i = 0; i < 5; i++) {
    Display.AsMarkdown($"Printing out variable: {i}");
}
```
> [!IMPORTANT]
> C# 笔记本当前不支持 Console.WriteLine() 语法。 使用 Display.AsMarkdown 从程序中输出控制台输出。 

## <a name="use-built-in-nteract-data-explorer"></a>使用内置的 nteract 数据资源管理器
可以使用内置的 [nteract 数据资源管理器](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897)来筛选和可视化项的集合。 在单元中，将要可视化的变量放在最后一行中，在运行单元时，该变量会自动在 nteract 中显示。

例如，在 GetingStarted_Csharp.ipynb 示例中，我们可以在结果中输出变量 ``telemetryEvents``。 有关整个示例，请参阅 [GettingStarted_Csharp.ipynb 笔记本](https://github.com/Azure-Samples/cosmos-notebooks/blob/master/CSharp_quickstarts/GettingStarted_CSharp.ipynb)。 

:::image type="content" source="media/use-notebook-features-and-commands/csharp-query-cell.png" alt-text="Csharp 查询单元":::

:::image type="content" source="media/use-notebook-features-and-commands/csharp-nteract-built-in-chart.png" alt-text="nteract 数据资源管理器":::

## <a name="use-built-in-dictionary-viewer"></a>使用内置字典查看器
可以使用内置字典查看器查看变量。 在单元格中，将要可视化的变量放在最后一行中，在运行单元时，该变量会自动显示。

:::image type="content" source="media/use-notebook-features-and-commands/csharp-built-in-dictionary-viewer.png" alt-text="内置字典查看器":::

## <a name="upload-json-items-to-a-container"></a>将 JSON 项上传到容器
可以使用 ``%%upload`` magic 命令将 JSON 文件中的数据上传到指定的 Azure Cosmos 容器。 运行以下命令来上传项：

```csharp
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- 请将 ``{database_id}`` 和 ``{container_id}`` 替换为 Azure Cosmos 帐户中的数据库和容器的名称。 
- 请将 ``{url_location_of_file}`` 替换为 JSON 文件的位置。 该文件必须是有效 JSON 对象的数组，并且应可通过公共 Internet 访问它。

例如：

```csharp
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```

可以使用输出统计信息计算用于上传项的有效 RU/秒。 例如，如果在 38 秒内消耗了 25,000 个 RU，则有效 RU/秒为 25,000 RU / 38 秒 = 658 RU/秒。

## <a name="run-another-notebook-in-current-notebook"></a>在当前笔记本中运行另一个笔记本 
可以使用 ``%%run`` magic 命令从当前笔记本运行工作区中的另一个笔记本。 使用语法：

```csharp
%%run ./path/to/{notebookName}.ipynb
```
请将 ``{notebookName}`` 替换为要运行的笔记本的名称。 该笔记本必须位于当前的“我的笔记本”工作区。 

## <a name="reset-notebooks-workspace"></a>重置笔记本工作区
若要将笔记本工作区重置为默认设置，请在命令栏上选择“重置工作区”。 这会删除所有自定义的已安装包，并重启 Jupyter 服务器。 笔记本、文件和 Azure Cosmos 资源不受影响。  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="重置笔记本工作区":::

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Cosmos DB Jupyter 笔记本](cosmosdb-jupyter-notebooks.md)的优势
- 了解 [Azure Cosmos DB .NET SDK for SQL API](https://github.com/Azure/azure-cosmos-dotnet-v3)
