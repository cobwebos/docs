---
title: 使用 Azure 诊断设置监视 Azure Cosmos DB 数据
description: 了解如何使用 Azure 诊断设置来监视 Azure Cosmos DB 中存储的数据的性能和可用性
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: sngun
ms.openlocfilehash: b1a507c54c6a6555fc945dd35ee6e54d37d49bfd
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857572"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>使用 Azure 中的诊断设置监视 Azure Cosmos DB 数据

Azure 中的诊断设置用于收集资源日志。 Azure 资源日志由资源发出，提供与该资源的操作相关的各种频繁生成的数据。 这些日志是按请求捕获的，也称为“数据平面日志”。 部分数据平面操作的示例包括 delete、insert 和 readFeed。 这些日志的内容因资源类型而异。

平台指标和活动日志是自动收集的；必须创建一个诊断设置才能收集资源日志，或在 Azure Monitor 外部转发这些日志。 可使用以下步骤为 Azure Cosmos 帐户启用诊断设置：

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 导航到 Azure Cosmos 帐户。 打开“诊断设置”窗格，然后选择“添加诊断设置”选项。  

1. 在“诊断设置”窗格的表单中填充以下详细信息：  

    * **名称**：为要创建的日志输入名称。

    * 可以存储日志以执行“存档到存储帐户”、“流式传输到事件中心”或“发送到 Log Analytics”的操作   

1. 创建诊断设置时，请指定要收集的日志类别。 下面列出了 Azure Cosmos DB 支持的日志类别，以及收集的示例日志：

 * **DataPlaneRequests**：选择此选项可在 Azure Cosmos DB 中将后端请求记录到所有 API，其中包括 SQL、图形、MongoDB、Cassandra 和表 API 帐户。 要记录的关键属性：`Requestcharge`、`statusCode`、`clientIPaddress` 和 `partitionID`。

    ```json
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**：选择此选项可记录用户从前端发起的请求，这些请求的内容是要求处理发送给 Azure Cosmos DB 的 MongoDB API 的请求。 此日志类型不适用于其他 API 帐户。 要记录的关键属性：`Requestcharge`、`opCode`。 在诊断日志中启用 MongoRequests 时，请务必禁用 DataPlaneRequests。 对于在 API 上发出的每个请求，都会看到一个日志。

    ```json
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* CassandraRequests  ：选择此选项可记录用户从前端发起的请求，这些请求的内容是要求处理发送给 Azure Cosmos DB API for Cassandra 的请求。 此日志类型不适用于其他 API 帐户。 要记录的关键属性为 `operationName`、`requestCharge`、`piiCommandText`。 在诊断日志中启用 CassandraRequests 时，请务必禁用 DataPlaneRequests。 对于在 API 上发出的每个请求，都会看到一个日志。

   ```json
   { "time": "2020-03-30T23:55:10.9579593Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "CassandraRequests", "operationName": "QuerySelect", "properties": {"activityId": "6b33771c-baec-408a-b305-3127c17465b6","opCode": "<empty>","errorCode": "-1","duration": "0.311900","requestCharge": "1.589237","databaseName": "system","collectionName": "local","retryCount": "<empty>","authorizationTokenType": "PrimaryMasterKey","address": "104.42.195.92","piiCommandText": "{"request":"SELECT key from system.local"}","userAgent": """"}}
   ```

