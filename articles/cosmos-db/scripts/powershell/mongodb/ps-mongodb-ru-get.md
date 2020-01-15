---
title: 用于获取吞吐量（RU/秒）的 PowerShell 脚本 - Azure Cosmos DB 的 API for MongoDB
description: Azure PowerShell 脚本 - Azure Cosmos DB 获取吞吐量（RU/秒）- MongoDB API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: d7cd068722a1dcc99b909020fa5bae34ae4a4768
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441390"
---
# <a name="get-throughput-rus-for-a-database-or-collection-for-azure-cosmos-db---mongodb-api"></a>获取 Azure Cosmos DB 的数据库或集合的吞吐量（RU/秒）- MongoDB API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-ru-get.ps1 "Get throughput on a database or collection for MongoDB API")]

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