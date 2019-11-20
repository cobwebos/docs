---
title: Azure Cosmos DB 监视数据引用 |Microsoft Docs
description: 用于监视来自 Azure Cosmos DB 的数据的日志和指标参考。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 5f46c9bf53d791eaec68763edc32996847e78ebd
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186898"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Azure Cosmos DB 监视数据引用
本文提供了收集的日志和指标数据的参考，用于分析 Azure Cosmos DB 的性能和可用性。 有关收集和分析 Azure Cosmos DB 的监视数据的详细信息，请参阅[监视 Cosmos DB](monitor-cosmos-db.md) 。


## <a name="resource-logs"></a>资源日志
下表列出了在 Azure Monitor 日志或 Azure 存储中收集 Azure Cosmos DB 资源日志时这些资源的属性。 在 Azure Monitor 日志中，这些日志是使用**ResourceProvider**值 MICROSOFT 在**AzureDiagnostics**表中收集的 *。DOCUMENTDB*。

| Azure 存储字段或属性 | Azure Monitor 日志属性 | 说明 |
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
| **duration** | **duration_s** | 操作持续时间，以毫秒为单位。 |
| **requestLength** | **requestLength_s** | 请求的长度（按字节计）。 |
| **responseLength** | **responseLength_s** | 响应的长度（按字节计）。|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | 将[资源令牌](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens)用于身份验证时，此值非空。 值指向用户的资源 ID。 |

## <a name="metrics"></a>度量值
下表列出了为 Azure CosmOS DB 收集的平台指标。 所有指标都存储在命名空间**Cosmos DB 标准指标**中。

#### <a name="request-metrics"></a>请求指标
            
|Metric（指标显示名称）|Unit（聚合类型） |说明|维度| 时间粒度| 旧指标映射 | 使用情况 |
|---|---|---|---| ---| ---| ---|
| TotalRequests（请求总数） | Count（计数） | 已发出的请求数| DatabaseName, CollectionName, Region, StatusCode| All | TotalRequests、Http 2xx、Http 3xx、Http 400、Http 401、内部服务器错误、服务不可用、受限制的请求数、每秒平均请求数 | 用于按状态代码、容器监视请求，以分钟为粒度。 若要获取每秒的平均请求数，请在分钟级别使用“计数”聚合并除以 60。 |
| MetadataRequests（元数据请求数） |Count（计数） | 元数据请求的计数。 Azure Cosmos DB 为每个帐户维护系统元数据容器，允许你免费枚举集合、数据库及其配置等等。 | DatabaseName, CollectionName, Region, StatusCode| All| |用于监视由于元数据请求而导致的限制。|
| MongoRequests（Mongo 请求数） | Count（计数） | 已发出的 Mongo 请求数 | DatabaseName, CollectionName, Region, CommandName, ErrorCode| All |Mongo 查询请求速率、Mongo 更新请求速率、Mongo 删除请求速率、Mongo 插入请求速率、Mongo 计数请求速率| 用于监视 Mongo 请求错误以及每个命令类型的使用情况。 |

#### <a name="request-unit-metrics"></a>请求单位指标

|Metric（指标显示名称）|Unit（聚合类型）|说明|维度| 时间粒度| 旧指标映射 | 使用情况 |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge（Mongo 请求费用） | Count（总数） |Mongo 已消耗的请求单位| DatabaseName, CollectionName, Region, CommandName, ErrorCode| All |Mongo 查询请求费用、Mongo 更新请求费用、Mongo 删除请求费用、Mongo 插入请求费用、Mongo 计数请求费用| 用于监视一分钟内的 Mongo 资源 RU。|
| TotalRequestUnits（请求单位总数）| Count（总数） | 已消耗的请求单位| DatabaseName, CollectionName, Region, StatusCode |All| TotalRequestUnits| 用于在分钟粒度监视总的 RU 使用量。 若要获取每秒平均使用的 RU，请在分钟级别使用“总计”聚合并除以 60。|
| ProvisionedThroughput（预配的吞吐量）| Count（最大值） |以容器粒度预配的吞吐量| DatabaseName、ContainerName| 5M| | 用于监视每个容器的预配吞吐量。|

