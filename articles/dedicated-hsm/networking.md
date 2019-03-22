---
title: 网络注意事项 - Azure 专用 HSM | Microsoft Docs
description: 适用于 Azure 专用 HSM 部署的网络注意事项概述
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: barclayn
ms.openlocfilehash: aed0eb6ba4cdaa57d282ac4484e0c27c0697afb5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58083124"
---
# <a name="azure-dedicated-hsm-networking"></a>Azure 专用 HSM 网络

Azure 专用 HSM 需要高度安全的网络环境。 无论是要 Azure 云连接到客户的云 IT 环境（本地）、使用分布式应用程序还是实现高可用性方案，都是如此。 Azure 网络可以提供这种环境，但必须解决四个不同的方面。

- 在 Azure 中的虚拟网络 (VNet) 内创建 HSM 设备
- 本地连接到基于云的资源以配置和管理 HSM 设备
- 创建并连接虚拟网络以互连应用程序资源和 HSM 设备
- 跨区域连接虚拟网络以实现相互通信和高可用性方案

## <a name="virtual-network-for-your-dedicated-hsms"></a>专用 HSM 的虚拟网络

专用 HSM 集成到虚拟网络，并定位在 Azure 中客户自己的专用网络内。 这样，便可以从虚拟网络中的虚拟机或计算资源访问设备。  
有关将 Azure 服务集成到虚拟网络以及虚拟网络提供的功能的详细信息，请参阅 [Azure 服务的虚拟网络](../virtual-network/virtual-network-for-azure-services.md)文档。

### <a name="virtual-networks"></a>虚拟网络

在预配专用 HSM 设备之前，客户首先需要在 Azure 中创建虚拟网络，或使用客户订阅中现存的虚拟网络。 虚拟网络定义专用 HSM 设备的安全周边。 有关创建虚拟网络的详细信息，请参阅[虚拟网络文档](../virtual-network/virtual-networks-overview.md)。

### <a name="subnets"></a>子网

子网将部署资源的虚拟网络分段成可由 Azure 资源使用的独立地址空间。 专用 HSM 部署在虚拟网络的子网中。 客户子网中部署的每个专用 HSM 设备将从此子网接收专用 IP 地址。 需将部署 HSM 设备的子网显式委托给服务：Microsoft.HardwareSecurityModules/dedicatedHSMs。 这会向 HSM 服务授予特定的权限，使其可部署到该子网中。 专用 HSM 委托会对子网施加特定的策略限制。 委托的子网目前不支持网络安全组 (NSG) 和用户定义的路由 (UDR)。 因此，将某个子网委托给专用 HSM 后，该子网只可用于部署 HSM 资源。 将其他任何客户资源部署到该子网都会失败。


### <a name="expressroute-gateway"></a>ExpressRoute 网关

当前体系结构的要求是，在需要将 HSM 设备定位到的客户子网中配置一个 ER 网关，以便能够将 HSM 设备集成到 Azure 中。 不能使用此 ER 网关将本地位置连接到 Azure 中的客户 HSM 设备。

## <a name="connecting-your-on-premises-it-to-azure"></a>将本地 IT 连接到 Azure

创建基于云的资源时，一个典型的要求是与本地 IT 资源建立专用连接。 如果使用专用 HSM，则主流方法是使用 HSM 客户端软件配置 HSM 设备，以及执行备份、从 HSM 提取用于分析的日志等活动。 由于存在多种选项，此处的关键决策点在于连接的性质。  最灵活的选项是站点到站点 VPN，因为可能有多个本地资源需要与 Azure 云中的资源（包括 HSM）安全通信。 这就要求客户组织提供 VPN 设备来方便连接。 如果本地只有一个终结点（例如，单个管理工作站），则可以使用点到站点 VPN 连接。
有关连接选项的详细信息，请参阅 [VPN 网关规划选项](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)。

> [!NOTE]
> 目前，ExpressRoute 并不适合用来与本地资源建立连接。 另请注意，按上述方法使用的 ExpressRoute 网关并不适合用来与本地基础设施建立连接。

### <a name="point-to-site-vpn"></a>点到站点 VPN

点到站点虚拟专用网络是与单个本地终结点建立安全连接的最简单方式。 如果你打算只对基于 Azure 的专用 HSM 使用一个管理工作站，则此选项可能很合适。

### <a name="site-to-site-vpn"></a>站点到站点 VPN

使用站点到站点虚拟专用网络可在基于 Azure 的专用 HSM 与本地 IT 之间实现安全通信。 进行安全通信的原因之一是为本地 HSM 提供备份设施，并在两者之间建立连接以运行备份。

## <a name="connecting-virtual-networks"></a>连接虚拟网络

专用 HSM 的典型部署体系结构的起点是创建并预配了 HSM 设备的单个虚拟网络和相应的子网。 在同一区域中，可能还为使用专用 HSM 的应用程序组件创建了其他虚拟网络和子网。 为了跨这些网络实现通信，我们将使用虚拟网络对等互连。

### <a name="virtual-network-peering"></a>虚拟网络对等互连

如果某个区域中有多个虚拟网络需要访问彼此的资源，可以使用虚拟网络对等互连在这些虚拟网络之间创建安全信道。  虚拟网络对等互连不仅提供安全通信，而且还确保在 Azure 中的资源之间提供低延迟、高带宽的连接。

![网络对等互连](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>跨 Azure 区域连接

HSM 设备可以通过软件库将流量重定向到备用 HSM。 如果设备发生故障，或者失去了对设备的访问权限，则流量重定向会很有帮助。 在其他区域中部署 HSM 并在跨区域的虚拟网络之间实现通信，可以缓解区域级别的故障问题。

### <a name="cross-region-ha-using-vpn-gateway"></a>使用 VPN 网关实现跨区域的高可用性

对于全局分布式应用程序或高可用性区域故障转移方案，需要跨区域连接虚拟网络。 借助 Azure 专用 HSM，可以使用一个能在两个虚拟网络之间提供安全隧道的 VPN 网关来实现高可用性。 有关使用 VPN 网关建立 VNet 到 VNet 连接的详细信息，请参阅标题为[什么是 VPN 网关？](../vpn-gateway/vpn-gateway-about-vpngateways.md#V2V)的文章

> [!NOTE]
> 目前，全局 VNet 对等互连在使用专用 HSM 的跨区域连接方案中不可用，为此应改用 VPN 网关。 

![global-vnet](media/networking/global-vnet.png)

## <a name="next-steps"></a>后续步骤

- [常见问题](faq.md)
- [可支持性](supportability.md)
- [高可用性](high-availability.md)
- [物理安全性](physical-security.md)
- [监视](monitoring.md)
- [部署体系结构](deployment-architecture.md)