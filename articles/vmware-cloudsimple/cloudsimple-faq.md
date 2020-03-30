---
title: 常见问题解答 - Azure VMware 解决方案（按云简单）
description: 云简单 Azure VMware 解决方案的常见问题
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 95d8c2974ea372dd59d15a9f2cc31ed171acd932
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025055"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>关于 VMware 解决方案的常见问题

## <a name="cloudsimple-service"></a>云简单服务

**什么是 Azure VMware 解决方案（按云简单）？**

云简单 Azure VMware 解决方案在几分钟内即可将 VMware 工作负载转换为 Azure 上的专用云。 CloudSimple 负责配置、管理基础结构以及协调本地和 Azure 之间的工作负载。 由于应用在本地和 Azure 中运行完全相同，因此您将受益于云的弹性和服务，而无需重新构建应用的复杂性。 CloudSimple 通过提供按需预配、随增长付费和容量优化的云消费模型，可降低您的总拥有成本。  有关功能、优点和方案，请参阅[Azure 上的 VMware 解决方案。简单。](cloudsimple-vmware-solutions-overview.md)

**什么是云简单私有云？**

CloudSimple 私有云是专用专用云，由部署在 Azure 位置的 Microsoft Azure 基础结构（硬件和数据中心空间）上的高性能计算、存储和网络环境组成。  私有云提供本机 VMware"平台即服务"。 在 VMware 术语中，每个私有云都包含 vCenter 服务器的一个实例。 vCenter 服务器管理一个或多个 vSphere 群集中包含的多个 ESXi 节点，以及相应的虚拟 SAN （vSAN） 存储。 云简单服务可以在 Azure 订阅中包含多个私有云。  有关详细信息，请参阅[私有云概述](cloudsimple-private-cloud.md)。

**云简单服务在哪里可用？**

CloudSimple 在美国东部、美国西部和西欧地区提供，其他区域即将推出。

**如何启用云简单订阅？**

您可以联系 Microsoft 帐户代表[azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com)，以便启用 CloudSimple 服务的订阅。 在您希望为其启用 CloudSimple 服务的电子邮件中提供订阅 ID。  

**如何访问云简单门户？**

从 Azure 门户访问云简单门户。  有关详细信息，请参阅[从 Azure 门户通过云简单门户访问 VMware 解决方案](access-cloudsimple-portal.md)。

**如何增加私有云的容量？**

要增加容量，请从 Azure 门户购买其他节点，然后使用这些节点从 CloudSimple 门户扩展私有云。  您可以将其他节点添加到现有 vSphere 群集，或将它们添加到新的 vSphere 群集。  有关详细信息，请参阅[扩展云简单私有云](expand-private-cloud.md)。

**在维护期间，我的私有云会发生什么情况？**

CloudSimple 在计划维护间隔前几天提供通知。  维护以无中断的方式完成，以确保私有云的可用性。  维护可以是以下类型：

* **云简单基础架构**。  云简单基础架构旨在高度可用。  在这种类型的维护间隔内，冗余组件一次更新一个，以避免任何服务中断。 您可以保持对私有云 vCenter、所有虚拟机、来自私有云的互联网连接以及到本地或 Azure 的连接的访问权限。
* **云简单门户**。 在这种类型的维护间隔内，云简单门户上的某些功能可能会被禁用或无法访问。  维护间隔前的通知包括有关进行维护时功能限制的详细信息。

## <a name="connectivity"></a>连接

**我与云简单区域网络的连接选项是什么？**

CloudSimple 提供了以下连接选项，可连接到云简单区域网络。 可以同时使用多个选项。

