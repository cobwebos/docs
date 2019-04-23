---
title: 使用命令行工具来启动和停止 Vm Azure 开发测试实验室 |Microsoft Docs
description: 了解如何使用命令行工具来启动和停止 Azure 开发测试实验室中的虚拟机。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 51c45fdb0c96e84d3f37f485279aa805361f3818
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798923"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>使用命令行工具来启动和停止 Azure 开发测试实验室虚拟机
本文介绍如何使用 Azure PowerShell 或 Azure CLI 来启动或停止 Azure 开发测试实验室中的实验室中的虚拟机。 可以创建 PowerShell/CLI 脚本来自动执行这些操作。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概述
Azure 开发测试实验室是一种方法来创建快速、 简单和精益的开发/测试环境。 它允许你管理成本、 快速预配 Vm，并最大程度减少浪费。  有在 Azure 门户中的内置功能，可用于将 Vm 配置在实验室中自动启动和停止在特定时间。 

但是，在某些情况下，你可能想要自动启动和停止的 Vm 通过 PowerShell/CLI 脚本。 这样，可以启动和停止在任何时间，而不是在特定时间的各台计算机具有一定的灵活性。 以下是一些在哪个运行这些任务使用脚本将会有所帮助的情况。

- 使用时的第 3 层应用程序作为测试环境的一部分，在各层需要启动序列中。 
- 关闭 VM 时自定义条件满足以节省资金。 
- 将其用作 CI/CD 工作流内的任务以流的开头开始，请使用 Vm 作为生成计算机、 测试机或基础结构，然后完成该过程时停止 Vm。 此示例将使用 Azure 开发测试实验室的自定义映像工厂。  

## <a name="azure-powershell"></a>Azure PowerShell
以下 PowerShell 脚本在实验室中启动 VM。 [调用 AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azurermps-6.13.0)此脚本的主要焦点。 **ResourceId**参数是在实验室中 VM 的完全限定的资源 ID。 **操作**参数是位置**启动**或**停止**具体取决于所需设置选项。

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
if ($(Get-Module -Name AzureRM).Version.Major -eq 6) {
    $devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -Name $devTestLabName
} else {
    $devTestLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $devTestLabName
}

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>Azure CLI
[Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)是另一种方法来自动执行的开始和停止的开发测试实验室 Vm。 可以是 azure CLI[安装](/cli/azure/install-azure-cli?view=azure-cli-latest)不同操作系统上。 以下脚本为你提供了命令用于启动和停止实验室中的 VM。 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>后续步骤
请参阅以下文章了解使用 Azure 门户来执行这些操作：[重新启动 VM](devtest-lab-restart-vm.md)。
