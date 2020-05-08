---
title: Azure Cosmos DB 服务配额
description: Azure Cosmos DB 服务配额和不同资源类型的默认限制。
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: e4d578596471153e4fc0e37d3ca093685326ecc7
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791759"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure Cosmos DB 服务配额

本文概述了 Azure Cosmos DB 中为不同资源提供的默认配额。

## <a name="storage-and-throughput"></a>存储和吞吐量

在订阅下创建 Azure Cosmos 帐户后，可以通过[创建数据库、容器和项](databases-containers-items.md)来管理帐户中的数据。 可以在容器级别或数据库级别按[请求单位 (RU)](request-units.md) 预配吞吐量。 下表列出了每个容器/数据库的存储和吞吐量限制。

| 资源 | 默认限制 |
| --- | --- |
| 每个容器的最大 RU 数（[专用吞吐量预配模式](databases-containers-items.md#azure-cosmos-containers)） | 默认为 1,000,000。 可以通过[开具 Azure 支持票证](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)来提高此限制 |
| 每个数据库的最大 RU 数（[共享吞吐量预配模式](databases-containers-items.md#azure-cosmos-containers)） | 默认为 1,000,000。 可以通过[开具 Azure 支持票证](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)来提高此限制 |
| 每个（逻辑）分区键的最大 RU 数 | 10,000 |
| 每个（逻辑）分区键的所有项的最大存储| 20 GB |
| 最大相异（逻辑）分区键数目 | 无限制 |
| 每个容器的最大存储 | 无限制 |
| 每个数据库的最大存储 | 无限制 |
| 每个帐户的最大附件大小（附件功能即将弃用） | 2 GB |
| 每 1 GB 需要的最小 RU 数 | 10 RU/秒 |

> [!NOTE]
> 对于具有需要更高存储或吞吐量限制的分区键的工作负荷，若要了解其最佳管理实践，请参阅[创建合成分区键](synthetic-partition-keys.md)。
>

Cosmos 容器（或共享吞吐量数据库）的吞吐量必须至少为 400 RU。 随着容器的不断扩大，支持的最小吞吐量还取决于以下因素：

* 可对容器设置的最小吞吐量取决于对该容器预配的最大吞吐量。 例如，如果吞吐量已提高至 10000 RU，则最低可能的预配吞吐量将是 1000 RU
* 共享吞吐量数据库的最小吞吐量也取决于曾经在共享吞吐量数据库中创建的容器总数，以每个容器 100 个 RU 计量。 例如，如果你在共享吞吐量数据库中创建了 5 个容器，则吞吐量必须至少为 500 RU

可以从 Azure 门户或 SDK 检索容器或数据库的当前吞吐量和最低吞吐量。 有关详细信息，请参阅[对容器和数据库预配吞吐量](set-throughput.md)。 

> [!NOTE]
> 在某些情况下，可将吞吐量降到 10% 以下。 使用 API 获取每个容器的确切最小 RU 数。
>

下面汇总了最低预配 RU 限制。 

| 资源 | 默认限制 |
| --- | --- |
| 每个容器的最小 RU 数（[专用吞吐量预配模式](databases-containers-items.md#azure-cosmos-containers)） | 400 |
| 每个数据库的最小 RU 数（[共享吞吐量预配模式](databases-containers-items.md#azure-cosmos-containers)） | 400 |
| 共享吞吐量数据库中每个容器的最小 RU 数 | 100 |

Cosmos DB 支持通过 SDK 或门户弹性缩放每个容器或数据库的吞吐量 (RU)。 可以同步方式或立即缩放每个容器，缩放范围为最小值和最大值之间的 10 到 100 倍。 如果请求的吞吐量值超出范围，将以异步方式执行缩放。 完成异步缩放所需的时间为数分钟到数小时不等，具体取决于请求的吞吐量和容器中的数据存储大小。  

## <a name="control-plane-operations"></a>控制平面操作

可以使用 Azure 门户、Azure PowerShell、Azure CLI 和 Azure 资源管理器模板[预配和管理 Azure Cosmos 帐户](how-to-manage-database-account.md)。 下表列出了每个订阅、帐户和操作数目的限制。

| 资源 | 默认限制 |
| --- | --- |
| 每个订阅的最大数据库帐户数 | 默认为 50。 可以通过[开具 Azure 支持票证](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)来提高此限制|
| 最大区域故障转移次数 | 默认为每小时 1 次。 可以通过[开具 Azure 支持票证](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)来提高此限制|

> [!NOTE]
> 区域故障转移仅适用于单区域写入帐户。 多区域写入帐户不需要，也不会对更改写入区域施加任何限制。

Cosmos DB 会定期自动备份数据。 有关备份保留间隔和时限的详细信息，请参阅 [Azure Cosmos DB 中的联机备份和按需数据还原](online-backup-and-restore.md)。

## <a name="per-account-limits"></a>每个帐户的限制

| 资源 | 默认限制 |
| --- | --- |
| 数据库的最小数目 | 无限制 |
| 具有共享吞吐量的每个数据库的最大容器数 |25 |
| 具有专用吞吐量的每个数据库或帐户的最大容器数  |不受限制 |
| 最大区域数 | 无限制（所有 Azure 区域） |

## <a name="per-container-limits"></a>每个容器的限制

根据所用的 API，Azure Cosmos 容器可以代表集合、表或图形。 容器支持[唯一键约束](unique-keys.md)、[存储过程、触发器和 UDF](stored-procedures-triggers-udfs.md) 和[索引策略](how-to-manage-indexing-policy.md)的配置。 下表列出了特定于容器中的配置的限制。 

| 资源 | 默认限制 |
| --- | --- |
| 数据库或容器名称的最大长度 | 255 |
| 每个容器的最大存储过程数 | 100 <sup>*</sup>|
| 每个容器的最大 UDF 数 | 25 <sup>*</sup>|
| 索引策略中的最大路径数| 100 <sup>*</sup>|
| 每个容器的最大唯一键数|10 <sup>*</sup>|
| 每个唯一键约束的最大路径数|16 <sup>*</sup>|

<sup>*</sup> 可以联系 Azure 支持人员来提高上述每个容器的限制。

## <a name="per-item-limits"></a>每个项的限制

根据所用的 API，Azure Cosmos 项可以代表集合中的文档、表中的行，或者图形中的节点或边缘。 下表显示了 Cosmos DB 中每个项的限制。 

| 资源 | 默认限制 |
| --- | --- |
| 项的最大大小 | 2 MB（JSON 表示形式的 UTF-8 长度） |
| 分区键值的最大长度 | 2048 字节 |
| ID 值的最大长度 | 1023 字节 |
| 每个项的最大属性数 | 无实际限制 |
| 最大嵌套深度 | 无实际限制 |
| 属性名称的最大长度 | 无实际限制 |
| 属性值的最大长度 | 无实际限制 |
| 字符串属性值的最大长度 | 无实际限制 |
| 数字属性值的最大长度 | IEEE754 双精度 64 位 |

项有效负载（例如属性数和嵌套深度）没有限制，不过，分区键和 ID 值存在长度限制，且总大小限制为 2 MB。 对于采用大型或复杂项结构的容器，可能需要配置索引策略来降低 RU 消耗。 [为 Cosmos DB 中的项建模](how-to-model-partition-example.md)中提供了一个真实示例，以及用于管理大项的模式。

## <a name="per-request-limits"></a>每个请求的限制

Azure Cosmos DB 支持对容器、项和数据库等资源执行 [CRUD 和查询操作](https://docs.microsoft.com/rest/api/cosmos-db/)。 它还支持针对容器中具有相同分区键的多个项的[事务性批处理请求](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch)。

| 资源 | 默认限制 |
| --- | --- |
| 单个操作（例如存储过程执行或单个查询页检索）的最长执行时间| 5 秒 |
| 最大请求大小（例如，存储过程、CRUD）| 2 MB |
| 最大响应大小（例如分页查询） | 4 MB |
| 事务性批处理中的最大操作数 | 100 |

一旦查询等操作达到执行超时或响应大小限制，就会返回一个结果页面，并向客户端返回一个继续令牌用于恢复执行。 单个查询可以针对不同的页面/继续执行活动运行的持续时间没有实际的限制。

Cosmos DB 使用 HMAC 进行授权。 可以使用主密钥或[资源令牌](secure-access-to-data.md)对容器、分区键或项等资源进行精细的访问控制。 下表列出了 Cosmos DB 中授权令牌的限制。

| 资源 | 默认限制 |
| --- | --- |
| 主令牌最长过期时间 | 15 分钟  |
| 资源令牌最短过期时间 | 10 分钟  |
| 资源令牌最长过期时间 | 默认为 24 小时。 可以通过[开具 Azure 支持票证](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)来提高此限制|
| 令牌授权的最大时钟偏差| 15 分钟 |

Cosmos DB 支持在写入期间执行触发器。 对于每个写入操作，服务最多支持一个前触发器和一个后触发器。 

## <a name="limits-for-autoscale-provisioned-throughput"></a>自动缩放预配吞吐量的限制

有关自动缩放的吞吐量和存储限制，请参阅[自动缩放](provision-throughput-autoscale.md#autoscale-limits)文章。

## <a name="sql-query-limits"></a>SQL 查询限制

Cosmos DB 支持使用 [SQL](how-to-sql-query.md) 查询项。 下表描述了查询语句的限制，例如子句数目或查询长度方面的限制。

| 资源 | 默认限制 |
| --- | --- |
| SQL 查询的最大长度| 256 KB |
| 每个查询的最大 JOIN 数目| 5 <sup>*</sup>|
| 每个查询的最大 UDF 数目| 10 <sup>*</sup>|
| 每个多边形的最大点数目| 4096 |
| 每个容器的最大包含路径数| 500 |
| 每个容器的最大排除路径数| 500 |
| 组合索引中的最大属性数| 8 |

<sup>*</sup> 可以联系 Azure 支持来提高上述 SQL 查询限制。

## <a name="mongodb-api-specific-limits"></a>特定于 MongoDB API 的限制

Cosmos DB 支持针为 MongoDB 编写的应用程序使用 MongoDB 线路协议。 可以在[支持的 MongoDB 功能和语法](mongodb-feature-support.md)中找到支持的命令和协议版本。

下表列出了特定于 MongoDB 功能支持的限制。 针对 SQL（核心）API 所述的其他服务限制同样适用于 MongoDB API。

| 资源 | 默认限制 |
| --- | --- |
| 最大 MongoDB 查询内存大小（此限制仅适用于 3.2 服务器版本） | 40 MB |
| MongoDB 操作的最长执行时间| 30 秒 |
| 导致服务器端连接关闭的空闲连接超时值* | 30 分钟 |

\* 我们建议客户端应用程序将驱动程序设置中的空闲连接超时值设为 2-3 分钟，因为 [Azure LoadBalancer 的默认超时值为 4 分钟](../load-balancer/load-balancer-tcp-idle-timeout.md#tcp-idle-timeout)。  此超时值可确保客户端计算机与 Azure Cosmos DB 之间的中间负载均衡器不会关闭空闲连接。

## <a name="try-cosmos-db-free-limits"></a>试用 Cosmos DB 可用限制

下表列出了免费试用版试用[Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)的限制。

| 资源 | 默认限制 |
| --- | --- |
| 试用持续时间 | 30天（可以随时续订） |
| 每个订阅的最大容器数（SQL、Gremlin、表 API） | 1 |
| 每个订阅的最大容器（MongoDB API） | 3 |
| 每个容器的最大吞吐量 | 5000 |
| 每个共享吞吐量数据库的最大吞吐量 | 20000 |
| 每个帐户的最大总存储 | 10 GB |

试用 Cosmos DB 仅支持美国中部、北欧和东南亚地区的全局分发。 无法为试用 Azure Cosmos DB 帐户创建 Azure 支持票证。 但是，为具有现有支持计划的订阅者提供了支持。

## <a name="free-tier-account-limits"></a>免费层帐户限制
下表列出了[Azure Cosmos DB 免费层帐户](optimize-dev-test.md#azure-cosmos-db-free-tier)的限制。

| 资源 | 默认限制 |
| --- | --- |
| 每个 Azure 订阅的免费层帐户数 | 1 |
| 免费层折扣的持续时间 | 帐户的生存期。 必须在创建帐户期间选择加入。 |
| 可用的最大 RU/秒 | 400 RU/s |
| 最大存储空间（免费） | 5 GB |
| 共享吞吐量数据库的最大数目 | 5 |
| 共享吞吐量数据库中的容器的最大数目 | 25 <br>在免费层帐户中，具有最多25个容器的共享吞吐量数据库的最小 RU/秒为 400 RU/s。 |

  除此之外，[每个帐户的限制](#per-account-limits)也适用于免费层帐户。

## <a name="next-steps"></a>后续步骤

详细了解 Cosmos DB 的核心概念[全局分发](distribute-data-globally.md)和[分区](partitioning-overview.md)和[预配的吞吐量](request-units.md)。

请通过阅读以下快速入门文章之一，来开始使用 Azure Cosmos DB：

* [Azure Cosmos DB SQL API 入门](create-sql-api-dotnet.md)
* [Azure Cosmos DB 的用于 MongoDB 的 API 入门](create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API 入门](create-cassandra-dotnet.md)
* [Azure Cosmos DB Gremlin API 入门](create-graph-dotnet.md)
* [Azure Cosmos DB 表 API 入门](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
