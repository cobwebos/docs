---
title: 网络部署功能-Azure App Service |Microsoft Docs
description: 如何使用各种应用服务网络功能
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 950818d08cb654bad969deaede24231cab9bcbe2
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098558"
---
# <a name="app-service-networking-features"></a>应用服务网络功能

可以通过多种方式部署 Azure App Service 中的应用程序。 默认情况下, 应用服务托管的应用可直接访问 internet, 只能访问 internet 托管的终结点。 但许多客户应用程序都需要控制入站和出站网络流量。 应用服务中有几个功能可满足这些需求。 难点在于知道应该使用哪种功能来解决给定的问题。 本文档旨在帮助客户根据某些示例用例来确定应使用哪些功能。

Azure App Service 有两种主要的部署类型。 多租户公共服务提供了免费、共享、基本、标准、高级和 Premiumv2 定价 Sku 中的应用服务计划。 然后是单租户应用服务环境 (ASE), 它直接在 Azure 虚拟网络 (VNet) 中托管隔离的 SKU 应用服务计划。 如果你使用的是多租户服务或 ASE 中的, 则使用的功能会有所不同。 

## <a name="multi-tenant-app-service-networking-features"></a>多租户应用服务网络功能 

Azure App Service 是一种分布式系统。 处理传入 HTTP/HTTPS 请求的角色称为前端。 承载客户工作负荷的角色称为辅助角色。 应用服务部署中的所有角色均存在于多租户网络中。 由于同一应用服务扩展单元中有许多不同的客户, 因此无法将应用服务网络直接连接到网络。 我们需要一些功能来处理应用程序通信的不同方面, 而不是连接网络。 处理应用程序的请求时, 处理对应用程序的请求的功能不能解决问题。 同样, 不能使用解决应用中的调用问题的功能来解决应用程序的问题。  

| 入站功能 | 出站功能 |
|---------------------|-------------------|
| 应用分配的地址 | 混合连接 |
| 访问限制 | 网关所需的 VNet 集成 |
| 服务终结点 | VNet 集成 (预览版) |

除非另有说明, 否则所有功能都可以一起使用。 可以混合使用这些功能来解决各种问题。

## <a name="use-case-and-features"></a>用例和功能

对于任何给定用例, 可以通过几种方法解决此问题。  使用正确的功能有时是由于仅用例本身之外的原因。 下面的入站用例建议如何使用应用服务网络功能来解决有关控制传入应用的流量的问题。 
 
| 入站用例 | 功能 |
|---------------------|-------------------|
| 支持应用的基于 IP 的 SSL 需求 | 应用分配的地址 |
| 不共享、应用的专用入站地址 | 应用分配的地址 |
| 从一组定义完善的地址限制对应用的访问 | 访问限制 |
| 在 VNet 中的专用 Ip 上公开我的应用 | ILB ASE </br> 具有服务终结点的应用程序网关 |
| 限制从 VNet 中的资源对应用的访问 | 服务终结点 </br> ILB ASE |
| 在 VNet 中的专用 IP 上公开我的应用 | ILB ASE </br> 使用服务终结点的应用程序网关上的入站专用 IP |
| 使用 WAF 保护我的应用程序 | 应用程序网关 + ILB ASE </br> 具有服务终结点的应用程序网关 </br> 具有访问限制的 Azure 前门 |
| 对不同区域中的应用的流量进行负载均衡 | 具有访问限制的 Azure 前门 | 
| 在同一区域对流量进行负载均衡 | 具有服务终结点的应用程序网关 | 

以下出站用例建议如何使用应用服务网络功能来解决应用的出站访问需求。 

| 出站用例 | 功能 |
|---------------------|-------------------|
| 在同一区域中访问 Azure 虚拟网络流入量中的资源 | VNet 集成 </br> ASE |
| 网络流入量不同的区域访问 Azure 虚拟中的资源 | 网关所需的 VNet 集成 </br> ASE 和 VNet 对等互连 |
| 访问通过服务终结点保护的资源 | VNet 集成 </br> ASE |
| 访问未连接到 Azure 的专用网络中的资源 | 混合连接 |
| 跨 ExpressRoute 线路访问资源 | VNet 集成 (目前限制为 RFC 1918 地址) </br> ASE | 


### <a name="default-networking-behavior"></a>默认网络行为

