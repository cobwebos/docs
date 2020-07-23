---
title: 创建和管理 Azure ExpressRoute 公共对等互连
description: 了解和管理 Azure 公共对等互连
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: cherylmc
ms.openlocfilehash: 8c1afac834fb9abb2cbf82f16f046a1624b251f1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "79481127"
---
# <a name="create-and-manage-expressroute-public-peering"></a>创建和管理 ExpressRoute 公共对等互连

> [!div class="op_single_selector"]
> * [文章 - 公共对等互连](about-public-peering.md)
> * [视频 - 公共对等互连](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [文章 - Microsoft 对等互连](expressroute-circuit-peerings.md#microsoftpeering)
>

可以参照本文来创建和管理 ExpressRoute 线路的公共对等互连路由配置。 还可以检查状态，以及更新/删除和取消预配对等互连。 本文适用于已弃用公共对等互连之前创建的资源管理器线路。 如果以前存在的线路（在公共对等互连之前已创建），则可以使用[Azure PowerShell](#powershell)、 [Azure CLI](#cli)和[Azure 门户](#portal)来管理/配置公共对等互连。

>[!NOTE]
>已弃用公共对等互连。 不能在新的 ExpressRoute 线路上创建公共对等互连。 如果你使用的是新的 ExpressRoute 线路，请将[Microsoft 对等互连](expressroute-circuit-peerings.md#microsoftpeering)用于 Azure 服务。
>

## <a name="connectivity"></a>连接

始终会从 WAN 发起到 Microsoft Azure 服务的连接。 Microsoft Azure 服务无法通过此路由域发起到网络的连接。 如果为 ExpressRoute 线路启用了 Azure 公共对等互连，则可以通过该线路访问 [Azure 中使用的公共 IP 范围](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。

启用公共对等互连后，可以连接到大多数 Azure 服务。 我们不允许选择要将路由播发到的服务。

* Azure 存储、SQL 数据库和网站等服务是通过公共 IP 地址提供的。
* 通过公共对等路由域可以私下连接到公共 IP 地址（包括云服务的 VIP）上托管的服务。
* 可以将公共对等域连接到外围网络，并从 WAN 连接到公共 IP 地址上的所有 Azure 服务，而无需通过 Internet 连接。

## <a name="services"></a><a name="services"></a>服务

本部分显示通过公共对等互连提供的服务。 由于不推荐使用公共对等互连，因此不会计划向公共对等互连添加新的或其他服务。 如果你使用公共对等互连，而你要使用的服务只能通过 Microsoft 对等互连获得支持，则必须切换到 Microsoft 对等互连。 有关受支持服务的列表，请参阅 [Microsoft 对等互连](expressroute-faqs.md#microsoft-peering)。

支持：

* Power BI
* 支持大多数 Azure 服务。 请直接对要使用的服务进行确认来验证是否支持。

不支持：
  * CDN
  * Azure Front Door
  * 多重身份验证服务器（旧版）
  * 流量管理器

若要验证特定服务的可用性，可以查看该服务的文档，确定是否存在针对该服务发布的保留范围。 然后，可以查找目标服务的 IP 范围，并将其与 [Azure IP 范围和服务标记 - 公有云 XML 文件](https://www.microsoft.com/download/details.aspx?id=56519)中列出的范围进行比较。 或者，可以为相应服务开具支持票证以便说明。

## <a name="peering-comparison"></a><a name="compare"></a>对等互连比较

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> Azure 公共对等互连有 1 个 NAT IP 地址与每个 BGP 会话相关联。 对于大于 2 个 NAT IP 地址，请转到 Microsoft 对等互连。 通过 Microsoft 对等互连，可以配置自己的 NAT 分配，并使用路由筛选器进行选择性前缀播发。 有关详细信息，请参阅[转到 Microsoft 对等互连](https://docs.microsoft.com/azure/expressroute/how-to-move-peering)。
>

## <a name="custom-route-filters"></a>自定义路由筛选器

可以在网络中定义自定义路由筛选器，以只使用所需的路由。 有关路由配置的详细信息，请参阅[路由](expressroute-routing.md)页。

## <a name="azure-powershell-steps"></a><a name="powershell"></a>Azure PowerShell 步骤


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

由于不推荐使用公共对等互连，因此无法在新的 ExpressRoute 线路上配置公共对等互连。

1. 验证是否有已预配且已启用的 ExpressRoute 线路。 使用以下示例：

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   其响应类似于如下示例：

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                      "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
2. 配置线路的 Azure 公共对等互连。 在继续下一步之前，请确保已准备以下信息。

   * 主链路的 /30 子网。 这必须是有效的公共 IPv4 前缀。
   * 辅助链路的 /30 子网。 这必须是有效的公共 IPv4 前缀。
   * 用于建立此对等互连的有效 VLAN ID。 请确保线路中没有其他对等互连使用同一个 VLAN ID。
   * 对等互连的 AS 编号。 可以使用 2 字节和 4 字节 AS 编号。
   * 可选：
   * MD5 哈希（如果选择使用）。

   运行以下示例为线路配置 Azure 公共对等互连

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   如果选择使用 MD5 哈希，请使用以下示例：

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > 请确保将 AS 编号指定为对等互连 ASN，而不是客户 ASN。
   > 
   >

### <a name="to-get-azure-public-peering-details"></a><a name="getpublic"></a>获取 Azure 公共对等互连详细信息

可以使用以下 cmdlet 来获取配置详细信息：

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>更新 Azure 公共对等互连配置

可以使用以下示例来更新配置的任何部分。 在此示例中，线路的 VLAN ID 将从 200 更新为 600。

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>删除 Azure 公共对等互连

可以运行以下示例来删除对等互连配置：

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-cli-steps"></a><a name="cli"></a>Azure CLI 步骤


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. 检查 ExpressRoute 线路以确保它已预配并已启用。 使用以下示例：

   ```azurecli-interactive
   az network express-route list
   ```

   其响应类似于如下示例：

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

2. 配置线路的 Azure 公共对等互连。 在继续下一步之前，请确保已准备以下信息。

   * 主链路的 /30 子网。 这必须是有效的公共 IPv4 前缀。
   * 辅助链路的 /30 子网。 这必须是有效的公共 IPv4 前缀。
   * 用于建立此对等互连的有效 VLAN ID。 请确保线路中没有其他对等互连使用同一个 VLAN ID。
   * 对等互连的 AS 编号。 可以使用 2 字节和 4 字节 AS 编号。
   * **可选** - MD5 哈希（如果选择使用）。

   运行以下示例为线路配置 Azure 公共对等互连：

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   如果选择使用 MD5 哈希，请使用以下示例：

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > 请确保将 AS 编号指定为对等互连 ASN，而不是客户 ASN。

### <a name="to-view-azure-public-peering-details"></a><a name="getpublic"></a>查看 Azure 公共对等互连详细信息

可以使用以下示例来获取配置详细信息：

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

输出类似于以下示例：

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>更新 Azure 公共对等互连配置

可以使用以下示例来更新配置的任何部分。 在此示例中，线路的 VLAN ID 将从 200 更新为 600。

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>删除 Azure 公共对等互连

可以运行以下示例来删除对等互连配置：

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="azure-portal-steps"></a><a name="portal"></a>Azure 门户步骤

若要配置对等互连，请使用本文中包含的 PowerShell 或 CLI 步骤。 若要管理对等互连，可以使用以下部分。 这些步骤类似于[在门户中管理 Microsoft 对等互连](expressroute-howto-routing-portal-resource-manager.md#msft)的步骤，可以将其用作参考。

### <a name="to-view-azure-public-peering-details"></a><a name="get"></a>查看 Azure 公共对等互连详细信息

可通过在门户中选择对等互连来查看 Azure 公共对等互连的属性。

### <a name="to-update-azure-public-peering-configuration"></a><a name="update"></a>更新 Azure 公共对等互连配置

选择对等互连所对应的行，然后修改对等互连属性。

### <a name="to-delete-azure-public-peering"></a><a name="delete"></a>删除 Azure 公共对等互连

通过选择“删除”图标来删除对等互连配置。

## <a name="next-steps"></a>后续步骤

下一步，[将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)。

* 有关 ExpressRoute 工作流的详细信息，请参阅 [ExpressRoute 工作流](expressroute-workflows.md)。
* 有关线路对等互连的详细信息，请参阅 [ExpressRoute 线路和路由域](expressroute-circuit-peerings.md)。
* 有关使用虚拟网络的详细信息，请参阅 [虚拟网络概述](../virtual-network/virtual-networks-overview.md)。