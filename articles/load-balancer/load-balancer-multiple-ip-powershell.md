---
title: "针对 Azure 中的多个 IP 配置进行负载均衡 | Microsoft Docs"
description: "在主要和辅助 IP 配置间进行负载均衡。"
services: load-balancer
documentationcenter: na
author: anavinahar
manager: narayan
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: annahar
ms.openlocfilehash: 2235d007434dabde1639ab19bc6813c818ed5ed7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>使用 PowerShell 在多个 IP 配置上进行负载均衡

> [!div class="op_single_selector"]
> * [门户](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

本文介绍如何将 Azure 负载均衡器用于辅助网络接口 (NIC) 的多个 IP 地址。 在此方案中，有两个运行 Windows 的 VM，每个 VM 有一个主 NIC 和一个辅助 NIC。 每个辅助 NIC 都有两个 IP 配置。 每个 VM 托管网站 contoso.com 和 fabrikam.com。每个网站都绑定到辅助 NIC 的一个 IP 配置。 我们使用 Azure 负载均衡器公开两个前端 IP 地址，每个地址分别对应于一个网站，从而将流量分发到网站的各个 IP 配置。 此场景中两个前端以及两个后端池 IP 地址都使用相同的端口号。

![负载均衡应用场景图像](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>在多个 IP 配置上进行负载均衡的步骤

按照以下步骤来实现本文所概述的场景：

1. 安装 Azure PowerShell 中的说明进行操作。 有关安装最新版 Azure PowerShell、选择订阅和登录到帐户的信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。
2. 使用以下设置创建资源组：

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    有关详细信息，请参阅[创建资源组](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)中的第 2 步。

3. [创建可用性集](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json)来包含 VM。 对于此场景，请使用以下命令：

    ```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. 按照[创建 Windows VM](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) 中步骤 3 至 5 的说明准备创建具有单个 NIC 的 VM。 执行步骤 6.1，使用以下命令而不是步骤 6.2：

    ```powershell
    $availset = Get-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzureRmVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    然后完成[创建 Windows VM](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) 的步骤 6.3 至 6.8。

5. 向每个 VM 中添加另一个 IP 配置。 按照[将多个 IP 地址分配给虚拟机](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add)文章中的说明执行操作。 请使用以下配置设置：

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    在本文中无需将辅助 IP 配置与公共 IP 关联。 请编辑此命令以删除公共 IP 关联部分。

6. 针对 VM2 再次完成本文的步骤 4 到 6。 执行此操作时务必将 VM 名称替换为 VM2。 请注意，无需为第二个 VM 创建虚拟网络。 用户可以根据自己的用例创建或不创建新的子网。

7. 创建两个公共 IP 地址并将它们存储在相应的变量中，如下所示：

    ```powershell
    $publicIP1 = New-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. 创建两个前端 IP 配置：

    ```powershell
    $frontendIP1 = New-AzureRmLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. 创建后端地址池、探测程序和负载均衡规则：

    ```powershell
    $beaddresspool1 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzureRmLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzureRmLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. 一旦创建这些资源后，即可创建负载均衡器：

    ```powershell
    $mylb = New-AzureRmLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. 将第二个后端地址池和前端 IP 配置添加到新创建的负载均衡器：

    ```powershell
    $mylb = Get-AzureRmLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzureRmLoadBalancer

    $mylb | Add-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzureRmLoadBalancer
    
    Add-AzureRmLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzureRmLoadBalancer
    ```

12. 下面的命令获取 NIC，然后将每个辅助 NIC 的两个 IP 配置添加到负载均衡器的后端地址池：

    ```powershell
    $nic1 = Get-AzureRmNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzureRmNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzureRmLoadBalancer

    $nic1 | Set-AzureRmNetworkInterface
    $nic2 | Set-AzureRmNetworkInterface
    ```

13. 最后，必须将 DNS 资源记录配置为指向各自的负载均衡器的前端 IP 地址。 可以在 Azure DNS 中托管域。 有关将 Azure DNS 与负载均衡器配合使用的详细信息，请参阅[将 Azure DNS 与其他 Azure 服务配合使用](../dns/dns-for-azure-services.md)。

## <a name="next-steps"></a>后续步骤
- 若要深入了解如何在 Azure 中结合使用负载均衡服务，请参阅[在 Azure 中使用负载均衡服务](../traffic-manager/traffic-manager-load-balancing-azure.md)。
- 若要了解如何在 Azure 中使用不同类型的日志对负载均衡器进行管理和故障排除，请参阅 [Azure 负载均衡器的 Log Analytics](../load-balancer/load-balancer-monitor-log.md)。
