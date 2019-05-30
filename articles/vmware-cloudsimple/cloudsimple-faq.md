---
title: 常见问题-VMware CloudSimple 解决方案
description: Azure 通过 CloudSimple VMware 解决方案的常见问题
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e727398e1b7bfa406166574ab40320c68dac5709
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358523"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>有关通过 CloudSimple VMware 解决方案的常见问题

一些常见问题和解答： CloudSimple VMware 解决方案，帮助您了解该服务以及如何使用它。  问题和解答分为以下类别。

* CloudSimple 服务
* 连接
* 网络
* 安全
* 计算
* 存储
* VMware
* Azure 集成
  
## <a name="cloudsimple-service"></a>CloudSimple 服务

**通过 CloudSimple VMware 解决方案是什么？**

**VMware 解决方案 CloudSimple**转换并在几分钟内扩展到 Azure 上的专有和专用云的 VMware 工作负荷。 我们负责预配、 管理基础结构和协调本地与 Azure 之间的工作负荷。 因为您的应用程序运行完全相同的本地和 Azure 中，您将受益于的弹性和而无需重新架构您的应用程序的复杂性的云服务。 CloudSimple 可降低总成本的所有权与云消耗模型，提供按需预配、 随-您的增长和容量优化。  请参阅[CloudSimple 通过在 Azure 上的 VMware 解决方案是什么](cloudsimple-vmware-solutions-overview.md)针对功能、 优势和方案。

**CloudSimple 私有云是什么？**

预配专用的专用云包含的高性能计算、 存储、 和中的 Azure 位置的联网环境部署在 Microsoft Azure 基础结构 （硬件和数据中心空间）。  私有云提供了本机 VMware 平台作为服务。 在 VMware 术语中，每个私有云包含 vCenter 服务器的一个的实例。 VCenter 服务器管理多个 ESXi 节点包含一个或多个 vSphere 群集，以及相应的虚拟 SAN (vSAN) 存储区中。 CloudSimple 服务可以包含在 Azure 订阅中的多个私有云。  有关私有云的详细信息，请参阅[私有云概述](cloudsimple-private-cloud.md)。

**其中是可用 CloudSimple 服务？**

CloudSimple 目前在美国东部和美国西部区域。

**如何启用 CloudSimple 我的订阅？**

可以联系 Microsoft 客户代表处[ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com)以启用 CloudSimple 服务订阅。 提供你想为其启用 CloudSimple 服务电子邮件中的订阅 ID。  

**如何访问 CloudSimple 门户？**

