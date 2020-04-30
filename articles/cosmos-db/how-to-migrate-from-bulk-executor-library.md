---
title: 从大容量执行库迁移到 Azure Cosmos DB .NET V3 SDK 中的批量支持
description: 了解如何将应用程序从使用批量执行程序库迁移到 Azure Cosmos DB SDK V3 中的批量支持
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: maquaran
ms.openlocfilehash: d63b34c118cd719f73abbd6711dcb3ef02a6fb28
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146293"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>从大容量执行库迁移到 Azure Cosmos DB .NET V3 SDK 中的批量支持

本文介绍将使用[.net 批量执行程序库](bulk-executor-dot-net.md)的现有应用程序的代码迁移到最新版本的 .net SDK 中的[批量支持](tutorial-sql-api-dotnet-bulk-import.md)功能所需的步骤。

## <a name="enable-bulk-support"></a>启用批量支持

通过`CosmosClient` [AllowBulkExecution](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution)配置在实例上启用批量支持：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>为每个操作创建任务

.NET SDK 中的批量支持通过利用并行执行的[任务并行库](https://docs.microsoft.com/dotnet/standard/parallel-programming/task-parallel-library-tpl)和分组操作来工作。 

SDK 中不存在将文档或操作列表作为输入参数的一种方法，而是需要为要批量执行的每个操作创建一个任务，然后只需等待它们完成。

例如，如果初始输入是一个项列表，其中的每一项都具有以下架构：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

如果要进行大容量导入（类似于使用 BulkExecutor. BulkImportAsync），则需要对执行并发调用`CreateItemAsync`。 例如：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

如果要进行大容量*更新*（类似于使用 BulkUpdateAsync），则在更新项值后，需要对`ReplaceItemAsync`方法进行并发调用[BulkExecutor。](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync) 例如：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

如果要执行批量*删除*（类似于使用[BulkExecutor. BulkDeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)），则需要具有每个项的并发调用`DeleteItemAsync`，以及每个`id`项的和分区键。 例如：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>捕获任务结果状态

在前面的代码示例中，我们创建了一个并发任务列表，并对这些`CaptureOperationResponse`任务调用了方法。 此方法是一个扩展，通过捕获任何错误并跟踪[请求单位的使用](request-units.md)情况，我们可以将*类似的响应架构*维护为 BulkExecutor。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

`OperationResponse`其中，声明为：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>并发执行操作

若要跟踪任务的整个列表范围，请使用以下帮助器类：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkOperationsHelper":::

此`ExecuteAsync`方法将一直等待，直到完成所有操作，并且可以使用它，如下所示：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>捕获统计信息

前面的代码将一直等待，直到完成所有操作并计算所需的统计信息。 这些统计信息类似于大容量执行程序库的[BulkImportResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse)。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

`BulkOperationResponse`包含：

1. 通过批量支持处理操作列表所用的总时间。
1. 操作成功的次数。
1. 使用的请求单位总数。
1. 如果发生失败，它将显示包含异常的元组的列表以及用于记录和标识目的的关联项。

## <a name="retry-configuration"></a>重试配置

大容量执行程序库中有一些[指导](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account)， `MaxRetryWaitTimeInSeconds`将`MaxRetryAttemptsOnThrottledRequests` [RetryOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions)的和`0`设置为，以将控件委托给库。

对于 .NET SDK 中的批量支持，没有隐藏的行为。 你可以通过[CosmosClientOptions. MaxRetryAttemptsOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests)和[CosmosClientOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests)直接配置重试选项。

> [!NOTE]
> 如果预配的请求单位的数据量远远低于预期值，则可能需要考虑将它们设置为较高的值。 大容量操作将花费较长的时间，但由于重试次数较重，此操作可能会完全成功。

## <a name="performance-improvements"></a>性能改进

与 .NET SDK 的其他操作一样，使用流 Api 可以提高性能，并避免任何不必要的序列化。 

仅当你使用的数据的性质与字节流（如文件流）的性质匹配时，才可以使用流 Api。 在这种情况下， `CreateItemStreamAsync`使用`ReplaceItemStreamAsync`、或`DeleteItemStreamAsync`方法并使用`ResponseMessage` （而不是`ItemResponse`）会增加可实现的吞吐量。

## <a name="next-steps"></a>后续步骤

* 若要了解有关 .NET SDK 版本的详细信息，请参阅[AZURE COSMOS DB SDK](sql-api-sdk-dotnet.md)文章。
* 从 GitHub 获取完整的[迁移源代码](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration)。
* [GitHub 上的其他批量示例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)
