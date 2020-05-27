---
title: 适用于 Azure Cosmos DB 的 Azure PowerShell 示例 - MongoDB API
description: 获取用于在 Azure Cosmos DB 的 API for MongoDB 中执行各种常见任务的 Azure PowerShell 示例
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: c8e0a7a60a3512d19a1dfdfdb07b20e523ce7b92
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649709"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>适用于 Azure Cosmos DB 的 Azure PowerShell 示例 - MongoDB API

下表包含用于 Azure Cosmos DB for MongoDB API 的示例 Azure PowerShell 脚本的链接。

> [!NOTE]
> 目前，你只能使用 PowerShell、CLI 和资源管理器模板为 MongoDB 帐户创建 3.2 版的 Azure Cosmos DB API for MongoDB（即，使用格式为 `*.documents.azure.com` 的终结点的帐户）。 若要创建 3.6 版帐户，请改用 Azure 门户。

> [!NOTE]
> 该示例使用 [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) 管理 cmdlet。 请定期检查 `Az.CosmosDB` 是否有更新。

| | |
|---|---|
|[创建帐户、数据库和集合](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建 Azure Cosmos 帐户、数据库和集合。 |
|[列出或获取数据库或集合](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 列出或获取数据库或集合。 |
|[获取 RU/秒](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 获取数据库或集合的 RU/秒。 |
|[更新 RU/秒](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新数据库或集合的 RU/秒。 |
|[更新帐户或添加区域](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 将区域添加到 Cosmos 帐户。 也可用于修改其他帐户属性，但这些必须与对区域的更改分开。 |
|[更改故障转移优先级或触发故障转移](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更改 Azure Cosmos 帐户的区域故障转移优先级或触发手动故障转移。 |
|[帐户密钥或连接字符串](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 获取主密钥和辅助密钥、连接字符串或重新生成 Azure Cosmos 帐户的帐户密钥。 |
|[创建启用 IP 防火墙的 Cosmos 帐户](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 创建启用 IP 防火墙的 Azure Cosmos 帐户。 |
|||
