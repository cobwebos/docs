---
title: Azure ExpressRoute：使用 Global Reach 连接到 Microsoft 云
description: 了解 Azure ExpressRoute Global Reach 如何将 ExpressRoute 线路链接在一起，以便在本地网络之间建立专用网络。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: e3f9105037c049a53f1b7b99da96dd857070fcc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987623"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute 是将本地网络连接到 Microsoft 云的一种私有且灵活的方式。 你可以从你的专用数据中心或企业网络访问许多 Microsoft 云服务，例如 Azure 和 Microsoft 365。 例如，你可能在旧金山有一家分公司，其 ExpressRoute 线路位于硅谷，在伦敦有另一家分公司，其 ExpressRoute 线路位于同一城市。 这两个分支机构都具有与美国西部和英国南部的 Azure 资源的高速连接。 但是，分支机构不能彼此连接并直接发送数据。 换句话说，10.0.1.0/24 可以将数据发送到 10.0.3.0/24 和 10.0.4.0/24 网络，而不能发送到 10.0.2.0/24 网络。

![显示未与 Express Route 一起链接 Global Reach 的线路的关系图。][1]

使用 **ExpressRoute Global Reach**，可以将 ExpressRoute 线路链接到一起，以在本地网络之间建立专用网络。 在上面的示例中，通过添加 ExpressRoute Global Reach，你的旧金山分公司 (10.0.1.0/24) 便可以通过现有的 ExpressRoute 线路和 Microsoft 的全球网络直接与你的伦敦分公司 (10.0.2.0/24) 交换数据。 

![显示与 Express Route Global Reach 关联的线路的关系图。][2]

## <a name="use-case"></a>使用案例
ExpressRoute Global Reach 旨在补充服务提供商的 WAN 实施，并连接你在世界各地的分公司。 例如，如果你的服务提供商主要在美国运营并且已连接你在美国的所有分公司，但服务提供商不在日本和香港运营，则可以使用 ExpressRoute Global Reach 与本地服务提供商合作，Microsoft 将使用 ExpressRoute 和我们的全球网络将你在那里的分公司连接到美国的分公司。

![显示快速路由 Global Reach 用例的关系图。][3]

## <a name="availability"></a>可用性 
以下位置支持 ExpressRoute Global Reach。 

> [!NOTE] 
> 若要在不同的 [地缘政治区域](expressroute-locations-providers.md#locations)之间启用 ExpressRoute Global Reach，线路必须是 **高级 SKU**。

* 澳大利亚
* 加拿大
* 法国
* 德国
* 香港特别行政区
* 爱尔兰
* 日本
* 韩国
* 荷兰
* 新西兰
* 挪威
* 新加坡
* 瑞典
* 瑞士
* 英国
* 美国

## <a name="next-steps"></a>后续步骤
- 查看 [GLOBAL REACH 常见问题解答](expressroute-faqs.md#globalreach)。
- 了解如何 [启用 Global Reach](expressroute-howto-set-global-reach.md)。
- 了解如何将 [ExpressRoute 线路链接到虚拟网络](expressroute-howto-linkvnet-arm.md)。

<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "不带 Global Reach 时的示意图"
[2]: ./media/expressroute-global-reach/2.png "带有 Global Reach 时的示意图"
[3]: ./media/expressroute-global-reach/3.png "Global Reach 的用例"
