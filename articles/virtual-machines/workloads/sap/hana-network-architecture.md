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
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8794a93cecb50774f30746c22931db31a9fa9194
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66239585"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA（大型实例）网络体系结构

Azure 网络服务的体系结构是在 HANA 大型实例上成功部署 SAP 应用程序的关键组件。 通常，Azure 上的 SAP HANA（大型实例）部署具有较大的 SAP 布局和多种不同的 SAP 解决方案，其中具有可变的数据库大小、CPU 资源消耗和内存利用。 很可能不是所有 IT 系统已经都位于 Azure 中。 SAP 布局通常是混合也从 DBMS 点和 SAP 应用程序的角度来看的 NetWeaver 和 S/4HANA 和 SAP HANA，以及其他 DBMS 混合使用。 Azure 提供了不同的服务，可用于在 Azure 中运行不同的 DBMS、 NetWeaver 和 S/4HANA 系统。 Azure 还网络技术，以查看 azure 产品/服务都喜欢你的本地软件的部署的虚拟数据中心

除非在 Azure 中托管你完成的 IT 系统。 Azure 网络功能用于与你的 Azure 资产，以使类似于你的虚拟数据中心的 Azure 连接的本地环境。 使用 Azure 网络功能是： 

- Azure 虚拟网络连接到[ExpressRoute](https://azure.microsoft.com/services/expressroute/)线路连接到你的本地网络资产。
- 在本地连接到 Azure 的 ExpressRoute 线路应具有的最小带宽[1 Gbps 或更高版本](https://azure.microsoft.com/pricing/details/expressroute/)。 规定这个最小带宽是为了确保在本地系统与在 VM 上运行的系统之间传输数据。 此外，还能提供足够的带宽从本地最终用户到 Azure 系统的连接。
- 在 Azure 中的所有 SAP 系统都设置的虚拟网络中进行相互通信。
- 承载在本地 active Directory 和 DNS 扩展到 Azure 通过 ExpressRoute 从本地，或在运行在 Azure 中完成。

为集成到 Azure 数据中心网络构造的 HANA 大型实例中的特定情况下，还使用 Azure ExpressRoute 技术


> [!NOTE] 
> 只有一个 Azure 订阅可以链接到特定 Azure 区域中的 HANA 大型实例模具中只有一个租户。 反过来，单个的 HANA 大型实例模具租户可以链接到只有一个 Azure 订阅。 此要求与 Azure 中的任何其他计费对象一致。

如果 Azure （大型实例） 上的 SAP HANA 部署在多个不同 Azure 区域中，在 HANA 大型实例模具中部署单独的租户。 可在同一 Azure 订阅下运行这两个实例，只要这些实例属于同一 SAP 布局即可。 

> [!IMPORTANT] 
> Azure （大型实例） 上的 SAP HANA 支持 Azure 资源管理器的部署方法。

 

## <a name="additional-virtual-network-information"></a>其他虚拟网络信息

若要连接到 ExpressRoute 的虚拟网络，必须创建 Azure ExpressRoute 网关。 有关详细信息，请参阅[有关 Expressroute 网关为 ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

Azure ExpressRoute 网关用于 ExpressRoute 在 Azure 外部的基础结构或 Azure 大型实例模具。 可以连接到最多四个不同的 ExpressRoute 线路的 Azure ExpressRoute 网关，只要这些连接来自不同的 Microsoft 企业边缘路由器。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

> [!NOTE] 
> 通过使用 ExpressRoute 连接，可以实现与 ExpressRoute 网关的最大吞吐量为 10 Gbps。 在位于虚拟网络中的 VM 与本地系统之间复制文件（使用单一复制流）不会获得各种网关 SKU 的完整吞吐量。 若要利用 ExpressRoute 网关的完整带宽，使用多个流。 或者，必须采用单一文件的并行流复制各个文件。


## <a name="networking-architecture-for-hana-large-instance"></a>HANA 大型实例的网络体系结构
HANA 大型实例的网络体系结构可以分为四个不同的部分：

- 本地网络和到 Azure 的 ExpressRoute 连接。 此部分是客户域，通过 ExpressRoute 连接到 Azure。 此 Expressroute 线路完全由你支付作为客户。 带宽应足够大，无法处理你的本地资产与针对要连接的 Azure 区域之间的网络流量。 请参阅下图右下方。
- Azure 网络服务，如前面所述，使用虚拟网络，再次需要 ExpressRoute 网关添加。 此部件是需要根据应用程序要求以及安全性和符合性要求找到合适设计的区域。 使用 HANA 大型实例是另一个考虑点，需要考虑到虚拟网络数以及可供选择的 Azure 网关 SKU。 请参阅下图右上方。
- 通过 ExpressRoute 技术将 HANA 大型实例连接到 Azure 中。 此部件已部署，由 Microsoft 处理。 你只需提供一些 IP 地址范围，在将资产部署到 HANA 大型实例中后，再将 ExpressRoute 线路连接到虚拟网络。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 没有为你作为 Azure 数据中心网络构造与 HANA 大型实例单元之间的连接的客户另行收费。
- 在 HANA 大型实例模具中的网络，看来是透明的。

![连接到了 Azure 上的 SAP HANA（大型实例）和本地的虚拟网络](./media/hana-overview-architecture/image1-architecture.png)

使用 HANA 大型实例这一事实并不会改变将本地资产通过 ExpressRoute 连接到 Azure 这一要求， 也不会改变使用一个或多个虚拟网络这一要求。虚拟网络运行的 Azure VM 托管应用层，该层连接到托管在 HANA 大型实例单位中的 HANA 实例。 

Azure 中 SAP 部署的差别如下：

- 客户租户的 HANA 大型实例单位通过另一 ExpressRoute 线路连接到虚拟网络中。 为了隔离负载条件，实现本地到 Azure 虚拟网络的 ExpressRoute 线路和 Azure 虚拟网络和 HANA 大型实例之间电路不共享相同的路由器。
- 在 SAP 应用层和 HANA 大型实例之间的工作负荷配置文件在本质上不同于包含多个小型请求的迸发型数据传输（结果集），后者是从 SAP HANA 传输到应用层中。
- 与数据在本地和 Azure 之间交换的典型方案相比，SAP 应用程序体系结构对网络延迟更为敏感。
- Azure ExpressRoute 网关具有至少两个 ExpressRoute 连接。 来自在本地，一个从 HANA 大型实例连接的连接的一条线路。 这将使从不同 Msee 上要连接到 ExpressRoute 网关仅适用于其他两个其他线路的空间。 此限制是独立的使用情况的 ExpressRoute 快速路径。 已连接的所有线路都共享的 ExpressRoute 网关传入数据的最大带宽。

在 VM 和 HANA 大型实例单位之间体验到的网络延迟可能高于典型的 VM 到 VM 网络往返延迟。 相关的 Azure 区域，测量值可能超过 0.7 毫秒往返滞后时间被归类为低于平均值中[SAP 说明 #1100926-常见问题解答：网络性能](https://launchpad.support.sap.com/#/notes/1100926/E)。 依赖于 Azure 区域和工具来测量 Azure VM 和 HANA 大型实例单元之间的网络往返延迟，所测量的延迟可以达到或大约 2 毫秒。 尽管如此，客户在 SAP HANA 大型实例上部署基于 SAP HANA 的生产型 SAP 应用程序很成功。 请确保在 Azure HANA 大型实例中对自己的业务流程进行彻底的测试。 一项新功能，称为 ExpressRoute 快速路径，是能够显著减少 HANA 大型实例和应用程序层在 Azure 中 Vm 之间的网络延迟 （见下文）。 

若要确保 Vm 和 HANA 大型实例，所选的 ExpressRoute 网关之间的确定性的网络延迟 SKU 非常重要。 不同于本地与 VM 之间的流量模式，VM 与 HANA 大型实例之间的流量模式可能是这样的：一开始流量很小，但随着要传输的请求和数据量的增多，可能会出现流量突然增高的迸发现象。 为了应对这种迸发现象，我们强烈建议使用 UltraPerformance 网关 SKU。 对于 II 类 HANA 大型实例 sku，将 UltraPerformance 网关 SKU 用作 ExpressRotue 网关是必需的。

> [!IMPORTANT] 
> 假定所有的网络流量都位于 SAP 应用层与数据库层之间，则仅支持使用虚拟网络的 HighPerformance 或 UltraPerformance 网关 SKU 来连接到 Azure 上的 SAP HANA（大型实例）。 对于 HANA 大型实例类型 II Sku，仅 UltraPerformance 网关 SKU 支持作为 ExpressRoute 网关。 使用 ExpressRoute 快速路径 （见下文） 时应用的异常

### <a name="expressroute-fast-path"></a>ExpressRoute 快速路径
为了降低延迟、 ExpressRoute 快速路径已引入和在 2019 年 5 针对发布的 HANA 大型实例与 Azure 虚拟网络的特定连接托管 SAP 应用程序 Vm。 到目前为止，推出解决方案的主要区别是，该 Vm 与 HANA 大型实例之间的数据流不会路由通过 ExpressRoute 网关不再。 而专用的企业边缘路由器与直接进行通信的 Azure 虚拟网络的子网中分配的 Vm。 

> [!IMPORTANT] 
> ExpressRoute 快速路径功能要求运行 SAP 应用程序 Vm 的子网中获取连接到 HANA 大型实例的同一 Azure 虚拟网络。 Azure 虚拟网络中的对等互连与直接连接到 HANA 大型实例单元的 Azure 虚拟网络的 Vm 不可以从 ExpressRoute 快速路径中受益。 因此典型中心和辐射虚拟网络设计，其中 ExpressRoute 线路连接与中心虚拟网络和虚拟网络包含 SAP 应用程序层 （分散） 获取对等互连，通过 ExpressRoute 快速优化路径将无法工作。 此外，ExpressRoute 快速路径不目前不支持用户定义路由规则 (UDR)。 有关详细信息，请参阅[ExpressRoute 虚拟网络网关和快速](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)。 


有关如何配置 ExpressRoute 快速路径，更多详细信息，请阅读文档[虚拟网络连接到 HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)。    

> [!NOTE]
> UltraPerformance ExpressRoute 网关时需要已创建有效的 ExpressRoute 快速路径


## <a name="single-sap-system"></a>单个 SAP 系统

上面所示的本地基础结构通过 ExpressRoute 连接到 Azure。 ExpressRoute 线路连接到 Microsoft enterprise 边缘路由器 (MSEE)。 有关详细信息，请参阅 [ExpressRoute 技术概述](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 建立路由后，它连接到 Azure 的主干。

> [!NOTE] 
> 若要在 Azure 中运行 SAP 布局，请连接到距离 SAP 布局中的 Azure 区域最近的企业边缘路由器。 HANA 大型实例模具通过专用的企业边缘路由器设备，以尽量降低网络延迟，在 Azure IaaS Vm 和 HANA 大型实例戳之间进行连接。

托管 SAP 应用程序实例的 Vm 的 ExpressRoute 网关连接到一条 ExpressRoute 线路连接到本地。 同一虚拟网络连接到一个专门用于连接大型实例模具的单独企业边缘路由器。 使用 ExpressRoute 快速路径，数据流从 HANA 大型实例的 SAP 应用层到 Vm 通过 ExpressRoute 网关不不再路由和使用，减少网络往返行程延迟。

此系统是单个 SAP 系统的直观示例。 SAP 应用层承载在 Azure 中。 SAP HANA 数据库在 Azure 上的 SAP HANA（大型实例）上运行。 假设条件是 2 Gbps 或 10 Gbps 吞吐量的 ExpressRoute 网关带宽不代表一个瓶颈。

## <a name="multiple-sap-systems-or-large-sap-systems"></a>多个 SAP 系统或大型 SAP 系统

如果多个 SAP 系统或大型 SAP 系统部署连接到 Azure （大型实例） 上的 SAP HANA，ExpressRoute 网关的吞吐量可能成为瓶颈。 或者，你想要隔离生产和非生产系统中不同 Azure 虚拟网络。 在这种情况下，请将应用层拆分成多个虚拟网络。 还可以针对以下案例创建用于连接到 HANA 大型实例的特殊虚拟网络：

- 为承载 NFS 共享的 Azure 中的 VM 直接从 HANA 大型实例中的 HANA 实例执行备份
- 将从 HANA 大型实例单位大的备份或其他文件复制到在 Azure 中管理的磁盘空间。

使用单独的虚拟网络到主机管理的 HANA 大型实例与 Azure 之间大容量传输数据存储的 Vm。 这种排列方式避免了大型文件或数据传输从 HANA 大型实例到 Azure 上运行 SAP 应用程序层的 Vm 提供服务的 ExpressRoute 网关的影响。 

要获得缩放性更好的网络体系结构，请采取以下措施：

- 对于单个较大的 SAP 应用层，使用多个虚拟网络。
- 为所部署的每个 SAP 系统部署一个单独的虚拟网络，而不是将这些 SAP 系统集中放置在同一虚拟网络中的各个子网中。

  适用于 Azure 上的 SAP HANA（大型实例）的缩放性更好的网络体系结构：

![跨多个虚拟网络部署 SAP 应用层](./media/hana-overview-architecture/image4-networking-architecture.png)

相关的规则和限制，你想要应用托管的不同 SAP 系统的 Vm 在不同虚拟网络之间，应建立这些虚拟网络对等互连。 有关虚拟网络对等互连的详细信息，请参阅[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。


## <a name="routing-in-azure"></a>Azure 中的路由

默认部署中，由三个网络路由注意事项非常重要的 Azure （大型实例） 上的 SAP HANA:

* Azure （大型实例） 上的 SAP HANA 可以仅通过 Azure Vm 和专用的 ExpressRoute 连接，不能直接从本地访问。 在 Microsoft 向你提供 HANA 大型实例单元后，不能立即从本地直接访问。 传递路由限制是用于 SAP HANA 大型实例的当前 Azure 网络体系结构造成的。 需要进行直接访问的某些管理客户端和任何应用程序（例如在本地运行的 SAP Solution Manager）都无法连接到 SAP HANA 数据库。 查看异常直接路由到 HANA 大型实例部分。

* 如果你有两个不同 Azure 区域中的灾难恢复相同的传递路由限制过去应用的部署的 HANA 大型实例单元。 换而言之，一个区域 （例如，美国西部） 中的 HANA 大型实例单元的 IP 地址不是路由到另一个区域 （例如，美国东部） 部署的 HANA 大型实例单位。 此限制是独立于使用 Azure 网络对等互连跨区域或跨 ExpressRoute 线路将 HANA 大型实例单元连接到虚拟网络。 有关图形表示形式，请参阅“在多个区域使用 HANA 大型实例单位”部分中的插图。 此限制，随一起部署的体系结构提供，禁止立即将 HANA 系统复制用作灾难恢复功能。 有关最新更改，查找在多个区域使用 HANA 大型实例单位的部分。 

* Azure （大型实例） 单元上的 SAP HANA 具有从提交请求的 HANA 大型实例部署时的服务器 IP 池地址范围分配有的 IP 地址。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 通过 Azure 订阅和 Azure 虚拟网络连接到 HANA 大型实例的线路访问此 IP 地址。 从该服务器 IP 池地址范围中分配的 IP 地址将直接分配给硬件单元， 而不会经过 NAT 转换，在此解决方案的第一个部署中也存在这种情况  。 

### <a name="direct-routing-to-hana-large-instances"></a>直接路由到 HANA 大型实例
默认情况下可传递的路由，HANA 大型实例单元之间的本地或两个不同区域中的 HANA 大型实例路由部署之间不工作。 有几种可能性，若要启用此类可传递路由。

- 来回路由数据的反向代理。 例如，F5 BIG-IP、 NGINX 使用流量管理器部署在 Azure 虚拟网络的连接到 HANA 大型实例和本地作为虚拟防火墙/流量路由解决方案。
- 在 Linux VM 中使用 [IPTables 规则](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ)在本地位置与 HANA 大型实例单元之间，或者在不同区域中的 HANA 大型实例单元之间实现路由。 运行 IPTables 的 VM 需要在连接到 HANA 大型实例的 Azure 虚拟网络中部署，并且要在本地。 VM 需要要相应地调整，因此，VM 的网络吞吐量足以满足预期的网络流量。 有关详细信息，在 VM 上的网络带宽，查看文章[大小的 Linux 虚拟机在 Azure 中](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- [Azure 防火墙](https://azure.microsoft.com/services/azure-firewall/)是另一种解决方案，以允许在本地和 HANA 大型实例单元之间直接通信。 

这些解决方案的所有流量将通过 Azure 虚拟网络都路由，这种情况下，流量可以是附加要限制通过软设备使用或通过 Azure 网络安全组，因此，从特定 IP 地址或 IP 地址范围无法阻止或显式允许访问 HANA 大型实例在本地。 

> [!NOTE]  
> 请注意，Microsoft 不实现也不支持涉及第三方网络设备或 IPTables 的自定义解决方案。 必须由所用组件的供应商或集成者提供支持。 

#### <a name="express-route-global-reach"></a>Express 路由的全球性覆盖
Microsoft 引入了新功能，称为[ExpressRoute 全球覆盖](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)。 在两个方案中，可以为 HANA 大型实例使用的全球性覆盖：

- 启用对你在不同区域中部署的 HANA 大型实例单位从本地直接访问
- 允许你在不同区域中部署的 HANA 大型实例单位之间进行直接通信


##### <a name="direct-access-from-on-premise"></a>从本地直接访问
在其中提供全局访问的 Azure 区域，可以请求启用 ExpressRoute 线路，将你的本地网络连接到 Azure 虚拟网络连接到你的 HANA 大型实例单元的全局访问功能。 有一些成本等因素在本地端 ExpressRoute 线路。 有关价格，请为价格[全局访问的外接程序](https://azure.microsoft.com/pricing/details/expressroute/)。 没有为您提供有关到线路将 HANA 大型实例单元连接到 Azure，无额外成本。 

> [!IMPORTANT]  
> 网络数据和控制流是在使用启用的 HANA 大型实例单元与本地资产之间的直接访问的全球覆盖，**不通过 Azure 虚拟网络路由**，但 Microsoft 之间直接企业 exchange 路由器。 因此任何 NSG 或 ASG 的规则或任何类型的防火墙、 NVA 或在 Azure 虚拟网络中部署的代理不获取涉及。 **如果你使用 ExpressRoute 全球覆盖以启用对 HANA 大型实例单位限制从本地直接访问和访问 HANA 大型实例单元的权限需要在本地端上的防火墙中定义** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>连接不同 Azure 区域中 HANA 大型实例
在相同的方式，因为 ExpressRoute 全球覆盖可用于连接到 HANA 大型实例单元，在本地使用它可以用于连接 HANA 大型实例租户的跨越两个不同区域中部署。 隔离是 HANA 大型实例租户用来连接到两个区域中的 Azure ExpressRoute 线路。 有用于连接两个在两个不同区域中部署的 HANA 大型实例租户的任何额外费用。 

> [!IMPORTANT]  
> 数据流和控制流之间的不同 HANA 大型实例租户将不通过 azure 网络进行路由的网络流量。 因此不能使用 Azure 功能或 Nva 来强制实施两个 HANA 大型实例租户之间的通信限制。 

有关如何获取 ExpressRoute 启用进行全局访问的详细信息，请参阅文档[虚拟网络连接到 HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)。


## <a name="internet-connectivity-of-hana-large-instance"></a>HANA 大型实例的 Internet 连接
HANA 大型实例未建立直接 Internet 连接  。 这会限制某些功能，例如，直接向 OS 供应商注册 OS 映像的功能。 可能需要使用本地 SUSE Linux Enterprise Server 订阅管理工具服务器或 Red Hat Enterprise Linux 订阅管理器。

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM 与 HANA 大型实例之间的数据加密
在 HANA 大型实例与 VM 之间传输的数据不会加密。 但是，仅仅是用于 HANA DBMS 端和基于 JDBC/ODBC 应用程序之间的交换，可以启用加密的流量。 有关详细信息，请参阅[此 SAP 文档](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)。

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>在多个区域使用 HANA 大型实例单位

若要实现灾难恢复设置 ups，需要在多个 Azure 区域中具有一元大型实例单元。 甚至与使用 Azure [全局 Vnet 对等互连]，可传递路由默认情况下无法使用两个不同区域中的 HANA 大型实例租户之间。 但是，全局访问打开两个不同区域中预配的 HANA 大型实例单元之间的通信路径。 这种使用方案的 ExpressRoute 全球覆盖启用：

 - HANA 系统复制，而无需任何其他代理或防火墙
 - 在两个不同区域中 HANA 大型实例单元，以执行系统副本或系统刷新之间的复制备份


![连接到了不同 Azure 区域中的 Azure 大型实例模具的虚拟网络](./media/hana-overview-architecture/image8-multiple-regions.png)

图显示了如何将两个区域中的不同虚拟网络连接到用于连接到 Azure （大型实例） 上的 SAP HANA 的两个不同的 ExpressRoute 线路在这两个 Azure 区域 （灰色行）。 这两个交叉连接的原因是为了防止服务中断的任意一侧的 Msee。 两个 Azure 区域中的两个虚拟网络之间的通信流应通过处理[全局对等互连](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/)的两个不同区域 （蓝色点线） 中的两个虚拟网络。 粗红线介绍 ExpressRoute 全局访问的连接，这允许您在两个不同区域中进行相互通信的租户的 HANA 大型实例单位。 

> [!IMPORTANT] 
> 如果使用了多个 ExpressRoute 线路，则应使用“AS 路径前追加”和“本地首选 BGP”设置来确保正确路由流量。

**后续步骤**
- 请参阅 [SAP HANA（大型实例）存储体系结构](hana-storage-architecture.md)