---
title: 在 Azure 虚拟网络中将 IPv4 应用程序升级到 IPv6 - PowerShell
titlesuffix: Azure Virtual Network
description: 本文介绍如何使用 Azure Powershell 将 IPv6 地址部署到 Azure 虚拟网络中的现有应用程序。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 82c8b71c154336cf59610d8f0b99e70eccee266f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84688545"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell"></a>在 Azure 虚拟网络中将 IPv4 应用程序升级到 IPv6 - PowerShell

本文介绍如何将 IPv6 连接添加到 Azure 虚拟网络中使用标准负载均衡器和公共 IP 的现有 IPv4 应用程序。 就地升级涉及到：
- 虚拟网络和子网的 IPv6 地址空间
- 采用 IPv4 和 IPV6 前端配置的标准负载均衡器
- 包含采用 IPv4 + IPv6 配置的 NIC 的 VM
- IPv6 公共 IP，使负载均衡器能够建立面向 Internet 的 IPv6 连接



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果你选择在本地安装和使用 PowerShell，本文要求使用 Azure PowerShell 模块 6.9.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-Az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="prerequisites"></a>先决条件

本文假设已根据以下文章所述部署了一个标准负载均衡器：[快速入门：创建标准负载均衡器 - Azure PowerShell](../load-balancer/quickstart-create-standard-load-balancer-powershell.md)。

## <a name="retrieve-the-resource-group"></a>检索资源组

在创建双堆栈虚拟网络之前，必须先使用 [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) 创建检索资源组。

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>创建 IPv6 IP 地址

使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 为标准负载均衡器创建公共 IPv6 地址。 以下示例在 *myResourceGroupSLB* 资源组中创建名为 *PublicIP_v6* 的 IPv6 公共 IP 地址：

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>配置负载均衡器前端

检索现有的负载均衡器配置，然后使用 [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) 添加新的 IPv6 IP 地址，如下所示：

```azurepowershell
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"
# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6
#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>配置负载均衡器后端池

在负载均衡器配置的本地副本中创建后端池，并使用新的后端池配置更新正在运行的负载均衡器，如下所示：

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>配置负载均衡器规则
检索现有的负载均衡器前端和后端池配置，然后使用 [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig) 添加新的负载均衡规则。

```azurepowershell
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb
# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>添加 IPv6 地址范围

将 IPv6 地址范围添加到托管 VM 的虚拟网络和子网，如下所示：

```azurepowershell
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 
#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("ace:cab:deca::/48")
#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]
#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("ace:cab:deca::/64")
#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork

```
## <a name="add-ipv6-configuration-to-nic"></a>将 IPv6 配置添加到 NIC

运行 [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) 配置使用 IPv6 地址的所有 VM NIC，如下所示：

```azurepowershell

#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName
#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>在 Azure 门户中查看 IPv6 双堆栈虚拟网络
可以在 Azure 门户中查看 IPv6 双堆栈虚拟网络，如下所示：
1. 在门户的搜索栏中输入 *myVnet*。
2. 当“myVnet”出现在搜索结果中时，将其选中。**** 此时会启动名为 *myVNet* 的双堆栈虚拟网络的“概述”页。**** 该双堆栈虚拟网络显示了位于 *mySubnet* 双堆栈子网中的三个 NIC，这些 NIC 采用 IPv4 和 IPv6 配置。

  ![Azure 中的 IPv6 双堆栈虚拟网络](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)



## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和所有相关的资源，可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令将其删除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>后续步骤

在本文中，你已将采用 IPv4 前端 IP 配置的现有标准负载均衡器更新为双堆栈（IPv4 和 IPv6）配置。 你还将 IPv6 配置添加到了后端池中 VM 的 NIC，并添加到了托管这些 VM 的虚拟网络。 若要详细了解 Azure 虚拟网络中的 IPv6 支持，请参阅 [Azure 虚拟网络 IPv6 是什么？](ipv6-overview.md)
