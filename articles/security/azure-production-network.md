---
title: Azure 生产网络
description: 本文提供有关 Azure 生产网络的一般说明。
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 710792c890c3e48fc54507f93eeaee529ca839f8
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114022"
---
# <a name="the-azure-production-network"></a>Azure 生产网络
Azure 生产网络的用户包括访问其自己的 Azure 应用程序的外部客户以及管理生产网络的 Azure 内部支持人员。 本文介绍了用于与 Azure 生产网络建立连接的安全接入方法和保护机制。

## <a name="internet-routing-and-fault-tolerance"></a>Internet 路由和容错
全局冗余的内部和外部 Azure 域名服务 (DNS) 基础结构与多个主要和辅助 DNS 服务器群集相结合，可提供容错功能。 与此同时，其他 Azure 网络安全控件（如 NetScaler）可预防分布式拒绝服务 (DDoS) 攻击并保护 Azure DNS 服务的完整性。

Azure DNS 服务器位于多个数据中心设施。 Azure DNS 实现整合了辅助和主要 DNS 服务器的层次结构，可公开解析 Azure 客户域名。 域名通常解析成 CloudApp.net 地址，其中包装了客户服务的虚拟 IP (VIP) 地址。 Azure 的独特之处在于，与租户转换的内部专用 IP (DIP) 地址对应的 VIP 由负责该 VIP 的 Microsoft 负载均衡器执行。

Azure 托管在分布于美国境内各处的 Azure 数据中心，且基于一流路由平台构建，可实施可靠、可缩放体系结构标准。 其中包含如下重要功能：

- 基于多协议标签交换 (MPLS) 的流量工程，在发生服务中断时，可提供高效的链路利用率和妥善的服务降级。
- 以“需求加一”(N+1) 冗余体系结构或更佳的方式实施网络。
- 从外部看，数据中心由专用的高带宽网络线路提供服务，这些线路以冗余方式将资产连接到全球 1,200 多个 Internet 服务提供商的多个对等互连点。 连接后可提供超过 2,000 GB/秒 (GBps) 的边缘容量。

由于 Microsoft 在数据中心之间拥有自身的网络线路，因此，这些属性有助于 Azure 产品/服务实现 99.9% 以上的网络可用性，而无需与传统的第三方 Internet 服务提供商合作。

## <a name="connection-to-production-network-and-associated-firewalls"></a>连接到生产网络和关联的防火墙
Azure 网络 Internet 流量流策略将流量定向到美国境内最靠近的区域数据中心内的 Azure 生产网络。 由于 Azure 生产数据中心拥有一致的网络体系结构和硬件，下面的流量流说明同样适用于所有数据中心。

将 Azure 的 Internet 流量路由到最近的数据中心后，将与接入的路由器建立连接。 这些接入路由器用于隔离 Azure 节点与客户实例化 VM 之间的流量。 位于接入位置和边缘位置的网络基础结构设备是应用入口和出口筛选器的边界点。 这些路由器已通过分层的访问口控制列表 (ACL) 进行配置，在必要时可以筛选不需要的网络流量并应用流量速率限制。 ACL 允许的流量将路由到负载均衡器。 分配路由器只允许 Microsoft 批准的 IP 地址，可提供反欺骗功能，并建立使用 ACL 的 TCP 连接。

外部负载均衡设备位于接入路由器后方，执行从 Internet 可路由 IP 到 Azure 内部 IP 的网络地址转换 (NAT)。 设备还将数据包路由到有效的生产内部 IP 和端口，并且它们充当保护机制，限制内部生产网络地址空间的公开。

默认情况下，Microsoft 针对传输到客户 Web 浏览器的所有流量（包括登录和由此产生的所有流量）强制实施安全超文本传输协议 (HTTPS)。 使用 TLS v1.2 能够为传送的流量建立安全隧道。 接入路由器和核心路由器上的 ACL 确保流量的源符合预期。

与传统的安全体系结构相比，此体系结构的重要区别在于没有专用的硬件防火墙、专用的入侵检测或预防设备，或者在与 Azure 生产环境建立连接之前通常需要的其他安全设备。 客户通常预期 Azure 网络中存在这些硬件防火墙设备；但是，Azure 中并未采用任何此类设备。 这些安全功能内置在运行 Azure 环境的软件中，提供包括防火墙功能在内的可靠多层安全机制，这几乎是 Azure 独有的特色。 此外，如上图所示，关键安全设备的边界范围和关联衍生功能更易于管理和清点，因为它们由运行 Azure 的软件管理。

