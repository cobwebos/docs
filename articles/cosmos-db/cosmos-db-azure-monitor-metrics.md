---
title: 从 Azure Monitor 获取 Azure Cosmos DB 指标
description: ''
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.openlocfilehash: 762c910336fa2b50a46eda23cf66d8a7aa383c52
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241222"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>从 Azure Monitor 监视和调试 Azure Cosmos DB 指标

可以从 Azure Monitor API 查看 Azure Cosmos DB 指标。 Azure Monitor 提供多种方式来与指标交互，包括 Azure 门户、通过 REST API 访问指标，或者使用 PowerShell 或 CLI 查询指标。 Azure Cosmos DB 指标是低延迟的数字值，默认每隔一分钟收集一次。你也可以聚合这些指标。 这些指标能够支持实时方案。  

本文介绍可以使用 Azure 门户从 Azure Monitor 查看的不同 Azure Cosmos DB 指标。 若要了解常见用例以及如何重复使用 Azure Cosmos DB 指标分析和调试问题，请参阅[在 Azure Cosmos DB 中使用指标进行监视和调试](use-metrics.md)一文。 你将使用某个现有的 Azure Cosmos 帐户，在数据库、容器、区域、请求或操作级别查看不同的指标。 因此，请务必准备好一个包含示例数据的 Azure Cosmos 帐户，并对这些数据执行 CRUD 操作。

