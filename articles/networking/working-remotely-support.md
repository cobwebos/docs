---
title: 使用 Azure 网络服务远程工作
description: 本页介绍如何使用可用于远程工作的 Azure 网络服务，以及如何缓解因远程工作人数增加而导致的流量问题。
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: bcdd938365e50da1f5ae0e830e86692f1b915123
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618915"
---
# <a name="working-remotely-using-azure-networking-services"></a>使用 Azure 网络服务远程工作

>[!NOTE]
> 本文介绍如何利用 Azure 网络服务、Microsoft 网络和 Azure 合作伙伴生态系统远程工作并缓解因 COVID-19 危机而可能面临的网络问题。

本文介绍了组织可用于为其用户设置远程访问或在高峰使用期间用额外容量补充其现有解决方案的选项。 网络架构师面临以下挑战：

- 解决网络利用率的提高问题。
- 为公司和客户中的更多员工提供可靠的安全连接。
- 提供与全球远程位置的连接。

并非所有网络（例如，专用广域网和企业核心网络）都遇到来自峰值远程工作负载的拥塞。 瓶颈通常只报告在家庭宽带网络和公司本地网络的VPN网关。

网络规划人员可以通过记住不同的流量类型需要不同的网络处理优先级以及一些智能负载重定向/分发来帮助缓解瓶颈并缓解网络拥塞。 例如，医生与患者互动的实时远程电敏流量非常重要，并且对延迟/抖动非常敏感。 但是，在存储之间复制相同的流量并不敏感。 以前的流量必须通过最佳网络路径路由，服务质量更高;而通过次优路线路由后续流量是可以接受的。



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>共享我们的最佳实践 - Azure 网络专为弹性和高可用性而设计

Azure 旨在承受资源利用率的突然变化，在高峰利用期间可以大有帮助。 此外，微软维护并运营着世界上最大的网络之一。 Microsoft 的网络专为高可用性而设计，可以承受不同类型的故障：从单个网络元素故障到整个区域的故障。

Microsoft 网络旨在满足各种网络流量的要求，并为不同类型的网络流量提供最佳性能，包括 Skype 和 Teams、CDN、实时大数据分析、Azure 存储、必应和 Xbox 的延迟敏感多媒体流量。 为了为不同类型的流量提供最佳性能，Microsoft 网络吸引了所有注定传输或想要传输的流量，其资源尽可能接近流量的来源。

>[!NOTE] 
>使用下面描述的 Azure 网络功能利用 Microsoft 全球网络的流量吸引行为提供更好的客户网络体验。 Microsoft 网络的流量吸引行为有助于尽快从在高峰使用期间可能会遇到拥塞的第一英里/最后一英里网络卸载流量。
>

## <a name="enable-employees-to-work-remotely"></a>使员工能够远程工作

Azure VPN 网关同时支持点到站点 （P2S） 和站点到站点 （S2S） VPN 连接。 使用 Azure VPN 网关可以缩放员工的连接以安全地访问 Azure 部署的资源和本地资源。 有关详细信息，请参阅[如何允许用户远程工作](../vpn-gateway/work-remotely-support.md)。 

如果使用安全套接字隧道协议 （SSTP），则并发连接数限制为 128。 为了获得更多的连接，我们建议过渡到 OpenVPN 或 IKEv2。 有关详细信息，请参阅从[SSTP 过渡到 OpenVPN 协议或 IKEv2。](../vpn-gateway/ikev2-openvpn-from-sstp.md
)

要访问部署在 Azure 中的资源，远程开发人员可以使用 Azure Bastion 解决方案，而不是 VPN 连接来获取安全外壳访问（RDP 或 SSH），而无需访问 VM 上的公共 IP。 有关详细信息，请参阅使用[Azure 堡垒 远程工作](../bastion/work-remotely-support.md)。

对于聚合大规模 VPN 连接，支持不同本地全局位置、不同区域中心和分支虚拟网络中的资源之间的任意连接，以及优化多个家庭宽带网络的利用率，您可以使用 Azure 虚拟 WAN。 有关详细信息，请参阅[从家庭需求中努力满足工作需求？这里是 Azure 虚拟 WAN 可以提供帮助的地方](../virtual-wan/work-remotely-support.md)。

支持远程工作人员的另一种方法是部署托管在 Azure 虚拟网络中的虚拟桌面基础结构 （VDI），该基础结构（VDI）通过 Azure 防火墙进行保护。 例如，Windows 虚拟桌面 （WVD） 是在 Azure 中运行的桌面和应用虚拟化服务。 使用 Windows 虚拟桌面，您可以在 Azure 订阅中设置可扩展且灵活的环境，而无需运行任何其他网关服务器。 您只对虚拟网络中的 WVD 虚拟机负责。 有关详细信息，请参阅[Azure 防火墙远程工作支持](../firewall/remote-work-support.md)。 

