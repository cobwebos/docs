---
title: FAQ-VMware 解决方案 (按 CloudSimple)
description: Azure VMware 解决方案的常见问题解答 (按 CloudSimple)
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 80380a1c33927029e000e59a5834f297340f5be3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816226"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>CloudSimple 有关 VMware 解决方案的常见问题

CloudSimple 提供有关 Azure VMware 解决方案的常见问题和解答, 可帮助你了解该服务及其使用方式。 问题和解答分为以下几个类别:

* CloudSimple 服务
* 连接性
* 网络
* 安全性
* 计算
* 存储
* VMware
* Azure 集成
 
## <a name="cloudsimple-service"></a>CloudSimple 服务

**什么是 Azure VMware 解决方案 (CloudSimple)？**

Azure VMware 解决方案在几分钟内, CloudSimple 将 VMware 工作负荷转换和扩展到专用的专用云。 解决方案预配、管理基础结构, 并协调本地和 Azure 之间的工作负荷。 由于你的应用程序在本地和 Azure 中运行完全相同, 因此你可以从云的弹性和服务中获益, 而不会重新架构你的应用程序的复杂性。 CloudSimple 使用云消耗模型降低了总拥有成本, 该模型提供按需预配、即用即付和容量优化。 有关功能、优点和方案, 请参阅[什么是 Azure VMware 解决方案的 CloudSimple？](cloudsimple-vmware-solutions-overview.md)。

**什么是 CloudSimple 私有云？**

你预配专用专用云, 其中包含部署在 Azure 位置 Microsoft Azure 基础结构 (硬件和数据中心空间) 上的高性能计算、存储和网络环境。 私有云提供本机 VMware 平台即服务。 在 VMware 术语中, 每个私有云只包含 vCenter Server 的一个实例。 VCenter Server 管理一个或多个 vSphere 群集中包含的多个 ESXi 节点, 以及相应的 vSAN 存储。 CloudSimple 服务可以在你的 Azure 订阅中包含多个私有云。 有关私有云的详细信息, 请参阅[私有云概述](cloudsimple-private-cloud.md)。

**CloudSimple 服务位于何处？**

CloudSimple 在美国东部和美国西部地区提供。

**如何实现为 CloudSimple 启用我的订阅？**

请联系你的 Microsoft 帐户[azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com)代表, 以便为 CloudSimple 服务启用订阅。 在要为其启用 CloudSimple 服务的电子邮件中提供订阅 ID。 

**如何实现访问 CloudSimple 门户？**