* **QueryRuntimeStatistics**：选择此选项以记录已执行的查询文本。 此日志类型仅适用于 SQL API 帐户。

    ```json
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**：选择此选项可记录分区键的统计信息。 目前，此信息以分区键的存储大小 (KB) 来表示。 请参阅本文的[使用 Azure 诊断查询排查问题](#diagnostic-queries)部分。 例如，使用“PartitionKeyStatistics”的查询。 日志针对占用大部分数据存储空间的前三个分区键发出。 此日志包含订阅 ID、区域名称、数据库名称、集合名称、分区键和存储大小 (KB) 等数据。

    ```json
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**：此日志报告分区键的每秒 RU 消耗量聚合值。 目前，Azure Cosmos DB 仅报告 SQL API 帐户的分区键，以及时点读取/写入和存储过程操作。 不支持其他 API 和操作类型。 对于其他 API，诊断日志表中的分区键列是空的。 此日志包含订阅 ID、区域名称、数据库名称、集合名称、分区键、操作类型和请求开销等数据。 请参阅本文的[使用 Azure 诊断查询排查问题](#diagnostic-queries)部分。 例如，使用“PartitionKeyRUConsumption”的查询。 

* **ControlPlaneRequests**：此日志包含有关控制平面操作的详细信息，例如创建帐户、添加或删除区域、更新帐户复制设置，等等。此日志类型适用于所有 API 类型，包含 SQL (Core)、MongoDB、Gremlin、Cassandra 和表 API。

* **请求**：选择此选项可将 Azure Cosmos DB 中的指标数据收集到诊断设置中的目标。 这是在 Azure 指标中自动收集的相同数据。 同时收集指标数据和资源日志可将这两种类型的数据一起分析，并在 Azure Monitor 外部发送指标数据。

有关如何使用 Azure 门户、CLI 或 PowerShell 创建诊断设置的详细信息，请参阅[创建诊断设置以在 Azure 中收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)一文。


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a>诊断查询问题疑难解答

1. 如何查询运行时间超过3毫秒的操作：

   ```Kusto
   AzureDiagnostics 
   | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by clientIpAddress_s, TimeGenerated
   ```

1. 如何查询正在运行操作的用户代理：

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by OperationName, userAgent_s
   ```

1. 如何查询长时间运行的操作：

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | project TimeGenerated , duration_s 
   | summarize count() by bin(TimeGenerated, 5s)
   | render timechart
   ```
    
1. 如何获取分区键统计信息以评估数据库帐户的前3个分区的偏差：

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
   | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
   ```

1. 如何获取高开销查询的请求开销？

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. 如何确定哪些操作消耗的每秒 RU 数最多？

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```

1. 如何获取与**DataPlaneRequests**和**QueryRunTimeStatistics**中的数据联接的、使用超过 100 RU/s 的所有查询。

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. 如何获取不同操作的分布情况？

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. 分区已使用的最大吞吐量是多少？

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. 如何获取分区键的每秒 RU 消耗量信息？

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. 如何获取特定分区键的请求开销

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. 如何获取在特定时间段每秒消耗了最多 RU 的分区键？ 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. 如何获取存储大小大于 8 GB 的分区键的日志？

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. 如何获取分区键统计信息，以评估数据库帐户的最大三个分区之间的偏差？

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
   | project SubscriptionId, regionName_s, databaseName_s, collectionName_s, partitionKey_s, sizeKb_d, ResourceId
   ```

1. 如何获取操作的 P99 或 P50 复制延迟，请求费用或响应的长度？

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2d)
   | summarize
   percentile(todouble(responseLength_s), 50), percentile(todouble(responseLength_s), 99), max(responseLength_s),
   percentile(todouble(requestCharge_s), 50), percentile(todouble(requestCharge_s), 99), max(requestCharge_s),
   percentile(todouble(duration_s), 50), percentile(todouble(duration_s), 99), max(duration_s),
   count()
   by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
 
1. 如何获取 Controlplane 日志？
 
   请记得按照[禁用基于密钥的元数据写入访问权限](audit-control-plane-logs.md#disable-key-based-metadata-write-access)articleand 中所述打开标志，并通过 AZURE POWERSHELL、CLI 或 ARM 执行操作。
 
   ```Kusto  
   AzureDiagnostics 
   | where Category =="ControlPlaneRequests"
   | summarize by OperationName 
   ```


## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 的 Azure Monitor](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [使用 Azure Cosmos DB 中的指标进行监视和调试](use-metrics.md)
