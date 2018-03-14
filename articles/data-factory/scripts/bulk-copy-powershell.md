---
title: "PowerShell 脚本：使用 Azure 数据工厂批量复制数据 | Microsoft Docs"
description: "此 PowerShell 脚本演示如何使用 Azure 数据工厂将源数据存储中的数据批量复制到目标数据存储。"
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: f8a66f3daab9b57723f67a60fa195e53333f3b8d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>PowerShell 脚本 - 使用 Azure 数据工厂批量复制多个表

此 PowerShell 示例脚本将数据从 Azure SQL 数据库中的多个表复制到 Azure SQL 数据仓库。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

有关运行此示例的先决条件，请参阅[教程：批量复制](../tutorial-bulk-copy.md#prerequisites)。

## <a name="sample-script"></a>示例脚本

> [!IMPORTANT]
> 此脚本在硬盘驱动器上的 c:\ 文件夹中创建 JSON 文件，用于定义数据工厂实体（链接服务、数据集和管道）。

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure SQL Data Warehouse")]

## <a name="clean-up-deployment"></a>清理部署

运行示例脚本后，可以使用以下命令删除资源组以及与其关联的所有资源：

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
若要从资源组中删除数据工厂，请运行以下命令： 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| 命令 | 说明 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 创建用于存储所有资源的资源组。 |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | 创建数据工厂。 |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | 在数据工厂中创建链接服务。 链接服务可将数据存储或计算链接到数据工厂。 |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | 在数据工厂中创建数据集。 数据集表示管道中活动的输入/输出。 | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactorv2ypipeline) | 在数据工厂中创建管道。 一个管道包含一个或多个执行某项操作的活动。 在此管道中，复制活动在 Azure Blob 存储中将数据从一个位置复制到另一个位置。 |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipelinerun) | 为管道创建运行。 换而言之，就是运行管道。 |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | 获取管道中活动的运行（活动运行）的相关详细信息。 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure 数据工厂 PowerShell 脚本](../samples-powershell.md)中找到其他 Azure 数据工厂 PowerShell 脚本示例。