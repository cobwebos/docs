---
title: "使用 IPv6 创建 Azure 面向 Internet 的负载均衡器 - PowerShell | Microsoft Docs"
description: "了解如何使用用于 Resource Manager 的 PowerShell 创建具有 IPv6 的面向 Internet 的负载均衡器"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-resource-manager
keywords: "ipv6, azure 负载均衡器, 双堆栈, 公共 ip, 本机 ipv6, 移动, iot"
ms.assetid: d4c649e3-84ad-4343-8b6a-0e89f0b9e518
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: 5eff828095cd58732c78d4af43b5ff5420dfe8fd
ms.lasthandoff: 01/24/2017

---

# <a name="get-started-creating-an-internet-facing-load-balancer-with-ipv6-using-powershell-for-resource-manager"></a>开始使用用于 Resource Manager 的 PowerShell 创建具有 IPv6 的面向 Internet 的负载均衡器

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [模板](load-balancer-ipv6-internet-template.md)

Azure load balancer 是位于第 4 层 (TCP, UDP) 的负载平衡器。 该负载平衡器可以在云服务或负载平衡器集的虚拟机中运行状况良好的服务实例之间分配传入流量，从而提供高可用性。 Azure Load Balancer 还可以在多个端口和/或多个 IP 地址上显示这些服务。

## <a name="example-deployment-scenario"></a>示例部署方案

下图演示了要在本文中部署的负载均衡解决方案。

