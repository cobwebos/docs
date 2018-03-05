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
ms.date: 02/27/2018
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: 3a59d85ea19ba6670ffbb60aa9b764560a3567a0
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>使用 Azure PowerShell 管理 Azure 虚拟网络和 Windows 虚拟机

Azure 虚拟机使用 Azure 网络进行内部和外部网络通信。 本教程会指导读者部署两个虚拟机，并为这些 VM 配置 Azure 网络。 本教程中的示例假设 VM 将要托管包含数据库后端的 Web 应用程序，但本教程并未介绍如何部署应用程序。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建虚拟网络和子网
> * 创建公共 IP 地址
> * 创建前端 VM
> * 保护网络流量的安全
> * 创建后端 VM



本教程需要 AzureRM.Compute 模块版本 4.3.1 或更高版本。 运行 `Get-Module -ListAvailable AzureRM.Compute` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

## <a name="vm-networking-overview"></a>VM 网络概述

Azure 虚拟网络在虚拟机、Internet 与其他 Azure 服务（例如 Azure SQL 数据库）之间实现安全网络连接。 虚拟网络分解为称作“子网”的逻辑段。 子网用于控制网络流，并充当安全边界。 部署 VM 时，该 VM 通常包含一个附加到子网的虚拟网络接口。

完成本教程时，可以看到创建了以下资源：

![包含两个子网的虚拟网络](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* - VM 彼此进行通信以及与 Internet 进行通信时使用的虚拟网络。
- *myFrontendSubnet* - *myVNet* 中供前端资源使用的子网。
- *myPublicIPAddress* - 用于从 Internet 访问 *myFrontendVM* 的公共 IP 地址。
- *myFrontentNic* - *myFrontendVM* 用来与 *myBackendVM* 进行通信的网络接口。
- *myFrontendVM* - 用于在 Internet 和 *myBackendVM* 之间进行通信的 VM。
- *myBackendNSG* - 控制 *myFrontendVM* 和 *myBackendVM* 之间的通信的网络安全组。
- *myBackendSubnet* - 与 *myBackendNSG* 关联且供后端资源使用的子网。
- *myBackendNic* - *myBackendVM* 用于与 *myFrontendVM* 通信的网络接口。
- *myBackendVM* - 使用端口 1433 与 *myFrontendVM* 通信的 VM。


## <a name="create-a-virtual-network-and-subnet"></a>创建虚拟网络和子网

本教程会创建包含两个子网的单个虚拟网络。 一个前端子网用于托管 Web 应用程序，一个后端子网用于托管数据库服务器。

创建虚拟网络之前，需使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 创建资源组。 以下示例在“EastUS”位置创建名为 *myRGNetwork* 的资源组：

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

### <a name="create-subnet-configurations"></a>创建子网配置

使用 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) 创建一个名为 *myFrontendSubnet* 的子网配置：

```azurepowershell-interactive
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

然后，创建一个名为 *myBackendSubnet* 的子网配置：

```azurepowershell-interactive
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

### <a name="create-virtual-network"></a>创建虚拟网络

通过 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) 使用 *myFrontendSubnet* 和 *myBackendSubnet* 创建名为“myVNet”的 VNET：

```azurepowershell-interactive
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

此时，已创建一个网络并将其分段为两个子网，其中一个子网用于前端服务，另一个用于后端服务。 下一部分将创建虚拟机并将其连接到这些子网。

## <a name="create-a-public-ip-address"></a>创建公共 IP 地址

使用公共 IP 地址可在 Internet 上访问 Azure 资源。 公共 IP 地址的分配方法可以配置为动态或静态。 默认情况下，将动态分配公共 IP 地址。 解除分配 VM 时，将释放动态 IP 地址。 在执行涉及到 VM 解除分配的任何操作期间，此行为会导致 IP 地址发生更改。

可将分配方法设置为静态，这可确保分配给 VM 的 IP 地址保持不变，即使该 VM 处于解除分配状态也是如此。 使用静态分配的 IP 地址时，无法指定 IP 地址本身。 该地址是从可用地址池中分配的。

使用 [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) 创建名为 *myPublicIPAddress* 的公共 IP 地址：

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

可以将 -AllocationMethod 参数更改为 `Static`，以分配静态公共 IP 地址。

## <a name="create-a-front-end-vm"></a>创建前端 VM

VM 需要虚拟网络接口 (NIC) 才能在虚拟网络中进行通信。 使用 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) 创建 NIC：

```azurepowershell-interactive
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

