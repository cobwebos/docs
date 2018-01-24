---
title: "在 Azure Cloud Shell 中使用简化 New-AzureRMVM cmdlet 创建 Windows VM | Microsoft Docs"
description: "快速了解如何在 Azure Cloud Shell 中使用简化 New-AzureRMVM cmdlet 创建 Windows 虚拟机。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 94eb6232cf59d502a9d70545785c3788398f4d27
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2017
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azurermvm-cmdlet-in-cloud-shell"></a>在 Cloud Shell 中使用简化 New-AzureRMVM cmdlet 创建 Windows 虚拟机 

[New-AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) cmdlet 已添加一组简化的参数，用于使用 PowerShell 创建新的 VM。 本主题演示如何通过 Azure Cloud Shell 中的 PowerShell，使用预安装的最新版本 New-AzureVM cmdlet 创建新的 VM。 我们将使用简化的参数集，以使用智能默认设置自动创建所有必需的资源。 

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块版本 5.1.1 或更高版本。 运行 ` Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。

## <a name="create-the-vm"></a>创建 VM

可以使用 [New-AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) cmdlet 创建具有智能默认设置（包括使用 Azure Marketplace 中的 Windows Server 2016 Datacenter 映像）的 VM。 可以将 New-AzureRMVM 仅与 **-Name** 参数配合使用，它会将该值用于所有资源名称。 在本示例中，我们将设置 **-Name** 参数设置为 *myVM*。 

请确保在 Cloud Shell 中选择 **PowerShell** 并键入：

```azurepowershell-interactive
New-AzureRMVm -Name myVM
```

系统会要求为 VM 创建用户名和密码，稍后在本主题中连接到该 VM 时需要用到这些凭据。 密码长度必须为 12 到 123 个字符，并且必须满足以下 4 个复杂性要求的其中 3 个：1 个小写字符、1 个大写字符、1 个数字和 1 个特殊字符。

创建 VM 和关联的资源需要一分钟时间。 完成后，可以使用 [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource) cmdlet 查看创建的所有资源。

```azurepowershell-interactive
Find-AzureRmResource `
    -ResourceGroupNameEquals myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>连接到 VM

在部署完成后，创建到虚拟机的远程桌面连接。

使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 命令返回虚拟机的公共 IP 地址。 请记下此 IP 地址。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

在本地计算机上打开命令提示符，并使用 **mstsc** 命令开始与新 VM 建立远程桌面会话。 请将 &lt;publicIPAddress&gt; 替换为虚拟机的 IP 地址。 出现提示时，请输入创建 VM 时指定的用户名和密码。

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>指定不同的资源名称

还可以为资源提供更具描述性的名称，并仍将它们自动创建。 下面是一个示例，其中为新的 VM 命名了多个资源，包括新的资源组。

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 3389
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和所有相关的资源，可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令将其删除。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVM
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

本主题使用 New-AzVM 部署了一个简单的虚拟机，然后通过 RDP 连接到该虚拟机。 若要详细了解 Azure 虚拟机，请继续学习 Windows VM 的教程。

> [!div class="nextstepaction"]
> [Azure Windows 虚拟机教程](./tutorial-manage-vm.md)
