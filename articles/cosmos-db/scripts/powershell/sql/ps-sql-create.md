---
title: 用于创建 Azure Cosmos DB SQL (Core) API 数据库和容器的 PowerShell 脚本
description: Azure PowerShell 脚本 - Azure Cosmos DB 创建 SQL (Core) API 数据库和容器
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: 5a57b8cb3e4e7076d3be73a9010d9a29a8b004a3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441361"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-core-api"></a>创建 Azure Cosmos DB 的数据库和容器 - SQL (Core) API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>示例脚本

此脚本在两个具有会话级一致性的区域、具有共享吞吐量的数据库和具有分区键、自定义索引策略、唯一键策略、TTL、专用吞吐量和“以最后一次写入为准”冲突解决策略的容器中使用在 `multipleWriteLocations=true` 时将使用的自定义冲突解决路径为 SQL (Core) API 创建 Cosmos 帐户。

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-create.ps1 "Create an account, database, and container for SQL (Core) API")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
|**Azure 资源**| |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | 创建资源。 |
|**Azure 资源组**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure Cosmos DB PowerShell 脚本](../../../powershell-samples.md)中找到其他 Azure Cosmos DB PowerShell 脚本示例。
