---
title: 网络部署功能-Azure 应用服务 |Microsoft Docs
description: 了解如何使用各种网络功能的应用服务
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 666430a11fb95871eb601b2a38eb7b97ad16119f
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66498944"
---
# <a name="app-service-networking-features"></a>应用服务网络功能

在 Azure 应用服务中的应用程序可以通过多种方式部署。 默认情况下，应用服务托管的应用程序是直接访问 internet，并且只能访问托管的 internet 的终结点。 但是，很多客户应用程序需要控制入站和出站网络流量。 要满足这些需求的应用服务中有多个功能。 面临的挑战要了解应使用哪项功能来解决给定的问题。 本文档旨在帮助客户确定应使用哪项功能，基于一些示例用例。

有两种主要的部署类型的 Azure 应用服务。 没有托管应用服务计划免费、 共享、 基本、 标准、 高级版和高级 v2 中的多租户公共服务定价 Sku。 然后是单租户应用服务 Environment(ASE)，承载直接在 Azure 虚拟网络 (VNet) 中的独立 SKU 应用服务计划。 如果你是多租户服务中或在 ASE 中，你使用的功能将在会有所不同。 

## <a name="multi-tenant-app-service-networking-features"></a>多租户应用服务网络功能 

Azure 应用服务是一个分布式的系统。 处理传入的 HTTP/HTTPS 请求的角色称为前端。 托管客户工作负荷的角色称为辅助角色。 应用服务部署中角色的所有存在于多租户网络。 因为同一应用服务缩放单位中有许多不同的客户，你不能在应用服务网络直接连接到你的网络。 而不是连接网络，我们需要功能来处理应用程序通信的不同方面。 处理向您的应用程序发出请求的功能不能用于解决问题时从您的应用程序进行调用。 同样，解决问题来自您的应用程序的调用的功能不能用于解决您的应用程序的问题。  

| 入站的功能 | 出站的功能 |
|---------------------|-------------------|
| 应用分配的地址 | 混合连接 |
| 访问限制 | 网关所需的 VNet 集成 |
| 服务终结点 | VNet 集成 （预览版） |

除非另行说明，可以一起使用的所有功能。 可以混合使用的功能来解决各种问题。

## <a name="use-case-and-features"></a>用例和功能

对于任何给定的用例，可以有几种方法来解决问题。  若要使用所需的功能有时是由于只需用例本身之外的原因。 以下的入站的用例建议如何使用应用服务网络功能来解决控制流量转到您的应用程序的问题。 
 
| 入站的用例 | Feature |
|---------------------|-------------------|
| 为你的应用需要基于 IP 的 SSL 支持 | 应用分配的地址 |
| 不共享，您的应用程序的专用入站地址 | 应用分配的地址 |
| 访问限于您的应用程序从一组定义完善的地址 | 访问限制 |
| 公开我在 VNet 中的专用 Ip 上的应用程序 | ILB ASE </br> 使用服务终结点的应用程序网关 |
| 从 VNet 中的资源限制对我的应用程序的访问 | 服务终结点 </br> ILB ASE |
| 公开我在 VNet 中的专用 IP 上的应用程序 | ILB ASE </br> 专用 IP 上使用服务终结点应用程序网关的入站 |
| 保护我使用 WAF 的应用程序 | 应用程序网关 + ILB ASE </br> 使用服务终结点的应用程序网关 </br> 具有访问限制的 azure 第一道防线 |
| 我的应用在不同区域中对传入流量负载均衡 | 具有访问限制的 azure 第一道防线 | 
| 在同一区域中流量进行负载平衡 | 使用服务终结点的应用程序网关 | 

在以下出站的用例建议如何使用网络功能的应用服务以解决您的应用程序的出站访问权限需求。 

| 出站的用例 | Feature |
|---------------------|-------------------|
| 在同一区域中的 Azure 虚拟网络中访问资源 | VNet 集成 </br> ASE |
| 访问不同的区域中的 Azure 虚拟网络中的资源 | 网关所需的 VNet 集成 </br> ASE 和 VNet 对等互连 |
| 使用服务终结点保护的访问资源 | VNet 集成 </br> ASE |
| 在未连接到 Azure 的专用网络中访问资源 | 混合连接 |
| 跨 ExpressRoute 线路访问资源 | （仅限于 RFC 1918 地址现在） 的 VNet 集成 </br> ASE | 


### <a name="default-networking-behavior"></a>默认网络行为

