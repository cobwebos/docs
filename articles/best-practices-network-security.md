---
title: "Azure 网络安全最佳实践 | Microsoft 文档"
description: "了解 Azure 中用于帮助创建安全网络环境的某些重要功能"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: d169387a-1243-4867-a602-01d6f2d8a2a1
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 243c1cd5ebf34f2d8a8fda234fa3875298390336
ms.lasthandoff: 04/27/2017


---

# <a name="microsoft-cloud-services-and-network-security"></a>Microsoft 云服务和网络安全性
Microsoft 云服务提供超大规模的服务和基础结构、企业级的功能，以及许多混合连接选项。 客户可以选择通过 Internet 或 Azure ExpressRoute（提供专用网络连接）访问这些服务。 Microsoft Azure 平台可让客户无缝地将基础结构扩展到云中并构建多层体系结构。 此外，第三方可以提供安全服务和虚拟设备，以启用增强的功能。 本白皮书概述了当客户使用通过 ExpressRoute 访问的 Microsoft 云服务创建安全服务时应该考虑的安全和体系结构问题。 此外，还介绍了如何在 Azure 虚拟网络中创建其他安全服务。

## <a name="fast-start"></a>快速开始
以下逻辑图表以具体示例说明了 Azure 平台提供的许多安全技术。 有关快速参考，请找到最适合你案例的示例。 有关更详尽的说明，请继续阅读本文。
[![0]][0]

