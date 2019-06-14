---
title: 关于 Azure 可用性区域中的区域冗余虚拟网络网关 | Microsoft Docs
description: 了解可用性区域中的 VPN 网关和 ExpressRoute 网关。
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 0ba818ef3c24d0e88e662adf87b22cc938fe5fab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60391036"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>关于 Azure 可用性区域中的区域冗余虚拟网络网关

可以在 [Azure 可用性区域](../availability-zones/az-overview.md)中部署 VPN 网关和 ExpressRoute 网关。 这样可以提高虚拟网络网关的复原性、可伸缩性和可用性。 如果在 Azure 可用性区域中部署网关，可以在地理位置和逻辑上将区域内的网关分隔开来，同时还能保护本地网络与 Azure 的连接免受区域级故障的影响。

### <a name="zrgw"></a>区域冗余网关

若要跨可用性区域自动部署虚拟网络网关，可以使用区域冗余虚拟网络网关。 使用区域冗余网关，可受益于区域复原能力，从而可以访问 Azure 上的任务关键型可扩展服务。

<br>
<br>

![区域冗余网关图](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>区块网关

若要在特定区域中部署网关，可使用区域网关。 部署区域网关时，网关的所有实例都部署在同一可用性区域中。

<br>
<br>

![区块网关图](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>网关 SKU

区域冗余网关和区域网关以新的网关 SKU 的形式提供。 我们已在 Azure AZ 区域中添加了新的虚拟网络网关 SKU。 这些 SKU 与 ExpressRoute 网关和 VPN 网关的相应现有 SKU 类似，不同之处在于它们是区域冗余网关和区域网关的专用 SKU。

新增的网关 SKU 包括：

### <a name="vpn-gateway"></a>VPN 网关

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>公共 IP SKU

区域冗余网关和区块网关都依赖 Azure 公共 IP 资源标准  SKU。 Azure 公共 IP 资源的配置决定了是部署区域冗余网关，还是部署区块网关。 如果使用基本  SKU 创建公共 IP 资源，网关不会有任何区域冗余，且网关资源具有区域性。

### <a name="pipzrg"></a>区域冗余网关

如果使用标准  公共 IP SKU 创建公共 IP 地址，但未指定区域，行为因网关是 VPN 网关还是 ExpressRoute 网关而异。 

* 对于 VPN 网关，两个网关实例会部署到这三个区域中的任意两个区域，以实现区域冗余。 
* 对于 ExpressRoute 网关，由于可以有超过两个实例，因此网关可以跨所有这三个区域部署。

### <a name="pipzg"></a>区块网关

如果使用标准  公共 IP SKU 创建公共 IP 地址，并指定区域（1、2 或 3），所有网关实例都会部署到同一区域中。

### <a name="piprg"></a>区域网关

如果使用基本  公共 IP SKU 创建公共 IP 地址，网关会部署为区域网关，并且不会内置有任何区域冗余。

## <a name="faq"></a>常见问题解答

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>部署这些新 SKU 时会发生什么变化？

从你的角度来看，可以部署区域冗余网关。 也就是说，所有网关实例都会跨 Azure 可用性区域部署，每个可用性区域都是不同的容错域和更新域。 这样可以提高网关的可靠性、可用性和区域故障复原性。

### <a name="can-i-use-the-azure-portal"></a>是否可以使用 Azure 门户？

是的，可以使用 Azure 门户部署新 SKU。 但是，你将仅在具有 Azure 可用性区域的 Azure 区域中看到这些新 SKU。

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>我可以在哪些区域中使用新 SKU？

新 SKU 可在具有 Azure 可用性区域的 Azure 区域（“美国中部”、“法国中部”、“北欧”、“西欧”和“美国西部 2”区域）中使用。 今后，我们将在其他 Azure 公共区域推出区域冗余网关。

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>我能否将现有虚拟网络网关更改/迁移/升级为区域冗余网关或区域网关？

暂不支持将现有虚拟网络网关迁移到区域冗余网关或区块网关。 不过，可以删除现有网关，并重新创建区域冗余网关或区块网关。

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>我能否在同一虚拟网络中同时部署 VPN 网关和 ExpressRoute 网关？

支持 VPN 网关和 ExpressRoute 网关同时共存于同一虚拟网络中。 但是，应为网关子网保留 /27 IP 地址范围。

## <a name="next-steps"></a>后续步骤

[创建区域冗余的虚拟网关](create-zone-redundant-vnet-gateway.md)
