---
title: Azure 上的 SAP HANA（大型实例）的网络体系结构 | Microsoft Docs
description: 有关如何部署 Azure 上的 SAP HANA（大型实例）的网络体系结构。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b3bc87b183803c0854542d6925af7429b593d2af
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605178"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA（大型实例）网络体系结构

Azure 网络服务的体系结构是在 HANA 大型实例上成功部署 SAP 应用程序的关键组件。 通常，Azure 上的 SAP HANA（大型实例）部署具有较大的 SAP 布局和多种不同的 SAP 解决方案，其中具有可变的数据库大小、CPU 资源消耗和内存利用。 很可能并非所有 IT 系统都位于 Azure 中。 使用 NetWeaver、S/4HANA 和 SAP HANA 和其他 DBMS 的混合，您的 SAP 环境通常也是从 DBMS 点和 SAP 应用程序角度混合的。 Azure 提供了不同的服务，允许您在 Azure 中运行不同的 DBMS、NetWeaver 和 S/4HANA 系统。 Azure 还提供网络技术，使 Azure 看起来像本地软件部署的虚拟数据中心

除非完整的 IT 系统托管在 Azure 中。 Azure 网络功能用于将本地世界与 Azure 资产连接起来，以使 Azure 看起来像您的虚拟数据中心。 使用的 Azure 网络功能是： 

