---
title: 使用 Azure PowerShell 创建 Azure 专用链接服务 |Microsoft Docs
description: 了解如何使用 Azure PowerShell 创建 Azure 专用链接服务
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 3c808623269b8fabc32134a165b964a3b0747d4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88505608"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>使用 Azure PowerShell 创建专用链接服务
本文介绍了如何使用 Azure PowerShell 在 Azure 中创建专用链接服务。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果你选择在本地安装和使用 PowerShell，则本文要求使用最新的 Azure PowerShell 模块。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-Az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="create-a-resource-group"></a>创建资源组

创建专用链接之前，必须使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建资源组。 以下示例在 " *WestCentralUS* " 位置创建名为 " *myResourceGroup* " 的资源组：

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>创建虚拟网络
使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 为专用链接创建虚拟网络。 以下示例创建名为 *myvnet* 的虚拟网络，以及前端子网 (*frontendSubnet*)、后端子网 (*backendSubnet*) 和专用链接 (*otherSubnet*)：

```azurepowershell
$virtualNetworkName = "myvnet"


# Create subnet config

$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name frontendSubnet `
-AddressPrefix "10.0.1.0/24"

$backendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name backendSubnet `
-AddressPrefix "10.0.2.0/24"

$otherSubnet = New-AzVirtualNetworkSubnetConfig `
-Name otherSubnet `
-AddressPrefix "10.0.3.0/24" `
-PrivateLinkServiceNetworkPolicies "Disabled"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
-Name $virtualNetworkName `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "10.0.0.0/16" `
-Subnet $frontendSubnet,$backendSubnet,$otherSubnet
```
## <a name="create-internal-load-balancer"></a>创建内部负载均衡器
使用 [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) 创建内部标准负载均衡器。 以下示例使用在前面的步骤中创建的前端 IP 配置、探测、规则和后端池创建一个内部标准负载均衡器：

```azurepowershell

$lbBackendName = "LB-backend"
$lbFrontName = "LB-frontend"
$lbName = "lb"

#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beaddresspool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard
```
## <a name="create-a-private-link-service"></a>创建专用链接服务
使用 [AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer) 创建专用链接服务。  此示例使用 *myResourceGroup* 资源组中的标准负载均衡器创建名为 *myPLS* 的专用链接服务。
```azurepowershell

$plsIpConfigName = "PLS-ipconfig"
$plsName = "pls"
$peName = "pe"
 
$IPConfig = New-AzPrivateLinkServiceIpConfig `
-Name $plsIpConfigName `
-Subnet $vnet.subnets[2] `
-PrivateIpAddress 10.0.3.5

$fe = Get-AzLoadBalancer -Name $lbName | Get-AzLoadBalancerFrontendIpConfig

$privateLinkService = New-AzPrivateLinkService `
-ServiceName $plsName `
-ResourceGroupName $rgName `
-Location $location `
-LoadBalancerFrontendIpConfiguration $frontendIP `
-IpConfiguration $IPConfig
```

### <a name="get-private-link-service"></a>获取专用链接服务
使用 [Get-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) 获取有关专用链接服务的详细信息，如下所示：

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName
```

在此阶段，专用链接服务已成功创建，并已准备好接收流量。 请注意，上面的示例仅演示了如何使用 PowerShell 创建专用链接服务。  我们尚未配置负载均衡器后端池，也未在后端池上配置任何应用程序来侦听流量。 如果你想查看端到端通信流，强烈建议你在标准负载均衡器后面配置应用程序。

接下来，我们将演示如何使用 PowerShell 将此服务映射到不同 VNet 中的专用终结点。 同样，示例仅限于创建专用终结点并连接到上面创建的专用链接服务。 你可以在虚拟网络中创建虚拟机，来为用于构建你的方案的专用终结点发送/接收流量。

## <a name="create-a-private-endpoint"></a>创建专用终结点
### <a name="create-a-virtual-network"></a>创建虚拟网络
使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 为专用终结点创建虚拟网络。 此示例在名为 *myResourceGroup* 的资源组中创建名为  *vnetPE*  的虚拟网络：

```azurepowershell
$virtualNetworkNamePE = "vnetPE"

# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled"

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet
```

### <a name="create-a-private-endpoint"></a>创建专用终结点
创建一个专用终结点，用于使用上面在虚拟网络中创建的专用链接服务：

```azurepowershell

$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest
```

### <a name="get-private-endpoint"></a>获取专用终结点
使用 `Get-AzPrivateEndpoint` 获取专用终结点的 IP 地址，如下所示：

```azurepowershell
# Get Private Endpoint and its IP Address
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress

```

### <a name="approve-the-private-endpoint-connection"></a>批准专用终结点连接
使用“AzPrivateEndpointConnection”批准到专用链接服务的专用终结点连接。

```azurepowershell

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved"

```

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure 专用链接](private-link-overview.md)