* **从本地数据中心到云简单区域网络的快速路由连接**。 这是一个高速、低延迟、安全的专用连接，使用 Global Reach 将本地快速路由电路桥接到云简单快速路由电路。 有关设置连接的说明，请参阅[使用 ExpressRoute 从本地连接到云简单](on-premises-connection.md)。
* **从 Azure 虚拟网络到云简单区域网络的快速路由连接**。 这是一种高速、低延迟、安全的专用连接，使用虚拟网络网关将 Azure 上的虚拟网络桥接到 CloudSimple ExpressRoute 电路。 有关设置连接的说明，请参阅[使用 ExpressRoute 将云简单私有云环境连接到 Azure 虚拟网络](azure-expressroute-connection.md)。
* **站点到站点 VPN 连接，从本地数据中心到云简单区域网络**。 这是一个安全虚拟专用网络，从本地 VPN 设备到云简单私有云区域。  有关详细信息，请参阅[在云简单网络上设置 VPN 网关](vpn-gateway.md)。

**如何连接到私有云？**

您可以在云简单门户中查看私有云的详细信息。 要连接到与私有云对应的 vCenter，请先验证是否使用站点到站点 VPN、点对点 VPN 或 ExpressRoute 建立了网络连接。 然后，从 Azure 门户启动 CloudSimple 门户，然后单击主页或私有云详细信息页上**的启动 vSphere 客户端**。

**快速路由电路的优势是什么？**

Azure ExpressRoute 电路是一种高速、低延迟、安全的连接。  CloudSimple 为每位客户提供专用的 ExpressRoute 电路。  使用此电路，可以从本地或 Azure 订阅建立安全连接。

**连接到云简单网络的成本是多少？ 云简单和 Azure 之间或跨区域是否收取任何出口费用？**

网络出口不收取云简单费用。  Azure 标准费率适用于来自虚拟网络或本地 ExpressRoute 电路的任何出口流量。

## <a name="networking"></a>网络

**我的私有云可以使用哪些网络功能？**

您可以预配 VLAN（及其子网）和防火墙表，并将映射到在私有云中运行的虚拟机的公共 IP 地址分配。 有关网络功能的详细信息，请参阅[VLAN 和子网概述](cloudsimple-vlans-subnets.md)、[防火墙表概述](cloudsimple-firewall-tables.md)和[公共 IP 地址概述](cloudsimple-public-ip-address.md)。

**如何在私有云中为应用程序设置不同的子网？**

您可以在私有云上从云简单门户创建 VLAN。  创建 VLAN 后，可以使用 VLAN 在私有云 vCenter 上创建分布式端口组，并创建连接到分布式端口组的虚拟机。  您可以为 VLAN/子网启用防火墙表，并定义防火墙规则以保护网络流量。

**我的私有云可以使用哪些防火墙设置？**

您可以为南北交通和东西交通配置规则。  规则在防火墙表中定义。  防火墙表可以附加到私有云上的 VLAN。  有关详细信息，请参阅[为私有云设置防火墙表和规则](firewall.md)。

**我可以将公共 IP 地址分配给私有云环境中的 VM 吗？**

在 CloudSimple 门户中，您可以分配新的公共 IP 地址并将其与虚拟机或设备的专用 IP 地址相关联。  您还可以创建新的防火墙规则或应用现有防火墙规则，以允许来自门户中特定端口和 IP 地址的流量。 有关详细信息，请参阅[为私有云环境分配公共 IP 地址](public-ips.md)。

## <a name="security"></a>安全性

**我在云简单上的安全选项是什么？**

CloudSimple 提供了以下安全功能，用于保护您的私有云环境：

* **静态数据加密**。 您可以加密驻留在私有云中的 vSAN 存储的静态数据。 vSAN 支持外部密钥管理服务器，这些服务器可以部署在 Azure vNet 或本地环境中。  有关详细信息，请参阅[为云简单私有云配置 vSAN 加密](vsan-encryption.md)。
* **网络安全**。 使用适用于私有云和 Internet、私有云和本地环境之间或私有云子网中的防火墙规则控制网络流量。
* **安全、私人连接**。 在本地网络和 Azure 订阅之间建立安全的私人连接。

## <a name="compute"></a>计算

**可用的主机类型是什么？**

CloudSimple 提供以下主机类型：

