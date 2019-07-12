---
title: 常见问题-VMware CloudSimple 解决方案
description: Azure 通过 CloudSimple VMware 解决方案的常见问题
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e187a4a75a27e206a632388f1e20a94da032eb08
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595344"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>有关通过 CloudSimple VMware 解决方案的常见问题

一些常见问题和解答： Azure CloudSimple VMware 解决方案，帮助您了解该服务以及如何使用它。 问题和解答分为以下类别：

* CloudSimple 服务
* 连接
* 网络
* 安全性
* 计算
* 存储
* VMware
* Azure 集成
 
## <a name="cloudsimple-service"></a>CloudSimple 服务

**Azure 通过 CloudSimple VMware 解决方案是什么？**

Azure 的 VMware 解决方案通过 CloudSimple 转换，并在几分钟内扩展到 Azure 上的专有和专用云的 VMware 工作负荷。 该解决方案预配、 管理基础结构，并安排在本地与 Azure 之间的工作负荷。 因为您的应用程序运行完全相同的本地和 Azure 中，您将受益于的弹性和而无需重新架构您的应用程序的复杂性的云服务。 CloudSimple 可降低总成本的所有权与云消耗模型，提供按需预配、 随-您的增长和容量优化。 有关功能、 优势和方案，请参阅[CloudSimple 由 Azure VMware 解决方案是什么？](cloudsimple-vmware-solutions-overview.md)。

**CloudSimple 私有云是什么？**

预配的高性能计算、 存储和网络环境中的 Azure 位置部署 Microsoft Azure 基础结构 （硬件和数据中心空间） 包含的私有专用云。 私有云提供本机 VMware 平台即服务。 在 VMware 术语中，每个私有云中包含 vCenter 服务器的一个的实例。 VCenter 服务器管理多个 ESXi 节点包含一个或多个 vSphere 群集，以及相应的 vSAN 存储区中。 CloudSimple 服务可以包含在 Azure 订阅中的多个私有云。 有关私有云的详细信息，请参阅[私有云概述](cloudsimple-private-cloud.md)。

**其中是 CloudSimple 服务可用的项目？**

CloudSimple 目前在美国东部和美国西部区域。

**如何启用 CloudSimple 我的订阅？**

请联系 Microsoft 客户代表处[ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com)以启用 CloudSimple 服务订阅。 提供你想为其启用 CloudSimple 服务电子邮件中的订阅 ID。 

**如何访问 CloudSimple 门户？**

