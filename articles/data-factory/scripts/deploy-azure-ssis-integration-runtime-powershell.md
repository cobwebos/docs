---
title: "PowerShell 脚本 - 部署 Azure-SSIS 集成运行时 | Microsoft Docs"
description: "此 PowerShell 脚本创建可在云中运行 SSIS 程序包的 Azure-SSIS 集成运行时。"
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
ms.openlocfilehash: 9be0ed15f5bea06b499c696a63829f7898eb7b74
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>PowerShell 脚本 - 部署 Azure-SSIS 集成运行时

此示例 PowerShell 脚本创建可在 Azure 中运行 SSIS 程序包的 Azure-SSIS 集成运行时。  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1?highlight=9,12-15,18,21-23,26-29,32-37 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>清理部署

运行示例脚本后，可以使用以下命令删除资源组以及与其关联的所有资源：

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令：

| 命令 | 说明 |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | 创建用于存储所有资源的资源组。 |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | 创建数据工厂。 |
| [Set-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime) | 创建可在云中运行 SSIS 程序包的 Azure-SSIS 集成运行时 |
| [Start-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime) | 启动 Azure-SSIS 集成运行时。 |
| [Get-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2integrationruntime) | 获取有关 Azure-SSIS 集成运行时的信息。 |
| [Get-AzureRmDataFactoryV2IntegrationRuntimeStatus](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2integrationruntimestatus) | 获取有关 Azure-SSIS 集成运行时状态的信息。 |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure Cosmos DB PowerShell 脚本](../samples-powershell.md)中找到其他 Azure Cosmos DB PowerShell 脚本示例。
