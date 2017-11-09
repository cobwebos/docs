---
title: "使用 New-AzVM 在 Azure 中创建 IIS VM | Microsoft Docs"
description: "Azure PowerShell 脚本示例 - 使用 New-AzVM 创建运行 IIS 的 VM。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/29/2017
ms.author: cynthn
ms.openlocfilehash: 932920483e9196251b125065f8efe68d27dda559
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-iis-vm-using-the-new-azvm-powershell-cmdlet-preview"></a>使用 New-AzVM PowerShell cmdlet（预览版）创建 IIS VM

此脚本创建一个运行 Windows Server 2016 的 Azure 虚拟机，并使用 Azure 虚拟机自定义脚本扩展安装 IIS。 运行此脚本后，可通过虚拟机的公共 IP 地址访问默认 IIS 网站。 

此示例使用 New-AzVM cmdlet（预览版）。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

## <a name="sample-script"></a>示例脚本

```azurepowershell-interactive

New-AzVM -Name myVM `
    -VirtualNetworkName myVNet `
    -Location westeurope `
    -SecurityGroupName myNSG `
    -PublicIpAddressName myPIP


# Create an inbound network security group rule for port 80

$nsgRuleHTTP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleHTTP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 80 -Access Allow

$nsg = Get-AzureRmNetworkSecurityGroup -Name myNSG -ResourceGroupName myVMResourceGroup
$nsg | Add-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleHTTP -NetworkSecurityGroup myNSG 
$nsg | Set-AzureRmNetworkSecurityGroup

# Install IIS

Set-AzureRmVMExtension -ExtensionName "IIS" -ResourceGroupName myVMResourceGroup -VMName myVM `
  -Publisher "Microsoft.Compute" -ExtensionType "CustomScriptExtension" -TypeHandlerVersion 1.4 `
  -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' -Location westeurope
  
```

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVMResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建部署。 表中的每一项均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | 创建网络安全组规则配置。 创建 NSG 时会使用此配置创建 NSG 规则。 |
| [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup) | 获取 NSG 信息。 |
| [Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig) | 将新规则添加到配置。 |
| [Set-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/Set-AzureRmNetworkSecurityGroup) | 使用新规则更新 NSG。 |
| [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) | 将 VM 扩展添加到虚拟机。 在此示例中，使用自定义脚本扩展来安装 IIS。 |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 删除资源组及其中包含的所有资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure Windows VM 文档](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
