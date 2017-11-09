---
title: "Azure 网络 | Microsoft Docs"
description: "了解 Azure 网络服务与功能。"
services: networking
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: jdial
ms.openlocfilehash: 7ed018c8c9759bc497c5fea129257486f6128531
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-networking"></a>Azure 网络

Azure 提供可以结合使用或单独使用的各种网络功能。 请单击以下任一重要功能了解更多相关信息：
- [Azure 资源之间的连接](#connectivity)：在云中的安全专用虚拟网络内将 Azure 资源连接在一起。
- [Internet 连接](#internet-connectivity)：通过 Internet 与 Azure 资源相互通信。
- [本地连接](#on-premises-connectivity)：在 Internet 上通过虚拟专用网络 (VPN) 或者通过与 Azure 建立的专用连接将本地网络连接到 Azure 资源。
- [负载均衡和流量方向](#load-balancing)：对发往同一位置中的服务器的流量进行负载均衡，并将流量定向到不同位置中的服务器。
- [安全性](#security)：筛选网络子网或单个虚拟机 (VM) 之间的网络流量。
- [路由](#routing)：使用默认路由，或者完全控制 Azure 与本地资源之间的路由。
- [可管理性](#manageability)：监视器和管理 Azure 网络资源。
- [部署和配置工具](#tools)：使用基于 Web 的门户或跨平台命令行工具来部署和配置网络资源。

## <a name="Connectivity"></a>Azure 资源之间的连接

虚拟机、云服务、虚拟机规模集和 Azure 应用服务环境等 Azure 资源可以通过 Azure 虚拟网络 (VNet) 进行私密通信。 VNet 是对专用于[订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fnetworking%2ftoc.json)的 Azure 云进行的逻辑隔离。 可在每个 Azure 订阅和 Azure [区域](https://azure.microsoft.com/regions)中实现多个 VNet。 每个 VNet 与其他 VNet 隔离。 对于每个 VNet，可执行以下操作：

- 使用公共和专用 (RFC 1918) 地址指定自定义专用 IP 地址空间。 Azure 从分配的地址空间中向连接到 VNet 的资源分配一个专用 IP 地址。
- 将 VNet 细分为一个或多个子网，并向每个子网分配一部分 VNet 地址空间。
- 使用 Azure 提供的名称解析或指定自己的 DNS 服务器以供连接到 VNet 的资源使用。

若要了解有关 Azure 虚拟网络服务的详细信息，请参阅[虚拟网络概述](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。 VNet 之间可相互连接，因此，连接到任意一个 VNet 的资源都可与 VNet 之间的每个资源进行通信。 可使用以下两个中任意一个选项或使用这两个选项相互连接 VNet：

- **对等互连：**使连接到相同 Azure 区域中不同 Azure VNet 的资源可相互通信。 如果资源连接到同一 VNet，则 VNet 之间的带宽和延迟相同。 若要了解有关对等互连的详细信息，请参阅[虚拟网络对等互连概述](../virtual-network/virtual-network-peering-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。
- **VPN 网关：**使连接到不同 Azure 区域中不同 Azure VNet 的资源可相互通信。 VNet 之间的流量通过 Azure VPN 网关流动。 VNet 之间的带宽限制为网关的带宽。 若要详细了解如何将 VNet 连接到 VPN 网关，请参阅[配置跨区域的 VNet 到 VNet 连接](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。

## <a name="internet-connectivity"></a>Internet 连接

默认情况下，连接到 VNet 的所有 Azure 资源都具有 Internet 出站连接。 资源的专用 IP 地址是由 Azure 基础结构转换到公共 IP 地址中的源网络地址 (SNAT)。 若要了解出站网络连接的详细信息，请阅读[了解 Azure 中的出站连接](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。

若要从 Internet 入站通信到 Azure 资源或出站通信到不具 SNAT 的 Internet，则必须向资源分配一个公共 IP 地址。 若要详细了解公共 IP 地址，请阅读 [公共 IP 地址](../virtual-network/virtual-network-public-ip-address.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。

## <a name="on-premises-connectivity"></a>本地连接

可以通过 VPN 连接或直接专用连接安全访问 VNet 中的资源。 若要在 Azure 虚拟网络与本地网络之间发送网络流量，必须创建虚拟网络网关。 需配置网关的设置，以创建所需的连接类型：VPN 或 ExpressRoute。

可组合使用以下任何选项将本地网络连接到 VNet：

点到站点（基于 SSTP 的 VPN）

下图显示了多台计算机与一个 VNet 之间的独立点到站点连接：

![点到站点](./media/networking-overview/point-to-site.png)

此连接是在一台计算机与一个 VNet 之间建立的。 这种连接类型适用于刚开始使用 Azure 的人员或开发人员，因为该连接类型仅需对现有网络作出极少更改或不做任何更改。 此连接类型还可方便你从远程位置（例如会议室或家中）建立连接。 点到站点连接通常通过相同的虚拟网络网关与站点到站点连接结合使用。 此连接使用 SSTP 协议在计算机与 VNet 之间通过 Internet 提供加密通信。 由于流量遍历 Internet，因此点到站点 VPN 的延迟不可预测。

**站点到站点（IPsec/IKE VPN 隧道）**

![站点到站点](./media/networking-overview/site-to-site.png)

此连接是在本地 VPN 设备与 Azure VPN 网关之间建立的。 此连接类型可使授权的任何本地资源访问 VNet。 此连接是一个 IPSec/IKE VPN，该 VPN 通过 Internet 在本地设备和 Azure VPN 网关之间提供加密通信。 可将多个本地站点连接到同一个 VPN 网关。 每个站点上的本地 VPN 设备必须具有一个面向外部的且不在 NAT 后面的公共 IP 地址。 由于流量遍历 Internet，因此站点到站点连接的延迟不可预测。

**ExpressRoute（专用连接）**

![ExpressRoute](./media/networking-overview/expressroute.png)

此类连接是通过 ExpressRoute 合作伙伴在网络与 Azure 之间建立的。 此连接是专用连接。 流量不会遍历 Internet。 由于流量未遍历 Internet，因此 ExpressRoute 连接的延迟可预测。 ExpressRoute 可与站点到站点连接结合使用。

若要了解有关所有以前连接选项的详细信息，请阅读[连接拓扑图](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。

## <a name="load-balancing"></a>负载均衡和流量方向

Microsoft Azure 提供了多种服务，以便可以管理网络流量的分布和负载均衡方式。 可以单独或者结合使用以下任何功能：

**DNS 负载均衡**

Azure 流量管理器服务提供全局 DNS 负载均衡。 流量管理器根据以下路由方法之一，使用正常终结点的 IP 地址响应客户端：
- **地理：**根据客户端 DNS 查询的来源地理位置将客户端定向到特定的终结点（Azure、外部或嵌套）。 对于必须知道客户端的地理区域并基于该地理区域路由流量的方案，可以使用此方法。 示例包括遵守数据所有权要求、内容本地化和用户体验，以及测量来自不同区域的流量。
- **性能：**返回给客户端的 IP 地址“最靠近”客户端。 “最靠近”的终结点不一定是地理距离最近的终结点。 此方法通过测量网络延迟来确定最靠近的终结点。 流量管理器维护一份 Internet 延迟表，用于跟踪 IP 地址范围与每个 Azure 数据中心之间的往返时间。
- **优先级：**流量定向到主（最高优先级）终结点。 如果主终结点不可用，流量管理器会将流量路由到第二个终结点。 如果主终结点和辅助终结点都不可用，流量将转到第三个终结点，依此类推。 终结点的可用性取决于配置的状态（已启用或已禁用）和正在进行的终结点监视。
- **加权轮循机制：**对于每个请求，流量管理器会随机选择一个可用的终结点。 选择哪个终结点取决于分配到所有可用终结点的权重。 对所有终结点使用相同的权重会导致均匀分布流量。 对特定的终结点使用较高或较低的权重会导致这些终结点在 DNS 响应中的返回次数较多或较少。

下图显示了如何将针对 Web 应用程序的请求定向到 Web 应用终结点。 终结点也可以是其他 Azure 服务，例如 VM 和云服务。

![流量管理器](./media/networking-overview/traffic-manager.png)

客户端直接连接到该终结点。 当某个终结点运行不正常时，Azure 流量管理器可检测到这种状态，然后将客户端重定向到其他正常的终结点。 若要了解有关流量管理器的详细信息，请参阅 [Azure 流量管理器概述](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。

**应用程序负载均衡**

Azure 应用程序网关服务以服务形式提供应用程序传送控制器 (ADC)。 应用程序网关为应用程序提供各种第 7 层 (HTTP/HTTPS) 负载均衡功能，包括 Web 应用程序防火墙（用于保护 Web 应用程序，使其免受漏洞和攻击的影响）。 使用应用程序网关还可通过将 CPU 密集型 SSL 终端的负载卸载到应用程序网关来优化 Web 场的工作效率。 

其他第 7 层路由功能包括传入流量的轮循机制分布、基于 Cookie 的会话相关性、基于 URL 路径的路由，以及在单个应用程序网关后托管多个网站的功能。 可以将应用程序网关配置为面向 Internet 的网关、仅限内部访问的网关或二者合一的网关。 应用程序网关完全受 Azure 管理，可缩放且高度可用。 它提供丰富的诊断和日志记录功能以改进可管理性。 若要了解有关应用程序网关的详细信息，请参阅[应用程序网关概述](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。

下图显示了使用应用程序网关的基于 URL 路径的路由：

![应用程序网关](./media/networking-overview/application-gateway.png)

**网络负载均衡**

Azure 负载均衡器为所有 UDP 和 TCP 协议提供高性能、低延迟的第 4 层负载均衡。 它管理入站和出站连接。 可以配置公共和内部负载均衡终结点。 可以定义规则，以便将入站连接映射到后端池目标，并在其中包含 TCP 和 HTTP 运行状况探测选项来管理服务的可用性。 若要了解有关负载均衡器的详细信息，请参阅[负载均衡器概述](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。

下图显示了利用外部和内部负载均衡器的面向 Internet 的多层应用程序：

![负载均衡](./media/networking-overview/load-balancer.png)

## <a name="security"></a>安全性

可使用以下选项筛选传入和传出 Azure 资源的流量：

- **网络：**可以实现 Azure 网络安全组 (NSG) 来筛选 Azure 资源的入站和出站流量。 每个 NSG 包含一个或多个入站和出站规则。 每个规则指定用于筛选流量的源 IP 地址、目标 IP 地址、端口和协议。 可将 NSG 应用到单个子网和单个 VM。 有关 NSG 的详细信息，请参阅[网络安全组概述](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。
- **应用程序：**将应用程序网关与 Web 应用程序防火墙结合使用可以保护 Web 应用程序，使其免受漏洞和攻击的影响。 常见的示例包括 SQL 注入攻击、跨站点脚本和格式不当的标头。 应用程序网关可筛选掉这些流量，并阻止其传入 Web 服务器。 可以配置想要启用的规则。 还可以配置 SSL 协商策略，以便能够禁用某些策略。 若要详细了解 Web 应用程序防火墙，请参阅 [Web 应用程序防火墙](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。

如果 Azure 无法提供所需的网络功能，或者你要使用本地使用的网络应用程序，可以在 VM 中实施产品并将其连接到 VNet。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) 包含许多不同的 VM，其中已预配置了你当前使用的网络应用程序。 这些预配置的 VM 通常称为网络虚拟设备 (NVA)。 NVA 可用于防火墙等应用程序以及 WAN 优化。

## <a name="routing"></a>路由

Azure 创建默认的路由表，使用这些路由表可让连接到 VNet 中任何子网的资源相互通信。 可使用以下一种或两种类型的路由来替代 Azure 创建的默认路由：
- **用户定义：**可创建自定义路由表，其中包含可对每个子网控制流量路由位置的路由。 若要详细了解用户定义的路由，请阅读[用户定义的路由](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。
- **边界网关协议 (BGP)：**如果使用 Azure VPN 网关或 ExpressRoute 连接将 VNet 连接到本地网络，则可将 BGP 路由传播到 VNet。 BGP 是通常在 Internet 上使用的，用于在两个或更多网络之间交换路由和可访问性信息的标准路由协议。 在 Azure 虚拟网络的上下文中使用时，BGP 允许 Azure VPN 网关和本地 VPN 设备（称为 BGP 对等节点或邻居）交换“路由”，这些路由将通知这两个网关这些前缀的可用性和可访问性，以便这些前缀可通过涉及的网关或路由器。 BGP 还可以通过将 BGP 网关从一个 BGP 对等节点获知的路由传播到所有其他 BGP 对等节点来允许在多个网络之间传输路由。 若要了解有关 BGP 的详细信息，请参阅[使用 Azure VPN 网关的 BGP 概述](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。

## <a name="manageability"></a>可管理性

Azure 提供以下工具用于监视和管理网络：
- **活动日志：**所有 Azure 资源都会生成活动日志，其中提供有关执行的操作、操作状态以及操作发起者的信息。 若要详细了解活动日志，请参阅[活动日志概述](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。
- **诊断日志：**定期和自发性事件由网络资源创建，记录在 Azure 存储帐户中并发送到事件中心或 Azure Log Analytics。 诊断日志提供资源运行状况的见解。 诊断日志是针对负载均衡器（面向 Internet）、网络安全组、路由和应用程序网关提供的。 若要详细了解诊断日志，请参阅[诊断日志概述](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。
- **指标：**指标是在一段时间内从资源收集的性能度量值与计数器。 使用指标可以基于阈值触发警报。 指标目前适用于应用程序网关。 若要详细了解指标，请参阅[指标概述](../monitoring-and-diagnostics/monitoring-overview-metrics.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。
- **故障排除：**可直接在 Azure 门户中访问故障排除信息。 这些信息可帮助诊断 ExpressRoute、VPN 网关、应用程序网关、网络安全日志、路由、DNS、负载均衡器和流量管理器的常见问题。
- **基于角色的访问控制 (RBAC)：**控制谁可以使用基于角色的访问控制 (RBAC) 创建和管理网络资源。 请参阅 [RBAC 入门](../active-directory/role-based-access-control-what-is.md?toc=%2fazure%2fnetworking%2ftoc.json)一文，了解有关 RBAC 的详细信息。 
- **数据包捕获：**使用 Azure 网络观察程序服务可以通过 VM 中的某个扩展在 VM 上运行数据包捕获。 此功能适用于 Linux 和 Windows VM。 若要详细了解数据包捕获，请参阅[数据包捕获概述](../network-watcher/network-watcher-packet-capture-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。
- **验证 IP 流：**使用网络观察程序可以验证 Azure VM 与远程资源之间的 IP 流，以确定是允许还是拒绝了数据包。 此功能可让管理员快速诊断连接问题。 若要详细了解如何验证 IP 流，请参阅[“IP 流验证”概述](../network-watcher/network-watcher-ip-flow-verify-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。
- **排查 VPN 连接问题：**使用网络观察程序的 VPN 故障排除功能可以查询连接或网关，并验证资源的运行状况。 若要详细了解如何排查 VPN 连接问题，请参阅 [VPN 连接故障排除概述](../network-watcher/network-watcher-troubleshoot-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。
- **查看网络拓扑：**使用网络观察程序查看 VNet 中网络资源的图形表示形式。 若要详细了解如何查看网络拓扑，请参阅[拓扑概述](../network-watcher/network-watcher-topology-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。

## <a name="tools"></a>部署和配置工具

可使用以下任何工具来部署和配置 Azure 网络资源：

- **Azure 门户：**在浏览器中运行的图形用户界面。 打开 [Azure 门户](http://portal.azure.com)。
- **Azure PowerShell：**用于在 Windows 计算机上管理 Azure 的命令行工具。 请阅读 [Azure PowerShell 概述](/powershell/azure/overview?view=azurermps-3.8.0?toc=%2fazure%2fnetworking%2ftoc.json)一文，了解有关 Azure PowerShell 的详细信息。
- **Azure 命令行接口 (CLI)：**用于在 Linux、macOS 或 Windows 计算机上管理 Azure 的命令行工具。 请阅读 [Azure CLI 概述](/cli/azure/get-started-with-azure-cli?toc=%2fazure%2fnetworking%2ftoc.json)一文，了解有关 Azure CLI 的详细信息。
- **Azure Resource Manager 模板：**用于定义 Azure 解决方案的基础结构和配置的文件（采用 JSON 格式）。 使用模板，可以在解决方案的整个生命周期内重复部署该解决方案，确保以一致的状态部署资源。 若要详细了解如何创作模板，请参阅[有关创作模板的最佳做法](../azure-resource-manager/resource-manager-template-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json)一文。 可以使用 Azure 门户、CLI 或 PowerShell 部署模板。 若要立即开始使用模板，请部署 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/?term=network)库中预配置的众多模板之一。 

## <a name="pricing"></a>定价

有些 Azure 网络服务是收费的，而有些则是免费的。 有关详细信息，请查看[虚拟网络](https://azure.microsoft.com/pricing/details/virtual-network)、[VPN 网关](https://azure.microsoft.com/pricing/details/vpn-gateway)、[应用程序网关](https://azure.microsoft.com/en-us/pricing/details/application-gateway/)、[负载均衡器](https://azure.microsoft.com/pricing/details/load-balancer)、[网络观察程序](https://azure.microsoft.com/pricing/details/network-watcher)、[DNS](https://azure.microsoft.com/pricing/details/dns)、[流量管理器](https://azure.microsoft.com/pricing/details/traffic-manager)和 [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) 定价页。

## <a name="next-steps"></a>后续步骤

- 完成[创建首个虚拟网络](../virtual-network/virtual-network-get-started-vnet-subnet.md?toc=%2fazure%2fnetworking%2ftoc.json)一文中的步骤，创建自己的首个虚拟网络，并将几个 VM 连接到此网络。
- 完成[配置点到站点连接](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)一文中的步骤，将计算机连接到 VNet。
- 完成[创建面向 Internet 的负载均衡器](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)一文中的步骤，对发往公共服务器的 Internet 流量进行负载均衡。
