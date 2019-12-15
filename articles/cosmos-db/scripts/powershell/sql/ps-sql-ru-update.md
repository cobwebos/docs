---
title: Azure PowerShell 脚本 - Azure Cosmos DB 更新 RU/秒 - SQL (Core) API
description: 使用 PowerShell 脚本- SQL (Core) API 更新 Azure Cosmos 数据库或容器的吞吐量
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 5a03c992e1ce1dc17c53bf414f0b07d2b60ddcce
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871881"
---
# <a name="update-rus-for-a-database-or-container-for-azure-cosmos-db---sql-core-api"></a>更新 Azure Cosmos DB 的数据库或容器的 RU/秒 - SQL (Core) API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-update.ps1 "Update throughput on a database or container for SQL (Core) API")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
|**Azure 资源**| |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | 创建资源。 |
|**Azure 资源组**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure Cosmos DB PowerShell 脚本](../../../powershell-samples.md)中找到其他 Azure Cosmos DB PowerShell 脚本示例。