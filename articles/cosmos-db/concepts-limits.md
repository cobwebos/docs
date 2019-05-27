---
title: Azure Cosmos DB 中的限制
description: 本指南介绍了 Azure Cosmos DB 中的限制。
author: arramac
ms.author: arramac
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2019
ms.openlocfilehash: 0086327661df637dc0ae60208ed9424b4610ef0e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969487"
---
# <a name="limits-in-azure-cosmos-db"></a>Azure Cosmos DB 中的限制

本文概述在 Azure Cosmos DB 服务的限制。

## <a name="storage-and-throughput"></a>存储和吞吐量

你的订阅下创建 Azure Cosmos 帐户后，可以通过在帐户中管理数据[创建数据库、 容器和项](databases-containers-items.md)。 你可以设置在容器级别或数据库级别的吞吐量[请求单位 （RU/秒或 Ru）](request-units.md)。 下表列出了针对存储和吞吐量，每个容器/数据库限制。

| Resource | 默认限制 |
| --- | --- |
| 每个容器的最大 ru 数 ([专用的吞吐量预配的模式](databases-containers-items.md#azure-cosmos-containers)) | 默认情况下 1,000,000。 您可以通过增加[提出 Azure 支持票证](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)或联系我们通过[询问 Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| 每个数据库的最大 ru 数 ([共享的吞吐量预配的模式](databases-containers-items.md#azure-cosmos-containers)) | 默认情况下 1,000,000。 您可以通过增加[提出 Azure 支持票证](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)或联系我们通过[询问 Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| 每个 （逻辑） 的分区键的最大 ru 数 | 10,000 |
| 在每个 （逻辑） 的分区键的所有项之间的最大存储| 10 GB |
| 最大数目的非重复 （逻辑） 的分区键 | 无限制 |
| 每个容器的最大存储 | 无限制 |
| 每个数据库的最大存储 | 无限制 |

> [!NOTE]
> 若要管理有存储或吞吐量需要更多配额的分区键的工作负荷的最佳做法，请参阅[设计热分区键](synthetic-partition-keys.md)
>

Cosmos 容器 （或共享的吞吐量数据库） 必须具有的最小吞吐量为 400 个 Ru。 随着容器时，支持的最小吞吐量还取决于以下因素：

* 容器中占用的最大存储空间的单位为增量的每 GB 消耗的存储容量的 40 Ru。 例如，如果容器包含 100 GB 的数据，则吞吐量必须至少 4000 Ru
* 曾经在容器上预配的最大吞吐量。 该服务支持容器的预配的最大的 10%的降低吞吐量。 例如，如果吞吐量提高到 10000 Ru，则最低可能预配的吞吐量应 1000 Ru
* 在每个容器的 100 个 Ru 测量的以往在共享的吞吐量数据库中，创建的容器的总数。 例如，如果你已创建共享的吞吐量数据库中的五个容器，则吞吐量必须至少 500 Ru

可以从 Azure 门户或 Sdk 检索容器或数据库的当前和最小吞吐量。 有关详细信息，请参阅[容器和数据库上的预配吞吐量](set-throughput.md)。 总之，以下是最小的预配的 RU 限制。 

| Resource | 默认限制 |
| --- | --- |
| 每个容器的最小 Ru ([专用的吞吐量预配的模式](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| 每个数据库的最小 Ru ([共享的吞吐量预配的模式](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| 每个共享的吞吐量数据库中的容器的最小 Ru | 100 |
| 每 GB 消耗的存储容量的最小 Ru | 40 |

Cosmos DB 支持每个容器或通过 Sdk 或门户的数据库弹性缩放吞吐量 (Ru)。 每个容器可以扩展以同步方式并立即在 10 到 100 倍，最小值和最大值之间的缩放范围内。 如果请求的吞吐量值超出范围，缩放是以异步方式执行。 异步缩放可能会需要分钟到数小时才能完成，具体取决于请求的吞吐量和容器中的数据存储大小。  

## <a name="control-plane-operations"></a>控制平面操作

你可以[预配和管理你的 Azure Cosmos 帐户](how-to-manage-database-account.md)使用 Azure 门户、 Azure PowerShell、 Azure CLI 和 Azure 资源管理器模板。 下表列出了每个订阅、 帐户和操作数目的限制。

| Resource | 默认限制 |
| --- | --- |
| 每个订阅的最大数据库帐户 | 默认情况下 50。 您可以通过增加[提出 Azure 支持票证](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)或联系我们通过[询问 Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| 区域故障转移的最大数目 | 默认为 1/小时。 您可以通过增加[提出 Azure 支持票证](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)或联系我们通过[询问 Cosmos DB](mailto:askcosmosdb@microsoft.com)|

> [!NOTE]
> 区域故障转移仅适用于单区域写入操作帐户。 多区域写入帐户不需要或已更改的写入区域任何限制。

Cosmos DB 自动备份的数据按固定间隔。 备份保留的时间间隔和 windows 的详细信息，请参阅[在 Azure Cosmos DB 中的联机备份和按需数据还原](online-backup-and-restore.md)。

## <a name="per-container-limits"></a>每个容器限制

具体取决于您使用哪个 API，Azure Cosmos 容器可以表示任何一个集合，一个表，或图形。 容器支持的配置[唯一键约束](unique-keys.md)，[存储过程、 触发器和 Udf](stored-procedures-triggers-udfs.md)，并[索引策略](how-to-manage-indexing-policy.md)。 下表列出了特定于容器中配置的限制。 

| Resource | 默认限制 |
| --- | --- |
| 数据库或容器名称的最大长度 | 255 |
| 每个容器的最大存储的过程 | 100 <sup>*</sup>|
| 每个容器的最大 Udf | 25 <sup>*</sup>|
| 索引策略路径的最大数量| 100 <sup>*</sup>|
| 每个容器的唯一键的最大数目|10 <sup>*</sup>|
| 每个唯一键约束的路径的最大数量|16 <sup>*</sup>|

<sup>*</sup> 您可以通过联系 Azure 支持人员或联系我们通过增加任何这些每个容器限制[询问 Cosmos DB](mailto:askcosmosdb@microsoft.com)。

## <a name="per-item-limits"></a>每个项的限制

具体取决于您使用哪个 API，Azure Cosmos 项可以表示集合，一个表，或节点中的行中的文档，或者在图形中的边缘。 下表显示了 Cosmos DB 中的每个项的限制。 

| Resource | 默认限制 |
| --- | --- |
| 项的最大大小 | 2 MB （utf-8 长度的 JSON 表示形式） |
| 分区键值的最大长度 | 2048 个字节 |
| Id 值的最大长度 | 1024 个字节 |
| 每个项的属性的最大数目 | 没有实际限制 |
| 最大嵌套深度 | 没有实际限制 |
| 属性名称的最大长度 | 没有实际限制 |
| 属性值的最大长度 | 没有实际限制 |
| 字符串属性值的最大长度 | 没有实际限制 |
| 数值属性值的最大长度 | IEEE754 双精度 64 位 |

没有任何限制上类似的值的属性和嵌套的深度，分区键和 id 值和整体的长度限制除项有效负载大小为 2 MB 的限制。 您可能需要使用大型或复杂的项目结构，以减少 RU 消耗配置索引策略的容器。 请参阅[建模 Cosmos DB 中的项](how-to-model-partition-example.md)有关实际示例和模式来管理大项目。

## <a name="per-request-limits"></a>每个请求限制

Cosmos DB 支持[CRUD 和查询操作](https://docs.microsoft.com/rest/api/cosmos-db/)对容器、 项和数据库等资源。  

| Resource | 默认限制 |
| --- | --- |
| 单个操作 （如存储的过程执行或单个查询页检索） 的最长执行时间| 5 秒 |
| 最大请求大小 （存储过程，CRUD）| 2 MB |
| 最大响应大小 （例如，分页查询） | 4 MB |

一次操作像查询达到执行超时或响应的大小限制，它返回结果和一个继续令牌的页到客户端继续执行。 上一个查询可以运行跨页/连续任务的持续时间没有实际限制。

Cosmos DB 使用 HMAC 进行授权。 可以使用任一一个主密钥，或[资源令牌](secure-access-to-data.md)的精细访问控制对资源容器，分区键或项。 下表列出了针对 Cosmos DB 中的授权令牌限制。

| Resource | 默认限制 |
| --- | --- |
| 最大的主令牌到期时间 | 15 分钟  |
| 最小资源令牌到期时间 | 10 分钟  |
| 最大资源令牌到期时间 | 默认情况下的 24 小时。 您可以通过增加[提出 Azure 支持票证](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)或联系我们通过[询问 Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| 为令牌授权的最大时钟偏差| 15 分钟 |

Cosmos DB 支持触发器的执行期间写入操作。 该服务支持一个预触发器和一个后的触发器每个写入操作最的多。 

## <a name="sql-query-limits"></a>SQL 查询限制

Cosmos DB 支持查询使用的项[SQL](how-to-sql-query.md)。 下表描述了在查询语句中，例如在数子句或查询长度方面的限制。

| Resource | 默认限制 |
| --- | --- |
| SQL 查询的最大长度| 256 KB <sup>*</sup>|
| 每个查询的最大联接| 5 <sup>*</sup>|
| 每个查询的最大 And| 2000 <sup>*</sup>|
| 每个查询的最大 Or| 2000 <sup>*</sup>|
| 每个查询的最大 Udf| 10 <sup>*</sup>|
| 每个表达式中的最大参数| 6000 <sup>*</sup>|
| 每个多边形的最大点| 4096 <sup>*</sup>|

<sup>*</sup> 您可以通过联系 Azure 支持人员或联系我们通过增加这些 SQL 查询限制的任何[询问 Cosmos DB](mailto:askcosmosdb@microsoft.com)。

## <a name="mongodb-api-specific-limits"></a>特定于 MongoDB API 的限制

Cosmos DB 支持 MongoDB 有线协议为 MongoDB 编写的应用程序。 可以查找受支持的命令和协议版本[支持 MongoDB 功能和语法](mongodb-feature-support.md)。

下表列出了特定于 MongoDB 功能支持的限制。 针对 SQL （核心） API 的其他服务限制也适用于 MongoDB API。

| Resource | 默认限制 |
| --- | --- |
| 最大 MongoDB 查询内存大小 | 40 MB |
| MongoDB 操作的最长执行时间| 30 秒 |

## <a name="try-cosmos-db-free-limits"></a>试用 Cosmos DB 免费限制

下表列出了针对限制[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)试用版。

| Resource | 默认限制 |
| --- | --- |
| 试用版的持续时间 | 30 天 (可以续订任意次数) |
| 每个订阅 (SQL、 Gremlin，表 API) 的最大容器 | 第 |
| 每个订阅 (MongoDB API) 的最大容器 | 3 |
| 每个容器的最大吞吐量 | 5000 |
| 每个共享吞吐量数据库的最大吞吐量 | 20000 |
| 每个帐户的最大总存储 | 10 GB |

试用 Cosmos DB 支持仅在美国中部、 北欧和东南亚区域中的全局分发。 不能为试用 Azure Cosmos DB 帐户创建 azure 支持票证。 但是，具有现有支持计划的订阅服务器提供支持。

## <a name="next-steps"></a>后续步骤

了解有关 Cosmos DB 的核心概念[全局分发](distribute-data-globally.md)并[分区](partitioning-overview.md)并[预配的吞吐量](request-units.md)。

请通过阅读以下快速入门文章之一，来开始使用 Azure Cosmos DB：

* [Azure Cosmos DB SQL API 入门](create-sql-api-dotnet.md)
* [Azure Cosmos DB 的用于 MongoDB 的 API 入门](create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API 入门](create-cassandra-dotnet.md)
* [Azure Cosmos DB Gremlin API 入门](create-graph-dotnet.md)
* [Azure Cosmos DB 表 API 入门](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
