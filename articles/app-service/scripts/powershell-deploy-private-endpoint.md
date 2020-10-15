---
title: PowerShell：使用 PowerShell 为 Web 应用部署专用终结点
description: 了解如何使用 PowerShell 为 Web 应用部署专用终结点
author: ericgre
ms.assetid: e1cc08d5-91cf-49d7-8d0a-c0e7bd2046ac
ms.topic: sample
ms.date: 07/07/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 97ded67b9f9daa2652b2740a21a4b7d6a0ac2bdd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87068271"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-powershell"></a>使用 PowerShell 创建应用服务应用并部署专用终结点

本示例脚本使用其相关资源，在应用服务中创建应用，然后部署专用终结点。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]


## <a name="sample-script"></a>示例脚本

```azurepowershell-interactive
# Parameters
$sitename="mywebapp-$(Get-Random)"
$appserviceplanname="ASP-"+$sitename
$resourcegroupname="RG-"+$sitename
$VNetname="VNet-$(Get-Random)"
$location="francecentral"
$privateendpointsubnetname = "privateEndpointSubnet"

# Create a resource group.
New-AzResourceGroup -Name $resourcegroupname -Location $location

# Create an App Service plan in PremiumV2 tier.
$asp = New-AzAppServicePlan -Name $appserviceplanname `
-Location $location `
-ResourceGroupName $resourcegroupname `
-Tier PremiumV2 `
-NumberofWorkers 1 `
-WorkerSize Small

# Create a web app.
$webApp = New-AzWebApp -Name $sitename `
-Location $location `
-AppServicePlan $appserviceplanname `
-ResourceGroupName $resourcegroupname

# Create a Virtual Network with two subnets
$integrationsubnet = New-AzVirtualNetworkSubnetConfig -Name "integrationSubnet" `
-AddressPrefix "10.8.1.0/24"

$privateendpointsubnet = New-AzVirtualNetworkSubnetConfig -Name $privateendpointsubnetname `
-AddressPrefix "10.8.2.0/24" `
-PrivateEndpointNetworkPoliciesFlag Disabled

$virtualNetwork = New-AzVirtualNetwork -Name $VNetname `
-ResourceGroupName $resourcegroupname `
-Location $location -AddressPrefix "10.8.0.0/16" `
-Subnet $integrationsubnet,$privateendpointsubnet

# Configure the Private Endpoint
$privateEndPointConnection = New-AzPrivateLinkServiceConnection -Name "myPrivateEndpointconnection" `
-PrivateLinkServiceID $webApp.Id `
-GroupId sites

$subnet = $virtualNetwork | select -ExpandProperty subnets | Where-Object {$_.Name -eq $privateendpointsubnetname}

$privateEndpoint = New-AzPrivateEndpoint -Name "myPrivateEndpoint" `
-ResourceGroupName $resourcegroupname `
-Location $location `
-Subnet $subnet `
-PrivateLinkServiceConnection $privateEndPointConnection

# Configure the Private DNS zone
$dnsZone = New-AzPrivateDnsZone -Name "privatelink.azurewebsites.net" `
-ResourceGroupName $resourcegroupname

$dnsLink = New-AzPrivateDnsVirtualNetworkLink -Name "myDNSLink" `
-ResourceGroupName $resourcegroupname `
-ZoneName "privatelink.azurewebsites.net" `
-VirtualNetworkId $virtualNetwork.Id

$dnsConfig = New-AzPrivateDnsZoneConfig -Name "privatelink.azurewebsites.net" `
-PrivateDnsZoneId $dnsZone.ResourceId

$dnsZoneGroup = New-AzPrivateDnsZoneGroup -Name "myZoneGroup" `
-ResourceGroupName $resourcegroupname `
-PrivateEndpointName $privateEndpoint.Name `
-PrivateDnsZoneConfig $dnsConfig
```


## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、Web 应用以及所有相关资源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | 创建应用服务计划。 |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 创建 Web 应用。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 创建虚拟网络子网配置。 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 创建虚拟网络。 |
| [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/new-azprivatelinkserviceconnection) | 创建专用链接服务连接配置。 |
| [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) | 创建专用终结点。 |
| [New-AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone) | 创建新的专用 DNS 区域。 |
| [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink) | 创建新的专用 DNS 虚拟网络链接。 |
| [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig) | 创建专用 DNS 区域组的 DNS 区域配置。 |
| [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup) | 在指定的专用终结点中创建专用 DNS 区域组。 |

## <a name="next-steps"></a>后续步骤

- 有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。
- 可以在 [Azure PowerShell 示例](../samples-powershell.md)中找到 Azure 应用服务 Web 应用的其他 Azure PowerShell 示例。