可以从 Azure 门户访问 CloudSimple 门户。 有关如何访问 CloudSimple 门户的信息, 请参阅[Azure 门户中的 "通过 CloudSimple 门户访问 VMware 解决方案](https://docs.azure.cloudsimple.com/access-cloudsimple-portal)"。

**如何实现增加私有云的容量？**

从 Azure 门户预配节点并从 CloudSimple 门户扩展私有云。 你可以通过向现有的 vSphere 群集添加节点或通过创建新的 vSphere 群集来扩展你的私有云。 有关该过程的信息, 请参阅[Expand a CloudSimple private cloud](https://docs.azure.cloudsimple.com/expand-private-cloud)。

**在维护期间, 我的私有云会发生什么情况？**

CloudSimple 提供定期通知天数, 然后计划维护。 维护是以无中断的方式进行的, 以确保私有云的可用性。 维护可以是以下类型:

- **CloudSimple 基础结构**:CloudSimple 基础结构设计为具有高可用性。 在维护期间, 可通过一次更新一个冗余组件来确保私有云的连接性和可用性, 而不会产生任何影响。 你可以访问私有云 vCenter、所有虚拟机、从私有云到 internet 的连接, 以及连接到本地或 Azure。
- **CloudSimple 门户**:在维护期间, CloudSimple 门户中的某些功能可能无法访问或已禁用。 维护通知包括有关可在门户上完成的操作的信息。

## <a name="connectivity"></a>连接性

**我的 CloudSimple 区域网络的连接选项有哪些？**

CloudSimple 提供了三种不同的连接选项来连接到 CloudSimple 区域网络。 所有三个选项都可以一起使用:

- 从本地数据中心到 CloudSimple 区域网络的 Azure ExpressRoute 连接:一种高速低延迟安全专用连接, 使用 Global Reach 将本地 ExpressRoute 线路与 CloudSimple ExpressRoute 线路桥接。 若要设置连接, 请参阅[使用 ExpressRoute 从本地连接到 CloudSimple](https://docs.azure.cloudsimple.com/on-premises-connection)。
- 从 Azure 虚拟网络到 CloudSimple 区域网络的 ExpressRoute 连接:一种高速、低延迟安全的专用连接, 使用虚拟网络网关将 Azure 上的虚拟网络与 CloudSimple ExpressRoute 线路桥接。 若要设置连接, 请参阅[使用 ExpressRoute 将 CloudSimple 私有云环境连接到 Azure 虚拟网络](https://docs.azure.cloudsimple.com/azure-expressroute-connection)。
- 从本地数据中心到 CloudSimple 区域网络的站点到站点 VPN 连接:从本地 VPN 设备到你的 CloudSimple 私有云区域的安全虚拟专用网络。 若要设置连接, 请参阅[在本地网络和 CloudSimple 私有云之间设置 VPN 连接](https://docs.azure.cloudsimple.com/set-up-vpn)。

**如何实现连接到私有云？**

可以在 CloudSimple 门户中查看私有云的详细信息。 若要连接到对应于私有云的 vCenter, 请确保使用站点到站点连接、点到站点连接或 ExpressRoute 建立网络连接。 然后, 从 Azure 门户启动 CloudSimple 门户。 在主页上或在私有云详细信息页上选择 "**启动 VSphere 客户端**"。

**ExpressRoute 线路的优点是什么？**

Azure ExpressRoute 线路提供高速、低延迟的安全连接。 CloudSimple 为每个客户提供专用的 ExpressRoute 线路。 使用此线路, 你可以从本地和 Azure 订阅建立安全连接。

**连接到 CloudSimple 的网络开销是多少？从 CloudSimple 到 Azure 是否有任何出口费用？地区是否有任何出口费用？**

网络出口不收取任何费用。 Azure 标准费率适用于来自虚拟网络或本地 ExpressRoute 线路的任何出口流量。

## <a name="networking"></a>网络

**哪些网络功能可用于我的私有云？**

可以预配 Vlan 及其子网和防火墙表。 可以分配公共 IP 地址并映射到在私有云中运行的虚拟机。 有关详细信息, 请参阅[vlan 和子网概述](cloudsimple-vlans-subnets.md)、[防火墙表概述](cloudsimple-firewall-tables.md)和[公共 IP 地址概述](cloudsimple-public-ip-address.md)。

**如何实现为私有云中的应用程序设置不同的子网？**

可以从 CloudSimple 门户在私有云上创建 Vlan。 创建 VLAN 后, 可以使用 VLAN 在私有云 vCenter 上创建一个分布式端口组, 并创建连接到分布式端口组的虚拟机。 可以启用 VLAN 或子网的防火墙表, 并定义防火墙规则来保护网络流量。

**哪些防火墙设置可用于我的私有云？**

你可以配置北南部和东西部流量的规则。 规则在防火墙表中定义。 防火墙表可以附加到私有云上的 Vlan。 有关安装过程, 请参阅为[私有云设置防火墙表和规则](https://docs.azure.cloudsimple.com/firewall)。

**能否在私有云环境中为 Vm 分配公共 IP 地址？**

在 CloudSimple 门户中, 你可以轻松地分配新的公共 IP 地址, 并将其与虚拟机或设备的专用 IP 地址相关联。 你还可以创建新的防火墙规则或应用现有防火墙规则, 以允许来自门户中特定端口和特定 IP 地址集的流量。 有关安装过程, 请参阅为[私有云环境分配公共 IP 地址](https://docs.azure.cloudsimple.com/public-ips)。

## <a name="security"></a>安全性

**我在 CloudSimple 上有哪些安全选项？**

CloudSimple 私有云提供了以下安全功能来保护私有云环境:

- **静态数据加密:** 可以加密位于私有云中的 vSAN 存储中的静态数据。 vSAN 支持可以部署在 Azure 虚拟网络或本地环境中的外部密钥管理服务器。 有关详细信息, 请参阅为[CloudSimple 私有云配置 vSAN 加密](https://docs.azure.cloudsimple.com/vsan-encryption)。
- **网络安全:** 使用防火墙规则控制从 internet、本地和私有云的子网中的私有云流动的网络流量。
- **安全专用连接:** 本地网络与 Azure 订阅之间的安全专用连接。

## <a name="compute"></a>计算

**可用的主机类型有哪些？**

CloudSimple 提供了两种主机类型:

* **CS28 节点**:CPU: 2x 2.2 GHz, 总共28核, 48 HT。 RAM：256 GB。 存储：1600 GB NVMe 缓存, 5760-GB 数据 (全部闪烁)。 网络：2x25Gbe NIC。
* **CS36 节点**:CPU 2x 2.3 GHz, 36 核心总数, 72 HT。 RAM：512 GB。 存储：3200-GB NVMe 缓存 11520-GB 数据 (全部刷新)。 网络：2x25Gbe NIC。

**硬件故障如何处理？**

所有 CloudSimple 基础结构都由 CloudSimple 平台及其服务操作团队持续监视。 如果检测到硬件故障, 则会将一个新节点添加到私有云。 将删除失败的节点以确保私有云的高可用性。

## <a name="storage"></a>存储

**私有云支持哪种类型的存储？**

CloudSimple 提供每个私有云的**所有闪存 VMware vSAN 存储**。 每个 vSphere 都创建有自己的 vSAN 数据存储。 有关详细信息, 请参阅[私有云 VMware 组件-vSAN 存储](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage)。

**是否支持数据加密？**
是的。 你可以将你的私有云上的 vSAN 存储设置为使用密钥管理服务器 (KMS), 该服务器部署在本地或 Azure 上, 用于加密在 vSAN 上存储的数据。

**如何处理失败的磁盘？**

CloudSimple 监视持续监视私有云的所有硬件组件。 如果检测到磁盘故障, 或者根据试探法将磁盘标识为失败, 则会自动将新节点添加到私有云。 将从私有云中删除具有失败或失败磁盘的节点。

## <a name="vmware"></a>VMware

**如何实现在本地执行应用程序和数据的大规模上传和迁移？**

CloudSimple 提供本机 VMware vSphere 解决方案。 用于批量数据迁移的任何工具都可以与 CloudSimple 私有云一起使用。 可用的选项包括:

- VMware HCX, 用于大容量迁移数据。
- 使用 Storage vMotion 从本地到 CloudSimple 的冷迁移数据。

**是否可以安装任何 VMware 工具？**

CloudSimple 提供本机 VMware vSphere 解决方案。 用于在本地管理 vSphere 环境的任何工具都可以在 CloudSimple 上使用。 CloudSimple 支持用于安装 VMware 工具的自带许可证 (BYOL) 模型。

**更新和升级的管理方式是什么？**

CloudSimple 以无缝的无中断方式管理和更新私有云的所有基础结构组件。 VMware 或基础结构供应商发布的任何更新或安全修补程序都将在 CloudSimple 限定后立即进行更新。

CloudSimple 不会对安装在私有云上的应用程序进行升级或更新。

## <a name="azure-integration"></a>Azure 集成

**支持哪些 Azure 服务？**

CloudSimple 提供 azure ExpressRoute 连接到 Azure 上的订阅。 在订阅中运行的任何服务都具有与私有云的网络连接, 并且可以连接到私有云。 示例:

- **Azure Active Directory**：使用 Azure Active Directory 作为 CloudSimple vCenter 的标识源。
- **Azure 存储**：使用存储从私有云中存储备份、映像和其他数据。
- **混合应用程序**:你可以创建跨公有云和私有云的应用程序体系结构。 例如, 你可以在 Azure 中创建可访问 CloudSimple 私有云上的应用程序和数据库服务器的 web 服务器。
- **Azure Monitor**和**Azure 安全中心**:在 VMware 上运行的工作负荷可以使用监视和安全中心进行日志记录、性能指标和安全管理。

**如何实现将 VMware 租户映射到 Azure？**

CloudSimple 提供了从 Azure 门户管理私有云上的 VMware Vm 的独特功能。 使用所需的资源约束配置的 vCenter 资源池可以由全局管理员映射到你的订阅。 

**Azure 有哪些授权权益？**

借助 CloudSimple, 你可以利用 Azure 混合权益和节省高达 90% 的许可证, 以节省你对 Microsoft 许可证的投资并降低你的总拥有成本 (与其他云相比)。 还会获得 Windows Server 2008 和 Microsoft SQL Server 2008 的扩展安全更新。 对于常见应用 (如 Veeam、Zerto 等), 将 BYOL 的成本降低到云中。 
