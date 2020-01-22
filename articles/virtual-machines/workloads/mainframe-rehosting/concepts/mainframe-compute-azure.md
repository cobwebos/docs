---
title: 将大型机计算移到 Azure 虚拟机
description: Azure 计算资源在很大程度上与大型机容量比较，因此你可以迁移和现代化 IBM z14 应用程序。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 97f354d0a313d58c671366dd0e5f485504823e13
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76288925"
---
# <a name="move-mainframe-compute-to-azure"></a>将大型机计算移到 Azure

大型机具有高可靠性和可用性的信誉，并继续成为许多企业的受信任主干。 它们常常被视为具有几乎无限制的可伸缩性和计算能力。 但是，某些企业 outgrown 了最大可用大型机的能力。 如果这听起来像你，Azure 可提供灵活性、覆盖和基础结构节约。

若要在 Microsoft Azure 上运行大型机工作负荷，需要知道大型机的计算功能与 Azure 的比较情况。 本文介绍如何在 Azure 上获得可比较的结果，具体取决于 IBM z14 大型机（本文撰写之时的最新型号）。

若要开始使用，请考虑并行环境。 下图比较了用于运行应用程序的大型机环境和 Azure 宿主环境。

![Azure 服务和模拟环境提供可比较的支持和简化迁移](media/mainframe-compute-azure.png)

大型机的强大功能通常用于联机事务处理（OLTP）系统，用于处理数百万个用户的数百万次更新。 这些应用程序通常使用软件进行事务处理、屏幕处理和窗体输入。 他们可以使用客户信息控制系统（CICS）、信息管理系统（IMS）或事务接口包（TIP）。

如图所示，Azure 上的 TPM 模拟器可以处理 CICS 和 IMS 工作负荷。 Azure 上的批处理系统模拟器执行作业控制语言（JCL）的角色。 大型机数据迁移到 azure 数据库（如 Azure SQL 数据库）。 Azure 服务或 Azure 虚拟机中托管的其他软件可用于系统管理。

## <a name="mainframe-compute-at-a-glance"></a>大型机计算一览

在 z14 大型机中，处理器最多排列为四个*抽屉*。 *抽屉*只是处理器和芯片组的群集。 每个抽屉可以有六个活动中心处理器（CP）芯片，每个 CP 有10个系统控制器（SC）芯片。 在 Intel x86 术语中，每个银箱有六个插槽，每个插槽10个核心，四个抽屉。 此体系结构为 z14 提供了相当于24个套接字和240个内核的最大值。

Fast z14 CP 具有 5.2 GHz 的时钟速度。 通常情况下，z14 提供了框中的所有 CPs。 它们会根据需要激活。 不管实际使用情况如何，客户通常每月至少收取四个小时的计算时间。

可以将大型机处理器配置为以下类型之一：

- 常规用途（GP）处理器
- System z 集成信息处理器（zIIP）
- 适用于 Linux 的集成设施（IFL）处理器
- 系统辅助处理器（SAP）
- 集成耦合设施（ICF）处理器

## <a name="scaling-mainframe-compute-up-and-out"></a>扩大和缩小大型机

IBM 大型机提供最多可扩展到240个内核（单个系统的当前 z14 大小）。 另外，IBM 大型机可以通过称为 "耦合设施" （CF）的功能进行扩展。 CF 允许多个大型机系统同时访问相同的数据。 使用 CF，大型机并行 Sysplex 技术将大型机处理器组合在一起。 编写本指南时，并行 Sysplex 功能支持每个64处理器32组。 最多可通过这种方式对2048处理器进行分组，以扩大计算能力。

CF 允许计算群集与直接访问共享数据。 它用于锁定信息、缓存信息和共享数据资源的列表。 可以将使用一个或多个 CFs 的并行 Sysplex 视为 "共享所有内容" 扩展计算群集。 有关这些功能的详细信息，请参阅 IBM 网站上的[并行 Sysplex ON Ibm Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) 。

应用程序可以使用这些功能来提供横向扩展性能和高可用性。 有关 CICS 如何将并行 Sysplex 与 CF 结合使用的信息，请下载[IBM CICS 和耦合设施：除基本](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)redbook。

## <a name="azure-compute-at-a-glance"></a>Azure 计算概览

有些人误以为基于 Intel 的服务器不像大型机那样强大。 但是，新的基于内核密集型系统的系统计算能力与大型机一样多。 本部分介绍用于计算和存储的 Azure 基础结构即服务（IaaS）选项。 Azure 还提供了平台即服务（PaaS）选项，但本文重点介绍提供类似大型机容量的 IaaS 选项。

Azure 虚拟机提供大小和类型范围内的计算能力。 在 Azure 中，虚拟 CPU （vCPU）大致等同于大型机上的核心。

目前，Azure 虚拟机大小的范围为从1到128的个 vcpu。 虚拟机（VM）类型针对特定工作负荷进行了优化。 例如，下面的列表显示了 VM 类型（当前编写的内容）及其建议的用法：

| 大小     | 类型和描述                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D 系列 | 一般用途为 64 vCPU，最高可达 3.5 GHz 时钟速度                           |
| E 系列 | 最大为64个 vcpu 的内存优化                                                 |
| F 系列 | 计算优化，最多64个 vcpu，3到 7 GHz 时钟速度                       |
| H 系列 | 针对高性能计算（HPC）应用程序进行了优化                          |
| L 系列 | 针对数据库（如 NoSQL）支持的高吞吐量应用程序进行优化的存储 |
| M 系列 | 最大计算和内存优化 Vm，最多支持128个 vcpu                        |