Azure App Service 扩展单元支持每个部署中的多个客户。 免费和共享 SKU 计划在多租户辅助角色上托管客户工作负荷。 基本和更高计划仅托管专用于一个应用服务计划 (ASP) 的客户工作负荷。 如果你有标准的应用服务计划, 则该计划中的所有应用都将在同一辅助角色上运行。 如果横向扩展辅助角色, 则将在 ASP 中的每个实例的新辅助角色上复制该 ASP 中的所有应用。 用于 Premiumv2 的辅助角色不同于用于其他计划的工作线程。 每个应用服务部署都有一个 IP 地址, 该地址用于发送到该应用服务部署中的应用的所有入站流量。 但在任何位置均可用于进行出站调用。 这些地址由该应用服务部署中的所有应用共享。 根据不同的辅助角色类型, 出站地址有所不同。 这意味着, 免费、共享、基本、标准和高级 Asp 使用的地址不同于 Premiumv2 Asp 中用于出站调用的地址。 如果查看应用的属性, 可以看到应用使用的入站和出站地址。 如果需要锁定与 IP ACL 的依赖项, 请使用 possibleOutboundAddresses。 

![应用属性](media/networking-features/app-properties.png)

应用服务有多个用于管理服务的终结点。  这些地址发布在单独的文档中, 并且也在 AppServiceManagement IP 服务标记中。 AppServiceManagement 标记仅与需要允许此类流量的应用服务环境 (ASE) 一起使用。 在 AppService IP 服务标记中跟踪应用服务的入站地址。 没有 IP 服务标记包含应用服务使用的出站地址。 

