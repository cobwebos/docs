---
title: "Azure 上的 SAP HANA（大型实例）概述和体系结构 | Microsoft 文档"
description: "有关如何部署 Azure 上的 SAP HANA（大型实例）的体系结构概述。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fa842efd99718be7fa9eaf8aac8030c32cbceeec
ms.openlocfilehash: a412304c33d915be6e30c6b748c8431a9c7e9f34
ms.lasthandoff: 03/01/2017


---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Azure 上的 SAP HANA（大型实例）概述和体系结构 
这是一份体系结构和技术部署指南，由五个部分组成，其中包含的信息可帮助用户在 Azure 中的新型“Azure 上的 SAP HANA（大型实例）”上部署 SAP。 本指南中的内容并不详尽，未包括有关设置 SAP 解决方案的具体详细信息。 它只是提供重要的信息来帮助用户完成初始部署和日常操作。 请不要使用它来取代有关安装 SAP HANA 的 SAP 文档（也不能取代包括该主题的许多 SAP 支持说明）。 此外，本指南提供了有关安装“Azure 上的 SAP HANA（大型实例）”的详细信息。


本指南的五个部分涵盖以下主题：

- [概述和体系结构](sap-hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [基础结构和连接](sap-hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA 安装](sap-hana-overview-sap-hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [高可用性和灾难恢复](sap-hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [故障排除和监视](sap-hana-overview-troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="definitions"></a>定义

体系结构和技术部署指南中广泛使用了几个常见定义。 请注意以下术语及其含义：

- **IaaS：**基础结构即服务
- **PaaS：**平台即服务
- **SaaS：**软件即服务
- **SAP 组件：**各个 SAP 应用程序，例如 ECC、BW、Solution Manager 或 EP。 SAP 组件可以基于传统的 ABAP 或 Java 技术，也可以是不基于 NetWeaver 的应用程序，例如业务对象。
- **SAP 环境：**以逻辑方式组合在一起，用于执行开发、QAS、培训、DR 或生产等业务功能的一个或多个 SAP 组件。
- **SAP 布局：**表示 IT 布局中所有 SAP 资产的整个格局。 SAP 布局包括所有生产和非生产环境。
- **SAP 系统：**SAP ERP 开发系统、SAP BW 测试系统、SAP CRM 生产系统等等的 DBMS 层与应用程序层的组合。Azure 部署不支持在本地与 Azure 之间分割这两个层。 这意味着，SAP 系统要么部署在本地，要么部署在 Azure 中。 但是，可以将 SAP 布局中的不同系统部署到 Azure 或本地。 例如，可以在 Azure 中部署 SAP CRM 开发系统和测试系统，同时在本地部署 SAP CRM 生产系统。 对于 Azure 上的 SAP HANA（大型实例），支持在 Azure VM 中在运行 SAP 系统的 SAP 应用程序层，在大型实例模具中的某个单元上运行 HANA 实例。
- **大型实例模具：**已通过 SAP HANA TDI 认证的硬件基础结构堆栈，专门用于在 Azure 中运行 SAP HANA 实例。
- **Azure 上的 SAP HANA（大型实例）：**用于在通过 SAP HANA TDI 认证的、部署在不同 Azure 区域中的大型实例模具中的硬件上运行 HANA 实例的产品的官方名称。 本技术部署指南中广泛使用的相关术语 **HANA 大型实例**是“Azure 上的 SAP HANA（大型实例）”的简称。
- **跨界：**描述了一种方案，其中 VM 部署到一个 Azure 订阅，该订阅在本地数据中心与 Azure 之间存在站点到站点、多站点或 ExpressRoute 连接。 在一般的 Azure 文档中，此类部署也称为跨界方案。 连接的原因是为了将本地域、本地 Active Directory/OpenLDAP 和本地 DNS 扩展到 Azure。 本地布局会扩展到订阅的 Azure 资产。 经过这种扩展后，VM 可以成为本地域的一部分。 本地域的域用户可以访问服务器，并可在这些 VM 上运行服务（例如 DBMS 服务）。 但无法在本地的 VM 和 Azure 部署的 VM 之间进行通信和名称解析。 这是大多数 SAP 资产的典型部署方案。 有关详细信息，请参阅 [VPN 网关的设计和规划](../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)以及[使用 Azure 门户创建具有站点到站点连接的 VNet](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

有关在 Azure 公有云上部署 SAP 工作负荷的主题，已发布的各种其他资源中都有所介绍。 强烈建议由拥有相关经验的人员来规划 Azure 中的 SAP HANA 的部署，他们应该了解 Azure IaaS 的原理，知道如何在 Azure IaaS 上部署 SAP 工作负荷。 在继续学习之前，应该先阅读以下资源，其中提供了更多信息：

- [在 Windows 虚拟机 (VM) 上使用 SAP](../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [在 Microsoft Azure 虚拟机上使用 SAP 解决方案](../virtual-machines-linux-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="certification"></a>认证

除了 NetWeaver 认证以外，SAP 还要求通过 SAP HANA 的特殊认证，以便在某些基础结构（如 Azure IaaS）上支持 SAP HANA。

[SAP 说明 #1928533 - Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型](https://launchpad.support.sap.com/#/notes/1928533)是有关 NetWeaver 的核心 SAP 说明，对 SAP HANA 认证做了某种程度的阐述。

[SAP 说明 #2316233 - Microsoft Azure 上的 SAP HANA（大型实例）](https://launchpad.support.sap.com/#/notes/2316233/E)也很重要。 其中涉及到了本指南所述的解决方案。 此外，支持在 Azure GS5 VM 中运行 SAP HANA。 [SAP 网站上发布了有关此方案的信息](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)。

SAP 说明 #2316233 中所述的“Azure 上的 SAP HANA（大型实例）”解决方案可让 Microsoft 和 SAP 客户在 Azure 中部署大型 SAP Business Suite、SAP Business Warehouse (BW)、S/4 HANA、BW/4HANA 或其他 SAP HANA 工作负荷。 此解决方案基于通过 SAP HANA 认证的专用硬件模具（[SAP HANA 定制数据中心集成 – TDI](https://scn.sap.com/docs/DOC-63140)）。 以 SAP TDI 配置的解决方案运行可以确信所有基于 SAP HANA 的应用程序（包括 SAP HANA 上的 SAP Business Suite、SAP HANA 上的 SAP Business Warehouse (BW)、S4/HANA 和 BW4/HANA）正常工作。

与在 Azure 虚拟机中运行 SAP HANA 相比，此解决方案具有一项优势 - 提供的内存量要大得多。 如果想要启用此解决方案，需要在一些重要的方面有所了解：

- SAP 应用程序层和非 SAP 应用程序在普通的 Azure 硬件模具中托管的 Azure 虚拟机 (VM) 上运行。
- 客户的本地基础结构、数据中心和应用程序部署通过 Azure ExpressRoute（建议）或虚拟专用网络 (VPN) 连接到 Microsoft Azure 云平台。 Active Directory (AD) 和 DNS 也扩展到 Azure 中。
- HANA 工作负荷的 SAP HANA 数据库实例在 Azure 上的 SAP HANA（大型实例）中运行。 大型实例模具已连接到 Azure 网络，因此，Azure VM 中运行的软件可与 HANA 大型实例中运行的 HANA 实例进行交互。
- Azure 上的 SAP HANA（大型实例）的硬件是基础结构即服务 (IaaS) 中提供的专用硬件，其上已预装 SUSE Linux Enterprise Server或 Red Hat Enterprise Linux。 在 Azure 虚拟机上，操作系统的进一步更新和维护由你负责。
- 在 HANA 大型实例的单元上安装 HANA 或者安装运行 SAP HANA 所需的任何其他组件都由你负责，Azure 上的 SAP HANA 的所有相关日常操作与管理同样由你负责。
- 除了此处所述的解决方案以外，还可以在连接到 Azure 上的 SAP HANA（大型实例）的 Azure 订阅中安装其他组件。  例如，用于实现与 SAP HANA 数据库通信的组件或者直接与其通信的组件（跳接服务器、RDP 服务器、SAP HANA Studio、适用于 SAP BI 方案的 SAP 数据服务，或网络监视解决方案）。
- 与在 Azure 中一样，HANA 大型实例支持实现高可用性和灾难恢复的功能。

## <a name="architecture"></a>体系结构

从较高层面讲，Azure 上的 SAP HANA（大型实例）解决方案的 SAP 应用程序层驻留在 Azure VM 中，数据库层驻留在 SAP TDI 配置的硬件上，该硬件位于连接到 Azure IaaS 的同一 Azure 区域中的大型实例模具中。

> [!NOTE]
> SAP 应用程序层需要与 SAP DBMS 层部署在同一 Azure 区域中。 有关 Azure 上的 SAP 工作负荷的已发布文档中做了详细的阐述。

Azure 上的 SAP HANA（大型实例）的总体体系结构提供了通过 SAP TDI 认证的硬件配置（用于 SAP HANA 数据库的非虚拟化、裸机高性能服务器），并且能够借助 Azure 灵活地缩放 SAP 应用程序层的资源来满足需求。

![Azure 上的 SAP HANA（大型实例）的体系结构概述](./media/sap-hana-overview-architecture/image1-architecture.png)

上述体系结构划分为三个部分：

- **右侧：**在数据中心内运行各种应用程序的本地基础结构，以及访问 LOB 应用程序（例如 SAP）的最终用户。 理想情况下，此本地基础结构使用 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 连接到 Azure。

- **中间：**显示了 Azure IaaS，在本例中，使用了 Azure VM 来承载 SAP 或利用 SAP HANA 作为 DBMS 系统的其他应用程序。 使用由 Azure VM 提供的内存的较小 HANA 实例与其应用程序层一起部署在 Azure VM 中。 了解有关[虚拟机](https://azure.microsoft.com/services/virtual-machines/)的详细信息。
<br />Azure 网络用于将 SAP 系统以及其他应用程序分组到 Azure 虚拟网络 (VNet)。 这些 Vnet 连接到本地系统以及 Azure 上的 SAP HANA（大型实例）。
<br />有关支持在 Azure 中运行的 SAP NetWeaver 应用程序和数据库，请参阅 [SAP 说明 #1928533 - Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型](https://launchpad.support.sap.com/#/notes/1928533)。 有关在 Azure 上部署 SAP 解决方案的文档，请查看：

  -  [在 Windows 虚拟机 (VM) 上使用 SAP](../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [在 Microsoft Azure 虚拟机上使用 SAP 解决方案](../virtual-machines-linux-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **左侧：**显示了 Azure 大型实例模具中通过 SAP HANA TDI 认证的硬件。 HANA 大型实例单元使用与从本地连接到 Azure 时相同的技术连接到订阅的 Azure VNet。

Azure 大型实例模具本身包含以下组件：

- **计算：**基于 Intel Xeon E7-8890v3 处理器提供必要的计算功能，是通过 SAP HANA 认证的服务器。
- **网络：**统一的高速网络结构，与计算、存储和 LAN 组件互连。
- **存储：**通过统一网络结构访问的存储基础结构。 根据所部署的特定的 Azure 上的 SAP HANA（大型实例）配置提供特定的存储容量（可以通过每月额外付费的方式获得更多存储容量）。

在大型实例模具的多租户基础结构中，客户被部署为隔离的租户。 这些租户与 Azure 订阅之间存在 1:1 的对应关系。 这意味着，你无法从两个不同的 Azure 订阅访问在 Azure 大型实例模具中运行的 Azure 上的 SAP HANA（大型实例）的同一实例。

与 Azure VM 一样，Azure 上的 SAP HANA（大型实例）是在多个 Azure 区域中提供的。 若要提供灾难恢复功能，可以选择启用。 各个 Azure 区域中的大型实例模具相互连接在一起。

如同可以为 Azure 虚拟机选择不同的 VM 类型一样，你可以从针对 SAP HANA 的不同工作负荷类型定制的具有不同 SKU 的 HANA 大型实例中进行选择。 SAP 基于 Intel 处理器世代为各种工作负荷应用内存对处理器插槽比率 - 提供了四种不同的 SKU 类型：

从 2016 年 12 月开始，Azure 上的 SAP HANA（大型实例）将以六种配置在美国西部和美国东部的 Azure 区域中提供：

| SAP 解决方案 | CPU | RAM | 存储 |
| --- | --- | --- | --- |
| 针对 OLAP 优化的：SAP BW、BW/4HANA<br /> 或 SAP HANA，适用于一般 OLAP 工作负荷 | Azure 上的 SAP HANA S72<br /> – 2 x Intel® Xeon® 处理器 E7-8890 v3 |  768 GB |  3 TB |
| --- | Azure 上的 SAP HANA S144<br /> – 4 x Intel® Xeon® 处理器 E7-8890 v3 |  1.5 TB |  6 TB |
| --- | Azure 上的 SAP HANA S192<br /> – 4 x Intel® Xeon® 处理器 E7-8890 v4 |  2.0 TB |  8 TB |
| 针对 OLTP 优化的：SAP HANA 或 S/4HANA 上的<br /> SAP Business Suite (OLTP)，<br /> 适用于一般 OLTP | Azure 上的 SAP HANA S72m<br /> – 2 x Intel® Xeon® 处理器 E7-8890 v3 |  1.5 TB |  6 TB |
|---| Azure 上的 SAP HANA S144m<br /> – 4 x Intel® Xeon® 处理器 E7-8890 v3 |  3.0 TB |  12 TB |
|---| Azure 上的 SAP HANA S192m<br /> – 4 x Intel® Xeon® 处理器 E7-8890 v4 |  4.0 TB |  16 TB |

[SAP 支持说明 #2316233 - Microsoft Azure 上的 SAP HANA（大型实例）](https://launchpad.support.sap.com/#/notes/2316233/E)中介绍了上述各种配置。

选择的具体配置取决于工作负荷、CPU 资源和所需的内存。 OLTP 工作负荷可以利用针对 OLAP 工作负荷进行了优化的 SKU。 同样，可以为 OLAP HANA 工作负荷利用 OLTP SKU。 但是，你可能需要将 HANA 利用的内存限制为通过 Intel E7 代处理器认证的内存，如 [SAP 网站](http://global12.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/appliances.html)上针对_放大 HANA 上的 BW_设备类型所列。

请务必注意，不会将大型实例模具的整个基础结构以独占方式分配给单个客户使用。 这适用于 Azure 上的 SAP HANA（大型实例），就像对待通过 Azure 中部署的网络结构连接的计算和存储资源的机架一样。 HANA 大型实例基础结构（例如 Azure）部署通过网络隔离实现了相互隔离的不同客户&quot;租户&quot;。 因此，HANA 大型实例的这些部署不会相互干扰，也看不到对方。 大型实例模具中部署的每个租户分配给一个 Azure 订阅。 如果有另一个也需要 HANA 大型实例的 Azure 订阅，则它将部署在大型实例模具中的一个单独租户中，并且将在网络中实施所有隔离以防止这些实例相互之间直接通信。

不过，在 HANA 大型实例上运行 SAP HANA 与在 Azure 中部署的 Azure VM 上运行 SAP HANA 之间有重大差别：

- Azure 上的 SAP HANA（大型实例）没有虚拟化层。 你可以利用底层裸机硬件的性能。
- 与 Azure 不同，Azure 上的 SAP HANA（大型实例）服务器专用于特定客户。 重新启动或关闭服务器不会导致操作系统和 SAP HANA 被部署在另一台服务器上。 （唯一的例外是当服务器可能遇到了问题并且需要在另一台刀片服务器上重新部署时。）
- 在 Azure 中，主机处理器类型是根据最佳性价比选择的，与之不同，为 Azure 上的 SAP HANA（大型实例）选择的处理器类型是 Intel E7v3 处理器系列中性能最高的类型。

将有多个客户部署到 Azure 上的 SAP HANA（大型实例）硬件上，并且他们部署到其自己的 VLAN 中，因而他们之间是相互屏蔽的。 为了将 HANA 大型实例连接到 Azure 虚拟网络 (VNet)，已就位的网络组件将在硬件基础结构中的 Azure VNet IP 地址范围与 VLAN IP 地址空间之间执行网络地址转换 (NAT)。

## <a name="operations-model-and-responsibilities"></a>操作模型和责任

Azure 上的 SAP HANA（大型实例）提供的服务与 Azure IaaS 服务相符。 你将得到一个 HANA 大型实例，其中安装了针对 SAP HANA 进行了优化的操作系统。 与 Azure IaaS VM 一样，执行对 OS 进行强化的大多数任务、安装所需的额外软件、安装 HANA、操作 OS 和 HANA，以及更新 OS 和 HANA 都由你负责。 Microsoft 不会强制你更新 OS 或 HANA。

![Azure 上的 SAP HANA（大型实例）的责任](./media/sap-hana-overview-architecture/image2-responsibilities.png)

如上图所示，Azure 上的 SAP HANA（大型实例）是一种多租户基础结构即服务产品。 因此，大多数情况下，责任的划分在 OS 基础结构边界处进行。 Microsoft 负责维护该服务在操作系统层之下的所有方面，你负责维护该服务在操作系统层之上的方面（包括操作系统）。 因此，你当前用于合规性、安全性、应用程序管理、基础和 OS 管理的大多数本地方法仍然可以继续使用。 系统看起来好像所有方面都位于你的网络中。

不过，此服务针对 SAP HANA 进行了优化，因此，在许多方面你需要与 Microsoft 协作，以便使用底层基础结构功能实现最佳效果。

下表提供了有关每个层和你的职责的更多详细信息：

**网络：**运行 SAP HANA 的大型实例模具中所有用于对其存储的访问、实例间的连接（用于扩展和其他功能）、与布局的连接以及与 Azure（其中的 SAP 应用程序层承载在 Azure 虚拟机中）的连接的内部网络。 它还在 Azure 数据中心之间提供了 WAN 连接，以便为数据恢复目的而进行复制。 所有网络都按租户进行分区，并且应用了 QOS。

**存储：**用于 SAP HANA 服务器需要的所有卷的虚拟化已分区存储，以及用于快照的虚拟化已分区存储。

**服务器：**专用物理服务器，用于运行分配给租户的 SAP HANA 数据库。 它们是虚拟化硬件。

**SDDC：**用来将数据中心作为软件定义的实体进行管理的管理软件。 Microsoft 可以通过它出于规模、可用性和性能原因而创建资源池。

**O/S：**你选择的在服务器上运行的 OS（SUSE Linux 或 Red Hat Linux）。 向你提供的 OS 映像将是各个 Linux 供应商提供给 Microsoft 用于运行 SAP HANA 的映像。 将要求你具有 Linux 供应商的订阅，以便获取 SAP HANA 优化的特定映像。 你的责任包括向 OS 供应商注册映像。 从 Microsoft 移交的观点来看，你还对进一步修补 Linux 操作系统负有责任。 这涉及成功的 SAP HANA 安装可能所需的附加包（请参阅 SAP HANA 安装文档和 SAP 说明），而这些包尚未由特定 Linux 供应商在其 SAP HANA 优化的 OS 映像中提供。 客户的责任还包括修补 OS，这与 OS 不正常工作/优化相关，其驱动程序涉及特定服务器硬件。 或对 OS 的任何安全性或功能进行修补。 客户的职责还包括对以下项进行监视和容量规划：

- CPU 资源消耗
- 内存消耗
- 与可用空间、IOPS 和延迟相关的磁盘卷
- HANA 大型实例与 SAP 应用程序层之间的网络卷流量

HANA 大型实例的底层基础结构提供了用于备份和还原 OS 卷的功能。 利用此功能也是你的责任。

**中间件：**主要是 SAP HANA 实例。 管理、操作和监视由你负责。 你可以通过提供的一项功能使用存储快照进行备份/还原和灾难恢复。 这些功能是由基础结构提供的。 但是，你的责任还包括利用这些功能构建高可用性或灾难恢复功能，利用它们，以及监视已成功执行的存储快照。

**数据：**由 SAP HANA 管理的数据，以及位于卷或文件共享上的其他数据（例如备份文件）。 你的责任包括监视磁盘可用空间、管理卷上的内容，以及监视磁盘卷备份和存储快照的成功执行。 不过，确保数据成功复制到 DR 站点是 Microsoft 的责任。

**应用程序：**SAP 应用程序实例；对于非 SAP 应用程序，则指那些应用程序的应用程序层。 你的责任包括部署、管理、操作和监视与以下各项的容量规划相关的那些应用程序：CPU 资源消耗、内存消耗、Azure 存储消耗、Azure VNet 内部的网络带宽消耗以及从 Azure VNet 到 Azure 上的 SAP HANA（大型实例）之间的网络带宽消耗。

**WAN：**为工作负荷建立的从本地到 Azure 部署的连接。 如果工作负荷是任务关键型的，请使用 Azure ExpressRoute。 此连接不是 Azure 上的 SAP HANA（大型实例）的一部分，因此需要由你负责设置此连接。

**存档：**你可能希望使用自己的方法在存储帐户中对数据副本进行存档。 这涉及到管理、合规性、成本和操作。 你负责在 Azure 上生成存档副本和备份并以合规的方式存储它们。

请参阅[Azure 上的 SAP HANA（大型实例）的 SLA](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/)。

## <a name="sizing"></a>调整大小

为 HANA 大型实例调整大小与为一般 HANA 调整大小没有什么不同。 对于现有的已部署系统，你可能希望从其他 RDBMS 迁移到 HANA，SAP 提供了许多在现有 SAP 系统上运行的报告。 它们检查数据，并计算将数据库移动到 HANA 时表的内存需求。 请阅读以下 SAP 说明来获取有关如何运行这些报告以及如何获取其最新修补程序/版本的详细信息：

- SAP 说明 #1793345 - 为 HANA 上的 SAP 套件调整大小
- SAP 说明 #1872170 - HANA 和 S/4 HANA 上的套件的大小调整报告
- SAP 说明 #2121330 - 常见问题：HANA 上的 SAP BW 的大小调整报告
- SAP 说明 #1736976 - 适用于 HANA 上的 BW 的大小调整报告
- SAP 说明 #2296290 - 新的适用于 HANA 上的 BW 的大小调整报告

为实现绿色的字段实施，可以使用 SAP Quick Sizer 来计算在 HANA 的基础上实施 SAP 软件时的内存需求。

HANA 的内存需求将随数据量增长而增加，因此，你需要了解当前的内存消耗并能够预测将来的内存消耗。 然后，你可以根据内存需求将你的需求映射到其中一个 HANA 大型实例 SKU。

## <a name="requirements"></a>要求

运行 Azure 上的 SAP HANA（大型实例）有一些要求。

**Microsoft Azure：**

- 一个可以链接到 Azure 上的 SAP HANA（大型实例）的 Azure 订阅。
- Microsoft 高级任务关键型支持。 有关与在 Azure 中运行 SAP 相关的特定信息，请参阅 [SAP 支持说明 #2015553 – Microsoft Azure 上的 SAP：支持先决条件](https://launchpad.support.sap.com/#/notes/2015553)。
- 要知道对 SAP 执行大小调整操作后你需要的 HANA 大型实例 SKU。

**网络连接：**

- 本地到 Azure 之间的 Azure ExpressRoute：确保你的 ISP 提供至少 1 Gbps 的连接来将本地数据中心连接到 Azure

**操作系统：**

- SUSE Linux Enterprise Server 12 for SAP Applications 的许可证。

> [!NOTE] 
> Microsoft 提供的操作系统未向 SUSE 注册，也未与 SMT 实例连接。

- 在 Azure 中的 Azure VM 上部署的 SUSE Linux 订阅管理工具 (SMT)。 这使得 Azure 上的 SAP HANA（大型实例）能够由 SUSE 进行注册并相应地进行更新（因为 HANA 大型实例数据中心内没有Internet 访问）。 
- 适用于 SAP HANA 的 Red Hat Enterprise Linux 6.7 或 7.2 的许可证。

> [!NOTE]
> Microsoft 提供的操作系统未向 Red Hat 注册，也未连接到 Red Hat 订阅管理器实例。

- 在 Azure 中的 Azure VM 上部署的 Red Hat 订阅管理器。 这使得 Azure 上的 SAP HANA（大型实例）能够由 Red Hat 进行注册并相应地进行更新（因为 Azure 大型实例模具上部署的租户内没有直接 Internet 访问）。
- 与 Linux 提供商的服务和支持协定，隐式包含在特定 Linux 版本订阅或其他服务和支持协定中，涵盖所用的特定 Linux 版本，并满足 SAP 的条件。

**数据库：**

- SAP HANA（平台版或企业版）的许可证和软件安装组件。

**应用程序：**

- 连接到 SAP HANA 和相关的 SAP 支持合同的任何 SAP 应用程序的许可证和软件安装组件。
- 与 Azure 上的 SAP HANA（大型实例）环境和相关的支持合同配合使用的任何非 SAP 应用程序的许可证和软件安装组件。

**技能：**

- 有关 Azure IaaS 及其组件的经验和知识。
- 有关在 Azure 中部署 SAP 工作负荷的经验和知识。
- 通过了 SAP HANA 安装技能认证的人员。
- 针对 SAP HANA 设计高可用性和灾难恢复解决方案的 SAP 架构技能。

## <a name="networking"></a>网络

Azure 网络的体系结构是成功部署 SAP 应用程序的关键组件。 通常，Azure 上的 SAP HANA（大型实例）部署具有较大的 SAP 布局和多种不同的 SAP 解决方案，其中具有可变的数据库大小、CPU 资源消耗和内存利用。 这些 SAP 系统中很可能只有一两个基于 SAP HANA，因此，SAP 布局可能是混合式的，它利用以下各项：

- 在本地部署的 SAP 系统。 由于其大小的原因，当前无法承载在 Azure 中；一个典型的示例是在 Microsoft SQL Server（用作数据库）上运行且具有 &gt;100 个 CPU 和 1 TB 内存的 SAP ERP 生产系统。
- 在本地部署的基于 SAP HANA 的 SAP 系统（例如，其 SAP HANA 数据库需要 6 TB 或更多内存的 SAP ERP 系统）。
- 在 Azure VM 中部署的 SAP 系统。 这些系统可能是可以根据资源消耗和内存需求成功在 Azure 中的 VM 上部署的任何基于 SAP NetWeaver 的应用程序的开发、测试、沙盒或生产实例。 它们也可能基于数据库，例如 SQL Server（请参阅 [SAP 支持说明 #1928533 – Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型](https://launchpad.support.sap.com/#/notes/1928533/E)）或 SAP HANA（请参阅[通过 SAP HANA 认证的 IaaS 平台](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)）。
- 在 Azure 中的 VM 上部署的 SAP 应用程序服务器，它们利用 Azure 大型实例模具中的 Azure 上的 SAP HANA（大型实例）。

虽然混合式 SAP 布局（具有四种或更多部署方案）比较常见，但是有些情况下 Azure 中也有完整的 SAP 布局，并且随着 Microsoft Azure VM 的日益强大，在 Azure VM 上部署的 SAP 解决方案的数量也会越来越多。

Azure 中部署的 SAP 系统的上下文中的 Azure 网络并不复杂。 它基于以下原则：

- Azure 虚拟网络 (VNet) 需要连接到与本地网络相连的 Azure ExpressRoute 线路。
- ExpressRoute 线路的带宽通常应当为 1 Gbps 或更高。 这将有足够的带宽用来在本地系统与在 Azure VM 上运行的系统之间（以及通过从本地最终用户到 Azure 系统的连接）传输数据。
- 需要在 Azure VNet 中设置 Azure 中的所有 SAP 系统以使其相互通信。
- 本地承载的 Active Directory 和 DNS 通过 ExpressRoute 扩展到 Azure 中。

**建议：**在 Azure 上的单个 Azure 订阅中部署完整的 SAP 布局。 SAP 布局中的许多过程要求在 SAP 开发、测试和生产实例之间具有透明且尽可能少的网络连接，并且 SAP NetWeaver 体系结构中有许多自动机制依赖于这些不同实例之间的这一透明网络。 因此，强烈建议将整个 SAP 布局放置在单个 Azure 订阅中，即使布局是跨多个 Azure 区域部署的也是如此。

与 Azure 上的 SAP HANA（大型实例）相关的体系结构和进程都是基于以上建议构建的。

> [!NOTE] 
> 单个 Azure 订阅只能链接到特定 Azure 区域中的大型实例模具中的单个租户，反过来，单个大型实例模具租户只能链接到单个 Azure 订阅。

将 Azure 上的 SAP HANA（大型实例）部署在两个不同的 Azure 区域中会导致在大型实例模具中部署单独的租户。 不过，两个租户将位于同一 Azure 订阅中，只要这些实例是同一 SAP 布局的一部分。

> [!IMPORTANT] 
> Azure 上的 SAP HANA（大型实例）仅支持 Azure Resource Manager 部署。

### <a name="additional-azure-vnet-information"></a>Azure VNet 的其他信息

若要将 Azure VNet 连接到 ExpressRoute，必须创建 Azure 网关（请参阅[关于 ExpressRoute 的虚拟网络网关](../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)）。 Azure 网关可以与位于 Azure 外部的基础结构的 ExpressRoute（或者是 Azure 大型实例模具的 ExpressRoute）一起使用，也可以连接在 Azure VNet 之间（请参阅[使用 PowerShell 为 Resource Manager 配置 VNet 到 VNet 连接](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)）。 可以将 Azure 网关连接到最多四个不同的 ExpressRoute 连接，只要这些连接来自不同的 MS Enterprise Exchange (MSEE) 即可。

> [!NOTE] 
> 对于两种用例，Azure 网关提供的吞吐量不同（请参阅[关于 VPN 网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)）。 使用 ExpressRoute 连接时，通过 VNet 网关可以获得的最大吞吐量为 10 Gbps。 请注意，在位于 Azure VNet 中的 Azure VM 与本地系统之间复制文件（使用单一复制流）不会获得各种网关 SKU 的完整吞吐量。 要利用 VNet 网关的完整带宽，必须使用多个流，或者采用单一文件的并行流复制各个文件。

阅读上述文章时，请仔细留意有关 UltraPerformance 网关 SKU 在不同 Azure 区域中的可用性的信息。

### <a name="networking-for-sap-hana-on-azure"></a>Azure 上的 SAP HANA 的网络

要连接到 Azure 上的 SAP HANA（大型实例），Azure VNet 必须通过其 VNet 网关使用客户的本地网络的 ExpressRoute 进行连接。 另外，它还需要通过另一个 ExpressRoute 线路连接到位于 Azure 大型实例模具中的 HANA 大型实例。 VNet 网关将具有至少两个 ExpressRoute 连接，并且两个连接将共享 VNet 网关的最大带宽。

> [!IMPORTANT] 
> 假定所有的网络流量都位于 SAP 应用程序层与数据库层之间，则仅支持使用 VNet 的 HighPerformance 或 UltraPerformance 网关 SKU 来连接到 Azure 上的 SAP HANA（大型实例）。

![连接到了 Azure 上的 SAP HANA（大型实例）和本地的 Azure VNet](./media/sap-hana-overview-architecture/image3-on-premises-infrastructure.png)

### <a name="single-sap-system"></a>单个 SAP 系统

上面所示的本地基础结构通过 ExpressRoute 连接到 Azure，并且 ExpressRoute 线路连接到 Microsoft Enterprise 边缘路由器 (MSEE)（请参阅 [ExpressRoute 技术概述](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)）。 在建立之后，该路由将连接到 Microsoft Azure 主干，并且所有 Azure 区域都可供访问。

> [!NOTE] 
> 若要在 Azure 中运行 SAP 布局，请连接到距离 SAP 布局中的 Azure 区域最近的 MSEE。 Azure 大型实例模具通过专用 MSEE 设备进行连接，从而最大程度地降低 Azure IaaS 中的 Azure VM 与大型实例模具之间的网络延迟。

承载着 SAP 应用程序实例的 Azure VM 的 VNet 网关连接到该 ExpressRoute 线路，并且同一 VNet 连接到一个专门用于连接大型实例模具的单独 MSEE 路由器。

这是单个 SAP 系统的直观示例，其中 SAP 应用程序层承载在 Azure 中，SAP HANA 数据库在 Azure 上的 SAP HANA（大型实例）上运行。 假设条件是 2 Gbps 或 10 Gbps 的 VNet 网关带宽不会成为瓶颈。

### <a name="multiple-sap-systems-or-large-sap-systems"></a>多个 SAP 系统或大型 SAP 系统

如果部署了连接到 Azure 上的 SAP HANA（大型实例）的多个 SAP 系统或大型 SAP 系统，则 HighPerformance VNet 网关 SKU 的吞吐量可能会成为瓶颈。 在这种情况下，请选择 UltraPerformance SKU（如果可用）。 不过，如果只有 HighPerformance SKU（最高 2 Gbps）可用，或者 UltraPerformance SKU（最高 10 Gbps）的潜力不足，则需要将应用程序层拆分到多个 Azure VNet 中。

要获得缩放性更好的网络体系结构，请采取以下措施：

- 对于单个较大的 SAP 应用程序层，使用多个 Azure VNet。
- 为所部署的每个 SAP 系统部署一个单独的 Azure VNet，而不是将这些 SAP 系统集中放置在同一 VNet 中的各个子网中。

 适用于 Azure 上的 SAP HANA（大型实例）的缩放性更好的网络体系结构：

![跨多个 Azure VNet 部署 SAP 应用程序层](./media/sap-hana-overview-architecture/image4-networking-architecture.png)

如果跨多个 Azure VNet 部署 SAP 应用程序层或组件（如上所示），可能会引入在托管在这些 Azure VM 中的应用程序之间进行通信时产生的不可避免的延迟开销。 默认情况下，位于不同 VNet 中的 Azure VM 之间的网络流量将通过此配置中的 MSEE 路由器进行传输。 但是，自 2016 年 9 月以后可以避免和优化此问题。 优化并降低两个 VNet 之间的通信延迟的方法是将同一区域内的 Azure VNet 对等互连。 即使这些 VNet 在不同订阅中，也是如此。 通过 Azure VNet 对等互连，两个不同 Azure Vnet 中的 VM 可以使用 Azure 网络主干来直接相互通信。 从而显示类似的延迟，就像这些 VM 在同一 VNet 中一样。 而发送到通过 Azure VNet 网关连接的 IP 地址范围的流量则通过单独的 VNet 网关进行路由。 可以在 [VNet 对等互连](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview)一文中获取有关 Azure VNet 对等互连的详细信息。
### <a name="minimal-deployment"></a>最小部署

对于小型 SAP 系统（最小部署），Azure VM 将 SAP 应用程序层承载在本机 Azure 中（在单个 VNet 内），并且通过 ExpressRoute 连接到大型实例模具。 请执行以下步骤使 Azure 上的 SAP HANA（大型实例）就绪可用：

- 收集与四个不同的 IP 地址范围相关的特定信息：

  1. P2P 连接的 /29 地址范围，用于 ExpressRoute 线路。
  
  2. /24（推荐）唯一 CIDR 块，用于为 Azure 上的 SAP HANA（大型实例）分配所需的特定 IP 地址。
  3. 一个或多个 /24（推荐）CIDR 块，用于 Azure VNet 租户子网。 这些是客户的 Azure 订阅中的子网，与 SAP 相关的 Azure VM 将位于其中；允许使用此类地址访问 Azure 上的 SAP HANA（大型实例）。 每个子网应当有一个租户地址块，并且如果这些块是连续的并且在同一 VNet 中，则它们可以聚合。
  4. VNet 网关子网的 /28 地址范围（如果需要 P2S 网络，则必须使用 /27）。

  - 前两个范围是必需的（分别用于 Azure 订阅和区域）。 第 3 项和第 4 项中陈述的 IP 地址范围是每个 Azure VNet 所需的最小范围，并且如果 VNet 中需要有多个子网/租户，则应当为第 3 项指定多个范围。
![Azure 上的 SAP HANA（大型实例）最小部署中必需的 IP 地址范围](./media/sap-hana-overview-architecture/image5-ip-address-range-a.png)

  -  如果在 Azure VNet 中配置多个租户子网：![其中包含用于 Azure VNet 的连续地址空间的 IP 地址范围](./media/sap-hana-overview-architecture/image6-ip-address-range-b.png)

> [!IMPORTANT] 
> 上面指定的每个 IP 地址范围都不得与任何其他范围重叠；每个都必须是离散的并且不能是任何其他范围的子网。 只有第 3 项和第 4 项中定义的地址应当应用于 Azure VNet，所有其他地址用于大型实例连接和路由。 此外，作为最佳做法，地址空间地址范围应当与子网范围相匹配，并且不应具有空的或未分配的空间。 如果在第 1 项和第 2 项中定义的范围与第 3 项和第 4 项中定义的范围之间存在重叠，则 Azure VNet 将不会连接到 ExpressRoute 线路。

- Express Route 线路是由 Microsoft 在你的订阅与大型实例模具之间创建的。
- 请在大型实例模具上创建网络租户。
- 在 Azure 上的 SAP HANA（大型实例）基础结构中配置网络以接受在 Azure VNet 中指定的范围中的 IP 地址，将通过这些地址与 HANA 大型实例进行通信。
- 在大型实例模具的客户租户中设置网络地址转换 (NAT)（以便将租户内部 IP 地址映射为由 Azure 的租户定义的 IP 地址）。
- 根据所购买的具体的 Azure 上的 SAP HANA（大型实例）SKU，在租户网络中分配一个计算单元，分配并装载存储，然后安装操作系统（SUSE 或 RedHat Linux）。

Azure 上的 SAP HANA（大型实例）网络体系结构的最小部署：

![包含 IP 地址范围的最小部署](./media/sap-hana-overview-architecture/image7-minimal-deployment.png)

### <a name="routing-in-azure"></a>Azure 中的路由

对于 Azure 上的 SAP HANA（大型实例），有两个重要的网络路由注意事项：

1. Azure 上的 SAP HANA（大型实例）只能由 Azure VM 通过专用 ExpressRoute 连接进行访问，不能从本地直接访问。 因此，需要进行直接访问的管理客户端和任何应用程序（例如在本地运行的 SAP Solution Manager）都无法连接到 SAP HANA 数据库。

2. Azure 上的 SAP HANA（大型实例）有一个来自所定义的 NAT 池的已分配 IP 地址。 可以通过 Azure 订阅和 ExpressRoute 访问此 IP 地址。 因为该 IP 地址是 NAT 池的一部分，因此你需要在环境中执行额外的网络配置。 有关详细信息，请参阅与 SAP HANA 安装相关的文章。

> [!NOTE] 
> 在某个_数据仓库_方案中，应用程序和/或最终用户需要连接到 SAP HANA 数据库（直接运行），在这种情况下，如果需要连接到 Azure 上的 SAP HANA（大型实例），则必须使用另一个网络组件：使用一个反向代理来往返路由数据。 例如，使用 Azure 中部署的带流量管理器的 F5 BIG-IP 作为虚拟防火墙/流量路由解决方案。

### <a name="leveraging-in-multiple-regions"></a>在多个区域中利用

除了灾难恢复之外，可能还有其他原因需要你在多个 Azure 区域中部署 Azure 上的 SAP HANA（大型实例）。 你可能希望从各个区域中的不同 VNet 中部署的每个 VM 访问 HANA 大型实例。 由于不同 HANA 大型实例服务器的 NAT 转换后地址不会跨越（通过其网关直接连接到实例的）Azure VNet 进行传播，因此对上面介绍的 VNet 设计进行了细微的更改：Azure VNet 网关可以处理来自不同 MSEE 的四个不同的 ExpressRoute 线路，连接到其中一个大型实例模具的每个 VNet 都可以连接到另一 Azure 区域中的大型实例模具。

![连接到了不同 Azure 区域中的 Azure 大型实例模具的 Azure VNet](./media/sap-hana-overview-architecture/image8-multiple-regions.png)

上图显示了两个区域中的不同 Azure VNet 如何连接到两个不同的 ExpressRoute 线路，这两个线路用来连接到两个 Azure 区域中的 Azure 上的 SAP HANA（大型实例）。 新引入的连接采用矩形红色线条标出。 有了来自 Azure VNet 的这些连接，在那些 VNet 之一中运行的 VM 可以访问在两个区域中部署的每个不同的 HANA 大型实例单元（假定你使用同一订阅）。 以上各图假定你创建了从本地到两个 Azure 区域的两个 ExpressRoute 连接；出于灾难恢复方面的考虑，建议使用此设置。

> [!IMPORTANT] 
> 如果使用了多个 ExpressRoute 线路，则应当使用“AS 路径前追加”和“本地首选 BGP”设置来确保对流量进行正确路由。



