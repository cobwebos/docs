---
title: 用于创建 Azure Cosmos MongoDB API 数据库和集合（具有自动缩放功能）的 PowerShell 脚本
description: Azure PowerShell 脚本 - 创建 Azure Cosmos MongoDB API 数据库和集合（具有自动缩放功能）
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 4f41f940b26adfe66745dca447294102e65607a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87505076"
---
# <a name="create-a-database-and-collection-with-autoscale-for-azure-cosmos-db---mongodb-api"></a>为 Azure Cosmos DB 创建数据库和集合（具有自动缩放功能）- MongoDB API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-autoscale.ps1 "Create a database and collection with autoscale for MongoDB API")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
|**Azure Cosmos DB**| |
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | 创建 Cosmos DB 帐户。 |
| [New-AzCosmosDBMongoDBDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbdatabase) | 创建 MongoDB API 数据库。 |
| [New-AzCosmosDBMongoDBIndex](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbindex) | 创建 MongoDB API 索引。 |
| [New-AzCosmosDBMongoDBCollection](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbcollection) | 创建 MongoDB API 集合。 |
|**Azure 资源组**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/)。

可以在 [Azure Cosmos DB PowerShell 脚本](../../../powershell-samples.md)中找到其他 Azure Cosmos DB PowerShell 脚本示例。