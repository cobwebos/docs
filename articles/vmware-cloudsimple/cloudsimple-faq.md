---
title: FAQ-Azure VMware 解决方案 (按 CloudSimple)
description: Azure VMware 解决方案的常见问题解答 (按 CloudSimple)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ed1d28bbc58256b4c73e5a8f6662dba505cce98
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563149"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>CloudSimple 有关 VMware 解决方案的常见问题

## <a name="cloudsimple-service"></a>CloudSimple 服务

**什么是 Azure VMware 解决方案 (CloudSimple)？**

Azure VMware 解决方案在几分钟内, CloudSimple 将 VMware 工作负荷转换和扩展到专用的专用云。 CloudSimple 负责预配、管理基础结构, 并协调本地和 Azure 之间的工作负荷。 由于你的应用程序在本地和 Azure 中运行完全相同, 因此你可以从云的弹性和服务中获益, 而不会重新架构你的应用程序的复杂性。 CloudSimple 使用云消耗模型降低了总拥有成本, 该模型提供按需预配、即用即付和容量优化。  有关功能、优点和方案, 请参阅[Azure 上的 VMware 解决方案的 CloudSimple](cloudsimple-vmware-solutions-overview.md) 。

**什么是 CloudSimple 私有云？**

CloudSimple 私有云是专用的专用云, 它包含部署在 Azure 位置 Microsoft Azure 基础结构 (硬件和数据中心空间) 上的高性能计算、存储和网络环境。  私有云提供本机 VMware "平台即服务"。 在 VMware 术语中, 每个私有云只包含 vCenter Server 的一个实例。 VCenter Server 管理一个或多个 vSphere 群集中包含的多个 ESXi 节点, 以及相应的虚拟 SAN (vSAN) 存储。 CloudSimple 服务可以在你的 Azure 订阅中包含多个私有云。  有关更多详细信息, 请参阅[私有云概述](cloudsimple-private-cloud.md)。

**CloudSimple 服务提供在何处？**

CloudSimple 在美国东部和美国西部区域提供, 即将推出其他区域。

**如何实现为 CloudSimple 启用我的订阅？**

你可以在上[azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com)联系你的 Microsoft 帐户代表, 为 CloudSimple 服务启用订阅。 在要为其启用 CloudSimple 服务的电子邮件中提供订阅 ID。  

**如何实现访问 CloudSimple 门户？**

