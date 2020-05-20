---
title: 将现有数据迁移到 Azure Cosmos DB 中的表 API 帐户
description: 了解如何将本地或云数据迁移或导入到 Azure Cosmos DB 中的 Azure 表 API 帐户。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5c828644cb03d83df38265719cd8afabc24cf739
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "66242573"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>将数据迁移到 Azure Cosmos DB 表 API 帐户

本教程说明如何导入要在 Azure Cosmos DB [表 API](table-introduction.md) 中使用的数据。 如果已将数据存储在 Azure 表存储中，可以使用数据迁移工具或 AzCopy 将数据导入 Azure Cosmos DB 表 API。 如果已将数据存储在 Azure Cosmos DB 表 API（预览版）帐户中，则必须使用数据迁移工具来迁移数据。 

本教程涵盖以下任务：

> [!div class="checklist"]
> * 使用数据迁移工具导入数据
> * 使用 AzCopy 导入数据
> * 从表 API（预览版）迁移到表 API 

## <a name="prerequisites"></a>必备条件

* **增加吞吐量：** 数据迁移的持续时间取决于为单个容器或一组容器设置的吞吐量。 请确保对于较大的数据迁移增加吞吐量。 完成迁移后，减少吞吐量以节约成本。 有关在 Azure 门户中增加吞吐量的详细信息，请参阅 Azure Cosmos DB 中的性能级别和定价层。

* **创建 Azure Cosmos DB 资源：** 在开始迁移数据之前，从 Azure 门户预先创建所有表。 如果要迁移到具有数据库级别吞吐量的 Azure Cosmos DB 帐户，请确保在创建 Azure Cosmos DB 表时提供分区键。

## <a name="data-migration-tool"></a>数据迁移工具

可以使用命令行形式的 Azure Cosmos DB 数据迁移工具 (dt.exe) 将现有 Azure 表存储数据导入到表 API GA 帐户，或者将数据从表 API（预览版）帐户迁移到表 API GA 帐户。 目前不支持其他源。 基于 UI 的数据迁移工具 (dtui.exe) 目不支持表 API 帐户。 

若要执行表数据迁移，请完成以下任务：

1. 从 [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool) 下载迁移工具。
2. 使用适用于你的方案的命令行参数运行 `dt.exe`。 `dt.exe` 采用以下格式的命令：

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

该命令的选项包括：

    /ErrorLog: Optional. Name of the CSV file to redirect data transfer failures
    /OverwriteErrorLog: Optional. Overwrite error log file
    /ProgressUpdateInterval: Optional, default is 00:00:01. Time interval to refresh on-screen data transfer progress
    /ErrorDetails: Optional, default is None. Specifies that detailed error information should be displayed for the following errors: None, Critical, All
    /EnableCosmosTableLog: Optional. Direct the log to a cosmos table account. If set, this defaults to destination account connection string unless /CosmosTableLogConnectionString is also provided. This is useful if multiple instances of DT are being run simultaneously.
    /CosmosTableLogConnectionString: Optional. ConnectionString to direct the log to a remote cosmos table account. 

### <a name="command-line-source-settings"></a>命令行源设置

将 Azure 表存储或表 API 预览版定义为迁移源时，请使用以下源选项。

    /s:AzureTable: Reads data from Azure Table storage
    /s.ConnectionString: Connection string for the table endpoint. This can be retrieved from the Azure portal
    /s.LocationMode: Optional, default is PrimaryOnly. Specifies which location mode to use when connecting to Azure Table storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
    /s.Table: Name of the Azure Table
    /s.InternalFields: Set to All for table migration as RowKey and PartitionKey are required for import.
    /s.Filter: Optional. Filter string to apply
    /s.Projection: Optional. List of columns to select

在从 Azure 表存储进行导入时，若要检索源连接字符串，请打开 Azure 门户并单击“存储帐户” > “帐户” > “访问密钥”，然后使用复制按钮复制**连接字符串**。

