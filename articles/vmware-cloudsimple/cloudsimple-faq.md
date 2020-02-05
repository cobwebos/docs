---
title: FAQ-Azure VMware 解决方案（AVS）
description: Azure VMware 解决方案（AVS）常见问题
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c3808491c84f6c76a51c914aac6ee5e5ee370970
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025055"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-avs"></a>有关 AVS 的 VMware 解决方案常见问题

## <a name="avs-service"></a>AVS 服务

**什么是 Azure VMware 解决方案（AVS）？**

Azure VMware 解决方案（AVS）可以在几分钟内转换 Azure 工作负荷并将其扩展到专用于 Azure 的专用云。 AVS 负责预配、管理基础结构，并协调本地和 Azure 之间的工作负荷。 由于你的应用程序在本地和 Azure 中运行完全相同，因此你可以从云的弹性和服务中获益，而不会重新架构你的应用程序的复杂性。 AVS 使用云消耗模型降低了总拥有成本，该模型提供按需预配、即用即付和容量优化。 请参阅[Azure 上的 Azure 上的 VMware 解决方案](cloudsimple-vmware-solutions-overview.md)，了解功能、优点和方案。

**什么是 AVS 私有云？**

AVS 私有云是专用的专用云，由部署在 Azure 位置 Microsoft Azure 基础结构（硬件和数据中心空间）上的高性能计算、存储和网络环境组成。 AVS 私有云提供本机 VMware "平台即服务"。 在 VMware 术语中，每个 AVS 私有云只包含 vCenter Server 的一个实例。 VCenter Server 管理一个或多个 vSphere 群集中包含的多个 ESXi 节点，以及相应的虚拟 SAN （vSAN）存储。 AVS 服务可以在 Azure 订阅中包含多个 AVS 私有云。 有关更多详细信息，请参阅[AVS 私有云概述](cloudsimple-private-cloud.md)。

**可在何处使用 AVS 服务？**

在美国东部、美国西部和西欧地区提供了 AVS，其中包含其他区域即将推出。

**如何实现为 AVS 启用我的订阅？**

你可以在[azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com)联系你的 Microsoft 帐户代表，为你的 AVS 服务启用订阅。 在要为其启用 AVS 服务的电子邮件中提供订阅 ID。 

**如何实现访问 AVS 门户？**

可以从 Azure 门户访问 AVS 门户。 有关详细信息，请参阅[从 Azure 门户访问 VMware 解决方案（AVS）门户](access-cloudsimple-portal.md)。

**如何实现提高 AVS 私有云的容量？**

若要增加容量，请从 Azure 门户购买其他节点，然后使用节点从 AVS 门户扩展你的 AVS 私有云。 可以向现有的 vSphere 群集添加更多节点，或将其添加到新的 vSphere 群集。 有关详细信息，请参阅[扩展 AVS 私有云](expand-private-cloud.md)。

**在维护期间，我的 AVS 私有云会发生什么情况？**

AVS 在计划的维护间隔之前的几天提供通知。 维护以无中断的方式进行，以确保你的 AVS 私有云的可用性。 维护可以是以下类型：

* **AVS 基础结构**。 AVS 基础结构设计为具有高可用性。 在此类型的维护间隔期间，每次更新一个冗余组件以避免任何服务中断。 你可以保持对你的 AVS 私有云 vCenter、所有虚拟机、来自 AVS 私有云的 internet 连接以及连接到本地或 Azure 的访问权限。
* **AVS 门户**。 在此类型的维护间隔期间，AVS 门户上的某些功能可能已禁用或无法访问。 维护间隔之前的通知包括进行维护时的功能限制的详细信息。

## <a name="connectivity"></a>连接性

**AVS 区域网络的连接选项有哪些？**

AVS 提供以下连接选项来连接到 AVS 区域网络。 可以同时使用多个选项。

