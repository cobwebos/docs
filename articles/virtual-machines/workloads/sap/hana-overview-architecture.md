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
ms.date: 10/02/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 63e1820033e051b72601291c5206772192e68769
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Azure 上的 SAP HANA（大型实例）概述和体系结构

## <a name="what-is-sap-hana-on-azure-large-instances"></a>什么是 Azure 上的 SAP HANA（大型实例）？

Azure 上的 SAP HANA（大型实例）是一种针对 Azure 的独特解决方案。 除了提供 Azure 虚拟机以用于部署和运行 SAP HANA，Azure 还使你可以在专用于客户的逻辑服务器上运行和部署 SAP HANA。 Azure 上的 SAP HANA（大型实例）解决方案在分配给客户的非共享主机/服务器裸机硬件上进行构建。 服务器硬件嵌入在包含计算/服务器、网络和存储基础结构的较大模具中。 这作为组合经过了 HANA TDI 认证。 Azure 上的 SAP HANA（大型实例）的服务可提供各种不同的服务器 SKU 或规模（从具有 72 个 CPU 和 768 GB 内存的单元开始，到具有 960 个 CPU 和 20 TB 内存的单元）。

基础结构模具中的客户隔离在租户中执行，详细情况如下所示：

- 网络：对于分配了租户的每个客户，通过虚拟网络实现基础结构堆栈中的客户隔离。 一个租户分配给单个客户。 一个客户可以具有多个租户。 租户的网络隔离禁止基础结构模具级别中的租户之间进行网络通信。 即使租户属于同一个客户。
- 存储组件：通过分配了存储卷的存储虚拟机实现隔离。 存储卷只能分配给一个存储虚拟机。 存储虚拟机以独占方式分配给 SAP HANA TDI 认证的基础结构堆栈中的单个租户。 因此，只能在一个特定相关租户中访问分配给存储虚拟机的存储卷。 这些存储卷在部署的不同租户之间不可见。
- 服务器或主机：服务器或主机单元不在客户或租户之间进行共享。 部署到客户的服务器或主机是分配给单个租户的原子裸机计算单元。 **不**使用硬件分区或软分区，这可能会导致某个客户与其他客户共享主机或服务器。 分配给特定租户的存储虚拟机的存储卷会装载到这类服务器。 可以按独占方式向一个租户分配具有不同 SKU 的一个到多个服务器单元。
- 在 Azure 上的 SAP HANA（大型实例）基础结构模具中，会部署许多不同的租户，并通过网络、存储和计算级别上的租户概念使它们相互隔离。 


仅支持这些裸机服务器单元运行 SAP HANA。 SAP 应用层或工作负荷中间软件层在 Microsoft Azure 虚拟机中运行。 运行 Azure 上的 SAP HANA（大型实例）单元的基础结构模具连接到 Azure 网络骨干网，因此可提供 Azure 上的 SAP HANA（大型实例）单元与 Azure 虚拟机之间的那种低延迟连接。

本文档是介绍 Azure 上的 SAP HANA（大型实例）主题的五个文档之一。 在本文档中，我们介绍了该解决方案的基本体系结构、职责、提供的服务并概括介绍了功能。 对于大多数领域（如网络和连接），其他四个文档涵盖了详细信息并进行了深入介绍。 Azure 上的 SAP HANA（大型实例）文档不涵盖 SAP NetWeaver 安装或 Azure VM 中的 SAP NetWeaver 部署这些方面。 同一文档容器中的不同文档中介绍了此主题。 


本指南的五个部分涵盖以下主题：

