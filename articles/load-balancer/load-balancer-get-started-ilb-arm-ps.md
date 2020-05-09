---
title: 使用 PowerShell 创建 Azure 内部负载均衡器
titleSuffix: Azure Load Balancer
description: 了解如何将 Azure PowerShell 模块与 Azure 资源管理器配合使用，以便创建内部负载均衡器
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 485afaa4b7009731784cf5da6f8c28e0a787c1d9
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629416"
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-powershell-module"></a>使用 Azure PowerShell 模块创建内部负载均衡器

> [!div class="op_single_selector"]
> * [Azure 门户](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [模板](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="get-started-with-the-configuration"></a>配置入门

本文介绍如何将 Azure 资源管理器与 Azure PowerShell 模块配合使用，以便创建内部负载均衡器。 在资源管理器部署模型中，创建内部负载均衡器所需的对象是单独配置的。 创建和配置对象后，即可将其组合起来创建负载均衡器。

若要部署负载均衡器，必须创建以下对象：

* 前端 IP 池：适用于所有传入网络流量的专用 IP 地址。
* 后端地址池：网络接口，用于接收来自前端 IP 地址的负载均衡流量。
* 负载均衡规则：负载均衡器的端口（源和本地）配置。
* 探测配置：虚拟机的运行状况探测。
* 入站 NAT 规则：直接访问虚拟机时的端口规则。

有关负载均衡器组件的详细信息，请参阅 [Azure 负载均衡器组件](components.md)。

以下步骤介绍如何配置两个虚拟机之间的负载均衡器。

## <a name="set-up-powershell-to-use-resource-manager"></a>将 PowerShell 设置为使用 Resource Manager

确保具备 Azure PowerShell 模块的最新生产版本。 必须正确配置 PowerShell 才能访问 Azure 订阅。

### <a name="step-1-start-powershell"></a>步骤 1：启动 PowerShell

启动适用于 Azure 资源管理器的 PowerShell 模块。

```azurepowershell-interactive
Connect-AzAccount
```

### <a name="step-2-view-your-subscriptions"></a>步骤 2：查看订阅

检查可用的 Azure 订阅。

```azurepowershell-interactive
Get-AzSubscription
```

当系统提示你进行身份验证时，请输入凭据。

### <a name="step-3-select-the-subscription-to-use"></a>步骤 3：选择要使用的订阅

选择用于部署负载均衡器的 Azure 订阅。

```azurepowershell-interactive
Select-AzSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4-choose-the-resource-group-for-the-load-balancer"></a>步骤 4：选择负载均衡器的资源组

创建负载均衡器的新资源组。 若要使用现有资源组，请跳过此步骤。

```azurepowershell-interactive
New-AzResourceGroup -Name NRP-RG -location "West US"
```

Azure Resource Manager 要求所有资源组指定一个位置。 此位置用作资源组中所有资源的默认值。 对于与创建负载均衡器相关的所有命令，请始终使用同一资源组。

在示例中，我们使用位置“美国西部”创建了名为“NRP-RG”的资源组。****

## <a name="create-the-virtual-network-and-ip-address-for-the-front-end-ip-pool"></a>为前端 IP 池创建虚拟网络和 IP 地址

为虚拟网络创建子网，并将其分配给变量 $backendSubnet。 

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

创建虚拟网络。

```azurepowershell-interactive
$vnet= New-AzVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

虚拟网络已创建。  LB-Subnet-BE 子网已添加到  NRPVNet 虚拟网络。 这些值已分配给  $vnet 变量。

## <a name="create-the-front-end-ip-pool-and-back-end-address-pool"></a>创建前端 IP 池和后端地址池

为传入流量创建前端 IP 池，并创建后端地址池，用于接收负载均衡的流量。

### <a name="step-1-create-a-front-end-ip-pool"></a>步骤 1：创建前端 IP 池

使用子网 10.0.2.0/24 的专用 IP 地址 10.0.2.5 创建前端 IP 池。 此地址是传入网络流量终结点。

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2-create-a-back-end-address-pool"></a>步骤 2：创建后端地址池

创建后端地址池，用于接收前端 IP 池的传入流量：

```powershell
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-the-configuration-rules-probe-and-load-balancer"></a>创建配置规则、探测和负载均衡器

创建前端 IP 池和后端地址池后，指定负载均衡器资源的规则。

### <a name="step-1-create-the-configuration-rules"></a>步骤 1：创建配置规则

此示例创建以下四个规则对象：

* 远程桌面协议 (RDP) 的入站 NAT 规则：将端口 3441 上的所有传入流量重定向到端口 3389。
* RDP 的第二个入站 NAT 规则：将端口 3442 上的所有传入流量重定向到端口 3389。
* 运行状况探测规则：检查 HealthProbe.aspx 路径的运行状况。
* 负载均衡器规则：将公共端口 80 上的所有传入流量负载均衡到后端地址池中的本地端口 80。

```azurepowershell-interactive
$inboundNATRule1= New-AzLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

### <a name="step-2-create-the-load-balancer"></a>步骤 2：创建负载均衡器

创建负载均衡器并将规则对象（适用于 RDP 的入站 NAT、负载均衡器、运行状况探测）组合到一起：

```azurepowershell-interactive
$NRPLB = New-AzLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-the-network-interfaces"></a>创建网络接口

创建内部负载均衡器后，请定义网络接口 (NIC)，以便接收传入的负载均衡网络流量、NAT 规则和探测。 每个网络接口单独进行配置，随后分配给虚拟机。

### <a name="step-1-create-the-first-network-interface"></a>步骤 1：创建第一个网络接口

获取资源虚拟网络和子网。 以下值用于创建网络接口：

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

创建第一个网络接口，其名称为  lb-nic1-be。 将接口分配给负载均衡器后端池。 将第一个适用于 RDP 的 NAT 规则与此 NIC 相关联：

```azurepowershell-interactive
$backendnic1= New-AzNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2-create-the-second-network-interface"></a>步骤 2：创建第二个网络接口

创建第二个网络接口，其名称为  lb-nic2-be。 将第二个接口分配到第一个接口所分配到的负载均衡器后端池。 将第二个 NIC 与第二个适用于 RDP 的 NAT 规则相关联：

```azurepowershell-interactive
$backendnic2= New-AzNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

查看配置：

    $backendnic1

设置应如下所示：

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3-assign-the-nic-to-a-vm"></a>步骤 3：将 NIC 分配到 VM

使用 `Add-AzVMNetworkInterface` 命令将 NIC 分配到虚拟机。

有关如何创建虚拟机和分配 NIC 的分步说明，请参阅[使用 PowerShell 创建 Azure VM](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)。

## <a name="add-the-network-interface"></a>添加网络接口

创建虚拟机后，请添加网络接口。

### <a name="step-1-store-the-load-balancer-resource"></a>步骤 1：存储负载均衡器资源

将负载均衡器资源存储到变量中（如果还没有这样做）。 我们将使用变量名称  $lb。对于脚本中的属性值，请使用在前述步骤中创建的负载均衡器资源的名称。

```azurepowershell-interactive
$lb = Get-AzLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2-store-the-back-end-configuration"></a>步骤 2：存储后端配置

将后端配置存储到  $backend 变量中。

```azurepowershell-interactive
$backend = Get-AzLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3-store-the-network-interface"></a>步骤 3：存储网络接口

在另一个变量中存储网络接口。 此接口已在“创建网络接口（步骤 1）”中创建。 我们将使用变量名称  $nic1。 请使用前一示例中的网络接口名称。

```azurepowershell-interactive
$nic = Get-AzNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4-change-the-back-end-configuration"></a>步骤 4：更改后端配置

更改网络接口上的后端配置。

```azurepowershell-interactive
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5-save-the-network-interface-object"></a>步骤 5：保存网络接口对象

保存网络接口对象。

```azurepowershell-interactive
Set-AzNetworkInterface -NetworkInterface $nic
```

将接口添加到后端池以后，系统会根据规则对网络流量进行负载均衡。 这些规则已在“创建配置规则、探测和负载均衡器”中配置。

## <a name="update-an-existing-load-balancer"></a>更新现有的负载均衡器

### <a name="step-1-assign-the-load-balancer-object-to-a-variable"></a>步骤 1：将负载均衡器对象分配给一个变量

使用 `Get-AzLoadBalancer` 命令将负载均衡器对象（取自前一示例）分配到  $slb 变量：

```azurepowershell-interactive
$slb = Get-AzLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

### <a name="step-2-add-a-nat-rule"></a>步骤 2：添加 NAT 规则

将新的入站 NAT 规则添加到现有的负载均衡器。 将端口 81 用于前端池，端口 8181 用于后端池：

```azurepowershell-interactive
$slb | Add-AzLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3-save-the-configuration"></a>步骤 3：保存配置

使用 `Set-AzureLoadBalancer` 命令保存新配置：

```azurepowershell-interactive
$slb | Set-AzLoadBalancer
```

## <a name="remove-an-existing-load-balancer"></a>删除现有的负载均衡器

使用 `Remove-AzLoadBalancer` 命令删除  NRP-RG 资源组中的  NRP-LB 负载均衡器：

```azurepowershell-interactive
Remove-AzLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

> [!NOTE]
> 使用可选的  -Force 开关，防止针对删除操作的确认提示符出现。

## <a name="next-steps"></a>后续步骤

* [配置负载均衡器分发模式](load-balancer-distribution-mode.md)
* [配置负载均衡器的空闲 TCP 超时设置](load-balancer-tcp-idle-timeout.md)