* **CS28 节点：** CPU：2x 2.2 GHz，共28个内核，48个HT。  内存：256 GB。  存储：1600 GB NVMe 缓存，5760 GB 数据（全闪存）。 网络： 4x25Gbe NIC
* **CS36 节点：** CPU 2x 2.3 GHz，共 36 个内核，72 个 HT。  内存：512 GB。  存储：3200 GB NVMe 缓存 11520 GB 数据（全闪存）。  网络： 4x25Gbe NIC
* **CS36m 节点：** CPU 2x 2.3 GHz，共 36 个内核，72 个 HT。  内存：576 GB。  存储：3200 GB NVMe 缓存 13360 GB 数据（全闪存）。  网络： 4x25Gbe NIC

**如何处理任何硬件故障？**

云简单平台和我们的服务运营团队持续监控所有云简单基础架构。  如果检测到硬件故障，将新节点添加到私有云，并删除故障节点。

## <a name="storage"></a>存储

**私有云支持哪种类型的存储？**

CloudSimple 提供全闪存 VMware vSAN 存储，包括每个私有云。  每个 vSphere 都使用自己的 vSAN 数据存储创建。  有关详细信息，请参阅[私有云 VMware 组件 - vSAN 存储](vmware-components.md#vsan-storage)。

**是否支持数据加密？**
是的。  您可以在私有云上设置 vSAN 存储，以便使用部署在本地或 Azure 上的关键管理服务器 （KMS） 来加密存储在 vSAN 上的数据。

**如何处理故障磁盘？**

CloudSimple 持续监控私有云的所有硬件组件。  如果检测到磁盘故障或磁盘标识为故障（基于启发式），则新节点将自动添加到私有云中。  具有失败或失败磁盘的节点将从私有云中删除。

## <a name="vmware"></a>VMware

**如何从本地执行应用程序和数据的大规模上载或迁移？**

CloudSimple 提供了原生 VMware vSphere 解决方案。  所有用于批量数据迁移的 VMware 工具都可以与私有云一起使用。  选项包括：

* VMware HCX 用于批量迁移数据。
* 使用存储 vMotion 从本地迁移到云简单数据进行冷迁移。

**我可以安装任何 VMware 工具吗？**

CloudSimple 提供了原生 VMware vSphere 解决方案。  用于管理本地 vSphere 环境的所有 VMware 工具都可以在 CloudSimple 上使用。  CloudSimple 支持自带许可证 （BYOL） 模型来安装 VMware 工具。

**如何管理更新和升级？**

CloudSimple 以无缝无中断的方式管理和更新私有云的所有基础架构组件。  VMware 或基础结构供应商发布的所有更新和安全修补程序一旦通过 CloudSimple 认证，即可进行更新。

CloudSimple 不执行私有云上安装的应用程序的升级或更新。

## <a name="azure-integration"></a>Azure 集成

**支持哪些 Azure 服务？**

CloudSimple 提供 Azure 快速路由连接到 Azure 上的订阅。  订阅中运行的所有服务都可以连接到您的私有云。  示例包括：

* **Azure 活动目录**作为云简单 vCenter 的标识源。
* **用于**存储来自私有云的备份、映像和其他数据的 Azure 存储。
* 具有跨公共云和私有云的应用程序体系结构**的混合应用程序**。  例如，您可以在 Azure 中创建访问私有云上的应用程序和数据库服务器的 Web 服务器。
* **在**VMware 上运行的工作负载的 Azure 监视器和**Azure 安全中心**支持日志记录、性能指标和安全管理。

**如何将 VMware 租户映射到 Azure？**

CloudSimple 提供了从 Azure 门户管理私有云上的 Vm 的独特功能。  全局管理员可以映射到您的订阅，该资源池配置了所需的资源约束。  

**使用 Azure 可以获得哪些许可权益？**

借助 CloudSimple，您可以利用 Azure 混合使用优势，并在许可证上节省高达 90% 的许可证。 此优势可保留您对 Microsoft 许可证的投资，并降低相对于其他云解决方案的 TCO。 您还可以获得 Windows Server 2008 和 Microsoft SQL Server 2008 的扩展安全更新。  自带许可证 （BYOL） 模型可帮助您降低 Veeam 和 Zerto 等常见应用的成本。  
