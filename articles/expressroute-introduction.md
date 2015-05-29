<properties 
   pageTitle="ExpressRoute 简介"
   description="本页提供 ExpressRoute 服务的概述。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="03/31/2015"
   ms.author="cherylmc"/>

# ExpressRoute 技术概述

使用 Microsoft Azure ExpressRoute，可在 Azure 数据中心与你的本地环境或共同租用环境中的基础结构之间创建专用连接。使用 ExpressRoute，你可以在 ExpressRoute 合作伙伴共同租用的设施中建立与 Azure 的连接，也可以直接从现有的 WAN 网络（如网络服务提供商提供的 MPLS VPN）连接到 Azure。使用 ExpressRoute 可以将网络扩展到 Azure 和解锁混合 IT 方案。

与通过 Internet 的典型连接相比，ExpressRoute 连接提供更高的安全性、更多的可靠性、更快的速度和更少的延迟。在某些情况下，使用 ExpressRoute 连接在本地网络和 Azure 之间传输数据还可以产生显著的成本效益。如果你已创建从本地网络到 Azure 的跨界连接，则可以在不改动虚拟网络的情况下迁移到 ExpressRoute 连接。

有关详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。




## ExpressRoute 连接的工作原理

若要连接到 Azure 中托管的 WAN 和服务（计算、存储、媒体服务、网站和其他服务），你必须通过连接提供商订购一条专用线路。有两种类型的连接供你选择：通过交换提供商的第 3 层直接连接，或通过网络服务提供商的第 3 层连接。你可以选择启用其中的一种或两种类型，以通过线路连接到 Azure 订阅。仅当已同时配置了第 3 层直接连接和第 3 层连接时，才可以通过线路连接到所有受支持的 Azure 服务。注意以下事项：



- 如果你要通过 Exchange 提供商位置连接到 Azure，则需要一对物理交叉连接，并需要为每个物理交叉连接配置两个 BGP 会话（一个用于公共对等互连，一个用于私有对等互连），以便获得一个高度可用的链路。交叉连接将会接入交换提供商的云连接基础结构，而不直接连接到 Azure 路由器。
- 如果你通过网络服务提供商连接到 Azure，则网络服务提供商将负责配置所有服务的路由。请与网络服务提供商协作，以正确配置路由。

下图显示了你的基础结构与 Azure 之间的连接的逻辑表示形式。在此图中，线路表示你的网络与主-主配置中配置的 Azure 之间的一对冗余逻辑交叉连接。此线路将划分为 2 条子线路以隔离流量。 

将隔离以下流量：

 - 隔离你的本地与 Azure 计算服务之间的流量。这包括虚拟网络内部署的 Azure 计算服务（即虚拟机 (IaaS)）和云服务 (PaaS)。
 - 隔离你的本地与公共 IP 地址上托管的 Azure 服务之间的流量。可在以下网页中找到支持的服务：[支持的 Azure 服务](expressroute-faqs.md)

![](./media/expressroute-introduction/expressroute-basic.png)


## Exchange 提供商和网络服务提供商
ExpressRoute 提供商划分为网络服务提供商 (NSP) 和 Exchange 提供商 (EXP)。

![](./media/expressroute-introduction/expressroute-nsp-exp.png)


|   |**Exchange 提供商**|**网络服务提供商**|
|---|---|---|
|**典型连接模式**| 点对点以太网链接或通过云交换连接 | 通过电信公司 VPN 的任意对等连接 |
|**支持的带宽**|200 Mbps、500 Mbps、1 Gbps 和 10 Gbps|10 Mbps、50 Mbps、100 Mbps、500 Mbps、1 Gbps|
|**连接提供商**|[Exchange 提供商页面](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_EXP)|[网络服务提供商页面](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_NSP)|
|**路由**|直接使用客户边缘路由器建立 BGP 会话|与电信公司建立 BGP 会话|
|**价格**|[EXP 价格](http://azure.microsoft.com/pricing/details/expressroute/)|[NSP 价格](http://azure.microsoft.com/pricing/details/expressroute/)|

### Exchange 提供商 (EXP)
我们与 Equinix 和 TeleCity 集团等云交换服务提供商，以及 Level 3 等点对点连接服务提供商合作，以帮助在 Azure 与客户本地之间建立连接。我们提供从 200 Mbps 到 10 Gbps（200 Mbps、500 Mbps、1 Gbps 和 10 Gbps）的线路带宽。

如果你想要通过交换提供商建立第 3 层直接连接，可以采用下列 3 种方法之一：

- 可以将 Equinix 等设备放置在我们提供服务的位置。 
- 你可以与当地以太网提供商（例如 Equinix）合作，在本地网络与 Azure 之间设置以太网线路。对于交换提供商的连接，我们将在 Azure 基础结构与连接提供商的基础结构之间配置一对主动-主动交叉连接，以确保连接高度可用并灵活处理故障。
- 可以从当地的城域以太网服务提供商购买租用线路，以连接到能够与 Azure 连接的最近交换提供商设施。

无论你决定采用哪种方式，对于第 3 层直接连接，我们都会在 Azure 基础结构与连接提供商的基础结构之间配置一对主动-主动交叉连接，以确保连接高度可用并灵活处理故障。在满足先决条件后，可以在你的路由器与 Microsoft 路由器之间设置 BGP 会话以交换路由，并在你的网络与 Azure 之间传送流量。

若要了解有关配置的详细信息并查看实际示例，你可以遵循以下分步指南操作：[通过 EXP 配置 ExpressRoute 连接](https://msdn.microsoft.com/library/azure/dn606306.aspx)。


### 网络服务提供商 (NSP)

我们与 AT&T 和 British Telecom 等电信公司合作，以便在 Azure 与你的 WAN 之间建立连接。我们提供从 10 Mbps 到 1 Gbps 的线路带宽。如果你使用了我们一家合作伙伴提供的 VPN 服务，则无需部署任何新硬件或者对现有网络做出重大配置更改，就能将网络扩展到 Azure。

若要了解有关配置的详细信息并查看实际示例，你可以遵循以下分步指南操作：[通过 NSP 配置 ExpressRoute 线路](https://msdn.microsoft.com/library/azure/dn643736.aspx)。

## 后续步骤


- [ExpressRoute 服务提供商和位置](https://msdn.microsoft.com/library/azure/dn957919.aspx)
- [通过网络服务提供商配置 ExpressRoute 连接](https://msdn.microsoft.com/library/azure/dn606309.aspx)
- [通过 Exchange 提供商配置 ExpressRoute 连接](https://msdn.microsoft.com/library/azure/dn606306.aspx)

<!---HONumber=56-->