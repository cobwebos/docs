---
title: PowerShell 脚本 - 部署 Azure-SSIS 集成运行时 | Microsoft Docs
description: 此 PowerShell 脚本创建可在云中运行 SSIS 程序包的 Azure-SSIS 集成运行时。
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2017
author: swinarko
ms.author: sawinark
manager: craigg
ms.openlocfilehash: a2d53ddb9f55942cd6ad396d42cc926f20c2d396
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57574708"
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>PowerShell 脚本 - 部署 Azure-SSIS 集成运行时

此示例 PowerShell 脚本创建可在 Azure 中运行 SSIS 程序包的 Azure-SSIS 集成运行时。  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>清理部署

运行示例脚本后，可以使用以下命令删除资源组以及与其关联的所有资源：

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
若要从资源组中删除数据工厂，请运行以下命令： 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令：

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | 创建数据工厂。 |
| [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) | 创建可在云中运行 SSIS 程序包的 Azure-SSIS 集成运行时 |
| [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) | 启动 Azure-SSIS 集成运行时。 |
| [Get-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/get-Azdatafactoryv2integrationruntime) | 获取有关 Azure-SSIS 集成运行时的信息。 |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure 数据工厂 PowerShell 示例](../samples-powershell.md)中找到其他 Azure 数据工厂 PowerShell 脚本示例。
