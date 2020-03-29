---
title: 将大型机计算移动到 Azure 虚拟机
description: Azure 计算资源与大型机容量相比可得，因此您可以迁移 IBM z14 应用程序并实现现代化。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 97f354d0a313d58c671366dd0e5f485504823e13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288925"
---
# <a name="move-mainframe-compute-to-azure"></a>将大型机计算移动到 Azure

大型机以高可靠性和可用性著称，并继续是许多企业值得信赖的骨干。 它们通常被认为具有几乎无限的可扩展性和计算能力。 然而，一些企业已经发展出了最大的可用大型机的能力。 如果这听起来类似您，Azure 可提供敏捷性、覆盖范围和基础结构节省。

要在 Microsoft Azure 上运行大型机工作负荷，您需要了解大型机的计算功能与 Azure 的比较情况。 本文基于 IBM z14 大型机（本文编写时的最最新型号），告诉您如何在 Azure 上获得可比较的结果。

要开始，并排考虑环境。 下图将运行应用程序的大型机环境与 Azure 托管环境进行比较。

![Azure 服务和仿真环境提供类似的支持并简化迁移](media/mainframe-compute-azure.png)

大型机的强大功能通常用于在线事务处理 （OLTP） 系统，该系统可为数千用户处理数百万个更新。 这些应用程序通常使用软件进行事务处理、屏幕处理和表单输入。 他们可以使用客户信息控制系统 （CICS）、信息管理系统 （IMS） 或事务接口包 （TIP）。

如图所示，Azure 上的 TPM 仿真程序可以处理 CICS 和 IMS 工作负载。 Azure 上的批处理系统仿真程序执行作业控制语言 （JCL） 的角色。 大型机数据将迁移到 Azure 数据库，如 Azure SQL 数据库。 Azure 服务或 Azure 虚拟机中托管的其他软件可用于系统管理。

## <a name="mainframe-compute-at-a-glance"></a>大型机计算一目了然

在 z14 大型机中，处理器最多排列在四*个抽屉中*。 *抽屉*只是一组处理器和芯片组。 每个抽屉可以有六个活跃的中央处理器 （CP） 芯片，每个 CP 有 10 个系统控制器 （SC） 芯片。 在英特尔 x86 术语中，每个抽屉有 6 个插槽、每个插槽 10 个内核和 4 个抽屉。 此体系结构为 z14 提供 24 个套接字和 240 个内核的粗糙等效。

快速 z14 CP 具有 5.2 GHz 时钟速度。 通常，z14 随框中的所有 CP 一起传递。 根据需要激活它们。 尽管实际使用情况，客户通常每月至少收取四个小时的计算费用。

大型机处理器可以配置为以下类型之一：

- 通用 （GP） 处理器
- 系统 z 集成信息处理器 （zIIP）
- Linux （IFL） 处理器集成设施
- 系统辅助处理器 （SAP）
- 集成耦合设施 （ICF） 处理器

## <a name="scaling-mainframe-compute-up-and-out"></a>扩展大型机计算向上和出

IBM 大型机能够扩展至 240 个内核（单个系统的当前 z14 大小）。 此外，IBM 大型机可以通过称为耦合设施 （CF） 的功能横向扩展。 CF 允许多个大型机系统同时访问相同的数据。 使用 CF，大型机并行系统技术将大型机处理器分组群集中。 编写本指南时，Parallel Sysplex 功能支持 32 个分组，每个分组包含 64 个处理器。 可以以这种方式对多达 2，048 个处理器进行分组，以横向扩展计算容量。

CF 允许计算群集通过直接访问共享数据。 它用于锁定信息、缓存信息和共享数据资源列表。 使用一个或多个 CF 的并行系统可被视为"共享所有内容"横向扩展计算群集。 有关这些功能的详细信息，请参阅 IBM 网站上的 IBM Z 上的[并行系统](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)。

应用程序可以使用这些功能提供横向扩展性能和高可用性。 有关 CICS 如何使用 CF 并行系统的信息，请下载[IBM CICS 和耦合工具：超越基础知识](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)红皮书。

## <a name="azure-compute-at-a-glance"></a>Azure 计算一览

有些人错误地认为基于英特尔的服务器不如大型机强大。 但是，基于英特尔的新核心密集型系统具有与大型机一样多的计算能力。 本节介绍用于计算和存储的 Azure 基础架构即服务 （IaaS） 选项。 Azure 还提供平台即服务 （PaaS） 选项，但本文重点介绍了提供类似大型机容量的 IaaS 选项。

Azure 虚拟机提供各种大小和类型的计算能力。 在 Azure 中，虚拟 CPU （vCPU） 大致等同于大型机上的内核。

目前，Azure 虚拟机大小范围提供从 1 到 128 vCPU。 虚拟机 （VM） 类型针对特定工作负载进行了优化。 例如，下面的列表显示 VM 类型（截至本文编写时当前）及其建议的用途：

| 大小     | 类型和描述                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D 系列 | 通用，64 vCPU 和高达 3.5-GHz 时钟速度                           |
| 电子系列 | 内存优化，最多 64 个 vCPU                                                 |
| F 系列 | 优化了高达 64 个 vCPU 和 3.7 GHz 时钟速度的计算优化                       |
| H 系列 | 针对高性能计算 （HPC） 应用进行了优化                          |
| L 系列 | 针对由 NoSQL 等数据库支持的高通量应用程序优化的存储 |
| M 系列 | 最大的计算和内存优化的 VM，多达 128 个 vCPU                        |

