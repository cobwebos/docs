---
title: 快速入门：使用 Azure 数据资源管理器 Node 库引入数据
description: 本快速入门介绍如何使用 Node.js 在 Azure 数据资源管理器中引入（加载）数据。
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 10/25/2018
ms.openlocfilehash: c638369efc89ca4442b69c9337827fe3872fd197
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085954"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-node-library"></a>快速入门：使用 Azure 数据资源管理器 Node 库引入数据

Azure 数据资源管理器是一项快速且高度可缩放的数据探索服务，适用于日志和遥测数据。 Azure 数据资源管理器为 Node 提供了两个客户端库：[引入库](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest)和[数据库](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data)。 可以使用这些库在群集中引入（加载）数据并从代码中查询数据。 在本快速入门，首先在测试群集中创建一个表和数据映射。 然后将引入排列到群集并验证结果。

如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

除 Azure 订阅外，还需以下条件才能完成此快速入门：

* [测试群集和数据库](create-cluster-database-portal.md)

* 已在开发计算机上安装了 [Node.js](https://nodejs.org/en/download/)

## <a name="install-the-data-and-ingest-libraries"></a>安装数据和引入库

安装 *azure-kusto-ingest* 和 *azure-kusto-data*

```bash
npm i --save azure-kusto-ingest azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>添加导入语句和常量

从库中导入类

```javascript
const KustoClient = require('azure-kusto-data').KustoClient;
const KustoIngestClient = require('azure-kusto-ingest').KustoIngestClient;
const KustoConnectionStringBuilder = require('azure-kusto-ingest').KustoConnectionStringBuilder;
```

Azure 数据资源管理器使用 Azure Active Directory 租户 ID，以对应用程序进行身份验证。 若要查找租户 ID，请按[查找 Office 365 租户 ID](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id) 中的说明操作。

为 `authorityId`、`kustoUri`、`kustoIngestUri` 和 `kustoDatabase` 设置值，然后运行以下代码。

```javascript
const authorityId = "<TenantId>";
const kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
const kustoIngestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
const kustoDatabase  = "<DatabaseName>";
```

现在构造连接字符串。 此示例使用设备身份验证来访问群集。 还可以使用 Azure Active Directory 应用程序证书、应用程序密钥以及用户和密码。

在后续步骤中创建目标表和映射。

```javascript
const kcsbIngest = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoIngestUri, authorityId);
const kcsbData = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoUri, authorityId);
const destTable = "StormEvents";
const destTableMapping = "StormEvents_CSV_Mapping";
```

## <a name="set-source-file-information"></a>设置源文件信息

导入其他类并设置数据源文件的常数。 此示例使用 Azure Blob 存储上托管的示例文件。 StormEvents 示例数据集包含[美国国家环境信息中心](https://www.ncdc.noaa.gov/stormevents/)中与天气相关的数据。

```javascript
from azure.storage.blob import BlockBlobService
from azure.kusto.ingest import KustoIngestClient, IngestionProperties, FileDescriptor, BlobDescriptor, DataFormat, ReportLevel, ReportMethod

const container = "samplefiles";
const account = "kustosamplefiles";
const sas = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
const filePath = "StormEvents.csv";
const fileSize = 64158321; // in bytes
const blobPath = `https://${account}.blob.core.windows.net/${container}/${filePath}${sas}`;
```

## <a name="create-a-table-on-your-test-cluster"></a>在测试群集上创建表

创建与 `StormEvents.csv` 文件中的数据架构匹配的表。 运行此代码时，它会返回如下消息：若要登录，请使用 Web 浏览器打开页 https://microsoft.com/devicelogin，然后输入代码 XXXXXXXXX 进行身份验证。 按照步骤登录，然后返回运行下一个代码块。 建立连接的后续代码块会要求你再次登录。

```javascript
const kustoClient = new KustoClient(kcsbData);
const createTableCommand = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)";