Azure 应用服务缩放单位在每个部署中支持很多客户。 免费和共享的 SKU 计划托管多租户辅助进程上的客户工作负荷。 基本，及更高版本计划主机客户工作负荷专用于只有一个应用服务计划 (ASP)。 如果您有一个标准应用服务计划，该计划中的应用的所有将运行在同一个辅助角色上。 如果横向扩展辅助角色时，然后在 ASP 中每个实例在新辅助角色上将复制下列所有该 ASP 中的应用。 辅助角色用于高级 v2 是不同于使用其他计划的辅助进程。 每个应用服务部署都有一个用于所有入站流量发往该应用服务部署中的 IP 地址。 有但是任何位置从 4 到 11 进行出站调用使用的地址。 这些地址可由所有应用都共享该应用服务部署中。 出站地址是不同的基于不同的工作类型。 这意味着，使用免费、 共享、 基本、 标准和高级 Asp 的地址不同于用于出站调用从 Premiumv2 Asp 的地址。 如果您查看的属性中为你的应用，可以看到您的应用程序使用的入站和出站地址。 如果你需要锁定 IP ACL 的依赖项，使用 possibleOutboundAddresses。 

![应用程序属性](media/networking-features/app-properties.png)

应用服务提供的用于管理服务的终结点的数量。  这些地址在一个单独的文档中发布，也要 AppServiceManagement IP 服务标记中。 AppServiceManagement 标记仅用于与应用服务环境 (ASE) 需要允许此类流量。 应用服务中的应用服务 IP 服务标记跟踪的入站的地址。 没有任何 IP 服务标记，包含由应用服务的出站地址。 

