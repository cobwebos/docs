---
title: Azure SAP HANA（大型实例）的操作模型 | Microsoft Docs
description: Azure SAP HANA（大型实例）的操作模型。
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
ms.openlocfilehash: 36a648e2d46cce96a8ff663f45ccf45326898a84
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60477864"
---
# <a name="operations-model-and-responsibilities"></a>操作模型和责任

Azure 上的 SAP HANA（大型实例）提供的服务与 Azure IaaS 服务相符。 将得到一个 HANA 大型实例，其中安装了针对 SAP HANA 进行了优化的操作系统。 与 Azure IaaS VM 一样，执行对 OS 进行强化的大多数任务、安装所需的额外软件、安装 HANA、操作 OS 和 HANA，以及更新 OS 和 HANA 都由你负责。 Microsoft 不会强制你更新 OS 或 HANA。

![Azure 上的 SAP HANA（大型实例）的责任](./media/hana-overview-architecture/image2-responsibilities.png)

如上图所示，Azure 上的 SAP HANA（大型实例）是一种多租户 IaaS 产品/服务。 大多数情况下，责任的划分在 OS 基础结构边界处进行。 Microsoft 负责维护该服务在操作系统层之下的所有方面。 你负责维护该服务在操作系统层之上的所有方面。 OS 由你负责。 大多数目前可以用来进行符合性管理、安全管理、应用程序管理、基础管理和 OS 管理的本地方法仍可继续使用。 系统看起来好像所有方面都位于网络中。

此服务针对 SAP HANA 进行了优化，因此，在许多方面需要与 Microsoft 协作，以便使用底层基础结构功能实现最佳效果。

下表提供了有关每个层和职责的更多详细信息：

**网络**：运行 SAP HANA 的大型实例标记的所有内部网络。 你的责任包括对存储的访问、实例间的连接（用于扩展和其他功能）、与布局的连接以及与 Azure（其中的 SAP 应用层承载在 Azure 虚拟机中）的连接。 此外，还包括在 Azure 数据中心之间提供 WAN 连接，以便为灾难恢复目的而进行复制。 所有网络都按租户进行分区，并且应用了服务质量。

**存储**：用于 SAP HANA 服务器所需的全部卷的虚拟化已分区存储，以及用于快照的虚拟化已分区存储。 

**服务器**：专用物理服务器，用于运行分配给租户的 SAP HANA 数据库。 I 类 SKU 的服务器已进行硬件抽象。 使用这些类型的服务器时，服务器配置是以配置文件方式收集和维护的，可以从一个物理硬件移至另一个物理硬件。 这种通过操作（手动）移动配置文件的方式在某种程度上可以与 Azure 服务修复相比。 II 类 SKU 的服务器不提供此类功能。

**SDDC**：用来将数据中心作为软件定义的实体进行管理的管理软件。 Microsoft 可以通过它出于规模、可用性和性能原因而创建资源池。

**O/S**：选择的在服务器上运行的 OS（SUSE Linux 或 Red Hat Linux）。 向提供的 OS 映像是各个 Linux 供应商提供给 Microsoft 用于运行 SAP HANA 的映像。 必须具有 Linux 供应商的订阅，以便获取 SAP HANA 优化的特定映像。 你负责向 OS 供应商注册映像。 

从 Microsoft 移交的观点来看，还对进一步修补 Linux 操作系统负有责任。 此修补还包括成功安装 SAP HANA 所需的附加包，而这些包尚未由特定 Linux 供应商在其 SAP HANA 优化型 OS 映像中提供。 （有关详细信息，请参阅 SAP 的 HANA 安装文档和 SAP 说明。） 

你还负责修补 OS，这与 OS 不正常工作/优化相关，其驱动程序涉及特定服务器硬件。 你还负责对 OS 的任何安全性或功能进行修补。 

你的责任还包括对以下项进行监视和容量规划：

- CPU 资源消耗。
- 内存消耗。
- 与可用空间、IOPS 和延迟相关的磁盘卷。
- HANA 大型实例与 SAP 应用层之间的网络卷流量。

HANA 大型实例的底层基础结构提供了用于备份和还原 OS 卷的功能。 使用此功能也是你的责任。

**中间件**：主要是 SAP HANA 实例。 管理、操作和监视由你负责。 可以通过提供的功能使用存储快照来实现备份、还原和灾难恢复目的。 这些功能由基础结构提供。 但是，责任还包括利用这些功能设计高可用性或灾难恢复功能，利用它们，以及监视是否已成功执行存储快照。

**Data**：由 SAP HANA 管理的数据，以及位于卷或文件共享上的其他数据（例如备份文件）。 你的责任包括监视磁盘可用空间、管理卷上的内容。 你还负责监视磁盘卷备份和存储快照的成功执行。 确保数据成功复制到灾难恢复站点是 Microsoft 的责任。

**应用程序：** SAP 应用程序实例；对于非 SAP 应用程序，则指那些应用程序的应用层。 你的责任包括这些应用程序的部署、管理、操作和监视。 你还负责与以下各项的容量规划相关的那些应用程序：CPU 资源消耗、内存消耗、Azure 存储消耗、虚拟网络内部的网络带宽消耗。 你还负责对虚拟网络到 Azure 上的 SAP HANA（大型实例）之间的网络带宽消耗进行容量规划。

**WAN**：为工作负荷建立的从本地到 Azure 部署的连接。 所有使用 HANA 大型实例的客户都使用 Azure ExpressRoute 进行连接。 此连接不是 Azure 上的 SAP HANA（大型实例）解决方案的一部分。 你负责设置此连接。

**存档**：你可能希望使用自己的方法在存储帐户中对数据副本进行存档。 存档涉及到管理、符合性、成本和操作。 你负责在 Azure 上生成存档副本和备份并以合规的方式存储它们。

请参阅[Azure 上的 SAP HANA（大型实例）的 SLA](https://azure.microsoft.com/support/legal/sla/sap-hana-large/)。

**后续步骤**
- 请参阅 [Azure 上的 SAP HANA（大型实例）体系结构](hana-architecture.md)
