---
title: Azure Cosmos DB monitoring data reference | Microsoft Docs
description: Log and metrics reference for monitoring data from Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: 0f61057daf994fde284e9484e41c01ed2e8ae78c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74220214"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Azure Cosmos DB monitoring data reference
This article provides a reference of log and metric data collected to analyze the performance and availability of Azure Cosmos DB. See [Monitoring Cosmos DB](monitor-cosmos-db.md) for details on collecting and analyzing monitoring data for Azure Cosmos DB.


## <a name="resource-logs"></a>资源日志
The following table lists the properties for Azure Cosmos DB resource logs when they're collected in Azure Monitor Logs or Azure Storage. In Azure Monitor Logs, they're collected in the **AzureDiagnostics** table with a **ResourceProvider** value of *MICROSOFT.DOCUMENTDB*. 

| Azure 存储字段或属性 | Azure Monitor Logs property | 描述 |
| --- | --- | --- |
| **time** | **TimeGenerated** | 操作发生时的日期和时间 (UTC)。 |
| **resourceId** | **资源** | 为其启用日志的 Azure Cosmos DB 帐户。|
| **category** | **类别** | 对于 Azure Cosmos DB 日志，**DataPlaneRequests** 是唯一可用的值。 |
| **operationName** | **OperationName** | 操作的名称。 此值可以是以下任意操作：创建、更新、读取、ReadFeed、删除、替换、执行、SqlQuery、查询、JSQuery、Head、HeadFeed 或 Upsert。   |
| **properties** | 不适用 | 下面的行中描述了此字段的内容。 |
| **activityId** | **activityId_g** | 日志记录操作的唯一 GUID。 |
| **userAgent** | **userAgent_s** | 一个字符串，指定执行请求的客户端用户代理。 格式为 {用户代理名}/{版本}。|
| **requestResourceType** | **requestResourceType_s** | 所访问资源的类型。 此值可以是以下任意资源类型：数据库、容器、文档、附件、用户、权限、StoredProcedure、触发器、UserDefinedFunction 或产品/服务。 |
| **statusCode** | **statusCode_s** | 操作的响应状态。 |
| **requestResourceId** | **ResourceId** | 与请求相关的 resourceId。 值可以指向 databaseRid、collectionRid 或 documentRid（具体取决于所执行的操作）。|
| **clientIpAddress** | **clientIpAddress_s** | 客户端的 IP 地址。 |
| **requestCharge** | **requestCharge_s** | 操作使用的 RU 数目 |
| **collectionRid** | **collectionId_s** | 集合的唯一 ID。|
| **duration** | **duration_s** | The duration of the operation, in milliseconds. |
| **requestLength** | **requestLength_s** | 请求的长度（按字节计）。 |
| **responseLength** | **responseLength_s** | 响应的长度（按字节计）。|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | 将[资源令牌](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens)用于身份验证时，此值非空。 值指向用户的资源 ID。 |

For a list of all Azure Monitor log categories and links to associated schemas, see [Azure Monitor Logs categories and schemas](../azure-monitor/platform/diagnostic-logs-schema.md). 

## <a name="metrics"></a>指标
The following tables list the platform metrics collected for Azure CosmOS DB. All metrics are stored in the namespace **Cosmos DB standard metrics**.

For a list of all Azure Monitor support metrics (including CosmosDB), see [Azure Monitor supported metrics](../azure-monitor/platform/metrics-supported.md). 

#### <a name="request-metrics"></a>请求指标
            
|Metric (Metric Display Name)|Unit (Aggregation Type) |描述|维度| 时间粒度| 旧指标映射 | 使用情况 |
|---|---|---|---| ---| ---| ---|
| TotalRequests (Total Requests) | Count (Count) | 已发出的请求数| DatabaseName, CollectionName, Region, StatusCode| 所有 | TotalRequests、Http 2xx、Http 3xx、Http 400、Http 401、内部服务器错误、服务不可用、受限制的请求数、每秒平均请求数 | Used to monitor requests per status code, container at a minute granularity. 若要获取每秒的平均请求数，请在分钟级别使用“计数”聚合并除以 60。 |
| MetadataRequests (Metadata Requests) |Count (Count) | 元数据请求的计数。 Azure Cosmos DB maintains system metadata container for each account, that allows you to enumerate collections, databases, etc., and their configurations, free of charge. | DatabaseName, CollectionName, Region, StatusCode| 所有| |用于监视由于元数据请求而导致的限制。|
| MongoRequests (Mongo Requests) | Count (Count) | 已发出的 Mongo 请求数 | DatabaseName, CollectionName, Region, CommandName, ErrorCode| 所有 |Mongo 查询请求速率、Mongo 更新请求速率、Mongo 删除请求速率、Mongo 插入请求速率、Mongo 计数请求速率| 用于监视 Mongo 请求错误以及每个命令类型的使用情况。 |

#### <a name="request-unit-metrics"></a>请求单位指标

