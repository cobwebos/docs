---
title: 已知问题：从蒙戈DB迁移到 Azure 宇宙DB
titleSuffix: Azure Database Migration Service
description: 使用 Azure 数据库迁移服务了解从 MongoDB 迁移到 Azure Cosmos DB 的已知问题和迁移限制。
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
ms.openlocfilehash: ae5d5f2d282c546f5172ca1c8cb0e420d3b6e96b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878045"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>从蒙戈DB迁移到 Azure Cosmos DB 的 MongoDB API 时已知的问题/迁移限制

以下各节介绍了与从 MongoDB 迁移到 Cosmos DB MongoDB 的 API 相关的已知问题和限制。

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>迁移失败，因为使用不正确的 SSL 证书

* **症状**：当用户无法连接到 MongoDB 源服务器时，此问题是显而易见的。 尽管打开了所有防火墙端口，但用户仍无法连接。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 在 Azure 数据库迁移服务中使用自签名证书可能会导致迁移失败，因为 SSL Cert 不正确。错误消息可能包括"根据验证过程远程证书无效"。 | 使用来自 CA 的正版证书。  自签名证书通常仅用于内部测试。 从 CA 颁发机构安装正版证书时，可以在 Azure 数据库迁移服务中无问题地使用 SSL（与 Cosmos DB 的连接通过 Mongo API 使用 SSL）。<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>无法获取要在 DMS 中映射的数据库列表

* **症状**：在 **"选择源"** 边栏选项卡上使用**Azure 存储模式的数据**时，无法获取**数据库设置**边栏选项卡上的数据库列表。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 存储帐户连接字符串缺少 SAS 信息，因此无法进行身份验证。 | 在存储资源管理器中的 blob 容器上创建 SAS，并将具有容器 SAS 信息的 URL 用作源详细信息连接字符串。<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>使用不支持的数据库版本

* **症状**：迁移失败。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 您尝试从不支持的 MongoDB 版本迁移到 Azure 宇宙 DB。 | 随着 MongoDB 的新版本发布，将测试它们以确保与 Azure 数据库迁移服务的兼容性，并且该服务将定期更新以接受最新版本。 如果迫切需要迁移，则可以将数据库/集合导出到 Azure 存储，然后将源指向生成的转储。 在存储资源管理器中的 blob 容器上创建 SAS，然后将具有容器 SAS 信息的 URL 用作源详细信息连接字符串。<br><br> |

## <a name="next-steps"></a>后续步骤

* 使用 DMS 在线查看[将蒙戈DB迁移到 Azure Cosmos DB](tutorial-mongodb-cosmos-db-online.md)的 API 教程。
* 使用 DMS 查看将[蒙戈DB迁移到 Azure Cosmos DB](tutorial-mongodb-cosmos-db.md)的 API 教程。