Azure 还具有一组丰富的生态系统合作伙伴。 我们的合作伙伴 Azure 上的网络虚拟设备还可以帮助扩展 VPN 连接。 有关详细信息，请参阅[远程工作的网络虚拟设备 （NVA） 注意事项](../vpn-gateway/nva-work-remotely-support.md)。

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>扩展员工连接以访问全球分布的资源

以下 Azure 服务可帮助员工访问全局分布的资源。 您的资源可能位于任何 Azure 区域、本地网络，甚至位于其他公共或私有云中。 

- **Azure 虚拟网络对等**：如果将资源部署在多个 Azure 区域和/或者使用多个虚拟网络聚合远程工作员工的连接，则可以使用虚拟网络对等互连在多个 Azure 虚拟网络之间建立连接。 有关详细信息，请参阅[虚拟网络对等互连][VNet-peer]。

- **基于 Azure VPN 的解决方案**：对于通过 P2S 或 S2S VPN 连接到 Azure 的远程员工，可以通过在本地网络和 Azure VPN 网关之间配置 S2S VPN 来启用对本地网络的访问。 有关详细信息，请参阅[创建站点到站点的连接][S2S]。

- **ExpressRoute**：使用 ExpressRoute 专用对等互连，可以在 Azure 部署与本地基础结构或位于主机位置设施中的基础结构之间启用专用连接。 ExpressRoute 通过 Microsoft 对等互连，还允许从本地网络访问 Microsoft 中的公共终结点。 ExpressRoute 连接不通过公共 Internet 。 它们提供安全的连接、可靠性、更高的吞吐量，比 Internet 上的典型连接具有更低且一致的延迟。 有关详细信息，请参阅 [ExpressRoute 概述][ExR]。 利用您现有的网络提供商，这已经是我们[ExpressRoute 合作伙伴生态系统][ExR-eco]的一部分，可以帮助缩短与 Microsoft 进行大型带宽连接的时间。  使用[ExpressRoute Direct，][ExR-D]您可以将本地网络直接连接到 Microsoft 主干。 ExpressRoute Direct 提供两种不同的线路速率选项，双 10 Gbps 或 100 Gbps。 

- **Azure 虚拟 WAN**：Azure 虚拟 WAN 允许 VPN 连接和 ExpressRoute 电路之间的无缝互操作性。 如前所述，Azure 虚拟 WAN 还支持不同预部署全局位置、不同区域中心和分支虚拟网络中的资源之间的任意连接

## <a name="scale-customer-connectivity-to-frontend-resources"></a>扩展客户与前端资源的连接

在越来越多的人上网的时候，许多公司网站的客户流量都有所增加。 Azure 应用程序网关可帮助管理此增加的前端工作负荷。 有关详细信息，请参阅[应用程序网关高流量支持](../application-gateway/high-traffic-support.md)。

## <a name="microsoft-support-for-multi-cloud-traffic"></a>微软支持多云流量

对于在其他公共云中的部署，Microsoft 可以提供全局连接。 Azure 虚拟广域网、VPN 或 ExpressRoute 在这方面可以提供帮助。 要将连接从 Azure 扩展到其他云，可以在两个云之间配置 S2S VPN。 您还可以使用 ExpressRoute 建立从 Azure 到其他公共云的连接。 Oracle 云是 ExpressRoute 合作伙伴生态系统的一部分。 您可以在[Azure 和 Oracle 云基础结构之间建立直接互连][Az-OCI]。 属于 ExpressRoute 合作伙伴生态系统的大多数服务提供商还提供与其他公共云的专用连接。 利用这些服务提供商，您可以通过 ExpressRoute 在 Azure 中的部署与其他云之间建立专用连接。

## <a name="next-steps"></a>后续步骤

以下文章讨论了如何使用不同的 Azure 网络功能来扩展用户以远程工作：

| **文章** | **上次更新** |
| --- | --- |
| [如何使用户能够远程工作](../vpn-gateway/work-remotely-support.md) | 2020年3月23日 |
| [努力满足家庭需求的工作？这里是 Azure 虚拟 WAN 可以提供帮助的地方](../virtual-wan/work-remotely-support.md) | 2020年3月23日 |
| [应用程序网关高流量支持](../application-gateway/high-traffic-support.md) | 2020年3月23日 |
| [远程工作的网络虚拟设备 （NVA） 注意事项](../vpn-gateway/nva-work-remotely-support.md)| 2020年3月23日 |
| [从 SSTP 过渡到 OpenVPN 协议或 IKEv2](https://go.microsoft.com/fwlink/?linkid=2124112) | 2020年3月23日 |
| [使用 Azure 堡垒远程工作](../bastion/work-remotely-support.md) | 2020年3月23日 |
| [使用 Azure 快速路由创建混合连接以支持远程用户](../expressroute/work-remotely-support.md) | 2020年3月23日 |
| [Azure 防火墙远程工作支持](../firewall/remote-work-support.md)|2020年3月25日|

<!--Link References-->
[VNet-peer]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[S2S]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[ExR-eco]: https://docs.microsoft.com/azure/expressroute/expressroute-locations
[ExR-D]: https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about
[Az-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking
