---
title: Azure SAP HANA（大型实例）概述 | Microsoft Docs
description: 有关如何部署 Azure SAP HANA（大型实例）的概述。
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
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c177dbad1145dee6eda3202d8076997cc7673dfc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60477794"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>什么是 Azure 上的 SAP HANA（大型实例）？

Azure 上的 SAP HANA（大型实例）是一种针对 Azure 的独特解决方案。 除了提供虚拟机用于部署和运行 SAP HANA 以外，Azure 还可让你在专用的逻辑服务器上运行和部署 SAP HANA。 Azure 上的 SAP HANA（大型实例）解决方案在分配的非共享主机/服务器裸机硬件上进行构建。 服务器硬件嵌入在包含计算/服务器、网络和存储基础结构的较大模具中。 这作为组合经过了 HANA 定制数据中心集成 (TDI) 认证。 Azure 上的 SAP HANA（大型实例）提供各种不同的服务器 SKU 或规模。 单元可包含 36 个 Intel CPU 内核和 768 GB 的内存，最多可包含 480 个 Intel CPU 内核和 24 TB 的内存。

基础结构模具中的客户隔离在租户中执行，详细情况如下所示：

- **网络**：通过每个客户的虚拟网络基础结构堆栈中的客户隔离分配租户。 一个租户分配给单个客户。 一个客户可以具有多个租户。 租户的网络隔离禁止基础结构模具级别中的租户之间进行网络通信，即使租户属于同一个客户。
- **存储组件**:通过向其分配了存储卷的存储虚拟机实现隔离。 存储卷只能分配给一个存储虚拟机。 存储虚拟机以独占方式分配给 SAP HANA TDI 认证的基础结构堆栈中的单个租户。 因此，只能在一个特定相关租户中访问分配给存储虚拟机的存储卷。 这些存储卷在部署的不同租户之间不可见。
- **服务器或主机**:服务器或主机单元不会客户或租户之间共享。 部署到客户的服务器或主机是分配给单个租户的原子裸机计算单元。 不使用硬件分区或软分区，这可能会导致某个客户与其他客户共享主机或服务器。  分配给特定租户的存储虚拟机的存储卷会装载到这类服务器。 可以按独占方式向一个租户分配具有不同 SKU 的一个到多个服务器单元。
- 在 Azure 上的 SAP HANA（大型实例）基础结构模具中，会部署许多不同的租户，并通过网络、存储和计算级别上的租户概念使它们相互隔离。 


仅支持这些裸机服务器单元运行 SAP HANA。 SAP 应用层或工作负荷中间软件层在虚拟机中运行。 运行 Azure 上的 SAP HANA（大型实例）单元的基础结构模具连接到 Azure 网络服务骨干网。 因此可提供 Azure 上的 SAP HANA（大型实例）单元与 Azure 虚拟机之间的那种低延迟连接。

本文档是介绍 Azure 上的 SAP HANA （大型实例）的几个文档之一。 本文档介绍了该解决方案提供的基本体系结构、责任和服务。 此外介绍了解决方案的高级功能。 对于大多数领域（如网络和连接），其他四个文档涵盖了详细信息并进行了深入介绍。 Azure 上的 SAP HANA（大型实例）文档不涵盖 SAP NetWeaver 安装或 VM 中的 SAP NetWeaver 部署这些方面。 Azure 上的 SAP NetWeaver 在同一 Azure 文档容器的不同文档中介绍。 


HANA 大型实例指南的不同文档涵盖以下几个方面：

- [Azure 上的 SAP HANA（大型实例）概述和体系结构](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [安装和配置 Azure 上的 SAP HANA（大型实例）](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure 上的 SAP HANA（大型实例）的故障排除和监视](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [使用 STONITH 在 SUSE 中进行高可用性设置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [为类型 II SKU 执行 OS 备份和还原](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

**后续步骤**
- 请参阅[了解术语](hana-know-terms.md)