使用 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 设置 VM 上管理员帐户所需的用户名和密码。 若要使用这些凭据连接到 VM，请执行其他步骤：

```azurepowershell-interactive
$cred = Get-Credential
```

使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建 VM。

```azurepowershell-interactive
New-AzureRmVM `
   -Credential $cred `
   -Name myFrontend `
   -PublicIpAddressName myPublicIPAddress `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -Size Standard_D1 `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

## <a name="secure-network-traffic"></a>保护网络流量的安全

网络安全组 (NSG) 包含一系列安全规则，这些规则可以允许或拒绝流向连接到 Azure 虚拟网络 (VNet) 的资源的网络流量。 NSG 可以关联到子网或单个网络接口。 当 NSG 与网络接口关联时，NSG 只会应用到关联的 VM。 将 NSG 关联到子网时，规则适用于连接到该子网的所有资源。

### <a name="network-security-group-rules"></a>网络安全组规则

NSG 规则定义要允许或拒绝哪些网络端口上的流量。 这些规则可以包括源和目标 IP 地址范围，以便控制特定系统或子网之间的流量。 NSG 规则还包括优先级（介于 1 和 4096 之间）。 将按优先级顺序来评估规则。 优先级为 100 的规则会在优先级为 200 的规则之前评估。

所有 NSG 都包含一组默认规则。 默认规则无法删除，但由于给它们分配的优先级最低，可以用创建的规则来重写它们。

- **虚拟网络** - 从方向上来说，在虚拟网络中发起和结束的通信可以是入站通信，也可以是出站通信。
- **Internet** - 允许出站通信，但阻止入站通信。
- **负载均衡器** - 允许 Azure 的负载均衡器探测 VM 和角色实例的运行状况。 如果不使用负载均衡集，则可替代此规则。

### <a name="create-network-security-groups"></a>创建网络安全组

使用 [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) 创建名为 *myFrontendNSGRule* 的入站规则以允许 *myFrontendVM* 上的传入 Web 流量：

```azurepowershell-interactive
$nsgFrontendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

可通过为后端子网创建 NSG，将内部流量限制为仅从 myFrontendVM 流向 myBackendVM。 以下示例创建名为“myBackendNSGRule”的 NSG 规则：

```azurepowershell-interactive
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

使用 [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) 添加名为 *myFrontendNSG* 的网络安全组：

```azurepowershell-interactive
$nsgFrontend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

现在，使用 New-AzureRmNetworkSecurityGroup 添加名为 *myBackendNSG* 的网络安全组：

```azurepowershell-interactive
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

将网络安全组添加到子网：

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>创建后端 VM

创建本教程的后端 VM 的最简单方法是使用 SQL Server 映像。 本教程仅使用数据库服务器创建 VM，但不提供有关访问数据库的信息。

创建 myBackendNic：

```azurepowershell-interactive
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

使用 Get-Credential 设置 VM 上管理员帐户所需的用户名和密码：

```azurepowershell-interactive
$cred = Get-Credential
```

创建 myBackendVM。

```azurepowershell-interactive
New-AzureRmVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

虽然使用的映像安装了 SQL Server，但本教程中不会使用 SQL Server。 安装它是为了演示如何配置处理 Web 流量的 VM 和处理数据库管理的 VM。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建和保护与虚拟机相关的 Azure 网络。 

> [!div class="checklist"]
> * 创建虚拟网络和子网
> * 创建公共 IP 地址
> * 创建前端 VM
> * 保护网络流量的安全
> * 创建后端 VM

请继续学习下一教程，了解如何使用 Azure 备份监视和保护虚拟机上的数据。

> [!div class="nextstepaction"]
> [在 Azure 中备份 Windows 虚拟机](./tutorial-backup-vms.md)
