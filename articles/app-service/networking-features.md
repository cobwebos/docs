---
title: 网络功能
description: 了解 Azure 应用服务中的网络功能，以及需要通过哪些功能来满足网络的安全性或功能需求。
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 03/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: d2b74af723e3ba8b1d71e9f481bf96d009540a52
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962088"
---
# <a name="app-service-networking-features"></a>应用服务网络功能

可通过多种方式部署 Azure 应用服务中的应用程序。 默认情况下，应用服务托管的应用可直接通过 Internet 访问，并且它们只能访问 Internet 托管的终结点。 但是，许多客户应用程序需要控制入站和出站网络流量。 应用服务中的多项功能可以满足这些需求。 难点在于，应该使用哪种功能来解决给定的问题。 本文档旨在帮助客户根据某些示例用例来确定要使用的功能。

Azure 应用服务有两种主要部署类型。 多租户公共服务：在“免费”、“共享”、“基本”、“标准”、“高级”和“高级 v2”定价 SKU 中托管应用服务计划。 单租户应用服务环境 (ASE)：直接在 Azure 虚拟网络 (VNet) 中托管隔离的 SKU 应用服务计划。 所用的功能根据是在多租户服务还是 ASE 中操作而有所不同。 

## <a name="multi-tenant-app-service-networking-features"></a>多租户应用服务网络功能 

Azure 应用服务是一种分布式系统。 处理传入 HTTP/HTTPS 请求的角色称为前端。 托管客户工作负荷的角色称为辅助角色。 应用服务部署中的所有角色均存在于多租户网络中。 由于同一应用服务缩放单元中包含许多不同的客户，因此无法将应用服务网络直接连接到网络。 在不连接网络的情况下，我们需要使用相应的功能来处理应用程序通信的各个方面。 处理向应用发出的请求的功能不可用于解决从应用发出调用时出现的问题。 同理，解决从应用发出的调用的问题的功能，不可用于解决向应用发出请求的问题。  

| 入站功能 | 出站功能 |
|---------------------|-------------------|
| 应用分配的地址 | 混合连接 |
| 访问限制 | 需要网关的 VNet 集成 |
| 服务终结点 | VNet 集成 |

除非另有说明，否则所有功能都可以配合使用。 可以混合使用这些功能来解决各种问题。

## <a name="use-case-and-features"></a>用例和功能

对于任何给定的用例，可通过多种方法来解决问题。  有时，哪种功能合适并不仅仅是由用例本身决定的。 以下入站用例演示了如何使用应用服务网络功能来解决有关控制传入应用的流量的问题。 
 
| 入站用例 | 功能 |
|---------------------|-------------------|
| 支持应用的基于 IP 的 SSL 需求 | 应用分配的地址 |
| 应用的非共享专用入站地址 | 应用分配的地址 |
| 从一组妥善定义的地址限制对应用的访问 | 访问限制 |
| 限制从 VNet 中的资源访问应用 | 服务终结点 </br> ILB ASE </br> 专用终结点 (预览)  |
| 在 VNet 中的专用 IP 上公开我的应用 | ILB ASE </br> 包含服务终结点的应用程序网关上用于入站通信的专用 IP </br> 服务终结点 (预览)  |
| 使用 WAF 保护我的应用 | 应用程序网关 + ILB ASE </br> 包含服务终结点的应用程序网关 </br> 提供访问限制的 Azure Front Door |
| 对发往不同区域中的应用的流量进行负载均衡 | 提供访问限制的 Azure Front Door | 
| 对同一区域中的流量进行负载均衡 | [包含服务终结点的应用程序网关][appgwserviceendpoints] | 

以下出站用例演示如何使用应用服务网络功能来解决应用的出站访问需求。 

