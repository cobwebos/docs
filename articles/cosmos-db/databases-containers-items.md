---
title: 使用数据库、 容器和 Azure Cosmos DB 中的项
description: 本文介绍如何创建和使用 Azure Cosmos DB 中的数据库、 容器和项。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 7d607b4370d51ea2605fae6543bd3336853b0806
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954222"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>使用数据库、 容器和 Azure Cosmos DB 中的项

在 Azure 订阅下创建 [Azure Cosmos DB 帐户](account-overview.md)后，可以通过创建数据库、容器和项来管理帐户中的数据。 本指南介绍了上述每个实体。 

下图显示 Azure Cosmos DB 帐户中的不同实体的层次结构：

![Azure Cosmos 帐户实体](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Azure Cosmos 数据库

你的帐户，可以创建一个或多个 Azure Cosmos 数据库。 数据库类似于命名空间， 数据库是管理的一组的 Azure Cosmos 容器的单位。 下表显示如何将 Azure Cosmos 数据库映射到各种特定于 API 的实体：

| Azure Cosmos 实体 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 数据库 | 数据库 | 密钥空间 | 数据库 | 数据库 | NA |

> [!NOTE]
> 使用表 API 帐户时，创建第一个表，默认数据库是自动创建 Azure Cosmos 帐户中。

### <a name="operations-on-an-azure-cosmos-database"></a>对 Azure Cosmos 数据库执行的操作

下表中所述，可以使用 Azure Cosmos 数据库与 Azure Cosmos Api 中进行交互：

| Operation | Azure CLI | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- |
|枚举所有数据库| “是” | “是” | 是（将数据库映射至密钥空间） | “是” | NA | NA |
|读取数据库| “是” | “是” | 是（将数据库映射至密钥空间） | “是” | NA | NA |
|新建数据库| “是” | “是” | 是（将数据库映射至密钥空间） | “是” | NA | NA |
|更新数据库| “是” | “是” | 是（将数据库映射至密钥空间） | “是” | NA | NA |


## <a name="azure-cosmos-containers"></a>Azure Cosmos 容器

Azure Cosmos 容器是针对预配的吞吐量和存储可伸缩性的单位。 容器会进行水平分区，然后在多个区域间复制。 添加到容器的项以及针对容器预配的吞吐量将基于分区键自动分配给一组逻辑分区。 若要了解有关分区和分区键的详细信息，请参阅[将数据分区](partition-data.md)。 

创建 Azure Cosmos 容器时，以下模式之一中配置的吞吐量：

* **专用预配的吞吐量模式**:针对容器预配的吞吐量是专门为该容器保留的，由 SLA 提供支持。 若要了解详细信息，请参阅[如何预配 Azure Cosmos 容器的吞吐量](how-to-provision-container-throughput.md)。

* **共享预配的吞吐量模式**:这些容器与同一数据库 （不包括已配置了专用预配的吞吐量的容器） 中的其他容器共享预配的吞吐量。 换而言之，在"共享的吞吐量"的所有容器之间共享上，对数据库的预配的吞吐量。 若要了解详细信息，请参阅[如何预配 Azure Cosmos 数据库上的吞吐量](how-to-provision-database-throughput.md)。

是否使用专用或共享预配的吞吐量模式创建容器，可以弹性，缩放 Azure Cosmos 容器。

Azure Cosmos 容器是与架构无关的项容器。 在容器中的项可以具有任意的数据库架构。 例如，在放置表示人员的项和项，它代表汽车*同一个容器*。 默认情况下，会自动创建向容器添加的所有项都索引而无需显式索引或架构管理。 通过在容器上配置的[索引策略](index-overview.md)，可以自定义索引行为。 

可以设置[生存时间 (TTL)](time-to-live.md)上选定项中的 Azure Cosmos 容器或以适当地从系统中清除这些项对整个容器。 过期时，azure Cosmos DB 会自动删除这些项。 它还保证对容器执行的查询不会返回固定的绑定中的过期的项。 若要了解详细信息，请参阅[配置在容器上的 TTL](how-to-time-to-live.md)。

可以使用[更改源](change-feed.md)订阅管理你的容器的每个逻辑分区的操作日志。 更改源提供对容器，以及执行的所有更新的日志之前和之后的项的图像。 有关详细信息，请参阅[构建响应式应用程序通过使用更改源](serverless-computing-database.md)。 此外可以配置的更改源通过使用更改源在容器上的策略的保留持续时间。 

可以注册[存储的过程、 触发器、 用户定义函数 (Udf)](stored-procedures-triggers-udfs.md)，并[合并过程](how-to-manage-conflicts.md)为 Azure Cosmos 容器。 

可以在 Azure Cosmos 容器上指定一个[唯一键约束](unique-keys.md)。 通过创建唯一键策略，可确保每个逻辑分区键的一个或多个值的唯一性。 如果使用唯一键策略创建一个容器，可以不创建任何新的或更新项使用重复的唯一键约束指定的值的值。 若要了解详细信息，请参阅[唯一键约束](unique-keys.md)。

下表中所示，Azure Cosmos 容器被专用到特定于 API 的实体：

| Azure Cosmos 实体 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 容器 | 集合 | 表 | 集合 | 图形 | 表 |

### <a name="properties-of-an-azure-cosmos-container"></a>Azure Cosmos 容器的属性

Azure Cosmos 容器具备一组系统定义的属性。 具体取决于您使用哪个 API，某些属性可能不会直接公开。 下表介绍了系统定义属性的列表：

| 系统定义的属性 | 由系统生成或用户可配置 | 目的 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | System-generated | 容器的唯一标识符 | “是” | 否 | 否 | 否 | “否” |
|\_etag | System-generated | 用于乐观并发控制的实体标记 | “是” | 否 | 否 | 否 | “否” |
|\_ts | System-generated | 容器上次更新的时间戳 | “是” | 否 | 否 | 否 | “否” |
|\_self | System-generated | 容器的可寻址 URI | “是” | 否 | 否 | 否 | “否” |
|ID | 用户可配置 | 用户定义的容器唯一名称 | “是” | 是 | 是 | 是 | “是” |
|indexingPolicy | 用户可配置 | 提供的功能更改路径的索引、 索引类型和索引模式 | “是” | 否 | 否 | 否 | “是” |
|TimeToLive | 用户可配置 | 提供的功能以设定的时间段后从容器中自动删除项。 有关详细信息，请参阅[生存时间](time-to-live.md)。 | “是” | 否 | 否 | 否 | “是” |
|changeFeedPolicy | 用户可配置 | 用于读取对容器中的项所做的更改。 有关详细信息，请参阅[更改源](change-feed.md)。 | “是” | 否 | 否 | 否 | “是” |
|uniqueKeyPolicy | 用户可配置 | 用于确保的逻辑分区中的一个或多个值的唯一性。 有关详细信息，请参阅[唯一键约束](unique-keys.md)。 | “是” | 否 | 否 | 否 | “是” |

### <a name="operations-on-an-azure-cosmos-container"></a>对 Azure Cosmos 容器执行的操作

使用任何 Azure Cosmos Api 时，Azure Cosmos 容器支持以下操作：

| Operation | Azure CLI | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- |
| 枚举数据库中的容器 | “是” | 是 | 是 | “是” | NA | NA |
| 读取容器 | “是” | 是 | 是 | “是” | NA | NA |
| 创建新容器 | “是” | 是 | 是 | “是” | NA | NA |
| 更新容器 | “是” | 是 | 是 | “是” | NA | NA |
| 删除容器 | “是” | 是 | 是 | “是” | NA | NA |

## <a name="azure-cosmos-items"></a>Azure Cosmos 项

具体取决于您使用哪个 API，Azure Cosmos 项可以表示集合，一个表，或节点中的行中的文档，或者在图形中的边缘。 下表显示了为 Azure Cosmos 项特定于 API 的实体的映射：

| Cosmos 实体 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 项 | 文档 | 行 | 文档 | 节点或边界 | 项 |

### <a name="properties-of-an-item"></a>项的属性

每个 Azure Cosmos 项具有以下系统定义属性。 具体取决于您使用哪个 API，其中一些可能不会直接公开。

| 系统定义的属性 | 由系统生成或用户可配置| 目的 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | System-generated | 项的唯一标识符 | “是” | 否 | 否 | 否 | “否” |
|\_etag | System-generated | 用于乐观并发控制的实体标记 | “是” | 否 | 否 | 否 | “否” |
|\_ts | System-generated | 项的上次更新时间戳 | “是” | 否 | 否 | 否 | “否” |
|\_self | System-generated | 项的可寻址 URI | “是” | 否 | 否 | 否 | “否” |
|ID | 任一个 | 用户定义的逻辑分区中唯一的名称。 如果用户未指定 ID，系统会自动生成一个密钥。 | “是” | 是 | 是 | 是 | “是” |
|任意用户定义的属性 | 用户定义 | 在 API 的本机表示形式 （包括 JSON、 BSON 和 CQL） 中表示的用户定义的属性 | “是” | 是 | 是 | 是 | “是” |

### <a name="operations-on-items"></a>对项执行的操作

Azure Cosmos 项支持以下操作。 可以使用任何 Azure Cosmos Api 来执行操作。

| Operation | Azure CLI | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- |
| 插入、替换、删除、Upsert、读取 | “否” | 是 | 是 | 是 | 是 | “是” |

## <a name="next-steps"></a>后续步骤

了解有关这些任务和概念：

* [Azure Cosmos 数据库上的预配吞吐量](how-to-provision-database-throughput.md)
* [Azure Cosmos 容器上预配吞吐量](how-to-provision-container-throughput.md)
* [带逻辑分区工作](partition-data.md)
* [在 Azure Cosmos 容器配置 TTL](how-to-time-to-live.md)
* [使用更改源生成响应式应用程序](change-feed.md)
* [配置 Azure Cosmos 容器上的唯一键约束](unique-keys.md)
