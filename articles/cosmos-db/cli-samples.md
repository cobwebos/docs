---
title: 用于 Azure Cosmos DB 的 Azure CLI 示例
description: Azure CLI 示例 - 创建和管理 Azure Cosmos DB 帐户、数据库、容器、区域和防火墙。
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 48beb93fbc5952951fff1ed31e5f8625faf78ccd
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850391"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>用于 Azure Cosmos DB 的 Azure CLI 示例

下表包括用于 Azure Cosmos DB 的示例 Azure CLI 脚本的链接。 [Azure CLI 参考](/cli/azure/cosmosdb)中收录了所有 Azure Cosmos DB CLI 命令的参考页。

| |  |
|---|---|
|**创建 Azure Cosmos DB 帐户、数据库和容器**||
| [创建 SQL API 帐户](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| 创建单个 Azure Cosmos DB SQL API 帐户、数据库和容器。 |
| [创建 MongoDB API 帐户](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 创建单个 Azure Cosmos DB MongoDB API 帐户、数据库和集合。 |
| [创建 Gremlin API 帐户](scripts/create-gremlin-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 创建单个 Azure Cosmos DB Gremlin API 帐户、数据库和图形。 |
| [创建 Cassandra API 帐户](scripts/create-cassandra-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 创建单个 Azure Cosmos DB Cassandra API 帐户和数据库。 |
| [创建表 API 帐户](scripts/create-table-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 创建单个 Azure Cosmos DB 表 API 帐户、数据库和表。 |
|**缩放 Azure Cosmos DB**||
| [缩放容器吞吐量](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 更改容器上预配的吞吐量。|
| [将 Azure Cosmos DB 数据库帐户复制到多个区域中并配置故障转移优先级](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|在全局范围内将帐户数据复制到具有指定故障转移优先级的多个区域中。|
|**保护 Azure Cosmos DB**||
| [获取帐户密钥](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 获取帐户的主要和辅助 master 写密钥以及主要和辅助只读密钥。|
| [获取 MongoDB 连接字符串](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 获取用于将 MongoDB 应用连接到 Azure Cosmos DB 帐户的连接字符串。|
| [重新生成帐户密钥](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|重新生成帐户密钥。|
| [创建防火墙](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| 创建入站 IP 访问控制策略，仅允许从获批准的一组计算机和/或云服务访问帐户。|
|**高可用性、灾难恢复、备份和还原**||
| [配置故障转移策略](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|为帐户所复制的每个区域设置故障转移优先级。|
|**将 Azure Cosmos DB 连接到资源**||
| [将 Web 应用连接到 Azure Cosmos DB](../app-service/scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|创建并连接 Azure Cosmos DB 数据库和 Azure Web 应用。|
|||
