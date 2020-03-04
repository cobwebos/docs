---
title: 已知问题：从 MongoDB 迁移到 Azure CosmosDB
titleSuffix: Azure Database Migration Service
description: 了解使用 Azure 数据库迁移服务从 MongoDB 迁移到 Azure Cosmos DB 的已知问题和迁移限制。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 194da036260a78b27748dfc7f755212ab4f30b1e
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78256033"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>从 MongoDB 迁移到 Azure Cosmos DB 的适用于 MongoDB 的 API 的已知问题/迁移限制

以下各节将介绍与从 MongoDB 迁移到 Cosmos DB 的 API for MongoDB 相关的已知问题和限制。

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>由于使用了不正确的 SSL 证书，迁移失败

* **症状**：当用户无法连接到 MongoDB 源服务器时，此问题很明显。 尽管所有防火墙端口都已打开，但用户仍然无法连接。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 使用 Azure 数据库迁移服务中的自签名证书可能会导致迁移失败，因为 SSL 证书不正确。错误消息可能包含 "根据验证过程，远程证书无效"。 | 使用 CA 颁发的正版证书。  自签名证书通常仅用于内部测试。 从 CA 颁发机构安装正版证书时，可以在 Azure 数据库迁移服务中使用 SSL，而不会出现问题（连接到 Cosmos DB 使用 SSL over Mongo API）。<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>无法获取 DM 中要映射的数据库的列表

* **症状**：在 "**选择源**" 边栏选项卡上使用**Azure 存储**模式的数据时，无法从 "**数据库设置**" 边栏选项卡获取数据库列表。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 存储帐户连接字符串缺少 SAS 信息，因此无法对其进行身份验证。 | 在存储资源管理器中的 blob 容器上创建 SAS，并使用 URL 和容器 SAS 信息作为源详细信息连接字符串。<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>使用不受支持的数据库版本

* **症状**：迁移失败。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 尝试从不受支持的 MongoDB 版本迁移到 Azure Cosmos DB。 | 发布新版本的 MongoDB 后，将对其进行测试，以确保与 Azure 数据库迁移服务兼容，并且定期更新服务以接受最新版本。 如果立即需要迁移，作为一种解决方法，你可以将数据库/集合导出到 Azure 存储，并将源点指向生成的转储。 在存储资源管理器中的 blob 容器上创建 SAS，然后使用 URL 和容器 SAS 信息作为源详细信息连接字符串。<br><br> |

## <a name="next-steps"></a>后续步骤

* 查看教程：[通过 DMS 将 Mongodb 迁移到 Azure Cosmos DB 的 API For MongoDB](tutorial-mongodb-cosmos-db-online.md)。
* 查看教程：[使用 DMS 将 Mongodb 迁移到 Azure Cosmos DB 的 API For MongoDB](tutorial-mongodb-cosmos-db.md)。