| 出站用例 | 功能 |
|---------------------|-------------------|
| 访问位于同一区域的 Azure 虚拟网络中的资源 | VNet 集成 </br> ASE |
| 访问位于不同区域的 Azure 虚拟网络中的资源 | 需要网关的 VNet 集成 </br> ASE 和 VNet 对等互连 |
| 访问通过服务终结点保护的资源 | VNet 集成 </br> ASE |
| 访问未连接到 Azure 的专用网络中的资源 | 混合连接 |
| 跨 ExpressRoute 线路访问资源 | VNet 集成 </br> ASE | 
| 保护来自 Web 应用的出站流量 | VNet 集成和网络安全组 </br> ASE | 
| 路由来自 Web 应用的出站流量 | VNet 集成和路由表 </br> ASE | 


### <a name="default-networking-behavior"></a>默认网络行为

Azure 应用服务缩放单元为每个部署中的多个客户提供支持。 “免费”和“共享”SKU 计划在多租户辅助角色上托管客户工作负荷。 “基本”和更高层的计划仅托管专用于一个应用服务计划 (ASP) 的客户工作负荷。 如果你有“标准”应用服务计划，该计划中的所有应用将在同一个辅助角色上运行。 如果横向扩展辅助角色，将在 ASP 中每个实例的新辅助角色上复制该 ASP 中的所有应用。 用于“高级 v2”的辅助角色不同于其他计划使用的辅助角色。 在每个应用服务部署中，有一个 IP 地址用于发往该应用服务部署中的应用的所有入站流量。 但是，有 4 到 11 个地址可用于发出出站调用。 这些地址由该应用服务部署中的所有应用共享。 出站地址根据不同的辅助角色类型而异。 即，“免费”、“共享”、“基本”、“标准”和“高级”ASP 使用的地址不同于用于从“高级 v2”ASP 发出出站调用的地址。 查看应用的属性时，可以看到应用使用的入站和出站地址。 如果需要锁定与 IP ACL 之间的依赖关系，请使用 possibleOutboundAddresses。 

![应用属性](media/networking-features/app-properties.png)

应用服务包含许多用于管理服务的终结点。  这些地址发布在单独的文档中，同时包含在 AppServiceManagement IP 服务标记中。 AppServiceManagement 标记只能与需要允许此类流量的应用服务环境 (ASE) 配合使用。 将在 AppService IP 服务标记中跟踪应用服务的入站地址。 没有任何 IP 服务标记包含应用服务使用的出站地址。 

