---
title: Azure Cosmos DB 的 Azure 资源管理器模板
description: 使用 Azure 资源管理器模板创建和配置 Azure Cosmos DB。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: ecdfe89e899e0d416784ae32a0d66b335c09e2b6
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582811"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB 的 Azure 资源管理器模板

下表包含 Azure Cosmos DB 的 Azure 资源管理器模板链接：

## <a name="sql-core-api"></a>SQL（核心）API

|**模板**|**说明**|
|---|---|
|[创建 Azure Cosmos 帐户、数据库、容器](manage-sql-with-resource-manager.md#create-resource) | 此模板在两个区域中创建 SQL （核心） API 帐户，两个区域具有共享数据库吞吐量和具有专用吞吐量的容器。 可以通过使用更新的吞吐量属性值重新提交模板来更新吞吐量。 |
|[使用存储过程、触发器和 UDF 创建 Azure Cosmos 帐户、数据库和容器](manage-sql-with-resource-manager.md#create-sproc) | 此模板使用容器的存储过程、触发器和 UDF 在两个区域中创建一个 SQL （核心） API 帐户。 |

## <a name="mongodb-api"></a>MongoDB API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户、数据库、集合](manage-mongodb-with-resource-manager.md#create-resource) | 此模板使用 Azure Cosmos DB API for MongoDB 在两个区域创建一个启用了多主数据库的帐户。 Azure Cosmos 帐户会有两个共享数据库级吞吐量的容器。 |
|[更新数据库的吞吐量（RU/秒）](manage-mongodb-with-resource-manager.md#database-ru-update) | 此模板更新 MongoDB API 帐户中数据库的吞吐量。 |
|[更新集合的吞吐量（RU/秒）](manage-mongodb-with-resource-manager.md#collection-ru-update) | 此模板更新 MongoDB API 帐户中容器的吞吐量。 |

## <a name="cassandra-api"></a>Cassandra API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户、密钥空间、表](manage-cassandra-with-resource-manager.md#create-resource) | 此模板在两个区域创建一个启用了多主数据库的 Cassandra API 帐户。 Azure Cosmos 帐户会有两个共享密钥空间级吞吐量的表。 |
|[更新密钥空间的吞吐量（RU/秒）](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | 此模板更新 Cassandra API 帐户中密钥空间的吞吐量。 |
|[更新表的吞吐量（RU/秒）](manage-cassandra-with-resource-manager.md#table-ru-update) | 此模板更新 Cassandra API 帐户中表的吞吐量。 |

## <a name="gremlin-api"></a>Gremlin API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户、数据库、图形](manage-gremlin-with-resource-manager.md#create-resource) | 此模板在两个区域创建一个启用了多主数据库的 Gremlin API 帐户。 Azure Cosmos 帐户会有两个共享数据库级吞吐量的图形。 |
|[更新数据库的吞吐量（RU/秒）](manage-gremlin-with-resource-manager.md#database-ru-update) | 此模板更新 Gremlin API 帐户中数据库的吞吐量。 |
|[更新图形的吞吐量（RU/秒）](manage-gremlin-with-resource-manager.md#graph-ru-update) | 此模板更新 Gremlin API 帐户中图形的吞吐量。 |

## <a name="table-api"></a>表 API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户、表](manage-table-with-resource-manager.md#create-resource) | 此模板在两个区域创建一个启用了多主数据库的表 API 帐户。 Azure Cosmos 帐户会有一个表。 |
|[更新表的吞吐量（RU/秒）](manage-table-with-resource-manager.md#table-ru-update) | 此模板更新表 API 帐户中表的吞吐量。 |

> [!TIP]
> 若要在使用表 API 时启用共享吞吐量，请在 Azure 门户中启用帐户级吞吐量。

有关参考文档，请参阅 Azure Cosmos DB 页面的[ARM 参考](/azure/templates/microsoft.documentdb/allversions)。