从 Azure 门户访问 CloudSimple 门户。 有关如何访问 CloudSimple 门户的信息，请参阅[CloudSimple 门户从 Azure 门户访问 VMware 解决方案](https://docs.azure.cloudsimple.com/access-cloudsimple-portal)。

**如何增加私有云的容量？**

从 Azure 门户预配节点并展开 CloudSimple 门户从私有云。 通过添加到现有的 vSphere 群集节点或通过创建新的 vSphere 群集可以扩展你的私有云。 该过程的信息，请参阅[展开 CloudSimple 私有云](https://docs.azure.cloudsimple.com/expand-private-cloud)。

**会发生什么情况我私有云在维护期间？**

CloudSimple 提供计划的维护的定期通知天才。 确保你的私有云中的可用性的非破坏性方法中完成维护。 维护可以是以下类型之一：

- **CloudSimple 基础结构**:CloudSimple 基础结构设计高度可用。 在维护期间，连接性和可用性的私有云是通过确保实现不会影响一次更新一个冗余组件。 您可以访问的私有云 vCenter、 所有虚拟机、 私有云，从 internet 连接和连接到在本地或 Azure。
- **CloudSimple 门户**:在维护期间 CloudSimple 门户上的某些功能可能无法访问或可能已禁用。 维护通知包括有关可以在门户上做什么的信息。

## <a name="connectivity"></a>连接

**我跟我 CloudSimple 区域的网络的连接选项有哪些？**

CloudSimple 提供用于连接到 CloudSimple 区域网络的三个不同的连接选项。 可以一起使用所有三个选项：

- Azure ExpressRoute 从连接的本地数据中心到 CloudSimple 区域网络：一个高速低延迟安全专用连接，通过使用全球覆盖来桥接 CloudSimple ExpressRoute 线路的本地 ExpressRoute 线路。 若要设置的连接，请参阅[从本地连接到使用 ExpressRoute CloudSimple](https://docs.azure.cloudsimple.com/on-premises-connection)。
- 与 CloudSimple 区域网络的 ExpressRoute 连接从 Azure 虚拟网络：高速、 低延迟安全专用连接，通过使用虚拟网络网关来桥接在 Azure 上的使用 CloudSimple ExpressRoute 线路在虚拟网络。 若要设置的连接，请参阅[使用 ExpressRoute 连接到 Azure 虚拟网络 CloudSimple 私有云环境](https://docs.azure.cloudsimple.com/azure-expressroute-connection)。
- 站点到站点 VPN 连接从本地数据中心拖动到 CloudSimple 区域网络：安全虚拟专用网络从你的本地 VPN 设备到 CloudSimple 私有云区域。 若要设置的连接，请参阅[设置 VPN 连接的本地网络和 CloudSimple 私有云之间](https://docs.azure.cloudsimple.com/set-up-vpn)。

**如何向私有云连接？**

CloudSimple 门户中，可以查看您的私有云的详细信息。 若要连接到 vCenter 对应于你的私有云，请确保通过使用站点到站点、 点到站点或 ExpressRoute 建立网络连接。 然后，启动从 Azure 门户 CloudSimple 门户。 选择**启动 vSphere 客户端**的主页上或在私有云的详细信息页上。

**ExpressRoute 线路的优势是什么？**

Azure ExpressRoute 线路提供高速、 低延迟的安全连接。 CloudSimple 提供每个区域每个客户的专用的 ExpressRoute 线路。 使用此线路，可以建立从本地和 Azure 订阅的安全连接。

**若要连接到和从 CloudSimple 的网络成本有哪些？是否有任何与 CloudSimple 到 Azure 的传出费用？跨区域是否有任何传出费用？**

不没有网络出口不收取任何费用。 Azure 的标准费率适用于任何传出流量，从你的虚拟网络或本地 ExpressRoute 线路。

## <a name="networking"></a>网络

**我的私有云提供网络功能的？**

你可以设置 Vlan 及其子网和防火墙的表。 可以将公共 IP 地址分配，并将映射到在私有云中运行的虚拟机。 有关详细信息，请参阅[Vlan 和子网概述](cloudsimple-vlans-subnets.md)，[防火墙表概述](cloudsimple-firewall-tables.md)，并[公共 IP 地址概述](cloudsimple-public-ip-address.md)。

**如何设置不同的子网为我的应用程序在我的私有云中？**

从 CloudSimple 门户，可以在私有云上创建的 Vlan。 创建 VLAN 后，可以使用 VLAN 在私有云 vCenter 上创建分布式的端口组，并创建虚拟机连接到分布式的端口组。 可以为 VLAN 或子网启用防火墙表并定义要保护的网络流量的防火墙规则。

**哪些防火墙设置都可用于我的私有云？**

可以配置北-南和东-西流量的规则。 防火墙表中定义的规则。 在防火墙表可以附加到私有云上的 Vlan。 有关安装过程中，请参阅[设置防火墙表和私有云的规则](https://docs.azure.cloudsimple.com/firewall)。

**可以分配公共 IP 地址的 Vm 在我的私有云环境？**

在 CloudSimple 门户中，可以轻松地分配新的公共 IP 地址并将其与你的虚拟机或的设备的专用 IP 地址关联。 此外可以创建新的防火墙规则或应用现有防火墙规则以允许流量从特定端口和特定设置将在门户中的 IP 地址。 有关安装过程中，请参阅[为私有云环境分配公共 IP 地址](https://docs.azure.cloudsimple.com/public-ips)。

## <a name="security"></a>安全性

**我在 CloudSimple 上的安全选项有哪些？**

CloudSimple 私有云的保护您的私有云环境提供了以下安全功能：

- **静态加密的数据：** 可以对驻留在 vSAN 存储在私有云的静态数据进行加密。 vSAN 支持的外部密钥管理服务器，可以在 Azure 虚拟网络或本地环境中部署的。 有关详细信息，请参阅[配置为 CloudSimple 私有云的 vSAN 加密](https://docs.azure.cloudsimple.com/vsan-encryption)。
- **网络安全：** 控制网络流量流从 / 向私有云通过 internet 在本地和私有云使用防火墙规则的子网中。
- **安全、 专用的连接：** 安全、 专用的本地网络与你的 Azure 订阅之间的连接。

## <a name="compute"></a>计算

**哪种主机是否可用？**

CloudSimple 提供两种主机类型：

* **CS28 节点**:CPU:2 个 2.2 ghz 处理器，总的 28 个内核，48 超线程。 RAM：256 GB。 存储：1600 GB NVMe 缓存，5760 GB 的数据 （所有闪存）。 网络：2x25Gbe nic。
* **CS36 节点**:CPU 2 x 2.3 GHz，总 36 个内核，72 超线程。 RAM：512 GB。 存储：3200 GB NVMe 缓存时为 11520 GB 的数据 （所有闪存）。 网络：2x25Gbe nic。

**如何处理硬件故障？**

CloudSimple 平台和其服务运营团队将持续监视所有 CloudSimple 基础结构。 如果检测到硬件故障，则新节点添加到你的私有云。 发生故障的节点被删除，以确保你的私有云的高可用性。

## <a name="storage"></a>存储

**私有云上支持什么类型的存储？**

提供了 CloudSimple**所有闪存 VMware vSAN 存储**与每个私有云。 每个 vSphere 创建具有其自己的 vSAN 数据存储。 有关详细信息，请参阅[私有云 VMware 组件-vSAN 存储](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage)。

**是支持的数据加密？**
是的。 您可以设置 vSAN 存储在私有云以使用密钥管理服务器 (KMS)，这是部署在本地上或在 Azure 上的 vSAN 上存储的数据加密。

**如何处理失败的磁盘？**

CloudSimple 监视连续监视私有云的所有硬件的组件。 如果检测到磁盘故障或磁盘被标识为失败的基于启发式方法，一个新的节点是自动添加到私有云。 具有失败或即将发生故障的磁盘的节点已从私有云。

## <a name="vmware"></a>VMware

**如何从本地执行较大的比例上传和应用程序和数据迁移？**

CloudSimple 提供本机的 VMware vSphere 解决方案。 用于大容量数据迁移的任何工具可以用于 CloudSimple 私有云。 下面是一些可用的选项：

- 大容量数据迁移的 VMware HCX。
- 通过使用存储 vMotion 从本地到 CloudSimple 数据的冷迁移。

**可以安装任何 VMware 工具吗？**

CloudSimple 提供本机的 VMware vSphere 解决方案。 用来管理 vSphere 环境可在本地 CloudSimple 使用任何工具。 CloudSimple 支持自带的自己的许可证 (BYOL) 模型，用于安装 VMware 工具。

**如何更新和升级管理？**

CloudSimple 管理并无中断无缝的方式更新所有的私有云基础结构组件。 只要它由 CloudSimple 限定由 VMware 或基础结构供应商发布任何更新或安全修补程序已计划更新。

CloudSimple 不会执行升级或私有云上安装的应用程序的更新。

## <a name="azure-integration"></a>Azure 集成

**支持哪些 Azure 服务？**

CloudSimple Azure 上提供 Azure ExpressRoute 连接到你的订阅。 在你的订阅中运行的任何服务网络连接到你的私有云，可以连接到你的私有云。 示例：

- **Azure Active Directory**：使用 Azure Active Directory 作为标识源 CloudSimple vCenter。
- **Azure 存储**：使用存储来存储备份、 图像和其他私有云的数据。
- **混合应用程序**:您可以创建应用程序体系结构： 跨公有云和私有云。 例如，您可以创建 web 服务器在 Azure 中访问应用程序和数据库服务器的 CloudSimple 私有云上。
- **Azure Monitor**并**Azure 安全中心**:在 VMware 运行的工作负荷可以使用监视和安全中心进行日志记录、 性能度量值和安全管理。

**如何将我的 VMware 租户映射到 Azure？**

CloudSimple 提供独特的功能在 Azure 门户中管理私有云上的 VMware Vm。 VCenter 资源池配置所需的全局管理员可以映射到你的订阅的资源约束。 

**通过 Azure 获取哪些许可权益**

使用 CloudSimple，可以充分利用 Azure 混合权益和许可证，以保持你的 Microsoft 许可证中的投资并降低总成本的所有权相比其他云上节省多达 90%。 您还获得扩展的安全更新对于 Windows Server 2008 和 Microsoft SQL Server 2008。 到云，如 Veeam、 Zerto，以及其他常见应用程序用于使成本保持低，BYOL。 
