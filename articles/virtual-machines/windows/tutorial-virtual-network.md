---
title: "Azure 虚拟网络和 Windows 虚拟机 | Microsoft Docs"
description: "教程 - 使用 Azure PowerShell 管理 Azure 虚拟网络和 Windows 虚拟机"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/18/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: e1b3e9756e149c5cba67f8b5c37e1d153dbf81ab
ms.contentlocale: zh-cn
ms.lasthandoff: 05/03/2017

---

# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>使用 Azure PowerShell 管理 Azure 虚拟网络和 Windows 虚拟机

本教程介绍如何在虚拟网络 (VNet) 中创建多个虚拟机 (VM)，并在虚拟机之间配置网络连接。 完成后，可通过 Internet 在用于 HTTP 连接的端口 80 上访问“前端”VM。 含有 SQL Server 数据库的“后端”VM 将被隔离，并且只能通过端口 1433 上的前端 VM 访问。

可使用最新版 [Azure PowerShell](/powershell/azure/overview) 模块完成本教程中的步骤。

## <a name="create-vnet"></a>创建 VNet

VNet 是你自己的网络在云中的表示形式。 VNet 是对专用于你的订阅的 Azure 云进行的逻辑隔离。 在 VNet 中，可发现子网、连接到这些子网的规则，以及从 VM 到子网的连接。

创建任何其他 Azure 资源前，需要使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 创建一个资源组。 以下示例在“westus”位置创建名为“myRGNetwork”的资源组：

```powershell
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location westus
```

子网是 VNet 的子资源，可帮助定义使用 IP 地址前缀在 CIDR 块中定义地址空间的段。 可以将 NIC 添加到子网，并连接到 VM，以便为各种工作负荷提供连接。

使用 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) 创建子网：

```powershell
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

通过 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) 使用 myFrontendSubnet 创建名为“myVNet”的 VNET：

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet
```

## <a name="create-front-end-vm"></a>创建前端 VM

VM 需要虚拟网络接口 (NIC) 才能在 VNet 中进行通信。 由于将通过 Internet 访问 myFrontendVM，因此它也需要公共 IP 地址。 

使用 [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) 创建一个公共 IP 地址：

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

使用 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) 创建 NIC：


```powershell
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myFrontendNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

使用 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 设置 VM 上管理员帐户所需的用户名和密码：

```powershell
$cred = Get-Credential
```

使用 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig)、[Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem)、[Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage)、[Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk)、[Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) 和 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建 VM。 

```powershell
$frontendVM = New-AzureRmVMConfig `
    -VMName myFrontendVM `
    -VMSize Standard_D1
$frontendVM = Set-AzureRmVMOperatingSystem `
    -VM $frontendVM `
    -Windows `
    -ComputerName myFrontendVM `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$frontendVM = Set-AzureRmVMSourceImage `
    -VM $frontendVM `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
$frontendVM = Set-AzureRmVMOSDisk `
    -VM $frontendVM `
    -Name myFrontendOSDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
$frontendVM = Add-AzureRmVMNetworkInterface `
    -VM $frontendVM `
    -Id $frontendNic.Id
New-AzureRmVM `
    -ResourceGroupName myRGNetwork `
    -Location westus `
    -VM $frontendVM
```

## <a name="install-web-server"></a>安装 Web 服务器

可使用远程桌面会话在 myFrontendVM 上安装 IIS。 需要获取 VM 的公共 IP 地址才能对其进行访问。

