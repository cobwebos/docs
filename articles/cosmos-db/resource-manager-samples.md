---
title: Azure Cosmos DB 的 Azure 资源管理器模板
description: 使用 Azure 资源管理器模板创建和配置 Azure Cosmos DB。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 4a32b0497d2457a740e9c082f990bb9112208bfd
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969179"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB 的 Azure 资源管理器模板

下表包括适用于 Azure Cosmos DB 的 Azure 资源管理器模板链接：

## <a name="sql-core-api"></a>SQL（核心）API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户、 数据库、 容器](manage-sql-with-resource-manager.md#create-resource) | 此模板创建 SQL (Core) API 帐户在两个区域通过多主启用。 Azure Cosmos 帐户会有两个共享数据库级吞吐量的容器。 |
|[更新数据库的吞吐量 (RU/s)](manage-sql-with-resource-manager.md#database-ru-update) | 此模板更新 (Core) SQL API 帐户中的数据库的吞吐量。 |
|[更新容器的吞吐量 (RU/s)](manage-sql-with-resource-manager.md#container-ru-update) | 此模板更新 (Core) SQL API 帐户中的容器的吞吐量。 |

## <a name="mongodb-api"></a>MongoDB API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户、 数据库、 集合](manage-mongodb-with-resource-manager.md#create-resource) | 此模板创建使用多主机启用 Azure Cosmos DB API for MongoDB 中的两个区域的帐户。 Azure Cosmos 帐户会有两个共享数据库级吞吐量的容器。 |
|[更新数据库的吞吐量 (RU/s)](manage-mongodb-with-resource-manager.md#database-ru-update) | 此模板更新数据库中的 MongoDB API 帐户的吞吐量。 |
|[更新集合的吞吐量 (RU/s)](manage-mongodb-with-resource-manager.md#collection-ru-update) | 此模板更新为 MongoDB API 帐户中的容器的吞吐量。 |

## <a name="cassandra-api"></a>Cassandra API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户、 密钥空间、 表](manage-cassandra-with-resource-manager.md#create-resource) | 此模板在两个区域创建一个启用了多主数据库的 Cassandra API 帐户。 Azure Cosmos 帐户会有两个共享密钥空间级吞吐量的表。 |
|[更新密钥空间的吞吐量 (RU/s)](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | 此模板更新 keyspace Cassandra API 帐户中的吞吐量。 |
|[更新表的吞吐量 (RU/s)](manage-cassandra-with-resource-manager.md#table-ru-update) | 此模板更新 Cassandra API 帐户中的表的吞吐量。 |

## <a name="gremlin-api"></a>Gremlin API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户、 数据库、 关系图](manage-gremlin-with-resource-manager.md#create-resource) | 此模板在两个区域创建一个启用了多主数据库的 Gremlin API 帐户。 Azure Cosmos 帐户会有两个共享数据库级吞吐量的图形。 |
|[更新数据库的吞吐量 (RU/s)](manage-gremlin-with-resource-manager.md#database-ru-update) | 此模板更新数据库中的 Gremlin API 帐户的吞吐量。 |
|[更新图形的吞吐量 (RU/s)](manage-gremlin-with-resource-manager.md#graph-ru-update) | 此模板更新的图表中的 Gremlin API 帐户的吞吐量。 |

## <a name="table-api"></a>表 API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户、 表](manage-table-with-resource-manager.md#create-resource) | 此模板在两个区域创建一个启用了多主数据库的表 API 帐户。 Azure Cosmos 帐户会有一个表。 |
|[更新表的吞吐量 (RU/s)](manage-table-with-resource-manager.md#table-ru-update) | 此模板更新表 API 帐户中的表的吞吐量。 |

> [!TIP]
> 若要在使用表 API 时启用共享吞吐量，请在 Azure 门户中启用帐户级吞吐量。

请参阅[适用于 Azure Cosmos DB 的 ARM 引用](/azure/templates/microsoft.documentdb/allversions)的参考文档的页。