从 Azure 门户访问 CloudSimple 门户。  请参阅[CloudSimple 门户从 Azure 门户访问 VMware 解决方案](https://docs.azure.cloudsimple.com/access-cloudsimple-portal)一文，了解访问 CloudSimple 门户的详细信息。 

**如何增加私有云的容量？**

购买 Azure 门户中的节点，并展开 CloudSimple 门户从您的私有云。  通过将其他节点添加到现有 vSphere 群集或创建新的 vSphere 群集，您可以展开您的私有云。  请参阅[展开 CloudSimple 私有云](https://docs.azure.cloudsimple.com/expand-private-cloud)一文，了解该过程。

**在维护期间会我私有云？**

CloudSimple 提供定期通知的计划的维护之前的天数。  确保您的私有云的可用性的非中断性方法中完成维护。  维护可以是以下类型之一：

1. **CloudSimple 基础结构：** CloudSimple 基础结构设计高度可用。  在维护期间，连接性和可用性的您的私有云是通过确保实现不会影响一次更新一个冗余组件。  您可以访问到私有云 vCenter、 所有虚拟机，从您的私有云的 internet 连接和连接到在本地或 Azure。
2. **CloudSimple 门户：** 在维护期间，CloudSimple 门户上的某些功能可能无法访问或已禁用。  维护通知将包括在门户中可以做什么的详细信息。

## <a name="connectivity"></a>连接

**我到 CloudSimple 区域网络的连接选项有哪些？**

CloudSimple 提供用于连接到 CloudSimple 区域网络的三个不同的连接选项。  所有这三个可以一起使用。

1. 从你的本地数据中心 CloudSimple 区域网络-安全高速低延迟桥接在本地 ExpressRoute 线路与使用全球覆盖 CloudSimple ExpressRoute 线路的专用连接到 ExpressRoute 连接。 请参阅[从本地连接到使用 ExpressRoute CloudSimple](https://docs.azure.cloudsimple.com/on-premises-connection)一文，了解设置连接。
2. 从在 Azure 虚拟网络到 CloudSimple 区域网络的安全高速低延迟的专用连接你在 Azure 上的虚拟网络的桥接 CloudSimple ExpressRoute 线路使用虚拟网络网关与 ExpressRoute 连接。  请参阅[将 CloudSimple 私有云环境连接到 Azure 虚拟网络使用 ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection)一文，了解设置连接。
3. 从本地数据中心的站点到站点 VPN 连接到 CloudSimple 区域网络-安全到 CloudSimple 私有云区域中的本地 VPN 设备的虚拟专用网络。  有关设置 VPN 连接，请参阅 [设置你的本地网络和 CloudSimple 私有云之间的 VPN 连接] 一文。

**如何连接到私有云？**

CloudSimple 门户中，可以查看您的私有云的详细信息。 若要连接到 vCenter 对应于您的私有云，请确保网络连接已建立使用站点到站点、 点到站点或 ExpressRoute。 然后，启动 CloudSimple 门户从 Azure 门户并单击*启动 vSphere 客户端*的主页上或在私有云的详细信息页上。

**ExpressRoute 线路的优势是什么？**

Azure ExpressRoute 线路提供低延迟高速安全连接。  CloudSimple 提供每个区域每个客户专用的 ExpressRoute 线路。  使用此线路，可以建立从本地和/或你的 Azure 订阅的安全连接。

**若要向/从 CloudSimple 连接的网络费用是。向/从 Azure 到 CloudSimple 任何传出费用？跨区域？**

不没有任何网络出口不收取任何费用。  Azure 的标准费率适用于任何传出流量，从你的虚拟网络或本地 ExpressRoute 线路。

## <a name="networking"></a>网络

**提供有关我的私有云网络功能的？**

可以预配 Vlan （和它们的子网）、 防火墙表，并分配公共 IP 地址和映射到你的私有云中运行的虚拟机。  有关详细信息，请参阅[Vlan/子网概述](cloudsimple-vlans-subnets.md)，[防火墙表概述](cloudsimple-firewall-tables.md)，并[公共 IP 地址概述](cloudsimple-public-ip-address.md)文章。

**如何设置不同的子网为我的应用程序中我私有云？**

从 CloudSimple 门户，可以在您的私有云上创建 Vlan。  一旦创建 VLAN，可以在使用 VLAN 在私有云 vCenter 上创建分布式的端口组并创建连接到分布式的端口组的虚拟机。  可以为 VLAN/子网启用防火墙表并定义要保护的网络流量的防火墙规则。

**哪些防火墙设置都可用于我的私有云？**

可以配置北-南和东-西流量的规则。  防火墙表中定义的规则。  在防火墙表可以附加到您的私有云上 VLAN(s)。  请参阅[为私有云设置防火墙表和规则](https://docs.azure.cloudsimple.com/firewall)一文，了解安装过程。

**可以分配公共 IP 地址的 Vm 在我的私有云环境？**

在 CloudSimple 门户中，可以轻松地分配新的公共 IP 地址并将其与你的虚拟机或的设备的专用 IP 地址关联。  此外可以创建新的防火墙规则或应用现有防火墙规则以允许流量从特定端口和/或特定设置将在门户中的 IP 地址。 请参阅[为私有云环境分配公共 IP 地址](https://docs.azure.cloudsimple.com/public-ips)的安装过程。

## <a name="security"></a>安全

**我在 CloudSimple 上的安全选项有哪些？**

CloudSimple 私有云的保护您的私有云环境提供了以下安全功能：

1. **静态数据加密**:可以对静态 vSAN 存储上位于您的私有云数据进行加密。 vSAN 支持外部密钥管理服务器，可以在 Azure vNet 或本地环境中部署。  请参阅[配置您的 CloudSimple 私有云的 vSAN 加密](https://docs.azure.cloudsimple.com/vsan-encryption)的更多详细信息。
2. **网络安全**:控制网络流量流从/向您的私有云从 Internet，在本地和私有云使用防火墙规则的子网中。
3. **安全专用连接**:你的本地网络与你的 Azure 订阅之间的安全专用连接。

## <a name="compute"></a>计算

**哪种主机是否可用？**

CloudSimple 提供两种主机类型：

* **CS28 节点：** CPU:2 个 2.2 ghz 处理器，总的 28 个内核，48 超线程。  RAM：256 GB。  存储：1600 GB NVMe 缓存，5760 GB 的数据 （所有闪存）。 网络：2x25Gbe NIC
* **CS36 节点：** CPU 2 x 2.3 GHz，总 36 个内核，72 超线程。  RAM：512 GB。  存储：3200 GB NVMe 缓存时为 11520 GB 数据 （所有闪存）。  网络：2x25Gbe NIC

**如何处理任何硬件故障？**

CloudSimple 平台和我们服务运营团队将持续监视所有 CloudSimple 基础结构。  如果检测到硬件故障时，将新节点添加到您的私有云，并确保您的私有云的高可用性删除失败的节点。

## <a name="storage"></a>存储

**私有云支持哪种类型的存储？**

提供了 CloudSimple**所有闪存 VMware vSAN 存储**通过每个私有云。  每个 vSphere 创建具有其自己的 vSAN 数据存储。  请参阅[私有云 VMware 组件-vSAN 存储](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage)一文，了解更多详细信息。

**是支持的数据加密？**
是的。  您可以设置 vSAN 存储在您的私有云以使用密钥管理服务器 (KMS) 这是部署在本地或在 Azure 上的 vSAN 上存储的数据加密

**如何处理失败的磁盘？**

CloudSimple 监视连续监视私有云的所有硬件的组件。  如果检测到任何磁盘故障或任何磁盘被标识为故障 （基于启发式方法），一个新的节点是自动添加到私有云。  具有故障或即将发生故障的磁盘的节点已从私有云。

## <a name="vmware"></a>VMware

**如何从本地执行大规模的上传/迁移应用程序和数据？**

CloudSimple 提供本机 VMware vSphere 解决方案。  用于大容量数据迁移的任何工具可以用于 CloudSimple 私有云。  下面是一些可用的选项：

1. 大容量数据迁移的 VMware HCX。
2. 使用从本地到 CloudSimple 存储 vMotion 的数据的冷迁移。

**可以安装任何 VMware 工具吗？**

CloudSimple 提供本机 VMware vSphere 解决方案。  上 CloudSimple，可以使用任何工具来管理 vSphere 环境的本地。  CloudSimple 支持自带的自己的许可证 (BYOL) 模型，用于安装 VMware 工具。

**如何更新和升级管理？**

CloudSimple 管理并无中断无缝的方式更新您的私有云的所有基础结构组件。  由 VMware 或基础结构供应商发布任何更新或安全修补程序将按计划进行更新，只要它由 CloudSimple 限定。

CloudSimple 不执行升级或私有云上安装的应用程序的更新。

## <a name="azure-integration"></a>Azure 集成

**支持哪些 Azure 服务？**

CloudSimple Azure 上提供 Azure ExpressRoute 连接到你的订阅。  在订阅中运行的任何服务具有网络连接到您的私有云，并可连接到您的私有云。  示例：

1. **Azure Active Directory**作为 CloudSimple vCenter 的标识源
2. **Azure 存储**用于存储备份、 图像和其他数据从您的私有云
3. **混合应用程序**-可以创建应用程序体系结构： 跨公有云和私有云。  例如，您可以在访问应用程序和 CloudSimple 私有云上的数据库服务器的 Azure 中创建 web 服务器的 web。
4. **Azure monitor**并**Azure 安全中心**-在 VMware 上运行的工作负荷可以使用这些日志记录、 性能度量值和安全管理。

**如何将我的 VMware 租户映射到 Azure？**

CloudSimple 提供从 Azure 门户管理私有云上的 VMware Vm 的唯一功能。  （配置与所需的资源约束） 的 vCenter 资源池可以映射到你的订阅的全局管理员。  

**通过 Azure 获取哪些许可权益**

使用 CloudSimple，可以充分利用 Azure 混合使用权益，并保留你的投资中降低了总拥有成本，相比其他云的 Microsoft 许可证的许可证上节省多达 90%。 此外，你获得扩展的 Windows Server 2008 和 Microsoft SQL Server 2008 的安全更新。  到云，如 Veeam、 Zerto，以及其他常见应用程序用于使成本保持低使用自带许可证 (BYOL)。  