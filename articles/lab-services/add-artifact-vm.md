---
title: 将项目添加到 Azure 开发测试实验室中的 VM |Microsoft Docs
description: 了解如何在 Azure 开发测试实验室中的实验室中将项目添加到虚拟机
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 27fec279582d845972b87ac635c87c16c239924e
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2019
ms.locfileid: "73901315"
---
# <a name="add-an-artifact-to-a-vm"></a>向 VM 添加项目
创建 VM 时，可以向其添加现有项目。 这些项目可以来自[公共开发测试实验室 Git 存储库](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)，也可以来自你自己的 Git 存储库。 本文说明如何使用 Azure PowerShell 在 Azure 门户中添加项目。 

Azure 开发测试实验室项目可让你指定预配 VM 时执行的操作，如运行 Windows PowerShell 脚本、运行 Bash 命令和安装软件。 使用项目参数可为特定方案自定义项目。

若要了解如何创建自定义项目，请参阅文章：[创建自定义项目](devtest-lab-artifact-author.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>使用 Azure 门户 
1. 登录到 [Azure 门户](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 选择“所有服务”，并从列表中选择“开发测试实验室”。
1. 从实验室列表中，选择要处理的 VM 所在的实验室。  
1. 选择“我的虚拟机”。
1. 选择所需的 VM。
1. 选择“管理项目”。 
1. 选择“应用项目”。
1. 在“应用项目”窗格上，选择要添加到 VM 的项目。
1. 在“添加项目”窗格上，输入所需参数值及所需的任何可选参数。  
1. 选择“添加”以添加该项目，并返回到“应用项目”窗格。
1. 根据需要继续为 VM 添加项目。
1. 添加项目后，可以[更改项目的运行顺序](#change-the-order-in-which-artifacts-are-run)。 还可以返回到[查看或修改项目](#view-or-modify-an-artifact)。
1. 完成项目添加后，选择“应用”

### <a name="change-the-order-in-which-artifacts-are-run"></a>更改项目的运行顺序
默认情况下，按添加到 VM 的顺序执行项目操作。 以下步骤说明如何更改项目的运行顺序。

1. 在“应用项目”窗格顶部，选择指示已添加到 VM 的项目数的链接。
   
    ![添加到 VM 的项目数](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. 在“已选项目”窗格上，将项目拖放为所需的顺序。 如果在拖动项目时遇到问题，请确保是从项目的左侧拖动。 
1. 完成后选择“确定”。  

### <a name="view-or-modify-an-artifact"></a>查看或修改项目
以下步骤说明如何查看或修改项目参数：

1. 在“应用项目”窗格顶部，选择指示已添加到 VM 的项目数的链接。
   
    ![添加到 VM 的项目数](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. 在“已选项目”窗格上，选择要查看或编辑的项目。  
1. 在“添加项目”窗格上，根据需要进行更改，并选择“确定”关闭“添加项目”窗格。
1. 选择“确定”关闭“已选项目”窗格。

## <a name="use-powershell"></a>使用 PowerShell
下面的脚本将指定的项目应用到指定的虚拟机。 [AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction)命令是执行操作的命令。  

```powershell
#Requires -Module Az.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [指定实验室的必需项目](devtest-lab-mandatory-artifacts.md)
- [创建自定义项目](devtest-lab-artifact-author.md)
- [将项目存储库添加到实验室](devtest-lab-artifact-author.md)
- [对项目故障进行诊断](devtest-lab-troubleshoot-artifact-failure.md)