![HBase 源选项的屏幕截图](./media/table-import/storage-table-access-key.png)

在从 Azure 表存储进行导入时，若要检索源连接字符串，请打开 Azure 门户并单击“存储帐户” > “帐户” > “访问密钥”，然后使用复制按钮复制**连接字符串**。

![HBase 源选项的屏幕截图](./media/table-import/cosmos-connection-string.png)

[示例 Azure 表存储命令](#azure-table-storage)

[示例 Azure Cosmos DB 表 API（预览版）命令](#table-api-preview)

### <a name="command-line-target-settings"></a>命令行目标设置

将 Azure Cosmos DB 表 API 定义为迁移目标时，请使用以下目标选项。

    /t:TableAPIBulk: Uploads data into Azure CosmosDB Table in batches
    /t.ConnectionString: Connection string for the table endpoint
    /t.TableName: Specifies the name of the table to write to
    /t.Overwrite: Optional, default is false. Specifies if existing values should be overwritten
    /t.MaxInputBufferSize: Optional, default is 1GB. Approximate estimate of input bytes to buffer before flushing data to sink
    /t.Throughput: Optional, service defaults if not specified. Specifies throughput to configure for table
    /t.MaxBatchSize: Optional, default is 2MB. Specify the batch size in bytes

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>示例命令：源是 Azure 表存储

下面的命令行示例展示了如何从 Azure 表存储导入到表 API：

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```
<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>示例命令：源是 Azure Cosmos DB 表 API（预览版）

下面的命令行示例展示了如何从表 API 预览版导入到表 API GA：

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>使用 AzCopy 迁移数据

使用 AzCopy 命令行实用工具是将数据从 Azure 表存储迁移到 Azure Cosmos DB 表 API 的另一个选项。 若要使用 AzCopy，首先需要根据[从表存储导出数据](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage)中所述导出数据，然后根据 [Azure Cosmos DB 表 API](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage) 中所述将数据导入到 Azure Cosmos DB。

在执行到 Azure Cosmos DB 的导入时，请参阅以下示例。 注意，/Dest 值使用的是 cosmosdb，而不是 core。

示例导入命令：

```
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>从表 API（预览版）迁移到表 API

> [!WARNING]
> 如果希望立即享受正式版表带来的好处，请根据本部分中所述迁移现有的预览版表，否则，我们将在接下来的几周内为现有的预览版客户执行自动迁移，但请注意，自动迁移的预览版表将具有一些限制，而新创建的表则不会有这些限制。
> 

表 API 现在已为正式版 (GA)。 表的预览版和 GA 版之间有一些差异，这同时体现于在云中运行的代码和在客户端运行的代码。 因此，建议不要尝试将预览版 SDK 客户端与 GA 表 API 帐户混合使用，反之亦然。 对于希望在生产环境中继续使用其现有表的表 API 预览版客户，需要从预览版迁移到 GA 环境，或者等待自动迁移。 如果等待自动迁移，则会向你发送通知，指明迁移的表的限制。 在迁移后，将能够在现有帐户上创建没有限制的新表（只有迁移的表才会有限制）。

要从表 API（预览版）迁移到正式版表 API，请执行以下操作：

1. 如[创建数据库帐户](create-table-dotnet.md#create-a-database-account)中所述，创建一个新的 Azure Cosmos DB 帐户并将其 API 类型设置为 Azure 表。

2. 将客户端更改为使用[表 API SDK](table-sdk-dotnet.md) 的 GA 版。

3. 使用数据迁移工具将客户端数据从预览版表迁移到 GA 表。 [数据迁移工具](#data-migration-tool)中提供了将数据迁移工具用于此用途的说明。 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 使用数据迁移工具导入数据
> * 使用 AzCopy 导入数据
> * 从表 API（预览版）迁移到表 API

现在可以继续学习下一教程并了解如何使用 Azure Cosmos DB 表 API 查询数据。 

> [!div class="nextstepaction"]
>[如何查询数据？](../cosmos-db/tutorial-query-table.md)
