---
title: 关于虚拟 WAN 定价
titleSuffix: Azure Virtual WAN
description: 本文介绍常见的虚拟 WAN 定价问题
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: reyandap
ms.custom: references_pricing
ms.openlocfilehash: b4025990a1a62351d3971d788558dea8ecb390ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327951"
---
# <a name="about-virtual-wan-pricing"></a>关于虚拟 WAN 定价

Azure 虚拟 WAN 在统一框架中提供多个网络和安全服务。 它基于中心辐射型体系结构，在该体系结构中，中心由中心提供的各种服务（例如 VPN、ExpressRoute、用户 VPN (点到站点) 、防火墙、路由等）进行了 Microsoft 管理。

虚拟 WAN 中的每个服务都是定价。 因此，建议单个价格不适用于虚拟 WAN。 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)提供一种机制，用于根据虚拟 WAN 中预配的服务来派生成本。 本文介绍有关虚拟 WAN 定价的常见问题。

>[!NOTE]
>有关最新定价信息，请参阅 [虚拟 WAN 定价](https://azure.microsoft.com/pricing/details/virtual-wan/)。
>

## <a name="common-pricing-questions"></a><a name="questions"></a>常见定价问题

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>缩放单位是什么？

**缩放单位**为虚拟中心中的站点到站点 (S2S) 、点到站点 (P2S) 和 EXPRESSROUTE (ER) 提供聚合容量的单位。 例如：

* **1 S2S vpn 缩放单位** 表示在虚拟中心成本 $ 0.361/小时内部署了 500 Mbps vpn 网关 (两个实例的恢复) 的总容量。
* **1 ER 缩放单位** 指的是虚拟中心开销 $ 0.42/hr 中的 2 Gbps er 网关。
* **5 ER 刻度单位** 会将虚拟中心 VNet 中的总速率为 10 Gbps，以 $ 0.42 * 5/小时为单位。 ER 每6到10个缩放单位增加了 $ 0.25/hr。

### <a name="what-is-a-connection-unit"></a><a name="connection-unit"></a>什么是连接单元？

**连接单元**适用于连接到 Azure 网关的任何本地/非 Microsoft 终结点。 对于站点到站点 VPN，这意味着分支。 对于用户 VPN (点到站点) ，这意味着远程用户。 对于 ExpressRoute，这意味着 ExpressRoute 线路连接。<br>例如：

* 一个在虚拟中心中连接到 Azure VPN 的分支连接会降低 $ 0.05/小时。 因此，连接到 Azure 虚拟中心的100分支连接的成本为 $ 0.05 * 100/小时。

* 连接到虚拟中心的两个 ExpressRoute 线路连接的成本为 $ 0.05 * 2/小时。

* 连接到 Azure 虚拟中心 P2S 网关的三个远程用户连接将成本 $ 0.03 * 3/小时。

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>如何计算数据传输费用？

* 进入 Azure 的任何流量都不收费。 通过 VPN、ExpressRoute 或点到站点用户 VPN) 连接离开 Azure (的流量受标准 [Azure 数据传输费用](https://azure.microsoft.com/pricing/details/bandwidth/)的限制。

* 对于虚拟 WAN 中心之间的数据传输费用，以及与源集线器不同的区域中的远程虚拟 WAN 集线器或 VNet，数据传输费用适用于离开集线器的流量。 示例：离开美国东部中心的流量将按 $ 0.02/GB 向美国西部中心收费。 进入美国西部中心的流量不收取费用。 下表显示了费用。

下表使用以下缩写形式：北美 {MEA}、{EU：欧洲}、{：中东东非}、{OC：大洋洲 (澳大利亚中部和澳大利亚中部 2) }、{LATAM：拉丁美洲} 

**大陆定价 ( * ) **

| Intra-Continent| 价格 ($/GB) |
|---|---|
| 与|$0.02 |
| 欧盟到欧盟 |$0.02 |
| 亚太地区 (排除中国) |$0.10 |
| MEA 到 MEA|$0.16 |
| LATAM-LATAM |$0.16 |
| OC-OC|$0.12 |

**大陆 ( * ) **

| Inter-Continental| 价格 ($/GB) |
|---|---|
| 从 a 到欧盟或欧盟到 |$0.07 |
| 从 LATAM 到任意位置 |$0.17 |
| 从 MEA 到任意位置 |$0.17 |
| 从大洋洲到任意位置 |$0.12 |
| 从亚洲 (除了中国) 到任何地方 |$0.12 |

 ( * ) 从2020年8月1日起，可能会收取一些费用。

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>标准集线器费和标准集线器处理费用之间的区别是什么？

虚拟 WAN 分为两种风格：

* 一种 **基本的虚拟 WAN**，用户可以在其中部署多个中心并欣赏 VPN 站点到站点连接。 基本的虚拟 WAN 没有高级功能，如完全网格集线器、ExpressRoute 连接、用户 VPN/点到站点 VPN 连接、VNet 到 VNet 传递连接、VPN 和 ExpressRoute 传输连接，或 Azure 防火墙等。对于基本虚拟 WAN 中的中心，无基本费用或数据处理费用。

* **标准虚拟 WAN**提供了高级功能，如完全网格集线器、ExpressRoute 连接、用户 VPN/点到站点 VPN 连接、VNet 到 VNet 的可传递连接、VPN 和 ExpressRoute 传输连接、Azure 防火墙等。所有虚拟中心路由都由在虚拟中心内启用多个服务的路由器提供。 中心有一个基本费用，价格为 $ 0.25/小时。 对于 VNet 到 VNet 的传输连接，还需要对虚拟中心路由器中的数据处理收费。 请参阅下图。

 在下面的 **示例** 中，有两个 VNET，vnet 1 和 vnet 2。 假设正在从 VNET 1 中的 VM 向 VNET 2 中的其他 VM 发送 1 Gbps 数据。 以下费用适用：

* 虚拟中心基本费用 $ 0.25/hr

* 1 Gbps 的虚拟中心数据处理费用为 $ 0.02/GB

**示例**

   :::image type="content" source="./media/pricing-concepts/figure-1.png" alt-text="示例":::

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。

* 有关当前定价，请参阅 [虚拟 WAN 定价](https://azure.microsoft.com/pricing/details/virtual-wan/)。