[示例 1：构建外围网络（也称为 DMZ、外围安全区域或屏蔽子网），以使用网络安全组 (NSG) 帮助保护应用程序。](#example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs)</br>
[示例 2：构建外围网络以使用防火墙和 NSG 帮助保护应用程序。](#example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs)</br>
[示例 3：构建外围网络，以使用防火墙、用户定义的路由 (UDR) 和 NSG 帮助保护网络。](#example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg)</br>
[示例 4：使用站点到站点虚拟设备虚拟专用网络 (VPN) 添加混合连接。](#example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[示例 5：使用站点到站点 Azure VPN 网关添加混合连接。](#example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway)</br>
[示例 6：使用 ExpressRoute 添加混合连接。](#example-6-add-a-hybrid-connection-with-expressroute)</br>
未来几个月内，本文档中将会加入有关在虚拟网络之间添加连接、高可用性和服务链接的示例。

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Microsoft 合规性与基础结构保护
为了确保组织在收集和使用个人数据时遵循国家、地区和行业特定要求，Microsoft 提供了 40 多种认证和证明。 最全面的集合，囊括所有云服务提供商。

有关详细信息，请参阅 [Microsoft 信任中心][TrustCenter]上的合规性信息。

Microsoft 采取综合性的方案来保护运行超大规模全球服务所需的云基础结构。 Microsoft 云基础结构包括硬件、软件、网络、管理和运营人员以及物理数据中心。

![2]

此方案提供更安全的基础，使客户能够将服务部署在 Microsoft 云中。 客户要采取的后续措施是设计和创建安全体系结构来保护这些服务。

## <a name="traditional-security-architectures-and-perimeter-networks"></a>传统的安全体系结构与外围网络
尽管 Microsoft 为保护云基础结构投入了大量资金，但客户仍必须保护其云服务和资源组。 安全的多层方案提供最佳的防御措施。 外围网络安全区域可防止不受信任的网络访问内部网络资源。 外围网络是指位于 Internet 与受保护企业 IT 基础结构之间的网络边缘或组件。

在典型的企业网络中，核心基础结构的周边有多层的安全设备严加保护。 每一层的边界由设备和策略实施点组成。 每一层都可能包括以下网络安全设备的组合：防火墙、拒绝服务 (DoS) 预防、入侵检测或保护系统 (IDS/IPS)，以及 VPN 设备。 策略可能以防火墙策略、访问控制列表 (ACL) 或特定路由等形式实施。 网络的第一道防线（直接接受来自 Internet 的传入流量）由这些机制联合起来阻挡攻击和有害流量，但允许合法的请求进入网络。 此流量直接路由到外围网络中的资源。 该资源可能与网络中更深处的资源“对话”，并首先通过下一个边界的验证。 最外层称为外围网络，因为网络的这个部分对 Internet 公开，而外围网络的两端通常有某种形式的保护。 下图显示了企业网络中的单子网外围网络示例，其中有两个安全边界。

![3]

有许多用于实现外围网络的体系结构。 这些体系结构既可以是简单的负载均衡器，也可以是多子网型外围网络，在每个边界上设有各种阻止流量和保护更深层企业网络的机制。 如何构建外围网络取决于组织的特定要求及其总体风险容限度。

当客户将工作负荷移到公有云时，Azure 中的外围网络体系结构必须支持类似的功能，才能满足合规性和安全性要求。 本文提供有关客户如何在 Azure 中构建安全网络环境的指导。 其中重点介绍外围网络，但也全面讨论了网络安全的许多层面。 内容文档从以下问题开始：

* 如何在 Azure 中构建外围网络？
* 有哪些 Azure 功能可用来构建外围网络？
* 如何保护后端工作负荷？
* 如何控制 Internet 与 Azure 中工作负荷的通信？
* 如何防止本地网络受到 Azure 中的部署的影响？
* 何时使用本机 Azure 安全功能？何时又该使用第三方设备或服务？

下图显示了 Azure 提供给客户的各种安全层。 这些层是 Azure 平台的本机功能和客户定义的功能：

![4]

对于来自 Internet 的入站流量，Azure DDoS 可帮助防御针对 Azure 的大规模攻击。 下一层是客户定义的公共 IP 地址（终结点），可以根据这些终结点确定哪些流量可以通过云服务进入虚拟网络。 本机 Azure 虚拟网络隔离可确保与其他所有网络完全隔离，而且流量只能流经用户配置的路径和方法。 这些路径和方法就是下一个安全层，在该层中，可以使用 NSG、UDR 和网络虚拟设备来创建安全边界，以保护受保护网络中的应用程序部署。

下一部分提供 Azure 虚拟网络的概述。 这些虚拟网络由客户创建，它们是部署的工作负荷所要连接的目标。 虚拟网络是创建外围网络来保护 Azure 中的客户部署时所需一切网络安全功能的基础。

## <a name="overview-of-azure-virtual-networks"></a>Azure 虚拟网络概述
在 Internet 流量进入 Azure 虚拟网络之前，Azure 平台本身将实施两层安全性：

1.    **DDoS 保护**：DDoS 保护是一个 Azure 物理网络层，可防范 Azure 平台本身受到大规模的基于 Internet 的攻击。 这些攻击利用多个“傀儡”节点尝试瘫痪 Internet 服务。 Azure 使用一个强大的 DDoS 保护网来保护所有入站、出站和跨 Azure 区域连接。 此 DDoS 保护层没有用户可配置的属性，客户也无法对其进行访问。 DDoS 保护层保护 Azure 平台免受大规模攻击，并监视出站流量和跨 Azure 区域流量。 使用 VNet 上的网络虚拟设备，可以通过客户配置其他弹性层，防止受到规模较小且不会妨碍平台级保护的攻击。 举一个进行 DDoS 攻击的例子：如果某个面向 Internet 的 IP 地址遭受大规模的 DDoS 攻击，Azure 会检测攻击源，清理带攻击性的流量，阻止其访问预期的目标。 在几乎所有情况下，受攻击的终结点不会受攻击的影响。 在极少数情况下，如果某个终结点受影响，则只影响到受攻击终结点的流量，到其他终结点的流量不受影响。 因此，其他客户和服务也不会受该攻击的影响。 必须注意的是，Azure DDoS 仅监视大规模攻击。 可能会出现平台级保护阈值尚未超出，而特定服务却已不堪重负的情况。 例如，在 Azure 平台级 DDoS 保护将某个威胁记录在册之前，DDoS 攻击可能已导致单个 A0 IIS 服务器上的网站脱机。

2.    **公共 IP 地址**：使用公共 IP 地址（通过服务终结点、公共 IP 地址、应用程序网关以及其他 Azure 功能启用，以便将路由到用户资源的公共 IP 地址呈现给 Internet），云服务或资源组就可以公开公共 Internet IP 地址和端口。 终结点使用网络地址转换 (NAT) 将流量路由到 Azure 虚拟网络上的内部地址和端口。 此路径是外部流量进入虚拟网络的主要方式。 可以对公共 IP 地址进行配置，确定可以传入哪种流量、如何在虚拟网络上转换该流量以及要将它路由到何处。

流量进入虚拟网络后，有许多功能将派上用场。 Azure 虚拟网络是客户连接其工作负荷的基础，也是应用基本网络层安全性的所在之处。 它是客户在 Azure 中的专用网络（虚拟网络覆盖），并具有以下功能和特性：

* **流量隔离**：虚拟网络是 Azure 平台上的流量隔离边界。 一个虚拟网络中的虚拟机 (VM) 无法与不同虚拟网络中的 VM 直接通信，即使这两个虚拟网络是由同一个客户所创建。 隔离是一个非常关键的属性，可确保客户 VM 与通信在虚拟网络中保持私密性。

>[!NOTE]
>流量隔离仅针对虚拟网络的*入站* 流量。 默认情况下，从 VNet 到 Internet 的出站流量是允许的，但也可根据 NSG 的需要将其阻止。
>
>

* **多层拓扑**：虚拟网络允许客户通过分配子网并为工作负荷的不同元素或“层”指定独立地址空间，来定义多层拓扑。 这些逻辑分组和拓扑可让客户根据工作负荷类型来定义不同的访问策略，以及控制各层之间的流量。
* **跨界连接**：客户可以在虚拟网络和多个本地站点或 Azure 中的其他虚拟网络之间创建跨界连接。 客户可以使用 VNet 对等互连、Azure VPN 网关、第三方网络虚拟设备或 ExpressRoute 来构造连接。 Azure 支持使用标准 IPsec/IKE 协议和 ExpressRoute 专用连接的站点到站点 (S2S) VPN。
* **NSG** 允许客户根据所需的粒度（网络接口、单个 VM 或虚拟子网）创建规则 (ACL)。 客户可以从客户网络上的系统，通过跨界连接或直接 Internet 通信来允许或拒绝虚拟网络内的工作负荷，以控制访问。
* **UDR** 和 **IP 转发**允许客户定义虚拟网络中不同层之间的通信路径。 客户可以部署防火墙、IDS/IPS 和其他虚拟设备，并通过这些安全设备来路由网络流量，以实施安全边界策略、审核和检查。
* Azure 应用商店中的**网络虚拟设备**：Azure 应用商店和 VM 映像库中提供了防火墙、负载均衡器和 IDS/IPS 等安全设备。 客户可将这些设备部署到其虚拟网络，特别是安全边界（包括外围网络子网），以实现多层安全网络环境。

下图演示了如何使用这些功能在 Azure 中构造外围网络体系结构：

![5]

## <a name="perimeter-network-characteristics-and-requirements"></a>外围网络特征与要求
外围网络是网络的前端，直接应对来自 Internet 的通信。 传入数据包应该在流经安全设备（例如防火墙、IDS 和 IPS）之后才到达后端服务器。 来自工作负荷的 Internet 绑定数据包可能也会流经外围网络中的安全设备，经过策略实施、检查和审核之后，才离开网络。 此外，外围网络可以托管客户虚拟网络与本地网络之间的跨界 VPN 网关。

### <a name="perimeter-network-characteristics"></a>外围网络特征
参考上图，良好外围网络的一些特征如下：

* 面向 Internet：
  * 外围网络子网本身面向 Internet，直接与 Internet 通信。
  * 公共 IP 地址、VIP 和/或服务终结点将 Internet 流量传递给前端网络和设备。
  * 来自 Internet 的入站流量先通过安全设备，然后再通过前端网络上其他资源。
  * 如果已启用出站安全性，则流量先通过安全设备（最后一个步骤），然后才传递到 Internet。
* 受保护的网络：
  * Internet 与核心基础结构之间没有直接的路径。
  * 连往核心基础结构的通道必须遍历 NSG、防火墙或 VPN 设备等安全设备。
  * 其他设备不得桥接 Internet 和核心基础结构。
  * 在面向 Internet 的外围网络和面向受保护网络边界的外围网络上（例如上图所示的两个防火墙图标），安全设备实际上可能是单个虚拟设备，但有不同的规则或接口处理每个边界。 例如，一个在逻辑上分隔的物理设备，负责处理外围网络两个边界的负载。
* 其他常见做法与约束：
  * 工作负荷不得存储业务关键型信息。
  * 只有获授权的管理员才能访问和更新外围网络配置与部署。

### <a name="perimeter-network-requirements"></a>外围网络要求
若要实现这些特征，请遵循有关需要满足哪些虚拟网络要求才能实现成功外围网络的指导：

* **子网体系结构：**指定虚拟网络，使整个子网专门作为外围网络，与相同虚拟网络中的其他子网分开。 这样分隔可确保外围网络与其他内部或专用子网层之间的流量流经防火墙或 IDS/IPS 虚拟设备。  若要将此流量转发到虚拟设备，需在边界子网上使用用户定义的路由。
* **NSG：**外围网络子网本身应该打开以允许与 Internet 通信，但这不表示客户应该绕过 NSG。 请遵循常用的安全实践，将曝露于 Internet 的网络接触面减到最小。 管制允许访问部署的远程地址范围，或特定的应用程序协议和打开的端口。 但在某些情况下，不可能实现完全锁定。 例如，如果客户在 Azure 中有外部网站，则外围网络应该允许从任何公共 IP 地址传入的 Web 请求，但只应该打开以下 Web 应用程序端口：TCP:80 和/或 TCP:443。
* **路由表：**外围网络子网本身必须能够直接与 Internet 通信，但不应该允许未通过防火墙或安全设备，就直接与后端或本地网络之间相互通信。
* **安全设备配置：**为了路由和检查外围网络与受保护网络其余部分之间的数据包，安全设备（例如防火墙、IDS 和 IPS 设备）可以有多重主目录。 外围网络和后端子网可能有独立的 NIC。 外围网络中的 NIC 将使用相应的 NSG 和路由表直接与 Internet 相互通信。 对于相应的后端子网，连接到后端子网的 NIC 有更受限制的 NSG 和路由表。
* **安全设备功能：**部署在外围网络中的安全设备通常执行以下功能：
  * 防火墙：对传入请求实施防火墙规则或访问控制策略。
  * 威胁检测和预防：检测并缓解来自 Internet 的恶意攻击。
  * 审核和日志记录：维护详细记录供审核和分析。
  * 反向代理：将传入请求重定向到相应的后端服务器。 此重定向涉及到将前端设备（通常是防火墙）上的目标地址映射并转换成后端服务器地址。
  * 正向代理：提供 NAT，并审核从虚拟网络内部发起的与 Internet 的通信。
  * 路由器：转发虚拟网络内的传入流量和跨子网流量。
  * VPN 设备：充当跨界 VPN 网关，用于在本地网络上的客户与 Azure 虚拟网络之间进行跨界 VPN 连接。
  * VPN 服务器：接受连接到 Azure 虚拟网络的 VPN 客户端。

> [!TIP]
> 使以下两个组保持隔离：有权访问外围网络安全设备的个人，以及获授权成为应用程序开发、部署或操作管理员的个人。 将这些组隔开可区分权责，防止个人绕过应用程序安全与网络安全控制机制。
>
>

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>构建网络边界时的疑问
对于本部分中提到的“网络”，除非专门说明，否则是指订阅管理员创建的专用 Azure 虚拟网络。 该术语不是指 Azure 中的基础物理网络。

此外，Azure 虚拟网络通常用于扩展传统的本地网络。 站点到站点或 ExpressRoute 混合网络解决方案可与外围网络体系结构合并。 在构建网络安全边界时，此混合链接是一个重要的考虑因素。

构建包含外围网络和多个安全边界的网络时，必须解决以下三个重要问题。

#### <a name="1-how-many-boundaries-are-needed"></a>1) 需要多少个边界？
第一个决策点是确定在特定情况下需要多少个安全边界：

* 单个边界：虚拟网络与 Internet 之间的前端外围网络上有一个边界。
* 两个边界：一个在外围网络网络的 Internet 端，另一个在 Azure 虚拟网络中的外围网络子网与后端子网之间。
* 三个边界：一个在外围网络的 Internet 端，一个在外围网络与后端子网之间，还有一个在后端子网与本地网络之间。
* N 个边界：可变数量。 可以在给定网络中应用的安全边界没有数量限制，具体取决于安全要求。

需要的边界类型和数量根据公司的风险承受度和实施的特定方案而有所不同。 通常由组织内的多个团队共同达成此决策，这些团队通常包括风险和合规性小组、网络和平台小组，以及应用程序开发小组。 了解安全、所涉及的数据及运用技术的人员，在此决策上应该有发言权，以确保对每种实施方案表达适当的安全立场。

> [!TIP]
> 应该尽可能以最少的边界来满足特定情况下的安全要求。 边界越多，操作和故障排除就越困难，后续管理多个边界策略时的管理开销也越大。 不过，边界不足也会增大风险。 获得平衡至关重要。
>
>

![6]

上图从较高层面显示了网络的三个安全边界。 这些边界位于外围网络与 Internet 之间、Azure 前端与后端专用子网之间，以及 Azure 后端子网与本地企业网络之间。

#### <a name="2-where-are-the-boundaries-located"></a>2) 边界位于何处？
确定边界数量后，下一个决策点就是在何处实施它们。 通常有三种选择：

* 使用基于 Internet 的中介服务（例如基于云的 Web 应用程序防火墙，本文中未介绍）
* 使用 Azure 中的本机功能和/或网络虚拟设备
* 使用本地网络上的物理设备

在纯粹的 Azure 网络上，选项包括本机 Azure 功能（例如 Azure 负载均衡器），或由丰富的合作伙伴生态系统提供的网络虚拟设备（例如检查点防火墙）。

如果 Azure 与本地网络之间需要边界，安全设备可以位于连接的任何一端（或两端）。 因此，必须确定安全设备的位置。

上图中，Internet 到外围网络及前端到后端的边界完全在 Azure 内，而且必须是本机 Azure 功能或网络虚拟设备。 在 Azure（后端子网）与企业网络之间的边界上，安全设备可以在 Azure 端或本地端，甚至是两端的设备组合。 任一选项都可能有明显的优点和缺点，必须慎重考虑。

例如，在本地网络端使用现有物理安全设备的优点是不需要添置设备， 而只需要重新配置。 但缺点是，所有流量必须从 Azure 传回到本地网络才能被安全设备看到。 因此，如果将 Azure 到 Azure 流量强制传回本地网络来实施安全策略，则可能会造成明显的延迟，影响应用程序性能和用户体验。

#### <a name="3-how-are-the-boundaries-implemented"></a>3) 如何实施边界？
每个安全边界可能有不同的功能要求（例如，外围网络的 Internet 端需要 IDS 和防火墙规则，但外围网络与后端子网之间只需要 ACL）。 将根据方案和安全要求决定要使用的具体设备（或设备数量）。 在以下部分中，示例 1、2 和 3 讨论了一些可用的选项。 了解 Azure 本机网络功能和 Azure 中由合作伙伴生态系统提供的设备可以展示大量的选项来满足几乎任何方案。

另一个重要的实施决策点就是如何连接本地网络与 Azure。 是否应使用 Azure 虚拟网关或网络虚拟设备？ 以下部分（示例 4、5 和 6）详细描述了这些选项。

此外，Azure 中可能需要虚拟网络之间的流量。 将来会添加这些方案。

知道上述问题的答案后，可以参考[快速开始](#fast-start)部分确定最适合特定方案的示例。

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>示例：使用 Azure 虚拟网络构建安全边界
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>示例 1：构建外围网络以使用 NSG 帮助保护应用程序
[返回“快速开始”](#fast-start) | [此示例的详细构建说明][Example1]

[![7]][7]

#### <a name="environment-description"></a>环境描述
在本示例中，有一个包含以下资源的订阅：

- 单个资源组
- 包含下述两个子网的虚拟网络：“FrontEnd”和“BackEnd”
- 应用到这两个子网的网络安全组
- 一个代表应用程序 Web 服务器的 Windows Server（“IIS01”）
- 两个代表应用程序后端服务器的 Windows Server（“AppVM01”、“AppVM02”）
- 一个代表 DNS 服务器的 Windows Server（“DNS01”）
- 与应用程序 Web 服务器关联的公共 IP

有关脚本和 Azure Resource Manager 模板，请参阅[详细构建说明][Example1]。

#### <a name="nsg-description"></a>NSG 描述
此示例将构建一个 NSG 组，然后加载六个规则。

> [!TIP]
> 一般而言，应该先创建特定的“允许”规则，然后创建常规“拒绝”规则。 给定的优先级确定了要先评估哪些规则。 发现要向流量应用哪个特定规则后，不需要评估后续规则。 可以朝入站或出站方向（从子网的角度看）应用 NSG 规则。
>
>

以声明性的方式为入站流量构建以下规则：

1. 允许内部 DNS 流量（端口 53）。
2. 允许从 Internet 到任何虚拟机的 RDP 流量（端口 3389）。
3. 允许从 Internet 到 Web 服务器 (IIS01) 的 HTTP 流量（端口 80）。
4. 允许从 IIS01 到 AppVM1 的任何流量（所有端口）。
5. 拒绝从 Internet 到整个虚拟网络（两个子网）的任何流量（所有端口）。
6. 拒绝从前端子网到后端子网的任何流量（所有端口）。

将这些规则绑定到每个子网后，如果有从 Internet 到 Web 服务器的入站 HTTP 请求，将应用规则 3（允许）和规则 5（拒绝）。 但由于规则 3 具有较高的优先级，因此只应用规则 3 并忽略规则 5。 这样就会允许 HTTP 请求传往 Web 服务器。 如果相同的流量尝试传往 DNS01 服务器，则会先应用规则 5（拒绝），因此不允许该流量传递到服务器。 规则 6（拒绝）阻止前端子网与后端子网对话（规则 1 和 4 允许的流量除外）。 此规则集可在攻击者入侵前端的 Web 应用程序时保护后端网络。 攻击者只能对后端的“受保护”网络进行有限度的访问（只能访问 AppVM01 服务器上公开的资源）。

有一个默认出站规则可允许流量外流到 Internet。 在此示例中，我们允许出站流量，且未修改任何出站规则。 如果两个方向的流量都要锁定，则需要用户定义的路由（参阅示例 3）。

#### <a name="conclusion"></a>结束语
此示例是一种隔离后端子网与输入流量的方式，相当直截了当。 有关详细信息，请参阅[详细构建说明][Example1]。 这些说明包括：

* 如何使用经典 PowerShell 脚本构建此外围网络。
* 如何使用 Azure Resource Manager 模板构建此外围网络。
* 每个 NSG 命令的详细描述。
* 展示如何在每一层允许或拒绝流量的详细流量传送方案。


### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>示例 2：构建外围网络以使用防火墙和 NSG 帮助保护应用程序
[返回“快速开始”](#fast-start) | [此示例的详细构建说明][Example2]

[![8]][8]

#### <a name="environment-description"></a>环境描述
在本示例中，有一个包含以下资源的订阅：

* 单个资源组
* 包含下述两个子网的虚拟网络：“FrontEnd”和“BackEnd”
* 应用到这两个子网的网络安全组
* 一个与前端子网连接的网络虚拟设备（在本例中为防火墙）
* 一个代表应用程序 Web 服务器的 Windows Server（“IIS01”）
* 两个代表应用程序后端服务器的 Windows Server（“AppVM01”、“AppVM02”）
* 一个代表 DNS 服务器的 Windows Server（“DNS01”）

有关脚本和 Azure Resource Manager 模板，请参阅[详细构建说明][Example2]。

#### <a name="nsg-description"></a>NSG 描述
此示例将构建一个 NSG 组，然后加载六个规则。

> [!TIP]
> 一般而言，应该先创建特定的“允许”规则，然后创建常规“拒绝”规则。 给定的优先级确定了要先评估哪些规则。 发现要向流量应用哪个特定规则后，不需要评估后续规则。 可以朝入站或出站方向（从子网的角度看）应用 NSG 规则。
>
>

以声明性的方式为入站流量构建以下规则：

1. 允许内部 DNS 流量（端口 53）。
2. 允许从 Internet 到任何虚拟机的 RDP 流量（端口 3389）。
3. 允许从 Internet 到网络虚拟设备（防火墙）的任何流量（所有端口）。
4. 允许从 IIS01 到 AppVM1 的任何流量（所有端口）。
5. 拒绝从 Internet 到整个虚拟网络（两个子网）的任何流量（所有端口）。
6. 拒绝从前端子网到后端子网的任何流量（所有端口）。

将这些规则绑定到每个子网后，如果有从 Internet 到防火墙的入站 HTTP 请求，将应用规则 3（允许）和规则 5（拒绝）。 但由于规则 3 具有较高的优先级，因此只应用规则 3 并忽略规则 5。 这样就会允许 HTTP 请求传往防火墙。 如果相同的流量尝试传往 IIS01 服务器，即使是在前端子网上，也会应用规则 5（拒绝），因此不允许该流量传递到服务器。 规则 6（拒绝）阻止前端子网与后端子网对话（规则 1 和 4 允许的流量除外）。 此规则集可在攻击者入侵前端的 Web 应用程序时保护后端网络。 攻击者只能对后端的“受保护”网络进行有限度的访问（只能访问 AppVM01 服务器上公开的资源）。

有一个默认出站规则可允许流量外流到 Internet。 在此示例中，我们允许出站流量，且未修改任何出站规则。 如果两个方向的流量都要锁定，则需要用户定义的路由（参阅示例 3）。

#### <a name="firewall-rule-description"></a>防火墙规则描述
应在防火墙上创建转发规则。 本示例只将 Internet 流量入站路由到防火墙，再传送到 Web 服务器，因此只需要一个转发网络地址转换 (NAT) 规则。

转发规则接受任何到达防火墙并尝试访问 HTTP（端口 80，若为 HTTPS 则为 443）的入站源地址。 此地址将从防火墙的本地接口发出，并重定向到 IP 地址为 10.0.1.5 的 Web 服务器。

#### <a name="conclusion"></a>结束语
此示例是一种使用防火墙保护应用程序并隔离后端子网与输入流量的方式，相当直截了当。 有关详细信息，请参阅[详细构建说明][Example2]。 这些说明包括：

* 如何使用经典 PowerShell 脚本构建此外围网络。
* 如何使用 Azure Resource Manager 模板构建此示例。
* 每个 NSG 命令和防火墙规则的详细描述。
* 展示如何在每一层允许或拒绝流量的详细流量传送方案。

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg"></a>示例 3：构建外围网络，以使用防火墙、UDR 和 NSG 帮助保护网络
[返回“快速开始”](#fast-start) | [此示例的详细构建说明][Example3]

[![9]][9]

#### <a name="environment-description"></a>环境描述
在本示例中，有一个包含以下资源的订阅：

* 单个资源组
* 一个虚拟网络，其中包含三个子网：“SecNet”、“FrontEnd”和“BackEnd”
* 一个与 SecNet 子网连接的网络虚拟设备（在本例中为防火墙）
* 一个代表应用程序 Web 服务器的 Windows Server（“IIS01”）
* 两个代表应用程序后端服务器的 Windows Server（“AppVM01”、“AppVM02”）
* 一个代表 DNS 服务器的 Windows Server（“DNS01”）

有关脚本和 Azure Resource Manager 模板，请参阅[详细构建说明][Example3]。

#### <a name="udr-description"></a>UDR 描述
默认情况下，以下系统路由定义为：

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal 始终是一个或多个已定义地址前缀，组成该特定网络的虚拟网络（也就是说，根据每个特定虚拟网络的定义方式，不同的虚拟网络有不同的前缀）。 其余系统路由是静态的，表中列出了其默认值。

此示例中创建了两个路由表，分别用于前端和后端子网。 已在每个表中加载了适用于给定子网的静态路由。 在此示例中，每个表包含三个路由，它们负责通过防火墙定向所有流量 (0.0.0.0/0)（下一跃点 = 虚拟设备 IP 地址）：

1. 定义了不带下一跃点的本地子网流量以允许本地子网流量绕过防火墙。
2. 将下一跃点定义为防火墙的虚拟网络流量。 这个下一跃点将覆盖允许本地虚拟网络流量直接路由的默认规则。
3. 将带有下一跃点的所有剩余流量 (0/0) 定义为防火墙。

> [!TIP]
> UDR 中没有本地子网条目会导致本地子网通信中断。
>
> * 在我们的示例中，将 10.0.1.0/24 指向 VNETLocal 至关重要！ 否则，数据包在离开 Web 服务器 (10.0.1.4) 发往另一本地服务器（例如 10.0.1.25）时会失败，因为会被发送到 NVA。 NVA 会将其发送给子网，而子网又会将其重新发送给 NVA，形成一个无限循环。
> * 造成路由循环的几率在多 NIC 设备中通常会更高，因为这些设备会连接到不同的子网，后者在传统的本地设备中很常见。
>
>

路由表在创建后必须绑定到其子网。 前端子网路由表在创建并绑定到子网后将如以下输出所示：

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

> [!NOTE]
> 现在可以将 UDR 应用到在其上连接了 ExpressRoute 线路的网关子网。
>
> 以下示例 3 和 4 中显示了有关使用 ExpressRoute 或站点到站点网络启用外围网络的示例。
>
>

#### <a name="ip-forwarding-description"></a>IP 转发描述
IP 转发是 UDR 的随附功能。 IP 转发是虚拟设备上的一项设置，使虚拟设备能够接收不是要专门传送到该设备的流量，再将流量转发到其最终目标。

例如，如果 AppVM01 对 DNS01 服务器发出请求，UDR 会将此流量路由到防火墙。 在启用 IP 转发后，目标为 DNS01 (10.0.2.4) 的流量被设备 (10.0.0.4) 所接受，然后转发到其最终目标 (10.0.2.4)。 如果防火墙上未启用 IP 转发，则即使路由表将防火墙用作下一跃点，流量也不会被设备所接受。 若要使用虚拟设备，必须记得一同启用 IP 转发和 UDR。

#### <a name="nsg-description"></a>NSG 描述
此示例将构建一个 NSG 组，然后加载单个规则。 此组接着只绑定到前端和后端子网（不绑定到 SecNet）。 以声明性的方式构建以下规则：

* 拒绝从 Internet 到整个虚拟网络（所有子网）的任何流量（所有端口）。

尽管本示例使用了 NSG，但它的主要用途是作为防止人为配置错误的第二道防线。 目标是阻止从 Internet 传送到前端或后端子网的所有入站流量。 流量只应流经 SecNet 子网前往防火墙（并于合适时流往前端或后端子网）。 此外，在配置 UDR 规则后，确实能够流往前端或后端子网的流量都将被定向到防火墙（得益于 UDR）。 防火墙会将此流量视为非对称流量，并且会丢弃出站流量。 因此，有三个安全层在保护子网：

* 在任何 FrontEnd 或 BackEnd NIC 上都没有公共 IP 地址。
* NSG 拒绝来自 Internet 的流量。
* 防火墙丢弃非对称流量。

本示例中的 NSG 有一个有趣的特点，那就是它只包含一个规则，用于拒绝由 Internet 流往整个虚拟网络（包含安全子网）的流量。 不过，由于 NSG 只绑定到前端和后端子网，因此不对流往安全子网的入站流量处理此规则。 因此，流量流向安全子网。

#### <a name="firewall-rules"></a>防火墙规则
应在防火墙上创建转发规则。 由于防火墙会阻止或转发所有入站、出站和虚拟网络内部流量，因此需要配置许多防火墙规则。 此外，所有入站流量都将抵达安全服务公共 IP 地址（在不同端口上），并由防火墙进行处理。 最佳实践是先绘制逻辑流量图，再设置子网和防火墙规则，以避免事后进行修改。 下图是此示例中防火墙规则的逻辑视图：

![10]

> [!NOTE]
> 根据所用的网络虚拟设备，管理端口会有所不同。 本示例中提到的 Barracuda NextGen 防火墙使用端口 22、801 和 807。 请参阅设备供应商的文档来查找用于管理所用设备的确切端口。
>
>

#### <a name="firewall-rules-description"></a>防火墙规则描述
在上面的逻辑图中，安全子网没有显示，因为防火墙是该子网上的唯一资源。 此图显示的是防火墙规则以及这些规则在逻辑上是如何允许或拒绝流量的流动，而不是实际的路由路径。 此外，针对 RDP 流量选择的外部端口均为较高范围的端口 (8014-8026)，并且选择时大致遵循了本地 IP 地址的最后两个八位字节以方便阅读（例如，本地服务器地址 10.0.1.4 与外部端口 8014 相关联）。 不过可以使用任何更高的无冲突端口。

在此示例中，我们需要 7 种类型的规则：

* 外部规则（针对入站流量）：
  1. 防火墙管理规则：此应用重定向规则允许流量传递到网络虚拟设备的管理端口。
  2. RDP 规则（针对每个 Windows 服务器）：这四个规则（每台服务器一个）允许通过 RDP 管理单个服务器。 根据所用的网络虚拟设备功能，也可将四项 RDP 规则合并为一项规则。
  3. 应用程序流量规则：有两个这样的规则，第一个针对前端 Web 流量，第二个针对后端流量（例如 Web 服务器流往数据层）。 这些规则的配置取决于网络体系结构（服务器的放置位置）和流量流动行为（流量的流动方向，以及使用的端口）。
     * 第一个规则允许实际的应用程序流量抵达应用程序服务器。 其他规则所考虑的是安全、管理等方面的事项，应用程序流量规则用于允许外部用户或服务访问应用程序。 就本示例而言，端口 80 上有单个 Web 服务器。 因此单个防火墙应用程序规则将流往外部 IP 的入站流量，并重定向到 Web 服务器的内部 IP 地址。 重定向的流量会话经过 NAT 转换后流往内部服务器。
     * 第二个规则是后端规则，用于允许 Web 服务器通过任何端口与 AppVM01 服务器（而非 AppVM02）对话。
* 内部规则（针对虚拟网络内部流量）
  1. 出站到 Internet 规则：此规则允许来自任何网络的流量传递到选定的网络。 此规则通常是防火墙上已有的但处于禁用状态的默认规则。 对于本示例，应启用此规则。
  2. DNS 规则：此规则只允许 DNS（端口 53）流量传递到 DNS 服务器。 在此环境中，阻止了大部分由前端流往后端的流量。 此规则专门允许来自任何本地子网的 DNS。
  3. 子网到子网规则：此规则允许后端子网上的任何服务器连接到前端子网上的任何服务器（但不允许反向连接）。
* 防故障规则（针对不符合上述任一规则的流量）：
  1. 拒绝所有流量规则：请始终将此拒绝规则用作最终规则（在优先级方面），这样，如果流量的流动行为无法符合上述任何规则，此规则就会将其丢弃。 此规则为默认规则，通常已设置到位并处于活动状态。 此规则通常不需做任何修改。

> [!TIP]
> 为简化本示例，允许第二个应用程序流量规则上的任何端口。 在真实方案中，应使用具体的端口和地址范围，以降低此规则的攻击面。
>
>

创建好上述规则后，请务必检查每个规则的优先级，确保可根据需要允许或拒绝流量。 在本示例中，规则设置了优先顺序。

#### <a name="conclusion"></a>结束语
与前面的示例相比，此示例是以更复杂但更完整的方式来保护和隔离网络的方法。 （示例 2 只保护应用程序，示例 1 只隔离子网）。 这项设计允许你同时监视两个方向的流量，而不只是保护入站应用程序服务器，同时还对此网络上的所有服务器实施网络安全策略。 此外，根据使用的设备，还能实现全面的流量审核和感知。 有关详细信息，请参阅[详细构建说明][Example3]。 这些说明包括：

* 如何使用经典 PowerShell 脚本构建此示例外围网络。
* 如何使用 Azure Resource Manager 模板构建此示例。
* 每个 UDR、NSG 命令和防火墙规则的详细描述。
* 展示如何在每一层允许或拒绝流量的详细流量传送方案。

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn"></a>示例 4：使用站点到站点虚拟设备 VPN 添加混合连接
[返回“快速开始”](#fast-start) | 详细构建说明即将发布

[![11]][11]

#### <a name="environment-description"></a>环境描述
可将使用网络虚拟设备 (NVA) 的混合网络添加到示例 1、2 或 3 中所述的任何外围网络类型。

如上图所示，通过 Internet 的 VPN 连接（站点到站点）用于通过 NVA 将本地网络连接到 Azure 虚拟网络。

> [!NOTE]
> 如果使用 ExpressRoute 并启用“Azure 公共对等”选项，则应创建静态路由。 此静态路由会路由到企业 Internet 外部的 NVA VPN IP 地址，而无需通过 ExpressRoute 连接。 ExpressRoute“Azure 公共对等”选项所需的 NAT 很可能会中断 VPN 会话。
>
>

配置好 VPN 之后，NVA 将成为所有网络和子网的“中心”。 防火墙转发规则确定允许、通过 NAT 转换、重定向或丢弃哪些流量（即使是对于本地网络与 Azure 之间传送的流量）。

应该谨慎考虑流量传送，因为根据具体的用例，这种设计模式可能会优化或退化传送的性能。

使用示例 3 中构建的环境并添加站点到站点 VPN 混合网络连接后会生成以下设计：

[![12]][12]

本地路由器或与 VPN 的 NVA 兼容的任何其他网络设备是 VPN 客户端。 此物理设备负责发起和保持与 NVA 的 VPN 连接。

就 NVA 而言，网络在逻辑上就像四个不同的“安全区域”，NVA 上的规则统管这些区域之间的流量：

![13]

#### <a name="conclusion"></a>结束语
将站点到站点 VPN 混合网络连接添加到 Azure 虚拟网络能够安全地将本地网络扩展到 Azure。 使用 VPN 连接时，流量将会加密并通过 Internet 路由。 本示例中的 NVA 提供一个中心位置用于实施和管理安全策略。 有关详细信息，请参阅详细构建说明（即将发布）。 这些说明包括：

* 如何使用 PowerShell 脚本构建此示例外围网络。
* 如何使用 Azure Resource Manager 模板构建此示例。
* 展示此设计中流量如何传送的详细流量方案。

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway"></a>示例 5：使用站点到站点 Azure VPN 网关添加混合连接
[返回“快速开始”](#fast-start) | 详细构建说明即将发布

[![14]][14]

#### <a name="environment-description"></a>环境描述
可将使用 Azure VPN 网关的混合网络添加到示例 1 或 2 中所述的任何一种外围网络。

如上图所示，通过 Internet 的 VPN 连接（站点到站点）用于通过 Azure VPN 网关将本地网络连接到 Azure 虚拟网络。

> [!NOTE]
> 如果使用 ExpressRoute 并启用“Azure 公共对等”选项，则应创建静态路由。 此静态路由会路由到企业 Internet 外部的 NVA VPN IP 地址，而无需通过 ExpressRoute WAN。 ExpressRoute“Azure 公共对等”选项所需的 NAT 很可能会中断 VPN 会话。
>
>

下图显示了此示例中的两个网络边缘。 在第一个边缘上，NVA 和 NSG 控制 Azure 网络内部及 Azure 与 Internet 之间的流量。 第二个边缘是 Azure VPN 网关，它是本地与 Azure 之间一个独立且隔离的网络边缘。

应该谨慎考虑流量传送，因为根据具体的用例，这种设计模式可能会优化或退化传送的性能。

使用示例 1 中构建的环境并添加站点到站点 VPN 混合网络连接后会生成以下设计：

[![15]][15]

#### <a name="conclusion"></a>结束语
将站点到站点 VPN 混合网络连接添加到 Azure 虚拟网络能够安全地将本地网络扩展到 Azure。 使用本机 Azure VPN 网关时，流量将由 IPSec 加密并通过 Internet 路由。 此外，使用 Azure VPN 网关的成本也较低（不像第三方 NVA 一样需要额外的许可成本）。 在不使用任何 NVA 的示例 1 中，此选项是最经济实惠的方案。 有关详细信息，请参阅详细构建说明（即将发布）。 这些说明包括：

* 如何使用 PowerShell 脚本构建此示例外围网络。
* 如何使用 Azure Resource Manager 模板构建此示例。
* 展示此设计中流量如何传送的详细流量方案。

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>示例 6：使用 ExpressRoute 添加混合连接
[返回“快速开始”](#fast-start) | 详细构建说明即将发布

[![16]][16]

#### <a name="environment-description"></a>环境描述
可将使用 ExpressRoute 专用对等连接的混合网络添加到示例 1 或 2 中所述的任一种外围网络。

如上图所示，ExpressRoute 专用对等在本地网络与 Azure 虚拟网络之间提供直接连接。 流量只在服务提供商网络和 Microsoft Azure 网络上流动，永远不会传到 Internet。

> [!TIP]
> 通过 ExpressRoute 使企业网络流量脱离 Internet。 同时，还能符合 ExpressRoute 提供商的服务级别协议。 Azure 网关在 ExpressRoute 上的传递速度高达 10 Gbps，而使用站点到站点 VPN 时，Azure 网关的最大吞吐量为 200 Mbps。
>
>

如下图所示，使用此选项时，环境现在有两个网络边缘。 NVA 和 NSG 控制 Azure 网络内部及 Azure 与 Internet 之间的流量，而网关是本地与 Azure 之间一个独立且隔离的网络边缘。

应该谨慎考虑流量传送，因为根据具体的用例，这种设计模式可能会优化或退化传送的性能。

使用示例 1 中构建的环境并添加 ExpressRoute 混合网络连接后会生成以下设计：

[![17]][17]

#### <a name="conclusion"></a>结束语
添加 ExpressRoute 专用对等网络连接能够以安全、低延迟、高性能的方式将本地网络扩展到 Azure。 此外，如本示例中所示，使用本机 Azure 网关还能降低成本（不像第三方 NVA 一样需要额外的许可）。 有关详细信息，请参阅详细构建说明（即将发布）。 这些说明包括：

* 如何使用 PowerShell 脚本构建此示例外围网络。
* 如何使用 Azure Resource Manager 模板构建此示例。
* 展示此设计中流量如何传送的详细流量方案。

## <a name="references"></a>参考
### <a name="helpful-websites-and-documentation"></a>有用的网站和文档
* 使用 Azure Resource Manager 访问 Azure
* 使用 PowerShell 访问 Azure：[https://docs.microsoft.com/powershell/azureps-cmdlets-docs/](/powershell/azure/overview)
* 虚拟网络文档：[https://docs.microsoft.com/azure/virtual-network/](https://docs.microsoft.com/azure/virtual-network/)
* 网络安全组文档：[https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg](virtual-network/virtual-networks-nsg.md)
* 用户定义的路由文档：[https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview](virtual-network/virtual-networks-udr-overview.md)
* Azure 虚拟网关：[https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)
* 站点到站点 VPN：[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell](vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* ExpressRoute 文档（请务必阅读“入门”和“操作指南”部分）：[https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "安全选项流程图"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Azure 安全功能"
[3]: ./media/best-practices-network-security/dmzcorporate.png "企业网络中的外围网络"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Azure 安全体系结构"
[5]: ./media/best-practices-network-security/dmzazure.png "Azure 虚拟网络中的外围网络"
[6]: ./media/best-practices-network-security/dmzhybrid.png "包含三个安全边界的混合网络"
[7]: ./media/best-practices-network-security/example1design.png "使用 NSG 的入站外围网络"
[8]: ./media/best-practices-network-security/example2design.png "使用 NVA 和 NSG 的入站外围网络"
[9]: ./media/best-practices-network-security/example3design.png "使用 NVA、NSG 和 UDR 的双向外围网络"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "防火墙规则逻辑视图"
[11]: ./media/best-practices-network-security/example3designoptions.png "包含连接 NVA 的混合网络的外围网络"
[12]: ./media/best-practices-network-security/example4designs2s.png "使用站点到站点 VPN 的连接 NVA 的外围网络"
[13]: ./media/best-practices-network-security/example4networklogical.png "从 NVA 角度观察的逻辑网络"
[14]: ./media/best-practices-network-security/example5designoptions.png "包含站点到站点连接 Azure 网关的混合网络的外围网络"
[15]: ./media/best-practices-network-security/example5designs2s.png "使用站点到站点 VPN 的 Azure 网关的外围网络"
[16]: ./media/best-practices-network-security/example6designoptions.png "包含使用 ExpressRoute 连接 Azure 网关的混合网络的外围网络"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "包含使用 ExpressRoute 连接的 Azure 网关的外围网络"

<!--Link References-->
[TrustCenter]: https://azure.microsoft.com/support/trust-center/compliance/
[Example1]: ./virtual-network/virtual-networks-dmz-nsg.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md

