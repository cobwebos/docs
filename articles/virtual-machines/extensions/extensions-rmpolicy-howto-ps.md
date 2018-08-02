---
title: 使用 Azure Policy 限制 VM 扩展安装 | Microsoft Docs
description: 使用 Azure Policy 限制扩展部署。
services: virtual-machines-linux
documentationcenter: ''
author: zroiy
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz;cynthn
ms.openlocfilehash: b63bfba4c1d84480724c4b03891f068145109626
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411720"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>使用 Azure Policy 限制 Windows VM 上的扩展安装

如果想要阻止在 Windows VM 上使用或安装某些扩展，可以使用 PowerShell 创建 Azure Policy 以限制资源组中的 VM 扩展。 

本教程在 Cloud Shell 中使用 Azure PowerShell，后者已不断更新到最新版本。 如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 3.6 或更高版本。 运行 ` Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 

## <a name="create-a-rules-file"></a>创建规则文件

若要限制可以安装哪些扩展，需要使用[规则](/azure/azure-policy/policy-definition#policy-rule)来提供用于识别扩展的逻辑。

本示例演示如何通过在 Azure Cloud Shell 中创建规则文件来拒绝“Microsoft.Compute”发布的扩展，但如果在本地使用 PowerShell，也可以创建一个本地文件并将路径 ($home/clouddrive) 替换为计算机上本地文件的路径。

在 [Cloud Shell](https://shell.azure.com/powershell) 中，键入：

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

将以下 .json 的内容复制并粘贴到该文件。

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

完成后，按 **Ctrl + O**，然后按 **Enter** 保存该文件。 按 **Ctrl + X** 关闭文件并退出。

## <a name="create-a-parameters-file"></a>创建参数文件

还需要一个[参数](/azure/azure-policy/policy-definition#parameters)文件，以创建一个用于传入要阻止的扩展列表的结构。 

本示例演示如何在 Cloud Shell 中为 VM 创建参数文件，但如果在本地使用 PowerShell，也可以创建一个本地文件并将路径 ($home/clouddrive) 替换为计算机上本地文件的路径。

在 [Cloud Shell](https://shell.azure.com/powershell) 中，键入：

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

将以下 .json 的内容复制并粘贴到该文件。

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

完成后，按 **Ctrl + O**，然后按 **Enter** 保存该文件。 按 **Ctrl + X** 关闭文件并退出。

## <a name="create-the-policy"></a>创建策略

策略定义是用于存储想要使用的配置的对象。 策略定义使用规则和参数文件定义策略。 使用 [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) cmdlet 创建策略定义。

 策略规则和参数是在 cloud shell 中创建并存储为 .json 文件的文件。


```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>分配策略

此示例使用 [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) 将策略分配给资源组。 **myResourceGroup** 资源组中创建的任何 VM 将不能安装 VM 访问代理扩展或自定义脚本扩展。 

使用 [Get-AzureRMSubscription | Format-Table](/powershell/module/azurerm.profile/get-azurermsubscription) cmdlet 获取你的订阅 ID 以替换此示例中的订阅 ID。

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzureRMPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>测试策略

若要测试策略，请尝试使用 VM 访问扩展。 以下命令将失败并显示消息“Set-AzureRmVMAccessExtension：策略不允许使用资源 'myVMAccess'”。

```azurepowershell-interactive
Set-AzureRmVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

在门户中，密码更改将失败并显示消息“由于违反策略，模板部署失败” 。

## <a name="remove-the-assignment"></a>删除分配

```azurepowershell-interactive
Remove-AzureRMPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>删除策略

```azurepowershell-interactive
Remove-AzureRmPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅 [Azure Policy](../../azure-policy/azure-policy-introduction.md)。
