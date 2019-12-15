---
title: 适用于 Azure Cosmos DB 的 Azure PowerShell 示例 - SQL (Core) API
description: 获取用于在 Azure Cosmos DB SQL API 帐户中执行各种常见任务的 Azure PowerShell 示例
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: 29195ce141c29248840807b978cae3ecf79ccf34
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871918"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>适用于 Azure Cosmos DB 的 Azure PowerShell 示例 - SQL (Core) API

下表包含常用于 Azure Cosmos DB for SQL (Core) API 的 Azure PowerShell 脚本的链接。 如果要从我们的 GitHub 存储库创建这些适用于 Cosmos DB 的 PowerShell 示例的分支，请访问 [GitHub 上的 Cosmos DB PowerShell 示例](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb)。

有关 SQL (Core) API 的其他 Cosmos DB PowerShell 示例和文档，请参阅[使用 PowerShell 管理 Azure Cosmos DB SQL API 资源](manage-with-powershell.md)。 有关其他 API 的 Cosmos DB PowerShell 示例，请参阅 [Cassandra API](powershell-samples-cassandra.md)、[MongoDB API](powershell-samples-mongodb.md)、[Gremlin API](powershell-samples-gremlin.md) 和[表 API](powershell-samples-table.md)。

| | |
|---|---|
|[创建帐户、数据库和容器](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建 Azure Cosmos 帐户、数据库和容器。 |
|[创建具有大分区键的容器](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建具有大分区键的容器。 |
|[列出或获取数据库或容器](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 列出或获取数据库或容器。 |
|[获取 RU/秒](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 获取数据库或容器的 RU/秒。 |
|[更新 RU/秒](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新数据库或容器的 RU/秒。 |
|[使用无索引策略创建容器](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 在索引策略关闭的情况下创建 Azure Cosmos 容器。|
|[更新帐户或添加区域](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 将区域添加到 Cosmos 帐户。 也可用于修改其他帐户属性，但这些必须与对区域的更改分开。 |
|[更改故障转移优先级或触发故障转移](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更改 Azure Cosmos 帐户的区域故障转移优先级或触发手动故障转移。 |
|[帐户密钥或连接字符串](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 获取主密钥和辅助密钥、连接字符串或重新生成 Azure Cosmos 帐户的帐户密钥。 |
|[创建启用 IP 防火墙的 Cosmos 帐户](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建启用 IP 防火墙的 Azure Cosmos 帐户。 |
|||
