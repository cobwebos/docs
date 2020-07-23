---
title: 关于升级线路带宽 |Azure ExpressRoute
description: 本文介绍升级 ExpressRoute 线路带宽的最佳实践
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: cherylmc
ms.openlocfilehash: a8f5aaa7b2a054aa31198779414387cebf0f0fbd
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537031"
---
# <a name="about-upgrading-expressroute-circuit-bandwidth"></a>关于升级 ExpressRoute 线路带宽

ExpressRoute 可与 Microsoft 的全球网络建立专用和专用连接。 连接由 ExpressRoute 伙伴的网络或与 Microsoft 企业边缘（MSEE）设备的直接连接来推动。 配置和测试物理连接后，可以通过创建 ExpressRoute 线路并配置对等互连，启用第2层和第3层连接。

## <a name="upgrade-circuit-bandwidth"></a><a name="upgrade"></a>升级线路带宽

为了升级线路带宽，ExpressRoute 直接或 ExpressRoute 伙伴需要具有[足够的可用带宽](#considerations)，升级才能成功。

如果容量可用，可以使用以下方法升级线路：

* [Azure 门户](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [PowerShell](expressroute-howto-circuit-arm.md#modify)
* [Azure CLI](howto-circuit-cli.md#modify)

## <a name="capacity-considerations"></a><a name="considerations"></a>容量注意事项

### <a name="insufficient-expressroute-partner-bandwidth"></a><a name="bandwidth"></a>ExpressRoute 伙伴带宽不足

如果 ExpressRoute 伙伴没有足够的容量，则需要创建一个新线路，并将其配置为所需的带宽。 为了保持连接性，请不要删除旧线路，直至预配新创建的线路，配置对等互连，并且（有关专用对等互连）已设置 ExpressRoute 虚拟网络网关的连接对象。

如果 ExpressRoute 伙伴没有足够的可用容量，则需要在所需的对等互连位置请求额外的容量。 预配新容量后，可以使用 "[升级线路带宽](#upgrade)" 部分中的文章中包含的步骤来创建新线路、配置连接并删除旧线路。


### <a name="insufficient-expressroute-direct-bandwidth"></a><a name="bandwidth"></a>ExpressRoute 直接带宽不足

如果 ExpressRoute 直接没有足够的容量，则可以删除与不再需要的 ExpressRoute 直接资源关联的线路，或创建新的 ExpressRoute 直接资源。 有关管理 ExpressRoute 直接资源的指南，请参阅[如何配置 Expressroute 直通](how-to-expressroute-direct-portal.md)。

## <a name="next-steps"></a>后续步骤

* [创建和修改线路](expressroute-howto-circuit-portal-resource-manager.md)
* [创建和修改对等配置](expressroute-howto-routing-portal-resource-manager.md)
* [将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-portal-resource-manager.md)
