---
title: 使用命令行工具启动和停止 Vm Azure 开发测试实验室
description: 了解如何使用命令行工具在 Azure 开发测试实验室中启动和停止虚拟机。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: fd643559a09d5c75aad9be5f35c653994c8488cf
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169251"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>使用命令行工具启动和停止 Azure 开发测试实验室虚拟机
本文介绍如何使用 Azure PowerShell 或 Azure CLI 在 Azure 开发测试实验室中的实验室中启动或停止虚拟机。 你可以创建 PowerShell/CLI 脚本来自动执行这些操作。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概述
Azure 开发测试实验室是一种创建快速、简单和精益的开发/测试环境的方式。 它允许您管理成本、快速设置 Vm，并最大程度地减少浪费。  Azure 门户中提供了一些内置功能，可让你将实验室中的 Vm 配置为在特定时间自动启动和停止。 

但是，在某些情况下，你可能希望自动启动和停止 PowerShell/CLI 脚本中的 Vm。 它为您提供了一些灵活性，使您可以随时启动和停止单个计算机，而不是在特定时间启动和停止。 下面是使用脚本运行这些任务的一些情况非常有用。

- 在测试环境中使用第三层应用程序时，需要按顺序启动层。 
- 满足自定义条件时关闭 VM 以节省资金。 
- 将其作为 CI/CD 工作流中的任务，以从流的开头开始，使用 Vm 作为生成计算机、测试计算机或基础结构，然后在进程完成时停止 Vm。 例如，使用 Azure 开发测试实验室自定义映像工厂。  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> 下面的脚本使用 Azure PowerShell Az 模块。 

下面的 PowerShell 脚本在实验室中启动 VM。 [AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0)是此脚本的主要焦点。 **ResourceId**参数是实验室中 VM 的完全限定的资源 ID。 **操作**参数是根据所需的设置**开始**或**停止**选项的位置。

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
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

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
[Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)是自动执行开发测试实验室 vm 的另一种方法。 Azure CLI 可以[安装](/cli/azure/install-azure-cli?view=azure-cli-latest)在不同的操作系统上。 以下脚本提供了用于在实验室中启动和停止 VM 的命令。 

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
请参阅以下文章，了解如何使用 Azure 门户执行以下操作：[重新启动 VM](devtest-lab-restart-vm.md)。