kustoClient.executeMgmt(kustoDatabase, createTableCommand, (err, results) => {
    console.log(result.primaryResults[0]);
});
```

## <a name="define-ingestion-mapping"></a>定义引入映射

将传入的 CSV 数据映射到创建表时使用的列名称和数据类型。

```javascript
const createMappingCommand = `.create table StormEvents ingestion csv mapping ${destTableMapping} '[{\"Name\":\"StartTime\",\"datatype\":\"datetime\",\"Ordinal\":0}, {\"Name\":\"EndTime\",\"datatype\":\"datetime\",\"Ordinal\":1},{\"Name\":\"EpisodeId\",\"datatype\":\"int\",\"Ordinal\":2},{\"Name\":\"EventId\",\"datatype\":\"int\",\"Ordinal\":3},{\"Name\":\"State\",\"datatype\":\"string\",\"Ordinal\":4},{\"Name\":\"EventType\",\"datatype\":\"string\",\"Ordinal\":5},{\"Name\":\"InjuriesDirect\",\"datatype\":\"int\",\"Ordinal\":6},{\"Name\":\"InjuriesIndirect\",\"datatype\":\"int\",\"Ordinal\":7},{\"Name\":\"DeathsDirect\",\"datatype\":\"int\",\"Ordinal\":8},{\"Name\":\"DeathsIndirect\",\"datatype\":\"int\",\"Ordinal\":9},{\"Name\":\"DamageProperty\",\"datatype\":\"int\",\"Ordinal\":10},{\"Name\":\"DamageCrops\",\"datatype\":\"int\",\"Ordinal\":11},{\"Name\":\"Source\",\"datatype\":\"string\",\"Ordinal\":12},{\"Name\":\"BeginLocation\",\"datatype\":\"string\",\"Ordinal\":13},{\"Name\":\"EndLocation\",\"datatype\":\"string\",\"Ordinal\":14},{\"Name\":\"BeginLat\",\"datatype\":\"real\",\"Ordinal\":16},{\"Name\":\"BeginLon\",\"datatype\":\"real\",\"Ordinal\":17},{\"Name\":\"EndLat\",\"datatype\":\"real\",\"Ordinal\":18},{\"Name\":\"EndLon\",\"datatype\":\"real\",\"Ordinal\":19},{\"Name\":\"EpisodeNarrative\",\"datatype\":\"string\",\"Ordinal\":20},{\"Name\":\"EventNarrative\",\"datatype\":\"string\",\"Ordinal\":21},{\"Name\":\"StormSummary\",\"datatype\":\"dynamic\",\"Ordinal\":22}]'`;

kustoClient.executeMgmt(kustoDatabase, createMappingCommand, (err, results) => {
    console.log(result.primaryResults[0]);
});
```

## <a name="queue-a-message-for-ingestion"></a>列入一条引入消息

将一条消息排入队列，以便从 blob 存储中提取数据并将该数据引入到 Azure 数据资源管理器。

```javascript
const { DataFormat, JsonColumnMapping } =  require("azure-kusto-ingest").IngestionPropertiesEnums;
const { BlobDescriptor } = require("azure-kusto-ingest").Descriptors;
const ingestClient = new KustoIngestClient(kcsbIngest);


// All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties
const ingestionProps  = new IngestionProperties(kustoDatabase, destTable, DataFormat.csv, null,destTableMapping, {'ignoreFirstRecord': 'true'});
const blobDesc = new BlobDescriptor(blobPath, 10);
ingestClient.ingestFromBlob(blobDesc,ingestionProps, (err) => {
    if (err) throw new Error(err);
});
```

## <a name="validate-that-table-contains-data"></a>验证表是否包含数据

验证数据已引入表中。 等待五到十分钟，直到排入队列的引入已计划在 Azure 数据资源管理器中引入和加载数据。 然后运行以下代码，以获取 `StormEvents` 表中记录的计数。

```javascript
const query = "StormEvents | count";

kustoClient.execute(kustoDatabse, query, (err, results) => {
    if (err) throw new Error(err);  
    console.log(results.primaryResults[0].toString());
});
```

## <a name="run-troubleshooting-queries"></a>运行故障排除查询

登录到 [https://dataexplorer.azure.com](https://dataexplorer.azure.com) 并连接到群集。 在数据库中运行以下命令以查看过去四个小时内是否存在任何失败引入。 在运行之前替换数据库名称。
    
```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

运行以下命令以查看过去四个小时内所有引入操作的状态。 在运行之前替换数据库名称。

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>清理资源

如果计划学习其他快速入门和教程，请保留创建的资源。 否则，在数据库中运行以下命令以清除 `StormEvents` 表。

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [编写查询](write-queries.md)
