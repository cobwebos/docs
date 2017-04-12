---
title: "使用 Azure Active Directory 应用程序代理时的网络拓扑注意事项 | Microsoft 文档"
description: "介绍使用 Azure AD 应用程序代理时的网络拓扑注意事项。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6869453e0776405841890978eef97f549be97541
ms.lasthandoff: 04/03/2017


---

# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>使用 Azure Active Directory 应用程序代理时的网络拓扑注意事项

本文介绍使用 Azure Active Directory (Azure AD) 应用程序代理远程发布和访问应用程序时的网络拓扑注意事项。

## <a name="traffic-flow"></a>流量流

通过 Azure AD 应用程序代理发布应用程序时，从用户发往目标后端应用程序的所有流量将流经以下跃点：

* 跃点 1：从用户到 AD 应用程序代理服务在 Azure 上的公共终结点
* 跃点 2：从应用程序代理服务到连接器
* 跃点 3：从连接器到目标应用程序

 ![显示从用户发往目标应用程序的流量流的示意图](./media/application-proxy-network-topologies/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>租户位置和应用程序代理服务

注册 Azure AD 租户时，租户所在的区域根据指定的国家/地区确定。 启用应用程序代理时，租户的应用程序代理服务实例将显示在 Azure AD 租户所在的同一区域或者与它最靠近的区域中。

例如，如果你的 Azure AD 租户所在的区域为欧盟 (EU)，则所有 Azure AD 应用程序代理连接器将使用位于 EU 的 Azure 数据中心内的服务实例。 这也意味着，你的所有用户在尝试访问发布的应用程序时，都要通过此位置中的应用程序代理服务实例进行访问。

## <a name="considerations-for-reducing-latency"></a>有关降低延迟的注意事项

所有代理解决方案都会给网络连接造成延迟。 无论选择哪种代理或 VPN 解决方案作为远程访问解决方案，它始终会包含一组服务器来实现与企业网络内部的连接。

组织通常已在其外围网络中添置了服务器终结点。 但如果使用 Azure AD 应用程序代理，则无需构建外围网络。 这是因为，流量将流经云中的代理服务，而连接器驻留在企业网络中。

### <a name="connector-placement"></a>连接器的位置

应用程序代理根据租户的位置选择实例的位置。 因此，需确定要将连接器安装在哪个位置，以便能够定义网络流量的延迟特征。

设置应用程序代理服务时，应提出以下问题：

* 应用位于何处？
* 访问应用的大部分用户位于何处？
* 应用程序代理实例位于何处（取决于租户）？
* 是否已经与 Azure 数据中心建立专用网络连接（例如，使用 Azure ExpressRoute 或类似的 VPN）？

连接器的位置决定了跃点 2 和跃点 3 的延迟（如前一部分中所述）。 评估连接器的位置时，应考虑以下因素：

* 连接器需要与数据中心保持直通。 这样，连接器便可执行 Kerberos 约束委派 (KCD) 操作，从而让你在后端应用程序中执行单一登录 (SSO)。
* 连接器通常安全在较靠近应用程序的位置，以缩减从连接器到应用程序的访问时间。

### <a name="general-approach-to-minimize-latency"></a>最小化延迟的常规方法

可以通过优化每个网络跃点来尽量降低端到端流量的延迟。 可通过以下方式优化每个跃点：

* 减少跃点两个端点之间的距离。
* 选择要遍历的适当网络。 例如，遍历专用网络而不遍历公共 Internet 可能速度更快，因为前者是专用链路。

如果在 Azure 与企业网络之间建立了专用 VPN 或 ExpressRoute 链路，可以利用它。

## <a name="focus-your-optimizing-strategy"></a>注重优化策略

因为用户可能通过 Internet 远程访问应用，因此始终应该注重优化跃点 2 和 3。 下面是可以整合的一些常用模式。

### <a name="pattern-1-optimize-hop-3"></a>模式 1：优化跃点 3

若要优化跃点 3，应该将连接器放在客户网络中靠近目标应用程序的位置。 连接器有可能需要与域控制器建立直通连接，因此这种做法可以带来优势。 这种方法足以应对大多数方案。 （事实上，我们的大多数客户都遵循此模式。）

 ![显示跃点 3 优化的示意图，其中的连接器放置在靠近目标应用程序的位置](./media/application-proxy-network-topologies/application-proxy-hop3.png)


> [!NOTE]
有某些情况下，需要同时优化跃点 2 和跃点 3 才能获得所需的延迟特征。 例如，如果在网络与 Azure 数据中心之间设置了 VPN 或 ExpressRoute，则可以优化这两个跃点。

### <a name="pattern-2-take-advantage-of-expressroute-with-public-peering"></a>模式 2：利用结合公共对等互连的 ExpressRoute

如果结合公共对等互连设置了 ExpressRoute，可为跃点 2 利用更快的 ExpressRoute 连接。 （已通过将连接器放置在网络中靠近应用的位置，对跃点 3 做了优化。）

![显示使用 ExpressRoute 连接优化跃点 2 的示意图](./media/application-proxy-network-topologies/application-proxy-expressroute-public.png)

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>模式 3：利用结合专用对等互连的 ExpressRoute

如果已在 Azure 与企业网络之间结合专用对等互连设置了专用 VPN 或 ExpressRoute，则还可以使用另一个选项。 在此配置中，Azure 中的虚拟网络通常被视为企业网络的扩展。 因此，可将连接器安装在 Azure 数据中心，同时仍可满足跃点 3 的连接器到应用的连接的低延迟要求。

因为流量通过专用连接流动，因此延迟不会加重。 这样做还会带来另一个好处，那就是可以改善跃点 2 的延迟特征。 这是因为，应用程序代理的服务到连接器连接是一个短距离跃点。 连接器就安装在 Azure 数据中心内靠近 Azure AD 租户的位置（因此也很靠近应用程序代理）。

![显示 Azure 数据中心内安装的连接器的示意图](./media/application-proxy-network-topologies/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>其他方法

尽管本文的重点是连接器的位置，但是，也可以通过改变应用程序的位置来获得更好的延迟特征。

越来越多的组织正在将其网络移入托管环境。 这样，便可以将应用放在同样属于企业网络一部分的托管环境中，而这些应用仍会保留在域中。 在这种情况下，可向新的应用程序位置应用前面部分中所述的模式。

考虑使用连接器组来针对不同位置和网络中的应用分组。 考虑此选项时，请参阅 [Azure AD 域服务](../active-directory-domain-services/active-directory-ds-overview.md)。

## <a name="common-scenarios"></a>常见方案

本部分将演练几个用例。 假设 Azure AD 租户（因此也包括代理服务终结点）位于美国 (US)。 在全球的其他区域，这些用例中所述的注意事项通常也适用。

### <a name="use-case-1"></a>用例 1

该应用位于组织在美国部署的网络中，其用户位于同一区域。 Azure 数据中心与企业网络之间不存在 ExpressRoute 或 VPN。

**建议：**遵循前一部分中所述的模式 1。 为了降低延迟，可以根据需要考虑使用 ExpressRoute。

这是一个简单的模式。 可以通过将连接器放置在应用附近来优化跃点 3。 这也是一种自然而然的选择，因为安装的连接器通常与应用和数据中心建立直通连接，以便执行 KCD 操作。

![显示美国地图以及如何在本用例中排列跃点的示意图](./media/application-proxy-network-topologies/application-proxy-pattern1.png)

### <a name="use-case-2"></a>用例 2

该应用位于组织在美国部署的网络中，其用户遍布全球各地。 Azure 数据中心与企业网络之间不存在 ExpressRoute 或 VPN。

**建议：**遵循前一部分中所述的模式 2。 为了降低延迟，可以根据需要考虑使用 ExpressRoute。

同样，常见的模式是优化跃点 3，其中的连接器放置在应用附近。 如果将整个跃点 3 放在同一区域，则它的系统开销通常不大。 但是，根据用户所在的位置，跃点 1 的系统开销可能更大，因为所有用户将访问位于美国的应用程序代理实例。 值得注意的是，由于用户遍布全球各地，任何代理解决方案将具有类似的特征。

![显示全球各洲地图以及如何在本用例中排列跃点的示意图](./media/application-proxy-network-topologies/application-proxy-pattern2.png)

### <a name="use-case-3"></a>用例 3

该应用位于组织在美国部署的网络中。 Azure 与企业网络之间存在结合公共对等互连的 ExpressRoute。

**建议：**使连接器尽量靠近应用。 系统自动为跃点 2 使用 ExpressRoute。 可遵循前一部分中所述的模式 2。

如果 ExpressRoute 链路使用公共对等互连，代理与连接器之间的流量将通过该链路流动。 跃点 2 的延迟将得到优化。

![显示美国地图以及如何在本用例中排列跃点的示意图](./media/application-proxy-network-topologies/application-proxy-pattern3.png)

### <a name="use-case-4"></a>用例 4

该应用位于组织在美国部署的网络中。 Azure 与企业网络之间存在结合专用对等互连的 ExpressRoute。

**建议：**将连接器放置在通过 ExpressRoute 专用对等互连连接到企业网络的 Azure 数据中心内。 可遵循前一部分中所述的模式 3。

可将连接器放置在 Azure 数据中心内。 由于连接器仍旧通过专用网络与应用程序和数据中心建立直通连接，跃点 3 将保持优化。 此外，跃点 2 会进一步优化。

![显示美国地图以及如何在本用例中排列跃点的示意图](./media/application-proxy-network-topologies/application-proxy-pattern4.png)

### <a name="use-case-5"></a>用例 5

该应用位于组织在欧盟部署的网络中，其大多数用户位于美国。

**建议：**将连接器放置在应用的附近。 由于美国用户访问的应用程序代理实例正好处于同一区域，因此跃点 1 的系统开销也不会太高。 跃点 3 经过优化。 但是，在此用例中，跃点 2 的系统开销通常较高。

![显示全球各洲地图以及如何在本用例中排列跃点的示意图](./media/application-proxy-network-topologies/application-proxy-pattern5a.png)

考虑根据前面有关模式 2 和 3 的部分中所述使用 ExpressRoute。

![显示全球各洲地图以及如何在本用例中排列跃点的示意图](./media/application-proxy-network-topologies/application-proxy-pattern5b.png)

还可以考虑对此用例使用另一种变化形式。 如果组织中的大多数用户位于美国，则组织有可能也会将网络扩展到美国。 如果是这样，可将连接器放置在美国，使用专用的内部企业网络线路访问位于欧盟的应用程序。 因此，跃点 2 和跃点 3 将得到优化。

![显示全球各洲地图以及如何在本用例中排列跃点的示意图](./media/application-proxy-network-topologies/application-proxy-pattern5c.png)

## <a name="next-steps"></a>后续步骤

- [启用应用程序代理](active-directory-application-proxy-enable.md)
- [启用单一登录](active-directory-application-proxy-sso-using-kcd.md)
- [启用条件性访问](active-directory-application-proxy-conditional-access.md)
- [解决使用应用程序代理时遇到的问题](active-directory-application-proxy-troubleshoot.md)

