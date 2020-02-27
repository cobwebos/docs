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
ms.openlocfilehash: 068cc2ed9743a62aa2249a815893c71499711092
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617017"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA（大型实例）网络体系结构

Azure 网络服务的体系结构是在 HANA 大型实例上成功部署 SAP 应用程序的关键组件。 通常，Azure 上的 SAP HANA（大型实例）部署具有较大的 SAP 布局和多种不同的 SAP 解决方案，其中具有可变的数据库大小、CPU 资源消耗和内存利用。 可能并非所有 IT 系统都已位于 Azure 中。 你的 SAP 布局通常也是混合的，它从 DBMS 点和 SAP 应用程序的角度来看，同时使用 NetWeaver 和 S/4HANA 以及 SAP HANA 与其他 DBMS。 Azure 提供了不同的服务，可让你在 Azure 中运行不同的 DBMS、NetWeaver 和 S/4HANA 系统。 Azure 还提供网络技术，使 Azure 看起来像虚拟数据中心，以实现本地软件部署

除非你的完整 IT 系统托管在 Azure 中。 Azure 网络功能用于将本地环境连接到 Azure 资产，使 Azure 看起来像你的虚拟数据中心。 使用的 Azure 网络功能是： 

- Azure 虚拟网络连接到连接到本地网络资产的[ExpressRoute](https://azure.microsoft.com/services/expressroute/)线路。
- 将本地连接到 Azure 的 ExpressRoute 线路的最小带宽应为[1 Gbps 或更高](https://azure.microsoft.com/pricing/details/expressroute/)。 规定这个最小带宽是为了确保在本地系统与在 VM 上运行的系统之间传输数据。 此外，还能提供足够的带宽从本地最终用户到 Azure 系统的连接。
- 在虚拟网络中设置 Azure 中的所有 SAP 系统以相互通信。
- 本地托管的 Active Directory 和 DNS 通过 ExpressRoute 从本地扩展到 Azure，或者在 Azure 中运行已完成。

对于将 HANA 大型实例集成到 Azure 数据中心网络结构的特定情况，还将使用 Azure ExpressRoute 技术


> [!NOTE] 
> 仅一个 Azure 订阅只能链接到特定 Azure 区域中 HANA 大型实例标记中的一个租户。 相反，单个 HANA 大型实例戳记租户只能链接到一个 Azure 订阅。 此要求与 Azure 中的任何其他计费对象一致。

如果 Azure 上的 SAP HANA （大型实例）部署在多个不同的 Azure 区域中，则在 HANA 大型实例标记中部署单独的租户。 可在同一 Azure 订阅下运行这两个实例，只要这些实例属于同一 SAP 布局即可。 

> [!IMPORTANT] 
> Azure 上的 SAP HANA （大型实例）仅支持 Azure 资源管理器部署方法。

 

## <a name="additional-virtual-network-information"></a>其他虚拟网络信息

若要将虚拟网络连接到 ExpressRoute，必须创建 Azure ExpressRoute 网关。 有关详细信息，请参阅[关于 expressroute 的 expressroute 网关](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

使用 Azure ExpressRoute 网关与 Azure 外部的基础结构或 Azure 大型实例标记。 可以将 Azure ExpressRoute 网关连接到最多四个不同的 ExpressRoute 线路，只要这些连接来自不同的 Microsoft 企业边缘路由器。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

> [!NOTE] 
> 对于 ExpressRoute 网关，可以使用 ExpressRoute 连接实现的最大吞吐量为 10 Gbps。 在位于虚拟网络中的 VM 与本地系统之间复制文件（使用单一复制流）不会获得各种网关 SKU 的完整吞吐量。 若要利用 ExpressRoute 网关的完整带宽，请使用多个流。 或者，必须采用单一文件的并行流复制各个文件。


## <a name="networking-architecture-for-hana-large-instance"></a>HANA 大型实例的网络体系结构
HANA 大型实例的网络体系结构可以分为四个不同的部分：

- 本地网络和到 Azure 的 ExpressRoute 连接。 此部分是客户域，通过 ExpressRoute 连接到 Azure。 此 Expressroute 线路完全由你作为客户支付。 带宽应该足够大，能够处理本地资产与连接到的 Azure 区域之间的网络流量。 请参阅下图右下方。
- 如前所述，Azure 网络服务与虚拟网络一起讨论，后者又需要添加 ExpressRoute 网关。 此部件是需要根据应用程序要求以及安全性和符合性要求找到合适设计的区域。 使用 HANA 大型实例是另一个考虑点，需要考虑到虚拟网络数以及可供选择的 Azure 网关 SKU。 请参阅下图右上方。
- 通过 ExpressRoute 技术将 HANA 大型实例连接到 Azure 中。 此部件已部署，由 Microsoft 处理。 你只需提供一些 IP 地址范围，在将资产部署到 HANA 大型实例中后，再将 ExpressRoute 线路连接到虚拟网络。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 对于 Azure 数据中心网络结构与 HANA 大型实例单位之间的连接，客户不需要支付额外的费用。
- HANA 大型实例戳记中的网络，这对您来说是透明的。

![连接到了 Azure 上的 SAP HANA（大型实例）和本地的虚拟网络](./media/hana-overview-architecture/image1-architecture.png)

使用 HANA 大型实例这一事实并不会改变将本地资产通过 ExpressRoute 连接到 Azure 这一要求， 也不会改变使用一个或多个虚拟网络这一要求。虚拟网络运行的 Azure VM 托管应用层，该层连接到托管在 HANA 大型实例单位中的 HANA 实例。 

Azure 中 SAP 部署的差别如下：

- 客户租户的 HANA 大型实例单位通过另一 ExpressRoute 线路连接到虚拟网络中。 为了隔离负载条件，本地到 Azure 虚拟网络 ExpressRoute 线路和 Azure 虚拟网络与 HANA 大型实例之间的线路不共享相同的路由器。
- 在 SAP 应用层和 HANA 大型实例之间的工作负荷配置文件在本质上不同于包含多个小型请求的迸发型数据传输（结果集），后者是从 SAP HANA 传输到应用层中。
- 与数据在本地和 Azure 之间交换的典型方案相比，SAP 应用程序体系结构对网络延迟更为敏感。
- Azure ExpressRoute 网关具有至少两个 ExpressRoute 连接。 一个从本地连接的线路，另一个从 HANA 大型实例连接的线路。 这只是为了使不同 Msee 中的另一条额外线路连接到 ExpressRoute 网关。 此限制与 ExpressRoute 快速路径的使用无关。 所有连接的线路均共享 ExpressRoute 网关传入数据的最大带宽。

在 HANA 大型实例戳记的修订版3中，Vm 与 HANA 大型实例单元之间经历的网络延迟可能高于典型的 VM 到 VM 网络往返延迟。 测量到的值可能超过 0.7 毫秒的往返延迟，具体取决于 Azure 区域。而在 [SAP 说明 #1100926 - 常见问题解答：网络性能](https://launchpad.support.sap.com/#/notes/1100926/E)中，0.7 毫秒被归类为低于平均值。 依赖于 Azure 区域和工具来测量 Azure VM 和 HANA 大型实例单元之间的网络往返延迟，所测量的延迟可以达到或大约 2 毫秒。 尽管如此，客户在 SAP HANA 大型实例上部署基于 SAP HANA 的生产型 SAP 应用程序很成功。 请确保在 Azure HANA 大型实例中对自己的业务流程进行彻底的测试。 称为 ExpressRoute 快速路径的一项新功能，它能够减少 Azure 中 HANA 大型实例和应用程序层 Vm 之间的网络延迟（见下文）。 

在版本为 HANA 大型实例标记的情况下，如 SAP 说明 #1100926-常见问题：如果配置了 Azure ExpressRoute Fast 路径，则在[SAP 说明-FAQ：网络性能](https://launchpad.support.sap.com/#/notes/1100926/E)（如下所示）中所述，在 azure VM 与 Hana 大型实例戳记更近之间网络延迟。 若要部署接近于版本4的 HANA 大型实例单元的 Azure Vm，需利用[Azure 邻近性放置组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)。 [对于 sap 应用程序的最佳网络延迟，Azure 邻近性放置组](sap-proximity-placement-scenarios.md)中介绍了如何使用近程放置组来查找同一 azure 数据中心内的 sap 应用程序层（如修订版4托管 HANA 大型实例单位）。

若要在 Vm 和 HANA 大型实例之间提供确定性网络延迟，请选择 ExpressRoute 网关 SKU。 不同于本地与 VM 之间的流量模式，VM 与 HANA 大型实例之间的流量模式可能是这样的：一开始流量很小，但随着要传输的请求和数据量的增多，可能会出现流量突然增高的迸发现象。 为了应对这种迸发现象，我们强烈建议使用 UltraPerformance 网关 SKU。 对于类型 II 类 HANA 大型实例 Sku，将 UltraPerformance 网关 SKU 用作 Expressroute 网关是必需的。

> [!IMPORTANT] 
> 假定所有的网络流量都位于 SAP 应用层与数据库层之间，则仅支持使用虚拟网络的 HighPerformance 或 UltraPerformance 网关 SKU 来连接到 Azure 上的 SAP HANA（大型实例）。 对于 HANA 大型实例类型 II Sku，只支持使用 UltraPerformance 网关 SKU 作为 ExpressRoute 网关。 使用 ExpressRoute 快速路径时的异常适用（请参阅下文）

### <a name="expressroute-fast-path"></a>ExpressRoute 快速路径
为了降低延迟，为托管 SAP 应用程序 Vm 的 Azure 虚拟网络提供了在5月2019的特定连接中引入和发布的 ExpressRoute 快速路径。 到目前为止，解决方案的主要差异在于： Vm 与 HANA 大型实例之间的数据流动不再通过 ExpressRoute 网关路由。 相反，Azure 虚拟网络的子网中分配的 Vm 会直接与专用企业边缘路由器进行通信。 

> [!IMPORTANT] 
> ExpressRoute 快速路径功能要求运行 SAP 应用程序 Vm 的子网位于连接到 HANA 大型实例的同一 Azure 虚拟网络中。 位于 Azure 虚拟网络中的 Vm 与通过 Azure 虚拟网络直接连接到 HANA 大型实例单元对等互连 因此，典型的中心和辐射虚拟网络设计，ExpressRoute 线路连接到中心虚拟网络，虚拟网络（包含 SAP 应用程序层（轮辐））正在获得对等互连，通过 ExpressRoute 快速优化路径将不起作用。 在除了中，ExpressRoute Fast 路径目前不支持用户定义的路由规则（UDR）。 有关详细信息，请参阅[ExpressRoute 虚拟网络网关和 FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)。 


有关如何配置 ExpressRoute 快速路径的更多详细信息，请参阅将[虚拟网络连接到 HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)的文档。    

> [!NOTE]
> 需要 UltraPerformance ExpressRoute 网关才能使用 ExpressRoute 快速路径


## <a name="single-sap-system"></a>单个 SAP 系统

上面所示的本地基础结构通过 ExpressRoute 连接到 Azure。 ExpressRoute 线路连接到 Microsoft 企业边缘路由器（MSEE）。 有关详细信息，请参阅 [ExpressRoute 技术概述](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 建立路由后，它会连接到 Azure 主干。

> [!NOTE] 
> 若要在 Azure 中运行 SAP 布局，请连接到距离 SAP 布局中的 Azure 区域最近的企业边缘路由器。 HANA 大型实例戳通过专用企业边缘路由器设备进行连接，以最大限度地减少 Azure IaaS 中 Vm 与 HANA 大型实例标记之间的网络延迟。

托管 SAP 应用程序实例的 Vm 的 ExpressRoute 网关连接到一个连接到本地的 ExpressRoute 线路。 同一虚拟网络连接到一个专门用于连接大型实例模具的单独企业边缘路由器。 使用 ExpressRoute 快速路径，从 HANA 大型实例到 SAP 应用程序层 Vm 的数据流将不再通过 ExpressRoute 网关路由，从而减少网络往返延迟。

此系统是单个 SAP 系统的直观示例。 SAP 应用层承载在 Azure 中。 SAP HANA 数据库在 Azure 上的 SAP HANA（大型实例）上运行。 假设有 2 Gbps 或 10 Gbps 吞吐量的 ExpressRoute 网关带宽不表示瓶颈。

## <a name="multiple-sap-systems-or-large-sap-systems"></a>多个 SAP 系统或大型 SAP 系统

如果部署了多个 SAP 系统或大型 SAP 系统来连接到 Azure 上的 SAP HANA （大型实例），ExpressRoute 网关的吞吐量可能会成为瓶颈。 或者，需要在不同的 Azure 虚拟网络中隔离生产和非生产系统。 在这种情况下，请将应用层拆分成多个虚拟网络。 还可以针对以下案例创建用于连接到 HANA 大型实例的特殊虚拟网络：

- 为承载 NFS 共享的 Azure 中的 VM 直接从 HANA 大型实例中的 HANA 实例执行备份
- 将从 HANA 大型实例单位大的备份或其他文件复制到在 Azure 中管理的磁盘空间。

使用独立的虚拟网络来托管 Vm，这些 Vm 用于管理存储，以便在 HANA 大型实例和 Azure 之间批量传输数据。 这种安排避免了在为运行 SAP 应用程序层的 Vm 提供服务的 ExpressRoute 网关上，将大型文件或数据从 HANA 大型实例传输到 Azure 的影响。 

要获得缩放性更好的网络体系结构，请采取以下措施：

- 对于单个较大的 SAP 应用层，使用多个虚拟网络。
- 为所部署的每个 SAP 系统部署一个单独的虚拟网络，而不是将这些 SAP 系统集中放置在同一虚拟网络中的各个子网中。

  适用于 Azure 上的 SAP HANA（大型实例）的缩放性更好的网络体系结构：

![跨多个虚拟网络部署 SAP 应用层](./media/hana-overview-architecture/image4-networking-architecture.png)

根据规则和限制，你希望在托管不同 SAP 系统的虚拟机的不同虚拟网络之间进行应用，你应该对这些虚拟网络对等互连。 有关虚拟网络对等互连的详细信息，请参阅[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。


## <a name="routing-in-azure"></a>Azure 中的路由

默认情况下，部署的三个网络路由注意事项对于 Azure 上的 SAP HANA （大型实例）非常重要：

* Azure 上的 SAP HANA （大型实例）只能通过 Azure Vm 和专用 ExpressRoute 连接进行访问，而不能直接从本地访问。 在 Microsoft 向你提供 HANA 大型实例单元后，不能立即从本地直接访问。 传递路由限制是用于 SAP HANA 大型实例的当前 Azure 网络体系结构造成的。 需要进行直接访问的某些管理客户端和任何应用程序（例如在本地运行的 SAP Solution Manager）都无法连接到 SAP HANA 数据库。 有关异常，请检查 "直接路由到 HANA 大型实例" 部分。

* 如果在两个不同的 Azure 区域部署了 HANA 大型实例单元用于进行灾难恢复，则会在过去应用相同的暂时性路由限制。 换言之，一个区域（例如美国西部）中的 HANA 大型实例单元的 IP 地址未路由到另一个区域（例如美国东部）中部署的 HANA 大型实例单元。 此限制独立于跨区域使用 Azure 网络对等互连，或者跨连接 ExpressRoute 线路（将 HANA 大型实例单元连接到虚拟网络）。 有关图形表示形式，请参阅“在多个区域使用 HANA 大型实例单位”部分中的插图。 此限制在已部署的体系结构的基础上，禁止立即将 HANA 系统复制用作灾难恢复功能。 对于最近的更改，查找 "在多个区域使用 HANA 大型实例单元" 一节。 

* Azure 上的 SAP HANA （大型实例）单位有一个从服务器 IP 池地址范围分配的 IP 地址，该地址是在请求 HANA 大型实例部署时提交的。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 可以通过将 Azure 虚拟网络连接到 HANA 大型实例的 Azure 订阅和线路来访问此 IP 地址。 从该服务器 IP 池地址范围中分配的 IP 地址将直接分配给硬件单元， 而不会经过 NAT 转换，在此解决方案的第一个部署中也存在这种情况。 

### <a name="direct-routing-to-hana-large-instances"></a>直接路由到 HANA 大型实例

默认情况下，在以下情况下，可传递路由不起作用：

* 在 HANA 大型实例单元和本地部署之间。

* 在两个不同区域中部署的 HANA 大型实例路由之间。

在这些情况下，可通过三种方式启用可传递路由：

- 来回路由数据的反向代理。 例如，在连接到 HANA 大型实例并连接到本地作为虚拟防火墙/流量路由解决方案的 Azure 虚拟网络中，F5 大 IP、带有流量管理器的 NGINX。
- 在 Linux VM 中使用 [IPTables 规则](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ)在本地位置与 HANA 大型实例单元之间，或者在不同区域中的 HANA 大型实例单元之间实现路由。 需要将运行 IPTables 的 VM 部署到连接到 HANA 大型实例和本地的 Azure 虚拟网络中。 VM 需要相应调整大小，因此，VM 的网络吞吐量足以满足预期的网络流量。 有关 VM 网络带宽的详细信息，请参阅[Azure 中 Linux 虚拟机的大小一](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)文。
- [Azure 防火墙](https://azure.microsoft.com/services/azure-firewall/)将是另一种解决方案，用于在本地与 HANA 大型实例单元之间实现直接通信。 

这些解决方案的所有流量都将通过 Azure 虚拟网络进行路由，因此，Azure 网络安全组所使用的软设备可能还会对流量进行限制，因此某些 IP 地址或 IP 地址范围来自本地可能被阻止或被显式允许访问 HANA 大型实例。 

> [!NOTE]  
> 请注意，Microsoft 不实现也不支持涉及第三方网络设备或 IPTables 的自定义解决方案。 必须由所用组件的供应商或集成者提供支持。 

#### <a name="express-route-global-reach"></a>Express Route Global Reach
Microsoft 引入了新功能，称为[ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)。 在两个方案中，Global Reach 可用于 HANA 大型实例：

- 启用从本地到部署在不同区域中的 HANA 大型实例单元的直接访问
- 在部署在不同区域中的 HANA 大型实例单元之间实现直接通信


##### <a name="direct-access-from-on-premises"></a>从本地直接访问
在提供 Global Reach 的 Azure 区域中，你可以请求为 ExpressRoute 线路启用 Global Reach 功能，将本地网络连接到连接到 HANA 大型实例单元的 Azure 虚拟网络。 ExpressRoute 线路的本地端有一些成本含义。 对于价格，请查看[Global Reach 外接程序](https://azure.microsoft.com/pricing/details/expressroute/)的价格。 对于将 HANA 大型实例单元连接到 Azure 的线路，没有额外的费用。 

> [!IMPORTANT]  
> 如果使用 Global Reach 在 HANA 大型实例单元和本地资产之间启用直接访问，则**不会通过 Azure 虚拟网络路由**网络数据和控制流，而直接通过 Microsoft 企业 exchange 路由器进行路由。 因此，在 Azure 虚拟网络中部署的任何 NSG 或 ASG 规则或任何类型的防火墙、NVA 或代理都不会受到接触。 **如果你使用 ExpressRoute Global Reach 允许从本地到 HANA 大型实例单元的直接访问限制和访问 HANA 大型实例单位的权限，则需要在本地端的防火墙中定义。** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>连接不同 Azure 区域中的 HANA 大型实例
同样，与 ExpressRoute Global Reach 可用于将本地连接到 HANA 大型实例单元时，可以使用它将在两个不同的区域中为你部署的两 HANA 大型实例租户连接起来。 隔离是 HANA 大型实例租户用于连接到 Azure 中两个区域的 ExpressRoute 线路。 连接两个不同区域中部署的两个 HANA 大型实例租户不会产生额外费用。 

> [!IMPORTANT]  
> 不同 HANA 大型实例租户之间的网络流量的数据流和控制流不会通过 azure 网络路由。 因此，不能使用 Azure 功能或 Nva 来强制实施两个 HANA 大型实例租户之间的通信限制。 

有关如何启用 ExpressRoute Global Reach 的更多详细信息，请参阅将[虚拟网络连接到 HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)的文档。


## <a name="internet-connectivity-of-hana-large-instance"></a>HANA 大型实例的 Internet 连接
HANA 大型实例未建立直接 Internet 连接。 这会限制某些功能，例如，直接向 OS 供应商注册 OS 映像的功能。 可能需要使用本地 SUSE Linux Enterprise Server 订阅管理工具服务器或 Red Hat Enterprise Linux 订阅管理器。

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM 与 HANA 大型实例之间的数据加密
在 HANA 大型实例与 VM 之间传输的数据不会加密。 但是，仅仅是用于 HANA DBMS 端和基于 JDBC/ODBC 应用程序之间的交换，可以启用加密的流量。 有关详细信息，请参阅[此 SAP 文档](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)。

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>在多个区域使用 HANA 大型实例单位

若要实现灾难恢复集的使用，需要在多个 Azure 区域中 SHANA 大型实例单位。 即使使用 Azure [全局 Vnet 对等互连]，默认情况下，在两个不同区域中的 HANA 大型实例租户之间，传递的路由仍不起作用。 但 Global Reach 会打开在两个不同区域中预配的 HANA 大型实例单元之间的通信路径。 ExpressRoute Global Reach 的这种使用方案启用：

 - HANA 系统复制，无需任何其他代理或防火墙
 - 在两个不同的区域中的 HANA 大型实例单元之间复制备份，以执行系统副本或系统刷新


![连接到了不同 Azure 区域中的 Azure 大型实例模具的虚拟网络](./media/hana-overview-architecture/image8-multiple-regions.png)

此图显示了两个区域中的不同虚拟网络如何连接到两个不同的 ExpressRoute 线路，这些线路用于连接到两个 Azure 区域中的 Azure （大型实例）上的 SAP HANA。 这两个交叉连接的原因是为了防止任何一侧的 Msee 中断。 两个 Azure 区域中的两个虚拟网络之间的通信流应在两个不同区域（蓝色虚线）中两个虚拟网络的[全局对等互连](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/)上进行处理。 厚红线介绍 ExpressRoute Global Reach 连接，这允许两个不同区域中的租户的 HANA 大型实例单元彼此通信。 

> [!IMPORTANT] 
> 如果使用了多个 ExpressRoute 线路，则应使用“AS 路径前追加”和“本地首选 BGP”设置来确保正确路由流量。

**后续步骤**
- 请参阅 [SAP HANA（大型实例）存储体系结构](hana-storage-architecture.md)
