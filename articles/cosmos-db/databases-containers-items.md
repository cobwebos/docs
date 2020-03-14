---
title: 使用 Azure Cosmos DB 中的数据库、容器和项
description: 本文介绍如何创建和使用 Azure Cosmos DB 中的数据库、容器和项。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 43a842c3b6d6d421eca4196c7f3facc7876318cd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246781"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的数据库、容器和项

在 Azure 订阅下创建 [Azure Cosmos DB 帐户](account-overview.md)后，可以通过创建数据库、容器和项来管理帐户中的数据。 本文介绍上述每个条目。 

下图显示了 Azure Cosmos DB 帐户中不同实体的层次结构：

![Azure Cosmos 帐户实体](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Azure Cosmos 数据库

可以在帐户下创建一个或多个 Azure Cosmos 数据库。 数据库类似于命名空间。 数据库是一组 Azure Cosmos 容器的管理单元。 下表显示如何将 Azure Cosmos 数据库映射到各种特定于 API 的实体：

| Azure Cosmos 实体 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 数据库 | 数据库 | 密钥空间 | 数据库 | 数据库 | NA |

> [!NOTE]
> 使用表 API 帐户时，当你创建第一个表时，将自动在你的 Azure Cosmos 帐户中创建一个默认数据库。

### <a name="operations-on-an-azure-cosmos-database"></a>对 Azure Cosmos 数据库执行的操作

可以使用 Azure Cosmos Api 与 Azure Cosmos 数据库进行交互，如下表所述：

| Operation | Azure CLI | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- |
|枚举所有数据库| 是 | 是 | 是（将数据库映射至密钥空间） | 是 | NA | NA |
|读取数据库| 是 | 是 | 是（将数据库映射至密钥空间） | 是 | NA | NA |
|创建新的数据库| 是 | 是 | 是（将数据库映射至密钥空间） | 是 | NA | NA |
|更新数据库| 是 | 是 | 是（将数据库映射至密钥空间） | 是 | NA | NA |


## <a name="azure-cosmos-containers"></a>Azure Cosmos 容器

Azure Cosmos 容器是预配吞吐量和存储的可伸缩性单元。 容器会进行水平分区，然后在多个区域间复制。 添加到容器的项以及针对容器预配的吞吐量将基于分区键自动分配给一组逻辑分区。 若要了解有关分区和分区键的详细信息，请参阅[分区数据](partition-data.md)。 

创建 Azure Cosmos 容器时，可以采用以下模式之一配置吞吐量：

* **专用预配的吞吐量模式**：容器上预配的吞吐量专为该容器保留，并且由 sla 提供支持。 若要了解详细信息，请参阅[如何在 Azure Cosmos 容器上预配吞吐量](how-to-provision-container-throughput.md)。

* **共享预配的吞吐量模式**：这些容器与同一数据库中的其他容器共享预配的吞吐量（不包括已配置专用预配吞吐量的容器）。 换句话说，在所有 "共享吞吐量" 容器之间共享数据库的预配吞吐量。 若要了解详细信息，请参阅[如何在 Azure Cosmos 数据库上预配吞吐量](how-to-provision-database-throughput.md)。

> [!NOTE]
> 仅在创建数据库和容器时，才能配置共享和专用吞吐量。 在创建容器后，若要从专用吞吐量模式切换到共享吞吐量模式（以及反过来进行操作），必须创建一个新容器，然后将数据迁移到新容器。 你可以使用 Azure Cosmos DB 更改源功能迁移数据。

无论是使用专用还是共享预配的吞吐量模式创建容器，Azure Cosmos 容器都可以缩放弹性。

Azure Cosmos 容器是与架构无关的项容器。 容器中的项可以包含任意架构。 例如，表示人员的项和表示汽车的项可以放置在*同一个容器*中。 默认情况下，你添加到容器中的所有项都将自动编制索引，而无需显式索引或架构管理。 可以通过配置容器上的[索引策略](index-overview.md)来自定义索引行为。 

可以在 Azure Cosmos 容器中的选定项上设置[生存时间（TTL）](time-to-live.md) ，也可以为整个容器设置从系统中正常清除这些项的时间。 当项过期时，Azure Cosmos DB 会自动删除这些项。 它还保证在容器中执行的查询不会返回固定边界内的过期项。 若要了解详细信息，请参阅在[容器中配置 TTL](how-to-time-to-live.md)。

可以使用 "[更改源](change-feed.md)" 订阅为容器的每个逻辑分区管理的操作日志。 更改源提供对容器执行的所有更新以及项目的 "之前" 和 "之后" 图像的日志。 有关详细信息，请参阅[使用更改源构建反应应用程序](serverless-computing-database.md)。 还可以通过使用容器上的更改源策略来配置更改源的保留期。 

可以注册 Azure Cosmos 容器的[存储过程、触发器、用户定义的函数（udf）](stored-procedures-triggers-udfs.md)和[合并过程](how-to-manage-conflicts.md)。 

可以在 Azure Cosmos 容器上指定[唯一键约束](unique-keys.md)。 通过创建唯一键策略，可确保每个逻辑分区键的一个或多个值的唯一性。 如果使用唯一键策略创建容器，则不能创建具有与 unique key 约束指定的值重复的新项或更新项。 若要了解详细信息，请参阅[唯一键约束](unique-keys.md)。

Azure Cosmos 容器专用于特定于 API 的实体，如下表所示：

| Azure Cosmos 实体 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 容器 | 容器 | 表 | 集合 | 图形 | 表 |

### <a name="properties-of-an-azure-cosmos-container"></a>Azure Cosmos 容器的属性

Azure Cosmos 容器包含一组系统定义的属性。 根据所使用的 API，某些属性可能不会直接公开。 下表描述了系统定义的属性的列表：

| 系统定义的属性 | 系统生成的或用户可配置的 | 目的 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | 系统生成 | 容器的唯一标识符 | 是 | 否 | 否 | 否 | 否 |
|\_etag | 系统生成 | 用于乐观并发控制的实体标记 | 是 | 否 | 否 | 否 | 否 |
|\_ts | 系统生成 | 容器上次更新的时间戳 | 是 | 否 | 否 | 否 | 否 |
|\_self | 系统生成 | 容器的可寻址 URI | 是 | 否 | 否 | 否 | 否 |
|id | 用户可配置 | 用户定义的容器唯一名称 | 是 | 是 | 是 | 是 | 是 |
|indexingPolicy | 用户可配置 | 提供更改索引路径、索引类型和索引模式的功能 | 是 | 否 | 否 | 否 | 是 |
|timeToLive | 用户可配置 | 提供在设置的时间段后从容器自动删除项的功能。 有关详细信息，请参阅[生存时间](time-to-live.md)。 | 是 | 否 | 否 | 否 | 是 |
|changeFeedPolicy | 用户可配置 | 用于读取对容器中的项所做的更改。 有关详细信息，请参阅[更改源](change-feed.md)。 | 是 | 否 | 否 | 否 | 是 |
|uniqueKeyPolicy | 用户可配置 | 用于确保逻辑分区中一个或多个值的唯一性。 有关详细信息，请参阅[Unique key 约束](unique-keys.md)。 | 是 | 否 | 否 | 否 | 是 |

### <a name="operations-on-an-azure-cosmos-container"></a>对 Azure Cosmos 容器执行的操作

使用任何 Azure Cosmos Api 时，Azure Cosmos 容器支持以下操作：

| Operation | Azure CLI | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- |
| 枚举数据库中的容器 | 是 | 是 | 是 | 是 | NA | NA |
| 读取容器 | 是 | 是 | 是 | 是 | NA | NA |
| 创建新容器 | 是 | 是 | 是 | 是 | NA | NA |
| 更新容器 | 是 | 是 | 是 | 是 | NA | NA |
| 删除容器 | 是 | 是 | 是 | 是 | NA | NA |

## <a name="azure-cosmos-items"></a>Azure Cosmos 项

根据所使用的 API，Azure Cosmos 项可以代表集合中的文档、表中的行或图形中的节点或边缘。 下表显示了 API 特定的实体到 Azure Cosmos 项的映射：

| Cosmos 实体 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 项 | Document | 行 | Document | 节点或边缘 | Item |

### <a name="properties-of-an-item"></a>项的属性

每个 Azure Cosmos 项都具有以下系统定义的属性。 根据所使用的 API，其中一些可能无法直接公开。

| 系统定义的属性 | 系统生成的或用户可配置的| 目的 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | 系统生成 | 项的唯一标识符 | 是 | 否 | 否 | 否 | 否 |
|\_etag | 系统生成 | 用于乐观并发控制的实体标记 | 是 | 否 | 否 | 否 | 否 |
|\_ts | 系统生成 | 项的上一次更新的时间戳 | 是 | 否 | 否 | 否 | 否 |
|\_self | 系统生成 | 项的可寻址 URI | 是 | 否 | 否 | 否 | 否 |
|id | 之前或之后 | 逻辑分区中用户定义的唯一名称。 | 是 | 是 | 是 | 是 | 是 |
|任意用户定义的属性 | 用户定义 | 以 API 本机表示形式表示的用户定义属性（包括 JSON、BSON 和 CQL） | 是 | 是 | 是 | 是 | 是 |

> [!NOTE]
> 仅在每个逻辑分区中强制执行 `id` 属性的唯一性。 多个文档可以具有相同的 `id` 属性和不同的分区键值。

### <a name="operations-on-items"></a>对项执行的操作

Azure Cosmos 项支持以下操作。 可以使用任何 Azure Cosmos Api 执行操作。

| Operation | Azure CLI | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- |
| 插入、替换、删除、Upsert、读取 | 否 | 是 | 是 | 是 | 是 | 是 |

## <a name="next-steps"></a>后续步骤

了解以下任务和概念：

* [在 Azure Cosmos 数据库上预配吞吐量](how-to-provision-database-throughput.md)
* [在 Azure Cosmos 容器上预配吞吐量](how-to-provision-container-throughput.md)
* [使用逻辑分区](partition-data.md)
* [在 Azure Cosmos 容器上配置 TTL](how-to-time-to-live.md)
* [使用更改源构建反应应用程序](change-feed.md)
* [在 Azure Cosmos 容器上配置唯一键约束](unique-keys.md)
