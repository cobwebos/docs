---
title: 使用 Azure PowerShell 配置负载均衡和出站规则
titlesuffix: Azure Load Balancer
description: 本文说明如何使用 Azure PowerShell 在标准负载均衡器中配置负载平衡和出站规则。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: b621d56a76a51f9ce9df10f88f5c3f2f4babfc03
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71816027"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>使用 Azure PowerShell 在标准负载均衡器中配置负载平衡和出站规则

本文说明如何使用 Azure PowerShell 在标准负载均衡器中配置出站规则。  

完成这篇文章的方案后，负载均衡器资源包含两个前端及其相关的规则。 为入站流量提供一个前端，并为出站流量提供另一个前端。  

每个前端引用一个公共 IP 地址。 在这种情况下，入站流量的公共 IP 地址与出站流量的地址不同。   负载均衡规则仅提供入站负载均衡。 出站规则控制 VM 的出站网络地址转换（NAT）。  

此方案使用两个后端池：一个用于入站流量，一个用于出站流量。 这些池阐释功能并为方案提供灵活性。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>连接到 Azure 帐户
使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 命令登录到 Azure 订阅。 然后，遵照屏幕指令进行操作。
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>创建资源组

使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)创建资源组。 Azure 资源组是在其中部署 Azure 资源的逻辑容器。 然后，将从组管理资源。

以下示例在“eastus2”位置创建名为“myresourcegroupoutbound”的资源组：

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>创建虚拟网络
创建名为*myvnetoutbound*的虚拟网络。 将其子网命名为*mysubnetoutbound*。 使用[AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0)和[AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0)将其置于*myresourcegroupoutbound*中。

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>创建入站公共 IP 地址 

若要在 internet 上访问 web 应用，需要负载均衡器的公共 IP 地址。 标准负载均衡器仅支持标准公共 IP 地址。 

使用[AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0)在*myresourcegroupoutbound*中创建名为*mypublicipinbound*的标准公共 IP 地址。

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>创建出站公共 IP 地址 

使用[AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0)为负载均衡器的前端出站配置创建标准 IP 地址。

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>创建 Azure 负载均衡器

本部分介绍如何创建和配置负载均衡器的以下组件：
  - 接收负载均衡器上的传入网络流量的前端 IP
  - 前端 IP 发送负载平衡的网络流量的后端池
  - 出站连接的后端池
  - 用于确定后端 VM 实例的运行状况的运行状况探测
  - 一种负载均衡器入站规则，用于定义将流量分配给 Vm 的方式
  - 用于定义如何从 Vm 分发流量的负载均衡器出站规则

### <a name="create-an-inbound-front-end-ip"></a>创建入站前端 IP
使用[AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0)为负载均衡器创建入站前端 IP 配置。 负载均衡器应包括一个名为*myfrontendinbound*的入站前端 IP 配置。 将此配置与公共 IP 地址*mypublicipinbound*。

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>创建出站前端 IP
使用[AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0)创建负载均衡器的出站前端 IP 配置。 此负载均衡器应包括一个名为*myfrontendoutbound*的出站前端 IP 配置。 将此配置与公共 IP 地址*mypublicipoutbound*。

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>创建入站后端池
使用[AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0)创建负载均衡器的后端入站池。 将池命名为*bepoolinbound*。

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>创建出站后端池
使用以下命令创建另一个后端地址池，以便使用[AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0)为 vm 池定义出站连接。 将此池命名为*bepooloutbound*。 

通过创建单独的出站池，可提供最大的灵活性。 但你可以忽略此步骤，并且仅当你喜欢时才使用入站*bepoolinbound* 。  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>创建运行状况探测器

运行状况探测检查所有 VM 实例，以确保它们可以发送网络流量。 从负载均衡器中删除失败的 VM 实例将从负载均衡器中删除，直到它恢复联机状态并且探测器检查确定它运行正常。 

若要监视 Vm 的运行状况，请使用[AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0)创建运行状况探测。 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则定义传入流量的前端 IP 配置和用于接收流量的后端池。 它还定义所需的源和目标端口。 

使用[AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0)创建名为*myinboundlbrule*的负载均衡器规则。 此规则将侦听前端池中的端口 80 *myfrontendinbound*。 它还将使用端口80将负载均衡的网络流量发送到后端地址池*bepoolinbound*。 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>此负载均衡规则禁用自动出站安全 NAT （SNAT），因为有 **-DisableOutboundSNAT**参数。 出站 NAT 仅由出站规则提供。

### <a name="create-an-outbound-rule"></a>创建出站规则

出站规则定义前端公共 IP，由前端*myfrontendoutbound*表示。 此前端将用于所有出站 NAT 流量以及规则应用到的后端池。  

使用以下命令创建出站规则*myoutboundrule* ，以便在*bepool*后端池中对所有 VM （在 NIC IP 配置中）进行出站网络转换。  命令将出站空闲超时时间从4分钟更改为15分钟。 它分配 10000 SNAT 端口，而不是1024。 有关详细信息，请参阅[AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0)。

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
如果你不想使用单独的出站池，则可以更改前面的命令中的 "地址池" 参数来指定 *$bepoolin* 。  建议使用单独的池，使生成的配置更灵活和更具可读性。

### <a name="create-a-load-balancer"></a>创建负载均衡器

使用以下命令通过[AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0)为入站 IP 地址创建负载均衡器。 将负载均衡器的名称命名为*lb*。它应包括入站前端 IP 配置。 它的后端池*bepoolinbound*应该与你在上一步中创建的公共 IP 地址*mypublicipinbound*相关联。

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

此时，可以通过更新相应 NIC 资源的 IP 配置，继续将 Vm 添加到*bepoolinbound*和*bepooloutbound*后端池。 使用[AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest)更新资源配置。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、负载均衡器和相关资源，可以使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0)将其删除。

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>后续步骤
本文介绍了如何创建标准负载均衡器、配置入站和出站负载平衡器流量规则，并为后端池中的 Vm 配置了运行状况探测。 

若要了解详细信息，请继续阅读[有关 Azure 负载均衡器的教程](tutorial-load-balancer-standard-public-zone-redundant-portal.md)。
