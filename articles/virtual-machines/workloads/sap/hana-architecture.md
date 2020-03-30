---
title: Azure SAP HANA（大型实例）的体系结构 | Microsoft Docs
description: 有关如何部署 Azure SAP HANA（大型实例）的体系结构。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 041da4198b0bdd040a4916008a1135aa2e2a5f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614526"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Azure 上的 SAP HANA（大型实例）体系结构

从较高层面讲，Azure 上的 SAP HANA（大型实例）解决方案的 SAP 应用层驻留在 Azure VM 中。 数据库层驻留在 SAP TDI 配置的硬件上，该硬件位于连接到 Azure IaaS 的同一 Azure 区域中的大型实例模具中。

> [!NOTE]
> 在 SAP DBMS 层所在的 Azure 区域部署 SAP 应用层。 有关 Azure 上的 SAP 工作负荷的已发布信息对此规则做了详细的阐述。 

Azure 上的 SAP HANA（大型实例）的总体体系结构提供了通过 SAP TDI 认证的硬件配置（用于 SAP HANA 数据库的非虚拟化、裸机高性能服务器）。 它还能够借助 Azure 灵活地缩放 SAP 应用层的资源来满足需求。

![Azure 上的 SAP HANA（大型实例）的体系结构概述](./media/hana-overview-architecture/image1-architecture.png)

所示体系结构分为三个部分：

- **右侧**：在数据中心内运行各种应用程序的本地基础结构，以及访问 LOB 应用程序（例如 SAP）的最终用户。 理想情况下，此本地基础结构使用[ExpressRoute](https://azure.microsoft.com/services/expressroute/)连接到 Azure。

- **中间**：显示 Azure IaaS，而在本例中，显示如何使用 VM 来托管 SAP 或其他将 SAP HANA 用作 DBMS 系统的应用程序。 使用由 VM 提供的内存运行的较小 HANA 实例与其应用层一起部署在 Azure VM 中。 有关虚拟机的详细信息，请参阅[虚拟机](https://azure.microsoft.com/services/virtual-machines/)。

   Azure 网络服务用于将 SAP 系统以及其他应用程序分组到虚拟网络。 这些虚拟网络连接到本地系统以及 Azure 上的 SAP HANA（大型实例）。

   有关支持在 Azure 中运行的 SAP NetWeaver 应用程序和数据库，请参阅 [SAP 说明 #1928533 - Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型](https://launchpad.support.sap.com/#/notes/1928533)。 有关如何在 Azure 上部署 SAP 解决方案的文档，请查看：

  -  [在 Windows 虚拟机上使用 SAP](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [在 Azure 虚拟机上使用 SAP 解决方案](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **左侧：** 显示 Azure 大型实例模具中通过 SAP HANA TDI 认证的硬件。 HANA 大型实例单元通过使用与从本地到 Azure 的连接相同的技术连接到 Azure 订阅的虚拟网络。 自 2019 年 5 月推出优化功能，允许在 HANA 大型实例单元和 Azure VM 之间进行通信，而无需参与 ExpressRoute 网关。 此体系结构（红线）中显示了此称为快速路由快速路径的优化。 

Azure 大型实例模具本身包含以下组件：

- **计算**：基于不同一代英特尔到贡处理器的服务器，它们提供必要的计算能力，并且经过 SAP HANA 认证。
- **网络**：连接计算、存储和 LAN 组件的统一高速网络结构。
- **存储**：通过统一网络结构访问的存储基础结构。 根据所部署的特定的 Azure 上的 SAP HANA（大型实例）配置提供特定的存储容量。 可以通过每月额外付费的方式获得更多存储容量。

在大型实例模具的多租户基础结构中，客户被部署为隔离的租户。 在部署租户时，需在 Azure 合约中命名一个 Azure 订阅。 该订阅将会是对 HANA 大型实例收费所依据的 Azure 订阅。 这些租户与 Azure 订阅之间存在 1:1 的对应关系。 在网络级别，可以从属于不同 Azure 订阅的不同虚拟网络访问部署在一个 Azure 区域的一个租户中的 HANA 大型实例单位。 但是，这些 Azure 订阅必须属于同一 Azure 合约。 

与 VM 一样，Azure 上的 SAP HANA（大型实例）是在多个 Azure 区域中提供的。 若要提供灾难恢复功能，可以选择启用。 一个地缘政治区域中的各种大型实例模具相互连接在一起。 例如，在美国西部和美国东部的 HANA 大型实例模具是通过专用网络链接连接的，目的是进行灾难恢复复制。 

如同可以为 Azure 虚拟机选择不同的 VM 类型一样，可以从针对 SAP HANA 的不同工作负荷类型定制的具有不同 SKU 的 HANA 大型实例中进行选择。 SAP 基于 Intel 处理器世代为各种工作负荷应用内存对处理器插槽比率。 下表显示提供的 SKU 类型。

可以在[对 HLI 可用的 SKU](hana-available-skus.md) 中找到可用的 SKU。

**后续步骤**
- 请参阅 [SAP HANA（大型实例）网络体系结构](hana-network-architecture.md)