- Azure 虚拟网络连接到连接到本地网络资产的[ExpressRoute](https://azure.microsoft.com/services/expressroute/)电路。
- 在本地连接到 Azure 的 ExpressRoute 电路应具有 1 [Gbps 或更高的](https://azure.microsoft.com/pricing/details/expressroute/)最小带宽。 规定这个最小带宽是为了确保在本地系统与在 VM 上运行的系统之间传输数据。 此外，还能提供足够的带宽从本地最终用户到 Azure 系统的连接。
- Azure 中的所有 SAP 系统都设置在虚拟网络中，以便相互通信。
- 本地托管的活动目录和 DNS 通过本地的 ExpressRoute 扩展到 Azure，或在 Azure 中运行完成。

对于将 HANA 大型实例集成到 Azure 数据中心网络结构中的特定情况，也使用 Azure ExpressRoute 技术


> [!NOTE] 
> 只有一个 Azure 订阅可以链接到特定 Azure 区域中 HANA 大型实例戳中的一个租户。 相反，单个 HANA 大型实例戳记租户只能链接到一个 Azure 订阅。 此要求与 Azure 中的任何其他计费对象一致。

如果 Azure 上的 SAP HANA（大型实例）部署在多个不同的 Azure 区域中，则在 HANA 大型实例戳中部署单独的租户。 可在同一 Azure 订阅下运行这两个实例，只要这些实例属于同一 SAP 布局即可。 

> [!IMPORTANT] 
> Azure 上的 SAP HANA（大型实例）仅支持 Azure 资源管理器部署方法。

 

## <a name="additional-virtual-network-information"></a>其他虚拟网络信息

要将虚拟网络连接到 ExpressRoute，必须创建 Azure ExpressRoute 网关。 有关详细信息，请参阅[有关快速路由的快线网关](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

Azure ExpressRoute 网关与 Azure 外部基础结构的 ExpressRoute 网关或 Azure 大型实例戳号一起使用。 只要这些连接来自不同的 Microsoft 企业边缘路由器，就可以将 Azure ExpressRoute 网关连接到最多四个不同的 ExpressRoute 电路。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

> [!NOTE] 
> 使用 ExpressRoute 网关可以达到的最大吞吐量是 10 Gbps，通过使用 ExpressRoute 连接。 在位于虚拟网络中的 VM 与本地系统之间复制文件（使用单一复制流）不会获得各种网关 SKU 的完整吞吐量。 要利用 ExpressRoute 网关的完整带宽，请使用多个流。 或者，必须采用单一文件的并行流复制各个文件。


## <a name="networking-architecture-for-hana-large-instance"></a>HANA 大型实例的网络体系结构
HANA 大型实例的网络体系结构可以分为四个不同的部分：

- 本地网络和到 Azure 的 ExpressRoute 连接。 此部分是客户域，通过 ExpressRoute 连接到 Azure。 此快速线路由您作为客户全额支付。 带宽应足够大，以处理本地资产和要连接的 Azure 区域之间的网络流量。 请参阅下图右下方。
- Azure 网络服务（如前面所述）与虚拟网络一起，再次需要添加 ExpressRoute 网关。 此部件是需要根据应用程序要求以及安全性和符合性要求找到合适设计的区域。 使用 HANA 大型实例是另一个考虑点，需要考虑到虚拟网络数以及可供选择的 Azure 网关 SKU。 请参阅下图右上方。
- 通过 ExpressRoute 技术将 HANA 大型实例连接到 Azure 中。 此部件已部署，由 Microsoft 处理。 你只需提供一些 IP 地址范围，在将资产部署到 HANA 大型实例中后，再将 ExpressRoute 线路连接到虚拟网络。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 作为客户，Azure 数据中心网络结构与 HANA 大型实例单元之间的连接不收取额外费用。
- HANA 大型实例戳中的网络，这主要对于您而言是透明的。

![连接到了 Azure 上的 SAP HANA（大型实例）和本地的虚拟网络](./media/hana-overview-architecture/image1-architecture.png)

使用 HANA 大型实例这一事实并不会改变将本地资产通过 ExpressRoute 连接到 Azure 这一要求， 也不会改变使用一个或多个虚拟网络这一要求。虚拟网络运行的 Azure VM 托管应用层，该层连接到托管在 HANA 大型实例单位中的 HANA 实例。 

Azure 中 SAP 部署的差别如下：

- 客户租户的 HANA 大型实例单位通过另一 ExpressRoute 线路连接到虚拟网络中。 为了分离负载条件，本地到 Azure 虚拟网络 ExpressRoute 电路以及 Azure 虚拟网络和 HANA 大型实例之间的电路不共享相同的路由器。
- 在 SAP 应用层和 HANA 大型实例之间的工作负荷配置文件在本质上不同于包含多个小型请求的迸发型数据传输（结果集），后者是从 SAP HANA 传输到应用层中。
- 与数据在本地和 Azure 之间交换的典型方案相比，SAP 应用程序体系结构对网络延迟更为敏感。
- Azure 快速路由网关至少有两个快速路由连接。 从本地连接的电路和从 HANA 大型实例连接的电路。 这仅为来自不同 MsEE 的其他两个电路留出空间，以便连接到 ExpressRoute 网关。 此限制与快速路由快速路径的使用无关。 所有连接的电路共享 ExpressRoute 网关传入数据的最大带宽。

使用 HANA 大型实例戳的修订版 3，VM 和 HANA 大型实例单元之间的网络延迟可能高于典型的 VM 到 VM 网络往返延迟。 测量到的值可能超过 0.7 毫秒的往返延迟，具体取决于 Azure 区域。而在 [SAP 说明 #1100926 - 常见问题解答：网络性能](https://launchpad.support.sap.com/#/notes/1100926/E)中，0.7 毫秒被归类为低于平均值。 依赖于 Azure 区域和工具来测量 Azure VM 和 HANA 大型实例单元之间的网络往返延迟，所测量的延迟可以达到或大约 2 毫秒。 尽管如此，客户在 SAP HANA 大型实例上部署基于 SAP HANA 的生产型 SAP 应用程序很成功。 请确保在 Azure HANA 大型实例中对自己的业务流程进行彻底的测试。 一个名为 ExpressRoute 快速路径的新功能能够显著减少 Azure 中的 HANA 大型实例和应用程序层 VM 之间的网络延迟（见下文）。 

使用 HANA 大型实例戳的修订版 4，部署在 HANA 大型实例戳附近的 Azure VM 之间的网络延迟会满足[SAP Note #1100926 - 常见问题解答中](https://launchpad.support.sap.com/#/notes/1100926/E)记录的平均或优于平均分类： 如果配置 Azure ExpressRoute 快速路径，网络性能（见下文）。 为了在接近第 4 修订版的 HANA 大型实例单元的地方部署 Azure VM，需要利用[Azure 邻近放置组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)。 Azure 邻近放置组中介绍了如何使用邻近放置组查找与修订版 4 托管 HANA 大型实例单元位于同一 Azure 数据中心中的 SAP 应用程序层的方式，[以便使用 SAP 应用程序实现最佳网络延迟](sap-proximity-placement-scenarios.md)。

为了在 VM 和 HANA 大型实例之间提供确定性网络延迟，选择 ExpressRoute 网关 SKU 至关重要。 不同于本地与 VM 之间的流量模式，VM 与 HANA 大型实例之间的流量模式可能是这样的：一开始流量很小，但随着要传输的请求和数据量的增多，可能会出现流量突然增高的迸发现象。 为了应对这种迸发现象，我们强烈建议使用 UltraPerformance 网关 SKU。 对于 HANA 大型实例 SKU 的 II 类，必须使用超高性能网关 SKU 作为 ExpressRoute 网关。

> [!IMPORTANT] 
> 假定所有的网络流量都位于 SAP 应用层与数据库层之间，则仅支持使用虚拟网络的 HighPerformance 或 UltraPerformance 网关 SKU 来连接到 Azure 上的 SAP HANA（大型实例）。 对于 HANA 大型实例类型 II SKU，仅支持超高性能网关 SKU 作为 ExpressRoute 网关。 使用快速路由快速路径时适用例外情况（见下文）

### <a name="expressroute-fast-path"></a>快速路由快速路径
为了降低延迟，ExpressRoute 快速路径于 2019 年 5 月推出并发布了，用于将 HANA 大型实例与托管 SAP 应用程序 VM 的 Azure 虚拟网络的特定连接。 到目前为止，解决方案的主要区别是，VM 和 HANA 大型实例之间的数据流不再通过 ExpressRoute 网关路由。 相反，在 Azure 虚拟网络的子网中分配的 VM 直接与专用企业边缘路由器通信。 

> [!IMPORTANT] 
> ExpressRoute 快速路径功能要求运行 SAP 应用程序 VM 的子网位于连接到 HANA 大型实例的同一 Azure 虚拟网络中。 位于 Azure 虚拟网络中的 VM 与直接连接到 HANA 大型实例单元的 Azure 虚拟网络对等互连，因此不会从 ExpressRoute 快速路径中受益。 因此，典型的集线器和分支虚拟网络设计，其中 ExpressRoute 电路与集线器虚拟网络连接，包含 SAP 应用程序层（分支）的虚拟网络正在对等，ExpressRoute 快速路径的优化将不起作用。 在加法中，快速路由快速路径今天不支持用户定义的路由规则 （UDR）。 有关详细信息，请参阅[ExpressRoute 虚拟网络网关和 FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)。 


有关如何配置 ExpressRoute 快速路径的更多详细信息，请阅读文档[将虚拟网络连接到 HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)。    

> [!NOTE]
> 需要超高性能快速路由网关才能使快速路由快速路径正常工作


## <a name="single-sap-system"></a>单个 SAP 系统

上面所示的本地基础结构通过 ExpressRoute 连接到 Azure。 ExpressRoute 电路连接到 Microsoft 企业边缘路由器 （MSEE）。 有关详细信息，请参阅 [ExpressRoute 技术概述](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 建立路由后，它将连接到 Azure 主干。

> [!NOTE] 
> 若要在 Azure 中运行 SAP 布局，请连接到距离 SAP 布局中的 Azure 区域最近的企业边缘路由器。 HANA 大型实例戳通过专用企业边缘路由器设备连接，以最大程度地减少 Azure IaaS 和 HANA 大型实例戳中的 VM 之间的网络延迟。

承载 SAP 应用程序实例的 VM 的 ExpressRoute 网关连接到连接到本地的一个 ExpressRoute 电路。 同一虚拟网络连接到一个专门用于连接大型实例模具的单独企业边缘路由器。 使用 ExpressRoute 快速路径，从 HANA 大型实例到 SAP 应用程序层 VM 的数据流不再通过 ExpressRoute 网关路由，从而减少网络往返延迟。

此系统是单个 SAP 系统的直观示例。 SAP 应用层承载在 Azure 中。 SAP HANA 数据库在 Azure 上的 SAP HANA（大型实例）上运行。 假设 ExpressRoute 网关带宽为 2 Gbps 或 10 Gbps 吞吐量并不代表瓶颈。

## <a name="multiple-sap-systems-or-large-sap-systems"></a>多个 SAP 系统或大型 SAP 系统

如果部署了多个 SAP 系统或大型 SAP 系统以在 Azure（大型实例）上连接到 SAP HANA，则 ExpressRoute 网关的吞吐量可能成为瓶颈。 或者，您希望将生产和非生产系统隔离到不同的 Azure 虚拟网络中。 在这种情况下，请将应用层拆分成多个虚拟网络。 还可以针对以下案例创建用于连接到 HANA 大型实例的特殊虚拟网络：

- 为承载 NFS 共享的 Azure 中的 VM 直接从 HANA 大型实例中的 HANA 实例执行备份
- 将从 HANA 大型实例单位大的备份或其他文件复制到在 Azure 中管理的磁盘空间。

使用单独的虚拟网络托管管理存储的 VM，以便在 HANA 大型实例和 Azure 之间大规模传输数据。 这种安排避免了大型文件或数据传输从 HANA 大型实例传输到 Azure 的 ExpressRoute 网关的影响，该网关为运行 SAP 应用程序层的 VM 提供服务。 

要获得缩放性更好的网络体系结构，请采取以下措施：

- 对于单个较大的 SAP 应用层，使用多个虚拟网络。
- 为所部署的每个 SAP 系统部署一个单独的虚拟网络，而不是将这些 SAP 系统集中放置在同一虚拟网络中的各个子网中。

  适用于 Azure 上的 SAP HANA（大型实例）的缩放性更好的网络体系结构：

![跨多个虚拟网络部署 SAP 应用层](./media/hana-overview-architecture/image4-networking-architecture.png)

根据规则和限制，您希望在托管不同 SAP 系统 VM 的不同虚拟网络之间应用，您应该对这些虚拟网络进行对等。 有关虚拟网络对等互连的详细信息，请参阅[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。


## <a name="routing-in-azure"></a>Azure 中的路由

默认情况下，在 Azure 上的 SAP HANA（大型实例）中，三个网络路由注意事项非常重要：

* Azure 上的 SAP HANA（大型实例）只能通过 Azure VM 和专用 ExpressRoute 连接进行访问，而不能直接从本地访问。 在 Microsoft 向你提供 HANA 大型实例单元后，不能立即从本地直接访问。 传递路由限制是用于 SAP HANA 大型实例的当前 Azure 网络体系结构造成的。 需要进行直接访问的某些管理客户端和任何应用程序（例如在本地运行的 SAP Solution Manager）都无法连接到 SAP HANA 数据库。 有关例外情况，请检查"直接路由到 HANA 大型实例"部分。

* 如果在两个不同的 Azure 区域中部署了 HANA 大型实例单元以进行灾难恢复，则与过去应用的瞬态路由限制相同。 换句话说，一个区域（例如，美国西部）中的 HANA 大型实例单元的 IP 地址未路由到部署在另一个区域（例如美国东部）中的 HANA 大型实例单元。 此限制与跨区域对等的 Azure 网络的使用无关，也独立于将 HANA 大型实例单元连接到虚拟网络的 ExpressRoute 电路交叉连接。 有关图形表示形式，请参阅“在多个区域使用 HANA 大型实例单位”部分中的插图。 此限制与部署的体系结构一起，禁止立即使用 HANA 系统复制作为灾难恢复功能。 有关最近的更改，请查看"在多个区域中使用 HANA 大型实例单元"一节。 

* Azure（大型实例）单元上的 SAP HANA 具有在请求 HANA 大型实例部署时提交的服务器 IP 池地址范围的分配的 IP 地址。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 此 IP 地址可通过将 Azure 虚拟网络连接到 HANA 大型实例的 Azure 订阅和电路访问。 从该服务器 IP 池地址范围中分配的 IP 地址将直接分配给硬件单元， 而不会经过 NAT 转换，在此解决方案的第一个部署中也存在这种情况**。 

### <a name="direct-routing-to-hana-large-instances"></a>直接路由到 HANA 大型实例

默认情况下，过渡路由在以下情况下不起作用：

* 在 HANA 大型实例单元和本地部署之间。

* 部署在两个不同区域中的 HANA 大型实例路由之间。

在这些情况下，有三种方法可以启用传递路由：

- 来回路由数据的反向代理。 例如，F5 BIG-IP，NGINX，在 Azure 虚拟网络中部署了流量管理器，该虚拟网络连接到 HANA 大型实例，并作为虚拟防火墙/流量路由解决方案连接到本地。
- 在 Linux VM 中使用 [IPTables 规则](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ)在本地位置与 HANA 大型实例单元之间，或者在不同区域中的 HANA 大型实例单元之间实现路由。 运行 IPTables 的 VM 需要部署在连接到 HANA 大型实例和本地的 Azure 虚拟网络中。 需要相应地调整 VM 的大小，以便 VM 的网络吞吐量足以满足预期的网络流量。 有关 VM 网络带宽的详细信息，请查看[Azure 中 Linux 虚拟机的大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)文章。
- [Azure 防火墙](https://azure.microsoft.com/services/azure-firewall/)是另一个解决方案，用于在本地和 HANA 大型实例单元之间启用直接流量。 

这些解决方案的所有流量都将通过 Azure 虚拟网络路由，因此，流量可能另外受到使用的软设备或 Azure 网络安全组的限制，因此，某些 IP 地址或 IP 地址范围从本地可能会被阻止或显式允许访问 HANA 大型实例。 

> [!NOTE]  
> 请注意，Microsoft 不实现也不支持涉及第三方网络设备或 IPTables 的自定义解决方案。 必须由所用组件的供应商或集成者提供支持。 

#### <a name="express-route-global-reach"></a>快速路线全球覆盖
微软推出了一种名为["快速路由全球覆盖"的](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)新功能。 全局覆盖可用于两种情况下的 HANA 大型实例：

- 启用从本地直接访问部署在不同区域的 HANA 大型实例单元
- 启用部署在不同区域的 HANA 大型实例单元之间的直接通信


##### <a name="direct-access-from-on-premises"></a>从本地直接访问
在提供全局覆盖的 Azure 区域中，您可以请求为 ExpressRoute 电路启用全局覆盖功能，该电路将本地网络连接到连接到 HANA 大型实例单元的 Azure 虚拟网络。 对 ExpressRoute 电路的本地端存在一些成本影响。 有关价格，请查看[全局覆盖附加组件](https://azure.microsoft.com/pricing/details/expressroute/)的价格。 与将 HANA 大型实例单元连接到 Azure 的电路相关的电路没有额外费用。 

> [!IMPORTANT]  
> 如果使用 Global 覆盖实现在 HANA 大型实例单元和本地资产之间实现直接访问，则网络数据和控制流**不是通过 Azure 虚拟网络路由**的，而是直接在 Microsoft 企业交换路由器之间路由的。 因此，任何 NSG 或 ASG 规则，或您在 Azure 虚拟网络中部署的任何类型的防火墙、NVA 或代理都未被触及。 **如果使用 ExpressRoute 全局覆盖功能，即可在本地端的防火墙中定义从本地到 HANA 大型实例单元的限制和访问 HANA 大型实例单元的权限** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>在不同 Azure 区域中连接 HANA 大型实例
同样，由于 ExpressRoute Global 覆盖可用于将本地连接到 HANA 大型实例单元，因此可用于连接部署在两个不同区域的两个 HANA 大型实例租户。 隔离是 HANA 大型实例租户用于在两个区域连接到 Azure 的 ExpressRoute 电路。 连接部署在两个不同区域的两个 HANA 大型实例租户不收取额外费用。 

> [!IMPORTANT]  
> 不同 HANA 大型实例租户之间的网络流量和控制流不会通过 azure 网络路由。 因此，不能使用 Azure 功能或 NVA 来强制实施两个 HANA 大型实例租户之间的通信限制。 

有关如何启用 ExpressRoute Global 覆盖范围的更多详细信息，请阅读文档[将虚拟网络连接到 HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)。


## <a name="internet-connectivity-of-hana-large-instance"></a>HANA 大型实例的 Internet 连接
HANA 大型实例未建立直接 Internet 连接**。 这会限制某些功能，例如，直接向 OS 供应商注册 OS 映像的功能。 可能需要使用本地 SUSE Linux Enterprise Server 订阅管理工具服务器或 Red Hat Enterprise Linux 订阅管理器。

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM 与 HANA 大型实例之间的数据加密
在 HANA 大型实例与 VM 之间传输的数据不会加密。 但是，仅仅是用于 HANA DBMS 端和基于 JDBC/ODBC 应用程序之间的交换，可以启用加密的流量。 有关详细信息，请参阅[此 SAP 文档](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html)。

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>在多个区域使用 HANA 大型实例单位

要实现灾难恢复设置，需要在多个 Azure 区域中具有 SHANA 大型实例单元。 即使使用 Azure [全局 Vnet 对等互连]，默认情况下，传输路由在两个不同区域的 HANA 大型实例租户之间不起作用。 但是，全局覆盖在两个不同的区域中预配了 HANA 大型实例单元之间的通信路径。 ExpressRoute 全球覆盖的这种使用方案支持：

 - HANA 系统复制，无需任何其他代理或防火墙
 - 在两个不同区域的 HANA 大型实例单元之间复制备份以执行系统副本或系统刷新


![连接到了不同 Azure 区域中的 Azure 大型实例模具的虚拟网络](./media/hana-overview-architecture/image8-multiple-regions.png)

下图显示了两个区域中不同的虚拟网络如何连接到两个不同的 ExpressRoute 电路，这些电路用于在 Azure 区域（大型实例）中连接到 Azure 上的 SAP HANA（大型实例）。 这两个交叉连接的理由是，防止任何一侧的 MSEE 中断。 两个 Azure 区域中的两个虚拟网络之间的通信流应该通过两个不同区域（蓝色虚线）中的两个虚拟网络的[全局对等互连](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/)进行处理。 粗红线描述 ExpressRoute 全局覆盖连接，它允许两个不同区域租户的 HANA 大型实例单元相互通信。 

> [!IMPORTANT] 
> 如果使用了多个 ExpressRoute 线路，则应使用“AS 路径前追加”和“本地首选 BGP”设置来确保正确路由流量。

**后续步骤**
- 请参阅 [SAP HANA（大型实例）存储体系结构](hana-storage-architecture.md)
