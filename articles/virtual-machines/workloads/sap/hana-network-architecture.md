---
title: Azure 上的 SAP HANA（大型实例）的网络体系结构 | Microsoft Docs
description: 有关如何部署 Azure 上的 SAP HANA（大型实例）的网络体系结构。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3777180a4d62f8b253ac4cd096bff15613f33565
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74206627"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA（大型实例）网络体系结构

Azure 网络服务的体系结构是在 HANA 大型实例上成功部署 SAP 应用程序的关键组件。 通常，Azure 上的 SAP HANA（大型实例）部署具有较大的 SAP 布局和多种不同的 SAP 解决方案，其中具有可变的数据库大小、CPU 资源消耗和内存利用。 It's likely that not all IT systems are located in Azure already. Your SAP landscape is often hybrid as well from a DBMS point and SAP application point of view using a mixture of NetWeaver, and S/4HANA and SAP HANA and other DBMS. Azure offers different services that allow you to run the different DBMS, NetWeaver, and S/4HANA systems in Azure. Azure also offers you network technology to make Azure look like a virtual data center to your on-premises software deployments

Unless your complete IT systems are hosted in Azure. Azure networking functionality is used to connect the on-premises world with your Azure assets to make Azure look like a virtual datacenter of yours. The Azure network functionality used is: 

