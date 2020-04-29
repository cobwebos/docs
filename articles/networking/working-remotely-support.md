---
title: 使用 Azure 网络服务远程进行工作
description: 本页介绍如何使用可用的 Azure 网络服务实现远程工作，以及如何缓解因远程工作者增多而出现的流量问题。
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: e0e5806ec59cd2d75111db213d8511488d043eec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80982848"
---
# <a name="working-remotely-using-azure-networking-services"></a>使用 Azure 网络服务远程进行工作

>[!NOTE]
> 本文介绍如何利用 Azure 网络服务、Microsoft 网络和 Azure 合作伙伴生态系统远程进行工作，以及如何缓解因 COVID-19（新冠病毒）危机而面临的网络问题。

本文将介绍可供组织用来为用户设置远程访问，或者在使用高峰期通过附加容量补充现有解决方案的选项。 网络架构师面临着以下挑战：

- 解决由于网络使用率提高而导致的问题。
- 为公司和客户的更多员工提供安全可靠的连接。
- 在全球范围内提供与远程位置的连接。

在远程工作者负载高峰期，并非所有网络都会出现拥塞（例如，专用 WAN 和企业核心网络就不会出现拥塞）。 通常只有家庭宽带网络以及企业本地网络的 VPN 网关才会报告瓶颈。

网络规划人员可以通过记住不同的流量类型需要不同的网络处理优先级，以及通过使用某种智能负载重定向/分配方法，来帮助缓解瓶颈并减轻网络拥塞问题。 例如，医生与患者之间的实时远程医疗交互流量的重要性就很高，对网络延迟/波动的敏感性也很高。 而如果相同的流量用于存储之间的复制，则它对延迟就不敏感。 对于前一种流量，必须通过服务质量较高的最佳网络路径进行路由；而对于后一种流量，可以接受通过次优路径进行路由。



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>共享我们的最佳实践-Azure 网络适用于弹性和高可用性

Azure 设计用于经受资源利用率突然改变，并在高峰利用率期间极大地帮助。 另外，Microsoft 还维护并运营一个世界上最大的网络。 Microsoft 的网络旨在实现可承受不同类型故障的高可用性：从单个网络元素失败到整个区域发生故障。

Microsoft 网络设计为满足要求，并为不同类型的网络通信提供最佳性能，包括为 Skype 和团队、CDN、实时大数据分析、Azure 存储、必应和 Xbox 延迟敏感多媒体通信。 为了提供不同类型的流量的最佳性能，Microsoft 网络 attracts 了所有要传输的流量，或想要将其资源尽可能接近流量来源的流量。

>[!NOTE] 
>使用下面所述的 Azure 网络功能可利用 Microsoft 全球网络的流量引力行为，提供更好的客户网络体验。 Microsoft 网络的流量引力行为有助于从第一个/最后一个英里网络中尽快加载流量，这可能会在高峰利用率期间遇到堵塞。
>

## <a name="enable-employees-to-work-remotely"></a>使员工能够远程工作

Azure VPN 网关支持点到站点 (P2S) 和站点到站点 (S2S) VPN 连接。 使用 Azure VPN 网关可以缩放员工的连接，使其能够安全访问 Azure 部署的资源和本地资源。 有关详细信息，请参阅[如何使用户能够远程工作](../vpn-gateway/work-remotely-support.md)。 

如果使用安全套接字隧道协议 (SSTP)，并发连接数将限制为 128。 若要获得更多的连接，我们建议转换到 OpenVPN 或 IKEv2。 有关详细信息，请参阅[从 SSTP 转换到 OpenVPN 协议或 IKEv2](../vpn-gateway/ikev2-openvpn-from-sstp.md
)。

若要访问 Azure 中部署的资源，远程开发人员可以使用 Azure 堡垒解决方案，而不是 VPN 连接来获取安全外壳访问（RDP 或 SSH），而无需访问要访问的 Vm 上的公共 Ip。 有关详细信息，请参阅[使用 Azure 堡垒远程工作](../bastion/work-remotely-support.md)。

若要聚合大规模 VPN 连接，以支持不同区域中心和辐射虚拟网络中不同本地全局位置的资源之间的任何连接，并优化多个 home 宽带网络的利用率，可以使用 Azure 虚拟 WAN。 有关详细信息，请参阅[从家庭需求开始工作所需的工作？Azure 虚拟 WAN 可在此处提供帮助](../virtual-wan/work-remotely-support.md)。

支持远程工作人员的另一种方法是部署在 Azure 虚拟网络中托管的虚拟桌面基础结构（VDI），并使用 Azure 防火墙进行保护。 例如，Windows 虚拟桌面（WVD）是在 Azure 中运行的桌面和应用虚拟化服务。 使用 Windows 虚拟桌面，可以在 Azure 订阅中设置可伸缩的灵活环境，而无需运行任何其他网关服务器。 你只负责虚拟网络中的 WVD 虚拟机。 有关详细信息，请参阅[Azure 防火墙远程工作支持](../firewall/remote-work-support.md)。 

Azure 还提供丰富的一组 eco 系统合作伙伴。 Azure 上的合作伙伴网络虚拟设备还可以帮助缩放 VPN 连接。 有关详细信息，请参阅[远程工作的网络虚拟设备（NVA）注意事项](../vpn-gateway/nva-work-remotely-support.md)。

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>扩展员工的连接以访问全局分布式资源

