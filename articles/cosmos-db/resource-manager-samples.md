---
title: 适用于 Azure Cosmos DB 的 Azure 资源管理器模板
description: 使用 Azure 资源管理器模板创建和配置 Azure Cosmos DB。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mjbrown
ms.openlocfilehash: 7861196daf3b04430200096a0d42bdcc6e6d5cf9
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053226"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>适用于 Azure Cosmos DB 的 Azure 资源管理器模板

下表包含 Azure Cosmos DB 的 Azure 资源管理器模板的链接：

## <a name="sql-core-api"></a>SQL（核心）API

|**模板**|**说明**|
|---|---|
|[创建 Azure Cosmos 帐户、数据库、容器](manage-sql-with-resource-manager.md#create-resource) | 此模板在启用了多主机的两个区域中创建 SQL （Core） API 帐户。 Azure Cosmos 帐户将有两个共享数据库级吞吐量的容器。 |
|[使用存储过程、触发器和 UDF 创建 Azure Cosmos 帐户、数据库和容器](manage-sql-with-resource-manager.md#create-sproc) | 此模板使用容器的存储过程、触发器和 UDF 在两个区域中创建一个 SQL （核心） API 帐户。 |
|[更新数据库的吞吐量（RU/s）](manage-sql-with-resource-manager.md#database-ru-update) | 此模板更新 SQL （核心） API 帐户中数据库的吞吐量。 |
|[更新容器的吞吐量（RU/s）](manage-sql-with-resource-manager.md#container-ru-update) | 此模板更新 SQL （核心） API 帐户中的容器的吞吐量。 |

## <a name="mongodb-api"></a>MongoDB API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户、数据库、集合](manage-mongodb-with-resource-manager.md#create-resource) | 此模板使用已启用多主机的两个区域中的 MongoDB Azure Cosmos DB API 创建帐户。 Azure Cosmos 帐户将有两个共享数据库级吞吐量的容器。 |
|[更新数据库的吞吐量（RU/s）](manage-mongodb-with-resource-manager.md#database-ru-update) | 此模板更新 MongoDB API 帐户中数据库的吞吐量。 |
|[更新集合的吞吐量（RU/s）](manage-mongodb-with-resource-manager.md#collection-ru-update) | 此模板更新 MongoDB API 帐户中的容器的吞吐量。 |

## <a name="cassandra-api"></a>Cassandra API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户，密钥空间，表](manage-cassandra-with-resource-manager.md#create-resource) | 此模板在启用了多主机的两个区域中创建 Cassandra API 帐户。 Azure Cosmos 帐户将有两个表共享密钥空间级别的吞吐量。 |
|[更新密钥空间的吞吐量（RU/秒）](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | 此模板更新 Cassandra API 帐户中密钥空间的吞吐量。 |
|[更新表的吞吐量（RU/s）](manage-cassandra-with-resource-manager.md#table-ru-update) | 此模板更新 Cassandra API 帐户中的表的吞吐量。 |

## <a name="gremlin-api"></a>Gremlin API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户、数据库、图形](manage-gremlin-with-resource-manager.md#create-resource) | 此模板在启用了多主机的两个区域中创建 Gremlin API 帐户。 Azure Cosmos 帐户将具有两个共享数据库级吞吐量的关系图。 |
|[更新数据库的吞吐量（RU/s）](manage-gremlin-with-resource-manager.md#database-ru-update) | 此模板更新 Gremlin API 帐户中数据库的吞吐量。 |
|[更新图形的吞吐量（RU/s）](manage-gremlin-with-resource-manager.md#graph-ru-update) | 此模板更新 Gremlin API 帐户中图形的吞吐量。 |

## <a name="table-api"></a>表 API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户，表](manage-table-with-resource-manager.md#create-resource) | 此模板在启用了多主机的两个区域中创建表 API 帐户。 Azure Cosmos 帐户将包含一个表。 |
|[更新表的吞吐量（RU/s）](manage-table-with-resource-manager.md#table-ru-update) | 此模板更新表 API 帐户中的表的吞吐量。 |

> [!TIP]
> 若要在使用表 API 时启用共享吞吐量，请在 Azure 门户中启用帐户级吞吐量。

有关参考文档，请参阅 Azure Cosmos DB 页面的[ARM 参考](/azure/templates/microsoft.documentdb/allversions)。