![负载平衡器方案](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

在此方案中，将创建以下 Azure 资源：

* 已分配 IPv4 和 IPv6 公共 IP 地址的面向 Internet 的负载均衡器
* 两个负载均衡规则，用于将公共 VIP 映射到专用终结点
* 包含两个 VM 的可用性集
* 两个虚拟机 (VM)
* 虚拟网络接口，用于每个已分配 IPv4 和 IPv6 地址的 VM

## <a name="deploying-the-solution-using-the-azure-powershell"></a>使用 Azure PowerShell 部署解决方案

以下步骤说明如何使用 Azure Resource Manager 和 PowerShell 创建面向 Internet 的负载均衡器。 借助 Azure Resource Manager，可单独创建和配置每个资源，再将其合成一个新资源。

若要部署负载均衡器，需要创建并配置以下对象：

* 前端 IP 配置 - 包含传入网络流量的公共 IP 地址。
* 后端地址池 - 包含从负载平衡器接收网络流量的虚拟机网络接口 (NIC)。
* 负载平衡规则 - 包含将负载平衡器上的公共端口映射到后端地址池中的端口的规则。
* 入站 NAT 规则 - 包含将负载平衡器上的公共端口映射到后端地址池中特定虚拟机的端口的规则。
* 探测器 - 包含用于检查后端地址池中虚拟机实例的可用性的运行状况探测器。

有关详细信息，请参阅 [Azure Resource Manager 对负载均衡器的支持](load-balancer-arm.md)。

## <a name="set-up-powershell-to-use-resource-manager"></a>将 PowerShell 设置为使用 Resource Manager

确保具备 Azure Resource Manager 模块的最新生产版本才可用于 PowerShell。

1. 登录 Azure

    ```powershell
    Login-AzureRmAccount
    ```

    在系统提示时输入凭据。

2. 检查帐户的订阅

    ```powershell
    Get-AzureRmSubscription
    ```

3. 选择要使用的 Azure 订阅。

    ```powershell
    Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'
    ```

4. 创建资源组（如果要使用现有的资源组，请跳过此步骤）

    ```powershell
    New-AzureRmResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>为前端 IP 池创建虚拟网络和公共 IP 地址

1. 创建一个包含子网的虚拟网络。

    ```powershell
    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    $vnet = New-AzureRmvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. 为前端 IP 地址池创建 Azure 公共 IP 地址 (PIP) 资源。

    ```powershell
    $publicIPv4 = New-AzureRmPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
    $publicIPv6 = New-AzureRmPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6
    ```

    > [!IMPORTANT]
    > 负载均衡器将公共 IP 的域标签用作 FQDN 的前缀。 在本示例中，FQDN 为 *lbnrpipv4.westus.cloudapp.azure.com* 和 *lbnrpipv6.westus.cloudapp.azure.com*。

## <a name="create-a-front-end-ip-configurations-and-a-back-end-address-pool"></a>创建前端 IP 配置和后端地址池

1. 创建使用所创建公共 IP 地址的前端地址配置。

    ```powershell
    $FEIPConfigv4 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
    $FEIPConfigv6 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6
    ```

2. 创建后端地址池

    ```powershell
    $backendpoolipv4 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
    $backendpoolipv6 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"
    ```

## <a name="create-lb-rules-nat-rules-a-probe-and-a-load-balancer"></a>创建 LB 规则、NAT 规则、探测器和负载均衡器

下例会创建以下项：

* 用于将端口 443 上的所有传入流量转换到端口 4443 的 NAT 规则
* 用于将端口 80 上的所有传入流量平衡到后端池中的地址端口 80 的负载均衡器规则。
* 一个负载均衡器规则，用来与端口 3389 上的 VM 建立 RDP 连接。
* 一个探测规则，用于检查名为 *HealthProbe.aspx* 的页面上或者端口 8080 上的服务的运行状况
* 使用上述所有对象的负载均衡器

1. 创建 NAT 规则。

    ```powershell
    $inboundNATRule1v4 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev4" -FrontendIpConfiguration $FEIPConfigv4 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    $inboundNATRule1v6 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev6" -FrontendIpConfiguration $FEIPConfigv6 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    ```

2. 创建运行状况探测器。 有两种方法可以配置探测器：

    HTTP 探测器

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    或 TCP 探测

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2
    $RDPprobe = New-AzureRmLoadBalancerProbeConfig -Name 'RDPprobe' -Protocol Tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
    ```

    对于本示例，我们将使用 TCP 探测。

3. 创建负载均衡器规则。

    ```powershell
    $lbrule1v4 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $lbrule1v6 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool $backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $RDPrule = New-AzureRmLoadBalancerRuleConfig -Name "RDPrule" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $RDPprobe -Protocol Tcp -FrontendPort 3389 -BackendPort 3389
    ```

4. 使用前面创建的对象创建负载均衡器。

    ```powershell
    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe,$RDPprobe -LoadBalancingRule $lbrule1v4,$lbrule1v6,$RDPrule
    ```

## <a name="create-nics-for-the-back-end-vms"></a>为后端 VM 创建 NIC

1. 获取需要创建 NIC 的虚拟网络和虚拟网络子网。

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. 为 VM 创建 IP 配置和 NIC。

    ```powershell
    $nic1IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
    $nic1IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
    $nic1 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

    $nic2IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
    $nic2IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
    $nic2 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'
    ```

## <a name="create-virtual-machines-and-assign-the-newly-created-nics"></a>创建虚拟机并分配新建的 NIC

有关创建 VM 的详细信息，请参阅[使用 Resource Manager 和 Azure PowerShell 创建并预先配置 Windows 虚拟机](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)

1. 创建可用性集和存储帐户

    ```powershell
    New-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG -location 'West US'
    $availabilitySet = Get-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
    New-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct' -Location 'West US' -SkuName "Standard_LRS"
    $CreatedStorageAccount = Get-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct'
    ```

2. 创建每个 VM 并分配前面创建的 NIC

    ```powershell
    $mySecureCredentials= Get-Credential -Message "Type the username and password of the local administrator account."

    $vm1 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM0' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm1 = Set-AzureRmVMOperatingSystem -VM $vm1 -Windows -ComputerName 'myNrpIPv6VM0' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm1 = Set-AzureRmVMSourceImage -VM $vm1 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm1 = Add-AzureRmVMNetworkInterface -VM $vm1 -Id $nic1.Id -Primary
    $osDisk1Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM0osdisk.vhd"
    $vm1 = Set-AzureRmVMOSDisk -VM $vm1 -Name 'myNrpIPv6VM0osdisk' -VhdUri $osDisk1Uri -CreateOption FromImage
    New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm1

    $vm2 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM1' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm2 = Set-AzureRmVMOperatingSystem -VM $vm2 -Windows -ComputerName 'myNrpIPv6VM1' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm2 = Set-AzureRmVMSourceImage -VM $vm2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm2 = Add-AzureRmVMNetworkInterface -VM $vm2 -Id $nic2.Id -Primary
    $osDisk2Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM1osdisk.vhd"
    $vm2 = Set-AzureRmVMOSDisk -VM $vm2 -Name 'myNrpIPv6VM1osdisk' -VhdUri $osDisk2Uri -CreateOption FromImage
    New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm2
    ```

## <a name="next-steps"></a>后续步骤

[开始配置内部负载均衡器](load-balancer-get-started-ilb-arm-ps.md)

[配置负载均衡器分发模式](load-balancer-distribution-mode.md)

[配置负载均衡器的空闲 TCP 超时设置](load-balancer-tcp-idle-timeout.md)