![应用服务入站和出站示意图](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>应用分配的地址 

应用分配的地址功能是基于 IP 的 SSL 功能的一个分支，可通过与应用建立 SSL 连接来访问它。 此功能可用于基于 IP 的 SSL 调用，但也可用来为应用分配一个专用的地址。 

![应用分配的地址示意图](media/networking-features/app-assigned-address.png)

使用应用分配的地址时，流量仍会流经相同的前端角色，这些角色处理所有传入应用服务缩放单元的流量。 但是，分配给应用的地址仅供该应用使用。 此功能的用例如下：

* 支持应用的基于 IP 的 SSL 需求
* 为应用设置一个不与其他任何组件共享的专用地址

可以通过有关[在 Azure 应用服务中添加 TLS/SSL 证书][appassignedaddress]的教程，学习如何在应用上设置地址。 

### <a name="access-restrictions"></a>访问限制 

使用访问限制功能可以基于来源 IP 地址筛选入站请求。 筛选操作在前端角色上发生，这些前端角色位于运行应用的辅助角色的上游。 由于前端角色位于辅助角色的上游，因此可将访问限制功能视为应用的网络级保护机制。 使用该功能可以生成按优先顺序评估的允许和拒绝地址块列表。 它类似于 Azure 网络中的网络安全组 (NSG) 功能。  可以在 ASE 或多租户服务中使用此功能。 在 ILB ASE 中使用时，可以限制从专用地址块进行的访问。

![访问限制](media/networking-features/access-restrictions.png)

需要限制可用于访问应用的 IP 地址时，访问限制功能非常有用。 此功能的用例包括：

* 从一组妥善定义的地址限制对应用的访问 
* 限制为通过负载均衡服务（例如 Azure Front Door）进行访问。 若要将入站流量锁定为 Azure Front Door，请创建规则以允许来自 147.243.0.0/16 和 2a01:111:2050::/44 的流量。 

![使用 Front Door 实现访问限制](media/networking-features/access-restrictions-afd.png)

若要锁定对应用的访问，以便只能从 Azure 虚拟网络 (VNet) 中的资源访问它，则当源位于 VNet 中时，需要指定一个静态公共地址。 如果资源没有公共地址，则应改用服务终结点功能。 在[配置访问限制][iprestrictions]教程中了解如何启用此功能。

### <a name="service-endpoints"></a>服务终结点

使用服务终结点可以锁定对应用的**入站**访问，使源地址必须来自所选的一组子网。 此功能可与 IP 访问限制结合使用。 服务终结点在与 IP 访问限制相同的用户体验中设置。 可以生成访问规则的允许/拒绝列表，其中包括公共地址以及 VNet 中的子网。 此功能支持如下方案：

![服务终结点](media/networking-features/service-endpoints.png)

* 设置包含你的应用的应用程序网关，以锁定发往该应用的入站流量
* 将对应用的访问权限限制为 VNet 中的资源。 这可能包括 VM、ASE，甚至其他使用 VNet 集成的应用 

![包含应用程序网关的服务终结点](media/networking-features/service-endpoints-appgw.png)

可以在[配置服务终结点访问限制][serviceendpoints]教程中详细了解如何配置包含你的应用的服务终结点

### <a name="private-endpoint-preview"></a>专用终结点 (预览) 

专用终结点是一个网络接口，可通过 Azure 专用链接将你私下安全地连接到你的 Web 应用。 专用终结点使用 VNet 中的专用 IP 地址，从而有效地将 Web 应用引入 VNet。 此功能仅适用于 **入站** 流到 Web 应用。
[为 Azure Web 应用使用专用终结点（预览版）][privateendpoints]
 
### <a name="hybrid-connections"></a>混合连接

应用可以通过应用服务混合连接向指定的 TCP 终结点发出**出站**调用。 终结点可以位于本地、VNet 中，或者允许通过端口 443 向 Azure 发出出站流量的任何位置。 该功能要求在 Windows Server 2012 或更高版本的主机上安装名为“混合连接管理器”(HCM) 的中继代理。 HCM 需要能够通过端口 443 访问 Azure 中继。 可以通过门户中的应用服务混合连接 UI 下载 HCM。 

![混合连接网络流](media/networking-features/hybrid-connections.png)

应用服务混合连接功能构建在 Azure 中继混合连接功能的基础之上。 应用服务使用一种特殊形式的功能，该功能仅支持从应用向 TCP 主机和端口发出出站调用。 只需在安装 HCM 的主机上解析此主机和端口。 当应用服务中的应用在混合连接中定义的主机和端口上执行 DNS 查找时，流量将自动重定向，以流经混合连接并传出混合连接管理器。 若要详细了解混合连接，请参阅有关[应用服务混合连接][hybridconn]的文档

此功能通常用于：

* 通过 VPN 或 ExpressRoute 访问未连接到 Azure 的专用网络中的资源
* 支持将本地应用直接迁移到应用服务，而无需同时移动支持数据库  
* 根据混合连接配置安全提供对单个主机和端口的访问。 大多数网络功能都开放了对网络的访问；使用混合连接时，只能访问单个主机和端口。
* 实现其他出站连接方法无法实现的方案
* 在应用服务中进行开发，其中的应用可以轻松利用本地资源 

由于使用此功能可以访问本地资源且无需在入站防火墙中开放额外的端口，因此它非常受开发人员的青睐。 其他出站应用服务网络功能与 Azure 虚拟网络密切相关。 混合连接不依赖于遍历 VNet，可用于满足更广泛的网络需求。 必须注意的是，应用服务混合连接功能不考虑，也不知道在其上执行的操作。 也就是说，可以使用它来访问数据库、Web 服务或大型机上的任意 TCP 套接字。 此功能在本质上是通过隧道传输 TCP 数据包。 

混合连接不仅在开发活动中非常流行，而且可以在众多的生产应用程序中使用。 它非常适合用于访问 Web 服务或数据库，但不适合在涉及到创建许多连接的场合中使用。 

### <a name="gateway-required-vnet-integration"></a>需要网关的 VNet 集成 

应用可以通过网关所需的应用服务 VNet 集成功能向 Azure 虚拟网络发出**出站**请求。 该功能的工作原理是通过点到站点 VPN 将运行应用的主机连接到 VNet 中的虚拟网络网关。 配置该功能时，应用将获取分配给每个实例的点到站点地址之一。 使用此功能可以访问位于任何区域的经典或资源管理器 VNet 中的资源。 

![需要网关的 VNet 集成](media/networking-features/gw-vnet-integration.png)

此功能解决了访问其他 VNet 中的资源的问题，并且可用于通过 VNet 连接到其他 VNet 甚至本地。 它不适用于 ExpressRoute 连接的 VNet，但确实适用于站点到站点 VPN 连接的网络。 一般情况下，不适合从应用服务环境 (ASE) 中的应用使用此功能，因为 ASE 已在 VNet 中。 此功能解决的用例如下：

* 访问 Azure 虚拟网络中专用 IP 上的资源 
* 访问本地资源（如果存在站点到站点 VPN） 
* 访问对等互连 VNet 中的资源 

启用此功能后，应用将使用配置了目标 VNet 的 DNS 服务器。 可在有关[应用服务 VNet 集成][vnetintegrationp2s]的文档中详细了解此功能。 

### <a name="vnet-integration"></a>VNet 集成

网关所需的 VNet 集成功能非常有用，但仍不能解决跨 ExpressRoute 访问资源的问题。 在需要跨 ExpressRoute 连接进行访问的情况下，应用需要能够调用服务终结点保护的服务。 为了同时解决这两个附加需求，我们添加了另一项 VNet 集成功能。 借助新的 VNet 集成功能，可将应用的后端放置在位于同一区域的资源管理器 VNet 中的子网内。 无法从已经位于 VNet 中的应用服务环境使用此功能。 使用此功能可以：

* 访问位于同一区域的资源管理器 VNet 中的资源
* 访问通过服务终结点保护的资源 
* 访问可跨 ExpressRoute 或 VPN 连接访问的资源
* 保护所有出站流量 
* 强制隧道所有出站流量。 

![VNet 集成](media/networking-features/vnet-integration.png)

若要详细了解此功能，请阅读有关[应用服务 VNet 集成][vnetintegration]的文档。

## <a name="app-service-environment"></a>应用服务环境 

应用服务环境 (ASE) 是在 VNet 中运行的 Azure 应用服务的单租户部署。 ASE 可以实现如下用例：

* 访问 VNet 中的资源
* 跨 ExpressRoute 访问资源
* 使用 VNet 中的专用地址公开应用 
* 跨服务终结点访问资源 

使用 ASE 时，无需使用 VNet 集成或服务终结点等功能，因为 ASE 已在 VNet 中。 若要通过服务终结点访问 SQL 或存储等资源，请在 ASE 子网中启用服务终结点。 若要访问 VNet 中的资源，无需进行额外的配置。  跨 ExpressRoute 访问资源时，你已位于 VNet 中，因此无需在 ASE 或其包含的应用中进行任何配置。 

由于可以在专用 IP 地址上公开 ILB ASE 中的应用，因此，可以轻松添加 WAF 设备，以便只在 Internet 中公开所需的应用，并使剩余的应用保持安全。 这有助于轻松开发多层应用程序。 

还有一些功能无法通过 ASE 中的多租户服务来实现。 这些功能包括：

* 在专用 IP 地址上公开应用
* 使用应用不提供的网络控制来保护所有出站流量 
* 在单租户服务中托管应用 
* 扩展到多租户服务无法支持的更多实例 
* 加载专用 CA 客户端证书，供应用通过专用 CA 保护的终结点使用 
* 在无法在应用级别禁用的情况下，跨系统中托管的所有应用强制实施 TLS 1.1 
* 为不与任何客户共享的 ASE 中的所有应用提供专用出站地址 

![VNet 中的 ASE](media/networking-features/app-service-environment.png)

ASE 提供最佳的隔离和专用应用托管，但也附带了一些管理难题。 在使用正常运行的 ASE 之前需要考虑的事项包括：
 
 * ASE 在 VNet 中运行，但在 VNet 外部具有依赖项。 必须允许这些依赖项。 在[应用服务环境的网络注意事项][networkinfo]中了解详细信息
 * ASE 无法像多租户服务那样即时缩放。 需要预测缩放需求，而不要被动地进行缩放。 
 * ASE 具有较高的前期成本。 若要充分利用 ASE，应该计划将多个工作负荷放入一个 ASE，而不要用它来完成一些微不足道的工作
 * ASE 中的应用无法限制对 ASE 中某些应用的访问，但可以限制对其他应用的访问。
 * ASE 位于子网中，任何网络规则将应用到传入和传出该 ASE 的所有流量。 如果只想要为一个应用分配入站流量规则，请使用访问限制。 

## <a name="combining-features"></a>组合功能 

可以结合使用多租户服务的所述功能来解决更复杂的用例。 此处描述了两个较常见用例，但它们只是示例。 了解每项功能的作用后，便几乎可以解决所有的系统体系结构需求。

### <a name="inject-app-into-a-vnet"></a>将应用注入 VNet

一个常见的请求是如何在 VNet 中放置应用。 将应用放入 VNet 意味着应用的入站和出站终结点都位于 VNet 中。 ASE 提供最佳的解决方案来解决此问题，但是，组合功能可以在多租户服务中实现大部分需求。 例如，可通过以下方式使用专用入站和出站地址托管仅限 Intranet 的应用程序：

* 使用专用入站和出站地址创建应用程序网关
* 使用服务终结点保护应用的入站流量 
* 使用新的 VNet 集成，使应用后端位于 VNet 中 

此部署样式不会针对发往 Internet 的出站流量提供专用地址，也无法让你锁定来自应用的所有出站流量。  此部署样式主要实现只能通过 ASE 实现的目的。 

### <a name="create-multi-tier-applications"></a>创建多层应用程序

多层应用程序是只能从前端层访问其中的 API 后端应用的应用程序。 若要创建多层应用程序，可以：

* 使用 VNet 集成将前端 Web 应用的后端连接到 VNet 中的子网
* 使用服务终结点保护发往 API 应用的入站流量，以便只允许来自前端 Web 应用所用子网的流量

![多层应用](media/networking-features/multi-tier-app.png)

可以在其他前端应用中使用 VNet 集成，并在 API 应用及其子网中使用服务终结点，让多个前端应用使用同一个 API 应用。  

<!--Links-->
[appassignedaddress]: ./configure-ssl-certificate.md
[iprestrictions]: ./app-service-ip-restrictions.md
[serviceendpoints]: ./app-service-ip-restrictions.md
[hybridconn]: ./app-service-hybrid-connections.md
[vnetintegrationp2s]: ./web-sites-integrate-with-vnet.md
[vnetintegration]: ./web-sites-integrate-with-vnet.md
[networkinfo]: ./environment/network-info.md
[appgwserviceendpoints]: ./networking/app-gateway-with-service-endpoints.md
[privateendpoints]: ./networking/private-endpoint.md