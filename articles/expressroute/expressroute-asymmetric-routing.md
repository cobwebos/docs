---
title: "非对称路由 | Microsoft Docs"
description: "本文逐步讲解客户在与目标建立多个链接的网络中使用非对称路由时可能会遇到的问题。"
documentationcenter: na
services: expressroute
author: osamazia
manager: carmonm
editor: 
ms.assetid: a754bff9-95c9-44b5-9796-377fc21e8322
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: osamam
ms.openlocfilehash: 8568c13d2834a0643e15ab1814a35c92123837d1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>非对称路由与多个网络路径
本文说明当网络源与目标之间有多个路径时，正向和返回网络流量如何选择不同的路由。

只有在理解两个概念之后，才能了解非对称路由。 一个概念是多个网络路径的影响。 另一个概念是设备（如防火墙）如何保持状态。 这种类型的设备称为有状态设备。 这两个因素的组合会造成有状态设备丢弃网络流量的情况，因为有状态设备未检测到源自设备本身的流量。

## <a name="multiple-network-paths"></a>多个网络路径
当企业网络只通过其 Internet 服务提供商与 Internet 建立了一条链路时，往返 Internet 的所有流量都会经过同一条路径。 公司往往会购买多条线路作为冗余路径，以改善网络运行时间。 发生这种情况时，离开网络前向 Internet 的流量可以通过一条链路，返回流量可以通过另一条链路。 这通常称为非对称路由。 在非对称路由中，反向网络流量采取与原始流程不同的路径。

