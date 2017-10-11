---
title: "对于 Azure Cosmos DB 的 azure PowerShell 脚本 Multiregion 复制 |Microsoft 文档"
description: "Azure PowerShell 脚本示例-对于 Azure Cosmos DB Multiregion 复制"
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: 3a469ba43e6c601f5eb0e13d588cd0bd4a0f8683
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-an-azure-cosmos-db-database-account-in-multiple-regions-and-configure-failover-priorities-using-powershell"></a>复制多个区域中的 Azure Cosmos DB 数据库帐户和配置使用 PowerShell 的故障转移优先级

此示例将复制任何类型的多个区域中的 Azure Cosmos DB 数据库帐户并配置使用 PowerShell 的故障转移优先级。 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[主要](../../../powershell_scripts/cosmosdb/replicate-database-multiple-regions/replicate-database-multiple-regions.ps1?highlight=37-44,47-48,51-55 "跨多个区域复制的 Azure Cosmos DB 帐户")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令以删除资源组和与之关联的所有资源。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 命令特定文档的表链接中的每个命令。

| 命令 | 注意 |
|---|---|
| [新 AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | 创建资源组中的所有资源的都存储。 |
| [新 AzureRmResource](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresource?view=azurermps-3.8.0) | 创建承载的数据库或弹性池的逻辑服务器。 |
| [集 AzureRMResource](https://docs.microsoft.com/powershell/module/azurerm.resources/set-azurermresource?view=azurermps-3.8.0) | 修改数据库帐户。 |
| [Remove-azurermresourcegroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | 删除资源组包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅[Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

在找不到其他 Azure Cosmos DB PowerShell 脚本示例[Azure Cosmos DB PowerShell 脚本](../powershell-samples.md)。