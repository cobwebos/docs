---
title: 快速入门：使用 Azure 数据资源管理器 Python 库引入数据
description: 在本快速入门中，你将了解如何使用 Python 在 Azure 数据资源管理器中引入（加载）数据。
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 10/16/2018
ms.openlocfilehash: fe405416e166b69ebe6b72e519ace4930c3062d8
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871713"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-python-library"></a>快速入门：使用 Azure 数据资源管理器 Python 库引入数据

Azure 数据资源管理器是一项快速且高度可缩放的数据浏览服务，适用于日志和遥测数据。 Azure 数据资源管理器为 Python 提供了两个客户端库：[引入库](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest)和[数据库](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data)。 可以使用这些库在群集中引入（加载）数据并从代码中查询数据。 在本快速入门，首先在测试群集中创建一个表和数据映射。 然后将引入排列到群集并验证结果。

本快速入门同时也可用作 [Azure Notebook](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueuedIngestSingleBlob.ipynb)。

如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

除 Azure 订阅外，还需以下条件才能完成此快速入门：

* [测试群集和数据库](create-cluster-database-portal.md)

* 已在开发计算机上安装了 [Python](https://www.python.org/downloads/)

## <a name="install-the-data-and-ingest-libraries"></a>安装数据和引入库

安装 azure-kusto-data 和 azure-kusto-ingest。

```
pip install azure-kusto-data
pip install azure-kusto-ingest
```

## <a name="add-import-statements-and-constants"></a>添加 import 语句和常量

从库中导入类以及 datetime 和 pandas，即数据分析库。

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
import pandas as pd
import datetime
```

要对应用程序进行身份验证，Azure 数据资源管理器使用 AAD 租户 ID。 要查找租户 ID，请使用以下 URL，并将域替换为 YourDomain。

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

例如，如果域名为 contoso.com，则该 URL 将是：[https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/)。 单击此 URL 以查看结果；第一行如下所示。 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

在这种情况下，租户 ID 为 `6babcaad-604b-40ac-a9d7-9fd97c0b779f`。 在运行此代码之前，请为 AAD_TENANT_ID、KUSTO_URI、KUSTO_INGEST_URI 和 KUSTO_DATABASE 设置值。

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_DATABASE  = "<DatabaseName>"
```

现在构造连接字符串。 此示例使用设备身份验证来访问群集。 此外可以使用 AAD 应用程序证书、AAD 应用程序密钥以及 AAD 用户和密码。

在后续步骤中创建目标表和映射。

```python
KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_INGEST_URI, AAD_TENANT_ID)

KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)

DESTINATION_TABLE = "StormEvents"
DESTINATION_TABLE_COLUMN_MAPPING = "StormEvents_CSV_Mapping"
```

## <a name="set-source-file-information"></a>设置源文件信息

导入其他类并设置数据源文件的常数。 此示例使用 Azure Blob 存储上托管的示例文件。 StormEvents 示例数据集包含[美国国家环境信息中心](https://www.ncdc.noaa.gov/stormevents/)中与天气相关的数据。

```python
from azure.storage.blob import BlockBlobService
from azure.kusto.ingest import KustoIngestClient, IngestionProperties, FileDescriptor, BlobDescriptor, DataFormat, ReportLevel, ReportMethod

CONTAINER = "samplefiles"
ACCOUNT_NAME = "kustosamplefiles"
SAS_TOKEN = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D"
FILE_PATH = "StormEvents.csv"
FILE_SIZE = 64158321    # in bytes

BLOB_PATH = "https://" + ACCOUNT_NAME + ".blob.core.windows.net/" + CONTAINER + "/" + FILE_PATH + SAS_TOKEN
```

## <a name="create-a-table-on-your-test-cluster"></a>在测试群集上创建表

创建与 StormEvents.csv 文件中的数据架构匹配的表。 此代码运行时，它将返回如下消息：要登录，请使用 Web 浏览器打开页面 https://microsoft.com/devicelogin，并输入代码 F3W4VWZDM 进行身份验证。 按照步骤登录，然后返回运行下一个代码块。 建立连接的后续代码块将要求你再次登录。

```python
KUSTO_CLIENT = KustoClient(KCSB_DATA)
CREATE_TABLE_COMMAND = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)"

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_TABLE_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="define-ingestion-mapping"></a>定义引入映射

将传入的 CSV 数据映射到创建表时使用的列名称和数据类型。

```python
CREATE_MAPPING_COMMAND = """.create table StormEvents ingestion csv mapping 'StormEvents_CSV_Mapping' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'"""

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_MAPPING_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="queue-a-message-for-ingestion"></a>列入一条引入消息

将一条消息排入队列，以便从 blob 存储中提取数据并将该数据引入到 Azure 数据资源管理器。

```python
INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)

# All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties
INGESTION_PROPERTIES  = IngestionProperties(database=KUSTO_DATABASE, table=DESTINATION_TABLE, dataFormat=DataFormat.csv, mappingReference=DESTINATION_TABLE_COLUMN_MAPPING, additionalProperties={'ignoreFirstRecord': 'true'})
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)  # 10 is the raw size of the data in bytes
INGESTION_CLIENT.ingest_from_blob(BLOB_DESCRIPTOR,ingestion_properties=INGESTION_PROPERTIES)

print('Done queuing up ingestion with Azure Data Explorer')

```

## <a name="validate-that-data-was-ingested-into-the-table"></a>验证数据已引入到表

等待五到十分钟，直到排入队列的引入已计划在 Azure 数据资源管理器中引入和加载数据。 然后运行以下代码，以获取 StormEvents 表中记录的计数。

```python
QUERY = "StormEvents | count"

RESPONSE = KUSTO_CLIENT.execute_query(KUSTO_DATABASE, QUERY)

dataframe_from_result_table(RESPONSE.primary_results[0])
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

如果计划学习其他快速入门和教程，请保留创建的资源。 否则，在数据库中运行以下命令以清除 StormEvents 表。

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [编写查询](write-queries.md)
