---
title: 从 Azure Monitor 获取 Azure Cosmos DB 指标
description: ''
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 2eb61a6b9afa3cabf1733be120dfbdacb7de4534
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276790"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>监视和调试 Azure Cosmos DB 通过 Azure Monitor 的指标

可以查看从 Azure Monitor API 的 Azure Cosmos DB 指标。 Azure Monitor 提供多种方式来与指标交互，包括 Azure 门户、 通过 REST API 访问这些或查询它们使用 PowerShell 或 CLI。 Azure Cosmos DB 指标会延迟较低数字值，默认情况下收集一分钟的频率，还可以聚合这些度量值。 这些指标是能够支持实时方案。  

本指南介绍了不同 Azure Cosmos DB 中的指标可以查看使用 Azure 门户的 Azure Monitor。 如果想要在常见用例和如何使用 Azure Cosmos DB 指标重新分析和调试这些问题，请参阅[监视和调试 Azure Cosmos DB 中的指标](use-metrics.md)一文。 您将使用一个现有的 Azure Cosmos 帐户并查看不同的指标，数据库、 容器、 区域、 请求或操作级别。 因此，请确保已使用示例数据的 Azure Cosmos 帐户并对该数据执行 CRUD 操作。

## <a name="view-metrics-from-azure-portal"></a>从 Azure 门户查看指标

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择**监视器**从左侧导航栏中，然后选择**指标**。

   ![Azure Monitor 中的度量值窗格](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. 从**指标**窗格 >**选择一个资源**> 选择所需**订阅**，以及**资源组**。 有关**资源类型**，选择**Azure Cosmos DB 帐户**，选择一个现有的 Azure Cosmos 帐户，然后选择**应用**。 

   ![选择要查看的度量值的 Cosmos DB 帐户](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. 接下来您可以从可用指标的列表中选择某个度量值。 您可以选择特定于请求单位数、 存储、 延迟、 可用性、 Cassandra 和其他指标。 若要了解有关此列表中的所有可用度量值的详细信息，请参阅[指标按类别](#metrics-by-category)本文的部分。 在此示例中，选择**请求单位**并**Avg**作为聚合值。 

   除了这些详细信息，还可以选中**时间范围**并**时间粒度**的度量值。 最多，您可以查看度量值在过去的 30 天。  应用筛选器后，图表会显示根据筛选器。 您所见的平均每分钟所选时间段使用的请求单位数。  

   ![从 Azure 门户选择指标](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>将筛选器添加到度量值

你还可以筛选度量值和显示由特定的图表**CollectionName**， **DatabaseName**， **OperationType**，**区域**，并**StatusCode**。 若要筛选的度量值，请选择**添加筛选器**并选择所需的属性，例如**OperationType**并选择一个值，如**查询**。 图形然后显示所选时间段的查询操作的请求单位。 通过存储过程执行的操作不会记录，因此它们不能在 OperationType 指标。

![添加筛选器选择的指标的粒度](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

可以使用组指标**应用拆分**选项。 例如，可以每种操作类型的请求单位数进行分组，并查看在一次，在下图中所示的所有操作的关系图： 

![添加应用拆分的筛选器](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>按类别的度量值

### <a name="request-metrics"></a>请求指标
            
|度量值 （度量值的显示名称）|单元 （聚合类型） |描述|维度| 时间粒度| 旧指标映射 | 使用情况 |
|---|---|---|---| ---| ---| ---|
| TotalRequests （请求总数） | 计数 （计数） | 已发出的请求数| DatabaseName, CollectionName, Region, StatusCode| 全部 | TotalRequests、Http 2xx、Http 3xx、Http 400、Http 401、内部服务器错误、服务不可用、受限制的请求数、每秒平均请求数 | 用来按状态代码监视请求，以分钟为粒度进行收集。 若要获取每秒的平均请求数，请在分钟级别使用“计数”聚合并除以 60。 |
| MetadataRequests （元数据请求） |计数 （计数） | 元数据请求的计数。 Azure Cosmos DB 保留系统元数据集合的可枚举集合、 数据库等，每一个帐户及其配置，免费。 | DatabaseName, CollectionName, Region, StatusCode| 全部| |用于监视由于元数据请求而导致的限制。|
| MongoRequests （Mongo 请求数） | 计数 （计数） | 已发出的 Mongo 请求数 | DatabaseName, CollectionName, Region, CommandName, ErrorCode| 全部 |Mongo 查询请求速率、Mongo 更新请求速率、Mongo 删除请求速率、Mongo 插入请求速率、Mongo 计数请求速率| 用于监视 Mongo 请求错误以及每个命令类型的使用情况。 |

### <a name="request-unit-metrics"></a>请求单位指标

|度量值 （度量值的显示名称）|单元 （聚合类型）|描述|维度| 时间粒度| 旧指标映射 | 使用情况 |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge （Mongo 请求费用） | 计数 （总计） |Mongo 已消耗的请求单位| DatabaseName, CollectionName, Region, CommandName, ErrorCode| 全部 |Mongo 查询请求费用、Mongo 更新请求费用、Mongo 删除请求费用、Mongo 插入请求费用、Mongo 计数请求费用| 用于监视一分钟内的 Mongo 资源 RU。|
| TotalRequestUnits （总请求单位数）| 计数 （总计） | 已消耗的请求单位| DatabaseName, CollectionName, Region, StatusCode |全部| TotalRequestUnits| 用于在分钟粒度监视总的 RU 使用量。 若要获取每秒平均使用的 RU，请在分钟级别使用“总计”聚合并除以 60。|
| ProvisionedThroughput （预配的吞吐量）| 计数 （最大值） |在集合粒度预配的吞吐量| DatabaseName、CollectionName| 5M| | 用于监视每个集合的预配吞吐量。|

### <a name="storage-metrics"></a>存储度量值

|度量值 （度量值的显示名称）|单元 （聚合类型）|描述|维度| 时间粒度| 旧指标映射 | 使用情况 |
|---|---|---|---| ---| ---| ---|
| AvailableStorage （可用的存储） |字节数 （总数） | 每个区域的 5 分钟粒度上报告的可用总存储| DatabaseName、CollectionName、Region| 5M| 可用存储| 用于监视可用存储容量（仅适用于固定存储集合）。最小粒度应当为 5 分钟。| 
| DataUsage （数据使用情况） |字节数 （总数） |在每个区域的 5 分钟粒度的总数据使用情况报告| DatabaseName、CollectionName、Region| 5M |数据大小 | 用于在集合和区域级别监视总的数据使用情况，最小粒度应当为 5 分钟。|
| IndexUsage （索引使用情况） | 字节数 （总数） |在每个区域的 5 分钟粒度总索引使用情况报告| DatabaseName、CollectionName、Region| 5M| 索引大小| 用于在集合和区域级别监视总的数据使用情况，最小粒度应当为 5 分钟。 |
| DocumentQuota （文档配额） | 字节数 （总数） | 在每个区域的 5 分钟粒度报告总存储配额。| DatabaseName、CollectionName、Region| 5M |存储容量| 用于在集合和区域级别监视总的配额，最小粒度应当为 5 分钟。|
| DocumentCount （文档计数） | 计数 （总计） |每个区域的 5 分钟粒度上报告的总文档计数| DatabaseName、CollectionName、Region| 5M |文档计数|用于在集合和区域级别监视文档计数，最小粒度应当为 5 分钟。|

### <a name="latency-metrics"></a>延迟指标

|度量值 （度量值的显示名称）|单元 （聚合类型）|描述|维度| 时间粒度| 使用情况 |
|---|---|---|---| ---| ---|
| ReplicationLatency （复制滞后时间）| 毫秒 （最小值、 最大值、 平均值） | 启用了异地复制的帐户的源和目标区域之间的 P99 复制延迟| SourceRegion、TargetRegion| 全部 | 用于监视异地复制帐户在任何两个区域之间的 P99 复制延迟。 |


### <a name="availability-metrics"></a>可用性指标

|度量值 （度量值的显示名称） |单元 （聚合类型）|描述| 时间粒度| 旧指标映射 | 使用情况 |
|---|---|---|---| ---| ---|
| ServiceAvailability （服务可用性）| %（最小值、 最大值） | 在一小时粒度内的帐户请求可用性| 1 小时 | 服务可用性 | 表示传递请求的总数的百分比。 如果状态代码为 410、500 或 503，则会认为请求因系统错误而失败。用于按小时粒度监视帐户的可用性。 |


### <a name="cassandra-api-metrics"></a>Cassandra API 指标

|度量值 （度量值的显示名称）|单元 （聚合类型）|描述|维度| 时间粒度| 使用情况 |
|---|---|---|---| ---| ---|
| CassandraRequests （Cassandra 请求） | 计数 （计数） | 发出的 Cassandra API 请求数| DatabaseName、CollectionName、ErrorCode、Region、OperationType、ResourceType| 全部| 用于按分钟粒度监视 Cassandra 请求。 若要获取每秒的平均请求数，请在分钟级别使用“计数”聚合并除以 60。|
| CassandraRequestCharges （Cassandra 请求费用） | 计数 （Sum、 Min、 最大、 平均） | Cassandra API 请求已消耗的请求单位数| DatabaseName、CollectionName、Region、OperationType、ResourceType| 全部| 用于监视 Cassandra API 帐户每分钟使用的 RU。|
| CassandraConnectionClosures （Cassandra 连接闭包） |计数 （计数） |关闭的 Cassandra 连接数| ClosureReason、Region| 全部 | 用于监视客户端与 Azure Cosmos DB Cassandra API 之间的连接。|

## <a name="next-steps"></a>后续步骤

* [从 Azure Cosmos DB 帐户指标窗格中查看和监视指标](use-metrics.md)

* [Azure Cosmos DB 中的诊断日志记录](logging.md)
