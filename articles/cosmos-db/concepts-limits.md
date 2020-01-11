---
title: Azure Cosmos DB 服务配额
description: Azure Cosmos DB 不同资源类型的服务配额和默认限制。
author: arramac
ms.author: arramac
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: d6b66c4a4924f8455b58b930621d6a7b93901a2a
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887608"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure Cosmos DB 服务配额

本文概述了 Azure Cosmos DB 中的不同资源提供的默认配额。

## <a name="storage-and-throughput"></a>存储和吞吐量

在订阅下创建 Azure Cosmos 帐户后，可以通过[创建数据库、容器和项](databases-containers-items.md)来管理帐户中的数据。 你可以在容器级别或数据库级别按[请求单位（RU/s 或 RU）](request-units.md)预配吞吐量。 下表列出了每个容器/数据库的存储和吞吐量限制。

| 资源 | 默认限制 |
| --- | --- |
| 每个容器的最大 ru 数（[专用吞吐量预配模式](databases-containers-items.md#azure-cosmos-containers)） | 默认值为1000000。 你可以通过[存档 Azure 支持票证](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)来增加它 |
| 每个数据库的最大每个 RUs （[共享吞吐量预配模式](databases-containers-items.md#azure-cosmos-containers)） | 默认值为1000000。 你可以通过[存档 Azure 支持票证](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)来增加它 |
| 最大每（逻辑）分区键 | 10,000 |
| 每个（逻辑）分区键的所有项的最大存储空间| 10 GB |
| 不同（逻辑）分区键的最大数目 | 无限制 |
| 每个容器的最大存储 | 无限制 |
| 每个数据库的最大存储 | 无限制 |
| 每个帐户的最大附件大小（附件功能正在进行折旧） | 2GB |
| 每 1 GB 需要的最小 ru | 10 RU/秒 |

> [!NOTE]
> 若要了解有关管理工作负荷的最佳实践，其中的分区键需要更高的存储或吞吐量限制，请参阅[创建合成分区键](synthetic-partition-keys.md)。
>

Cosmos 容器（或共享吞吐量数据库）的最小吞吐量必须为 400 ru。 容器增长时，支持的最小吞吐量也取决于以下因素：

* 容器上可以设置的最小吞吐量取决于已在容器上预配的最大吞吐量。 例如，如果吞吐量已增加到 10000 ru，则可能的最低预配吞吐量为 1000 ru
* 共享吞吐量数据库上的最小吞吐量也取决于你曾经在共享吞吐量数据库中创建的容器总数，以每个容器100个 ru 为单位进行度量。 例如，如果在共享的吞吐量数据库中创建了五个容器，则吞吐量必须至少为 500 ru

可以从 Azure 门户或 Sdk 检索容器或数据库的当前和最小吞吐量。 有关详细信息，请参阅[设置容器和数据库的吞吐量](set-throughput.md)。 

> [!NOTE]
> 在某些情况下，你可以将吞吐量降低到小于10%。 使用 API 获取每个容器的最小每个 ru。
>

概括而言，这里是最小预配的 RU 限制。 

| 资源 | 默认限制 |
| --- | --- |
| 每个容器的最小每个 RUs （[专用吞吐量预配模式](databases-containers-items.md#azure-cosmos-containers)） | 400 |
| 每个数据库的最小 ru （[共享吞吐量预配模式](databases-containers-items.md#azure-cosmos-containers)） | 400 |
| 共享吞吐量数据库内每个容器的最小 ru | 100 |

Cosmos DB 支持通过 Sdk 或门户对每个容器或数据库的吞吐量（ru）进行弹性缩放。 在最小值和最大值之间，每个容器都可以在10到100次的范围内同步和立即缩放。 如果请求的吞吐量值超出范围，则会以异步方式执行缩放。 异步缩放可能需要几分钟到几小时才能完成，具体取决于容器中请求的吞吐量和数据存储大小。  

## <a name="control-plane-operations"></a>控制平面操作

你可以使用 Azure 门户、Azure PowerShell、Azure CLI 和 Azure 资源管理器模板来[预配和管理 Azure Cosmos 帐户](how-to-manage-database-account.md)。 下表列出了每个订阅、帐户和操作数的限制。

| 资源 | 默认限制 |
| --- | --- |
| 每个订阅的最大数据库帐户 | 默认值为50。 你可以通过[存档 Azure 支持票证](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)来增加它|
| 区域故障转移的最大数目 | 默认为1小时。 你可以通过[存档 Azure 支持票证](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)来增加它|

> [!NOTE]
> 区域故障转移仅适用于单个区域写入帐户。 多区域写入帐户不需要或限制写入区域的更改。

Cosmos DB 会定期定期备份数据。 有关备份保留间隔和 windows 的详细信息，请参阅[Azure Cosmos DB 中的联机备份和按需数据还原](online-backup-and-restore.md)。

## <a name="per-account-limits"></a>每个帐户的限制

| 资源 | 默认限制 |
| --- | --- |
| 最大数据库数 | 无限制 |
| 每个数据库的最大容器数（或帐户） | 无限制 |
| 最大区域数 | 无限制（所有 Azure 区域） |

## <a name="per-container-limits"></a>每个容器的限制

根据所使用的 API，Azure Cosmos 容器可以表示集合、表或图形。 容器支持[唯一键约束](unique-keys.md)、[存储过程、触发器、udf](stored-procedures-triggers-udfs.md)和[索引策略](how-to-manage-indexing-policy.md)的配置。 下表列出了特定于容器中的配置的限制。 

| 资源 | 默认限制 |
| --- | --- |
| 数据库或容器名称的最大长度 | 255 |
| 每个容器的最大存储过程数 | 100 <sup>*</sup>|
| 每个容器的最大 Udf | 25 <sup>*</sup>|
| 索引策略中的最大路径数| 100 <sup>*</sup>|
| 每个容器的唯一密钥的最大数目|10 <sup>*</sup>|
| 每个唯一键约束的最大路径数|16 <sup>*</sup>|

<sup>*</sup>可以通过联系 Azure 支持人员，增加这些每个容器的限制。

## <a name="per-item-limits"></a>每项限制

根据所使用的 API，Azure Cosmos 项可以代表集合中的文档、表中的行或图形中的节点或边缘。 下表显示 Cosmos DB 中每个项的限制。 

| 资源 | 默认限制 |
| --- | --- |
| 项的最大大小 | 2 MB （utf-8 表示形式的 utf-8 长度） |
| 分区键值的最大长度 | 2048字节 |
| Id 值的最大长度 | 1024字节 |
| 每个项目的最大属性数 | 无实际限制 |
| 最大嵌套深度 | 无实际限制 |
| 属性名的最大长度 | 无实际限制 |
| 属性值的最大长度 | 无实际限制 |
| 字符串属性值的最大长度 | 无实际限制 |
| 数值属性值的最大长度 | IEEE754 双精度64位 |

除了对分区键和 id 值的长度限制以及 2 MB 的总大小限制外，项负载没有任何限制，如属性数量和嵌套深度。 可能需要为具有大型或复杂项结构的容器配置索引策略，以减少 RU 消耗。 有关实际示例，请参阅[Cosmos DB 建模项](how-to-model-partition-example.md)和用于管理大型项的模式。

## <a name="per-request-limits"></a>每请求限制

Azure Cosmos DB 支持对容器、项和数据库等资源[进行 CRUD 和查询操作](https://docs.microsoft.com/rest/api/cosmos-db/)。 它还支持针对容器中具有相同分区键的多个项的[事务批请求](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch)。

| 资源 | 默认限制 |
| --- | --- |
| 单个操作的最长执行时间（例如存储过程执行或单个查询页检索）| 5 秒 |
| 最大请求大小（例如，存储过程、CRUD）| 2 MB |
| 最大响应大小（例如，分页查询） | 4 MB |
| 事务批次中的最大操作数 | 100 |

当某个操作（例如查询）达到执行超时值或响应大小限制时，它会向客户端返回结果页和继续标记，以继续执行。 单个查询可跨页面/延续运行的持续时间没有实际限制。

Cosmos DB 使用 HMAC 进行授权。 可以使用主密钥或[资源令牌](secure-access-to-data.md)对资源（如容器、分区键或项）进行精细的访问控制。 下表列出了 Cosmos DB 中的授权令牌限制。

| 资源 | 默认限制 |
| --- | --- |
| 最大主令牌到期时间 | 15 分钟  |
| 最小资源令牌到期时间 | 10 分钟  |
| 最大资源令牌到期时间 | 默认情况下为 24 h。 你可以通过[存档 Azure 支持票证](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)来增加它|
| 令牌授权的最大时钟偏差| 15 分钟 |

Cosmos DB 支持在写入过程中执行触发器。 对于每个写入操作，服务最多支持一个预先触发器和一个后期触发器。 

## <a name="autopilot-mode-limits"></a>Autopilot 模式限制

有关 Autopilot 模式下的吞吐量和存储限制，请参阅[Autopilot](provision-throughput-autopilot.md#autopilot-limits)文章。

## <a name="sql-query-limits"></a>SQL 查询限制

Cosmos DB 支持使用[SQL](how-to-sql-query.md)查询项。 下表描述了查询语句中的限制，例如，在子句数或查询长度方面。

| 资源 | 默认限制 |
| --- | --- |
| SQL 查询的最大长度| 256 KB <sup>*</sup>|
| 每个查询的最大联接数| 5 <sup>*</sup>|
| 每个查询的最大 ANDs| 2000 <sup>*</sup>|
| 每个查询的最大 Or| 2000 <sup>*</sup>|
| 每个查询的最大 Udf| 10 <sup>*</sup>|
| Expression 中的最大参数| 6000 <sup>*</sup>|
| 每个多边形的最大点数| 4096 <sup>*</sup>|

<sup>*</sup>可以通过联系 Azure 支持人员来增加这些 SQL 查询限制。

## <a name="mongodb-api-specific-limits"></a>MongoDB API 特定限制

Cosmos DB 支持针对 MongoDB 编写的应用程序的 MongoDB 线路协议。 可以在[支持的 MongoDB 功能和语法](mongodb-feature-support.md)上查找支持的命令和协议版本。

下表列出了特定于 MongoDB 功能支持的限制。 SQL （core） API 提到的其他服务限制还适用于 MongoDB API。

| 资源 | 默认限制 |
| --- | --- |
| 最大 MongoDB 查询内存大小 | 40 MB |
| MongoDB 操作的最长执行时间| 30 秒 |

## <a name="try-cosmos-db-free-limits"></a>试用 Cosmos DB 可用限制

下表列出了免费试用版试用[Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)的限制。

| 资源 | 默认限制 |
| --- | --- |
| 试用持续时间 | 30天（可以随时续订） |
| 每个订阅的最大容器数（SQL、Gremlin、表 API） | 第 |
| 每个订阅的最大容器（MongoDB API） | 3 |
| 每个容器的最大吞吐量 | 5000 |
| 每个共享吞吐量数据库的最大吞吐量 | 20000 |
| 每个帐户的最大总存储 | 10 GB |

试用 Cosmos DB 仅支持美国中部、北欧和东南亚地区的全局分发。 无法为试用 Azure Cosmos DB 帐户创建 Azure 支持票证。 但是，为具有现有支持计划的订阅者提供了支持。

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