可使用 [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 获取 *myFrontendVM* 的公共 IP 地址。 以下示例获取前面创建的“myPublicIPAddress”的 IP 地址：

```powershell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName myRGNetwork `
    -Name myPublicIPAddress | select IpAddress
```

请记下此 IP 地址，以便在后续步骤中使用。

使用以下命令创建与 myFrontendVM 的远程桌面会话。 将 <publicIPAddress> 替换为之前记录的地址。 出现提示时，输入创建 VM 时使用的凭据。

```
mstsc /v:<publicIpAddress>
``` 

登录 myFrontendVM 后即可使用单行 PowerShell 安装 IIS，并启用本地防火墙规则以允许 Web 流量。 打开 PowerShell 提示符并运行以下命令：

使用 [Install-WindowsFeature](https://technet.microsoft.com/itpro/powershell/windows/servermanager/install-windowsfeature) 运行安装 IIS web 服务器的自定义脚本扩展：

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

现可使用公共 IP 地址浏览到要查看 IIS 站点的 VM。

![IIS 默认站点](./media/tutorial-virtual-network/iis.png)

## <a name="manage-internal-traffic"></a>管理内部流量

网络安全组 (NSG) 包含一系列安全规则，这些规则可允许或拒绝流向连接到 VNet 的资源的网络流量。 NSG 可与子网或附加到 VM 的单个 NIC 相关联。 使用 NSG 规则打开或关闭通过端口到 VM 的访问。 创建 myFrontendVM 后，会自动打开入站端口 3389 进行 RDP 连接。

可使用 NSG 配置 VM 的内部通信。 本部分介绍如何在网络中创建附加子网，并向其分配 NSG 以允许端口 1433 上 myFrontendVM 到 myBackendVM 的连接。 然后，在创建子网时将其分配给 VM。

可通过为后端子网创建 NSG，将内部流量限制为仅从 myFrontendVM 流向 myBackendVM。 以下示例使用 [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) 创建名为“myBackendNSGRule”的 NSG 规则：

```powershell
$nsgBackendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

使用 *New-AzureRmNetworkSecurityGroup[ 添加名为“myBackendNSG”*](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)的新网络安全组：

```powershell
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```
## <a name="add-back-end-subnet"></a>添加后端子网

使用 [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) 将 myBackEndSubnet 添加到 myVNet：

```powershell
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -VirtualNetwork $vnet `
  -AddressPrefix 10.0.1.0/24 `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
```

## <a name="create-back-end-vm"></a>创建后端 VM

创建后端 VM 的最简单方法是使用 SQL Server 映像。 本教程仅使用数据库服务器创建 VM，但不提供有关访问数据库的信息。

创建 myBackendNic：

```powershell
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myBackendNic `
  -SubnetId $vnet.Subnets[1].Id
```

使用 Get-Credential 设置 VM 上管理员帐户所需的用户名和密码：

```powershell
$cred = Get-Credential
```

创建 myBackendVM：

```powershell
$backendVM = New-AzureRmVMConfig `
  -VMName myBackendVM `
  -VMSize Standard_D1
$backendVM = Set-AzureRmVMOperatingSystem `
  -VM $backendVM `
  -Windows `
  -ComputerName myBackendVM `
  -Credential $cred `
  -ProvisionVMAgent `
  -EnableAutoUpdate
$backendVM = Set-AzureRmVMSourceImage `
  -VM $backendVM `
  -PublisherName MicrosoftSQLServer `
  -Offer SQL2016-WS2016 `
  -Skus Enterprise `
  -Version latest
$backendVM = Set-AzureRmVMOSDisk `
  -VM $backendVM `
  -Name myBackendOSDisk `
  -DiskSizeInGB 128 `
  -CreateOption FromImage `
  -Caching ReadWrite
$backendVM = Add-AzureRmVMNetworkInterface `
  -VM $backendVM `
  -Id $backendNic.Id
New-AzureRmVM `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -VM $backendVM
```

虽然使用的映像安装了 SQL Server，但本教程中不会使用 SQL Server。 安装它是为了演示如何配置处理 Web 流量的 VM 和处理数据库管理的 VM。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建并保护与虚拟机相关的 Azure 网络。 前往下一教程以了解使用 Azure 安全中心监视 VM 的相关信息。

[管理虚拟机安全](./tutorial-azure-security.md)
