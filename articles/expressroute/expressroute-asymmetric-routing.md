<properties
   pageTitle="非对称路由 | Azure"
   description="本文介绍客户在有多条链路连接到目标的网络中可能面临的非对称路由问题。"
   documentationCenter="na"
   services="expressroute"
   authors="osamazia"
   manager="carmonm"
   editor=""/>  

<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/23/2016"
   ms.author="osamazia"
   wacn.date=""/>  


# 具有多个网络路径的非对称路由

本文介绍当网络源和网络目标之间有多个路径可用时，流出的网络流量和返回的网络流量如何采用不同的路线。

要理解非对称路由，必须理解两个概念。一个概念是多个网络路径的影响。另一个概念是设备（如防火墙）如何保持状态。这些类型的设备称为有状态设备。由于有状态设备不会检测源自设备本身的网络流量，这两种因素共同导致了这种网络流量被有状态设备所删除。

## 多个网络路径

如果企业网络的 Internet 服务提供商只提供一条链路来连接 Internet，那么流入和流出 Internet 的所有流量都通过同一个路径传输。通常，公司需要购买多条线路作为冗余路径，提高网络的正常运行时间。发生这种情况可能是由于，从网络流入 Internet 的流量通过一条链路传输，而返回的流量通过另一条链路传输。这通常称为非对称路由。在非对称路由中，原始网络流量与返回的网络流量采用不同的路径。

![具有多个路径的网络](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)  


尽管非对称路由主要发生在 Internet 中，但它也适用于其他的多个路径组合情况。例如，它适用于转到同一目标的 Internet 路径和专用路径，以及转到同一目标的多个专用路径。

从源到目标的每个路由器都会计算达到目标的最佳路径。路由器根据两个主要因素确定可能的最佳路径：

-	根据路由协议（边界网关协议 (BGP)）确定外部网络之间的路由。BGP 接收邻居的播发，通过一系列步骤运行这些播发，确定到达预期目标的最佳路径。它将最佳路径存储在路由表中。
-	路线的子网掩码长度会影响路由路径。如果路由器收到 IP 地址相同、子网掩码不同的多个播发，路由器会首选子网掩码更长的播发，因为路由器认为它是更具体的路线。

## 有状态设备

路由器会查看要路由的数据包的 IP 标头。一些设备甚至会更深入地查看数据包的内容。通常，这些设备会查看到第 4 层（传输控制协议 (TCP) 或用户数据报协议 (UDP)）标头，甚至第 7 层（应用程序层）标头。这些类型的设备属于安全设备，或带宽优化设备。

防火墙就是一种常见的有状态设备。防火墙根据各个域（如协议、TCP/UDP 端口和 URL 标头）允许或拒绝数据包通过其接口。这一级别的数据包检查大幅增加了设备的处理负荷。为了提高性能，防火墙将检查网络流的第一个数据包。如果防火墙允许该数据包通过，就会将该网络流的信息保存在状态表中。根据初步判定，允许后续与此网络流相关的所有数据包。属于现有网络流的数据包可能会到达防火墙。如果防火墙之前没有该数据包的相关状态信息，就会删除该数据包。

## 通过 ExpressRoute 非对称路由

通过 Azure ExpressRoute 连接到 Microsoft 时，网络更改如下：

-	有多条链路连接到 Microsoft。一条链路是现有的 Internet 连接，另一条是通过 ExpressRoute 连接。一些流量可能通过 Internet 流入 Microsoft，却通过 ExpressRoute 返回，反之亦然。
-	通过 ExpressRoute 可以收到更具体的 IP 地址。因此，对于通过 ExpressRoute 提供的服务，路由器始终首选 ExpressRoute 将网络中的流量传输到 Microsoft。

以如下情况为例，了解这两项更改对网络产生的影响。例如，只有一条线路连接到 Internet，并且通过 Internet 使用所有 Microsoft 服务。流量通过同一条 Internet 链路，穿过防火墙往返于网络与 Microsoft 之间。防火墙在看到第一个数据包时记录网络流，并将其保存在状态表中，据此允许其返回数据包。

![通过 ExpressRoute 非对称路由](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)  



然后，启用 ExpressRoute，通过 ExpressRoute 使用 Microsoft 提供的服务。Microsoft 提供的所有其他服务均通过 Internet 使用。在连接到 ExpressRoute 的边缘部署单独的防火墙。Microsoft 通过 ExpressRoute 将特定服务的更具体前缀播发到网络。路由基础结构选择 ExpressRoute 作为这些前缀的首选路径。如果不通过 ExpressRoute 将公共 IP 地址播发到 Microsoft，Microsoft 将通过 Internet 与公共 IP 地址通信。网络中的流量使用 ExpressRoute 流入 Microsoft，Microsoft 的流量使用 Internet 返回。如果边缘的防火墙发现网络流的响应数据包不在状态表中，就会删除该返回流量。

如果 ExpressRoute 和 Internet 使用同一个网络地址转换 (NAT) 池，会发现专用 IP 地址网络中的客户端存在类似的问题。服务请求（如 Windows 更新）通过 Internet 传输，因为这些服务的 IP 地址不会通过 ExpressRoute 播发。但是，流量通过 ExpressRoute 返回。如果 Microsoft 从 Internet 和 ExpressRoute 收到子网掩码相同的 IP 地址，它会首选 ExpressRoute，而非 Internet。如果网络边缘中使用 ExpressRoute 的防火墙或其他有状态设备之前没有该网络流的相关信息，就会删除属于该网络流的数据包。

## 非对称路由解决方案

解决非对称路由问题的方法主要有两种。一种是通过路由，另一种是使用基于源的 NAT (SNAT)。

### 路由

确保公共 IP 地址播发到相应的广域网 (WAN) 链路。例如，如果使用 Internet 来传输身份验证流量，使用 ExpressRoute 来传输邮件流量，就不得通过 ExpressRoute 播发 Active Directory 联合身份验证服务 (AD FS) 公共 IP 地址。同样，请确保不向路由器通过 ExpressRoute 收到的 IP 地址公开本地 AD FS 服务器。通过 ExpressRoute 收到的路线更为具体，Microsoft 首选 ExpressRoute 来传输身份验证流量。这将导致非对称路由。

若要使用 ExpressRoute 来传输身份验证流量，请确保通过 ExpressRoute 播发 AD FS 公共 IP 地址，而不使用 NAT。这样一来，源自 Microsoft 的流量就会通过 ExpressRoute 流入本地 AD FS 服务器。源自客户的流量则使用 ExpressRoute 返回 Microsoft，因为相较于 Internet 而言，ExpressRoute 是首选路线。

### 基于源的 NAT

解决非对称路由问题的另一种方法是使用 SNAT。例如，不通过 ExpressRoute 播发本地简单邮件传输协议 (SMTP) 服务器的公共 IP 地址，而打算使用 Internet 进行这种类型的通信。源自 Microsoft 的请求通过 Internet 流入本地 SMTP 服务器。使用 SNAT 将传入的请求传输到内部 IP 地址。SMTP 服务器返回的流量流入边缘防火墙（用于 NAT），而不是通过 ExpressRoute 传输。流量通过 Internet 返回。


![基于源的 NAT 网络配置](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)  


## 非对称路由检测

确保网络流量通过预期路径的最佳方式是路线跟踪。如果希望流量通过 Internet 路径从本地 SMTP 服务器传输到 Microsoft，预期的路线跟踪是从 SMTP 服务器到 Office 365。结果证明流量确实从网络流向 Internet，而非 ExpressRoute。

<!---HONumber=Mooncake_0926_2016-->