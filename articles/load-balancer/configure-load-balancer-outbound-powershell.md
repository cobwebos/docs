---
title: 使用 Azure PowerShell 配置负载均衡和出站规则
titleSuffix: Azure Load Balancer
description: 本文介绍如何使用 Azure PowerShell 在标准负载均衡器中配置负载均衡和出站规则。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 5fd68f4559420ca688b3f4d6f6d66ee52db5191e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225445"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>使用 Azure PowerShell 在标准负载均衡器中配置负载均衡和出站规则

本文介绍如何使用 Azure PowerShell 在标准负载均衡器中配置出站规则。  

完成本文中的方案后，负载均衡器资源将包含两个前端及其关联的规则。 一个前端用于入站流量，另一个前端用于出站流量。  

每个前端引用一个公共 IP 地址。 在此方案中，入站流量的公共 IP 地址不同于出站流量的地址。   负载均衡规则仅提供入站负载均衡。 出站规则控制 VM 的出站网络地址转换 (NAT)。  

此方案使用两个后端池：一个池用于入站流量，另一个池用于出站流量。 这些池演示功能，并为方案提供灵活性。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>连接到 Azure 帐户
使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 命令登录到 Azure 订阅。 然后，遵照屏幕指令进行操作。
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) 创建资源组。 Azure 资源组是在其中部署 Azure 资源的逻辑容器。 然后可从该组管理资源。

以下示例在“eastus2”位置创建名为“myresourcegroupoutbound”的资源组：

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>创建虚拟网络
创建名为 *myvnetoutbound* 的虚拟网络。 将其子网命名为 *mysubnetoutbound*。 使用 *New-AzVirtualNetwork* 和 [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) 将此网络放入 [myresourcegroupoutbound](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0)。

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>创建入站公共 IP 地址 

若要通过 Internet 访问 Web 应用，需要负载均衡器有一个公共 IP 地址。 标准负载均衡器仅支持标准公共 IP 地址。 

使用 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) 在 *myresourcegroupoutbound* 中创建名为 *mypublicipinbound* 的标准公共 IP 地址。

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>创建出站公共 IP 地址 

使用 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) 为负载均衡器的前端出站配置创建标准 IP 地址。

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>创建 Azure 负载均衡器

本部分说明如何创建和配置负载均衡器的以下组件：
  - 前端 IP，用于在负载均衡器上接收传入网络流量
  - 后端池，其中的前端 IP 发送负载均衡的网络流量
  - 用于出站连接的后端池
  - 运行状况探测，用于确定后端 VM 实例的运行状况
  - 负载均衡器入站规则，用于定义如何将流量分配给 VM
  - 负载均衡器出站规则，用于定义如何从 VM 分配流量

### <a name="create-an-inbound-front-end-ip"></a>创建入站前端 IP
使用 [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) 为负载均衡器创建入站前端 IP 配置。 该负载均衡器应包含名为 *myfrontendinbound* 的入站前端 IP 配置。 将此配置关联到公共 IP 地址 *mypublicipinbound*。

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>创建出站前端 IP
使用 [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) 为负载均衡器创建出站前端 IP 配置。 此负载均衡器应包括名为 *myfrontendoutbound* 的出站前端 IP 配置。 将此配置关联到公共 IP 地址 *mypublicipoutbound*。

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>创建入站后端池
使用 [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) 为负载均衡器创建后端入站池。 将该池命名为 *bepoolinbound*。

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>创建出站后端池
使用以下 [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) 命令创建另一个后端地址池，以便为 VM 池定义出站连接。 将此池命名为 *bepooloutbound*。 

创建单独的出站池可以提供最大的灵活性。 但如果需要，也可以忽略此步骤，仅使用入站 *bepoolinbound*。  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>创建运行状况探测器

运行状况探测将检查所有 VM 实例，以确保它们可以发送网络流量。 未通过探测检查的 VM 实例将从负载均衡器中删除，直到它恢复联机状态并且探测检查确定它运行正常。 

若要监视 VM 的运行状况，请使用 [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0) 创建运行状况探测。 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则定义传入流量的前端 IP 配置，以及用于接收流量的后端池。 它还定义所需的源端口和目标端口。 

使用 *New-AzLoadBalancerRuleConfig* 创建名为 [myinboundlbrule](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0) 的负载均衡器规则。 此规则侦听前端池 *myfrontendinbound* 中的端口 80。 它还使用端口 80 将负载均衡的网络流量发送到后端地址池 *bepoolinbound*。 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>由于指定了 **-DisableOutboundSNAT** 参数，此负载均衡规则将禁用自动出站安全 NAT (SNAT)。 出站 NAT 仅通过出站规则提供。

### <a name="create-an-outbound-rule"></a>创建出站规则

出站规则定义由前端 *myfrontendoutbound* 表示的前端公共 IP。 此前端用于所有出站 NAT 流量，以及规则应用到的后端池。  

使用以下命令创建出站规则 *myoutboundrule*，用于对 *bepool* 后端池中的所有 VM（在 NIC IP 配置中）执行出站网络转换。  该命令将出站空闲超时从 4 分钟更改为 15 分钟。 它将分配 10,000 个 SNAT 端口，而不是 1,024 个。 有关详细信息，请参阅 [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0)。

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
如果你不想要使用单独的出站池，可以更改上述命令中的地址池参数，以指定 *$bepoolin*。  建议使用单独的池，使生成的配置更灵活且更易读。

### <a name="create-a-load-balancer"></a>创建负载均衡器

使用以下 [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0) 命令为入站 IP 地址创建负载均衡器。 将负载均衡器的名称命名为*lb*。它应包括入站前端 IP 配置。 应将其后端池 *bepoolinbound* 关联到在上一步骤中创建的公共 IP 地址 *mypublicipinbound*。

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

现在，可以通过更新相应 NIC 资源的 IP 配置，继续将 VM 添加到 *bepoolinbound* 和 *bepooloutbound* 后端池。 使用 [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) 更新资源配置。

## <a name="clean-up-resources"></a>清理资源

如果你不再需要本教程中创建的资源组、负载均衡器和相关资源，可以使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0) 将其删除。

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>后续步骤
本文介绍了如何创建标准负载均衡器、配置入站和出站负载平衡器流量规则，并为后端池中的 Vm 配置了运行状况探测。 

若要了解详细信息，请继续阅读[有关 Azure 负载均衡器的教程](tutorial-load-balancer-standard-public-zone-redundant-portal.md)。