![具有多个路径的网络](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

尽管这主要发生在 Internet 上，但非对称路由也适用于其他多个路径的组合。 举例来说，它适用于前往相同目标的 Internet 路径和专用路径，以及前往相同目标的多个专用路径。

从源到目标的每个路由器都会计算达到目标的最佳路径。 路由器根据两个主要因素确定可能最佳的路径：

* 外部网络之间的路由基于边界网关协议 (BGP) 路由协议。 BGP 接受邻居的播发并通过一连串的步骤来运行这些播发，确定抵达预定目标的最佳路径。 它在其路由表中存储最佳路径。
* 与路由关联的子网掩码的长度会影响路由路径。 如果路由器收到 IP 地址相同、子网掩码不同的多个播发，路由器会首选子网掩码更长的播发，因为路由器认为它是更具体的路线。

## <a name="stateful-devices"></a>有状态设备
路由器会查看要路由的数据包的 IP 标头。 某些设备看起来在数据包的更深处。 这些设备通常会查看第 4 层（传输控制协议 (TCP) 或用户数据报协议 (UDP)），甚至第 7 层（应用程序层）标头。 这几种设备要么是安全设备，要么是带宽优化设备。 

防火墙是有状态设备的常见示例。 防火墙根据各种字段（例如协议、TCP/UDP 端口、URL 标头），允许或拒绝数据包通过其接口。 此级别的数据包检查在设备上造成沉重的处理负载。 为了改善性能，防火墙会检查流程的第一个数据包。 如果允许数据包继续传递，防火墙会将流程信息保存在其状态表中。 根据最初的决定，允许与此流程相关的所有后续数据包。 属于现有流程的数据包可以抵达防火墙。 如果防火墙没有以前的状态信息，则丢弃数据包。

## <a name="asymmetric-routing-with-expressroute"></a>非对称路由与 ExpressRoute
通过 Azure ExpressRoute 连接到 Microsoft 时，网络会发生以下变化：

* 与 Microsoft 建立多个链接。 一个链接是现有的 Internet 连接，另一个是通过 ExpressRoute 的连接。 发往 Microsoft 一些流量可能通过 Internet 传输，但通过 ExpressRoute 返回，反之亦然。
* 可以通过 ExpressRoute 接收更明确的 IP 地址。 因此，对于通过 ExpressRoute 提供的服务而言，路由器始终偏向于针对网络到 Microsoft 的流量使用 ExpressRoute。

为了了解这两项更改对网络的影响，让我们设想一些场景。 例如，只有一条线路通往 Internet，并且所有 Microsoft 服务都是通过 Internet 使用的。 在用户网络与 Microsoft 之间来回传输的流量遍历相同的 Internet 链接并通过防火墙。 防火墙在看到第一个数据包时记录流程，并允许返回数据包，因为此流程在于状态表中存在。

![非对称路由与 ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

然后，启用 ExpressRoute，通过 ExpressRoute 使用 Microsoft 提供的服务。 Microsoft 提供的所有其他服务都通过 Internet 使用。 在连接到 ExpressRoute 的边缘服务器上部署不同的防火墙。 Microsoft 通过 ExpressRoute，针对特定服务向网络播发更明确的前缀。 路由基础结构选择 ExpressRoute 作为这些前缀的首选路径。 如果不是通过 ExpressRoute 向 Microsoft 播发公共 IP 地址，Microsoft 将通过 Internet 来与公共 IP 地址通信。 从网络到 Microsoft 的正向流量使用 ExpressRoute，来自 Microsoft 的反向流量使用 Internet。 当边缘服务器上的防火墙看到了在状态表中找不到的流程的响应数据包时，将丢弃返回流量。

如果选择将同一网络地址转译 (NAT) 池用于 ExpressRoute 和 Internet，会发现网络中专用 IP 地址上的客户端有类似的问题。 Windows Update 等服务的请求通过 Internet 传递，因为这些服务的 IP 地址不通过 ExpressRoute 播发。 但是，返回流量通过 ExpressRoute 返回。 如果 Microsoft 从 Internet 和 ExpressRoute 收到具有相同子网掩码的 IP 地址，则首选基于 Internet 的 ExpressRoute。 如果网络边缘中使用 ExpressRoute 的防火墙或其他有状态设备之前没有该网络流的相关信息，就会删除属于该网络流的数据包。

## <a name="asymmetric-routing-solutions"></a>非对称路由解决方案
有两个主要选项可以解决非对称路由问题。 一个是通过路由，另一个是使用基于源的 NAT (SNAT)。

### <a name="routing"></a>路由
确保已向相应的广域网 (WAN) 链接播发公共 IP 地址。 例如，如果要将 Internet 用于身份验证流量，将 ExpressRoute 用于邮件流量，则不应通过 ExpressRoute 播发 Active Directory 联合身份验证服务 (AD FS) 公共 IP 地址。 同样地，请确保不要对路由器通过 ExpressRoute 接收的 IP 地址公开本地 AD FS 服务器。 通过 ExpressRoute 收到的路由更加明确，因此可让 ExpressRoute 成为 Microsoft 身份验证流量的首选路径。 这会导致非对称路由。

如果想要使用 ExpressRoute 进行身份验证，请确保通过 ExpressRoute（不使用 NAT）播发 ADFS 公共 IP 地址。 这样一来，源自 Microsoft 的流量就会通过 ExpressRoute 流入本地 AD FS 服务器。 从客户到 Microsoft 的返回流量使用 ExpressRoute，因为这是基于 Internet 的首选路由。

### <a name="source-based-nat"></a>基于源的 NAT
解决非对称路由问题的另一种方法是使用 SNAT。 例如，某个用户尚未通过 ExpressRoute 播发本地简单邮件传输协议 (SMTP) 服务器的公共 IP 地址，因为该用户倾向于使用 Internet 进行此类通信。 源自 Microsoft 前往本地 SMTP 服务器的请求将遍历 Internet。 通过 SNAT 将传入请求传递到内部 IP 地址。 来自 SMTP 服务器的反向流量将抵达边缘防火墙（用于 NAT），而不通过 ExpressRoute。 返回流量将通过 Internet 返回。

![基于源的 NAT 网络配置](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>非对称路由检测
跟踪路由是确保网络流量遍历预期路径的最佳方式。 如果预期从本地 SMTP 服务器到 Microsoft 的流量采用 Internet 路径，则预期跟踪路由是从 SMTP 服务器到 Office 365。 结果证明，流量确实离开了网络前往 Internet，而不是前往 ExpressRoute。