* **从本地数据中心到 AVS 区域网络的 ExpressRoute 连接**。 这是一种高速、低延迟、安全的专用连接，使用 Global Reach 将本地 ExpressRoute 线路桥接到 AVS ExpressRoute 线路。 有关设置连接的说明，请参阅[使用 ExpressRoute 从本地连接到 AVS](on-premises-connection.md)。
* **从 Azure 虚拟网络到 AVS 区域网络的 ExpressRoute 连接**。 这是一种高速、低延迟、安全的专用连接，使用虚拟网络网关将 Azure 上的虚拟网络桥接到 AVS ExpressRoute 线路。 有关设置连接的说明，请参阅[使用 ExpressRoute 将你的 AVS 私有云环境连接到 Azure 虚拟网络](azure-expressroute-connection.md)。
* **从本地数据中心到 AVS 区域网络的站点到站点 VPN 连接**。 这是从本地 VPN 设备到 AVS 私有云区域的安全虚拟专用网络。 有关详细信息，请参阅[在 AVS 网络上设置 VPN 网关](vpn-gateway.md)。

**如何实现连接到 AVS 私有云？**

可以在 AVS 门户中查看 AVS 私有云的详细信息。 若要连接到与你的 AVS 私有云对应的 vCenter，请首先验证是否已使用站点到站点 VPN、点到站点 VPN 或 ExpressRoute 建立了网络连接。 然后，从 Azure 门户启动 AVS 门户，然后单击 "主页" 或 "AVS 私有云详细信息" 页上的 "**启动 VSphere 客户端**"。

**ExpressRoute 线路的优点是什么？**

Azure ExpressRoute 线路是一种高速、低延迟、安全的连接。 AVS 为每个客户提供专用的 ExpressRoute 线路。 使用此线路，可以从本地或 Azure 订阅建立安全连接。

**连接到 AVS 有哪些网络开销？在 AVS 和 Azure 之间还是跨区域，是否收取任何出口费用？**

网络出口无 AVS 收费。 Azure 标准费率适用于来自虚拟网络或本地 ExpressRoute 线路的任何出口流量。

## <a name="networking"></a>联网

**哪些网络功能可用于我的 AVS 私有云？**

可以预配 Vlan （及其子网）和防火墙表，并分配映射到你的 AVS 私有云中运行的虚拟机的公共 IP 地址。 有关网络功能的详细信息，请参阅[vlan 和子网概述](cloudsimple-vlans-subnets.md)、[防火墙表概述](cloudsimple-firewall-tables.md)和[公共 IP 地址概述](cloudsimple-public-ip-address.md)。

**如何实现在我的 AVS 私有云中为我的应用程序设置不同的子网？**

可从 AVS 门户在 AVS 私有云上创建 Vlan。 创建 VLAN 后，可以使用 VLAN 在 AVS 私有云 vCenter 上创建分布式端口组，并创建连接到分布式端口组的虚拟机。 可以为 VLAN/子网启用防火墙表，并定义防火墙规则来保护网络流量。

**哪些防火墙设置可用于我的 AVS 私有云？**

你可以配置北南部和东西部流量的规则。 规则在防火墙表中定义。 防火墙表可以附加到 AVS 私有云上的 Vlan。 有关详细信息，请参阅[设置适用于 AVS 私有云的防火墙表和规则](firewall.md)。

**能否在我的 AVS 私有云环境中将公共 IP 地址分配给 Vm？**

在 AVS 门户中，可以分配新的公共 IP 地址，并将其与虚拟机或设备的专用 IP 地址相关联。 你还可以创建新的防火墙规则或应用现有防火墙规则，以允许来自门户中特定端口和 IP 地址的流量。 有关详细信息，请参阅为[AVS 私有云环境分配公共 IP 地址](public-ips.md)。

## <a name="security"></a>安全性

**AVS 上我的安全选项有哪些？**

AVS 提供以下安全功能来保护你的 AVS 私有云环境：

* **静态数据加密**。 可以在你的 AVS 私有云中的 vSAN 存储上加密静态数据。 vSAN 支持可以部署在 Azure vNet 或本地环境中的外部密钥管理服务器。 有关详细信息，请参阅为[你的 AVS 私有云配置 vSAN 加密](vsan-encryption.md)。
* **网络安全**。 使用防火墙规则控制网络流量，这些规则适用于你的 AVS 私有云和 internet、你的 AVS 私有云和本地环境，或者在你的 AVS 私有云的子网中。
* **安全专用连接**。 本地网络和 Azure 订阅之间建立了安全的专用连接。

## <a name="compute"></a>计算

**可用的主机类型有哪些？**

AVS 提供以下主机类型：

