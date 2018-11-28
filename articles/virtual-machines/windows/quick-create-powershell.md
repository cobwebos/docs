---
title: 快速入门 - 使用 Azure PowerShell 创建 Windows虚拟机| Microsoft Docs
description: 本快速入门介绍了如何使用 Azure PowerShell 创建 Windows 虚拟机
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/04/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 3e797b801395bf4971bfb91a8ce4b35a710ea578
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816186"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-with-powershell"></a>快速入门：使用 PowerShell 在 Azure 中创建 Windows 虚拟机

Azure PowerShell 模块用于从 PowerShell 命令行或脚本创建和管理 Azure 资源。 这个快速入门演示如何使用 Azure PowerShell 模块在 Azure 中部署运行 Windows Server 2016 的虚拟机 (VM)。 若要查看虚拟机的运行情况，可以通过 RDP 登录到该 VM 并安装 IIS Web 服务器。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

Azure Cloud Shell 是一个免费的交互式 shell，可以使用它运行本文中的步骤。 它预先安装并配置了与您的帐户一起使用的常见Azure工具。 

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。 也可以通过转到 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) 在单独的浏览器选项卡中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按“回车键”来运行它。

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 5.7.0 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzureRmAccount` 来创建与 Azure 的连接。

## <a name="create-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-virtual-machine"></a>创建虚拟机

使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建虚拟机。 请为每个资源提供名称，如果这些资源不存在，[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) cmdlet 会创建它们。

出现提示时，请提供一个用户名和密码作为虚拟机的登录凭证：

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

在部署完成后，通过 RDP 登录到虚拟机。 若要查看虚拟机的运行情况，请安装 IIS Web 服务器。

若要查看虚拟机的公共 IP 地址，请使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) cmdlet：

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

使用以下命令从本地计算机创建远程桌面会话。 将 IP 地址替换为您的虚拟机的公用 IP 地址。 

```powershell
mstsc /v:publicIpAddress
```

在“Windows 安全性”窗口中，依次选择“更多选项”、“使用其他帐户”。 以 **本地主机**\\*用户名* 的形式键入用户名，输入为虚拟机创建的密码，然后单击“确定”。

你可能会在登录过程中收到证书警告。 单击“是”或“继续”以创建连接

## <a name="install-web-server"></a>安装 Web 服务器

若要查看虚拟机的运行情况，请安装 IIS Web 服务器。 在虚拟机中打开 PowerShell 提示符并运行以下命令：

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

完成后，关闭到虚拟机的 RDP 连接。

## <a name="view-the-web-server-in-action"></a>查看运行中的 Web 服务器

IIS 已安装，并且现在已从 Internet 打开虚拟机上的端口 80 - 可以使用所选的 Web 浏览器查看默认的 IIS 欢迎页面。 使用上一步中获取的虚拟机的公用 IP 地址。 以下示例展示了默认 IIS 网站：

![IIS 默认站点](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要、则可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) cmdlet 删除资源组、VM 和所有相关资源：

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>下一步步骤

在这个快速入门中，您部署了简单的虚拟机，为web流量打开了一个网络端口，并安装了一个基本 Web 服务器。 若要详细了解 Azure 虚拟机，请继续学习 Windows VM 的教程。

> [!div class="nextstepaction"]
> [Azure Windows 虚拟机教程](./tutorial-manage-vm.md)
