---
title: Azure Cosmos DB 服务配额
description: Azure Cosmos DB 不同资源类型的服务配额和默认限制。
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 7ef7a55c81441077d2217ccfc41a2a9c9578eefe
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697559"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure Cosmos DB 服务配额

本文概述了 Azure Cosmos DB 中提供给不同资源的默认配额。

## <a name="storage-and-throughput"></a>存储和吞吐量

在订阅下创建 Azure Cosmos 帐户后，可以通过[创建数据库、容器和项](databases-containers-items.md)来管理帐户中的数据。 可以按照[请求单位（RU/秒或 RU 数）](request-units.md)在容器或数据库级别预配吞吐量。 下表列出了每个容器/数据库的存储和吞吐量限制。

| 资源 | 默认限制 |
| --- | --- |
| 每容器的最大 RU 数（[专用吞吐量预配模式](databases-containers-items.md#azure-cosmos-containers)） | 默认为 1,000,000。 可以通过[填写 Azure 支持票证](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)增加此值 |
| 每个数据库的最大 RU 数（[共享吞吐量预配模式](databases-containers-items.md#azure-cosmos-containers)） | 默认为 1,000,000。 可以通过[填写 Azure 支持票证](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)增加此值 |
| 每个（逻辑）分区键的最大 RU 数 | 10,000 |
| 每个（逻辑）分区键的所有项的最大存储量| 20 GB |
| 不同（逻辑）分区键的最大数目 | 无限制 |
| 每个容器的最大存储量 | 无限制 |
| 每个数据库的最大存储量 | 无限制 |
| 每个帐户的最大附件大小（附件功能正在进行折旧） | 2 GB |
| 每 1 GB 需要的最小 RU 数 | 10 RU/秒 |

> [!NOTE]
> 若要了解有关管理其分区键需要更高存储或吞吐量限制的工作负荷的最佳做法，请参阅[创建合成分区键](synthetic-partition-keys.md)。
>

Cosmos 容器（或共享吞吐量数据库）的最小吞吐量必须为 400 RU/秒。 随着容器的扩展，支持的最小吞吐量还取决于以下因素：

* 曾经为容器预配的最大吞吐量。 例如，如果吞吐量已增加到 50000 RU/秒，则可能的最低预配吞吐量为 500 RU/秒
* 容器中的当前存储大小 (GB)。 例如，如果容器有 100 GB 的存储空间，则可能的最低预配吞吐量为 1000 RU/秒
* 共享吞吐量数据库上的最小吞吐量还取决于曾在共享吞吐量数据库中创建的容器总数，按每个容器 100 RU 来度量。 例如，如果在共享吞吐量数据库中创建了五个容器，则吞吐量必须至少为 500 RU/秒

可以从 Azure 门户或 SDK 检索容器或数据库的当前和最小吞吐量。 有关详细信息，请参阅[对容器和数据库预配吞吐量](set-throughput.md)。 

> [!NOTE]
> 在某些情况下，可以将吞吐量降低到小于 10%。 使用 API 获取每个容器的精确最小 RU 数。
>

总之，最小预配 RU 限制如下所示。 

| 资源 | 默认限制 |
| --- | --- |
| 每个容器的最大 RU 数（[专用吞吐量预配模式](databases-containers-items.md#azure-cosmos-containers)） | 400 |
| 每个数据库的最小 RU 数（[共享吞吐量预配模式](databases-containers-items.md#azure-cosmos-containers)） | 400 |
| 共享吞吐量数据库内每个容器的最小 RU 数 | 100 |

Cosmos DB 支持通过 SDK 或门户对每个容器或数据库的吞吐量 (RU) 进行弹性缩放。 在最小值和最大值之间，每个容器都可以在 10 到 100 倍的缩放范围内同步和立即缩放。 如果请求的吞吐量值超出范围，则会以异步方式执行缩放。 异步缩放可能需要几分钟到几小时才能完成，具体取决于容器中请求的吞吐量和数据存储大小。  

## <a name="control-plane-operations"></a>控制平面操作

可以使用 Azure 门户、Azure PowerShell、Azure CLI 和 Azure 资源管理器模板[预配和管理 Azure Cosmos 帐户](how-to-manage-database-account.md)。 下表列出了每个订阅、帐户和操作数的限制。

| 资源 | 默认限制 |
| --- | --- |
| 每个订阅的最大数据库帐户数 | 默认值为 50。 可以通过[填写 Azure 支持票证](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)增加此值|
| 最大区域故障转移数 | 默认值为 1/小时。 可以通过[填写 Azure 支持票证](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)增加此值|

> [!NOTE]
> 区域故障转移仅适用于单个区域写入帐户。 多区域写入帐户不需要更改写入区域或对此有一些限制。

Cosmos DB 会定期自动备份数据。 有关备份保留时间间隔和窗口的详细信息，请参阅 [Azure Cosmos DB 中的联机备份和按需数据还原](online-backup-and-restore.md)。

## <a name="per-account-limits"></a>每个帐户的限制

| 资源 | 默认限制 |
| --- | --- |
| 最大数据库数 | 无限制 |
| 共享吞吐量数据库中每个数据库的最大容器数 |25 |
| 每个专用吞吐量数据库或帐户的最大容器数  |不受限制 |
| 最大区域数 | 无限制（所有 Azure 区域） |

## <a name="per-container-limits"></a>每个容器的限制

根据所使用的 API，Azure Cosmos 容器可以表示集合、表或图形。 容器支持[唯一键约束](unique-keys.md)、[存储过程、触发器和 UDF](stored-procedures-triggers-udfs.md) 以及[索引策略](how-to-manage-indexing-policy.md)的配置。 下表列出了容器内特定于配置的限制。 

| 资源 | 默认限制 |
| --- | --- |
| 数据库或容器名称的最大长度 | 255 |
| 每个容器的最大存储过程数 | 100 <sup>*</sup>|
| 每个容器的最大 UDF 数 | 25 <sup>*</sup>|
| 索引策略中的最大路径数| 100 <sup>*</sup>|
| 每个容器的最大唯一键数目|10 <sup>*</sup>|
| 每个唯一键约束的最大路径数|16 <sup>*</sup>|

<sup>*</sup> 可以通过联系 Azure 支持人员增加每个容器的限制。

## <a name="per-item-limits"></a>每项的限制

根据所使用的 API，Azure Cosmos 项可以表示集合中的一个文件、表格中的一行或者图形中的一个节点/边缘。 下表显示 Cosmos DB 中每项的限制。 

| 资源 | 默认限制 |
| --- | --- |
| 最大项大小 | 2 MB（JSON 表示形式的 UTF-8 长度） |
| 分区键值的最大长度 | 2048 个字节 |
| ID 值的最大长度 | 1023 个字节 |
| 每项的最大属性数 | 无实际限制 |
| 最大嵌套深度 | 无实际限制 |
| 属性名称的最大长度 | 无实际限制 |
| 属性值的最大长度 | 无实际限制 |
| 字符串属性值的最大长度 | 无实际限制 |
| 数字属性值的最大长度 | IEEE754 双精度 64 位 |

除了对分区键和 ID 值的长度限制以及 2 MB 的总大小限制外，对项负载没有任何限制，如属性数量和嵌套深度。 可能需要为具有大型或复杂项结构的容器配置索引策略，以减少 RU 消耗。 有关实际示例，请参阅 [Cosmos DB 中的建模项](how-to-model-partition-example.md)以及用于管理大型项的模式。

## <a name="per-request-limits"></a>每个请求限制

Azure Cosmos DB 支持对容器、项和数据库等资源执行 [CRUD 和查询操作](https://docs.microsoft.com/rest/api/cosmos-db/)。 它还支持对容器中具有相同分区键的多个项发出[事务性批处理请求](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch)。

| 资源 | 默认限制 |
| --- | --- |
| 单个操作的最长执行时间（例如存储过程执行或单个查询页检索）| 5 秒 |
| 最大请求大小（例如存储过程、CRUD）| 2 MB |
| 最大响应大小（例如分页查询） | 4 MB |
| 事务性批处理中的最大操作数 | 100 |

一旦查询之类的操作达到执行超时或响应大小限制，它会向客户端返回结果页和继续标记，以继续执行操作。 单个查询可跨页面/继续符运行的持续时间没有实际限制。

Cosmos DB 使用 HMAC 进行授权。 可以使用主密钥或[资源令牌](secure-access-to-data.md)对资源（如容器、分区键或项）进行精细的访问控制。 下表列出了 Cosmos DB 中的授权令牌限制。

| 资源 | 默认限制 |
| --- | --- |
| 最大主令牌到期时间 | 15 分钟  |
| 最小资源令牌到期时间 | 10 分钟  |
| 最大资源令牌到期时间 | 默认为 24 小时。 可以通过[填写 Azure 支持票证](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)增加此值|
| 令牌授权的最大时钟偏差| 15 分钟 |

Cosmos DB 支持在写入过程中执行触发器。 对于每个写入操作，该服务最多支持一个预先触发器和一个后期触发器。 

## <a name="limits-for-autoscale-provisioned-throughput"></a>自动缩放预配吞吐量限制

有关自动缩放吞吐量和存储限制的更多详细说明，请参阅[自动缩放](provision-throughput-autoscale.md#autoscale-limits)文章和[常见问题解答](autoscale-faq.md#lowering-the-max-rus)。

| 资源 | 默认限制 |
| --- | --- |
| 系统可以缩放到的最大 RU/秒 |  `Tmax`，用户设置的自动缩放最大 RU/秒|
| 系统可以缩放到的最小 RU/秒 | `0.1 * Tmax`|
| 系统缩放到的当前 RU/秒  |  `0.1*Tmax <= T <= Tmax`根据使用情况|
| 每小时最小可计费 RU/秒| `0.1 * Tmax` <br></br>计费以小时为单位，在这种情况下，将按系统在一小时内缩放到的最高 RU/秒或 `0.1*Tmax` 计费，以较大者为准。 |
| 容器的最小自动缩放最大 RU/秒  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)`，舍入为最接近的 1000 RU/秒 |
| 数据库的最小自动缩放最大 RU/秒  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`，舍入为最接近的 1000 RU/秒。 <br></br>请注意，如果数据库有 25 个以上的容器，每增加一个容器，系统会将最小自动缩放最大 RU/秒增加 1000 RU/秒。 例如，如果有 30 个容器，则可以设置的最低自动缩放最大 RU/秒为 9000 RU/秒（缩放范围是 900 - 9000 RU/秒）。

## <a name="sql-query-limits"></a>SQL 查询限制

Cosmos DB 支持使用 [SQL](how-to-sql-query.md)查询各项。 下表描述了查询语句中的限制，例如，在子句数或查询长度方面的限制。

| 资源 | 默认限制 |
| --- | --- |
| 最大 SQL 队列长度| 256 KB |
| 每个查询的最大 JOIN 数| 5 <sup>*</sup>|
| 每个查询的最大 UDF 数| 10 <sup>*</sup>|
| 每个多边形的最大点数| 4096 |
| 每个容器的最大包含路径数| 500 |
| 每个容器的最大排除路径数| 500 |
| 复合索引中的最大属性数| 8 |

<sup>*</sup> 可以通过联系 Azure 支持人员增加这些 SQL 查询限制。

## <a name="mongodb-api-specific-limits"></a>MongoDB API 特定限制

Cosmos DB 支持针对 MongoDB 编写的应用程序的 MongoDB Wire Protocol。 可以在[受支持的 MongoDB 功能和语法](mongodb-feature-support.md)中找到受支持的命令和协议版本。

下表列出了特定于 MongoDB 功能支持的限制。 SQL（核心）API 提到的其他服务限制也适用于 MongoDB API。

| 资源 | 默认限制 |
| --- | --- |
| 最大 MongoDB 查询内存大小（此限制仅适用于 3.2 服务器版本） | 40 MB |
| MongoDB 操作的最长执行时间| 30 秒 |
| 服务器端连接关闭的空闲连接超时* | 30 分钟 |

\* 建议客户端应用程序将驱动程序设置中的空闲连接超时设置为 2-3 分钟，因为 [Azure LoadBalancer 的默认超时为 4 分钟](../load-balancer/load-balancer-tcp-idle-timeout.md#tcp-idle-timeout)。  此超时将确保客户端计算机与 Azure Cosmos DB 之间的中间负载均衡器不会关闭空闲连接。

## <a name="try-cosmos-db-free-limits"></a>免费试用 Cosmos DB 限制

下表列出了[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 的限制。

| 资源 | 默认限制 |
| --- | --- |
| 试用持续时间 | 30 天（过期后可以请求重新试用） <br> 过期后，存储的信息将被删除。 |
| 每个订阅的最大容器数（SQL、Gremlin、表 API） | 1 |
| 每个订阅的最大容器数 (MongoDB API) | 3 |
| 每个容器的最大吞吐量 | 5000 |
| 每个共享吞吐量数据库的最大吞吐量 | 20000 |
| 每个帐户的最大总存储量 | 10 GB |

试用 Cosmos DB 仅支持美国中部、北欧和东南亚地区的全局分发。 无法为 Azure Cosmos DB 试用帐户创建 Azure 支持票证。 但是，为参与现有支持计划的订阅者提供了支持。

## <a name="free-tier-account-limits"></a>免费层帐户限制
下表列出了 [Azure Cosmos DB 免费试用帐户](optimize-dev-test.md#azure-cosmos-db-free-tier)的限制。

| 资源 | 默认限制 |
| --- | --- |
| 每个 Azure 订阅的免费层帐户数 | 1 |
| 免费层折扣持续时间 | 帐户的生存期。 必须在创建帐户期间选择加入。 |
| 免费的最大 RU 数 | 400 RU/秒 |
| 免费的最大存储量 | 5 GB |
| 最大共享吞吐量数据库数 | 5 |
| 共享吞吐量数据库中的最大容器数 | 25 <br>在免费层帐户中，最多包含 25 个容器的共享吞吐量数据库的最小 RU/秒为 400 RU/秒。 |

  除此之外，[每个帐户的限制](#per-account-limits)也适用于免费层帐户。

## <a name="next-steps"></a>后续步骤

详细了解 Cosmos DB 的核心概念：[全局分布](distribute-data-globally.md)[分区](partitioning-overview.md)以及[预配吞吐量](request-units.md)。

请通过阅读以下快速入门文章之一，来开始使用 Azure Cosmos DB：

* [Azure Cosmos DB SQL API 入门](create-sql-api-dotnet.md)
* [Azure Cosmos DB 的用于 MongoDB 的 API 入门](create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API 入门](create-cassandra-dotnet.md)
* [Azure Cosmos DB Gremlin API 入门](create-graph-dotnet.md)
* [Azure Cosmos DB 表 API 入门](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
