---
title: 使用 Azure 诊断设置监视 Azure Cosmos DB 数据
description: 了解如何使用 Azure 诊断设置来监视存储在 Azure Cosmos DB 中的数据的性能和可用性
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
ms.openlocfilehash: 670797eb833b0a145a18e20c6bba711ca11609bc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483279"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>使用 Azure 中的诊断设置监视 Azure Cosmos DB 数据

Azure 中的诊断设置用于收集资源日志。 Azure 资源日志由资源发出，并提供有关该资源的操作的丰富、频繁的数据。 这些日志是根据请求捕获的，它们也称为 "数据平面日志"。 数据平面操作的一些示例包括 delete、insert 和 readFeed。 这些日志的内容因资源类型而异。

平台指标和活动日志会自动收集，而你必须创建诊断设置以收集资源日志，或将其转发到 Azure Monitor 之外。 可以使用以下步骤为 Azure Cosmos 帐户启用诊断设置：

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 导航到 Azure Cosmos 帐户。 打开 "**诊断设置**" 窗格，然后选择 "**添加诊断设置**" 选项。

1. 在 "**诊断设置**" 窗格中，填写包含以下详细信息的窗体： 

    * **名称**：为要创建的日志输入名称。

    * 您可以存储要存档到**存储帐户**、**流式传输到事件中心**或**发送到 Log Analytics**的日志。

1. 创建诊断设置时，可指定要收集的日志类别。 下面列出了 Azure Cosmos DB 支持的日志类别以及这些类别收集的示例日志：

 * **DataPlaneRequests**：选择此选项可将后端请求记录到包含 Azure Cosmos DB 中的 SQL、Graph、MongoDB、Cassandra 和表 API 帐户的所有 api。 要注意的关键属性是： `Requestcharge`、`statusCode`、`clientIPaddress`和 `partitionID`。

    ```
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**：选择此选项以记录前端的用户启动的请求，以便为 MongoDB 提供 Azure Cosmos DB api 的请求，此日志类型不可用于其他 API 帐户。 MongoDB 请求将显示在 MongoRequests 和 DataPlaneRequests 中。 要注意的关键属性为： `Requestcharge`，`opCode`。

    ```
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **QueryRuntimeStatistics**：选择此选项可记录已执行的查询文本。 此日志类型仅适用于 SQL API 帐户。

    ```
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**：选择此选项以记录分区键的统计信息。 当前用分区键的存储大小（KB）表示。 请参阅本文中的[使用 Azure 诊断查询解决问题](#diagnostic-queries)部分。 例如，使用 "PartitionKeyStatistics" 的查询。 日志针对占用大多数数据存储空间的前三个分区键发出。 此日志包含数据，如订阅 ID、区域名称、数据库名称、集合名称、分区键和存储大小（KB）。

    ```
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**：此日志报告分区键的聚合每秒 RU/秒消耗。 目前 Azure Cosmos DB 报告仅用于 SQL API 帐户的分区键，以及点读/写和存储过程操作。 不支持其他 Api 和操作类型。 对于其他 Api，诊断日志表中的分区键列将为空。 此日志包含一些数据，如订阅 ID、区域名称、数据库名称、集合名称、分区键、操作类型和请求费用。 请参阅本文中的[使用 Azure 诊断查询解决问题](#diagnostic-queries)部分。 例如，使用 "PartitionKeyRUConsumption" 的查询。 

* **ControlPlaneRequests**：此日志包含控制平面操作的详细信息，例如创建帐户、添加或删除区域、更新帐户复制设置等。此日志类型适用于包括 SQL （Core）、MongoDB、Gremlin、Cassandra、表 API 的所有 API 类型。

* **请求**：选择此选项可将指标数据从 Azure Cosmos DB 收集到诊断设置中的目标。 这是在 Azure 指标中自动收集的相同数据。 收集包含资源日志的指标数据，将这两种类型的数据组合在一起，并发送 Azure Monitor 之外的指标数据。

有关如何使用 Azure 门户、CLI 或 PowerShell 创建诊断设置的详细信息，请参阅[在 Azure 中创建用于收集平台日志和指标的诊断设置](../azure-monitor/platform/diagnostic-settings.md)一文。


## <a id="diagnostic-queries"></a>诊断查询问题疑难解答

1. 如何获取成本高昂的查询的请求费用？

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

1. 如何确定哪些操作采用的是 RU/s。

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
1. 如何实现不同操作的分发？

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. 分区提供的最大吞吐量是多少？

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. 如何获取每秒分区键每秒使用的分区键的相关信息？

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. 如何获取特定分区键的请求费用

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. 如何获取在特定时间段内使用的最大 RU/s 的顶部分区键？ 

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

1. 如何获取分区键统计信息以评估数据库帐户的前三个分区之间的偏差？

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 的 Azure Monitor](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [使用 Azure Cosmos DB 中的指标进行监视和调试](use-metrics.md)
