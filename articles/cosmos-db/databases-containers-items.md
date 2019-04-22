---
title: 使用 Azure Cosmos DB 数据库、容器和项
description: 本文介绍如何创建和使用 Azure Cosmos DB 数据库、容器和项
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 8eaca83b7ea89737a63fe56a18505c8df7e93fdc
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678782"
---
# <a name="work-with-databases-containers-and-items"></a>使用数据库、容器和项

在创建后[Azure Cosmos 帐户](account-overview.md)下你的 Azure 订阅，可以通过来管理数据在你的帐户中创建数据库、 容器和项。 本文介绍以下每个实体：数据库、容器和项。 下图显示 Azure Cosmos 帐户中不同实体的层次结构：

![Azure Cosmos 帐户实体](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Azure Cosmos 数据库

可以在帐户下创建一个或多个 Azure Cosmos 数据库。 数据库是类似于一个命名空间。 它是管理的一组的 Azure Cosmos 容器单元。 下表显示如何将 Azure Cosmos 数据库映射到各种特定于 API 的实体：

| **Azure Cosmos 实体** | **SQL API** | **Cassandra API** | **Azure Cosmos DB 的 API for MongoDB** | **Gremlin API** | **表 API** |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 数据库 | 数据库 | 密钥空间 | 数据库 | 数据库 | NA |

> [!NOTE]
> 使用表 API 帐户时，创建第一个表，默认数据库是自动创建 Azure Cosmos 帐户中。

### <a name="operations-on-an-azure-cosmos-database"></a>对 Azure Cosmos 数据库执行的操作

你可以与交互与 Azure Cosmos Api 的 Azure Cosmos 数据库，如下所示：

| **操作** | **Azure CLI**|**SQL API** | **Cassandra API** | **Azure Cosmos DB 的 API for MongoDB** | **Gremlin API** | **表 API** |
| --- | --- | --- | --- | --- | --- | --- |
|枚举所有数据库| 是 | 是 | 是（将数据库映射至密钥空间） | 是 | NA | NA |
|读取数据库| 是 | 是 | 是（将数据库映射至密钥空间） | 是 | NA | NA |
|创建新的数据库| 是 | 是 | 是（将数据库映射至密钥空间） | 是 | NA | NA |
|更新数据库| 是 | 是 | 是（将数据库映射至密钥空间） | 是 | NA | NA |


## <a name="azure-cosmos-containers"></a>Azure Cosmos 容器

Azure Cosmos 容器是可缩放性预配的吞吐量和存储单元。 容器会进行水平分区，然后在多个区域间复制。 添加到容器的项以及针对容器预配的吞吐量将基于分区键自动分配给一组逻辑分区。 若要了解有关分区和分区键的详细信息，请参阅[这](partition-data.md)一文。 

在创建 Azure Cosmos 容器时，会在以下某个模式中配置吞吐量：

* **专用预配吞吐量**模式：为该容器以独占方式保留在容器上预配的吞吐量，并由 Sla 提供支持。 有关详细信息，请参阅[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。

* **共享预配吞吐量**模式：这些容器与同一数据库 （不包括这些已配置了专用预配的吞吐量的容器） 中的其他容器共享预配的吞吐量。 换而言之，在"共享的吞吐量"的所有容器之间共享上，对数据库的预配的吞吐量。 有关详细信息，请参阅[如何对 Azure Cosmos 数据库配置预配吞吐量](how-to-provision-database-throughput.md)。

无论使用“共享”还是“专用”预配吞吐量模式创建容器，Azure Cosmos 容器都可以灵活缩放。

Azure Cosmos 容器是与架构无关的项容器。 容器中的项可以具备任意架构。 例如，一个表示某人的项，一个表示一辆汽车的项可以置于*同一个容器*。 默认情况下，添加到容器的所有项都会自动编入索引，不需要任何显式索引或架构管理工作。 你可以配置自定义索引行为[索引策略](index-overview.md)在容器中。 

可以设置[生存时间 (TTL)](time-to-live.md)上选定项中的 Azure Cosmos 容器或正常清除从系统中取出这些项对整个容器。 Azure Cosmos DB 会在这些项过期时自动将其删除。 这样还能保证对这些容器执行的查询不会返回固定边界内已过期的项。 若要了解详细信息，请参阅[如何对容器配置 TTL](how-to-time-to-live.md)。

通过使用[更改源](change-feed.md)，可以订阅管理的每个容器的逻辑分区的操作日志。 更改源提供容器上执行的所有更新的日志，以及更新前和更新后的项的映像。 请参阅[如何构建响应式应用程序使用更改源](serverless-computing-database.md)。 通过使用更改源在容器上的策略，还可以配置的保留持续时间的更改源。 

可以注册[存储的过程、 触发器、 用户定义函数 (Udf)](stored-procedures-triggers-udfs.md)并[合并过程](how-to-manage-conflicts.md)与 Azure Cosmos 容器。 

您可以指定[唯一键约束](unique-keys.md)Azure Cosmos 容器上。 通过创建唯一键策略，可确保每个逻辑分区键的一个或多个值的唯一性。 使用唯一键策略创建容器后，它可以避免创建值与唯一键约束指定的值重复的任何新的或更新项。 若要了解详细信息，请参阅[唯一键约束](unique-keys.md)。

Azure Cosmos 容器专用于特定于 API 的实体，如下所示：

| **Azure Cosmos 实体** | **SQL API** | **Cassandra API** | **Azure Cosmos DB 的 API for MongoDB** | **Gremlin API** | **表 API** |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 容器 | 集合 | 表 | 集合 | 图形 | 表 |

### <a name="properties-of-an-azure-cosmos-container"></a>Azure Cosmos 容器的属性

Azure Cosmos 容器包含一组系统定义属性。 根据所选的 API，其中一些属性可能不会直接公开。 下表描述了系统定义的属性的列表：

| **系统定义的属性** | **系统生成的或用户可配置** | **用途** | **SQL API** | **Cassandra API** | **Azure Cosmos DB 的 API for MongoDB** | **Gremlin API** | **表 API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_rid | 系统生成的 | 容器的唯一标识符 | 是 | 否 | 否 | 否 | 否 |
|_etag | 系统生成的 | 用于乐观并发控制的实体标记 | 是 | 否 | 否 | 否 | 否 |
|_ts | 系统生成的 | 容器上次更新的时间戳 | 是 | 否 | 否 | 否 | 否 |
|_self | 系统生成的 | 容器的可寻址 URI | 是 | 否 | 否 | 否 | 否 |
|id | 用户可配置 | 用户定义的容器唯一名称 | 是 | 是 | 是 | 是 | 是 |
|indexingPolicy | 用户可配置 | 提供的功能更改路径的索引、 索引类型和索引模式。 | 是 | 否 | 否 | 否 | 是 |
|TimeToLive | 用户可配置 | 提供在一段时间以后从容器自动删除项的功能。 有关详细信息，请参阅[生存时间](time-to-live.md)一文。 | 是 | 否 | 否 | 否 | 是 |
|changeFeedPolicy | 用户可配置 | 用于读取对容器中的项所做的更改。 有关更多详细信息，请参阅[更改源](change-feed.md)一文。 | 是 | 否 | 否 | 否 | 是 |
|uniqueKeyPolicy | 用户可配置 | 用于确保的逻辑分区内的一个或多个值的唯一性。 有关详细信息，请参阅[唯一键约束](unique-keys.md)一文。 | 是 | 否 | 否 | 否 | 是 |

### <a name="operations-on-an-azure-cosmos-container"></a>对 Azure Cosmos 容器执行的操作

Azure Cosmos 容器支持使用任何 Azure Cosmos API 执行的以下操作。

| **操作** | **Azure CLI** | **SQL API** | **Cassandra API** | **Azure Cosmos DB 的 API for MongoDB** | **Gremlin API** | **表 API** |
| --- | --- | --- | --- | --- | --- | --- |
| 枚举数据库中的容器 | 是 | 是 | 是 | 是 | NA | NA |
| 读取容器 | 是 | 是 | 是 | 是 | NA | NA |
| 新建容器 | 是 | 是 | 是 | 是 | NA | NA |
| 更新容器 | 是 | 是 | 是 | 是 | NA | NA |
| 删除容器 | 是 | 是 | 是 | 是 | NA | NA |

## <a name="azure-cosmos-items"></a>Azure Cosmos 项

根据所选的 API，Azure Cosmos 项可以表示集合中的一个文件、表格中的一行或者图形中的一个节点/边缘。 下表显示特定于 API 的实体和 Azure Cosmos 项之间的映射：

| **Cosmos 实体** | **SQL API** | **Cassandra API** | **Azure Cosmos DB 的 API for MongoDB** | **Gremlin API** | **表 API** |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 项 | 文档 | 行 | 文档 | 节点或边缘 | Item |

### <a name="properties-of-an-item"></a>项的属性

每个 Azure Cosmos 项都具有以下系统定义的属性。 根据所选的 API，其中一些属性可能不会直接公开。

|**系统定义的属性** | **系统生成的或用户可配置**| **用途** | **SQL API** | **Cassandra API** | **Azure Cosmos DB 的 API for MongoDB** | **Gremlin API** | **表 API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_id | 系统生成的 | 项的唯一标识符 | 是 | 否 | 否 | 否 | 否 |
|_etag | 系统生成的 | 用于乐观并发控制的实体标记 | 是 | 否 | 否 | 否 | 否 |
|_ts | 系统生成的 | 项的上次更新时间戳 | 是 | 否 | 否 | 否 | 否 |
|_self | 系统生成的 | 项的可寻址 URI | 是 | 否 | 否 | 否 | 否 |
|id | 任一个 | 逻辑分区内用户定义的唯一名称。 如果用户未指定 ID，系统将自动生成一个。 | 是 | 是 | 是 | 是 | 是 |
|任意用户定义的属性 | 用户定义 | 用户定义的属性以 API 本机表示形式表示（JSON、BSON、CQL 等等） | 是 | 是 | 是 | 是 | 是 |

### <a name="operations-on-items"></a>对项执行的操作

Azure Cosmos 项支持可使用任何 Azure Cosmos API 执行的以下操作。

| **操作** | **Azure CLI** | **SQL API** | **Cassandra API** | **Azure Cosmos DB 的 API for MongoDB** | **Gremlin API** | **表 API** |
| --- | --- | --- | --- | --- | --- | --- |
| 插入、替换、删除、Upsert、读取 | 否 | 是 | 是 | 是 | 是 | 是 |

## <a name="next-steps"></a>后续步骤

现在可以继续了解以下概念：

* [如何在 Azure Cosmos 数据库配置预配的吞吐量](how-to-provision-database-throughput.md)
* [如何在 Azure Cosmos 容器配置预配的吞吐量](how-to-provision-container-throughput.md)
* [逻辑分区](partition-data.md)
* [如何对 Azure Cosmos 容器配置 TTL](how-to-time-to-live.md)
* [如何使用更改源生成响应式应用程序](change-feed.md)
* [如何对 Azure Cosmos 容器配置唯一键约束](unique-keys.md)