有关可用 Vm 的详细信息，请参阅[虚拟机系列](https://azure.microsoft.com/pricing/details/virtual-machines/series/)。

Z14 大型机最多可以有240个核心。 但是，z14 大型机几乎不会将所有内核用于单个应用程序或工作负荷。 而是将大型机分隔开来为逻辑分区（LPARs），LPARs 具有分级（MIPS （每秒数百万条指令）或 MSU （百万个服务单位）。 确定在 Azure 上运行大型机工作负载所需的可比较 VM 大小时，请考虑 MIPS （或 MSU）分级。

下面是一般估算：

-   150 MIPS/vCPU

-   每个处理器 1000 MIPS

若要确定 LPAR 中给定工作负荷的正确 VM 大小，请先优化工作负荷的 VM。 然后确定所需的个 vcpu 数目。 保守估计值为每 vCPU 150 MIPS。 例如，根据此估算，具有16个个 vcpu 的 F 系列 VM 可以轻松地支持来自 2400 MIPS 的 LPAR 的 IBM Db2 工作负载。

## <a name="azure-compute-scale-up"></a>Azure 计算向上扩展

M 系列 Vm 最多可增加到128个 vcpu （本文撰写之时）。 M 系列 VM 对每个 vCPU 使用 150 MIPS，M 系列 VM 等于大约 19000 MIPS。 估算大型机的 MIPS 的一般规则是每个处理器 1000 MIPS。 Z14 大型机最多可以有24个处理器，并为单个大型机系统提供大约 24000 MIPS。

最大的单一 z14 大型机比 Azure 中提供的最大 VM 多约 5000 MIPS。 但比较工作负载的部署方式很重要。 如果大型机系统同时具有应用程序和关系数据库，则这些系统通常部署在同一台物理主机上-每个系统都在其自己的 LPAR 中。 Azure 上的同一解决方案通常是使用应用程序的一个 VM 来部署的，并为数据库提供一个适当大小的独立 VM。

例如，如果 M64-16ms vCPU 系统支持应用程序，并且对数据库使用了 M96 vCPU，则需要大约150个 vcpu，或大约 24000 MIPS，如下图所示。

![比较 24000 MIPS 的工作负载部署](media/mainframe-compute-mips.png)

方法是将 LPARs 迁移到各个 Vm。 然后，Azure 可轻松扩展到在单个大型机系统上部署的大多数应用程序所需的大小。

## <a name="azure-compute-scale-out"></a>Azure 计算横向扩展

基于 Azure 的解决方案的优点之一是可以横向扩展。缩放使得应用程序可使用几乎无限制的计算能力。 Azure 支持多种方法来横向扩展计算能力：

- **跨群集负载平衡。** 在这种情况下，应用程序可以使用[负载均衡器](/azure/load-balancer/load-balancer-overview)或资源管理器来分散群集中多个 vm 之间的工作负荷。 如果需要更多计算容量，则会将其他 Vm 添加到群集。

- **虚拟机规模集。** 在此突发情况下，应用程序可以根据 VM 使用情况扩展到其他[计算资源](/azure/virtual-machine-scale-sets/overview)。 当需求下降时，规模集中的 Vm 数目也会下降，从而有效地使用计算能力。

- **PaaS 缩放。** Azure PaaS 产品/服务缩放计算资源。 例如， [Azure Service Fabric](/azure/service-fabric/service-fabric-overview)会分配计算资源来满足请求数量的增加。

- **Kubernetes 群集。** Azure 上的应用程序可以将[Kubernetes 群集](/azure/aks/concepts-clusters-workloads)用于计算服务的指定资源。 Azure Kubernetes Service （AKS）是一种托管服务，用于协调 Azure 上的 Kubernetes 节点、池和群集。

若要为横向扩展计算资源选择适当的方法，请务必了解 Azure 和大型机的不同之处。 关键是计算资源是否共享数据。 在 Azure 中，数据（默认情况下）通常不由多个 Vm 共享。 如果横向扩展计算群集中的多个 Vm 需要数据共享，则共享数据必须驻留在支持此功能的资源中。 在 Azure 上，数据共享涉及存储，如以下部分所述。

## <a name="azure-compute-optimization"></a>Azure 计算优化

可以在 Azure 体系结构中优化每层处理。 为每个环境使用最适合的 Vm 和功能类型。 下图显示了在 Azure 中部署 Vm 以支持使用 Db2 的 CICS 应用程序的一种可能的模式。 在主站点中，以高可用性部署生产、预生产和测试 VM。 辅助站点用于备份和灾难恢复。

每个层还可以提供适当的灾难恢复服务。 例如，生产和数据库 VM 可能需要热或温恢复，而开发和测试 VM 支持冷恢复。

![支持灾难恢复的高可用性部署](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>后续步骤

- [大型机迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Azure 虚拟机上的大型机重新承载](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [将大型机存储移到 Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM 资源

- [IBM Z 的并行 Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 和耦合设施：除基础知识之外](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Creating required users for a Db2 pureScale Feature installation](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)（创建所需用户以安装 Db2 pureScale 功能）
- [Db2icrt - Create instance command](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)（Db2icrt - 创建实例命令）
- [Db2 pureScale 群集数据库解决方案](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [适用于大型机应用程序的 Microsoft Azure 政府云](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft 和 FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>更多迁移资源

- [Azure Virtual Data Center Lift and Shift Guide](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)（Azure 虚拟数据中心提升和迁移指南）
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