可以从 Azure 门户访问 CloudSimple 门户。  有关详细信息, 请参阅[Azure 门户中的 "通过 CloudSimple 门户访问 VMware 解决方案](access-cloudsimple-portal.md)"。

**如何实现增加私有云的容量？**

若要增加容量, 请从 Azure 门户购买其他节点, 然后使用节点从 CloudSimple 门户扩展私有云。  可以向现有的 vSphere 群集添加更多节点, 或将其添加到新的 vSphere 群集。  有关详细信息, 请参阅[扩展 CloudSimple 私有云](expand-private-cloud.md)。

**在维护期间, 我的私有云会发生什么情况？**

CloudSimple 在计划的维护间隔之前的几天提供通知。  维护是以一种无中断的方式进行的, 目的是确保私有云的可用性。  维护可以是以下类型:

* **CloudSimple 基础结构**。  CloudSimple 基础结构设计为具有高可用性。  在此类型的维护间隔期间, 每次更新一个冗余组件以避免任何服务中断。 你可以保持对私有云 vCenter、所有虚拟机、从私有云的 internet 连接以及连接到本地或 Azure 的访问权限。
* **CloudSimple 门户**。 在此类型的维护间隔期间, CloudSimple 门户中的某些功能可能已禁用或不可访问。  维护间隔之前的通知包括进行维护时的功能限制的详细信息。

## <a name="connectivity"></a>连接性

**CloudSimple 区域网络的连接选项有哪些？**

CloudSimple 提供下列连接选项用于连接到 CloudSimple 区域网络。 可以同时使用多个选项。

* **从本地数据中心到 CloudSimple 区域网络的 ExpressRoute 连接**。 这是一种高速、低延迟、安全的专用连接, 使用 Global Reach 将本地 ExpressRoute 线路桥接到 CloudSimple ExpressRoute 线路。 有关设置连接的说明, 请参阅[使用 ExpressRoute 从本地连接到 CloudSimple](on-premises-connection.md)。
* **从 Azure 虚拟网络到 CloudSimple 区域网络的 ExpressRoute 连接**。 这是一种高速、低延迟、安全的专用连接, 使用虚拟网络网关将 Azure 上的虚拟网络桥接到 CloudSimple ExpressRoute 线路。 有关设置连接的说明, 请参阅[使用 ExpressRoute 将 CloudSimple 私有云环境连接到 Azure 虚拟网络](azure-expressroute-connection.md)。
* **从本地数据中心到 CloudSimple 区域网络的站点到站点 VPN 连接**。 这是从本地 VPN 设备到你的 CloudSimple 私有云区域的安全虚拟专用网络。  有关详细信息, 请参阅[在 CloudSimple 网络上设置 VPN 网关](vpn-gateway.md)。

**如何实现连接到私有云？**

可以在 CloudSimple 门户中查看私有云的详细信息。 若要连接到对应于私有云的 vCenter, 请首先验证是否已使用站点到站点 VPN、点到站点 VPN 或 ExpressRoute 建立网络连接。 然后, 从 Azure 门户启动 CloudSimple 门户, 并单击主页或私有云详细信息页上的 "**启动 VSphere 客户端**"。

**ExpressRoute 线路的优点是什么？**

Azure ExpressRoute 线路是一种高速、低延迟、安全的连接。  CloudSimple 为每个客户提供专用的 ExpressRoute 线路。  使用此线路, 可以从本地或 Azure 订阅建立安全连接。

**连接到 CloudSimple 的网络开销是多少？是否在 CloudSimple 与 Azure 之间或跨区域应用任何出口费用？**

无 CloudSimple 的网络出口费用。  Azure 标准费率适用于来自虚拟网络或本地 ExpressRoute 线路的任何出口流量。

## <a name="networking"></a>网络

**哪些网络功能可用于我的私有云？**

可以预配 Vlan (及其子网) 和防火墙表, 并分配映射到在私有云中运行的虚拟机的公共 IP 地址。 有关网络功能的详细信息, 请参阅[vlan 和子网概述](cloudsimple-vlans-subnets.md)、[防火墙表概述](cloudsimple-firewall-tables.md)和[公共 IP 地址概述](cloudsimple-public-ip-address.md)。

**如何实现为私有云中的应用程序设置不同的子网？**

可以从 CloudSimple 门户在私有云上创建 Vlan。  创建 VLAN 后, 可以使用 VLAN 在私有云 vCenter 上创建分布式端口组, 并创建连接到分布式端口组的虚拟机。  可以为 VLAN/子网启用防火墙表, 并定义防火墙规则来保护网络流量。

**哪些防火墙设置可用于我的私有云？**

你可以配置北南部和东西部流量的规则。  规则在防火墙表中定义。  防火墙表可以附加到私有云上的 Vlan。  有关详细信息, 请参阅为[私有云设置防火墙表和规则](firewall.md)。

**能否在私有云环境中将公共 IP 地址分配给 Vm？**

在 CloudSimple 门户中, 可以分配新的公共 IP 地址, 并将其与虚拟机或设备的专用 IP 地址相关联。  你还可以创建新的防火墙规则或应用现有防火墙规则, 以允许来自门户中特定端口和 IP 地址的流量。 有关详细信息, 请参阅为[私有云环境分配公共 IP 地址](public-ips.md)。

## <a name="security"></a>安全性

**我在 CloudSimple 上有哪些安全选项？**

CloudSimple 提供了以下安全功能来保护私有云环境:

* **静态数据加密**。 可以加密位于私有云中的 vSAN 存储中的静态数据。 vSAN 支持可以部署在 Azure vNet 或本地环境中的外部密钥管理服务器。  有关详细信息, 请参阅为[CloudSimple 私有云配置 vSAN 加密](vsan-encryption.md)。
* **网络安全**。 使用防火墙规则控制网络流量, 这些规则应用于私有云和 internet、私有云和本地环境或私有云的子网中。
* **安全专用连接**。 本地网络和 Azure 订阅之间建立了安全的专用连接。

## <a name="compute"></a>计算

**可用的主机类型有哪些？**

CloudSimple 提供以下主机类型:

* **CS28 节点:** CPU: 2x 2.2 GHz, 总共28核, 48 HT。  RAM：256 GB。  存储：1600 GB NVMe 缓存, 5760 GB 数据 (全部刷新)。 网络：2x25Gbe NIC
* **CS36 节点:** CPU 2x 2.3 GHz, 36 核心总数, 72 HT。  RAM：512 GB。  存储：3200 GB NVMe 缓存 11520 GB 数据 (全部刷新)。  网络：2x25Gbe NIC

**所有硬件故障的处理方式是什么？**

CloudSimple 平台和我们的服务运营团队将不断监视所有 CloudSimple 基础结构。  如果检测到硬件故障, 则会将一个新节点添加到私有云, 并删除失败的节点。

## <a name="storage"></a>存储

**私有云支持哪种类型的存储？**

CloudSimple 提供每个私有云的所有闪存 VMware vSAN 存储。  每个 vSphere 都创建有自己的 vSAN 数据存储。  有关详细信息, 请参阅[私有云 VMware 组件-vSAN 存储](vmware-components.md#vsan-storage)。

**是否支持数据加密？**
是的。  你可以在私有云上设置 vSAN 存储, 以使用部署在本地或 Azure 上的密钥管理服务器 (KMS) 来加密 vSAN 上存储的数据。

**如何处理失败的磁盘？**

CloudSimple 连续监视私有云的所有硬件组件。  如果检测到磁盘故障或磁盘被标识为失败 (基于试探法), 则会将一个新节点自动添加到私有云。  将从私有云中删除具有失败或失败磁盘的节点。

## <a name="vmware"></a>VMware

**如何实现在本地执行应用程序和数据的大规模上传或迁移？**

CloudSimple 提供本机 VMware vSphere 解决方案。  用于批量数据迁移的所有 VMware 工具都可用于私有云。  选项包括：

* VMware HCX, 用于大容量迁移数据。
* 使用 Storage vMotion 从本地到 CloudSimple 的数据的冷迁移。

**是否可以安装任何 VMware 工具？**

CloudSimple 提供本机 VMware vSphere 解决方案。  用于管理本地 vSphere 环境的所有 VMware 工具都可以在 CloudSimple 上使用。  CloudSimple 支持用于安装 VMware 工具的自带许可证 (BYOL) 模型。

**更新和升级的管理方式是什么？**

CloudSimple 以无中断的方式管理和更新私有云的所有基础结构组件。  VMware 或基础结构供应商发布的所有更新和安全修补程序将在由 CloudSimple 限定后立即进行更新。

CloudSimple 不会对安装在私有云上的应用程序进行升级或更新。

## <a name="azure-integration"></a>Azure 集成

**支持哪些 Azure 服务？**

CloudSimple 提供与 Azure 上的订阅的 Azure ExpressRoute 连接。  订阅中运行的所有服务都可以连接到私有云。  示例包括：

* **Azure Active Directory**作为 CloudSimple vCenter 的标识源。
* 用于存储私有云中的备份、映像和其他数据的**Azure 存储**。
* 具有跨公有云和私有云的应用程序体系结构的**混合应用程序**。  例如, 可以在 Azure 中创建 webservers, 用于访问私有云上的应用程序和数据库服务器。
* 适用于 VMware 上运行的工作负荷的**azure monitor**和**azure 安全中心**支持日志记录、性能指标和安全管理。

**如何实现将 VMware 租户映射到 Azure？**

CloudSimple 提供了从 Azure 门户管理私有云上的 VMware Vm 的独特功能。  使用所需的资源约束配置的 vCenter 资源池可以由全局管理员映射到你的订阅。  

**Azure 有哪些授权权益？**

使用 CloudSimple, 可以利用 Azure 混合使用权益, 并节省高达 90% 的许可证。 此权益可节省你对 Microsoft 许可证的投资, 并可降低相对于其他云解决方案的总体拥有成本。 还会获得 Windows Server 2008 和 Microsoft SQL Server 2008 的扩展安全更新。  自带许可 (BYOL) 模型有助于降低常见应用 (例如 Veeam 和 Zerto) 的成本。  
