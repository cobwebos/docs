---
title: 用于 Azure Cosmos DB 的 Azure CLI 示例
description: Azure CLI 示例 - 创建和管理 Azure Cosmos DB 帐户、数据库、容器、区域和防火墙。
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: d7ce5e2c12feeee770de8acfd5df81d340e0a7d0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615568"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>用于 Azure Cosmos DB 的 Azure CLI 示例

下表包括用于 Azure Cosmos DB 的示例 Azure CLI 脚本的链接。 [Azure CLI 参考](/cli/azure/cosmosdb)中收录了所有 Azure Cosmos DB CLI 命令的参考页。

| |  |
|---|---|
|**创建 Azure Cosmos DB 帐户、数据库和容器**||
| [使用 SQL API 创建 Azure Cosmos DB 帐户](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| 创建单个 Azure Cosmos DB 帐户、数据库和容器。 |
| [使用 Cosmos DB 的用于 MongoDB 的 API 创建 Azure Cosmos DB 帐户](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 创建单个 Azure Cosmos DB 帐户、数据库和集合。 |
| [使用 Gremlin API 创建 Azure Cosmos DB 帐户](scripts/create-gremlin-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 创建单个 Azure Cosmos DB 帐户、数据库和图。 |
| [使用 Cassandra API 创建 Azure Cosmos DB 帐户](scripts/create-cassandra-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 创建单个 Azure Cosmos DB 帐户和数据库。 |
| [使用表 API 创建 Azure Cosmos DB 帐户](scripts/create-table-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 创建单个 Azure Cosmos DB 帐户、数据库和表。 |
|**缩放 Azure Cosmos DB**||
| [缩放容器吞吐量](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 更改容器上预配的吞吐量。|
| [将 Azure Cosmos 数据库帐户复制到多个区域中并配置故障转移优先级](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|在全局范围内将帐户数据复制到具有指定故障转移优先级的多个区域中。|
|**保护 Azure Cosmos DB**||
| [获取帐户密钥](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 获取帐户的主要和辅助 master 写密钥以及主要和辅助只读密钥。|
| [获取 Cosmos 帐户的连接字符串，该字符串配置有 Azure Cosmos DB 的用于 MongoDB 的 API](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 获取用于将 MongoDB 应用连接到 Azure Cosmos DB 帐户的连接字符串。|
| [重新生成帐户密钥](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|重新生成帐户密钥。|
| [创建防火墙](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| 创建入站 IP 访问控制策略，仅允许从获批准的一组计算机和/或云服务访问帐户。|
|**高可用性、灾难恢复、备份和还原**||
| [配置故障转移策略](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|为帐户所复制的每个区域设置故障转移优先级。|
|**将 Azure Cosmos DB 连接到资源**||
| [将 Web 应用连接到 Azure Cosmos DB](../app-service/scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|创建并连接 Azure Cosmos 数据库和 Azure Web 应用。|
|||
