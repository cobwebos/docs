---
title: 了解 Azure SAP HANA（大型实例）的术语 | Microsoft Docs
description: 了解 Azure SAP HANA（大型实例）的术语。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6785ae821f701121185f0064c6317c69d50191ab
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617050"
---
# <a name="know-the-terms"></a>了解术语

体系结构和技术部署指南中广泛使用了几个常见定义。 注意以下术语及其含义：

- **IaaS**：基础结构即服务。
- **PaaS**：服务型平台
- **SaaS**：服务型软件
- **SAP 组件**：单个 SAP 应用程序，例如 ERP Central Component (ECC)、Business Warehouse (BW)、Solution Manager 或 Enterprise Portal (EP)。 SAP 组件可以基于传统的 ABAP 或 Java 技术，也可以是不基于 NetWeaver 的应用程序，例如业务对象。
- **SAP 环境**：以逻辑方式组合在一起，用于执行开发、质量保证、培训、灾难恢复或生产等业务功能的一个或多个 SAP 组件。
- **SAP 布局**：表示 IT 布局中所有 SAP 资产的整个格局。 SAP 布局包括所有生产和非生产环境。
- **SAP 系统**：诸如 SAP ERP 开发系统、SAP BW 测试系统、SAP CRM 生产系统等的 DBMS 层与应用层的组合。 Azure 部署不支持在本地与 Azure 之间分割这两个层。 某个 SAP 系统要么部署在本地，要么部署在 Azure 中。 可将 SAP 布局中的不同系统部署到 Azure 或本地。 例如，可以在 Azure 中部署 SAP CRM 开发和测试系统，同时在本地部署 SAP CRM 生产系统。 对于 Azure 上的 SAP HANA（大型实例），应该在 VM 中托管 SAP 系统的 SAP 应用层，在 Azure 上的 SAP HANA（大型实例）模具中的某个单元上托管相关的 SAP HANA 实例。
- **大型实例模具**：已通过 SAP HANA TDI 认证的硬件基础结构堆栈，专门用于在 Azure 中运行 SAP HANA 实例。
- **Azure 上的 SAP HANA（大型实例）** ：用于在通过 SAP HANA TDI 认证的、部署在不同 Azure 区域中的大型实例模具中的硬件上运行 HANA 实例的产品/服务的官方名称。 本技术部署指南中广泛使用的相关术语“HANA 大型实例”是 *Azure 上的 SAP HANA（大型实例）* 的简称。
- **跨界**：描述这样一种场景：将 VM 部署到在本地数据中心与 Azure 之间建立了站点到站点、多站点或 ExpressRoute 连接的 Azure 订阅。 在一般的 Azure 文档中，此类部署也称为跨界方案。 连接的原因是为了将本地域、本地 Azure Active Directory/OpenLDAP 和本地 DNS 扩展到 Azure。 本地布局会扩展到 Azure 订阅的 Azure 资产。 经过这种扩展后，VM 可以成为本地域的一部分。 

   本地域的域用户可以访问服务器，并可在这些 VM 上运行服务（例如 DBMS 服务）。 但无法在本地的 VM 和 Azure 部署的 VM 之间进行通信和名称解析。 这是大多数 SAP 资产的典型部署场景。 有关详细信息，请参阅[AZURE VPN 网关](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)和[使用 Azure 门户创建具有站点到站点连接的虚拟网络](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
- **租户**：在 HANA 大型实例模具中部署的客户会隔离到租户中。 租户在网络、存储和计算层中相互隔离。 分配给不同租户的存储和计算单元在 HANA 大型实例模具级别上无法相互看到或进行通信。 客户可以选择部署到不同的租户中。 即使这样，HANA 大型实例模具级别上的租户之间也不进行通信。
- **SKU 类别**：对于 HANA 大型实例，提供以下两类 SKU。
    - **类型 I 类**： S72、S72m、S96、S144、S144m、S192、S192m、S192xm、S224 和 S224m
    - **II 类**：S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm 和 S960m
- **Stamp**：定义 HANA 大型实例的 Microsoft 内部部署大小。 在部署 HANA 大型实例单元之前，需要在数据中心位置部署由计算、网络和存储机架组成的 HANA 大型实例戳。 此类部署称为 HANA 大型实例标记或从修订版本4（请参阅下文），我们使用的是**大型实例行**的替代术语
- **修订**： HANA 大型实例标记有两个不同的标记修订版本。 这两种体系结构和 Azure 虚拟机主机接近于 Azure
    - "修订版本 3" （Rev 3）：是从2016年开始部署的原始设计
    - "修订版 4" （Rev 4）：是一种新的设计，可与 azure 虚拟机主机更接近于 azure 虚拟机主机，以及在 Azure Vm 与 HANA 大型实例单元之间实现更低的网络延迟 

其他各种资源介绍了如何在云中部署 SAP 工作负荷。 需要由拥有相关经验的人员来规划 Azure 中的 SAP HANA 的部署，他们应该了解 Azure IaaS 的原理，知道如何在 Azure IaaS 上部署 SAP 工作负荷。 在继续之前，请参阅[在 Azure 虚拟机上使用 SAP 解决方案](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)来了解详细信息。 

**后续步骤**
- 请参阅 [HLI 认证](hana-certification.md)