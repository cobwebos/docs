---
title: 从批量执行器库迁移到 Azure Cosmos DB .NET V3 SDK 中的批量支持
description: 了解如何将应用程序从使用批量执行器库迁移到 Azure Cosmos DB SDK V3 中的批量支持
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: maquaran
ms.openlocfilehash: e1a2a5d849d3c94d62b8645c41f288ba130aa6a4
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479326"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>从批量执行器库迁移到 Azure Cosmos DB .NET V3 SDK 中的批量支持

本文介绍了将使用[.NET 批量执行器库](bulk-executor-dot-net.md)的现有应用程序代码迁移到最新版本 .NET SDK 中的[批量支持](tutorial-sql-api-dotnet-bulk-import.md)功能所需的步骤。

## <a name="enable-bulk-support"></a>启用批量支持

通过`CosmosClient`["允许批量执行"](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution)配置对实例启用批量支持：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>为每个操作创建任务

.NET SDK 中的批量支持的工作原理是利用[任务并行库](https://docs.microsoft.com/dotnet/standard/parallel-programming/task-parallel-library-tpl)并同时发生的操作分组。 

没有一种方法将文档或操作列表作为输入参数，而是需要为要批量执行的每个操作创建一个任务。

例如，如果初始输入是每个项目具有以下架构的项列表：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

如果要执行批量导入（类似于使用 BulkExecutor.BulkImportAsync），则需要对每个项值进行并发调用`CreateItemAsync`。 例如：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

如果要执行批量*更新*（类似于使用[BulkExecutor.BulkUpdateAsync），](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)则需要在更新项值后对`ReplaceItemAsync`方法进行并发调用。 例如：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

如果要执行批量*删除*（类似于使用[BulkExecutor.BulkDeleteAsync），](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)则需要使用 每个项目的`DeleteItemAsync``id`和 分区键对 例如：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>捕获任务结果状态

在前面的代码示例中，您已经创建了任务的并发列表，并在每个任务上调用`CaptureOperationResponse`了 方法。 此方法是一个扩展，它允许我们通过捕获任何错误和跟踪[请求单位使用情况](request-units.md)来维护与 BulkExecutor*类似的响应架构*。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

其中声明`OperationResponse`为：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>同时执行操作

定义任务列表后，请等待，直到它们全部完成。 您可以通过定义批量操作的范围来跟踪任务的完成情况，如以下代码段所示：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>捕获统计信息

前面的代码等待所有操作完成并计算所需的统计信息。 这些统计数据与批量执行器库的[批量导入响应相似](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse)。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

包含`BulkOperationResponse`：

1. 通过批量支持处理操作列表所占用的总时间。
1. 成功操作的数量。
1. 已使用的请求单位总数。
1. 如果存在失败，它将显示包含异常的元数列表以及用于日志记录和标识目的的关联项。

## <a name="performance-improvements"></a>性能改进

与 .NET SDK 的其他操作一样，使用流 API 可提高性能并避免任何不必要的序列化。 

仅当使用的数据的性质与字节流（例如文件流）的性质匹配时，才可能使用流 API。 在这种情况下，使用`CreateItemStreamAsync`、`ReplaceItemStreamAsync`或`DeleteItemStreamAsync`方法以及使用`ResponseMessage`（而不是`ItemResponse`） 可提高可实现的吞吐量。

## <a name="next-steps"></a>后续步骤

* 要了解有关 .NET SDK 版本的更多，请参阅[Azure 宇宙 DB SDK](sql-api-sdk-dotnet.md)一文。
* 从 GitHub 获取完整的[迁移源代码](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration)。
* [GitHub 上的其他批量示例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)