![应用服务入站和出站关系图](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>应用分配的地址 

应用程序分配的地址功能是基于 IP 的 SSL 功能的一个分支，并通过使用你的应用设置 SSL 访问。 此功能可用于基于 IP 的 SSL 调用，但它还可用于为您的应用程序提供它仅具有一个地址。 

![应用分配的地址关系图](media/networking-features/app-assigned-address.png)

时使用应用分配的地址，你的流量，仍会通过相同的前端角色到应用服务缩放单位处理的所有传入流量。 但是，分配给您的应用程序的地址仅供您的应用程序。 此功能的用例是：

* 为你的应用需要基于 IP 的 SSL 支持
* 设置应用程序不与任何其他内容共享的专用的地址

您可以了解如何在设置本教程使用在应用程序上的地址[基于配置的 IP 的 SSL][appassignedaddress]。 

### <a name="access-restrictions"></a>访问限制 

访问限制功能允许你筛选**入站**根据源 IP 地址的请求。 筛选操作都的发生在从辅助角色将回滚上游的前端角色运行您的应用程序。 前端角色是辅助角色从上游，由于访问限制功能可被视作用于您的应用程序的网络级保护。 该功能可用于构建一组允许和拒绝按优先级顺序进行计算的地址块。 它将类似于在 Azure 网络中存在的网络安全组 (NSG) 功能。  在 ASE 中还是在多租户服务，可以使用此功能。 当用于 ILB ASE，可以从专用地址块限制访问。

![访问限制](media/networking-features/access-restrictions.png)

访问限制功能可帮助在你想要限制可用于访问该应用的 IP 地址的情况下。 使用此功能的情况下如下：

* 访问限于您的应用程序从一组定义完善的地址 
* 限制对经过负载平衡服务，如 Azure 第一道防线访问。 如果您希望锁定您的入站通信到 Azure 第一道防线，创建规则以允许流量从 147.243.0.0/16 和 2a01:111:2050:: / 44。 

![与第一道防线的访问限制](media/networking-features/access-restrictions-afd.png)

如果你想要访问锁定到您的应用程序，以便仅可以从 Azure 虚拟网络 (VNet) 中的资源访问它，则需要您的源是在 VNet 中的静态公共地址。 如果资源不具有公共地址，应改为使用服务终结点功能。 了解如何在启用此功能的教程[配置访问限制][iprestrictions]。

### <a name="service-endpoints"></a>服务终结点

服务终结点使您可以向下锁定**入站**这样的源地址必须来自你选择的子网的一组访问你的应用。 此功能结合使用的 IP 访问限制。 IP 访问限制为相同的用户体验中设置服务终结点。 您可以构建的允许/拒绝的访问规则的列表，包括公用地址，以及子网在 Vnet 中。 此功能支持如下方案：

![服务终结点](media/networking-features/service-endpoints.png)

* 设置你的应用与应用程序网关来锁定你的应用的入站流量
* 制约访问到 VNet 中的资源应用。 这可能包括 Vm、 Ase 或使用 VNet 集成的甚至是其他应用 

![应用程序网关的服务终结点](media/networking-features/service-endpoints-appgw.png)

您可以进一步了解在本教程中配置服务终结点与你的应用[配置服务终结点访问限制][serviceendpoints]
 
### <a name="hybrid-connections"></a>混合连接

应用服务混合连接使你的应用以使**出站**对指定的 TCP 终结点的调用。 终结点可以在本地，在 VNet 中或任意位置，允许到端口 443 上 Azure 的出站流量。 功能要求安装中继代理称为混合连接管理器 (HCM) Windows Server 2012 或更高版本主机上。 HCM 需要能够访问端口 443 上的 Azure 中继。 可以从应用服务混合连接用户界面在门户中下载此 HCM。 

![混合连接网络流](media/networking-features/hybrid-connections.png)

应用服务混合连接功能是基于 Azure 中继混合连接功能。 应用服务使用的功能仅支持进行从您的应用程序到 TCP 主机和端口的出站调用的一种专用的形式。 此主机和端口，只需安装 HCM 的主机上解析。 当应用服务中的应用程序执行的主机和混合连接中定义的端口上进行的 DNS 查找时，流量将自动重定向转通过混合连接和 out 混合连接管理器。 若要了解有关混合连接的详细信息，请阅读的文档上[应用服务混合连接][hybridconn]

此功能通常用于：

* 在未连接到 Azure 中使用 VPN 或 ExpressRoute 的专用网络中访问资源
* 支持提升和转变的对本地应用程序到应用服务而无需还将支持的数据库  
* 安全地提供对单个主机和每个混合连接的端口的访问。 大多数网络功能打开对网络进行访问，并与混合连接只有单个主机和可访问的端口。
* 包含不受其他出站连接方法的方案
* 应用可以轻松地利用本地资源的应用服务中执行开发 

功能，而无需入站的防火墙孔的本地资源的访问权限，因为它是常用与开发人员。 其他出站应用服务网络功能是非常 Azure 虚拟网络相关。 混合连接不具有依赖于通过 VNet，可用于更广泛的网络需求。 请务必注意应用服务混合连接功能不会关心或知道你在其上执行的操作。 也就是说，可以使用它来访问数据库、 web 服务或在大型机上的任意 TCP 套接字。 该功能实质上是隧道 TCP 数据包。 

常用于开发混合连接时，它还可在大量生产应用程序。 它非常适合用于访问 web 服务或数据库，但不是适用于以下情况涉及创建出色的多个连接。 

### <a name="gateway-required-vnet-integration"></a>网关所需的 VNet 集成 

网关所需应用服务 VNet 集成功能让你的应用以使**出站**到 Azure 虚拟网络的请求。 该功能通过连接您的应用程序运行到通过点到站点 VPN 在 VNet 上的虚拟网络网关的主机的工作。 当您配置的功能时，您的应用程序获取分配给每个实例的点到站点地址之一。 此功能，可访问经典或资源管理器 Vnet 中的任何区域中的资源。 

![网关所需的 VNet 集成](media/networking-features/gw-vnet-integration.png)

此功能可以解决访问其他 Vnet 中的资源的问题并甚至可用于通过 VNet 到其他 Vnet 或甚至在本地连接。 它并不适用于 ExpressRoute 连接 Vnet 但不是会使用站点到站点 VPN 连接网络。 是通常情况下也不适合用于从应用程序的此功能在应用服务环境 (ASE)，因为 ASE 已在你的 VNet。 此功能可解决的使用情形是：

* 访问 Azure 虚拟网络中的专用 Ip 上的资源 
* 访问资源的本地是否存在站点到站点 VPN 
* 访问对等 Vnet 中的资源 

启用此功能后，您的应用程序将使用目标 VNet 配置了 DNS 服务器。 你可以阅读更多有关此功能上的文档中[应用服务的 VNet 集成][vnetintegrationp2s]。 

### <a name="vnet-integration"></a>VNet 集成

网关所需 VNet 集成功能非常有用，但仍不能跨 ExpressRoute 解决访问资源。 基于无需通过 ExpressRoute 连接访问，没有需要应用程序要能够对服务终结点保护的服务进行调用。 若要解决这两个这些其他的需求，已添加另一个 VNet 集成功能。 新的 VNet 集成功能，可将您的应用程序的后端放在同一区域中的资源管理器 VNet 中的子网。 此功能不可用从应用服务环境，已在 VNet 中。 此功能支持：

* 访问同一区域中的资源管理器 Vnet 中的资源
* 访问服务终结点进行保护的资源 
* 访问通过 ExpressRoute 或 VPN 连接进行访问的资源

![VNet 集成](media/networking-features/vnet-integration.png)

此功能处于预览状态，因此不应为生产工作负荷。 若要了解有关此功能的详细信息，请阅读文档上[应用服务的 VNet 集成][vnetintegration]。

## <a name="app-service-environment"></a>应用服务环境 

应用服务环境 (ASE) 是在 VNet 中运行的 Azure 应用服务的单个租户部署。 ASE 可实现用例，例如：

* 访问 VNet 中的资源
* 跨 ExpressRoute 访问资源
* 公开你的应用与 VNet 中的专用地址 
* 在服务终结点之间的访问资源 

使用 ASE 时，不需要使用 VNet 集成或服务终结点等功能，因为 ASE 已在你的 VNet。 如果你想要访问资源，如 SQL 或存储服务终结点，启用服务终结点在 ASE 子网。 如果你想要访问 VNet 中的资源，则不需要其他配置。  如果你想要跨 ExpressRoute 访问的资源，则你已经在 VNet 中并且无需进行任何配置 ASE 或其内部的应用上。 

在 ILB ASE 中应用可以在专用 IP 地址上公开，因为可以轻松添加 WAF 设备公开您想到 internet，保留剩余安全应用。 它有助于轻松地开发多层应用程序。 

有尚不能通过多租户服务从 ase 发出的一些事项。 这些包括诸如：

* 公开你的应用上的专用 IP 地址
* 安全网络控制，同时不属于您的应用程序的所有出站网络流量 
* 托管在单个租户服务应用 
* 纵向扩展到更多实例不是在多租户服务，可以 
* 加载私有 CA 客户端证书，以供你的应用与专用 CA 保护终结点 
* 强制所有而无需在应用级别禁用任何功能在系统中托管的应用中的 TLS 1.1 
* 为所有不与任何客户共享在 ASE 中应用程序提供专用的出站地址 

![ASE 在 VNet 中](media/networking-features/app-service-environment.png)

ASE 提供隔离的专用应用程序托管最佳的情景，但却带来了一些管理方面的难题。 某些使用 ASE 操作之前，请考虑以下事项：
 
 * ASE 在 VNet 中运行，但具有 VNet 外部的依赖项。 必须允许这些依赖项。 更多信息，请参阅[应用服务环境的网络注意事项][networkinfo]
 * 与多租户服务一样不立即缩放 ASE。 您需要以应对预期的缩放需求而不是被动缩放。 
 * ASE 具有更高提前与之关联的成本。 若要充分利用你的 ASE，您应计划将许多工作负载放入一个 ASE 而不是将其用于小型工作
 * 在 ASE 中应用不能限制对 ASE，而不是其他一些应用的访问。
 * ASE 子网中，任何网络规则适用于传入和传出该 ASE 的所有流量。 如果你想要分配一个应用的入站的流量规则，使用访问限制。 

## <a name="combining-features"></a>组合功能 

记下的多租户服务的功能可以一起使用，以解决更复杂的用例。 两个更常见的用例此处所述，但它们是只是示例。 通过了解各种功能的作用，您可以解决几乎所有的系统体系结构需求。

### <a name="inject-app-into-a-vnet"></a>将应用程序注入到 VNet

一个常见的请求是有关如何将您的应用程序放在 VNet 中。 将您的应用程序放入 VNet 意味着应用的入站和出站终结点处于 VNet 内部的。 ASE 可提供最佳的解决方案来解决此问题，但可以获得所需的内容与多租户服务中通过合并功能的大部分。 例如，可以托管 intranet 与通过专用的入站和出站地址的唯一应用程序：

* 使用专用的入站和出站地址创建应用程序网关
* 保护发往应用程序与服务终结点的入站的流量 
* 使用新的 VNet 集成，因此在 VNet 中为您的应用程序的后端 

此部署样式不会到 internet 的出站流量为您提供一个专用的地址，或使你能够从您的应用程序的所有出站流量锁定。  此部署样式将为您提供结果将仅否则就使用 ASE 时有很的大。 

### <a name="create-multi-tier-applications"></a>创建多层应用程序

多层应用程序是一个应用程序，其中 API 后端应用只能访问从前端层。 若要创建多层应用程序，你可以：

* 使用 VNet 集成连接与 VNet 中子网的前端 web 应用的后端
* 使用服务终结点保护仅来自前端 web 应用使用的子网到 API 应用的入站的流量

![多层应用程序](media/networking-features/multi-tier-app.png)

您可以使用 VNet 集成的其他前端应用和 API 应用程序与它们的子网中的服务终结点使用相同的 API 应用的多个前端应用程序。  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl#bind-your-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