## <a name="view-metrics-from-azure-portal"></a>从 Azure 门户查看指标

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 在左侧导航栏中选择“监视”，然后选择“指标”。

   ![Azure Monitor 中的“指标”窗格](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. 在“指标”窗格中选择一个资源，然后选择所需的订阅和资源组。 对于“资源类型”，请选择“Azure Cosmos DB 帐户”，选择一个现有的 Azure Cosmos 帐户，然后选择“应用”。 

   ![选择 Cosmos DB 帐户以查看指标](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. 接下来，可以从可用指标列表中选择一个指标。 可以选择特定于请求单位、存储、延迟、可用性、Cassandra 等的指标。 若要详细了解此列表中的所有可用指标，请参阅本文的[按类别划分的指标](#metrics-by-category)部分。 在此示例中，我们选择 "**请求单位**" 和 " **Avg** " 作为聚合值。 

   除这些详细信息外，还可以选择指标的“时间范围”和“时间粒度”。 可以查看过去最长 30 天的指标。  应用筛选器后，系统会根据该筛选器显示图表。 可以查看所选时间段内每分钟消耗的平均请求单位数。  

   ![从 Azure 门户中选择指标](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>向指标添加筛选器

还可以按 **CollectionName**、**DatabaseName**、**OperationType**、**Region** 和 **StatusCode** 筛选指标及显示的图表。 若要筛选指标，请选择“添加筛选器”，选择所需的属性（例如 **OperationType**），然后选择一个值（例如 **Query**）。 然后，图表中会显示查询操作在所选时间段内消耗的请求单位数。 不会记录通过存储过程执行的操作，因此 OperationType 指标下不会显示这些操作。

![添加筛选器以选择指标粒度](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

可以使用“应用拆分”选项将指标分组。 例如，可按操作类型将请求单位分组，并一次性查看所有操作的图表，如下图所示： 

![添加“应用拆分”筛选器](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>按类别分类的指标

### <a name="request-metrics"></a>请求指标
            
|Metric（指标显示名称）|Unit（聚合类型） |描述|维度| 时间粒度| 旧指标映射 | 用法 |
|---|---|---|---| ---| ---| ---|
| TotalRequests（请求总数） | Count（计数） | 已发出的请求数| DatabaseName, CollectionName, Region, StatusCode| 全部 | TotalRequests、Http 2xx、Http 3xx、Http 400、Http 401、内部服务器错误、服务不可用、受限制的请求数、每秒平均请求数 | 用于监视每个状态代码的请求数，以分钟为单位的容器。 若要获取每秒的平均请求数，请在分钟级别使用“计数”聚合并除以 60。 |
| MetadataRequests（元数据请求数） |Count（计数） | 元数据请求的计数。 Azure Cosmos DB 维护每个帐户的系统元数据容器，这使你可以免费枚举集合、数据库等。 | DatabaseName, CollectionName, Region, StatusCode| 全部| |用于监视由于元数据请求而导致的限制。|
| MongoRequests（Mongo 请求数） | Count（计数） | 已发出的 Mongo 请求数 | DatabaseName, CollectionName, Region, CommandName, ErrorCode| 全部 |Mongo 查询请求速率、Mongo 更新请求速率、Mongo 删除请求速率、Mongo 插入请求速率、Mongo 计数请求速率| 用于监视 Mongo 请求错误以及每个命令类型的使用情况。 |

### <a name="request-unit-metrics"></a>请求单位指标

|Metric（指标显示名称）|Unit（聚合类型）|描述|维度| 时间粒度| 旧指标映射 | 用法 |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge（Mongo 请求费用） | Count（总数） |Mongo 已消耗的请求单位| DatabaseName, CollectionName, Region, CommandName, ErrorCode| 全部 |Mongo 查询请求费用、Mongo 更新请求费用、Mongo 删除请求费用、Mongo 插入请求费用、Mongo 计数请求费用| 用于监视一分钟内的 Mongo 资源 RU。|
| TotalRequestUnits（请求单位总数）| Count（总数） | 已消耗的请求单位| DatabaseName, CollectionName, Region, StatusCode |全部| TotalRequestUnits| 用于在分钟粒度监视总的 RU 使用量。 若要获取每秒平均使用的 RU，请在分钟级别使用“总计”聚合并除以 60。|
| ProvisionedThroughput（预配的吞吐量）| Count（最大值） |容器粒度预配的吞吐量| DatabaseName、容器名称| 5M| | 用于监视每个容器的预配吞吐量。|

### <a name="storage-metrics"></a>存储度量值

|Metric（指标显示名称）|Unit（聚合类型）|描述|维度| 时间粒度| 旧指标映射 | 用法 |
|---|---|---|---| ---| ---| ---|
| AvailableStorage（可用存储空间） |Bytes（总数） | 每个区域按 5 分钟粒度报告的可用存储总量| DatabaseName、CollectionName、Region| 5M| 可用存储| 用于监视可用存储容量（仅适用于固定存储集合）。最小粒度应当为 5 分钟。| 
| DataUsage（数据用量） |Bytes（总数） |每个区域按 5 分钟粒度报告的数据总用量| DatabaseName、CollectionName、Region| 5M |数据大小 | 用于监视容器和区域的总数据使用量，最小粒度应为5分钟。|
| IndexUsage（索引用量） | Bytes（总数） |每个区域按 5 分钟粒度报告的索引总用量| DatabaseName、CollectionName、Region| 5M| 索引大小| 用于监视容器和区域的总数据使用量，最小粒度应为5分钟。 |
| DocumentQuota（文档配额） | Bytes（总数） | 每个区域按 5 分钟粒度报告的存储配额总量。| DatabaseName、CollectionName、Region| 5M |存储容量| 用于监视容器和区域的总配额，最小粒度应为5分钟。|
| DocumentCount（文档计数） | Count（总数） |每个区域按 5 分钟粒度报告的文档总数| DatabaseName、CollectionName、Region| 5M |文档计数|用于监视容器和区域中的文档计数，最小粒度应为5分钟。|

### <a name="latency-metrics"></a>延迟指标

|Metric（指标显示名称）|Unit（聚合类型）|描述|维度| 时间粒度| 用法 |
|---|---|---|---| ---| ---|
| ReplicationLatency（复制延迟）| MilliSeconds（最小值、最大值、平均值） | 启用了异地复制的帐户的源和目标区域之间的 P99 复制延迟| SourceRegion、TargetRegion| 全部 | 用于监视异地复制帐户在任何两个区域之间的 P99 复制延迟。 |


### <a name="availability-metrics"></a>可用性指标

|Metric（指标显示名称） |Unit（聚合类型）|描述| 时间粒度| 旧指标映射 | 用法 |
|---|---|---|---| ---| ---|
| ServiceAvailability（服务可用性）| Percent（最小值、最大值） | 在一小时粒度内的帐户请求可用性| 1 小时 | 服务可用性 | 表示传递的请求总数百分比。 如果状态代码为 410、500 或 503，则会认为请求因系统错误而失败。用于按小时粒度监视帐户的可用性。 |


### <a name="cassandra-api-metrics"></a>Cassandra API 指标

|Metric（指标显示名称）|Unit（聚合类型）|描述|维度| 时间粒度| 用法 |
|---|---|---|---| ---| ---|
| CassandraRequests（Cassandra 请求数） | Count（计数） | 发出的 Cassandra API 请求数| DatabaseName、CollectionName、ErrorCode、Region、OperationType、ResourceType| 全部| 用于按分钟粒度监视 Cassandra 请求。 若要获取每秒的平均请求数，请在分钟级别使用“计数”聚合并除以 60。|
| CassandraRequestCharges（Cassandra 请求费用） | Count（合计、最小值、最大值、平均值） | Cassandra API 请求已消耗的请求单位数| DatabaseName、CollectionName、Region、OperationType、ResourceType| 全部| 用于监视 Cassandra API 帐户每分钟使用的 RU。|
| CassandraConnectionClosures（Cassandra 连接关闭次数） |Count（计数） |关闭的 Cassandra 连接数| ClosureReason、Region| 全部 | 用于监视客户端与 Azure Cosmos DB Cassandra API 之间的连接。|

## <a name="next-steps"></a>后续步骤

* [从 Azure Cosmos DB 帐户指标窗格查看和监视指标](use-metrics.md)

* [Azure Cosmos DB 中的诊断日志记录](logging.md)
