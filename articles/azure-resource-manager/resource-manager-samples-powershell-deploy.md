---
title: "Azure PowerShell 脚本示例 - 部署模板 | Microsoft Docs"
description: "用于部署 Azure Resource Manager 模板的示例脚本。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
ms.openlocfilehash: b7a7dda1da653d084e02e6724d2f0cb5aa76807a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-resource-manager-template-deployment---powershell-script"></a>Azure Resource Manager 模板部署 - PowerShell 脚本

此脚本将 Resource Manager 模板部署到订阅中的资源组。

[!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

```powershell
<#
 .SYNOPSIS
    Deploys a template to Azure

 .DESCRIPTION
    Deploys an Azure Resource Manager template
#>

param (
    [Parameter(Mandatory)]
    #The subscription id where the template will be deployed.
    [string]$SubscriptionId,  

    [Parameter(Mandatory)]
    #The resource group where the template will be deployed. Can be the name of an existing or a new resource group.
    [string]$ResourceGroupName, 

    #Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.
    [string]$ResourceGroupLocation, 

    #The deployment name.
    [Parameter(Mandatory)]
    [string]$DeploymentName,    

    #Path to the template file. Defaults to template.json.
    [string]$TemplateFilePath = "template.json",  

    #Path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
    [string]$ParametersFilePath = "parameters.json"
)

$ErrorActionPreference = "Stop"

# Login to Azure and select subscription
Write-Output "Logging in"
Login-AzureRmAccount
Write-Output "Selecting subscription '$SubscriptionId'"
Select-AzureRmSubscription -SubscriptionID $SubscriptionId

# Create or check for existing resource group
$resourceGroup = Get-AzureRmResourceGroup -Name $ResourceGroupName -ErrorAction SilentlyContinue
if ( -not $ResourceGroup ) {
    Write-Output "Could not find resource group '$ResourceGroupName' - will create it"
    if ( -not $ResourceGroupLocation ) {
        $ResourceGroupLocation = Read-Host -Prompt 'Enter location for resource group'
    }
    Write-Output "Creating resource group '$ResourceGroupName' in location '$ResourceGroupLocation'"
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else {
    Write-Output "Using existing resource group '$ResourceGroupName'"
}

# Start the deployment
Write-Output "Starting deployment"
if ( Test-Path $ParametersFilePath ) {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFilePath -TemplateParameterFile $ParametersFilePath
}
else {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFilePath
}
``` 

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令以删除资源组及其所有资源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建部署。 表中的每一项均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) | 注册资源提供程序，以便将其资源类型部署到订阅。  |
| [Get-AzureRmResourceGroup](/powershell/module/azurerm.resources/get-azurermresourcegroup) | 获取资源组。  |
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) | 将 Azure 部署添加到资源组。  |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 删除资源组及其中包含的所有资源。 |



## <a name="next-steps"></a>后续步骤
* 有关部署模板的简介，请参阅[使用 Resource Manager 模板和 Azure PowerShell 部署资源](resource-group-template-deploy.md)。
* 有关部署需要 SAS 令牌的模板的信息，请参阅[使用 SAS 令牌部署专用模板](resource-manager-powershell-sas-token.md)。
* 若要在模板中定义参数，请参阅[创作模板](resource-group-authoring-templates.md#parameters)。
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。

