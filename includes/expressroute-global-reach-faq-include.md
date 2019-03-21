---
title: include 文件
description: include 文件
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 551b1d4924c354eb6fd501c1b82ed306a7ea4822
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305174"
---
### <a name="what-is-expressroute-global-reach"></a>什么是 ExpressRoute Global Reach？

ExpressRoute Global Reach 是可以通过 ExpressRoute 服务和 Microsoft 全球网络连接本地网络的 Azure 服务。 例如，如果使用 ExpressRoute Global Reach，位于加利福利亚州的专用数据中心连接到硅谷中的 ExpressRoute，而位于德克萨斯州的另一个专用数据中心连接到达拉斯州的 ExpressRoute，则可以通过两个 ExpressRoute 连接将专用数据中心连接到一起，跨数据中心的流量将通过 Microsoft 的网络主干传递。

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>如何启用或禁用 ExpressRoute Global Reach？

可以通过将 ExpressRoute 线路连接到一起来启用 ExpressRoute Global Reach。 断开连接线路可以禁用该功能。 请参阅[配置](../articles/expressroute/expressroute-howto-set-global-reach.md)。

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>是否需要有 ExpressRoute 高级版才能使用 ExpressRoute Global Reach？

如果 ExpressRoute 线路位于同一地缘政治区域，则不需要 ExpressRoute 高级版即可将这些线路连接到一起。 如果两条 ExpressRoute 线路位于不同的地缘政治区域，则需要对两条线路使用 ExpressRoute 高级版，这样才能在两者之间建立连接。 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>ExpressRoute Global Reach 如何计费？

ExpressRoute 可在本地网络与 Microsoft 云服务之间建立连接。 ExpressRoute Global Reach 可以利用 Microsoft 的全球网络，通过现有的 ExpressRoute 线路在你自己的本地网络之间建立连接。 ExpressRoute Global Reach 独立于现有的 ExpressRoute 服务计费。 在每条 ExpressRoute 线路上启用此功能会产生附加费用。 ExpressRoute Global Reach 启用的本地网络之间的流量将在源位置根据传出费率计费，在目标位置根据传入费率计费。 费率基于线路所在的区域。 请参阅 <pricing page>

### <a name="where-is-expressroute-global-reach-supported"></a>哪些区域支持 ExpressRoute Global Reach？

[选择国家/地区或地方](../articles/expressroute/expressroute-global-reach.md)支持 ExpressRoute Global Reach。 必须在上述国家/地区或地方的对等位置创建 ExpressRoute 线路。

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>我有两个以上的本地网络，每个网络连接到 ExpressRoute 线路。 是否可以启用 ExpressRoute Global Reach，以将所有本地网络连接到一起？

可以，前提是这些线路位于受支持的国家/地区。 需要一次性连接两个 ExpressRoute 线路。 若要创建全网格式网络，需要枚举所有线路对并重复配置。 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>是否可以在同一对等位置中的两条 ExpressRoute 线路之间启用 ExpressRoute Global Reach？

不是。 两条线路必须来自不同的对等位置。 如果受支持国家/地区的某个市区包含多个 ExpressRoute 对等位置，则可以将该市区内不同对等位置创建的 ExpressRoute 线路连接到一起。 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>如果在线路 X 和 Y 之间以及线路 Y 和线路 Z 之间启用 ExpressRoute Global Reach，连接到线路 X 和线路 Z 的本地网络是否可以通过 Microsoft 的网络相互通信？

不是。 若要在任意两个本地网络之间实现连接，必须显式连接对应的 ExpressRoute 线路。 在上述示例中，必须连接线路 X 和线路 Z。 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>启用 ExpressRoute Global Reach 后，本地网络之间的网络吞吐量预期有多少？

ExpressRoute Global Reach 启用的本地网络之间的网络吞吐量以两条 ExpressRoute 线路中吞吐量较小者为准。 本地到 Azure 流量和内部部署的本地流量共享相同的线路并受到相同的带宽上限。 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>使用 ExpressRoute Global Reach 时，对于我可以播发的路由数和我将收到的路由数的限制是多少？

你可以在 Azure 专用对等互连上播发到 Microsoft 的路由数在标准线路上保持为 4000，在高级线路上为 10000。 你在 Azure 专用对等互连上将从 Microsoft 收到的路由数将是以下两者之和：你的 Azure 虚拟网络的路由数、来自通过 ExpressRoute Global Reach 连接的其他本地网络的路由数。 请确保在你的本地路由器上设置合适的最大前缀限制。 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>ExpressRoute Global Reach 的 SLA 是什么？

ExpressRoute Global Reach 将提供与常规 ExpressRoute 服务相同的[可用性 SLA](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/)。
