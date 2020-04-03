---
title: 用于更新 RU/秒的 PowerShell 脚本 - Azure Cosmos DB Gremlin API
description: Azure PowerShell 脚本 - Azure Cosmos DB 更新 RU/秒 - Gremlin API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 61c06fca0fe2f2449f67890dd962905642605716
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366012"
---
# <a name="update-rus-for-a-database-or-graph-for-azure-cosmos-db---gremlin-api"></a>更新 Azure Cosmos DB 的数据库或图的 RU/秒 - Gremlin API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-ru-update.ps1 "Update throughput on a database or graph for Gremlin API")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
|**Azure Cosmos DB**| |
| [Set-AzCosmosDBGremlinDatabase](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbgremlindatabase) | 创建或更新 Gremlin API 数据库。 |
| [Get-AzCosmosDBGremlinGraph](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbgremlingraph) | 获取 Gremlin API 图。 |
| [Set-AzCosmosDBGremlinGraph](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbgremlingraph) | 创建或更新 Gremlin API 图。 |
|**Azure 资源组**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure Cosmos DB PowerShell 脚本](../../../powershell-samples.md)中找到其他 Azure Cosmos DB PowerShell 脚本示例。