以下 Azure 服务可帮助员工访问全局分布式资源。 你的资源可以位于任何 Azure 区域、本地网络，甚至其他公有云或私有云。 

- **Azure 虚拟网络对等互连**：如果你在多个 azure 区域中部署资源和/或使用多个虚拟网络聚合远程工作员工的连接，则可以使用虚拟网络对等互连在多个 azure 虚拟网络之间建立连接。 有关详细信息，请参阅[虚拟网络对等互连][VNet-peer]。

- **基于 AZURE VPN 的解决方案**：对于通过 P2S 或 S2S vpn 连接到 azure 的远程员工，可以通过配置本地网络与 azure VPN 网关之间的 S2S VPN，来启用对本地网络的访问。 有关详细信息，请参阅[创建站点到站点连接][S2S]。

- **Expressroute**：使用 expressroute 专用对等互连，可以在 Azure 部署和本地基础结构或共同位置设施中的基础结构之间启用专用连接。 通过 Microsoft 对等互连，ExpressRoute 还允许从本地网络访问 Microsoft 中的公共终结点。 ExpressRoute 连接不通过公共 Internet 。 与通过 Internet 的典型连接相比，它们提供安全的连接性、可靠性、更高的吞吐量，且持续时间较低且一致。 有关详细信息，请参阅 [ExpressRoute 概述][ExR]。 利用已成为[ExpressRoute 合作伙伴生态系统][ExR-eco]的一部分的现有网络提供商，可以帮助减少与 Microsoft 建立大带宽连接的时间。  使用[ExpressRoute Direct][ExR-D] ，可以直接将本地网络连接到 Microsoft 骨干。 ExpressRoute Direct 提供两个不同的线路速率选项，即两个 10 Gbps 或 100 Gbps。 

- **Azure 虚拟 wan**： AZURE 虚拟 WAN 允许 VPN 连接和 ExpressRoute 线路之间的无缝互操作。 如前文所述，Azure 虚拟 WAN 还支持不同的区域中心和辐射虚拟网络中不同本地全局位置的资源之间的任何连接。

## <a name="scale-customer-connectivity-to-frontend-resources"></a>将客户连接规模调整到前端资源

在越来越多的人上网的时候，许多公司网站都会经历更多的客户流量。 Azure 应用程序网关可帮助管理这一增加的前端工作负载。 有关详细信息，请参阅[应用程序网关高流量支持](../application-gateway/high-traffic-support.md)。

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Microsoft 对多云流量的支持

对于其他公有云中的部署，Microsoft 可以提供全球连接。 Azure 虚拟 WAN、VPN 或 ExpressRoute 有助于这一点。 若要将连接从 Azure 扩展到其他云，可以在两个云之间配置 S2S VPN。 还可以使用 ExpressRoute 建立从 Azure 到其他公有云的连接。 Oracle 云是 ExpressRoute 合作伙伴生态系统的一部分。 可以在[Azure 与 Oracle 云基础结构之间建立直接互连][Az-OCI]。 大多数属于 ExpressRoute 伙伴生态系统的服务提供商也提供与其他公有云的专用连接。 利用这些服务提供程序，可以通过 ExpressRoute 在 Azure 和其他云中的部署之间建立专用连接。

## <a name="next-steps"></a>后续步骤

以下文章介绍了如何使用不同的 Azure 网络功能来扩展用户远程工作：

| **下文** | **说明** |
| --- | --- |
| [如何让用户远程工作](../vpn-gateway/work-remotely-support.md) | 查看可用的选项，为用户设置远程访问权限，或使用组织的额外容量对现有解决方案进行补充。|
| [想要从家庭需求中努力工作？Azure 虚拟 WAN 可在此处提供帮助](../virtual-wan/work-remotely-support.md) | 使用 Azure 虚拟 WAN 满足组织的远程连接需求。|
| [应用程序网关高流量支持](../application-gateway/high-traffic-support.md) | 通过 Web 应用程序防火墙（WAF）使用应用程序网关，以提供可缩放且安全的方式来管理 web 应用程序的流量。 |
| [远程工作的网络虚拟设备（NVA）注意事项](../vpn-gateway/nva-work-remotely-support.md)|查看有关利用 Azure 中的 Nva 提供远程访问解决方案的指南。 |
| [从 SSTP 过渡到 OpenVPN 协议或 IKEv2](https://go.microsoft.com/fwlink/?linkid=2124112) | 通过转换到 OpenVPN 协议或 IKEv2 来克服 SSTP 的128并发连接限制。|
| [使用 Azure 堡垒远程工作](../bastion/work-remotely-support.md) | 直接在 Azure 门户中提供与 Azure 虚拟网络中的虚拟机的安全且无缝的 RDP/SSH 连接，而无需使用公共 IP 地址。 |
| [使用 Azure ExpressRoute 创建混合连接来支持远程用户](../expressroute/work-remotely-support.md) | 使用 ExpressRoute 进行混合连接，使组织中的用户能够远程工作。|
| [Azure 防火墙远程工作支持](../firewall/remote-work-support.md)|使用 Azure 防火墙保护 Azure 虚拟网络资源。 |

<!--Link References-->
[VNet-peer]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[S2S]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[ExR-eco]: https://docs.microsoft.com/azure/expressroute/expressroute-locations
[ExR-D]: https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about
[Az-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking
