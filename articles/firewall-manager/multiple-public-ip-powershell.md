---
title: 使用 Azure PowerShell 部署具有多个公共 IP 地址的 Azure 防火墙
description: 部署包含多个公共 IP 地址的防火墙来保护虚拟中心
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 652c7cbfbe63ef2ae9a0d54e05407152ea300f1d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87006936"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses"></a>部署包含多个公共 IP 地址的 Azure 防火墙

如果要使用 Azure 防火墙保护虚拟中心，可以使用 Azure PowerShell 部署具有多个公共 IP 地址的防火墙。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-the-firewall"></a>部署防火墙

使用以下 Azure PowerShell 示例部署包含多个公共 IP 地址的 Azure 防火墙，以保护虚拟中心。

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID> 

$rgName = <resource group name> 
$vHub = Get-AzVirtualHub -Name <hub name> 
$vHubId = $vHub.Id 
$fwpips = New-AzFirewallHubPublicIpAddress -Count 3
$hubIpAddresses = New-AzFirewallHubIpAddress -PublicIPs $fwpips 
$fw = New-AzFirewall -Name <firewall name> -ResourceGroupName $rgName `
     -Location <location> -Sku AZFW_Hub -HubIPAddresses $hubIpAddresses `
     -VirtualHubId $vHubId 
```

### <a name="update-a-public-ip-address"></a>更新公共 IP 地址

你可以使用 Azure PowerShell 来更新 Azure 防火墙的公共 IP 地址。 以下示例从防火墙中删除一个公共 IP 地址。 它从三个公共 IP 地址开始。

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID>

$azfw = get-azfirewall -Name <firewall name> -ResourceGroupName <resource group name>
$ip1 = New-AzFirewallPublicIpAddress -Address <first ip address to keep>
$ip2 = New-AzFirewallPublicIpAddress -Address <second ip address to keep>
$ipAddresses = $ip1,$ip2
$azfw.HubIPAddresses.publicIPs.Addresses = $ipAddresses
$azfw.HubIPAddresses.publicIPs.count = 2
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="next-steps"></a>后续步骤

[什么是安全虚拟中心？](secured-virtual-hub.md)