## <a name="core-security-and-firewall-features"></a>核心安全性和防火墙功能
Azure 在各个级别实现可靠的软件安全性和防火墙功能来强制执行传统环境中通常需要的安全功能，以保护核心安全授权边界。

### <a name="azure-security-features"></a>Azure 安全功能
Azure 在生产网络内实现基于主机的软件防火墙。 核心 Azure 环境中包含多种核心安全性和防火墙功能。 这些安全功能反映了 Azure 环境中的深层防御策略。 Azure 中的客户数据受以下防火墙的保护：

**虚拟机监控程序防火墙（数据包筛选器）**：在虚拟机监控程序中实现此防火墙并由结构控制器 (FC) 代理配置。 此防火墙可保护在 VM 内运行的租户免受未经授权的访问。 默认情况下，创建 VM 时，将阻止所有流量，然后 FC 代理在筛选器中添加规则和例外，以允许获得授权的流量。

此处对两类规则进行了编程：

- **计算机配置或基础结构规则**：默认情况下，将阻止所有通信。 但也存在例外情况，可允许 VM 发送和接收动态主机配置协议 (DHCP) 通信和 DNS 信息，并将流量发送到“公共”Internet 并出站到 FC 群集与 OS 激活服务器内的其他 VM。 由于 VM 允许的传出目标列表不包括 Azure 路由器子网和其他 Microsoft 属性，因此这些规则将充当它们的一道防御层。
- **角色配置文件规则**：根据租户的服务模型定义入站 ACL。 例如，如果某个租户在某个特定 VM 的端口 80 上有一个 Web 前端，则会向所有 IP 地址开放端口 80。 如果 VM 上正在运行某个辅助角色，则只向同一租户中的 VM 开放该辅助角色。

**本机主机防火墙**：Azure Service Fabric 和 Azure 存储在本机 OS 上运行，其中没有虚拟机监控程序，因此会使用上述两组规则配置 Windows 防火墙。

**主机防火墙**：主机防火墙保护运行虚拟机监控程序的主机分区。 可以通过编程方式对规则进行设置，只允许 FC 和跳转盒在特定端口上与主机分区通信。 其他例外包括允许 DHCP 响应和 DNS 回复。 Azure 使用计算机配置文件，其中包括主机分区的防火墙规则模板。 还有一种主机防火墙例外情况，可允许 VM 通过特定协议/端口与主机组件、网络服务器和元数据服务器进行通信。

**来宾防火墙**：来宾 OS 的 Windows 防火墙部分，可由客户在客户 VM 和存储中配置。

内置于 Azure 功能中的其他安全功能包括：

- 基础结构组件，可为其分配来自 DIP 的 IP 地址。 Internet 上的攻击者无法将流量发往这些地址，因其无法访问 Microsoft。 Internet 网关路由器筛选仅发往内部地址的数据包，因此这些数据包不会进入生产网络。 只有负载均衡器才是接受定向到 VIP 的流量的组件。
- 在任何给定的场景下，所有内部节点上实现的防火墙在安全体系结构方面都存在三个主要注意事项：

   - 防火墙位于负载均衡器后方，接受来自任何位置的数据包。 这些数据包可在外部公开，对应于传统外围防火墙中打开的端口。
   - 防火墙仅接受来自一组有限地址的数据包。 此考虑是针对 DDoS 攻击的防御性深入战略的一部分。 此类连接以加密方式进行身份验证。
   - 仅可从选定的内部节点访问防火墙。 防火墙仅接受源 IP 地址枚举列表中的数据包，所有这些都是 Azure 网络中的 DIP。 例如，企业网络中出现的攻击可能会将请求定向到这些地址，但将阻止攻击，除非数据包的源地址是 Azure 网络内枚举列表中的某个地址。
     - 外围的接入路由器会阻止发往 Azure 网络中某个地址的出站数据包，因为它使用配置的静态路由。

## <a name="next-steps"></a>后续步骤
若要详细了解 Microsoft 如何保护 Azure 基础结构，请参阅：

- [Azure 设施、场地和物理安全性](azure-physical-security.md)
- [Azure 基础结构可用性](azure-infrastructure-availability.md)
- [Azure 信息系统的组件和边界](azure-infrastructure-components.md)
- [Azure 网络体系结构](azure-infrastructure-network.md)
- [Azure SQL 数据库安全功能](azure-infrastructure-sql.md)
- [Azure 生产运营和管理](azure-infrastructure-operations.md)
- [Azure 基础结构监视](azure-infrastructure-monitoring.md)
- [Azure 基础结构完整性](azure-infrastructure-integrity.md)
- [Azure 客户数据保护](azure-protection-of-customer-data.md)
