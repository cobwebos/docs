---
title: Azure Cosmos DB 的 Azure 资源管理器模板
description: 使用 Azure 资源管理器模板创建和配置 Azure Cosmos DB。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mjbrown
ms.openlocfilehash: 1c4f1a1920d98052231eaa3a7d5c0454441c88ee
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390880"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB 的 Azure 资源管理器模板

下表包含 Azure Cosmos DB 的 Azure 资源管理器模板链接：

## <a name="sql-core-api"></a>SQL（核心）API

|**模板**|**说明**|
|---|---|
|[创建 Azure Cosmos 帐户、数据库和容器](manage-sql-with-resource-manager.md#create-resource) | 此模板在两个区域创建一个 SQL (Core) API 帐户，区域中的两个容器具有共享数据库吞吐量，一个容器具有专用吞吐量。 吞吐量可以更新，只需重新提交包含已更新吞吐量属性值的模板即可。 |
|[通过存储过程、触发器和 UDF 创建 Azure Cosmos 帐户、数据库和容器](manage-sql-with-resource-manager.md#create-sproc) | 此模板通过存储过程、触发器和用于容器的 UDF 在两个区域中创建一个 SQL (Core) API 帐户。 |
|[为现有 Azure Cosmos 帐户创建专用终结点](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  此模板为现有虚拟网络中的现有 Azure Cosmos SQL API 帐户创建专用终结点。 |

## <a name="mongodb-api"></a>MongoDB API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户、数据库、集合](manage-mongodb-with-resource-manager.md#create-resource) | 此模板使用 Azure Cosmos DB API for MongoDB 在两个区域创建一个启用了多主数据库的帐户。 Azure Cosmos 帐户会有两个共享数据库级吞吐量的容器。 |

## <a name="cassandra-api"></a>Cassandra API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户、密钥空间、表](manage-cassandra-with-resource-manager.md#create-resource) | 此模板在两个区域创建一个启用了多主数据库的 Cassandra API 帐户。 Azure Cosmos 帐户会有两个共享密钥空间级吞吐量的表。 |

## <a name="gremlin-api"></a>Gremlin API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户、数据库、图形](manage-gremlin-with-resource-manager.md#create-resource) | 此模板在两个区域创建一个启用了多主数据库的 Gremlin API 帐户。 Azure Cosmos 帐户会有两个共享数据库级吞吐量的图形。 |

## <a name="table-api"></a>表 API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户、表](manage-table-with-resource-manager.md#create-resource) | 此模板在两个区域创建一个启用了多主数据库的表 API 帐户。 Azure Cosmos 帐户会有一个表。 |

> [!TIP]
> 若要在使用表 API 时启用共享吞吐量，请在 Azure 门户中启用帐户级吞吐量。

有关参考文档，请参阅[Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)页的 Azure 资源管理器参考。
