---
title: 在 Azure 虚拟网络中将 IPv4 应用程序升级到 IPv6-PowerShell
titlesuffix: Azure Virtual Network
description: 本文介绍如何使用 Azure Powershell 在 Azure 虚拟网络中将 IPv6 地址部署到现有应用程序。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/21/2019
ms.author: kumud
ms.openlocfilehash: d08ce1c382d173ac98a0e61e6117ed50b958ba44
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76119799"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell-preview"></a>在 Azure 虚拟网络中将 IPv4 应用程序升级到 IPv6-PowerShell （预览版）

本文介绍如何使用标准负载均衡器和公共 IP 将 IPv6 连接添加到 Azure 虚拟网络中的现有 IPv4 应用程序。 就地升级包括：
- 虚拟网络和子网的 IPv6 地址空间
- 同时使用 IPv4 和 IPV6 前端配置的标准负载均衡器
- 具有 IPv4 + IPv6 配置的 Nic 的 Vm
- IPv6 公共 IP，因此负载均衡器具有面向 Internet 的 IPv6 连接

> [!Important]
> Azure 虚拟网络的 IPv6 支持当前提供公共预览版。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本文需要 Azure PowerShell 模块版本6.9.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="prerequisites"></a>必备组件

### <a name="register-the-service"></a>注册服务

在 Azure 中部署双堆栈应用程序之前，必须使用以下 Azure PowerShell 来配置此预览功能的订阅：

按如下所示进行注册：
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
功能注册最多需要 30 分钟才能完成。 可以通过运行以下 Azure PowerShell 命令来检查注册状态：按如下所示检查注册：
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
注册完成后，运行以下命令：

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>创建标准负载均衡器
本文假设你按[快速入门：创建标准负载均衡器-Azure PowerShell](../load-balancer/quickstart-create-standard-load-balancer-powershell.md)中所述部署了标准负载均衡器。

## <a name="retrieve-the-resource-group"></a>检索资源组

在创建双堆栈虚拟网络之前，必须检索具有[AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup)的资源组。

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>创建 IPv6 IP 地址

使用[AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)为标准负载均衡器创建公共 IPv6 地址。 以下示例在*myResourceGroupSLB*资源组中创建名为*PublicIP_v6*的 IPv6 公共 IP 地址：

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

检索现有负载平衡器配置，然后使用[AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig)添加新的 IPv6 IP 地址，如下所示：

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

在负载平衡器配置的本地副本上创建后端池，并按如下所示更新运行负载均衡器：

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>配置负载均衡器规则
检索现有负载均衡器前端和后端池配置，并使用[AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig)添加新的负载均衡规则。

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

将 IPv6 地址范围添加到托管 Vm 的虚拟网络和子网，如下所示：

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

使用[AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig)配置包含 IPv6 地址的所有 VM nic，如下所示：

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
1. 在门户的搜索栏中，输入 " *myVnet*"。
2. 当 " **myVnet** " 出现在搜索结果中时，请选择它。 这将启动名为*myVNet*的双堆栈虚拟网络的 "**概述**" 页。 双堆栈虚拟网络显示三个 Nic，其中包含位于名为*mySubnet*的双堆栈子网中的 IPv4 和 IPv6 配置。

  ![Azure 中的 IPv6 双堆栈虚拟网络](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> 适用于 Azure 虚拟网络的 IPv6 可在此预览版本的只读 Azure 门户中使用。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和所有相关的资源，可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令将其删除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>后续步骤

本文介绍如何使用 IPv4 前端 IP 配置将现有标准负载均衡器更新为双堆栈（IPv4 和 IPv6）配置。 还将 IPv6 配置添加到后端池中的 Vm 的 Nic 和托管这些 Vm 的虚拟网络。 若要了解有关 Azure 虚拟网络中 IPv6 支持的详细信息，请参阅[什么是适用于 Azure 虚拟网络的 ipv6？](ipv6-overview.md)
