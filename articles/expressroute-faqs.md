<properties 
   pageTitle="ExpressRoute 常见问题"
   description="ExpressRoute 常见问题包含有关支持的 Azure 服务、费用、数据和连接、SLA、提供程序和位置、带宽的信息和其他技术详细信息。"
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
   ms.date="04/13/2015"
   ms.author="cherylmc"/>

# ExpressRoute 常见问题


## 什么是 ExpressRoute？
ExpressRoute 是一项 Azure 服务，允许你在 Azure 数据中心与你的本地环境或共同租用设施中的基础结构之间创建专用连接。ExpressRoute 连接不通过公共 Internet，与通过公共 Internet 的典型连接相比，提供更高的安全性、可靠性、速度和更低的延迟。

### 使用 ExpressRoute 和专用网络连接的好处是什么？
ExpressRoute 连接不通过公共 Internet，与通过公共 Internet 的典型连接相比，提供更高的安全性、可靠性、速度和一贯较低的延迟。在某些情况下，使用 ExpressRoute 连接在本地设备和 Azure 之间传输数据可以产生显著的成本效益。

### 哪里提供该服务？
请参阅此页面了解服务位置和可用性：[ExpressRoute 服务提供商和位置](https://msdn.microsoft.com/library/azure/dn957919.aspx)。

### 我如果未与 ExpressRoute 运营商合作伙伴之一建立合作伙伴关系，则如何使用 ExpressRoute 连接到 Azure？
你可以将区域运营商和地区以太网连接选择为支持的 Exchange 提供商位置之一。然后，你可以在交换提供商位置与 Azure 一起运行。请咨询你的 NSP，以确定它们是否处在上面所列的交换位置中，并让你的服务提供商将你的网络扩展到所选的交换位置。然后，你可以从交换提供商订购一条 ExpressRoute 线路以连接到 Azure。

### ExpressRoute 的费用是多少？
请查看[定价详细信息](http://azure.microsoft.com/pricing/details/expressroute/)
了解价格信息。

### 如果我购买了具有给定带宽的 ExpressRoute 线路，我必须从网络服务提供商购买具有相同速度的 VPN 连接吗？
不是。你可以从服务提供商购买任何速度的 VPN 连接。但是，与 Azure 的连接速度将限制为你购买的 ExpressRoute 线路带宽。 

### 如果我购买了具有给定带宽的 ExpressRoute 线路，是否可以根据需要提升到更高的速度？
是的。ExpressRoute 线路的配置支持免费将速度提升到所购带宽限制的两倍。请咨询你的服务提供商，以确定他们是否支持此功能。

### 能否同时与虚拟网络和其他 Azure 服务使用同一专用网络连接？
是的。设置 ExpressRoute 线路后，将允许你同时访问虚拟网络中的服务和其他 Azure 服务。

### ExpressRoute 是否提供服务级别协议 (SLA)？
有关详细信息，请参阅 [ExpressRoute SLA 页](http://azure.microsoft.com/support/legal/sla/)。

## 支持的 Azure 服务
大多数 Azure 服务都通过 ExpressRoute 提供支持。

与虚拟机和虚拟网络中部署的云服务的连接通过专用对等路径提供支持。

可通过公共对等路径访问 Azure 网站。

可通过公共对等路径访问所有其他服务。下面列出了例外情况 -

**不支持以下项目：**

- CDN
- RemoteApp
- Visual Studio Online Load Testing
- 通知中心
- 自动化
- Application Insights
- 多重身份验证
- API 管理
- 推送通知



## 数据和连接

### 对于使用 ExpressRoute 可以传输的数据量是否有限制？
我们不对数据传输量设置限制。有关带宽价格的信息，请参阅[定价详细信息](http://azure.microsoft.com/pricing/details/expressroute/)。

### ExpressRoute 支持的连接速度是多少？
支持带宽提供：

|**提供商**|**带宽**|
|---|---|
|**网络提供商**|10 Mbps、50 Mbps、100 Mbps、500 Mbps、1 Gbps|
|**Exchange 提供商**|200 Mbps、500 Mbps、1Gbps、10Gbps|

### 可以选择哪些服务提供商？
有关服务提供商和位置的列表，请参阅 [ExpressRoute 位置](https://msdn.microsoft.com/library/azure/dn957919.aspx)。

## 技术详细信息

### 将本地位置连接到 Azure 有哪些技术要求？
有关要求，请参阅 [ExpressRoute 先决条件页](expressroute-prerequisites.md)。

### 与 ExpressRoute 的连接是冗余的吗？
是的。每条 ExpressRoute 线路都配置了一对冗余的交叉连接，以便为你提供高可用性。

### 如果我的某个 ExpressRoute 链路出现故障，我会失去连接吗？
如果其中一个交叉连接出现故障，你不会失去连接。冗余连接可用于支持网络负载。另外，你还可以在不同对等位置创建多条线路以获得故障恢复能力。

### 我是否需要配置这两种链路才能使服务正常工作？
如果你正在通过 NSP 进行连接，NSP 将负责为你配置冗余链接。如果你正在通过 EXP 进行连接，则必须配置这两条链路。如果没有为线路配置冗余，我们的 SLA 将无效。

### 能否使用 ExpressRoute 将我的一个 VLAN 扩展到 Azure？
否。我们不支持将第 2 层连接扩展到 Azure。

### 能否在我的订阅中有多条 ExpressRoute 线路？
是的。你可以在订阅中有多条 ExpressRoute 线路。专用线路数的默认限制设置为 10。如果你需要增大限制，请联系 Microsoft 支持。

### 能否使用不同服务提供商的 ExpressRoute 线路？
是的。你可以使用许多服务提供商的 ExpressRoute 线路。每条 ExpressRoute 线路将只与一个服务提供商相关联。

### 如何将我的虚拟网络连接到 ExpressRoute 线路
基本步骤如下所述。

- 你必须建立一条 ExpressRoute 线路并让服务提供商启用它。
- 你必须为私有对等互连配置 BGP（如果你使用的是 Exchange 提供商）。
- 你必须将虚拟网络连接到 ExpressRoute 线路。

以下教程将帮助你：

- [通过网络服务提供商配置 ExpressRoute 连接](https://msdn.microsoft.com/library/azure/dn643736.aspx)
- [通过 Exchange 提供商配置 ExpressRoute 连接](https://msdn.microsoft.com/library/azure/dn606306.aspx)
- [为 ExpressRoute 配置虚拟网络和网关](https://msdn.microsoft.com/library/azure/dn643737.aspx)

### 能否将多个虚拟网络链接到一条 ExpressRoute 线路？
是的。最多可以将 10 个虚拟网络链接到一条 ExpressRoute 线路。所有虚拟网络必须与 ExpressRoute 线路在同一大洲。

### 能否将属于多个订阅的虚拟网络连接到一条 ExpressRoute 线路？
是的。最多可以授权其他 10 个 Azure 订阅使用单条 ExpressRoute 线路。有关详细信息，请参阅[在多个订阅之间共享 ExpressRoute 线路](https://msdn.microsoft.com/library/azure/dn835110.aspx)。

### 连接到同一线路的虚拟网络相互隔离吗？
不能。连接到同一 ExpressRoute 线路的所有虚拟网络都属于同一路由域，从路由角度看不是相互隔离的。如果需要路由隔离，则需要创建单独的 ExpressRoute 线路。

### 能否将一个虚拟网络连接到多条 ExpressRoute 线路？
是的。可以将一个虚拟网络最多链接到 4 条 ExpressRoute 线路。所有 ExpressRoute 线路必须位于同一个大洲。可以通过不同区域的不同服务提供商订购线路。

### 能否从连接到 ExpressRoute 线路的虚拟网络访问 Internet？
是的。如果你尚未通过 BGP 会话公布默认路由 (0.0.0.0/0) 或 Internet 路由前缀，你将能够从连接到 ExpressRoute 线路的虚拟网络连接到 Internet。

### 能否阻止与连接到 ExpressRoute 线路的虚拟网络建立 Internet 连接？
是的。你可以公布默认路由 (0.0.0.0/0) 以阻止与虚拟网络内部署的虚拟机建立所有 Internet 连接，并通过 ExpressRoute 线路路由出所有流量。

### 连接到同一 ExpressRoute 线路的虚拟网络能否互相对话？
是的。连接到同一 ExpressRoute 线路的虚拟网络中部署的虚拟机可以彼此通信。

### 能否将站点到站点和点到站点连接与 ExpressRoute 一起用于虚拟网络？
不能。连接到 ExpressRoute 线路的虚拟网络不能与站点到站点和点到站点一起使用。

### 能否将虚拟网络从站点到站点/点到站点配置转为使用 ExpressRoute？
是的。但是，此过程将会招致较短的停机时间。

### 通过 ExpressRoute 连接到 Azure 存储需要执行哪些操作？
你必须建立一条 ExpressRoute 线路并为公共对等互连配置路由。

### 对于我可以公布的路由数有限制吗？
是的。对于专用对等互连和公共对等互连，我们最多接受 4000 个路由前缀。

### 对于我可以通过 BGP 会话公布的 IP 范围有限制吗？
通过 BGP 公布的前缀必须为 /29 或更大。我们将在公共对等 BGP 会话中筛选出私有前缀 (RFC1918)。

### 如果超过 BGP 限制，会发生什么情况？
BGP 会话将被删除。当前缀计数低于限制后，将重置这些会话。

### 将默认路由 (0.0.0.0/0) 播发到虚拟网络后，我无法激活 Azure VM 上运行的 Windows。我该怎么办？
以下步骤可帮助 Azure 识别激活请求：

1. 为 ExpressRoute 线路建立公共对等互连。
2. 执行 DNS 查找，找到 **kms.core.windows.net** 的 IP 地址
3. 然后执行以下两项操作之一，使密钥管理服务能够识别来自 Azure 的激活请求并遵照该请求。
	- 在你的本地网络上，通过公共对等互连将发往 IP 地址（在步骤 2 中获得）的流量路由回到 Azure。
	- 让你的 NSP 提供商通过公共对等互连将流量路由回到 Azure。 

<!---HONumber=56-->