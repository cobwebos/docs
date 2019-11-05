---
title: Azure VPN 网关连接的自定义流量选择器 |Microsoft Docs
description: 了解如何使用 VPN 网关连接上的自定义流量选择器
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: radwiv
ms.openlocfilehash: cf9401b21d4aa736a6edeae5146d1355f2d49d1d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512153"
---
# <a name="custom-traffic-selectors-for-vpn-gateway-connections"></a>VPN 网关连接的自定义流量选择器

可以在 VPN 网关连接上设置自定义流量选择器。 设置流量选择器的功能可让你从要发送流量的 VPN 隧道两侧缩小地址空间。 如果有较大的 VNet 地址空间，但想要使用某个子网进行 IPsec/IKE 协商，则自定义流量选择器特别有用。

可以在创建新连接时添加自定义流量选择器，也可以更新现有连接。 `TrafficSelectorPolicies` 参数包含流量选择器的数组。 每个流量选择器都包含 CIDR 格式的本地和远程地址范围的集合。

## <a name="add-custom-traffic-selectors"></a>添加自定义流量选择器

以下示例演示如何使用 PowerShell 命令为虚拟网络网关连接创建自定义流量选择器。 有关如何创建虚拟网络网关连接的帮助，请参阅[配置站点到站点连接](vpn-gateway-create-site-to-site-rm-powershell.md)。

1. 在 $trafficselectorpolicy 中设置*trafficselectorpolicies*参数的值。 设置此项时，请注意， *AzTrafficSelectorPolicy*值在数组中采用本地和远程地址范围。

   ```azurepowershell-interactive
   $trafficSelectorPolicy = New-AzTrafficSelectorPolicy `
   -LocalAddressRanges ("10.10.10.0/24", "20.20.20.0/24") `
   -RemoteAddressRanges ("30.30.30.0/24", "40.40.40.0/24")
   ```
2. 创建新的虚拟网络网关连接。 调整要求的值参数。

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection `
   -ResourceGroupName $rgname `
   -name $vnetConnectionName `
   -location $location `
   -VirtualNetworkGateway1 $vnetGateway `
   -LocalNetworkGateway2 $localnetGateway `
   -ConnectionType IPsec `
   -RoutingWeight 3 `
   -SharedKey $sharedKey `
   -UsePolicyBasedTrafficSelectors $true `
   -TrafficSelectorPolicies ($trafficSelectorPolicy)
   ```

你还可以使用 "AzVirtualNetworkGatewayConnection" 更新现有虚拟网络网关连接的自定义流量选择器。 有关 PowerShell 值，请参阅[虚拟网络网关连接](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection?)。

## <a name="next-steps"></a>后续步骤

有关 VPN 网关的详细信息，请参阅[关于 Vpn 网关](vpn-gateway-about-vpngateways.md)。