有关可用 VM 的详细信息，请参阅[虚拟机系列](https://azure.microsoft.com/pricing/details/virtual-machines/series/)。

z14 大型机最多可具有 240 个内核。 但是，z14 大型机几乎从不将所有内核用于单个应用程序或工作负载。 相反，大型机将工作负载划分为逻辑分区 （LPAR），并且 LPAR 具有评级 — MIPS（每秒数百万条指令）或 MSU（百万服务单元）。 在确定在 Azure 上运行大型机工作负荷所需的可比 VM 大小时，将 MIPS（或 MSU） 评级考虑在内。

以下是一般估计值：

-   每 vCPU 150 MIPS

-   每个处理器 1，000 MIPS

要确定 LPAR 中给定工作负载的正确 VM 大小，请先优化工作负载的 VM。 然后确定所需的 vCPU 数量。 保守估计是每 vCPU 150 MIPS。 例如，基于此估计值，具有 16 个 vCPU 的 F 系列 VM 可以轻松支持来自具有 2，400 个 MIPS 的 LPAR 的 IBM Db2 工作负载。

## <a name="azure-compute-scale-up"></a>Azure 计算扩展

M 系列 VM 可以扩展到 128 vCPU（在撰写本文时）。 使用每个 vCPU 150 MIPS 的保守估计值，M 系列 VM 相当于大约 19，000 个 MIPS。 估计大型机的 MIPS 的一般规则是每个处理器 1，000 MIPS。 z14 大型机最多可具有 24 个处理器，并为单个大型机系统提供约 24，000 个 MIPS。

最大的单个 z14 大型机比 Azure 中可用的最大 VM 多约 5，000 个 MIPS。 但是，比较工作负载的部署方式非常重要。 如果大型机系统同时具有应用程序和关系数据库，则它们通常部署在同一物理主机上，每个机都部署在自己的 LPAR 中。 Azure 上的相同解决方案通常使用一个 VM 来部署应用程序，并为数据库使用单独、大小合适的 VM 部署。

例如，如果 M64 vCPU 系统支持该应用程序，并且数据库使用 M96 vCPU，则大约需要 150 vCPU，或者如下图所示，大约需要 24，000 个 MICPU。

![比较 24，000 个 MIPS 的工作负载部署](media/mainframe-compute-mips.png)

方法是将 LPAR 迁移到各个 VM。 然后，Azure 可轻松扩展到部署在单个大型机系统上的大多数应用程序所需的大小。

## <a name="azure-compute-scale-out"></a>Azure 计算横向扩展

基于 Azure 的解决方案的优点之一是能够横向扩展。扩展使应用程序几乎可以无限地使用计算容量。 Azure 支持多种方法来扩展计算能力：

- **跨群集的负载平衡。** 在这种情况下，应用程序可以使用[负载均衡器](/azure/load-balancer/load-balancer-overview)或资源管理器在群集中的多个 VM 之间分散工作负载。 如果需要更多计算容量，则向群集中添加其他 VM。

- **虚拟机缩放集。** 在此突发方案中，应用程序可以根据 VM 使用情况扩展到其他[计算资源](/azure/virtual-machine-scale-sets/overview)。 当需求下降时，规模集中的 VM 数量也会下降，从而确保计算能力的高效使用。

- **PaaS 缩放。** Azure PaaS 提供缩放计算资源。 例如[，Azure Service Fabric](/azure/service-fabric/service-fabric-overview)分配计算资源以满足请求量的增加。

- **库伯内斯集群。** Azure 上的应用程序可以使用[Kubernetes 群集](/azure/aks/concepts-clusters-workloads)来计算指定资源的服务。 Azure 库伯奈斯服务 （AKS） 是一种托管服务，用于协调 Azure 上的库伯奈斯节点、池和群集。

要选择正确的方法来扩展计算资源，请务必了解 Azure 和大型机有何不同。 关键是计算资源如何共享数据，或者数据是否共享。 在 Azure 中，数据（默认情况下）通常不由多个 VM 共享。 如果横向扩展计算群集中的多个 VM 需要数据共享，则共享数据必须驻留在支持此功能的资源中。 在 Azure 上，数据共享涉及存储，如下节讨论。

## <a name="azure-compute-optimization"></a>Azure 计算优化

您可以在 Azure 体系结构中优化每一层处理。 为每个环境使用最合适的 VM 和功能类型。 下图显示了在 Azure 中部署 VM 以支持使用 Db2 的 CICS 应用程序的潜在模式。 在主站点中，以高可用性部署生产、预生产和测试 VM。 辅助站点用于备份和灾难恢复。

每个层还可以提供适当的灾难恢复服务。 例如，生产和数据库 VM 可能需要热或温恢复，而开发和测试 VM 支持冷恢复。

![支持灾难恢复的极可用部署](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>后续步骤

- [大型机迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [大型机在 Azure 虚拟机上重新托管](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [将大型机存储移动到 Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM 资源

- [IBM Z 上的并行系统](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 和耦合设施：超越基础知识](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Creating required users for a Db2 pureScale Feature installation](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)（创建所需用户以安装 Db2 pureScale 功能）
- [Db2icrt - Create instance command](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)（Db2icrt - 创建实例命令）
- [Db2 纯标群集数据库解决方案](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government 

- [适用于大型机应用程序的 Microsoft Azure 政府云](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [微软和FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>更多迁移资源

- [Azure Virtual Data Center Lift and Shift Guide](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)（Azure 虚拟数据中心提升和迁移指南）
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
