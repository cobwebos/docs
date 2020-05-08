---
title: Azure Cosmos DB 的 Azure 资源管理器模板
description: 使用 Azure 资源管理器模板创建和配置 Azure Cosmos DB。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: 501065875cafc035d491e606c016f3ad4dbfc5d4
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791623"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB 的 Azure 资源管理器模板

下表包含 Azure Cosmos DB 的 Azure 资源管理器模板链接：

## <a name="core-sql-api"></a>Core (SQL) API

|**模板**|**说明**|
|---|---|
|[创建 Azure Cosmos 帐户、数据库、具有自动缩放吞吐量的容器](manage-sql-with-resource-manager.md#create-autoscale) | 此模板在两个区域创建一个核心（SQL） API 帐户，该帐户具有自动缩放吞吐量的数据库和容器。 |
|[创建 Azure Cosmos 帐户、数据库、具有标准（手动）吞吐量的容器](manage-sql-with-resource-manager.md#create-manual) | 此模板在两个区域创建一个核心（SQL） API 帐户，该帐户具有标准吞吐量的数据库和容器。 |
|[通过存储过程、触发器和 UDF 创建 Azure Cosmos 帐户、数据库和容器](manage-sql-with-resource-manager.md#create-sproc) | 此模板使用容器的存储过程、触发器和 UDF 在两个区域中创建核心（SQL） API 帐户。 |
|[为现有的 Azure Cosmos 帐户创建专用终结点](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  此模板在现有虚拟网络中创建现有 Azure Cosmos Core （SQL） API 帐户的专用终结点。 |
|[创建免费的 Azure Cosmos 帐户](manage-sql-with-resource-manager.md#free-tier) |  此模板在免费层上创建 Azure Cosmos DB 核心（SQL） API 帐户。 |

## <a name="mongodb-api"></a>MongoDB API

|**模板**|**说明**|
|---| ---|
|[创建使用自动缩放吞吐量的 Azure Cosmos 帐户、数据库、集合](manage-mongodb-with-resource-manager.md#create-autoscale) | 此模板在两个区域中使用 Azure Cosmos DB API 创建一个帐户，该帐户具有两个共享数据库级自动缩放吞吐量的容器。 |
|[创建具有标准（手动）吞吐量的 Azure Cosmos 帐户、数据库、集合](manage-mongodb-with-resource-manager.md#create-manual) | 此模板在两个区域中使用 Azure Cosmos DB API 创建一个帐户，该帐户具有两个共享数据库级标准吞吐量的容器。 |

## <a name="cassandra-api"></a>Cassandra API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户，密钥空间，具有自动缩放吞吐量的表](manage-cassandra-with-resource-manager.md#create-autoscale) | 此模板在两个区域创建 Cassandra API 帐户，其中密钥空间和表具有自动缩放吞吐量。 |
|[创建具有标准（手动）吞吐量的 Azure Cosmos 帐户、密钥空间和表](manage-cassandra-with-resource-manager.md#create-manual) | 此模板在两个区域创建 Cassandra API 帐户，其中密钥空间和表具有手动吞吐量。 |

## <a name="gremlin-api"></a>Gremlin API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户、数据库、具有自动缩放吞吐量的图形](manage-gremlin-with-resource-manager.md#create-autoscale) | 此模板在包含数据库的两个区域中创建 Gremlin API 帐户，并为图形提供自动缩放吞吐量。 |
|[创建具有标准（手动）吞吐量的 Azure Cosmos 帐户、数据库和图形](manage-gremlin-with-resource-manager.md#create-manual) | 此模板在两个区域中创建 Gremlin API 帐户，其中包含具有标准吞吐量的数据库和图形。 |

## <a name="table-api"></a>表 API

|**模板**|**说明**|
|---| ---|
|[创建 Azure Cosmos 帐户，具有自动缩放吞吐量的表](manage-table-with-resource-manager.md#create-autoscale) | 此模板在两个区域创建一个表 API 帐户，并创建一个具有自动缩放吞吐量的单一表。 |
|[创建 Azure Cosmos 帐户，具有标准（手动）吞吐量的表](manage-table-with-resource-manager.md#create-manual) | 此模板在两个区域创建表 API 帐户，并使用标准吞吐量创建单个表。 |

有关参考文档，请参阅[Azure 资源管理器参考 Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)页面。
