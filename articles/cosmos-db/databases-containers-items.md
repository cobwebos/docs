---
title: 在 Azure Cosmos DB 中使用数据库、容器和项
description: 本文介绍如何在 Azure Cosmos DB 中创建和使用数据库、容器与项。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 9c8460380755c6057f7507443d0b564e85c2ff86
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598495"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中使用数据库、容器和项

在 Azure 订阅下创建 [Azure Cosmos DB 帐户](account-overview.md)后，可以通过创建数据库、容器和项来管理帐户中的数据。 本文将介绍上述每个实体。 

下图显示 Azure Cosmos DB 帐户中不同实体的层次结构：

![Azure Cosmos 帐户实体](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Azure Cosmos 数据库

可以在帐户下创建一个或多个 Azure Cosmos 数据库。 数据库类似于命名空间， 数据库是一组 Azure Cosmos 容器的管理单元。 下表显示如何将 Azure Cosmos 数据库映射到各种特定于 API 的实体：

| Azure Cosmos 实体 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 数据库 | 数据库 | 密钥空间 | 数据库 | 数据库 | 不可用 |

> [!NOTE]
> 如果使用表 API 帐户，在创建第一个表时，系统会自动在 Azure Cosmos 帐户中创建默认数据库。

### <a name="operations-on-an-azure-cosmos-database"></a>对 Azure Cosmos 数据库执行的操作

可以使用下表中所述的 Azure Cosmos API 来与 Azure Cosmos 数据库交互：

| 操作 | Azure CLI | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- |
|枚举所有数据库| 是 | 是 | 是（将数据库映射至密钥空间） | 是 | 不可用 | 不可用 |
|读取数据库| 是 | 是 | 是（将数据库映射至密钥空间） | 是 | 不可用 | 不可用 |
|新建数据库| 是 | 是 | 是（将数据库映射至密钥空间） | 是 | 不可用 | 不可用 |
|更新数据库| 是 | 是 | 是（将数据库映射至密钥空间） | 是 | 不可用 | 不可用 |


## <a name="azure-cosmos-containers"></a>Azure Cosmos 容器

Azure Cosmos 容器是预配的吞吐量和存储的缩放单元。 容器会进行水平分区，然后在多个区域间复制。 添加到容器的项以及针对容器预配的吞吐量将基于分区键自动分配给一组逻辑分区。 若要详细了解分区和分区键，请参阅[将数据分区](partition-data.md)。 

创建 Azure Cosmos 容器时，会在以下某种模式下配置吞吐量：

* **专用预配吞吐量模式**：针对容器预配的吞吐量是专门为该容器保留的，由 SLA 提供支持。 有关详细信息，请参阅[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。

* **共享预配吞吐量模式**：这些容器与同一数据库中的其他容器共享预配吞吐量（不包含已配置专用预配吞吐量的容器）。 换句话说, 在所有 "共享吞吐量" 容器之间共享数据库的预配吞吐量。 有关详细信息，请参阅[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。

> [!NOTE]
> 只有在创建数据库和容器时，才能配置共享吞吐量和专用吞吐量。 若要在创建容器后从专用吞吐量模式切换为共享吞吐量模式（或反之），必须创建一个新容器，并将数据迁移到该容器。 可以使用 Azure Cosmos DB 更改源功能迁移数据。

无论是使用专用还是共享预配吞吐量模式创建容器，Azure Cosmos 容器都可以弹性缩放。

Azure Cosmos 容器是与架构无关的项容器。 容器中的项可以采用任意架构。 例如，可以在同一个容器中放置一个表示人员的项，以及一个表示汽车的项。 默认情况下，添加到容器的所有项会自动编制索引，不需要进行显式的索引或架构管理。 通过在容器上配置的[索引策略](index-overview.md)，可以自定义索引行为。 

可以针对 Azure Cosmos 容器中的所选项或整个容器设置[生存时间 (TTL)](time-to-live.md)，以正常从系统中清除这些项。 Azure Cosmos DB 会在这些项过期时自动将其删除。 这样还能保证对这些容器执行的查询不会返回固定边界内已过期的项。 有关详细信息，请参阅[对容器配置 TTL](how-to-time-to-live.md)。

可以使用[更改源](change-feed.md)订阅针对容器的每个逻辑分区管理的操作日志。 更改源提供对容器执行的所有更新的日志，以及更新前和更新后的项的映像。 有关详细信息，请参阅[使用更改源生成被动式应用程序](serverless-computing-database.md)。 还可以通过使用容器上的更改源策略来配置更改源的保留期限。 

可为 Azure Cosmos 容器注册[存储过程、触发器、用户定义的函数 (UDF)](stored-procedures-triggers-udfs.md) 和[合并过程](how-to-manage-conflicts.md)。 

可以在 Azure Cosmos 容器上指定一个[唯一键约束](unique-keys.md)。 通过创建唯一键策略，可确保每个逻辑分区键的一个或多个值的唯一性。 如果使用唯一键策略创建容器，则无法创建值与唯一键约束指定的值重复的任何新项或更新的项。 若要了解详细信息，请参阅[唯一键约束](unique-keys.md)。

Azure Cosmos 容器专用于 API 特定的实体，如下表所示：

| Azure Cosmos 实体 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 容器 | Collection | 表 | Collection | 图形 | 表 |

### <a name="properties-of-an-azure-cosmos-container"></a>Azure Cosmos 容器的属性

Azure Cosmos 容器具备一组系统定义的属性。 根据所用的 API，某些属性可能不会直接公开。 下表介绍了系统定义属性的列表：

| 系统定义的属性 | 由系统生成或用户可配置 | 用途 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | 由系统生成 | 容器的唯一标识符 | 是 | 否 | 否 | 否 | 否 |
|\_etag | 由系统生成 | 用于乐观并发控制的实体标记 | 是 | 否 | 否 | 否 | 否 |
|\_ts | 由系统生成 | 容器上次更新的时间戳 | 是 | 否 | 否 | 否 | 否 |
|\_self | 由系统生成 | 容器的可寻址 URI | 是 | 否 | 否 | 否 | 否 |
|id | 用户可配置 | 用户定义的容器唯一名称 | 是 | 是 | 是 | 是 | 是 |
|indexingPolicy | 用户可配置 | 提供更改索引路径、索引类型和索引模型的功能 | 是 | 否 | 否 | 否 | 是 |
|TimeToLive | 用户可配置 | 提供在设置的时间段后从容器自动删除项的功能 有关详细信息，请参阅[生存时间](time-to-live.md)。 | 是 | 否 | 否 | 否 | 是 |
|changeFeedPolicy | 用户可配置 | 用于读取对容器中的项所做的更改。 有关详细信息，请参阅[更改源](change-feed.md)。 | 是 | 否 | 否 | 否 | 是 |
|uniqueKeyPolicy | 用户可配置 | 用于确保逻辑分区中一个或多个值的唯一性。 有关详细信息，请参阅[唯一键约束](unique-keys.md)。 | 是 | 否 | 否 | 否 | 是 |

### <a name="operations-on-an-azure-cosmos-container"></a>对 Azure Cosmos 容器执行的操作

使用任一 Azure Cosmos API 时，Azure Cosmos 容器支持以下操作：

| 操作 | Azure CLI | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- |
| 枚举数据库中的容器 | 是 | 是 | 是 | 是 | 不可用 | 不可用 |
| 读取容器 | 是 | 是 | 是 | 是 | 不可用 | 不可用 |
| 创建新容器 | 是 | 是 | 是 | 是 | 不可用 | 不可用 |
| 更新容器 | 是 | 是 | 是 | 是 | 不可用 | 不可用 |
| 删除容器 | 是 | 是 | 是 | 是 | 不可用 | 不可用 |

## <a name="azure-cosmos-items"></a>Azure Cosmos 项

根据所用的 API，Azure Cosmos 项可以代表集合中的文档、表中的行，或者图形中的节点或边缘。 下表显示了 API 特定的实体与 Azure Cosmos 项之间的映射：

| Cosmos 实体 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 项 | Document | 行 | Document | 节点或边缘 | 项 |

### <a name="properties-of-an-item"></a>项的属性

每个 Azure Cosmos 项具有以下系统定义的属性。 根据所用的 API，其中的某些属性可能不会直接公开。

| 系统定义的属性 | 由系统生成或用户可配置| 用途 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | 由系统生成 | 项的唯一标识符 | 是 | 否 | 否 | 否 | 否 |
|\_etag | 由系统生成 | 用于乐观并发控制的实体标记 | 是 | 否 | 否 | 否 | 否 |
|\_ts | 由系统生成 | 项上次更新的时间戳 | 是 | 否 | 否 | 否 | 否 |
|\_self | 由系统生成 | 项的可寻址 URI | 是 | 否 | 否 | 否 | 否 |
|id | 任一个 | 逻辑分区中用户定义的唯一名称。 如果用户未指定 ID, 系统会自动生成一个 ID。 | 是 | 是 | 是 | 是 | 是 |
|任意用户定义的属性 | 用户定义 | 用户定义的属性以 API 本机表示形式表示（包括 JSON、BSON 和 CQL） | 是 | 是 | 是 | 是 | 是 |

> [!NOTE]
> `id`属性的唯一性仅在每个逻辑分区中强制执行。 多个文档的`id`属性可以具有不同的分区键值。

### <a name="operations-on-items"></a>对项执行的操作

Azure Cosmos 项支持以下操作。 可以使用任一 Azure Cosmos API 来执行这些操作。

| 操作 | Azure CLI | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- |
| 插入、替换、删除、Upsert、读取 | 否 | 是 | 是 | 是 | 是 | 是 |

## <a name="next-steps"></a>后续步骤

了解以下任务和概念：

* [对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)
* [对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)
* [使用逻辑分区](partition-data.md)
* [对 Azure Cosmos 容器配置 TTL](how-to-time-to-live.md)
* [使用更改源生成被动式应用程序](change-feed.md)
* [对 Azure Cosmos 容器配置唯一键约束](unique-keys.md)