![应用服务入站和出站关系图](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>应用分配的地址 

应用分配的地址功能是基于 IP 的 SSL 功能的分支, 可通过与应用建立 SSL 来进行访问。 此功能可用于基于 IP 的 SSL 呼叫, 但也可用于为应用程序提供一个仅具有的地址。 

![应用分配的地址关系图](media/networking-features/app-assigned-address.png)

使用应用分配的地址时, 流量仍会经历相同的前端角色, 这些角色将处理所有传入的流量到应用服务缩放单位。 但是, 分配给你的应用程序的地址仅供你的应用程序使用。 此功能的用例如下:

* 支持应用的基于 IP 的 SSL 需求
* 为你的应用设置一个不与其他内容共享的专用地址

有关[配置基于 IP 的 SSL][appassignedaddress]的教程, 可以了解如何在应用中设置地址。 

### <a name="access-restrictions"></a>访问限制 

访问限制功能使你可以基于源 IP 地址筛选**入站**请求。 筛选操作在运行应用程序的工作进程中上游的前端角色上发生。 由于前端角色与工作人员上游, 因此可将访问限制功能视为适用于你的应用的网络级别保护。 此功能允许你构建按优先级顺序计算的允许和拒绝地址块的列表。 它类似于 Azure 网络中存在的网络安全组 (NSG) 功能。  可以在 ASE 或多租户服务中使用此功能。 与 ILB ASE 一起使用时, 你可以限制从专用地址块进行访问。

![访问限制](media/networking-features/access-restrictions.png)

访问限制功能有助于限制可用于访问应用的 IP 地址的情况。 此功能的用例包括:

* 从一组定义完善的地址限制对应用的访问 
* 通过负载均衡服务 (如 Azure 前门) 限制访问。 如果要将入站流量锁定到 Azure 前门, 请创建规则以允许来自 147.243.0.0/16 和 2a01: 111::/44 的流量。 

![使用前门访问限制](media/networking-features/access-restrictions-afd.png)

如果你想要锁定对应用的访问, 以便只能从 Azure 虚拟网络 (VNet) 中的资源访问它, 则你的 VNet 中的任何源都需要一个静态公共地址。 如果资源没有公共地址, 则应改为使用 "服务终结点" 功能。 了解如何在[配置访问限制][iprestrictions]的教程中启用此功能。

### <a name="service-endpoints"></a>服务终结点

使用服务终结点可以锁定对应用程序的**入站**访问, 以使源地址必须来自所选的一组子网。 此功能与 IP 访问限制一起工作。 服务终结点的设置与 IP 访问限制相同。 可以在 Vnet 中构建包含公用地址和子网的访问规则的允许/拒绝列表。 此功能支持以下方案:

![服务终结点](media/networking-features/service-endpoints.png)

* 使用应用程序设置应用程序网关, 将入站流量锁定到你的应用
* 将对应用程序的访问权限限制到 VNet 中的资源。 这可能包括 Vm、Ase 甚至其他使用 VNet 集成的应用 

![应用程序网关的服务终结点](media/networking-features/service-endpoints-appgw.png)

可以在[配置服务终结点访问限制][serviceendpoints]教程中了解有关使用应用配置服务终结点的详细信息
 
### <a name="hybrid-connections"></a>混合连接

应用服务混合连接允许应用对指定的 TCP 终结点进行**出站**调用。 终结点可以在本地、在 VNet 中或在任何位置允许到端口443上的 Azure 的出站流量。 此功能要求在 Windows Server 2012 或更高版本的主机上安装称为混合连接管理器 (HCM) 的中继代理。 HCM 需要能够访问端口443上的 Azure 中继。 可以从门户中的应用服务混合连接 UI 下载 HCM。 

![混合连接网络流](media/networking-features/hybrid-connections.png)

应用服务混合连接功能是基于 Azure 中继混合连接功能构建的。 应用服务使用一种专用形式的功能, 该功能仅支持从应用到 TCP 主机和端口发出出站调用。 此主机和端口只需要在安装了 HCM 的主机上解析。 当应用服务中的应用在你的混合连接中定义的主机和端口上执行 DNS 查找时, 会自动将流量重定向到混合连接并传出混合连接管理器。 若要详细了解混合连接, 请参阅有关[应用服务][hybridconn]的文档混合连接

此功能通常用于:

* 使用 VPN 或 ExpressRoute 访问未连接到 Azure 的专用网络中的资源
* 支持将本地应用程序提升和移动到应用服务, 无需同时移动支持数据库  
* 安全地为每个混合连接提供对单个主机和端口的访问权限。 大多数网络功能都开放了对网络的访问权限, 并且通过混合连接您只能使用单个主机和端口。
* 其他出站连接方法未涵盖的覆盖方案
* 在应用服务中执行开发, 在应用服务中, 应用可轻松利用本地资源 

由于此功能可让你在没有入站防火墙洞的情况下访问本地资源, 因此开发人员非常熟悉。 其他出站应用服务网络功能与 Azure 虚拟网络相关。 混合连接不依赖于遍历 VNet, 可用于更广泛的网络需求。 需要特别注意的是, 应用服务混合连接功能并不关心或不知道在其上执行的操作。 也就是说, 您可以使用它来访问数据库、web 服务或大型机上的任意 TCP 套接字。 此功能实质上是对 TCP 数据包进行隧道。 

尽管混合连接适用于开发, 但也可用于众多的生产应用程序。 它非常适合用于访问 web 服务或数据库, 但不适用于涉及创建多个连接的情况。 

### <a name="gateway-required-vnet-integration"></a>网关所需的 VNet 集成 

通过网关所需的应用服务 VNet 集成功能, 你的应用可以向 Azure 虚拟网络发出**出站**请求。 此功能的工作原理是将应用运行所在的主机连接到 VNet 上具有点到站点 VPN 的虚拟网络网关。 当你配置该功能时, 你的应用将获取分配给每个实例的点到站点地址之一。 此功能可让你访问任何区域中的经典或资源管理器 Vnet 中的资源。 

![网关所需的 VNet 集成](media/networking-features/gw-vnet-integration.png)

此功能解决了访问其他 Vnet 中的资源的问题, 甚至可用于通过 VNet 连接到其他 Vnet 甚至本地。 它不适用于 ExpressRoute 连接的 Vnet, 但会与站点到站点 VPN 连接网络一起使用。 从应用服务环境 (ASE) 中的应用程序中使用此功能通常是不正确的, 因为 ASE 已在 VNet 中。 此功能的解决用例如下:

* 访问 Azure 虚拟网络中专用 Ip 的资源 
* 如果存在站点到站点 VPN, 则访问本地资源 
* 在对等互连 Vnet 中访问资源 

启用此功能后, 你的应用将使用配置了目标 VNet 的 DNS 服务器。 有关此功能的详细信息, 请参阅[应用服务 VNet 集成][vnetintegrationp2s]文档。 

### <a name="vnet-integration"></a>VNet 集成

网关所需的 VNet 集成功能非常有用, 但仍不能解决跨 ExpressRoute 访问资源的情况。 在需要跨 ExpressRoute 连接的情况下, 应用程序需要能够对服务终结点保护服务进行调用。 若要解决这两个附加需求, 添加了另一个 VNet 集成功能。 利用新的 VNet 集成功能, 你可以将应用程序的后端置于同一区域中资源管理器 VNet 的子网中。 此功能在已在 VNet 中的应用服务环境不可用。 此功能支持：

* 在同一区域中访问资源管理器 Vnet 中的资源
* 访问通过服务终结点保护的资源 
* 访问可跨 ExpressRoute 或 VPN 连接访问的资源

![VNet 集成](media/networking-features/vnet-integration.png)

此功能为预览版, 不应用于生产工作负荷。 若要了解有关此功能的详细信息, 请阅读[应用服务 VNet 集成][vnetintegration]中的文档。

## <a name="app-service-environment"></a>应用服务环境 

应用服务环境 (ASE) 是在 VNet 中运行的 Azure App Service 的单租户部署。 ASE 启用用例, 例如:

* 访问 VNet 中的资源
* 跨 ExpressRoute 访问资源
* 在 VNet 中使用专用地址公开你的应用 
* 跨服务终结点访问资源 

使用 ASE 时, 无需使用 VNet 集成或服务终结点等功能, 因为 ASE 已在 VNet 中。 如果要通过服务终结点访问 SQL 或存储等资源, 请在 ASE 子网上启用服务终结点。 如果要访问 VNet 中的资源, 则无需进行其他配置。  如果要跨 ExpressRoute 访问资源, 则已在 VNet 中, 无需在 ASE 或其中的应用上配置任何内容。 

由于可以在专用 IP 地址上公开 ILB ASE 中的应用, 因此, 你可以轻松地添加 WAF 设备, 以便只公开你想要连接到 internet 的应用, 并使其保持安全。 它本身使你能够轻松地开发多层应用程序。 

对于来自 ASE 的多租户服务, 尚无法实现某些功能。 其中包括:

* 在专用 IP 地址上公开你的应用
* 使用不属于应用程序的网络控件保护所有出站流量 
* 在单租户服务中托管你的应用 
* 扩展到多租户服务中的多个实例 
* 加载私有 CA 客户端证书以供使用私有 CA 安全终结点的应用使用 
* 在系统中托管的所有应用程序上强制执行 TLS 1.1, 而不能在应用程序级禁用 
* 为 ASE 中所有不与任何客户共享的应用提供专用的出站地址 

![VNet 中的 ASE](media/networking-features/app-service-environment.png)

ASE 提供有关隔离和专用应用程序托管的最佳故事, 但仍有一些管理挑战。 使用操作 ASE 之前需要考虑的事项包括:
 
 * ASE 在 VNet 中运行, 但在 VNet 之外具有依赖关系。 必须允许这些依赖项。 有关应用服务环境的详细信息, 请参阅[网络注意事项][networkinfo]
 * ASE 不会立即扩展到多租户服务。 需要预计缩放需求, 而不是被动缩放。 
 * ASE 具有与之相关联的更高成本。 为了充分利用 ASE, 你应该计划将多个工作负载放入一个 ASE, 而不是将其用于小工作
 * ASE 中的应用无法限制对 ASE 中的某些应用 (而不是其他) 的访问。
 * ASE 位于子网中, 任何网络规则都适用于进出该 ASE 的所有流量。 如果只想为一个应用分配入站流量规则, 请使用访问限制。 

## <a name="combining-features"></a>组合功能 

为多租户服务所记录的功能可以一起用于解决更复杂的用例。 此处描述了两个较常见的用例, 但它们只是示例。 通过了解各种功能的作用, 您几乎可以解决您的所有系统体系结构需求。

### <a name="inject-app-into-a-vnet"></a>将应用注入 VNet

常见的请求是如何在 VNet 中放置应用程序。 如果将应用程序放在 VNet 中, 则表示应用程序的入站和出站终结点在 VNet 中。 ASE 提供了解决此问题的最佳解决方案, 但你可以通过组合功能在多租户服务中获取所需的大部分功能。 例如, 你可以通过以下方式仅托管具有专用入站和出站地址的应用程序:

* 使用专用入站和出站地址创建应用程序网关
* 通过服务终结点保护应用程序的入站流量 
* 使用新的 VNet 集成, 使应用的后端位于 VNet 中 

此部署样式不会为你提供向 internet 出站流量的专用地址, 也不会使你能够锁定应用中的所有出站流量。  此部署样式仅为你提供了一种方法, 只需使用 ASE 即可。 

### <a name="create-multi-tier-applications"></a>创建多层应用程序

多层应用程序是一种应用程序, 在该应用程序中, 只能从前端层访问 API 后端应用。 若要创建多层应用程序, 可以:

* 使用 VNet 集成将前端 web 应用的后端连接到 VNet 中的子网
* 使用服务终结点保护到 API 应用的入站流量, 使其仅来自前端 web 应用使用的子网

![多层应用程序](media/networking-features/multi-tier-app.png)

可以让多个前端应用使用同一个 API 应用, 方法是使用来自其他前端应用的 VNet 集成, 并将 API 应用中的服务终结点与其子网结合使用。  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