- Azure virtual networks are connected to the [ExpressRoute](https://azure.microsoft.com/services/expressroute/) circuit that connects to your on-premises network assets.
- An ExpressRoute circuit that connects on-premises to Azure should have a minimum bandwidth of [1 Gbps or higher](https://azure.microsoft.com/pricing/details/expressroute/). 规定这个最小带宽是为了确保在本地系统与在 VM 上运行的系统之间传输数据。 此外，还能提供足够的带宽从本地最终用户到 Azure 系统的连接。
- All SAP systems in Azure are set up in virtual networks to communicate with each other.
- Active Directory and DNS hosted on-premises are extended into Azure through ExpressRoute from on-premises, or are running complete in Azure.

For the specific case of integrating HANA Large Instances into the Azure data center network fabric, Azure ExpressRoute technology is used as well


> [!NOTE] 
> Only one Azure subscription can be linked to only one tenant in a HANA Large Instance stamp in a specific Azure region. Conversely, a single HANA Large Instance stamp tenant can be linked to only one Azure subscription. 此要求与 Azure 中的任何其他计费对象一致。

If SAP HANA on Azure (Large Instances) is deployed in multiple different Azure regions, a separate tenant is deployed in the HANA Large Instance stamp. 可在同一 Azure 订阅下运行这两个实例，只要这些实例属于同一 SAP 布局即可。 

> [!IMPORTANT] 
> Only the Azure Resource Manager deployment method is supported with SAP HANA on Azure (Large Instances).

 

## <a name="additional-virtual-network-information"></a>其他虚拟网络信息

To connect a virtual network to ExpressRoute, an Azure ExpressRoute gateway must be created. For more information, see [About Expressroute gateways for ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

An Azure ExpressRoute gateway is used with ExpressRoute to an infrastructure outside of Azure or to an Azure Large Instance stamp. You can connect the Azure ExpressRoute gateway to a maximum of four different ExpressRoute circuits as long as those connections come from different Microsoft enterprise edge routers. 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

> [!NOTE] 
> The maximum throughput you can achieve with a ExpressRoute gateway is 10 Gbps by using an ExpressRoute connection. 在位于虚拟网络中的 VM 与本地系统之间复制文件（使用单一复制流）不会获得各种网关 SKU 的完整吞吐量。 To leverage the complete bandwidth of the ExpressRoute gateway, use multiple streams. 或者，必须采用单一文件的并行流复制各个文件。


## <a name="networking-architecture-for-hana-large-instance"></a>HANA 大型实例的网络体系结构
HANA 大型实例的网络体系结构可以分为四个不同的部分：

- 本地网络和到 Azure 的 ExpressRoute 连接。 此部分是客户域，通过 ExpressRoute 连接到 Azure。 This Expressroute circuit is fully paid by you as a customer. The bandwidth should be large enough to handle the network traffic between your on-premises assets and the Azure region you are connecting against. 请参阅下图右下方。
- Azure network services, as previously discussed, with virtual networks, which again need ExpressRoute gateways added. 此部件是需要根据应用程序要求以及安全性和符合性要求找到合适设计的区域。 使用 HANA 大型实例是另一个考虑点，需要考虑到虚拟网络数以及可供选择的 Azure 网关 SKU。 请参阅下图右上方。
- 通过 ExpressRoute 技术将 HANA 大型实例连接到 Azure 中。 此部件已部署，由 Microsoft 处理。 你只需提供一些 IP 地址范围，在将资产部署到 HANA 大型实例中后，再将 ExpressRoute 线路连接到虚拟网络。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 There is no additional fee for you as a customer for the connectivity between the Azure data center network fabric and HANA Large Instance units.
- Networking within the HANA Large Instance stamp, which is mostly transparent for you.

![连接到了 Azure 上的 SAP HANA（大型实例）和本地的虚拟网络](./media/hana-overview-architecture/image1-architecture.png)

使用 HANA 大型实例这一事实并不会改变将本地资产通过 ExpressRoute 连接到 Azure 这一要求， 也不会改变使用一个或多个虚拟网络这一要求。虚拟网络运行的 Azure VM 托管应用层，该层连接到托管在 HANA 大型实例单位中的 HANA 实例。 

Azure 中 SAP 部署的差别如下：

- 客户租户的 HANA 大型实例单位通过另一 ExpressRoute 线路连接到虚拟网络中。 To separate load conditions, the on-premises to Azure virtual network ExpressRoute circuits and the circuits between Azure virtual networks and HANA Large Instances don't share the same routers.
- 在 SAP 应用层和 HANA 大型实例之间的工作负荷配置文件在本质上不同于包含多个小型请求的迸发型数据传输（结果集），后者是从 SAP HANA 传输到应用层中。
- 与数据在本地和 Azure 之间交换的典型方案相比，SAP 应用程序体系结构对网络延迟更为敏感。
- The Azure ExpressRoute gateway has at least two ExpressRoute connections. One circuit that is connected from on-premises and one that is connected from HANA Large Instances. This leaves only room for another two additional circuits from different MSEEs to connect to on ExpressRoute Gateway. This restriction is independent of the usage of ExpressRoute Fast Path. All the connected circuits share the maximum bandwidth for incoming data of the ExpressRoute gateway.

With Revision 3 of HANA Large Instance stamps, the network latency experienced between VMs and HANA Large Instance units can be higher than a typical VM-to-VM network round-trip latency. 测量到的值可能超过 0.7 毫秒的往返延迟，具体取决于 Azure 区域。而在 [SAP 说明 #1100926 - 常见问题解答：网络性能](https://launchpad.support.sap.com/#/notes/1100926/E)中，0.7 毫秒被归类为低于平均值。 依赖于 Azure 区域和工具来测量 Azure VM 和 HANA 大型实例单元之间的网络往返延迟，所测量的延迟可以达到或大约 2 毫秒。 尽管如此，客户在 SAP HANA 大型实例上部署基于 SAP HANA 的生产型 SAP 应用程序很成功。 请确保在 Azure HANA 大型实例中对自己的业务流程进行彻底的测试。 A new functionality, called ExpressRoute Fast Path, is able to reduce the network latency between HANA Large Instances and application layer VMs in Azure substantially (see below). 

With Revision 4 of HANA Large Instance stamps, the network latency between Azure VMs that are deployed in proximity to the HANA Large Instance stamp, is experienced to meet the average or better than average classification as documented in [SAP Note #1100926 - FAQ: Network performance](https://launchpad.support.sap.com/#/notes/1100926/E) if Azure ExpressRoute Fast Path is configured (see below). In order to deploy Azure VMs in close proximity to HANA Large Instance units of Revision 4, you need to leverage [Azure Proximity Placement Groups](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). The way how proximity placement groups can be used to locate the SAP application layer in the same Azure datacenter as Revision 4 hosted HANA Large Instance units is described in [Azure Proximity Placement Groups for optimal network latency with SAP applications](sap-proximity-placement-scenarios.md).

To provide deterministic network latency between VMs and HANA Large Instance, the choice of the ExpressRoute gateway SKU is essential. 不同于本地与 VM 之间的流量模式，VM 与 HANA 大型实例之间的流量模式可能是这样的：一开始流量很小，但随着要传输的请求和数据量的增多，可能会出现流量突然增高的迸发现象。 为了应对这种迸发现象，我们强烈建议使用 UltraPerformance 网关 SKU。 For the Type II class of HANA Large Instance SKUs, the use of the UltraPerformance gateway SKU as a ExpressRotue gateway is mandatory.

> [!IMPORTANT] 
> 假定所有的网络流量都位于 SAP 应用层与数据库层之间，则仅支持使用虚拟网络的 HighPerformance 或 UltraPerformance 网关 SKU 来连接到 Azure 上的 SAP HANA（大型实例）。 For HANA Large Instance Type II SKUs, only the UltraPerformance gateway SKU is supported as a ExpressRoute gateway. Exceptions apply when using ExpressRoute Fast Path (see below)

### <a name="expressroute-fast-path"></a>ExpressRoute Fast Path
To lower the latency, ExpressRoute Fast Path got introduced and released in May 2019 for the specific connectivity of HANA Large Instances to Azure virtual networks that host the SAP application VMs. The major difference to the solution rolled out so far, is, that the data flows between VMs and HANA Large Instances are not routed through the ExpressRoute gateway anymore. Instead the VMs assigned in the subnet(s) of the Azure virtual network are directly communicating with the dedicated enterprise edge router. 

> [!IMPORTANT] 
> The ExpressRoute Fast Path functionality requires that the subnets running the SAP application VMs are in the same Azure virtual network that got connected to the HANA Large Instances. VMs located in Azure virtual networks that are peered with the Azure virtual network connected directly to the HANA Large Instance units are not benefiting from ExpressRoute Fast Path. As a result typical hub and spoke virtual network designs, where the ExpressRoute circuits are connecting against a hub virtual network and virtual networks containing the SAP application layer (spokes) are getting peered, the optimization by ExpressRoute Fast Path will not work. In addtion, ExpressRoute Fast Path does not support user defined routing rules (UDR) today. For more information, see [ExpressRoute virtual network gateway and FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


For more details on how to configure ExpressRoute Fast Path, read the document [Connect a virtual network to HANA large instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).    

> [!NOTE]
> An UltraPerformance ExpressRoute gateway is required to have ExpressRoute Fast Path working


## <a name="single-sap-system"></a>单个 SAP 系统

上面所示的本地基础结构通过 ExpressRoute 连接到 Azure。 The ExpressRoute circuit connects into a Microsoft enterprise edge router (MSEE). 有关详细信息，请参阅 [ExpressRoute 技术概述](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 After the route is established, it connects into the Azure backbone.

> [!NOTE] 
> 若要在 Azure 中运行 SAP 布局，请连接到距离 SAP 布局中的 Azure 区域最近的企业边缘路由器。 HANA Large Instance stamps are connected through dedicated enterprise edge router devices to minimize network latency between VMs in Azure IaaS and HANA Large Instance stamps.

The ExpressRoute gateway for the VMs that host SAP application instances are connected to one ExpressRoute circuit that connects to on-premises. 同一虚拟网络连接到一个专门用于连接大型实例模具的单独企业边缘路由器。 Using ExpressRoute Fast Path, the data flow from HANA Large Instances to the SAP application layer VMs are not routed through the ExpressRoute gateway anymore and with that reduce the network round-trip latency.

此系统是单个 SAP 系统的直观示例。 SAP 应用层承载在 Azure 中。 SAP HANA 数据库在 Azure 上的 SAP HANA（大型实例）上运行。 The assumption is that the ExpressRoute gateway bandwidth of 2-Gbps or 10-Gbps throughput doesn't represent a bottleneck.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>多个 SAP 系统或大型 SAP 系统

If multiple SAP systems or large SAP systems are deployed to connect to SAP HANA on Azure (Large Instances), the throughput of the ExpressRoute gateway might become a bottleneck. Or you want to isolate production and non-production systems in different Azure virtual networks. 在这种情况下，请将应用层拆分成多个虚拟网络。 还可以针对以下案例创建用于连接到 HANA 大型实例的特殊虚拟网络：

- 为承载 NFS 共享的 Azure 中的 VM 直接从 HANA 大型实例中的 HANA 实例执行备份
- 将从 HANA 大型实例单位大的备份或其他文件复制到在 Azure 中管理的磁盘空间。

Use a separate virtual network to host VMs that manage storage for mass transfer of data between HANA Large Instances and Azure. This arrangement avoids the effects of large file or data transfer from HANA Large Instance to Azure on the ExpressRoute gateway that serves the VMs that run the SAP application layer. 

要获得缩放性更好的网络体系结构，请采取以下措施：

- 对于单个较大的 SAP 应用层，使用多个虚拟网络。
- 为所部署的每个 SAP 系统部署一个单独的虚拟网络，而不是将这些 SAP 系统集中放置在同一虚拟网络中的各个子网中。

  适用于 Azure 上的 SAP HANA（大型实例）的缩放性更好的网络体系结构：

![跨多个虚拟网络部署 SAP 应用层](./media/hana-overview-architecture/image4-networking-architecture.png)

Dependent on the rules and restrictions, you want to apply between the different virtual networks hosting VMs of different SAP systems, you should peer those virtual networks. 有关虚拟网络对等互连的详细信息，请参阅[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。


## <a name="routing-in-azure"></a>Azure 中的路由

By default deployment, three network routing considerations are important for SAP HANA on Azure (Large Instances):

* SAP HANA on Azure (Large Instances) can be accessed only through Azure VMs and the dedicated ExpressRoute connection, not directly from on-premises. 在 Microsoft 向你提供 HANA 大型实例单元后，不能立即从本地直接访问。 传递路由限制是用于 SAP HANA 大型实例的当前 Azure 网络体系结构造成的。 需要进行直接访问的某些管理客户端和任何应用程序（例如在本地运行的 SAP Solution Manager）都无法连接到 SAP HANA 数据库。 For exceptions check the section 'Direct Routing to HANA Large Instances'.

* If you have HANA Large Instance units deployed in two different Azure regions for disaster recovery, the same transient routing restrictions applied in the past. In other words, IP addresses of a HANA Large Instance unit in one region (for example, US West) were not routed to a HANA Large Instance unit deployed in another region (for example, US East). This restriction was independent of the use of Azure network peering across regions or cross-connecting the ExpressRoute circuits that connect HANA Large Instance units to virtual networks. 有关图形表示形式，请参阅“在多个区域使用 HANA 大型实例单位”部分中的插图。 This restriction, which came with the deployed architecture, prohibited the immediate use of HANA System Replication as disaster recovery functionality. For recent changes, look up the section 'Use HANA Large Instance units in multiple regions'. 

* SAP HANA on Azure (Large Instances) units have an assigned IP address from the server IP pool address range that you submitted when requesting the HANA Large Instance deployment. 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 This IP address is accessible through the Azure subscriptions and circuit  that connects Azure virtual networks to HANA Large Instances. 从该服务器 IP 池地址范围中分配的 IP 地址将直接分配给硬件单元， 而不会经过 NAT 转换，在此解决方案的第一个部署中也存在这种情况。 

### <a name="direct-routing-to-hana-large-instances"></a>Direct Routing to HANA Large Instances

By default, the transitive routing does not work in these scenarios:

* Between HANA Large Instance units and an on-premises deployment.

* Between HANA Large Instance routing that are deployed in two different regions.

There are three ways to enable transitive routing in those scenarios:

- 来回路由数据的反向代理。 For example, F5 BIG-IP, NGINX with Traffic Manager deployed in the Azure virtual network that connects to HANA Large Instances and to on-premises as a virtual firewall/traffic routing solution.
- 在 Linux VM 中使用 [IPTables 规则](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ)在本地位置与 HANA 大型实例单元之间，或者在不同区域中的 HANA 大型实例单元之间实现路由。 The VM running IPTables needs to be deployed in the Azure virtual network that connects to HANA Large Instances and to on-premises. The VM needs to be sized accordingly, so, that the network throughput of the VM is sufficient for the expected network traffic. For details on VM network bandwidth, check the article [Sizes of Linux virtual machines in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) would be another solution to enable direct traffic between on-premises and HANA Large instance units. 

All the traffic of these solutions would be routed through an Azure virtual network and as such the traffic could be additionally restricted by the soft appliances used or by Azure Network Security Groups, so, that certain IP addresses or IP address ranges from on-premises could be blocked or explicitly allowed accessing HANA Large Instances. 

> [!NOTE]  
> 请注意，Microsoft 不实现也不支持涉及第三方网络设备或 IPTables 的自定义解决方案。 必须由所用组件的供应商或集成者提供支持。 

#### <a name="express-route-global-reach"></a>Express Route Global Reach
Microsoft introduced a new functionality called [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). Global Reach can be used for HANA Large Instances in two scenarios:

- Enable direct access from on-premises to your HANA Large Instance units deployed in different regions
- Enable direct communication between your HANA Large Instance units deployed in different regions


##### <a name="direct-access-from-on-premises"></a>Direct Access from on-premises
In the Azure regions where Global Reach is offered, you can request enabling the Global Reach functionality for your ExpressRoute circuit that connects your on-premises network to the Azure virtual network that connects to your HANA Large Instance units as well. There are some cost implications for the on-premises side of your ExpressRoute circuit. For prices, check the prices for [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/). There are no additional costs for you related to the circuit that connects the HANA Large Instance unit(s) to Azure. 

> [!IMPORTANT]  
> In case of using Global Reach for enabling direct access between your HANA Large Instance units and on-premises assets, the network data and control flow is **not routed through Azure virtual networks**, but directly between the Microsoft enterprise exchange routers. As a result any NSG or ASG rules, or any type of firewall, NVA, or proxy you deployed in an Azure virtual network, are not getting touched. **If you use ExpressRoute Global Reach to enable direct access from on-premises to HANA Large instance units restrictions and permissions to access HANA large Instance units need to be defined in firewalls on the on-premises side** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Connecting HANA Large Instances in different Azure regions
In the same way, as ExpressRoute Global Reach can be used for connecting on-premises to HANA Large Instance units, it can be used to connect tow HANA Large Instance tenants that are deployed for you in two different regions. The isolation is the ExpressRoute circuits that your HANA Large Instance tenants are using to connect to Azure in both regions. There are no additional charges for connecting two HANA Large Instance tenants that are deployed in two different regions. 

> [!IMPORTANT]  
> The data flow and control flow of the network traffic between the different HANA Large instance tenants will not be routed through azure networks. As a result you can't use Azure functionality or NVAs to enforce communication restrictions between your two HANA Large Instances tenants. 

For more details on how to get ExpressRoute Global Reach enabled, read the document [Connect a virtual network to HANA large instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).


## <a name="internet-connectivity-of-hana-large-instance"></a>HANA 大型实例的 Internet 连接
HANA 大型实例未建立直接 Internet 连接。 这会限制某些功能，例如，直接向 OS 供应商注册 OS 映像的功能。 可能需要使用本地 SUSE Linux Enterprise Server 订阅管理工具服务器或 Red Hat Enterprise Linux 订阅管理器。

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM 与 HANA 大型实例之间的数据加密
在 HANA 大型实例与 VM 之间传输的数据不会加密。 但是，仅仅是用于 HANA DBMS 端和基于 JDBC/ODBC 应用程序之间的交换，可以启用加密的流量。 有关详细信息，请参阅[此 SAP 文档](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)。

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>在多个区域使用 HANA 大型实例单位

To realize disaster recovery set ups, you need to have SHANA Large Instance units in multiple Azure regions. Even with using Azure [Global Vnet Peering], the transitive routing by default is not working between HANA Large Instance tenants in two different regions. However, Global Reach opens up the communication path between the HANA Large Instance units you have provisioned in two different regions. This usage scenario of ExpressRoute Global Reach enables:

 - HANA System Replication without any additional proxies or firewalls
 - Copying backups between HANA Large Instance units in two different regions to perform system copies or system refreshes


![连接到了不同 Azure 区域中的 Azure 大型实例模具的虚拟网络](./media/hana-overview-architecture/image8-multiple-regions.png)

The figure shows how the different virtual networks in both regions are connected to two different ExpressRoute circuits that are used to connect to SAP HANA on Azure (Large Instances) in both Azure regions (grey lines). Reason for this two cross connections is to protect from an outage of the MSEEs on either side. The communication flow between the two virtual networks in the two Azure regions is supposed to be handled over the [global peering](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) of the two virtual networks in the two different regions (blue dotted line). The thick red line describes the ExpressRoute Global Reach connection, which allows the HANA Large Instance units of your tenants in two different regions to communicate with each other. 

> [!IMPORTANT] 
> 如果使用了多个 ExpressRoute 线路，则应使用“AS 路径前追加”和“本地首选 BGP”设置来确保正确路由流量。

**后续步骤**
- 请参阅 [SAP HANA（大型实例）存储体系结构](hana-storage-architecture.md)
