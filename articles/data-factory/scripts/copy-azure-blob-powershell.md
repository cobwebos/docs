---
title: "PowerShell 脚本：使用 Azure 数据工厂复制云端数据 | Microsoft Docs"
description: "此 PowerShell 脚本将数据从 Azure Blob 存储中的一个位置复制到同一 Blob 存储中的另一个位置。"
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ad97a40c5c12aa5a8f69a8d56750bd6f39725be0
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>使用 PowerShell 创建用于复制云端数据的数据工厂管道

此示例 PowerShell 脚本创建一个 Azure Cosmos DB API 帐户。 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>示例脚本

> [!IMPORTANT]
> 此脚本在硬盘驱动器上的 c:\ 文件夹中创建 JSON 文件，用于定义数据工厂实体（链接服务、数据集和管道）。

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-azure-blob-to-blob/copy-from-azure-blob-to-blob.ps1?highlight=9,12-15,18,21-23,26-29,32-37 "Copy from Blob Storage -> Blob Storage")]


## <a name="clean-up-deployment"></a>清理部署

运行示例脚本后，可以使用以下命令删除资源组以及与其关联的所有资源：

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```
若要从资源组中删除数据工厂，请运行以下命令： 

```powershell
Remove-AzureRmDataFactoryV2 -Name "<data factory name>" -ResourceGroupName "<resource group name>"
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

可以在 [Azure Cosmos DB PowerShell 脚本](../samples-powershell.md)中找到其他 Azure Cosmos DB PowerShell 脚本示例。