|Metric (Metric Display Name)|Unit (Aggregation Type)|描述|维度| 时间粒度| 旧指标映射 | 使用情况 |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo Request Charge) | Count (Total) |Mongo 已消耗的请求单位| DatabaseName, CollectionName, Region, CommandName, ErrorCode| 所有 |Mongo 查询请求费用、Mongo 更新请求费用、Mongo 删除请求费用、Mongo 插入请求费用、Mongo 计数请求费用| 用于监视一分钟内的 Mongo 资源 RU。|
| TotalRequestUnits (Total Request Units)| Count (Total) | 已消耗的请求单位| DatabaseName, CollectionName, Region, StatusCode |所有| TotalRequestUnits| 用于在分钟粒度监视总的 RU 使用量。 若要获取每秒平均使用的 RU，请在分钟级别使用“总计”聚合并除以 60。|
| ProvisionedThroughput (Provisioned Throughput)| Count (Maximum) |Provisioned throughput at container granularity| DatabaseName, ContainerName| 5M| | Used to monitor provisioned throughput per container.|

#### <a name="storage-metrics"></a>存储度量值

|Metric (Metric Display Name)|Unit (Aggregation Type)|描述|维度| 时间粒度| 旧指标映射 | 使用情况 |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (Available Storage) |Bytes (Total) | Total available storage reported at 5-minutes granularity per region| DatabaseName、CollectionName、Region| 5M| 可用存储| 用于监视可用存储容量（仅适用于固定存储集合）。最小粒度应当为 5 分钟。| 
| DataUsage (Data Usage) |Bytes (Total) |Total data usage reported at 5-minutes granularity per region| DatabaseName、CollectionName、Region| 5M |数据大小 | Used to monitor total data usage at container and region, minimum granularity should be 5 minutes.|
| IndexUsage (Index Usage) | Bytes (Total) |Total Index usage reported at 5-minutes granularity per region| DatabaseName、CollectionName、Region| 5M| 索引大小| Used to monitor total data usage at container and region, minimum granularity should be 5 minutes. |
| DocumentQuota (Document Quota) | Bytes (Total) | Total storage quota reported at 5-minutes granularity per region.| DatabaseName、CollectionName、Region| 5M |存储容量| Used to monitor total quota at container and region, minimum granularity should be 5 minutes.|
| DocumentCount (Document Count) | Count (Total) |Total document count reported at 5-minutes granularity per region| DatabaseName、CollectionName、Region| 5M |文档计数|Used to monitor document count at container and region, minimum granularity should be 5 minutes.|

#### <a name="latency-metrics"></a>延迟指标

|Metric (Metric Display Name)|Unit (Aggregation Type)|描述|维度| 时间粒度| 使用情况 |
|---|---|---|---| ---| ---|
| ReplicationLatency (Replication Latency)| MilliSeconds (Minimum, Maximum, Average) | 启用了异地复制的帐户的源和目标区域之间的 P99 复制延迟| SourceRegion、TargetRegion| 所有 | 用于监视异地复制帐户在任何两个区域之间的 P99 复制延迟。 |


#### <a name="availability-metrics"></a>可用性指标

|Metric (Metric Display Name) |Unit (Aggregation Type)|描述| 时间粒度| 旧指标映射 | 使用情况 |
|---|---|---|---| ---| ---|
| ServiceAvailability (Service Availability)| Percent (Minimum, Maximum) | 在一小时粒度内的帐户请求可用性| 1 小时 | 服务可用性 | Represents the percent of total passed requests. 如果状态代码为 410、500 或 503，则会认为请求因系统错误而失败。用于按小时粒度监视帐户的可用性。 |


#### <a name="cassandra-api-metrics"></a>Cassandra API 指标

|Metric (Metric Display Name)|Unit (Aggregation Type)|描述|维度| 时间粒度| 使用情况 |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra Requests) | Count (Count) | 发出的 Cassandra API 请求数| DatabaseName、CollectionName、ErrorCode、Region、OperationType、ResourceType| 所有| 用于按分钟粒度监视 Cassandra 请求。 若要获取每秒的平均请求数，请在分钟级别使用“计数”聚合并除以 60。|
| CassandraRequestCharges (Cassandra Request Charges) | Count (Sum, Min, Max, Avg) | Cassandra API 请求已消耗的请求单位数| DatabaseName、CollectionName、Region、OperationType、ResourceType| 所有| 用于监视 Cassandra API 帐户每分钟使用的 RU。|
| CassandraConnectionClosures (Cassandra Connection Closures) |Count (Count) |关闭的 Cassandra 连接数| ClosureReason、Region| 所有 | 用于监视客户端与 Azure Cosmos DB Cassandra API 之间的连接。|

## <a name="see-also"></a>另请参阅

- See [Monitoring Azure Cosmos DB](monitor-cosmos-db.md) for a description of monitoring Azure Cosmos DB.
- See [Monitoring Azure resources with Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) for details on monitoring Azure resources.
