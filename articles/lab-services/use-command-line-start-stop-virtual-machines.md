---
title: 使用命令行工具启动和停止 VM Azure 开发人员测试实验室
description: 了解如何使用命令行工具在 Azure 开发人员测试实验室中启动和停止虚拟机。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169251"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>使用命令行工具启动和停止 Azure 开发人员测试实验室虚拟机
本文介绍如何使用 Azure PowerShell 或 Azure CLI 在 Azure DevTest 实验室的实验室中启动或停止虚拟机。 您可以创建 PowerShell/CLI 脚本来自动执行这些操作。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概述
Azure 开发人员测试实验室是创建快速、简单和精益开发/测试环境的一种方式。 它允许您管理成本、快速预配 VM 和尽量减少浪费。  Azure 门户中有一些内置功能，允许您在实验室中配置 VM 以在特定时间自动启动和停止 VM。 

但是，在某些情况下，您可能希望自动从 PowerShell/CLI 脚本启动和停止 VM。 它为您提供了一些灵活性，可随时启动和停止单个机器，而不是在特定时间。 下面是使用脚本运行这些任务的一些情况。

- 将 3 层应用程序用作测试环境的一部分时，需要按顺序启动层。 
- 满足自定义条件以节省资金时关闭 VM。 
- 将其用作 CI/CD 工作流中的任务，从流的开始开始，将 VM 用作生成计算机、测试计算机或基础结构，然后在该过程完成后停止 VM。 例如，使用 Azure 开发人员测试实验室的自定义映像工厂。  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> 以下脚本使用 Azure PowerShell Az 模块。 

以下 PowerShell 脚本在实验室中启动 VM。 [调用-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0)是此脚本的主要焦点。 **ResourceId**参数是实验室中 VM 完全限定的资源 ID。 **"操作"** 参数是根据所需内容设置 **"开始**"或 **"停止"** 选项的位置。

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
[Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)是自动启动和停止 DevTest 实验室 VM 的另一种方法。 Azure CLI 可以[安装在](/cli/azure/install-azure-cli?view=azure-cli-latest)不同的操作系统上。 以下脚本提供了在实验室中启动和停止 VM 的命令。 

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
有关使用 Azure 门户执行这些操作，请参阅以下文章：[重新启动 VM](devtest-lab-restart-vm.md)。
