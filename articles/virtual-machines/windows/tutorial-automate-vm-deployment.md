---
title: "在 Azure 中自定义 Windows VM | Microsoft Docs"
description: "了解如何使用自定义脚本扩展，在 Azure 中的 Windows VM 上自动完成应用程序安装"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 63858da0a4a47d67ec659e922ab10f9f7bc97938
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2018
---
# <a name="how-to-customize-a-windows-virtual-machine-in-azure"></a>如何在 Azure 中自定义 Windows 虚拟机
若要以快速一致的方式配置虚拟机 (VM)，通常需要某种形式的自动化。 自定义 Windows VM 的一种常用方法是使用[适用于 Windows 的自定义脚本扩展](extensions-customscript.md)。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用自定义脚本扩展安装 IIS
> * 创建使用自定义脚本扩展的 VM
> * 在应用扩展后查看正在运行的 IIS 站点

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 5.3 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。 


## <a name="custom-script-extension-overview"></a>自定义脚本扩展概述
自定义脚本扩展在 Azure VM 上下载和执行脚本。 此扩展适用于部署后配置、软件安装或其他任何配置/管理任务。 可以从 Azure 存储或 GitHub 下载脚本，或者在扩展运行时会脚本提供给 Azure 门户。

自定义脚本扩展与 Azure 资源管理器模板集成，也可以使用 Azure CLI、PowerShell、Azure 门户或 Azure 虚拟机 REST API 来运行它。

自定义脚本扩展适用于 Windows 和 Linux VM。


## <a name="create-virtual-machine"></a>创建虚拟机
首先，使用 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 设置 VM 的管理员用户名和密码：

```azurepowershell-interactive
$cred = Get-Credential
```

现在，可使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建 VM。 以下示例在“EastUS”位置创建一个名为 myVM 的 VM。 如果资源组 *myResourceGroupAutomate* 和支持的网络资源不存在，则会创建它们。 此 cmdlet 还打开端口 *80*，目的是允许 Web 流量。

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

创建资源和 VM 需要几分钟的时间。


## <a name="automate-iis-install"></a>自动安装 IIS
使用 [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) 安装自定义脚本扩展。 该扩展运行 `powershell Add-WindowsFeature Web-Server` 以安装 IIS Web 服务器，并更新“Default.htm”页以显示 VM 的主机名：

```azurepowershell-interactive
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>测试网站
使用 [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 获取负载均衡器的公共 IP 地址。 以下示例获取前面创建的“myPublicIPAddress”的 IP 地址：

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

然后，可将公共 IP 地址输入 web 浏览器中。 随即显示网站，包括负载均衡器将流量分发到的 VM 的主机名，如下例所示：

![运行 IIS 网站](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>后续步骤

在本教程中，会在 VM 上自动执行 IIS 安装。 你已了解如何：

> [!div class="checklist"]
> * 使用自定义脚本扩展安装 IIS
> * 创建使用自定义脚本扩展的 VM
> * 在应用扩展后查看正在运行的 IIS 站点

转到下一教程，了解如何创建自定义 VM 映像。

> [!div class="nextstepaction"]
> [创建自定义 VM 映像](./tutorial-custom-images.md)
