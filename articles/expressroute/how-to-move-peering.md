---
title: 将 Azure ExpressRoute 上的公共对等互连移动到 Microsoft 对等互连 | Microsoft 文档
description: 本文介绍了在 ExpressRoute 上将公共对等互连移动到 Microsoft 对等互连的步骤。
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2018
ms.author: cherylmc
ms.openlocfilehash: f34fabc95d5b56edc6e37c323bebf60bd98c8b90
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>将公共对等互连移动到 Microsoft 对等互连

ExpressRoute 支持将 Microsoft 对等互连与 Azure PaaS 服务的路由筛选器结合使用，例如 Azure 存储和 Azure SQL 数据库。 你现在仅需要一个路由域即可访问 Microsoft PaaS 和 SaaS 服务。 可以利用路由筛选器为要使用的 Azure 区域选择性地播发 PaaS 服务前缀。

本文可帮助你在不停机的情况下将公共对等互连配置移动到 Microsoft 对等互连。 有关路由域和对等互连的详细信息，请参阅 [ExpressRoute 线路和路由域](expressroute-circuit-peerings.md)。


## <a name="before"></a>准备工作

* 若要连接到 Microsoft 对等互连，你需要设置和管理 NAT。 连接服务提供商可以将 NAT 作为托管服务进行设置和管理。 如果计划在 Microsoft 对等互连上访问 Azure PaaS 和 Azure SaaS 服务，请务必正确设置 NAT IP 池大小。 有关适用于 ExpressRoute 的 NAT 详细信息，请参阅 [Microsoft 对等互连的 NAT 要求](expressroute-nat.md#nat-requirements-for-microsoft-peering)。

* 如果使用公共对等互连，并且当前为用于访问 [Azure 存储](../storage/common/storage-network-security.md)或 [Azure SQL 数据库](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md)的公共 IP 地址设置了 IP 网络规则，则需要确保配置了 Microsoft 对等互连的 NAT IP 池包含在 Azure 存储帐户或 Azure SQL 帐户的公共 IP 地址列表中。

* 若要移动到 Microsoft 对等互连而不停机，则需按本文介绍的顺序使用这些步骤。

## <a name="create"></a>1.创建 Microsoft 对等互连

如果尚未创建 Microsoft 对等互连，请使用以下任意文章创建 Microsoft 对等互连。 如果连接服务提供商提供第 3 层托管服务，则可以请求连接服务提供商为你的线路启用 Microsoft 对等互连。

  * [使用 Azure 门户创建 Microsoft 对等互连](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [使用 Azure Powershell 创建 Microsoft 对等互连](expressroute-howto-routing-arm.md#msft)
  * [使用 Azure CLI 创建 Microsoft 对等互连](howto-routing-cli.md#msft)

## <a name="validate"></a>2.验证 Microsoft 对等互连已启用

验证 Microsoft 对等互连已启用，且播发的公用前缀处于已配置状态。

  * [Azure 门户](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3.配置路由筛选器并连接到线路

默认情况下，新的 Microsoft 对等互连不播发任何前缀，直至路由筛选器连接到线路。 创建路由筛选器规则时，可以为要用于 Azure PaaS 服务的 Azure 区域指定服务社区列表，如以下屏幕截图所示：

![合并公共对等互连](.\media\how-to-move-peering\public.png)

使用以下任一文章配置路由筛选器：

  * [使用 Azure 门户为 Microsoft 对等互连配置路由筛选器](how-to-routefilter-portal.md)
  * [使用 Azure PowerShell 为 Microsoft 对等互连配置路由筛选器](how-to-routefilter-powershell.md)
  * [使用 Azure CLI 为 Microsoft 对等互连配置路由筛选器](how-to-routefilter-cli.md)

## <a name="delete"></a>4.删除公共对等互连

在验证 Microsoft 对等互连已配置且你要使用的前缀已在 Microsoft 对等互连上正确播发之后，随后即可删除公共对等互连。 若要删除公共对等互连，请使用以下任一文章：

  * [使用 Azure 门户删除 Azure 公共对等互连](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [使用 Azure PowerShell 删除 Azure 公共对等互连](expressroute-howto-routing-arm.md#deletepublic)
  * [使用 CLI 删除 Azure 公共对等互连](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5.查看对等互连
  
在 Azure 门户中可以看到所有 ExpressRoute 线路和对等互连的列表。 有关详细信息，请参阅[查看 Microsoft 对等互连详细信息](expressroute-howto-routing-portal-resource-manager.md#getmsft)。

## <a name="next-steps"></a>后续步骤

有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。
