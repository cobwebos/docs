---
title: 教程 - 在 Azure 中的 Windows VM 上安装应用程序 | Microsoft Docs
description: 本教程介绍如何使用自定义脚本扩展运行脚本并将应用程序部署到 Azure 中的 Windows 虚拟机
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 354625accb39344d07a22f2d3935cf4cf022d491
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977654"
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>教程 - 使用自定义脚本扩展将应用程序部署到 Azure 中的 Windows 虚拟机

若要以快速一致的方式配置虚拟机 (VM)，可以使用[适用于 Windows 的自定义脚本扩展](extensions-customscript.md)。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用自定义脚本扩展安装 IIS
> * 创建使用自定义脚本扩展的 VM
> * 在应用扩展后查看正在运行的 IIS 站点

## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。 也可以通过转到 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) 在单独的浏览器标签页中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。

## <a name="custom-script-extension-overview"></a>自定义脚本扩展概述
自定义脚本扩展在 Azure VM 上下载和执行脚本。 此扩展适用于部署后配置、软件安装或其他任何配置/管理任务。 可以从 Azure 存储或 GitHub 下载脚本，或者在扩展运行时会脚本提供给 Azure 门户。

自定义脚本扩展与 Azure 资源管理器模板集成，也可以使用 Azure CLI、PowerShell、Azure 门户或 Azure 虚拟机 REST API 来运行它。

自定义脚本扩展适用于 Windows 和 Linux VM。


## <a name="create-virtual-machine"></a>创建虚拟机
使用 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 设置 VM 的管理员用户名和密码：

```azurepowershell-interactive
$cred = Get-Credential
```

现在，可使用 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 创建 VM。 以下示例在“EastUS”位置创建一个名为 myVM 的 VM。 如果资源组 *myResourceGroupAutomate* 和支持的网络资源不存在，则会创建它们。 此 cmdlet 还打开端口 *80*，目的是允许 Web 流量。

```azurepowershell-interactive
New-AzVm `
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
使用 [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) 安装自定义脚本扩展。 该扩展运行 `powershell Add-WindowsFeature Web-Server` 以安装 IIS Web 服务器，并更新“Default.htm”页以显示 VM 的主机名：

```azurepowershell-interactive
Set-AzVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>测试网站
使用 [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) 获取负载均衡器的公共 IP 地址。 以下示例获取前面创建的“myPublicIPAddress”的 IP 地址：

```azurepowershell-interactive
Get-AzPublicIPAddress `
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