* **CS28 节点：** CPU： 2x 2.2 GHz，总共28核，48 HT。  RAM： 256 GB。  存储空间： 1600 GB NVMe 缓存，5760 GB 数据（全部闪烁）。 网络： 4x25Gbe NIC
* **CS36 节点：** CPU 2x 2.3 GHz，36核心总数，72 HT。  RAM： 512 GB。  存储： 3200 GB NVMe 缓存 11520 GB 数据（所有闪存）。  网络： 4x25Gbe NIC
* **CS36m 节点：** CPU 2x 2.3 GHz，36核心总数，72 HT。  RAM： 576 GB。  存储： 3200 GB NVMe 缓存 13360 GB 数据（所有闪存）。  网络： 4x25Gbe NIC

**所有硬件故障的处理方式是什么？**

所有 AVS 基础结构都由 AVS 平台和我们的服务运营团队持续监视。 如果检测到硬件故障，则会将新节点添加到 AVS 私有云，并删除失败的节点。

## <a name="storage"></a>存储空间

**AVS 私有云支持哪种类型的存储？**

AVS 提供每个 AVS 私有云的所有闪存 VMware vSAN 存储。 每个 vSphere 都创建有自己的 vSAN 数据存储。 有关详细信息，请参阅[AVS 私有云 VMware 组件-vSAN 存储](vmware-components.md#vsan-storage)。

**是否支持数据加密？**
可以。 你可以在你的 AVS 私有云上设置 vSAN 存储，以使用部署在本地或 Azure 上的密钥管理服务器（KMS）来加密在 vSAN 上存储的数据。

**如何处理失败的磁盘？**

AVS 持续监视 AVS 私有云的所有硬件组件。 如果检测到磁盘故障或磁盘被标识为失败（基于试探法），则会将一个新节点自动添加到 AVS 私有云。 将从 AVS 私有云中删除具有失败或失败磁盘的节点。

## <a name="vmware"></a>VMware

**如何实现在本地执行应用程序和数据的大规模上传或迁移？**

AVS 提供本机 VMware vSphere 解决方案。 适用于大容量数据迁移的所有 VMware 工具均可与 AVS 私有云一起使用。 选项包括：

* VMware HCX，用于大容量迁移数据。
* 使用从本地到 AVS 的存储 vMotion 进行数据的冷迁移。

**是否可以安装任何 VMware 工具？**

AVS 提供本机 VMware vSphere 解决方案。 用于管理本地 vSphere 环境的所有 VMware 工具都可以在 AVS 上使用。 AVS 支持自带许可（BYOL）模型，以便安装 VMware 工具。

**更新和升级的管理方式是什么？**

AVS 以无中断的方式管理和更新你的 AVS 私有云的所有基础结构组件。 VMware 或基础结构供应商发布的所有更新和安全修补程序在由 AVS 限定后立即进行更新。

AVS 不会执行安装在 AVS 私有云上的应用程序的升级或更新。

## <a name="azure-integration"></a>Azure 集成

**支持哪些 Azure 服务？**

AVS 提供与 Azure 上的订阅的 Azure ExpressRoute 连接。 订阅中运行的所有服务都可以连接到 AVS 私有云。 示例包括：

* 作为你的 AVS vCenter 的标识源**Azure Active Directory** 。
* 用于存储来自 AVS 私有云的备份、映像和其他数据的**Azure 存储**。
* **混合应用**程序，具有跨越公共和 AVS 私有云的应用程序体系结构。 例如，可以在 Azure 中创建 webservers，用于访问你的 AVS 私有云上的应用程序和数据库服务器。
* 适用于 VMware 上运行的工作负荷的**azure monitor**和**azure 安全中心**支持日志记录、性能指标和安全管理。

**如何实现将 VMware 租户映射到 Azure？**

AVS 提供从 Azure 门户管理 AVS 私有云上的 VMware Vm 的独特功能。 使用所需的资源约束配置的 vCenter 资源池可以由全局管理员映射到你的订阅。 

**Azure 有哪些授权权益？**

使用 AVS，可以利用 Azure 混合使用权益，并节省高达90% 的许可证。 此权益可节省你对 Microsoft 许可证的投资，并可降低相对于其他云解决方案的总体拥有成本。 还会获得 Windows Server 2008 和 Microsoft SQL Server 2008 的扩展安全更新。 自带许可（BYOL）模型有助于降低常见应用（例如 Veeam 和 Zerto）的成本。 
