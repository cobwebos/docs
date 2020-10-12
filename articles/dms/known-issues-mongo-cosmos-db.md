---
title: 已知问题：从 MongoDB 迁移到 Azure CosmosDB
titleSuffix: Azure Database Migration Service
description: 了解在使用 Azure 数据库迁移服务从 MongoDB 迁移到 Azure Cosmos DB 时的已知问题和迁移限制。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/27/2020
ms.openlocfilehash: be6d9d3b8a20e11c874234baae26629ce0187e4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91291804"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>从 MongoDB 迁移到 Azure Cosmos DB API for MongoDB 时的已知问题/迁移限制

以下各部分介绍了从 MongoDB 迁移到 Cosmos DB API for MongoDB 时的已知问题和限制。

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>迁移由于使用不正确的 SSL 证书而失败

* **症状**：当用户无法连接到 MongoDB 源服务器时，此问题很明显。 尽管所有防火墙端口都已打开，但用户仍然无法连接。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 在 Azure 数据库迁移服务中使用自签名证书可能会由于 SSL 证书不正确而导致迁移失败。错误消息可能包括“根据验证过程，远程证书无效。” | 使用来自 CA 的正版证书。  自签名证书通常仅用于内部测试。 安装来自 CA 颁发机构的正版证书时，可以在 Azure 数据库迁移服务中使用 SSL，这不会出现问题（与 Cosmos DB 的连接对 Mongo API 使用 SSL）。<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>无法获取要在 DMS 中映射的数据库的列表

* **症状**：在“选择源”  边栏选项卡上使用“Azure 存储中的数据”  模式时，无法在“数据库设置”边栏选项卡上获取 DB 列表  。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 存储帐户连接字符串中缺少 SAS 信息，因此无法对其进行身份验证。 | 在存储资源管理器中的 blob 容器中创建 SAS，并将 URL 与容器 SAS 用作源详细信息连接字符串。<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>使用了不受支持的数据库版本

* **症状**：迁移失败。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 你试图从不受支持的 MongoDB 版本迁移到 Azure Cosmos DB。 | 发布新版本的 MongoDB 时，对其进行测试，以确保与 Azure 数据库迁移服务兼容，并且定期更新服务以接受最新版本。 如果需要立即迁移，作为解决方法，你可以将数据库/集合导出到 Azure 存储，并将源指向生成的转储。 在存储资源管理器中的 blob 容器中创建 SAS，然后将 URL 与容器 SAS 用作源详细信息连接字符串。<br><br> |

## <a name="next-steps"></a>后续步骤

* 查看教程[使用 DMS 将 MongoDB 联机迁移到 Azure Cosmos DB API for MongoDB](tutorial-mongodb-cosmos-db-online.md)。
* 查看教程[使用 DMS 将 MongoDB 脱机迁移到 Azure Cosmos DB API for MongoDB](tutorial-mongodb-cosmos-db.md)。