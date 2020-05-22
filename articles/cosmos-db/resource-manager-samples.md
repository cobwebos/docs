---
title: 用于 Azure Cosmos DB 的 Azure 资源管理器模板
description: 使用 Azure 资源管理器模板创建和配置 Azure Cosmos DB。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: mjbrown
ms.openlocfilehash: a98aee61c3a330344b5b7fa0a5f7f53a65e5ecdb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586144"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>用于 Azure Cosmos DB 的 Azure 资源管理器模板

下表包含用于 Azure Cosmos DB 的 Azure 资源管理器模板链接：

## <a name="core-sql-api"></a>Core (SQL) API

|**模板**|**说明**|
|---|---|
|[创建具有自动缩放吞吐量的 Azure Cosmos 帐户、数据库和容器](manage-sql-with-resource-manager.md#create-autoscale) | 此模板在两个区域（具有自动缩放吞吐量的数据库和容器）中创建一个 Core (SQL) API 帐户。 |
|[创建具有分析存储的 Azure Cosmos 帐户、数据库和容器](manage-sql-with-resource-manager.md#create-analytical-store) | 此模板在一个区域中创建一个 Core (SQL) API 帐户，其中包含配置了启用分析 TTL 的容器和手动或自动缩放吞吐量选项。 |
|[创建具有标准（手动）吞吐量的 Azure Cosmos 帐户、数据库和容器](manage-sql-with-resource-manager.md#create-manual) | 此模板在两个区域（具有标准吞吐量的数据库和容器）中创建一个 Core (SQL) API 帐户。 |
|[创建包含存储过程、触发器和 UDF 的 Azure Cosmos 帐户、数据库和容器](manage-sql-with-resource-manager.md#create-sproc) | 此模板在两个区域（具有存储过程、触发器和容器的 UDF）中创建了一个 Core (SQL) API 帐户。 |
|[为现有的 Azure Cosmos 帐户创建专用终结点](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  此模板为现有虚拟网络中的现有 Azure Cosmos Core (SQL) API 帐户创建专用终结点。 |
|[创建免费层 Azure Cosmos 帐户](manage-sql-with-resource-manager.md#free-tier) |  此模板在免费层上创建 Azure Cosmos DB Core (SQL) API 帐户。 |

## <a name="mongodb-api"></a>MongoDB API

|**模板**|**说明**|
|---| ---|
|[创建具有自动缩放吞吐量的 Azure Cosmos 帐户、数据库和集合](manage-mongodb-with-resource-manager.md#create-autoscale) | 此模板在两个区域（具有两个共享数据库级自动缩放吞吐量的容器）中使用 Azure Cosmos DB API for MongoDB 创建一个帐户。 |
|[创建具有标准（手动）吞吐量的 Azure Cosmos 帐户、数据库和集合](manage-mongodb-with-resource-manager.md#create-manual) | 此模板在两个区域（具有两个共享数据库级标准吞吐量的容器）中使用 Azure Cosmos DB API for MongoDB 创建一个帐户。 |

## <a name="cassandra-api"></a>Cassandra API

|**模板**|**说明**|
|---| ---|
|[创建具有自动缩放吞吐量的 Azure Cosmos 帐户、密钥空间和表](manage-cassandra-with-resource-manager.md#create-autoscale) | 此模板在两个区域（具有自动缩放吞吐量的密钥空间和表）中创建一个 Cassandra API 帐户。 |
|[创建具有标准（手动）吞吐量的 Azure Cosmos 帐户、密钥空间和表](manage-cassandra-with-resource-manager.md#create-manual) | 此模板在两个区域（具有手动吞吐量的密钥空间和表）中创建一个 Cassandra API 帐户。 |

## <a name="gremlin-api"></a>Gremlin API

|**模板**|**说明**|
|---| ---|
|[创建具有自动缩放吞吐量的 Azure Cosmos 帐户、数据库和图](manage-gremlin-with-resource-manager.md#create-autoscale) | 此模板在两个区域（具有自动缩放吞吐量的数据库和图）中创建一个 Gremlin API 帐户。 |
|[创建具有标准（手动）吞吐量的 Azure Cosmos 帐户、数据库和图](manage-gremlin-with-resource-manager.md#create-manual) | 此模板在两个区域（具有标准吞吐量的数据库和图）中创建一个 Gremlin API 帐户。 |

## <a name="table-api"></a>表 API

|**模板**|**说明**|
|---| ---|
|[创建具有自动缩放吞吐量的 Azure Cosmos 帐户和表](manage-table-with-resource-manager.md#create-autoscale) | 此模板在两个区域中创建一个 Table API 帐户，并创建一个具有自动缩放吞吐量的表。 |
|[创建具有标准（手动）吞吐量的 Azure Cosmos 帐户和表](manage-table-with-resource-manager.md#create-manual) | 此模板在两个区域中创建一个 Table API 帐户，并创建一个具有标准吞吐量的表。 |

有关参考文档，请参阅 [Azure Cosmos DB 的 Azure 资源管理器参考](/azure/templates/microsoft.documentdb/allversions)页。
