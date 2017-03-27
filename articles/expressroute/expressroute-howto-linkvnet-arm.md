---
title: "将虚拟网络链接到 ExpressRoute 线路：PowerShell：Azure | Microsoft 文档"
description: "本文档概述如何使用 Resource Manager 部署模型和 PowerShell 将虚拟网络 (VNet) 链接到 ExpressRoute 线路。"
services: expressroute
documentationcenter: na
author: ganesr
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: daacb6e5-705a-456f-9a03-c4fc3f8c1f7e
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2016
ms.author: ganesr
translationtype: Human Translation
ms.sourcegitcommit: 75b7cee2485d1d68fee8dcd65ade84995dcdb9be
ms.openlocfilehash: fc5bc67fd38fe8a8c4499257a29bc0f0ec71468c
ms.lasthandoff: 02/03/2017


---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>将虚拟网络连接到 ExpressRoute 线路
> [!div class="op_single_selector"]
> * [Resource Manager - Azure 门户](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-linkvnet-arm.md)
> * [经典 - PowerShell](expressroute-howto-linkvnet-classic.md)
> * [视频 - Azure 门户](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> 
> 

本文将帮助你使用 Resource Manager 部署模型和 PowerShell 将虚拟网络 (VNet) 链接到 Azure ExpressRoute 线路。 虚拟网络可以在同一个订阅中，也可以属于另一个订阅。

**关于 Azure 部署模型**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>配置先决条件
* 需要最新版本的 Azure PowerShell 模块（至少 1.0 版）。 有关安装 PowerShell cmdlet 的详细信息，请参阅 [如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs) 。
* 在开始配置之前，需要查看[先决条件](expressroute-prerequisites.md)、[路由要求](expressroute-routing.md)和[工作流](expressroute-workflows.md)。
* 你必须有一个活动的 ExpressRoute 线路。 
  * 请按说明[创建 ExpressRoute 线路](expressroute-howto-circuit-arm.md)，并通过连接提供商启用该线路。 
  * 请确保为线路配置 Azure 专用对等互连。 有关路由说明，请参阅[配置路由](expressroute-howto-routing-arm.md)一文。 
  * 确保配置 Azure 专用对等互连并运行你的网络和 Microsoft 之间的 BGP 对等互连，以便启用端到端连接。
  * 确保已创建并完全预配一个虚拟网络和一个虚拟网络网关。 请按照说明创建 [VPN 网关](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)，但务必使用 `-GatewayType ExpressRoute`。

最多可以将 10 个虚拟网络链接到一条标准 ExpressRoute 线路。 使用标准 ExpressRoute 线路时，所有虚拟网络必须都位于同一地缘政治区域。 

如果已启用 ExpressRoute 高级外接程序，则可以链接 ExpressRoute 线路的地缘政治区域外部的虚拟网络，或者将更多虚拟网络连接到 ExpressRoute 线路。 有关高级外接程序的更多详细信息，请参阅[常见问题](expressroute-faqs.md)。

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>将同一订阅中的虚拟网络连接到线路
你可以使用以下 cmdlet 将虚拟网络网关连接到 ExpressRoute 线路。 在运行 cmdlet 之前，请确保已创建虚拟网络网关并可将其用于进行链接：

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>将不同订阅中的虚拟网络连接到线路
用户可以在多个订阅之间共享 ExpressRoute 线路。 下图是在多个订阅之间共享 ExpressRoute 线路的简单示意图。

大型云中的每个较小云用于表示属于组织中不同部门的订阅。 组织内的每个部门可以使用自己的订阅部署其服务，但可以共享单个 ExpressRoute 线路以连接回本地网络。 单个部门（在此示例中为 IT 部门）可以拥有 ExpressRoute 线路。 组织内的其他订阅可以使用 ExpressRoute 线路。

> [!NOTE]
> 专用线路的连接和带宽费用将应用于 ExpressRoute 线路所有者。 所有虚拟网络共享相同的带宽。
> 
> 

![跨订阅连接](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>管理
线路所有者是 ExpressRoute 线路资源的已授权超级用户。 线路所有者可以创建可由线路用户兑现的授权。 线路用户是虚拟网络网关的所有者（这些网关与 ExpressRoute 线路位于不同的订阅中）。 线路用户可以兑现授权（每个虚拟网络需要一个授权）。

线路所有者有权随时修改和撤消授权。 撤消授权将导致从撤消了访问权限的订阅中删除所有链路连接。

### <a name="circuit-owner-operations"></a>线路所有者操作

**创建授权**

线路所有者创建授权。 这样即可创建授权密钥，供线路用户用来将其虚拟网络网关连接到 ExpressRoute 线路。 一个授权只可用于一个连接。

以下 cmdlet 代码段演示如何创建授权：

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

        $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"


对此操作的响应将包含授权密钥和状态：

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**查看授权**

线路所有者可以通过运行以下 cmdlet 来查看针对特定线路发出的所有授权：

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit


**添加授权**

线路所有者可以使用以下 cmdlet 添加授权：

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit


**删除授权**

线路所有者可以通过运行以下 cmdlet 来撤消/删除对用户的授权：

    Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit    

**线路用户操作**

线路用户需有对等 ID 以及线路所有者提供的授权密钥。 授权密钥是一个 GUID。

可以从以下命令检查对等 ID。

    Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

**兑现连接授权**

线路用户可以通过运行以下 cmdlet 来兑现链接授权：

    $id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

**释放连接授权**

可以通过删除 ExpressRoute 线路与虚拟网络之间的连接来释放授权。

## <a name="next-steps"></a>后续步骤
有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。


