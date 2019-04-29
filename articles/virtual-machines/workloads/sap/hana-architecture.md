---
title: Azure SAP HANA（大型实例）的体系结构 | Microsoft Docs
description: 有关如何部署 Azure SAP HANA（大型实例）的体系结构。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 80ef63cdd9de8cb2340fe15d761402bb9f00fae9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60795964"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Azure 上的 SAP HANA（大型实例）体系结构

从较高层面讲，Azure 上的 SAP HANA（大型实例）解决方案的 SAP 应用层驻留在 Azure VM 中。 数据库层驻留在 SAP TDI 配置的硬件上，该硬件位于连接到 Azure IaaS 的同一 Azure 区域中的大型实例模具中。

> [!NOTE]
> 在 SAP DBMS 层所在的 Azure 区域部署 SAP 应用层。 有关 Azure 上的 SAP 工作负荷的已发布信息对此规则做了详细的阐述。 

Azure 上的 SAP HANA（大型实例）的总体体系结构提供了通过 SAP TDI 认证的硬件配置（用于 SAP HANA 数据库的非虚拟化、裸机高性能服务器）。 它还能够借助 Azure 灵活地缩放 SAP 应用层的资源来满足需求。

![Azure 上的 SAP HANA（大型实例）的体系结构概述](./media/hana-overview-architecture/image1-architecture.png)

所示体系结构分为三个部分：

- **右**:显示数据中运行不同的应用程序的本地基础结构中心，以便最终用户可以访问 LOB SAP 等应用程序。 理想情况下，此本地基础结构使用 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 连接到 Azure。

- **Center**:显示 Azure IaaS 和中，在这种情况下，使用的 Vm 来托管 SAP 或其他将 SAP HANA 用作 DBMS 系统的应用程序。 使用由 VM 提供的内存运行的较小 HANA 实例与其应用层一起部署在 Azure VM 中。 有关虚拟机的详细信息，请参阅[虚拟机](https://azure.microsoft.com/services/virtual-machines/)。

   Azure 网络服务用于将 SAP 系统以及其他应用程序分组到虚拟网络。 这些虚拟网络连接到本地系统以及 Azure 上的 SAP HANA（大型实例）。

   SAP NetWeaver 的应用程序和数据库，支持在 Azure 中运行，请参阅[SAP 支持说明 #1928533 – Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型](https://launchpad.support.sap.com/#/notes/1928533)。 有关如何在 Azure 上部署 SAP 解决方案的文档，请查看：

  -  [在 Windows 虚拟机上使用 SAP](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [在 Azure 虚拟机上使用 SAP 解决方案](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **左**:显示 Azure 大型实例模具中的 SAP HANA TDI 认证的硬件。 HANA 大型实例单元使用与从本地连接到 Azure 时相同的技术连接到订阅的虚拟网络。

Azure 大型实例模具本身包含以下组件：

- **计算**:基于 Intel Xeon E7 8890v3 或 Intel Xeon E7 8890v4 处理器提供必要的计算能力，是通过 SAP HANA 认证的服务器。
- **网络**：一种统一的高速网络结构，可实现计算、 存储和 LAN 组件互连。
- **存储**：通过统一的网络结构访问存储基础结构。 根据所部署的特定的 Azure 上的 SAP HANA（大型实例）配置提供特定的存储容量。 可以通过每月额外付费的方式获得更多存储容量。

在大型实例模具的多租户基础结构中，客户被部署为隔离的租户。 在部署租户时，需在 Azure 合约中命名一个 Azure 订阅。 该订阅将会是对 HANA 大型实例收费所依据的 Azure 订阅。 这些租户与 Azure 订阅之间存在 1:1 的对应关系。 在网络级别，可以从属于不同 Azure 订阅的不同虚拟网络访问部署在一个 Azure 区域的一个租户中的 HANA 大型实例单位。 但是，这些 Azure 订阅必须属于同一 Azure 合约。 

与 VM 一样，Azure 上的 SAP HANA（大型实例）是在多个 Azure 区域中提供的。 若要提供灾难恢复功能，可以选择启用。 一个地缘政治区域中的各种大型实例模具相互连接在一起。 例如，在美国西部和美国东部的 HANA 大型实例模具是通过专用网络链接连接的，目的是进行灾难恢复复制。 

如同可以为 Azure 虚拟机选择不同的 VM 类型一样，可以从针对 SAP HANA 的不同工作负荷类型定制的具有不同 SKU 的 HANA 大型实例中进行选择。 SAP 基于 Intel 处理器世代为各种工作负荷应用内存对处理器插槽比率。 下表显示提供的 SKU 类型。

可以在[对 HLI 可用的 SKU](hana-available-skus.md) 中找到可用的 SKU。

**后续步骤**
- 请参阅 [SAP HANA（大型实例）网络体系结构](hana-network-architecture.md)