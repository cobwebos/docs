---
title: Azure Cosmos DB 的 Azure 资源管理器模板
description: 使用 Azure 资源管理器模板创建和配置 Azure Cosmos DB。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: thvankra
ms.openlocfilehash: 7b08ca98f25b079d831033b9393effd4ee4b65e3
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961859"
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

有关参考文档，请参阅[Azure 资源管理器参考 Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)页面。
