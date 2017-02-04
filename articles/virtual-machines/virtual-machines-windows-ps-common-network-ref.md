---
title: "VM 的常用网络 PowerShell 命令 | Microsoft Docs"
description: "可用于为 VM 创建虚拟网络及其关联资源的常用 PowerShell 命令。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: ce08e9dcd0585e00bdd42b8aa34ac2c597a53a6e


---
# <a name="common-network-azure-powershell-commands-for-vms"></a>VM 的常用网络 Azure PowerShell 命令
如果想要创建虚拟机，需要创建[虚拟网络](../virtual-network/virtual-networks-overview.md)或了解可在其中添加 VM 的现有虚拟网络。 通常情况下，创建 VM 时，还需考虑创建本文所述资源。

有关安装最新版 Azure PowerShell、选择订阅和登录到帐户的信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

## <a name="create-network-resources"></a>创建网络资源
| 任务 | 命令 |
| --- | --- |
| 创建子网配置 |$subnet1 = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -Name "subnet_name" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name "subnet_name" -AddressPrefix XX.X.X.X/XX<BR><BR>典型的网络可能包含用于[面向 Internet 的负载均衡器](../load-balancer/load-balancer-internet-overview.md)的子网，以及用于[内部负载均衡器](../load-balancer/load-balancer-internal-overview.md)的独立子网。 |
| 创建虚拟网络 |$vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -Name "virtual_network_name" -ResourceGroupName "resource_group_name" -Location "location_name" -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| 测试唯一域名 |[Test-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) -DomainQualifiedName "domain_name" -Location "location_name"<BR><BR>可以为[公共 IP 资源](../virtual-network/virtual-network-ip-addresses-overview-arm.md)指定一个 DNS 域名，以便在 Azure 托管的 DNS 服务器中为 domainname.location.cloudapp.azure.com 创建目标为公共 IP 地址的映射。 该名称只能包含字母、数字和连字符。 第一个和最后一个字符必须是字母或数字，域名在其 Azure 位置内必须是唯一的。 如果返回 **True**，则表示使用的名称全局唯一。 |
| 创建公共 IP 地址 |$pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -Name "ip_address_name" -ResourceGroupName "resource_group_name" -DomainNameLabel "domain_name" -Location "location_name" -AllocationMethod Dynamic<BR><BR>公共 IP 地址使用前面测试过的并由负载均衡器前端配置使用的域名。 |
| 创建前端 IP 配置 |$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -Name "frontend_ip_name" -PublicIpAddress $pip<BR><BR>前端配置包括前面针对传入网络流量创建的公共 IP 地址。 |
| 创建后端地址池 |$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -Name "backend_pool_name"<BR><BR>提供可通过网络接口访问的负载均衡器后端内部地址。 |
| 创建探测 |$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -Name "probe_name" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>包含用于检查后端地址池中虚拟机实例的可用性的运行状况探测。 |
| 创建负载均衡规则 |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>包含可将负载均衡器上的公共端口分配到后端地址池中的端口的规则。 |
| 创建入站 NAT 规则 |$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -Name "rule_name" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>包含将负载均衡器上的公共端口映射到后端地址池中特定虚拟机的端口的规则。 |
| 创建负载平衡器 |$loadBalancer = [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) -ResourceGroupName "resource_group_name" -Name "load_balancer_name" -Location "location_name" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| 创建网络接口 |$nic1= [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) -ResourceGroupName "resource_group_name" -Name "network_interface_name" -Location "location_name" -PrivateIpAddress XX.X.X.X -Subnet subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>创建一个使用前面所创建公共 IP 地址和虚拟网络子网的网络接口。 |

## <a name="get-information-about-network-resources"></a>获取有关网络资源的信息
| 任务 | 命令 |
| --- | --- |
| 列出虚拟网络 |[Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -ResourceGroupName "resource_group_name"<BR><BR>列出资源组中的所有虚拟网络。 |
| 获取有关虚拟网络的信息 |Get-AzureRmVirtualNetwork -Name "virtual_network_name" -ResourceGroupName "resource_group_name" |
| 列出虚拟网络中的子网 |Get-AzureRmVirtualNetwork -Name "virtual_network_name" -ResourceGroupName "resource_group_name" &#124; Select Subnets |
| 获取有关子网的信息 |[Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -Name "subnet_name" -VirtualNetwork $vnet<BR><BR>获取指定虚拟网络中子网的相关信息。 $vnet 值表示 Get-AzureRmVirtualNetwork 返回的对象。 |
| 列出 IP 地址 |[Get-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619342.aspx) -ResourceGroupName "resource_group_name"<BR><BR>列出资源组中的公共 IP 地址。 |
| 列出负载均衡器 |[Get-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603668.aspx) -ResourceGroupName "resource_group_name"<BR><BR>列出资源组中的所有负载均衡器。 |
| 列出网络接口 |[Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) -ResourceGroupName "resource_group_name"<BR><BR>列出资源组中的所有网络接口。 |
| 获取有关网络接口的信息 |Get-AzureRmNetworkInterface -Name "network_interface_name" -ResourceGroupName "resource_group_name"<BR><BR>获取有关特定网络接口的信息。 |
| 获取网络接口的 IP 配置 |[Get-AzureRmNetworkInterfaceIPConfig](https://msdn.microsoft.com/library/mt732618.aspx) -Name "ipconfiguration_name" -NetworkInterface $nic<BR><BR>获取指定网络接口的 IP 配置的相关信息。 $nic 值表示 Get-AzureRmNetworkInterface 返回的对象。 |

## <a name="manage-network-resources"></a>管理网络资源
| 任务 | 命令 |
| --- | --- |
| 将子网添加到虚拟网络 |[Add-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603722.aspx) -AddressPrefix XX.X.X.X/XX -Name "subnet_name" -VirtualNetwork $vnet<BR><BR>将子网添加到现有虚拟网络。 $vnet 值表示 Get-AzureRmVirtualNetwork 返回的对象。 |
| 删除虚拟网络 |[Remove-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt619338.aspx) -Name "virtual_network_name" -ResourceGroupName "resource_group_name"<BR><BR>从资源组中删除指定的虚拟网络。 |
| 删除网络接口 |[Remove-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt603836.aspx) -Name "network_interface_name" -ResourceGroupName "resource_group_name"<BR><BR>从资源组中删除指定的网络接口。 |
| 删除负载平衡器 |[Remove-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) -Name "load_balancer_name" -ResourceGroupName "resource_group_name"<BR><BR>从资源组中删除指定的负载均衡器。 |
| 删除公共 IP 地址 |[Remove-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619352.aspx)-Name "ip_address_name" -ResourceGroupName "resource_group_name"<BR><BR>从资源组中删除指定的公共 IP 地址。 |

## <a name="next-steps"></a>后续步骤
* 使用[创建 VM](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 时创建的网络接口。
* 了解如何[创建具有多个网络接口的 VM](../virtual-network/virtual-networks-multiple-nics.md)。




<!--HONumber=Dec16_HO2-->


