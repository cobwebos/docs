---
title: Azure 快速入门 - 创建 Windows VM PowerShell | Microsoft 文档
description: 快速了解如何使用 PowerShell 创建 Windows 虚拟机
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: f1ab714407bd686ac8bffb23c7ba9ec8951902ef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-windows-virtual-machine-with-powershell"></a>使用 PowerShell 创建 Windows 虚拟机

Azure PowerShell 模块用于从 PowerShell 命令行或脚本创建和管理 Azure 资源。 本快速入门详细说明了如何使用 PowerShell 创建运行 Windows Server 2016 的 Azure 虚拟机。 部署完成后，我们将连接到服务器并安装 IIS。  

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 5.3.0 或更高版本。 运行 ` Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。



## <a name="create-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```


## <a name="create-virtual-machine"></a>创建虚拟机

使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建虚拟机。 只需提供每个资源的名称即可，如果这些资源不存在，New-AzureRMVM cmdlet 会创建它们。

运行此步骤时，会提示输入凭据。 输入的值将配置为用于虚拟机的用户名和密码。

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389  
```

## <a name="connect-to-virtual-machine"></a>连接到虚拟机

在部署完成后，创建到虚拟机的远程桌面连接。

使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 命令返回虚拟机的公共 IP 地址。 需记下此 IP 地址，以便在后续步骤中使用浏览器连接到它测试 Web 连接。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

在本地计算机上使用以下命令创建与虚拟机的远程桌面会话。 将 IP 地址替换为虚拟机的 publicIPAddress。 出现提示时，输入创建虚拟机时使用的凭据。

```
mstsc /v:<publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>通过 PowerShell 安装 IIS

现在，已登录到 Azure VM，可以使用单行 PowerShell 安装 IIS，并启用本地防火墙规则以允许 Web 流量。 在 VM 中打开 PowerShell 提示符并运行以下命令：

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>查看 IIS 欢迎页

IIS 已安装，并且现在已从 Internet 打开 VM 上的端口 80 - 可以使用所选的 Web 浏览器查看默认的 IIS 欢迎页。 请务必使用前面记录的 *publicIpAddress* 访问默认页面。 

![IIS 默认站点](./media/quick-create-powershell/default-iis-website.png) 

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和所有相关的资源，可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令将其删除。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，部署了一台简单的虚拟机、一条网络安全组规则，并安装了一台 Web 服务器。 若要详细了解 Azure 虚拟机，请继续学习 Windows VM 的教程。

> [!div class="nextstepaction"]
> [Azure Windows 虚拟机教程](./tutorial-manage-vm.md)
