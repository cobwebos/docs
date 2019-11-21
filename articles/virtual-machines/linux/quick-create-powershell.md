---
title: 快速入门 - 使用 Azure PowerShell 创建 Linux VM
description: 本快速入门介绍如何使用 Azure PowerShell 创建 Linux 虚拟机
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/17/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1e336ad05d70341365b63d806b249740113724d3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035191"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>快速入门：使用 PowerShell 在 Azure 中创建 Linux 虚拟机

Azure PowerShell 模块用于从 PowerShell 命令行或脚本创建和管理 Azure 资源。 本快速入门展示了如何使用 Azure PowerShell 模块在 Azure 中部署 Linux 虚拟机 (VM)。 本快速入门使用 Canonical 提供的 Ubuntu 16.04 LTS 市场映像。 若要查看运行中的 VM，也可以通过 SSH 登录到该 VM 并安装 NGINX Web 服务器。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。  选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。 

## <a name="create-ssh-key-pair"></a>创建 SSH 密钥对

需要一个 SSH 密钥对才能完成本快速入门。 如果已有一个 SSH 密钥对，则可以跳过此步骤。

打开 bash shell，使用 [ssh-keygen](https://www.ssh.com/ssh/keygen/) 创建一个 SSH 密钥对。 如果本地计算机上没有 bash shell，则可使用 [Azure Cloud Shell](https://shell.azure.com/bash)。  

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```

有关如何创建 SSH 密钥对的更多详细信息，包括 PuTTy 的用法，请参阅[如何将 SSH 密钥与 Windows 配合使用](ssh-from-windows.md)。

如果使用 Cloud Shell 创建 SSH 密钥对，则会将密钥对存储在[由 Cloud Shell 自动创建的存储帐户](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)的容器映像中。 在检索密钥之前，请勿删除此存储帐户或其中的文件共享，否则将无法访问 VM。 

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器：

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>创建虚拟网络资源

创建虚拟网络、子网和公共 IP 地址。 这些资源用来与 VM 建立网络连接，以及将其连接到 Internet：

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myVNET" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

创建 Azure 网络安全组和流量规则。 网络安全组使用入站和出站规则来保护 VM。 在下面的示例中，将为 TCP 端口 22 创建允许 SSH 连接的入站规则。 为允许传入的 Web 流量，还将为 TCP 端口 80 创建一个入站规则。

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleSSH"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 22 `
  -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWWW"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access "Allow"

# Create a network security group
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myNetworkSecurityGroup" `
  -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

使用 [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) 创建虚拟网络接口卡 (NIC)。 虚拟 NIC 将 VM 连接到子网、网络安全组和公用 IP 地址。

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface `
  -Name "myNic" `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

若要在 PowerShell 中创建 VM，请创建一个配置，其中包含要使用的映像、大小和身份验证选项等设置。 然后，系统会使用此配置来生成 VM。

定义 SSH 凭据、OS 信息和 VM 大小。 在此示例中，SSH 密钥存储在 `~/.ssh/id_rsa.pub` 中。 

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig `
  -VMName "myVM" `
  -VMSize "Standard_D1" | `
Set-AzVMOperatingSystem `
  -Linux `
  -ComputerName "myVM" `
  -Credential $cred `
  -DisablePasswordAuthentication | `
Set-AzVMSourceImage `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest" | `
Add-AzVMNetworkInterface `
  -Id $nic.Id

# Configure the SSH key
$sshPublicKey = cat ~/.ssh/id_rsa.pub
Add-AzVMSshPublicKey `
  -VM $vmconfig `
  -KeyData $sshPublicKey `
  -Path "/home/azureuser/.ssh/authorized_keys"
```

现在，组合前面的配置定义来使用 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 创建虚拟机：

```azurepowershell-interactive
New-AzVM `
  -ResourceGroupName "myResourceGroup" `
  -Location eastus -VM $vmConfig
```

部署 VM 需要数分钟。 部署完成后，请转到下一部分。

## <a name="connect-to-the-vm"></a>连接到 VM

使用公共 IP 地址创建与 VM 的 SSH 连接。 若要查看 VM 的公用 IP 地址，请使用 [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) cmdlet：

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

使用创建 SSH 密钥对时使用过的 bash shell（例如 [Azure Cloud Shell](https://shell.azure.com/bash) 或本地 bash shell）将 SSH 连接命令粘贴到 shell 中，以便创建一个 SSH 会话。

```bash
ssh azureuser@10.111.12.123
```

出现提示时，请输入登录用户名 *azureuser*。 如果将通行短语与 SSH 密钥配合使用，则需要在出现提示时将其输入。


## <a name="install-nginx"></a>安装 NGINX

若要查看运行中的 VM，请安装 NGINX Web 服务器。 在 SSH 会话中更新包源，然后安装最新的 NGINX 包。

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

完成后，键入 `exit` 以离开 SSH 会话。


## <a name="view-the-web-server-in-action"></a>查看运行中的 Web 服务器

使用所选的 Web 浏览器查看默认的 NGINX 欢迎页。 输入 VM 的公共 IP 地址作为 Web 地址。 可以在 VM 概览页上或此前使用过的 SSH 连接字符串中找到公共 IP 地址。

![NGINX 默认欢迎页](./media/quick-create-cli/nginix-welcome-page.png)

## <a name="clean-up-resources"></a>清理资源

不再需要时，可以使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) cmdlet 删除资源组、VM 和所有相关资源：

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你部署了一台简单的虚拟机、一条网络安全组规则组和规则，并安装了一台基本 Web 服务器。 若要详细了解 Azure 虚拟机，请继续学习 Linux VM 的教程。

> [!div class="nextstepaction"]
> [Azure Linux 虚拟机教程](./tutorial-manage-vm.md)
