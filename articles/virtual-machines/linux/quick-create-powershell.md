---
title: 快速入门 - 使用 Azure PowerShell 创建 Linux VM | Microsoft Docs
description: 本快速入门介绍如何使用 Azure PowerShell 创建 Linux 虚拟机
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e910ced35738fcba27a8a1d7f2f010b2cd42e55d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32188984"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>快速入门：使用 PowerShell 在 Azure 中创建 Linux 虚拟机

Azure PowerShell 模块用于从 PowerShell 命令行或脚本创建和管理 Azure 资源。 本快速入门展示了如何使用 Azure PowerShell 模块在 Azure 中部署运行 Ubuntu 的 Linux 虚拟机 (VM)。 若要查看运行中的 VM，可以通过 SSH 登录到该 VM 并安装 NGINX Web 服务器。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 5.7.0 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzureRmAccount` 以创建与 Azure 的连接。

最后，需要在 Windows 用户配置文件的 *.ssh* 目录中存储名为 *id_rsa.pub* 的公共 SSH 密钥。 有关如何创建和使用 SSH 密钥的详细信息，请参阅[创建适用于 Azure 的 SSH 密钥](ssh-from-windows.md)。

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器：

```azurepowershell-interactive
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>创建虚拟网络资源

创建虚拟网络、子网和公用 IP 地址。 这些资源用来与 VM 建立网络连接，以及将其连接到 Internet：

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-Name "myVNET" -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

创建 Azure 网络安全组和流量规则。 网络安全组使用入站和出站规则来保护 VM。 在下面的示例中，将为 TCP 端口 22 创建允许 SSH 连接的入站规则。 为允许传入的 Web 流量，还将为 TCP 端口 80 创建一个入站规则。

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRuleSSH"  -Protocol "Tcp" `
-Direction "Inbound" -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRuleWWW"  -Protocol "Tcp" `
-Direction "Inbound" -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access "Allow"

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-Name "myNetworkSecurityGroup" -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

使用 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) 创建虚拟网络接口卡 (NIC)。 虚拟 NIC 将 VM 连接到子网、网络安全组和公用 IP 地址。

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name "myNic" -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

虚拟机配置包括部署 VM 时使用的设置，例如 VM 映像、大小和身份验证选项。 定义 SSH 凭据、OS 信息和 VM 大小，如下所示：

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_D1" | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName "myVM" -Credential $cred -DisablePasswordAuthentication | `
Set-AzureRmVMSourceImage -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS" -Version "latest" | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"
```

现在，组合前面的配置定义来使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建虚拟机：

```azurepowershell-interactive
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location eastus -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>连接到虚拟机

在部署完成后，通过 SSH 登录到 VM。 若要查看运行中的 VM，请安装 NGINX 服务器。

若要查看 VM 的公用 IP 地址，请使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) cmdlet：

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

使用 SSH 客户端连接到 VM。 可以通过 Web 浏览器使用 Azure Cloud Shell，如果使用 Windows，则可以使用 [Putty](ssh-from-windows.md) 或[适用于 Linux 的 Windows 子系统](/windows/wsl/install-win10)。 提供 VM 的公用 IP 地址：

```bash
ssh azureuser@IpAddress
```

出现提示时，请输入登录用户名 *azureuser*。 如果将通行短语与 SSH 密钥配合使用，则需要在出现提示时将其输入。

## <a name="install-web-server"></a>安装 Web 服务器

若要查看运行中的 VM，请安装 NGINX Web 服务器。 若要更新包源并安装最新的 NGINX 包，请从 SSH 会话运行以下命令：

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

完成后，`exit` SSH 会话

## <a name="view-the-web-server-in-action"></a>查看运行中的 Web 服务器

NGINX 已安装，并且现在已从 Internet 打开 VM 上的端口 80 - 可以使用所选的 Web 浏览器查看默认的 NGINX 欢迎页。 使用上一步中获取的 VM 的公用 IP 地址。 以下示例演示了默认 NGINX 网站：

![NGINX 默认站点](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要、则可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) cmdlet 删除资源组、VM 和所有相关资源：

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你部署了一台简单的虚拟机、一条网络安全组规则组和规则，并安装了一台基本 Web 服务器。 若要详细了解 Azure 虚拟机，请继续学习 Linux VM 的教程。

> [!div class="nextstepaction"]
> [Azure Linux 虚拟机教程](./tutorial-manage-vm.md)
