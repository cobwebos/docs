---
title: Azure 上的 SAP HANA（大型实例）的网络体系结构 | Microsoft Docs
description: 有关如何部署 Azure 上的 SAP HANA（大型实例）的网络体系结构。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 724a91b6ba0be030a2281bce366e4378892df59b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835753"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA（大型实例）网络体系结构

Azure 网络服务的体系结构是在 HANA 大型实例上成功部署 SAP 应用程序的关键组件。 通常，Azure 上的 SAP HANA（大型实例）部署具有较大的 SAP 布局和多种不同的 SAP 解决方案，其中具有可变的数据库大小、CPU 资源消耗和内存利用。 这些 SAP 系统中，可能并不是所有系统都基于 SAP HANA。 SAP 布局可能是混合式的，它使用以下各项：

- 在本地部署的 SAP 系统。 由于其大小的原因，这些系统当前无法托管在 Azure 中。 一个典型的示例是在 SQL Server（用作数据库）上运行且需要的 CPU 或内存资源超出 VM 提供能力的 SAP ERP 生产系统。
- 在本地部署的基于 SAP HANA 的 SAP 系统。
- 在 VM 中部署的 SAP 系统。 这些系统可能是任何基于 SAP NetWeaver 的应用程序的开发、测试、沙盒或生产实例，这些应用程序可以根据资源消耗和内存需求成功地在 Azure 中的 VM 上部署。 这些系统也可能基于数据库，例如 SQL Server。 有关详细信息，请参阅[SAP 支持说明 #1928533 – Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型](https://launchpad.support.sap.com/#/notes/1928533/E)。 这些系统可能基于数据库，例如 SAP HANA。 有关详细信息，请参阅 [SAP HANA 认证的 IaaS 平台](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)。
- 在 Azure 中的 VM 上部署的 SAP 应用程序服务器，它们利用 Azure 大型实例模具中的 Azure 上的 SAP HANA（大型实例）。

使用至少四个不同部署方案的混合式 SAP 布局很常见。 也有很多客户方案在 Azure 中运行完整的 SAP 布局。 由于 VM 的功能越来越强大，将其所有 SAP 解决方案移到 Azure 上的客户数量也在增长。

Azure 中部署的 SAP 系统的上下文中的 Azure 网络并不复杂。 它基于以下原则：

- Azure 虚拟网络必须连接到与本地网络相连的 ExpressRoute 线路。
- 从本地连接到 Azure 的 ExpressRoute 线路的带宽通常应当为 1 Gbps 或更高。 规定这个最小带宽是为了确保在本地系统与在 VM 上运行的系统之间传输数据。 此外，还能提供足够的带宽从本地最终用户到 Azure 系统的连接。
- 必须在虚拟网络中设置 Azure 中的所有 SAP 系统以使其相互通信。
- 本地承载的 Active Directory 和 DNS 通过 ExpressRoute 从本地扩展到 Azure 中。


> [!NOTE] 
> 从计费角度来看，单个 Azure 订阅只能链接到特定 Azure 区域中的大型实例模具中的单个租户。 反过来，单个大型实例模具租户只能链接到单个 Azure 订阅。 此要求与 Azure 中的任何其他计费对象一致。

将 Azure 上的 SAP HANA（大型实例）部署在多个不同的 Azure 区域中导致在大型实例模具中部署单独的租户。 可在同一 Azure 订阅下运行这两个实例，只要这些实例属于同一 SAP 布局即可。 

> [!IMPORTANT] 
> Azure 上的 SAP HANA（大型实例）仅支持 Azure 资源管理器部署。

 

## <a name="additional-virtual-network-information"></a>其他虚拟网络信息

要将虚拟网络连接到 ExpressRoute，必须创建 Azure 网关。 有关详细信息，请参阅[关于 ExpressRoute 的虚拟网络网关](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

Azure 网关可与位于 Azure 外部的基础结构或者 Azure 大型实例模具的 ExpressRoute 一起使用。 Azure 网关还可用于在虚拟网络之间建立连接。 有关详细信息，请参阅[使用 PowerShell 为资源管理器配置网络到网络连接](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 可以将 Azure 网关连接到最多四个不同的 ExpressRoute 连接，只要这些连接来自不同的 Microsoft 企业边缘路由器即可。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

> [!NOTE] 
> 对于两种用例，Azure 网关提供的吞吐量不同。 有关详细信息，请参阅[关于 VPN 网关](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 使用 ExpressRoute 连接时，通过虚拟网络网关可以获得的最大吞吐量为 10 Gbps。 在位于虚拟网络中的 VM 与本地系统之间复制文件（使用单一复制流）不会获得各种网关 SKU 的完整吞吐量。 若要利用虚拟网络网关的完整带宽，请使用多个流。 或者，必须采用单一文件的并行流复制各个文件。


## <a name="networking-architecture-for-hana-large-instance"></a>HANA 大型实例的网络体系结构
HANA 大型实例的网络体系结构可以分为四个不同的部分：

- 本地网络和到 Azure 的 ExpressRoute 连接。 此部分是客户域，通过 ExpressRoute 连接到 Azure。 请参阅下图右下方。
- Azure 网络服务，在上面已与虚拟网络（也有网关）一起简单讨论过。 此部件是需要根据应用程序要求以及安全性和符合性要求找到合适设计的区域。 使用 HANA 大型实例是另一个考虑点，需要考虑到虚拟网络数以及可供选择的 Azure 网关 SKU。 请参阅下图右上方。
- 通过 ExpressRoute 技术将 HANA 大型实例连接到 Azure 中。 此部件已部署，由 Microsoft 处理。 你只需提供一些 IP 地址范围，在将资产部署到 HANA 大型实例中后，再将 ExpressRoute 线路连接到虚拟网络。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 
- HANA 大型实例中的网络，大部分在作为客户看来是透明的。

![连接到了 Azure 上的 SAP HANA（大型实例）和本地的虚拟网络](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

使用 HANA 大型实例这一事实并不会改变将本地资产通过 ExpressRoute 连接到 Azure 这一要求， 也不会改变使用一个或多个虚拟网络这一要求。虚拟网络运行的 Azure VM 托管应用层，该层连接到托管在 HANA 大型实例单位中的 HANA 实例。 

Azure 中 SAP 部署的差别如下：

- 客户租户的 HANA 大型实例单位通过另一 ExpressRoute 线路连接到虚拟网络中。 为了隔离负载条件，从本地到虚拟网络的 ExpressRoute 链接以及虚拟网络和 HANA 大型实例之间的链接不共享相同的路由器。
- 在 SAP 应用层和 HANA 大型实例之间的工作负荷配置文件在本质上不同于包含多个小型请求的迸发型数据传输（结果集），后者是从 SAP HANA 传输到应用层中。
- 与数据在本地和 Azure 之间交换的典型方案相比，SAP 应用程序体系结构对网络延迟更为敏感。
- 虚拟网络网关具有至少两个 ExpressRoute 连接。 两个连接共享虚拟网络网关传入数据的最大带宽。

在 VM 和 HANA 大型实例单位之间体验到的网络延迟可能高于典型的 VM 到 VM 网络往返延迟。 相关的 Azure 区域，测量值可能超过 0.7 毫秒往返滞后时间被归类为低于平均值中[SAP 说明 #1100926-常见问题解答：网络性能](https://launchpad.support.sap.com/#/notes/1100926/E)。 依赖于 Azure 区域和工具来测量 Azure VM 和 HANA 大型实例单元之间的网络往返延迟，所测量的延迟可以达到或大约 2 毫秒。 尽管如此，客户在 SAP HANA 大型实例上部署基于 SAP HANA 的生产型 SAP 应用程序很成功。 请确保在 Azure HANA 大型实例中对自己的业务流程进行彻底的测试。
 
为了确保 VM 和 HANA 大型实例之间的网络延迟稳定，必须选择虚拟网络网关 SKU。 不同于本地与 VM 之间的流量模式，VM 与 HANA 大型实例之间的流量模式可能是这样的：一开始流量很小，但随着要传输的请求和数据量的增多，可能会出现流量突然增高的迸发现象。 为了应对这种迸发现象，我们强烈建议使用 UltraPerformance 网关 SKU。 对于 II 类 HANA 大型实例 SKU，将 UltraPerformance 网关 SKU 用作虚拟网络网关是强制性的。

> [!IMPORTANT] 
> 假定所有的网络流量都位于 SAP 应用层与数据库层之间，则仅支持使用虚拟网络的 HighPerformance 或 UltraPerformance 网关 SKU 来连接到 Azure 上的 SAP HANA（大型实例）。 对于 HANA 大型实例 II 类 SKU，只支持使用 UltraPerformance 网关 SKU 作为虚拟网络网关。



## <a name="single-sap-system"></a>单个 SAP 系统

上面所示的本地基础结构通过 ExpressRoute 连接到 Azure。 ExpressRoute 线路连接到企业边缘路由器。 有关详细信息，请参阅 [ExpressRoute 技术概述](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 在建立之后，该路由将连接到 Azure 主干，并且所有 Azure 区域都可供访问。

> [!NOTE] 
> 若要在 Azure 中运行 SAP 布局，请连接到距离 SAP 布局中的 Azure 区域最近的企业边缘路由器。 Azure 大型实例模具通过专用企业边缘路由器设备进行连接，从而最大程度地降低 Azure IaaS 中的 VM 与大型实例模具之间的网络延迟。

承载着 SAP 应用程序实例的 VM 的虚拟网络网关连接到该 ExpressRoute 线路。 同一虚拟网络连接到一个专门用于连接大型实例模具的单独企业边缘路由器。

此系统是单个 SAP 系统的直观示例。 SAP 应用层承载在 Azure 中。 SAP HANA 数据库在 Azure 上的 SAP HANA（大型实例）上运行。 假设条件是 2 Gbps 或 10 Gbps 吞吐量的虚拟网络网关带宽不会成为瓶颈。

## <a name="multiple-sap-systems-or-large-sap-systems"></a>多个 SAP 系统或大型 SAP 系统

如果部署了连接到 Azure 上的 SAP HANA（大型实例）的多个 SAP 系统或大型 SAP 系统，则虚拟网络网关的吞吐量可能会成为瓶颈。 在这种情况下，请将应用层拆分成多个虚拟网络。 还可以针对以下案例创建用于连接到 HANA 大型实例的特殊虚拟网络：

- 为承载 NFS 共享的 Azure 中的 VM 直接从 HANA 大型实例中的 HANA 实例执行备份
- 将从 HANA 大型实例单位大的备份或其他文件复制到在 Azure 中管理的磁盘空间。

使用独立的虚拟网络来托管进行存储管理的 VM。 这样可以避免在虚拟网络网关（为运行 SAP 应用层的 VM 提供服务）上将大型文件或数据从 HANA 大型实例传输到 Azure 时受到影响。 

要获得缩放性更好的网络体系结构，请采取以下措施：

- 对于单个较大的 SAP 应用层，使用多个虚拟网络。
- 为所部署的每个 SAP 系统部署一个单独的虚拟网络，而不是将这些 SAP 系统集中放置在同一虚拟网络中的各个子网中。

  适用于 Azure 上的 SAP HANA（大型实例）的缩放性更好的网络体系结构：

![跨多个虚拟网络部署 SAP 应用层](./media/hana-overview-architecture/image4-networking-architecture.png)

该图显示跨多个虚拟网络部署的 SAP 应用层或组件。 此配置会在这些虚拟网络中的应用程序之间通信时产生不可避免的延迟开销。 默认情况下，位于不同虚拟网络中的 VM 之间的网络流量通过此配置中的企业边缘路由器进行路由。 优化并降低两个虚拟网络之间的通信延迟的方法是将同一区域内的虚拟网络对等互连。 即使这些虚拟网络在不同订阅中，也可以使用此方法。 通过虚拟网络对等互连，两个不同虚拟网络中的 VM 可以使用 Azure 网络主干来直接相互通信。 延迟状态如同这些 VM 在同一个虚拟网络中一样。 发送到通过 Azure 虚拟网络网关连接的 IP 地址范围的流量则通过单独的虚拟网络网关进行路由。 

有关虚拟网络对等互连的详细信息，请参阅[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。


## <a name="routing-in-azure"></a>Azure 中的路由

对于 Azure 上的 SAP HANA（大型实例），有三个重要的网络路由注意事项：

* Azure 上的 SAP HANA（大型实例）只能由 VM 通过专用 ExpressRoute 连接进行访问，不能从本地直接访问。 在 Microsoft 向你提供 HANA 大型实例单元后，不能立即从本地直接访问。 传递路由限制是用于 SAP HANA 大型实例的当前 Azure 网络体系结构造成的。 需要进行直接访问的某些管理客户端和任何应用程序（例如在本地运行的 SAP Solution Manager）都无法连接到 SAP HANA 数据库。

* 如果在两个不同的 Azure 区域部署了 HANA 大型实例单元用于进行灾难恢复，将适用相同的传递路由限制。 换言之，一个区域（如美国西部）中的 HANA 大型实例的 IP 地址不会路由到另一个区域（如美国东部）部署的 HANA 大型实例。 此限制与跨区域或跨 ExpressRoute 线路连接（将 HANA 大型实例单元连接到虚拟网络）使用 Azure 网络对等互连无关。 有关图形表示形式，请参阅“在多个区域使用 HANA 大型实例单位”部分中的插图。 部署的体系结构存在此限制，禁止立即将 HANA 系统复制用作灾难恢复功能。

* Azure 上的 SAP HANA（大型实例）单位有一个分配的 IP 地址，该地址源自客户提交的 Server IP 池地址范围。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 可通过 Azure 订阅以及用于将虚拟网络连接到 Azure 上的 HANA（大型实例）的 ExpressRoute 访问此 IP 地址。 从该服务器 IP 池地址范围中分配的 IP 地址将直接分配给硬件单元， 而不会经过 NAT 转换，在此解决方案的第一个部署中也存在这种情况。 

> [!NOTE]
> 若要克服前两个列表项中所述的暂时性路由限制，需要使用其他组件进行路由。 可用于克服该限制的组件可以是：
> 
> * 来回路由数据的反向代理。 例如，使用 Azure 中部署的带流量管理器的 F5 BIG-IP、NGINX 作为虚拟防火墙/流量路由解决方案。
> * 在 Linux VM 中使用 [IPTables 规则](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ)在本地位置与 HANA 大型实例单元之间，或者在不同区域中的 HANA 大型实例单元之间实现路由。
> 
> 请注意，Microsoft 不实现也不支持涉及第三方网络设备或 IPTables 的自定义解决方案。 必须由所用组件的供应商或集成者提供支持。 

## <a name="internet-connectivity-of-hana-large-instance"></a>HANA 大型实例的 Internet 连接
HANA 大型实例未建立直接 Internet 连接。 这会限制某些功能，例如，直接向 OS 供应商注册 OS 映像的功能。 可能需要使用本地 SUSE Linux Enterprise Server 订阅管理工具服务器或 Red Hat Enterprise Linux 订阅管理器。

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM 与 HANA 大型实例之间的数据加密
在 HANA 大型实例与 VM 之间传输的数据不会加密。 但是，仅仅是用于 HANA DBMS 端和基于 JDBC/ODBC 应用程序之间的交换，可以启用加密的流量。 有关详细信息，请参阅[此 SAP 文档](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)。

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>在多个区域使用 HANA 大型实例单位

除了灾难恢复以外，可能还有其他原因需要你在多个 Azure 区域中部署 Azure 上的 SAP HANA（大型实例）。 可能需要从各个区域的不同虚拟网络中部署的每个 VM 访问 HANA 大型实例。 分配给不同 HANA 大型实例单元的 IP 地址不会跨越（通过其网关直接连接到实例的）虚拟网络进行传播。 因此，对上面介绍的虚拟网络设计进行了轻微的更改。 虚拟网络网关可以处理来自不同企业边缘路由器的四个不同的 ExpressRoute 线路。 连接到其中一个大型实例模具的每个虚拟网络都可以连接到另一 Azure 区域中的大型实例模具。

![连接到了不同 Azure 区域中的 Azure 大型实例模具的虚拟网络](./media/hana-overview-architecture/image8-multiple-regions.png)

上图显示了两个区域中的不同虚拟网络如何连接到两个不同的 ExpressRoute 线路，这两个线路用来连接到两个 Azure 区域中的 Azure 上的 SAP HANA（大型实例）。 新引入的连接采用矩形红色线条标出。 有了来自虚拟网络的这些连接，在那些虚拟网络之一中运行的 VM 可以访问在两个区域中部署的每个不同的 HANA 大型实例单位。 如图所示，假设已建立从本地到两个 Azure 区域的两个 ExpressRoute 连接。 出于灾难恢复方面的原因，建议使用此配置。

> [!IMPORTANT] 
> 如果使用了多个 ExpressRoute 线路，则应使用“AS 路径前追加”和“本地首选 BGP”设置来确保正确路由流量。

**后续步骤**
- 请参阅 [SAP HANA（大型实例）存储体系结构](hana-storage-architecture.md)