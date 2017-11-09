---
title: "在 Azure Cloud Shell 中使用 New-AzVM cmdlet 创建 Windows VM | Microsoft Docs"
description: "快速了解如何在 Azure Cloud Shell 中使用 New-AzVM cmdlet 创建 Windows 虚拟机。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/21/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 3be46c8c02ad136edb1936fbb39560d479b27277
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-with-the-new-azvm-preview-in-cloud-shell"></a>在 Azure Cloud Shell 中使用 New-AzVM（预览版）创建 Windows 虚拟机 

New-AzVM（预览版）cmdlet 是使用 PowerShell 创建新 VM的简化方法。 本指南详细介绍如何在预装了 New-AzVM cmdlet 的 Azure Cloud Shell 中，使用 PowerShell 创建运行 Windows Server 2016 的新 Azure 虚拟机。 部署完成后，我们使用 RDP 连接到服务器。  

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

## <a name="create-the-vm"></a>创建 VM

可以使用 **New-AzVM** cmdlet 创建具有智能默认设置（包括使用 Azure Marketplace 中的 Windows Server 2016 Datacenter 映像）的 VM。 可以单独使用 New-AzVM，在这种情况下，它会对资源名称使用默认值。 在本示例中，我们将设置 **-Name** 参数设置为 *myVM*。 该 cmdlet 使用 *myVM* 作为资源名称的前缀创建所有所需的资源。 

请确保在 Cloud Shell 中选择 **PowerShell** 并键入：

```azurepowershell-interactive
New-AzVm -Name myVM
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

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和所有相关的资源，可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令将其删除。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVMResourceGroup
```

## <a name="next-steps"></a>后续步骤

本主题使用 New-AzVM 部署了一个简单的虚拟机，然后通过 RDP 连接到该虚拟机。 若要详细了解 Azure 虚拟机，请继续学习 Windows VM 的教程。

> [!div class="nextstepaction"]
> [Azure Windows 虚拟机教程](./tutorial-manage-vm.md)
