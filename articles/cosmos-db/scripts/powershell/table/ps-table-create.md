---
title: 用于创建表的 PowerShell 脚本 - Azure Cosmos DB 表 API
description: 了解如何使用 PowerShell 脚本更新 Azure Cosmos DB 表 API 中数据库或容器的吞吐量
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 05/01/2020
ms.author: mjbrown
ms.openlocfilehash: 2be2934d53e3546a913a4842d16897c5fd1e51c5
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652952"
---
# <a name="create-a-table-for-azure-cosmos-db---table-api"></a>为 Azure Cosmos DB 创建表 - 表 API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-create.ps1 "Create a table for Table API")]

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
| [New-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | 新建 Cosmos DB 帐户。 |
| [Set-AzCosmosDBTable](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbtable) | 创建或更新表 API 表。 |
|**Azure 资源组**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure Cosmos DB PowerShell 脚本](../../../powershell-samples.md)中找到其他 Azure Cosmos DB PowerShell 脚本示例。