- [Azure 上的 SAP HANA（大型实例）概述和体系结构](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [如何安装和配置 Azure 上的 SAP HANA（大型实例）](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure 上的 SAP HANA（大型实例）的故障排除和监视](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="definitions"></a>定义

体系结构和技术部署指南中广泛使用了几个常见定义。 注意以下术语及其含义：

- **IaaS：**基础结构即服务
- **PaaS：**平台即服务
- **SaaS：**软件即服务
- SAP 组件：单个 SAP 应用程序，例如 ECC、BW、Solution Manager 或 EP。 SAP 组件可以基于传统的 ABAP 或 Java 技术，也可以是不基于 NetWeaver 的应用程序，例如业务对象。
- SAP 环境：以逻辑方式组合在一起，用于执行开发、QAS、培训、DR 或生产等业务功能的一个或多个 SAP 组件。
- SAP 布局：表示 IT 布局中所有 SAP 资产的整个格局。 SAP 布局包括所有生产和非生产环境。
- **SAP 系统：**SAP ERP 开发系统、SAP BW 测试系统、SAP CRM 生产系统等等的 DBMS 层与应用程序层的组合。Azure 部署不支持在本地与 Azure 之间分割这两个层。 这意味着，某个 SAP 系统要么部署在本地，要么部署在 Azure 中。 但是，可以将 SAP 布局中的不同系统部署到 Azure 或本地。 例如，可以在 Azure 中部署 SAP CRM 开发系统和测试系统，同时在本地部署 SAP CRM 生产系统。 对于 Azure 上的 SAP HANA（大型实例），应该在 Azure VM 中托管 SAP 系统的 SAP 应用程序层，在 HANA 大型实例模具中的某个单元上托管相关的 SAP HANA 实例。
- **大型实例模具：**已通过 SAP HANA TDI 认证的硬件基础结构堆栈，专门用于在 Azure 中运行 SAP HANA 实例。
- **Azure 上的 SAP HANA（大型实例）：**用于在通过 SAP HANA TDI 认证的、部署在不同 Azure 区域中的大型实例模具中的硬件上运行 HANA 实例的产品的官方名称。 本技术部署指南中广泛使用的相关术语“HANA 大型实例”是“Azure 上的 SAP HANA（大型实例）”的简称。
- 跨界：描述这样一种方案：将 VM 部署到在本地数据中心与 Azure 之间建立了站点到站点、多站点或 ExpressRoute 连接的 Azure 订阅。 在一般的 Azure 文档中，此类部署也称为跨界方案。 连接的原因是为了将本地域、本地 Active Directory/OpenLDAP 和本地 DNS 扩展到 Azure。 本地布局会扩展到 Azure 订阅的 Azure 资产。 经过这种扩展后，VM 可以成为本地域的一部分。 本地域的域用户可以访问服务器，并可在这些 VM 上运行服务（例如 DBMS 服务）。 但无法在本地的 VM 和 Azure 部署的 VM 之间进行通信和名称解析。 这是大多数 SAP 资产的典型部署方案。 有关更多详细信息，请参阅 [VPN 网关的设计和规划](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)和[使用 Azure 门户创建具有站点到站点连接的 VNet](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 指南。
- **租户：**在 HANA 大型实例模具中部署的客户会隔离到“租户”中。 租户在网络、存储和计算层中相互隔离。 因此，分配给不同租户的存储和计算单元在 HANA 大型实例模具级别上无法相互看到或进行通信。 客户可以选择部署到不同的租户中。 即使这样，HANA 大型实例模具级别上的租户之间也不进行通信。

有关在 Azure 公有云上部署 SAP 工作负荷的主题，已发布的各种其他资源中都有所介绍。 强烈建议由拥有相关经验的人员来规划和执行 Azure 中的 SAP HANA 的部署，他们应该了解 Azure IaaS 的原理，知道如何在 Azure IaaS 上部署 SAP 工作负荷。 在继续学习之前，应该先阅读以下资源，其中提供了更多信息：


- [在 Microsoft Azure 虚拟机上使用 SAP 解决方案](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="certification"></a>认证

除了 NetWeaver 认证以外，SAP 还要求通过 SAP HANA 的特殊认证，以便在某些基础结构（如 Azure IaaS）上支持 SAP HANA。

[SAP 说明 #1928533 - Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型](https://launchpad.support.sap.com/#/notes/1928533)是有关 NetWeaver 的核心 SAP 说明，对 SAP HANA 认证做了某种程度的阐述。

[SAP 说明 #2316233 - Microsoft Azure 上的 SAP HANA（大型实例）](https://launchpad.support.sap.com/#/notes/2316233/E)也很重要。 其中涉及到了本指南所述的解决方案。 此外，支持在 Azure GS5 VM 中运行 SAP HANA。 [SAP 网站上发布了有关此方案的信息](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)。

SAP 说明 #2316233 中所述的“Azure 上的 SAP HANA（大型实例）”解决方案可让 Microsoft 和 SAP 客户在 Azure 中部署大型 SAP Business Suite、SAP Business Warehouse (BW)、S/4 HANA、BW/4HANA 或其他 SAP HANA 工作负荷。 此解决方案基于通过 SAP HANA 认证的专用硬件模具（[SAP HANA 定制数据中心集成 – TDI](https://scn.sap.com/docs/DOC-63140)）。 以 SAP HANA TDI 配置的解决方案运行可以确信所有基于 SAP HANA 的应用程序（包括 SAP HANA 上的 SAP Business Suite、SAP HANA 上的 SAP Business Warehouse (BW)、S4/HANA 和 BW4/HANA）会在硬件基础结构上正常工作。

与在 Azure 虚拟机中运行 SAP HANA 相比，此解决方案具有一项优势 - 提供的内存量要大得多。 如果想要启用此解决方案，需要在一些重要的方面有所了解：

- SAP 应用程序层和非 SAP 应用程序在普通的 Azure 硬件模具中托管的 Azure 虚拟机 (VM) 上运行。
- 客户的本地基础结构、数据中心和应用程序部署通过 Azure ExpressRoute（建议）或虚拟专用网络 (VPN) 连接到 Microsoft Azure 云平台。 Active Directory (AD) 和 DNS 也扩展到 Azure 中。
- HANA 工作负荷的 SAP HANA 数据库实例在 Azure 上的 SAP HANA（大型实例）中运行。 大型实例模具已连接到 Azure 网络，因此，Azure VM 中运行的软件可与 HANA 大型实例中运行的 HANA 实例进行交互。
- Azure 上的 SAP HANA（大型实例）的硬件是基础结构即服务 (IaaS) 中提供的专用硬件，其上已预装 SUSE Linux Enterprise Server或 Red Hat Enterprise Linux。 在 Azure 虚拟机上，操作系统的进一步更新和维护由你负责。
- 在 HANA 大型实例的单元上安装 HANA 或者安装运行 SAP HANA 所需的任何其他组件都由你负责，Azure 上的 SAP HANA 的所有相关日常操作与管理同样由你负责。
- 除了此处所述的解决方案以外，还可以在连接到 Azure 上的 SAP HANA（大型实例）的 Azure 订阅中安装其他组件。  例如，用于实现与 SAP HANA 数据库通信的组件或者直接与其通信的组件（跳接服务器、RDP 服务器、SAP HANA Studio、适用于 SAP BI 方案的 SAP 数据服务，或网络监视解决方案）。
- 与在 Azure 中一样，HANA 大型实例提供支持性的高可用性和灾难恢复功能。

## <a name="architecture"></a>体系结构

从较高层面讲，Azure 上的 SAP HANA（大型实例）解决方案的 SAP 应用程序层驻留在 Azure VM 中，数据库层驻留在 SAP TDI 配置的硬件上，该硬件位于连接到 Azure IaaS 的同一 Azure 区域中的大型实例模具中。

> [!NOTE]
> 需在 SAP DBMS 层所在的 Azure 区域部署 SAP 应用程序层。 有关 Azure 上的 SAP 工作负荷的已发布信息对此规则做了详细的阐述。 

Azure 上的 SAP HANA（大型实例）的总体体系结构提供了通过 SAP TDI 认证的硬件配置（用于 SAP HANA 数据库的非虚拟化、裸机高性能服务器），并且能够借助 Azure 灵活地缩放 SAP 应用程序层的资源来满足需求。

![Azure 上的 SAP HANA（大型实例）的体系结构概述](./media/hana-overview-architecture/image1-architecture.png)

所示体系结构分为三个部分：

- **右侧：**在数据中心内运行各种应用程序的本地基础结构，以及访问 LOB 应用程序（例如 SAP）的最终用户。 理想情况下，此本地基础结构使用 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 连接到 Azure。

- 中间：显示了 Azure IaaS，而在本例中，显示了如何使用 Azure VM 来托管 SAP 或其他将 SAP HANA 用作 DBMS 系统的应用程序。 使用由 Azure VM 提供的内存的较小 HANA 实例与其应用程序层一起部署在 Azure VM 中。 了解有关[虚拟机](https://azure.microsoft.com/services/virtual-machines/)的详细信息。
<br />Azure 网络用于将 SAP 系统以及其他应用程序分组到 Azure 虚拟网络 (VNet)。 这些 Vnet 连接到本地系统以及 Azure 上的 SAP HANA（大型实例）。
<br />有关支持在 Azure 中运行的 SAP NetWeaver 应用程序和数据库，请参阅 [SAP 说明 #1928533 - Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型](https://launchpad.support.sap.com/#/notes/1928533)。 有关在 Azure 上部署 SAP 解决方案的文档，请查看：

  -  [在 Windows 虚拟机 (VM) 上使用 SAP](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [在 Microsoft Azure 虚拟机上使用 SAP 解决方案](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **左侧：**显示了 Azure 大型实例模具中通过 SAP HANA TDI 认证的硬件。 HANA 大型实例单元使用与从本地连接到 Azure 时相同的技术连接到订阅的 Azure VNet。

Azure 大型实例模具本身包含以下组件：

- 计算：基于 Intel Xeon E7-8890v3 或 Intel Xeon E7-8890v4 处理器的服务器，提供必要的计算能力，并通过 SAP HANA 进行认证。
- **网络：**统一的高速网络结构，与计算、存储和 LAN 组件互连。
- **存储：**通过统一网络结构访问的存储基础结构。 根据所部署的特定的 Azure 上的 SAP HANA（大型实例）配置提供特定的存储容量（可以通过每月额外付费的方式获得更多存储容量）。

在大型实例模具的多租户基础结构中，客户被部署为隔离的租户。 在部署租户时，需在 Azure 合约中命名一个 Azure 订阅。 该订阅将会是对 HANA 大型实例收费所依据的 Azure 订阅。 这些租户与 Azure 订阅之间存在 1:1 的对应关系。 在网络级别，可以从属于不同 Azure 订阅的不同 Azure VNet 访问部署在一个 Azure 区域的一个租户中的 HANA 大型实例单位。 但是，这些 Azure 订阅需要属于同一 Azure 合约。 

与 Azure VM 一样，Azure 上的 SAP HANA（大型实例）是在多个 Azure 区域中提供的。 若要提供灾难恢复功能，可以选择启用。 一个地缘政治区域中的各种大型实例模具相互连接在一起。 例如，在美国西部和美国东部的 HANA 大型实例模具是通过专用网络链接连接的，目的是进行 DR 复制。 

如同可以为 Azure 虚拟机选择不同的 VM 类型一样，可以从针对 SAP HANA 的不同工作负荷类型定制的具有不同 SKU 的 HANA 大型实例中进行选择。 SAP 基于 Intel 处理器世代为各种工作负荷应用内存对处理器插槽比率 - 提供了四种不同的 SKU 类型：

从 2017 年 7 月开始，Azure 上的 SAP HANA（大型实例）以多种配置在多个 Azure 区域发布，其中包括美国西部和美国东部、澳大利亚东部、澳大利亚东南部、西欧、北欧：

| SAP 解决方案 | CPU | 内存 | 存储 | 可用性 |
| --- | --- | --- | --- | --- |
| 针对 OLAP 优化的：SAP BW、BW/4HANA<br /> 或 SAP HANA，适用于一般 OLAP 工作负荷 | Azure 上的 SAP HANA S72<br /> – 2 x Intel® Xeon® 处理器 E7-8890 v3<br /> 36 CPU 内核和 72 CPU 线程 |  768 GB |  3 TB | 可用 |
| --- | Azure 上的 SAP HANA S144<br /> – 4 x Intel® Xeon® 处理器 E7-8890 v3<br /> 72 CPU 内核和 144 CPU 线程 |  1.5 TB |  6 TB | 不再提供 |
| --- | Azure 上的 SAP HANA S192<br /> – 4 x Intel® Xeon® 处理器 E7-8890 v4<br /> 96 CPU 内核和 192 CPU 线程 |  2.0 TB |  8 TB | 可用 |
| --- | Azure 上的 SAP HANA S384<br /> – 8 x Intel® Xeon® 处理器 E7-8890 v4<br /> 192 CPU 内核和 384 CPU 线程 |  4.0 TB |  16 TB | 准备订购 |
| 针对 OLTP 优化的：SAP HANA 或 S/4HANA 上的<br /> SAP Business Suite (OLTP)，<br /> 适用于一般 OLTP | Azure 上的 SAP HANA S72m<br /> – 2 x Intel® Xeon® 处理器 E7-8890 v3<br /> 36 CPU 内核和 72 CPU 线程 |  1.5 TB |  6 TB | 可用 |
|---| Azure 上的 SAP HANA S144m<br /> – 4 x Intel® Xeon® 处理器 E7-8890 v3<br /> 72 CPU 内核和 144 CPU 线程 |  3.0 TB |  12 TB | 不再提供 |
|---| Azure 上的 SAP HANA S192m<br /> – 4 x Intel® Xeon® 处理器 E7-8890 v4<br /> 96 CPU 内核和 192 CPU 线程  |  4.0 TB |  16 TB | 可用 |
|---| Azure 上的 SAP HANA S384m<br /> – 8 x Intel® Xeon® 处理器 E7-8890 v4<br /> 192 CPU 内核和 384 CPU 线程 |  6.0 TB |  18 TB | 准备订购 |
|---| Azure 上的 SAP HANA S384xm<br /> – 8 x Intel® Xeon® 处理器 E7-8890 v4<br /> 192 CPU 内核和 384 CPU 线程 |  8.0 TB |  22 TB |  准备订购 |
|---| Azure 上的 SAP HANA S576<br /> – 12 x Intel® Xeon® 处理器 E7-8890 v4<br /> 288 CPU 内核和 576 CPU 线程 |  12.0 TB |  28 TB | 准备订购 |
|---| Azure 上的 SAP HANA S768<br /> – 16 x Intel® Xeon® 处理器 E7-8890 v4<br /> 384 CPU 内核和 768 CPU 线程 |  16.0 TB |  36 TB | 准备订购 |
|---| Azure 上的 SAP HANA S960<br /> – 20 x Intel® Xeon® 处理器 E7-8890 v4<br /> 480 CPU 内核和 960 CPU 线程 |  20.0 TB |  46 TB | 准备订购 |

- CPU 内核数 = 服务器单元处理器之和的非超线程 CPU 内核数的总和。
- CPU 线程数 = 服务器单元处理器之和的超线程 CPU 内核数所提供的计算线程总和。 所有单元都默认配置为使用超线程。


[SAP 支持说明 #2316233 - Microsoft Azure 上的 SAP HANA（大型实例）](https://launchpad.support.sap.com/#/notes/2316233/E)中介绍了上述各种配置（不管是“可用”还是“不再提供”）。 标记为“准备订购”的配置会很快进入“SAP 说明”。 不过，这些实例 SKU 在提供 HANA 大型实例服务的六个不同的 Azure 区域已经可以订购。

选择的具体配置取决于工作负荷、CPU 资源和所需的内存。 OLTP 工作负荷可以利用针对 OLAP 工作负荷进行了优化的 SKU。 

所有产品/服务的硬件基地都经过 SAP HANA TDI 认证。 不过，我们会对两种不同类的硬件进行区分，该硬件可将 SKU 分为：

- 称为“I 类”SKU 的 S72、S72m、S144、S144m、S192 和 S192m。
- 称为“II 类”SKU 的 S384、S384m、S384xm、S576、S768 和 S960。

请务必注意，不会将整个 HANA 大型实例模具以独占方式分配给单个客户使用。 此事实也适用于通过 Azure 中部署的网络结构连接的计算和存储资源的机架。 HANA 大型实例基础结构（例如 Azure）部署在以下三个级别中相互隔离的不同客户&quot;租户&quot;：

- 网络：在 HANA 大型实例模具中通过虚拟网络实现隔离。
- 存储：通过分配了存储卷的存储虚拟机实现隔离，并在租户之间隔离存储卷。
- 计算：专用于单个租户的服务器单元分配。 不对服务器单元进行硬分区或软分区。 租户之间不共享单个服务器或主机。 

这样，不同租户之间的 HANA 大型实例单元部署相互不可见。 在 HANA 大型实例模具级别上，不同租户中部署的 HANA 大型实例单元也无法直接相互通信。 在 HANA 大型实例模具级别上，只有一个租户中的 HANA 大型实例单元才能相互通信。
为了收费方便，会为大型实例模具中部署的一个租户分配一个 Azure 订阅。 但在网络级别，则可从其他 Azure 订阅的 Azure VNet 进行访问，只要是属于同一 Azure 合约即可。 如果在部署时使用了同一 Azure 区域的另一 Azure 订阅，则也可选择要求隔离的 HANA 大型实例租户。

在 HANA 大型实例上运行 SAP HANA 与在 Azure 中部署的 Azure VM 上运行 SAP HANA 之间有重大差别：

- Azure 上的 SAP HANA（大型实例）没有虚拟化层。 可以利用底层裸机硬件的性能。
- 与 Azure 不同，Azure 上的 SAP HANA（大型实例）服务器专用于特定客户。 服务器单元或主机不可能进行硬分区或软分区。 因此，HANA 大型实例单元作为一个整体分配给租户，并在这种情况下分配给客户。 重新启动或关闭服务器不会自动导致操作系统和 SAP HANA 被部署在另一台服务器上。 （对于 I 类 SKU，唯一的例外是当服务器可能遇到了问题并且需要在另一台服务器上重新部署时。）
- 在 Azure 中，主机处理器类型是根据最佳性价比选择的，与之不同，为 Azure 上的 SAP HANA（大型实例）选择的处理器类型是 Intel E7v3 和 E7v4 处理器系列中性能最高的类型。


### <a name="running-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>在一个 HANA 大型实例单位上运行多个 SAP HANA 实例
可以在 HANA 大型实例单位上托管多个活动的 SAP HANA 实例。 此类配置需要按实例进行卷设置，这样就仍然能够提供存储快照和灾难恢复的功能。 目前，HANA 大型实例单位可以细分如下：

- S72、S72m、S144、S192：以 256 GB 为增量，且以 256 GB 为最小起始单位。 可以组合使用不同的增量（例如 256 GB、512 GB 等），但不得超出该单位的最大内存。
- S144m 和 S192m：以 256 GB 为增量，以 512 GB 为最小单位。 可以组合使用不同的增量（例如 512 GB、768 GB 等），但不得超出该单位的最大内存。
- II 类：以 512 GB 为增量，最小起始单位为 2 TB。 可以组合使用不同的增量（例如 512 GB、1 TB、1.5 TB 等），但不得超出该单位的最大内存。

运行多个 SAP HANA 实例的部分示例如下：

| SKU | 内存大小 | 存储大小 | 使用多个数据库时的大小 |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1x768 GB HANA 实例<br /> 或 1x512 GB 实例 + 1x256 GB 实例<br /> 或 3x256 GB 实例 | 
| S72m | 1.5 TB | 6 TB | 3x512GB HANA 实例<br />或 1x512 GB 实例 + 1x1 TB 实例<br />或 6x256 GB 实例<br />或 1x1.5 TB 实例 | 
| S192m | 4 TB | 16 TB | 8x512 GB 实例<br />或 4x1 TB 实例<br />或 4x512 GB 实例 + 2x1 TB 实例<br />或 4x768 GB 实例 + 2x512 GB 实例<br />或 1x4 TB 实例 |
| S384xm | 8 TB | 22 TB | 4x2 TB 实例<br />或 2x4 TB 实例<br />或 2x3 TB 实例 + 1x2 TB 实例<br />或 2x2.5 TB 实例 + 1x3 TB 实例<br />或 1x8 TB 实例 |


依此类推。 当然还有其他组合变化。 

### <a name="using-sap-hana-data-tiering-and-extension-nodes"></a>使用 SAP HANA 数据分层和扩展节点
SAP 支持面向不同 SAP NetWeaver 版本和 SAP BW/4HANA 的 SAP BW 的数据分层模型。 有关数据分层模型的详细信息，请参阅本文档及本文档所参考的 SAP 博客：[SAP BW/4HANA AND SAP BW ON HANA WITH SAP HANA EXTENSION NODES](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#)（具有 SAP HANA 扩展节点的 SAP BW/4HANA 和 SAP BW-ON-HANA）。
利用 HANA 大型实例，可以使用 SAP HANA 扩展节点的选项 1 配置（本常见问题解答和 SAP 博客文档中提供详细介绍）。 可使用以下 HANA 大型实例 SKU 配置选项 2 配置：S72m、S192、S192m、S384 和 S384m。  
阅读文档时，可能不会立刻发现此方法的优点。 但如果深入研究 SAP 大小调整准则，便可能发现使用选项 1 和选项 2 SAP HANA 扩展节点的优点。 下面是一个示例：

- SAP HANA 大小调整准则所需的数据量通常是内存所需的两倍。 因此，运行具有热数据的 SAP HANA 实例时，内存中只会填充 50% 或更少数据。 理想状况下，内存中的剩余空间将保留，供 SAP HANA 执行其工作。
- 这意味着，如果在拥有 2 TB 内存的 HANA 大型实例 S192 单元中运行 SAP BW 数据库，只能拥有 1 TB 的数据量。
- 如果仍然运行 S192 HANA 大型实例 SKU，但额外使用选项 1 的其他 SAP HANA 扩展节点，则可获得额外的 2 TB 数据量。 在选项 2 配置中，甚至可获得额外的 4 TB 暖数据量。 与热节点相比，“暖”扩展节点的完整内存容量可供选项 1 用于数据存储，并且选项 2 SAP HANA 扩展节点配置中的数据量可使用双倍内存。
- 因此，选项 1 最终将获得 3 TB 数据容量，其中热、暖数据比率为 1:2，而选项 2 扩展节点配置中将获得 5 TB 数据容量，其中热、暖数据比率为 1:4。

但是，数据量超出内存量的差值越大，请求的暖数据存储在磁盘存储中的可能性就越大。


## <a name="operations-model-and-responsibilities"></a>操作模型和责任

Azure 上的 SAP HANA（大型实例）提供的服务与 Azure IaaS 服务相符。 将得到一个 HANA 大型实例，其中安装了针对 SAP HANA 进行了优化的操作系统。 与 Azure IaaS VM 一样，执行对 OS 进行强化的大多数任务、安装所需的额外软件、安装 HANA、操作 OS 和 HANA，以及更新 OS 和 HANA 都由你负责。 Microsoft 不会强制你更新 OS 或 HANA。

![Azure 上的 SAP HANA（大型实例）的责任](./media/hana-overview-architecture/image2-responsibilities.png)

如上图所示，Azure 上的 SAP HANA（大型实例）是一种多租户基础结构即服务产品。 因此，大多数情况下，责任的划分在 OS 基础结构边界处进行。 Microsoft 负责维护该服务在操作系统层之下的所有方面，负责维护该服务在操作系统层之上的方面（包括操作系统）。 因此，大多数目前可以用来进行符合性管理、安全管理、应用程序管理、基础管理和 OS 管理的本地方法仍可继续使用。 系统看起来好像所有方面都位于网络中。

不过，此服务针对 SAP HANA 进行了优化，因此，在许多方面需要与 Microsoft 协作，以便使用底层基础结构功能实现最佳效果。

下表提供了有关每个层和职责的更多详细信息：

**网络：**运行 SAP HANA 的大型实例模具中所有用于对其存储的访问、实例间的连接（用于扩展和其他功能）、与布局的连接以及与 Azure（其中的 SAP 应用程序层承载在 Azure 虚拟机中）的连接的内部网络。 它还在 Azure 数据中心之间提供了 WAN 连接，以便为数据恢复目的而进行复制。 所有网络都按租户进行分区，并且应用了 QOS。

**存储：**用于 SAP HANA 服务器需要的所有卷的虚拟化已分区存储，以及用于快照的虚拟化已分区存储。 

**服务器：**专用物理服务器，用于运行分配给租户的 SAP HANA 数据库。 I 类 SKU 的服务器已进行硬件抽象。 使用这些类型的服务器时，服务器配置是以配置文件方式收集和维护的，可以从一个物理硬件移至另一个物理硬件。 这种通过操作（手动）移动配置文件的方式在某种程度上可以与 Azure 服务修复相比。 II 类 SKU 的服务器不提供此类功能。

**SDDC：**用来将数据中心作为软件定义的实体进行管理的管理软件。 Microsoft 可以通过它出于规模、可用性和性能原因而创建资源池。

O/S：你选择的在服务器上运行的 OS（SUSE Linux 或 Red Hat Linux）。 向你提供的 OS 映像是各个 Linux 供应商提供给 Microsoft 用于运行 SAP HANA 的映像。 将要求具有 Linux 供应商的订阅，以便获取 SAP HANA 优化的特定映像。 责任包括向 OS 供应商注册映像。 从 Microsoft 移交的观点来看，还对进一步修补 Linux 操作系统负有责任。 此修补还包括成功安装 SAP HANA 所需的附加包（请参阅 SAP HANA 安装文档和 SAP 说明），而这些包尚未由特定 Linux 供应商在其 SAP HANA 优化型 OS 映像中提供。 客户的责任还包括修补 OS，这与 OS 不正常工作/优化相关，其驱动程序涉及特定服务器硬件。 或对 OS 的任何安全性或功能进行修补。 客户的职责还包括对以下项进行监视和容量规划：

- CPU 资源消耗
- 内存消耗
- 与可用空间、IOPS 和延迟相关的磁盘卷
- HANA 大型实例与 SAP 应用程序层之间的网络卷流量

HANA 大型实例的底层基础结构提供了用于备份和还原 OS 卷的功能。 使用此功能也是你的责任。

**中间件：**主要是 SAP HANA 实例。 管理、操作和监视由你负责。 可以通过提供的一项功能使用存储快照进行备份/还原和灾难恢复。 这些功能由基础结构提供。 但是，责任还包括利用这些功能设计高可用性或灾难恢复功能，利用它们，以及监视是否已成功执行存储快照。

**数据：**由 SAP HANA 管理的数据，以及位于卷或文件共享上的其他数据（例如备份文件）。 责任包括监视磁盘可用空间、管理卷上的内容，以及监视磁盘卷备份和存储快照的成功执行。 不过，确保数据成功复制到 DR 站点是 Microsoft 的责任。

**应用程序：**SAP 应用程序实例；对于非 SAP 应用程序，则指那些应用程序的应用程序层。 责任包括部署、管理、操作和监视与以下各项的容量规划相关的那些应用程序：CPU 资源消耗、内存消耗、Azure 存储消耗、Azure VNet 内部的网络带宽消耗以及从 Azure VNet 到 Azure 上的 SAP HANA（大型实例）之间的网络带宽消耗。

WAN：为工作负荷建立的从本地到 Azure 部署的连接。 所有使用 HANA 大型实例的客户都使用 Azure ExpressRoute 进行连接。 此连接不是 Azure 上的 SAP HANA（大型实例）解决方案的一部分，因此需要由你负责设置此连接。

存档：你可能希望使用自己的方法在存储帐户中对数据副本进行存档。 存档涉及到管理、符合性、成本和操作。 负责在 Azure 上生成存档副本和备份并以合规的方式存储它们。

请参阅[Azure 上的 SAP HANA（大型实例）的 SLA](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/)。

## <a name="sizing"></a>调整大小

为 HANA 大型实例调整大小与为一般 HANA 调整大小没有什么不同。 对于现有的已部署系统，需要从其他 RDBMS 迁移到 HANA，SAP 提供了许多在现有 SAP 系统上运行的报告。 如果数据库移到 HANA，这些报告会针对 HANA 实例检查数据并计算内存需求。 请阅读以下 SAP 说明来获取有关如何运行这些报告以及如何获取其最新修补程序/版本的详细信息：

- [SAP 说明 #1793345 - 为 HANA 上的 SAP 套件调整大小](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP 说明 #1872170 - HANA 和 S/4 HANA 上的套件的大小调整报告](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP 说明 #2121330 - 常见问题解答：HANA 上的 SAP BW 的大小调整报告](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP 说明 #1736976 - 适用于 HANA 上的 BW 的大小调整报告](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP 说明 #2296290 - 新的适用于 HANA 上的 BW 的大小调整报告](https://launchpad.support.sap.com/#/notes/2296290)

为实现绿色的字段实施，可以使用 SAP Quick Sizer 来计算在 HANA 的基础上实施 SAP 软件时的内存需求。

HANA 的内存需求将随数据量增长而增加，因此，需要了解当前的内存消耗并能够预测将来的内存消耗。 然后，可以根据内存需求将需求映射到其中一个 HANA 大型实例 SKU。

## <a name="requirements"></a>要求

下表汇总了运行 Azure 上的 SAP HANA（大型实例）的要求。

**Microsoft Azure：**

- 一个可以链接到 Azure 上的 SAP HANA（大型实例）的 Azure 订阅。
- Microsoft 顶级支持协定。 有关与在 Azure 中运行 SAP 相关的特定信息，请参阅 [SAP 支持说明 #2015553 – Microsoft Azure 上的 SAP：支持先决条件](https://launchpad.support.sap.com/#/notes/2015553)。 使用包含至少 384 个 CPU 的 HANA 大型实例单位时，还需要扩大顶级支持协定范围，使之包括 Azure 快速响应 (ARR)。
- 要知道对 SAP 执行大小调整操作后需要的 HANA 大型实例 SKU。

**网络连接：**

- 本地到 Azure 之间的 Azure ExpressRoute：若要将本地数据中心连接到 Azure，请确保从 ISP 订购至少 1 Gbps 的连接。 

**操作系统：**

- SUSE Linux Enterprise Server 12 for SAP Applications 的许可证。

> [!NOTE] 
> Microsoft 提供的操作系统未向 SUSE 注册，也未与 SMT 实例连接。

- 在 Azure 中的 Azure VM 上部署的 SUSE Linux 订阅管理工具 (SMT)。 这使得 Azure 上的 SAP HANA（大型实例）能够由 SUSE 进行注册并相应地进行更新（因为 HANA 大型实例数据中心内没有Internet 访问）。 
- 适用于 SAP HANA 的 Red Hat Enterprise Linux 6.7 或 7.2 的许可证。

> [!NOTE]
> Microsoft 提供的操作系统未向 Red Hat 注册，也未连接到 Red Hat 订阅管理器实例。

- 在 Azure 中的 Azure VM 上部署的 Red Hat 订阅管理器。 使用 Red Hat 订阅管理器，Azure 上的 SAP HANA（大型实例）能够通过 Red Hat 进行注册并相应地进行更新（因为 Azure 大型实例模具上部署的租户内没有直接 Internet 访问）。
- SAP 要求拥有支持与 Linux 提供程序的协定。 此要求不清除 HANA 大型实例或这一事实的解决方案，在 Azure 中的运行的 Linux。 与不同的 Linux Azure 库映像，一些服务费未包含在 HANA 大型实例解决方案产品/服务。 你作为客户有责任履行 SAP 与 Linux 分发服务器之间的支持协定的要求。   
   - 对于 SUSE Linux，请查看 [SAP 说明 #1984787 - SUSE LINUX Enterprise Server 12：安装说明](https://launchpad.support.sap.com/#/notes/1984787)和 [SAP 说明 #1056161 - SUSE 优先支持 SAP 应用程序](https://launchpad.support.sap.com/#/notes/1056161)中的支持合同要求。
   - 对于 Red Hat Linux，你需要具有正确的订阅级别，其中包括支持和服务（HANA 大型实例的操作系统的更新）。 Red Hat 建议获取“RHEL for SAP Business Applications”订阅。 有关支持和服务的详细信息，请查看 [SAP 说明 #2002167 - Red Hat Enterprise Linux 7.x：安装和升级](https://launchpad.support.sap.com/#/notes/2002167)和 [SAP 说明 #1496410 - Red Hat Enterprise Linux 6.x：安装和升级](https://launchpad.support.sap.com/#/notes/1496410)。

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

**SAP：**

- 你应该是一位 SAP 客户，与 SAP 有支持协定
- 强烈建议你咨询 SAP，了解各版本的 SAP HANA，以及大型纵向扩展硬件的最终配置（尤其是在针对 II 类 HANA 大型实例 SKU 进行实施的情况下）。


## <a name="storage"></a>存储

Azure 上的 SAP HANA 服务管理部门可遵循 [SAP HANA 存储要求](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)白皮书中的 SAP 建议指南，配置 Azure 上的 SAP HANA（大型实例）的存储布局。

I 类 HANA 大型实例附带有四倍内存卷作为存储卷。 对于 II 类 HANA 大型实例单位，存储不会是四倍大小。 这些单位附带的卷用于存储 HANA 事务日志备份。 有关详细信息，请参阅[如何在 Azure 上安装和配置 SAP HANA（大型实例）](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

请参阅下表，了解存储分配情况。 此表大致列出了不同卷的容量，这些卷是随不同的 HANA 大型实例单位一起提供的。

| HANA 大型实例 SKU | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| S72 | 1280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3328 GB | 768 GB |1280 GB | 768 GB |
| S192 | 4608 GB | 1024 GB | 1536 GB | 1024 GB |
| S192m | 11,520 GB | 1536 GB | 1792 GB | 1536 GB |
| S384 | 11,520 GB | 1536 GB | 1792 GB | 1536 GB |
| S384m | 12,000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xm | 16,000 GB | 2050 GB | 2050 GB | 2040 GB |
| S576 | 20,000 GB | 3100 GB | 2050 GB | 3100 GB |
| S768 | 28,000 GB | 3100 GB | 2050 GB | 3100 GB |
| S960 | 36,000 GB | 4100 GB | 2050 GB | 4100 GB |


实际部署的卷可能会稍有不同，具体取决于部署以及用来显示卷大小的工具。

如果对 HANA 大型实例 SKU 进行细分，则请参阅以下示例，了解可能的分区块：

| 内存分区 (GB) | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1280 GB | 512 GB | 768 GB | 512 GB |
| 1024 | 1792 GB | 640 GB | 1024 GB | 640 GB |
| 1536 | 3328 GB | 768 GB | 1280 GB | 768 GB |


这些大小为大致的卷数字，可能会因部署和用来查看卷的工具而稍有不同。 此外还可以考虑其他分区大小，例如 2.5 TB。 这些存储大小在计算时使用的公式类似于那些用于上述分区的公式。 “分区”一词并不表示操作系统、内存或 CPU 资源也可以分区。 它只是表示适用于不同 HANA 实例的存储分区，这些实例可能需要部署到单个 HANA 大型实例单位。 

作为客户，你可能需要更多的存储，可以通过购买额外存储（以 1 TB 为单位）的方式来添加存储。 该额外存储可以作为额外卷来添加，也可以用来扩展一个或多个现有卷。 卷的大小（大部分如上表所述）一经部署就不能缩减。 也不能更改卷名或装入名。 如上所述的存储卷是作为 NFS4 卷附加到 HANA 大型实例单位的。

客户可以选择使用备份/还原和灾难恢复目的的存储快照。 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中介绍了有关此主题的详细信息。

### <a name="encryption-of-data-at-rest"></a>静态数据加密
用于 HANA 大型实例存储允许数据透明加密存储在磁盘上。 在部署 HANA 大型实例单位时，可以选择启用这种加密。 还可以选择已在部署后将更改为加密卷。 将从非加密移动到加密卷是透明的并且不需要停机时间。 

使用 I 类 SKU 时，会加密存储启动 LUN 的卷。 对于 II 类 SKU 的 HANA 大型实例，需使用 OS 方法加密启动 LUN。 


## <a name="networking"></a>联网

Azure 网络的体系结构是在 HANA 大型实例上成功部署 SAP 应用程序的关键组件。 通常，Azure 上的 SAP HANA（大型实例）部署具有较大的 SAP 布局和多种不同的 SAP 解决方案，其中具有可变的数据库大小、CPU 资源消耗和内存利用。 这些 SAP 系统中，可能并不是所有系统都基于 SAP HANA，因此，SAP 布局可能是混合式的，它使用以下各项：

- 在本地部署的 SAP 系统。 由于其大小的原因，这些系统当前无法托管在 Azure 中；一个典型的示例是在 Microsoft SQL Server（用作数据库）上运行且需要的 CPU 或内存资源超出 Azure VM 提供能力的 SAP ERP 生产系统。
- 在本地部署的基于 SAP HANA 的 SAP 系统。
- 在 Azure VM 中部署的 SAP 系统。 这些系统可能是任何基于 SAP NetWeaver 的应用程序的开发、测试、沙盒或生产实例，这些应用程序可以根据资源消耗和内存需求成功地在 Azure 中的 VM 上部署。 这些系统也可能基于数据库，例如 SQL Server（请参阅 [SAP 支持说明 #1928533 – Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型](https://launchpad.support.sap.com/#/notes/1928533/E)）或 SAP HANA（请参阅[通过 SAP HANA 认证的 IaaS 平台](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)）。
- 在 Azure 中的 VM 上部署的 SAP 应用程序服务器，它们利用 Azure 大型实例模具中的 Azure 上的 SAP HANA（大型实例）。

虽然混合式 SAP 布局（使用至少四个不同的部署方案）很常见，但很多客户方案是在 Azure 中运行完整的 SAP 布局。 由于 Microsoft Azure VM 的功能越来越强大，将其所有 SAP 解决方案移到 Azure 上的客户数量也在增长。

Azure 中部署的 SAP 系统的上下文中的 Azure 网络并不复杂。 它基于以下原则：

- Azure 虚拟网络 (VNet) 需要连接到与本地网络相连的 Azure ExpressRoute 线路。
- 从本地连接到 Azure 的 ExpressRoute 线路的带宽通常应当为 1 Gbps 或更高。 规定这个最小带宽是为了确保有足够的带宽用来在本地系统与在 Azure VM 上运行的系统之间（以及通过从本地最终用户到 Azure 系统的连接）传输数据。
- 需要在 Azure VNet 中设置 Azure 中的所有 SAP 系统以使其相互通信。
- 本地承载的 Active Directory 和 DNS 通过 ExpressRoute 从本地扩展到 Azure 中。


> [!NOTE] 
> 从收费的角度来看，单个 Azure 订阅只能链接到特定 Azure 区域中的大型实例模具中的单个租户，反过来，单个大型实例模具租户只能链接到单个 Azure 订阅。 此事实与 Azure 中的任何其他计费对象并无不同

将 Azure 上的 SAP HANA（大型实例）部署在多个不同的 Azure 区域中导致在大型实例模具中部署单独的租户。 不过，可在同一 Azure 订阅下运行这两个实例，只要这些实例属于同一 SAP 布局即可。 

> [!IMPORTANT] 
> Azure 上的 SAP HANA（大型实例）仅支持 Azure 资源管理器部署。

 

### <a name="additional-azure-vnet-information"></a>Azure VNet 的其他信息

要将 Azure VNet 连接到 ExpressRoute，必须创建 Azure 网关（请参阅[关于 ExpressRoute 的虚拟网络网关](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)）。 Azure 网关可以与位于 Azure 外部的基础结构的 ExpressRoute（或者是 Azure 大型实例模具的 ExpressRoute）一起使用，也可以连接在 Azure VNet 之间（请参阅[使用 PowerShell 为资源管理器配置 VNet 到 VNet 连接](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)）。 可以将 Azure 网关连接到最多四个不同的 ExpressRoute 连接，只要这些连接来自不同的 MS Enterprise Edges (MSEE) 路由器即可。  有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

> [!NOTE] 
> 对于两种用例，Azure 网关提供的吞吐量不同（请参阅[关于 VPN 网关](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)）。 使用 ExpressRoute 连接时，通过 VNet 网关可以获得的最大吞吐量为 10 Gbps。 请注意，在位于 Azure VNet 中的 Azure VM 与本地系统之间复制文件（使用单一复制流）不会获得各种网关 SKU 的完整吞吐量。 要利用 VNet 网关的完整带宽，必须使用多个流，或者采用单一文件的并行流复制各个文件。


### <a name="networking-architecture-for-hana-large-instances"></a>HANA 大型实例的网络体系结构
HANA 大型实例的网络体系结构如下所示，可以分为四个不同的部分：

- 本地网络和到 Azure 的 ExpressRoute 连接。 此部分是客户域，通过 ExpressRoute 连接到 Azure。 这是下图的右下部分。
- Azure 网络，在上面已与 Azure VNet（也有网关）一起简单讨论过。 这是需要根据应用程序要求以及安全性和符合性要求找到合适设计的区域。 使用 HANA 大型实例是另一个考虑点，需要考虑到 VNet 数以及可供选择的 Azure 网关 SKU。 这是图片的右上部分。
- 通过 ExpressRoute 技术将 HANA 大型实例连接到 Azure 中。 此部件已部署，由 Microsoft 处理。 作为客户，你只需提供一些 IP 地址范围，在将资产部署到 HANA 大型实例中后，再将 ExpressRoute 线路连接到 Azure VNet（请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)）。 
- HANA 大型实例中的网络，大部分在作为客户的你看来是透明的。

![连接到了 Azure 上的 SAP HANA（大型实例）和本地的 Azure VNet](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

使用 HANA 大型实例这一事实并不会改变将本地资产通过 ExpressRoute 连接到 Azure 这一要求， 也不会改变使用一个或多个 VNet 这一要求。VNet 运行的 Azure VM 托管应用程序层，该层连接到托管在 HANA 大型实例单位中的 HANA 实例。 

与在纯 Azure 中进行 SAP 部署的差异所伴随的事实是：

- 客户租户的 HANA 大型实例单位通过另一 ExpressRoute 线路连接到 Azure VNet 中。 为了隔离负载条件，从本地到 Azure VNet 的 ExpressRoute 链接以及 Azure VNet 和 HANA 大型实例之间的链接不共享相同的路由器。
- 在 SAP 应用程序层和 HANA 实例之间的工作负荷配置文件在本质上不同于包含多个小型请求的迸发型数据传输（结果集），后者是从 SAP HANA 传输到应用程序层中。
- 与数据在本地和 Azure 之间交换的典型方案相比，SAP 应用程序体系结构对网络延迟更为敏感。
- VNet 网关具有至少两个 ExpressRoute 连接，并且两个连接共享 VNet 网关传入数据的最大带宽。

在 Azure VM 和 HANA 大型实例单位之间体验到的网络延迟可能高于典型的 VM 到 VM 网络往返延迟。 测量到的值可能超过 0.7 毫秒的往返延迟，具体取决于 Azure 区域。而在 [SAP 说明 #1100926 - 常见问题解答：网络性能](https://launchpad.support.sap.com/#/notes/1100926/E)中，0.7 毫秒被归类为低于平均值。 尽管如此，客户在 SAP HANA 大型实例上部署基于 SAP HANA 的生产型 SAP 应用程序很成功。 客户报告，在部署时可以在使用 HANA 大型实例单位的 SAP HANA 上运行 SAP 应用程序，从而获得了极大的改进。 尽管如此，你还是应在 Azure HANA 大型实例中对自己的业务流程进行彻底的测试。
 
为了确保 Azure VM 和 HANA 大型实例之间的网络延迟稳定，必须选择 Azure VNet 网关 SKU。 不同于本地与 Azure VM 之间的流量模式，Azure VM 与 HANA 大型实例之间的流量模式可能是这样的：一开始流量很小，但随着要传输的请求和数据量的增多，可能会出现流量突然增高的迸发现象。 为了应对这种迸发现象，强烈建议使用 UltraPerformance 网关 SKU。 对于 II 类 HANA 大型实例 SKU，将 UltraPerformance 网关 SKU 用作 Azure VNet 网关是强制性的。  

> [!IMPORTANT] 
> 假定所有的网络流量都位于 SAP 应用程序层与数据库层之间，则仅支持使用 VNet 的 HighPerformance 或 UltraPerformance 网关 SKU 来连接到 Azure 上的 SAP HANA（大型实例）。 对于 HANA 大型实例 II 类 SKU，只支持使用 UltraPerformance 网关 SKU 作为 Azure VNet 网关。



### <a name="single-sap-system"></a>单个 SAP 系统

上面所示的本地基础结构通过 ExpressRoute 连接到 Azure，并且 ExpressRoute 线路连接到 Microsoft Enterprise 边缘路由器 (MSEE)（请参阅 [ExpressRoute 技术概述](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)）。 在建立之后，该路由将连接到 Microsoft Azure 主干，并且所有 Azure 区域都可供访问。

> [!NOTE] 
> 若要在 Azure 中运行 SAP 布局，请连接到距离 SAP 布局中的 Azure 区域最近的 MSEE。 Azure 大型实例模具通过专用 MSEE 设备进行连接，从而最大程度地降低 Azure IaaS 中的 Azure VM 与大型实例模具之间的网络延迟。

承载着 SAP 应用程序实例的 Azure VM 的 VNet 网关连接到该 ExpressRoute 线路，并且同一 VNet 连接到一个专门用于连接大型实例模具的单独 MSEE 路由器。

这是单个 SAP 系统的直观示例，其中 SAP 应用程序层承载在 Azure 中，SAP HANA 数据库在 Azure 上的 SAP HANA（大型实例）上运行。 假设条件是 2 Gbps 或 10 Gbps 吞吐量的 VNet 网关带宽不会成为瓶颈。

### <a name="multiple-sap-systems-or-large-sap-systems"></a>多个 SAP 系统或大型 SAP 系统

如果部署了连接到 Azure 上的 SAP HANA（大型实例）的多个 SAP 系统或大型 SAP 系统，则 VNet 网关 的吞吐量可能会成为瓶颈。 在这种情况下，需将应用程序层拆分成多个 Azure VNet。 我们还建议针对以下案例创建用于连接到 HANA 大型实例的特殊 VNet：

- 为承载 NFS 共享的 Azure 中的 VM 直接从 HANA 大型实例中的 HANA 实例执行备份
- 将从 HANA 大型实例单位大的备份或其他文件复制到在 Azure 中管理的磁盘空间。

使用独立的 VNet 来托管进行存储管理的 VM，这样可以避免在 VNet 网关（为运行 SAP 应用程序层的 VM 提供服务）上将大型文件或数据从 HANA 大型实例传输到 Azure 时受到影响。 

要获得缩放性更好的网络体系结构，请采取以下措施：

- 对于单个较大的 SAP 应用程序层，使用多个 Azure VNet。
- 为所部署的每个 SAP 系统部署一个单独的 Azure VNet，而不是将这些 SAP 系统集中放置在同一 VNet 中的各个子网中。

 适用于 Azure 上的 SAP HANA（大型实例）的缩放性更好的网络体系结构：

![跨多个 Azure VNet 部署 SAP 应用程序层](./media/hana-overview-architecture/image4-networking-architecture.png)

如果跨多个 Azure VNet 部署 SAP 应用程序层或组件（如上所示），可能会在这些 Azure VNet 中的应用程序之间通信时产生不可避免的延迟开销。 默认情况下，位于不同 VNet 中的 Azure VM 之间的网络流量通过此配置中的 MSEE 路由器进行路由。 但自 2016 年 9 月以后，可以优化此路由。 优化并降低两个 VNet 之间的通信延迟的方法是将同一区域内的 Azure VNet 对等互连。 即使这些 VNet 在不同订阅中，也是如此。 通过 Azure VNet 对等互连，两个不同 Azure VNet 中的 VM 可以使用 Azure 网络主干来直接相互通信。 从而显示类似的延迟，就像这些 VM 在同一 VNet 中一样。 而发送到通过 Azure VNet 网关连接的 IP 地址范围的流量则通过单独的 VNet 网关进行路由。 可以在 [VNet 对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)一文中获取有关 Azure VNet 对等互连的详细信息。


### <a name="routing-in-azure"></a>Azure 中的路由

对于 Azure 上的 SAP HANA（大型实例），有两个重要的网络路由注意事项：

1. Azure 上的 SAP HANA（大型实例）只能由 Azure VM 通过专用 ExpressRoute 连接进行访问，不能从本地直接访问。 需要进行直接访问的某些管理客户端和任何应用程序（例如在本地运行的 SAP Solution Manager）都无法连接到 SAP HANA 数据库。

2. Azure 上的 SAP HANA（大型实例）单位有一个分配的 IP 地址，该地址源自你作为客户提交的 Server IP 池地址范围（有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)）。  可通过 Azure 订阅以及用于将 Azure VNet 连接到 Azure 上的 HANA（大型实例）的 ExpressRoute 访问此 IP 地址。 从该服务器 IP 池地址范围中分配的 IP 地址将直接分配给硬件单元，而不会经过 NAT 转换；在此解决方案的第一个部署中也存在这种情况。 

> [!NOTE] 
> 在某个_数据仓库_方案中，应用程序和/或最终用户需要连接到 SAP HANA 数据库（直接运行），在这种情况下，如果需要连接到 Azure 上的 SAP HANA（大型实例），则必须使用另一个网络组件：使用一个反向代理来往返路由数据。 例如，使用 Azure 中部署的带流量管理器的 F5 BIG-IP、NGINX 作为虚拟防火墙/流量路由解决方案。

### <a name="internet-connectivity-of-hana-large-instances"></a>HANA 大型实例的 Internet 连接
HANA 大型实例未建立直接 Internet 连接。 这会限制某些功能，例如直接向操作系统供应商注册 OS 映像的功能。 因此，可能需要使用本地 SLES SMT 服务器或 RHEL 订阅管理器

### <a name="data-encryption-between-azure-vms-and-hana-large-instances"></a>Azure VM 与 HANA 大型实例之间的数据加密
在 HANA 大型实例与 Azure VM 之间传输的数据不会加密。 但是，仅仅是用于 HANA DBMS 端和基于 JDBC/ODBC 应用程序之间的交换，可以启用加密的流量。 请参考 [SAP 提供的此文档](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)

### <a name="using-hana-large-instance-units-in-multiple-regions"></a>在多个区域中使用 HANA 大型实例单位

除了灾难恢复之外，可能还有其他原因需要你在多个 Azure 区域中部署 Azure 上的 SAP HANA（大型实例）。 可能需要从各个区域的不同 VNet 中部署的每个 VM 访问 HANA 大型实例。 由于分配给不同 HANA 大型实例单元的 IP 地址不会跨越（通过其网关直接连接到实例的）Azure VNet 进行传播，因此对上面介绍的 VNet 设计进行了细微的更改：Azure VNet 网关可以处理来自不同 MSEE 的四个不同的 ExpressRoute 线路，连接到其中一个大型实例模具的每个 VNet 都可以连接到另一 Azure 区域中的大型实例模具。

![连接到了不同 Azure 区域中的 Azure 大型实例模具的 Azure VNet](./media/hana-overview-architecture/image8-multiple-regions.png)

上图显示了两个区域中的不同 Azure VNet 如何连接到两个不同的 ExpressRoute 线路，这两个线路用来连接到两个 Azure 区域中的 Azure 上的 SAP HANA（大型实例）。 新引入的连接采用矩形红色线条标出。 有了来自 Azure VNet 的这些连接，在那些 VNet 之一中运行的 VM 可以访问在两个区域中部署的每个不同的 HANA 大型实例单位。 以上各图假定你创建了从本地到两个 Azure 区域的两个 ExpressRoute 连接；出于灾难恢复方面的考虑，建议使用此设置。

> [!IMPORTANT] 
> 如果使用了多个 ExpressRoute 线路，则应当使用“AS 路径前追加”和“本地首选 BGP”设置来确保对流量进行正确路由。


