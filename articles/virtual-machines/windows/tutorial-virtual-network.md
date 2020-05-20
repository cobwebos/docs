---
title: 教程 - 为 Windows VM 创建和管理 Azure 虚拟网络
description: 本教程介绍如何使用 Azure PowerShell 为 Windows 虚拟机创建和管理 Azure 虚拟网络
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: networking
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: dff2acba63eaa2caabaddb0228424744be6ded16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82101682"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-windows-virtual-machines-with-azure-powershell"></a>教程：使用 Azure PowerShell 为 Windows 虚拟机创建和管理 Azure 虚拟网络

Azure 虚拟机使用 Azure 网络进行内部和外部网络通信。 本教程会指导读者部署两个虚拟机，并为这些 VM 配置 Azure 网络。 本教程中的示例假设 VM 将要托管包含数据库后端的 Web 应用程序，但本教程并未介绍如何部署应用程序。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建虚拟网络和子网
> * 创建公共 IP 地址
> * 创建前端 VM
> * 保护网络流量的安全
> * 创建后端 VM


## <a name="vm-networking-overview"></a>VM 网络概述

Azure 虚拟网络在虚拟机、Internet 与其他 Azure 服务（例如 Azure SQL 数据库）之间实现安全网络连接。 虚拟网络分解为称作“子网”的逻辑段。 子网用于控制网络流，并充当安全边界。 部署 VM 时，该 VM 通常包含一个附加到子网的虚拟网络接口。

完成本教程时，可以看到创建了以下资源：

![包含两个子网的虚拟网络](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* - VM 彼此进行通信以及与 Internet 进行通信时使用的虚拟网络。
- *myFrontendSubnet* - *myVNet* 中供前端资源使用的子网。
- *myPublicIPAddress* - 用于从 Internet 访问 *myFrontendVM* 的公共 IP 地址。
- *myFrontendNic* - *myFrontendVM* 用来与 *myBackendVM* 进行通信的网络接口。
- *myFrontendVM* - 用于在 Internet 和 *myBackendVM* 之间进行通信的 VM。
- *myBackendNSG* - 控制 *myFrontendVM* 和 *myBackendVM* 之间的通信的网络安全组。
- *myBackendSubnet* - 与 *myBackendNSG* 关联且供后端资源使用的子网。
- *myBackendNic* - *myBackendVM* 用于与 *myFrontendVM* 进行通信的网络接口。
- *myBackendVM* - 使用端口 1433 与 *myFrontendVM* 通信的 VM。


## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。  也可以通过转到 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) 在单独的浏览器标签页中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。 


## <a name="create-subnet"></a>创建子网 

本教程会创建包含两个子网的单个虚拟网络。 一个前端子网用于托管 Web 应用程序，一个后端子网用于托管数据库服务器。

创建虚拟网络之前，需使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 创建资源组。 以下示例在“EastUS”位置创建名为 *myRGNetwork* 的资源组：

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

使用 [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 创建一个名为 *myFrontendSubnet* 的子网配置：

```azurepowershell-interactive
$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

然后，创建一个名为 *myBackendSubnet* 的子网配置：

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

## <a name="create-virtual-network"></a>创建虚拟网络

通过 [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) 使用 *myFrontendSubnet* 和 *myBackendSubnet* 创建名为“myVNet”的 VNET：

```azurepowershell-interactive
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

此时，已创建一个网络并将其分段为两个子网，其中一个子网用于前端服务，另一个用于后端服务。 下一部分将创建虚拟机并将其连接到这些子网。

## <a name="create-a-public-ip-address"></a>创建公共 IP 地址

使用公共 IP 地址可在 Internet 上访问 Azure 资源。 公共 IP 地址的分配方法可以配置为动态或静态。 默认情况下，将动态分配公共 IP 地址。 解除分配 VM 时，将释放动态 IP 地址。 在执行涉及到 VM 解除分配的任何操作期间，此行为会导致 IP 地址发生更改。

可将分配方法设置为静态，这可确保分配给 VM 的 IP 地址保持不变，即使该 VM 处于解除分配状态也是如此。 如果使用静态 IP 地址，则无法指定 IP 地址本身。 该地址是从可用地址池中分配的。

使用 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) 创建名为 *myPublicIPAddress* 的公共 IP 地址：

```azurepowershell-interactive
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

可以将 -AllocationMethod 参数更改为 `Static`，以分配静态公共 IP 地址。

## <a name="create-a-front-end-vm"></a>创建前端 VM

VM 需要虚拟网络接口 (NIC) 才能在虚拟网络中进行通信。 使用 [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) 创建 NIC：

```azurepowershell-interactive
$frontendNic = New-AzNetworkInterface `
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

使用 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 创建 VM。

```azurepowershell-interactive
New-AzVM `
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

网络安全组 (NSG) 包含一系列安全规则，这些规则可以允许或拒绝流向连接到 Azure 虚拟网络 (VNet) 的资源的网络流量。 NSG 可以关联到子网或单个网络接口。 与网络接口关联的 NSG 只会应用到关联的 VM。 将 NSG 关联到子网时，规则适用于连接到该子网的所有资源。

### <a name="network-security-group-rules"></a>网络安全组规则

NSG 规则定义要允许或拒绝哪些网络端口上的流量。 这些规则可以包括源和目标 IP 地址范围，以便控制特定系统或子网之间的流量。 NSG 规则还包括优先级（介于 1 和 4096 之间）。 将按优先级顺序来评估规则。 优先级为 100 的规则会在优先级为 200 的规则之前评估。

所有 NSG 都包含一组默认规则。 默认规则无法删除，但由于给它们分配的优先级最低，可以用创建的规则来重写它们。

- **虚拟网络** - 从方向上来说，在虚拟网络中发起和结束的通信可以是入站通信，也可以是出站通信。
- **Internet** - 允许出站通信，但阻止入站通信。
- **负载均衡器** - 允许 Azure 的负载均衡器探测 VM 和角色实例的运行状况。 如果不使用负载均衡集，则可替代此规则。

### <a name="create-network-security-groups"></a>创建网络安全组

使用 [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig) 创建名为 *myFrontendNSGRule* 的入站规则以允许 *myFrontendVM* 上的传入 Web 流量：

```azurepowershell-interactive
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
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

可通过为后端子网创建 NSG，将内部流量限制为仅从 myFrontendVM 流向 myBackendVM。 以下示例创建名为“myBackendNSGRule”  的 NSG 规则：

```azurepowershell-interactive
$nsgBackendRule = New-AzNetworkSecurityRuleConfig `
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

使用 [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) 添加名为 *myFrontendNSG* 的网络安全组：

```azurepowershell-interactive
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

现在，使用 New-AzNetworkSecurityGroup 添加名为 *myBackendNSG* 的网络安全组：

```azurepowershell-interactive
$nsgBackend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

将网络安全组添加到子网：

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>创建后端 VM

创建本教程的后端 VM 的最简单方法是使用 SQL Server 映像。 本教程仅使用数据库服务器创建 VM，但不提供有关访问数据库的信息。

创建 myBackendNic  ：

```azurepowershell-interactive
$backendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

使用 Get-Credential 设置 VM 上管理员帐户所需的用户名和密码：

```azurepowershell-interactive
$cred = Get-Credential
```

创建 myBackendVM  。

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName MyBackendSubnet `
   -VirtualNetworkName myVNet
```

此示例中的映像安装了 SQL Server，但本教程中不会使用它。 安装它是为了演示如何配置处理 Web 流量的 VM 和处理数据库管理的 VM。

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