#### <a name="storage-metrics"></a>存储度量值

|Metric（指标显示名称）|Unit（聚合类型）|说明|维度| 时间粒度| 旧指标映射 | 使用情况 |
|---|---|---|---| ---| ---| ---|
| AvailableStorage（可用存储空间） |Bytes（总数） | 每个区域按 5 分钟粒度报告的可用存储总量| DatabaseName、CollectionName、Region| 5M| 可用存储| 用于监视可用存储容量（仅适用于固定存储集合）。最小粒度应当为 5 分钟。| 
| DataUsage（数据用量） |Bytes（总数） |每个区域按 5 分钟粒度报告的数据总用量| DatabaseName、CollectionName、Region| 5M |数据大小 | 用于在容器和区域级别监视总的数据使用情况，最小粒度应当为 5 分钟。|
| IndexUsage（索引用量） | Bytes（总数） |每个区域按 5 分钟粒度报告的索引总用量| DatabaseName、CollectionName、Region| 5M| 索引大小| 用于在容器和区域级别监视总的数据使用情况，最小粒度应当为 5 分钟。 |
| DocumentQuota（文档配额） | Bytes（总数） | 每个区域按 5 分钟粒度报告的存储配额总量。| DatabaseName、CollectionName、Region| 5M |存储容量| 用于在容器和区域级别监视总的配额，最小粒度应当为 5 分钟。|
| DocumentCount（文档计数） | Count（总数） |每个区域按 5 分钟粒度报告的文档总数| DatabaseName、CollectionName、Region| 5M |文档计数|用于在容器和区域级别监视文档计数，最小粒度应当为 5 分钟。|

#### <a name="latency-metrics"></a>延迟指标

|Metric（指标显示名称）|Unit（聚合类型）|说明|维度| 时间粒度| 使用情况 |
|---|---|---|---| ---| ---|
| ReplicationLatency（复制延迟）| MilliSeconds（最小值、最大值、平均值） | 启用了异地复制的帐户的源和目标区域之间的 P99 复制延迟| SourceRegion、TargetRegion| All | 用于监视异地复制帐户在任何两个区域之间的 P99 复制延迟。 |


#### <a name="availability-metrics"></a>可用性指标

|Metric（指标显示名称） |Unit（聚合类型）|说明| 时间粒度| 旧指标映射 | 使用情况 |
|---|---|---|---| ---| ---|
| ServiceAvailability（服务可用性）| Percent（最小值、最大值） | 在一小时粒度内的帐户请求可用性| 1 小时 | 服务可用性 | 表示传递的请求总数百分比。 如果状态代码为 410、500 或 503，则会认为请求因系统错误而失败。用于按小时粒度监视帐户的可用性。 |


#### <a name="cassandra-api-metrics"></a>Cassandra API 指标

|Metric（指标显示名称）|Unit（聚合类型）|说明|维度| 时间粒度| 使用情况 |
|---|---|---|---| ---| ---|
| CassandraRequests（Cassandra 请求数） | Count（计数） | 发出的 Cassandra API 请求数| DatabaseName、CollectionName、ErrorCode、Region、OperationType、ResourceType| All| 用于按分钟粒度监视 Cassandra 请求。 若要获取每秒的平均请求数，请在分钟级别使用“计数”聚合并除以 60。|
| CassandraRequestCharges（Cassandra 请求费用） | Count（合计、最小值、最大值、平均值） | Cassandra API 请求已消耗的请求单位数| DatabaseName、CollectionName、Region、OperationType、ResourceType| All| 用于监视 Cassandra API 帐户每分钟使用的 RU。|
| CassandraConnectionClosures（Cassandra 连接关闭次数） |Count（计数） |关闭的 Cassandra 连接数| ClosureReason、Region| All | 用于监视客户端与 Azure Cosmos DB Cassandra API 之间的连接。|

## <a name="see-also"></a>另请参阅

- 有关监视 Azure Cosmos DB 的说明，请参阅[监视 Azure Cosmos DB](monitor-cosmos-db.md) 。
- 有关监视 Azure 资源的详细信息，请参阅[监视 azure 资源的 Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) 。