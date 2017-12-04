---
title: "关于 Azure Migrate | Microsoft Docs"
description: "概述 Azure Migrate 服务。"
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 7b313bb4-c8f4-43ad-883c-789824add3288
ms.service: migrate
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/23/2017
ms.author: raynew
ms.openlocfilehash: d3d5a3bcd3be55d1915ff7fdc6d82aebbb992fc7
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="about-azure-migrate"></a>关于 Azure Migrate

Azure Migrate 服务会评估要迁移到 Azure 的本地工作负荷。 该服务会评估是否适合迁移以及需要根据性能进行哪些大小调整，并提供在 Azure 上运行本地计算机的成本估算。 如果打算进行直接迁移，或者处于迁移的早期评估阶段，则不妨选择此服务。 进行评估以后，即可使用 Azure Site Recovery 和 Azure 数据库迁移之类的服务将计算机迁移到 Azure。

> [!NOTE]
> Azure Migrate 目前为预览版，支持生产工作负荷。

## <a name="why-use-azure-migrate"></a>为何使用 Azure Migrate？

Azure Migrate 有助于：

- **评估 Azure 迁移就绪性**：评估本地计算机是否适合在 Azure 中运行。 
- **获取大小建议**：迁移后 Azure VM 的大小调整建议，具体取决于本地 VM 的性能历史记录。 
- **估算每月成本**：在 Azure 中运行本地计算机的估算成本。
- **充满信心地进行迁移**：将本地计算机组合起来进行评估时，可以通过将依赖关系可视化来提高评估可信度。 可以准确地查看特定计算机或一个组中所有计算机的依赖关系。

## <a name="current-limitations"></a>当前限制

- 目前可以评估要迁移到 Azure VM 的本地 VMware 虚拟机 (VM)。
> [!NOTE]
> 对 Hyper-V 的支持已在规划中，将在数月内启用。 在此期间，建议使用 Azure Site Recovery 部署规划器来计划 Hyper-V 工作负荷的迁移。 
- 可以在单次评估中最多评估 1000 个 VM，在单个 Azure Migrate 项目中最多评估 1500 个计算机。 如需评估更多，可以增加项目数或评估次数。 [了解详细信息](how-to-scale-assessment.md)。
- 必须通过 vCenter Server 5.5、6.0 或 6.5 来管理需评估的 VM。
- 只能在“美国中西部”区域创建一个 Azure Migrate 项目。 但是，这不会影响迁移计划的制定。可以迁移到另一 Azure 目标位置。 迁移项目的位置只用来存储在本地环境中发现的元数据。
- Azure Migrate 门户目前仅提供英文版。 
- Azure Migrate 目前仅支持[本地冗余存储 (LRS)](../storage/common/storage-introduction.md#replication) 复制。

## <a name="what-do-i-need-to-pay-for"></a>需要支付哪些费用？

无需额外付费即可使用 Azure Migrate。 但在公开预览期间，使用依赖关系可视化功能需要额外付费。 默认情况下，Azure Migrate 创建支持[依赖关系可视化](concepts-dependency-visualization.md)的 Log Analytics 工作区。 如果使用依赖关系可视化，或使用 Azure Migrate 外部的工作区，则需按工作区使用情况付费。 [详细了解](https://azure.microsoft.com/en-us/pricing/details/insight-analytics/)费用。 当该服务公开发布后，使用依赖关系可视化功能不需付费。


## <a name="whats-in-an-assessment"></a>评估内容有哪些？

Azure Migrate 评估基于表中汇总的设置。

**设置** | **详细信息**
--- | ---
**目标位置** | 要迁移到的 Azure 位置。 默认情况下，这是创建 Azure Migrate 项目的位置。 可以修改此设置。   
**存储冗余** | Azure VM 会在迁移后使用的存储类型。 LRS 为默认设置。
**定价计划** | 评估会考虑你是否加入了软件保障计划，因而能够使用 [Azure 混合使用权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)这一情况。 此外还会考虑应该应用的 Azure 优惠，并允许你指定可以在该优惠基础上使用的特定于订阅的折扣 (%)。 
**定价层** | 可以指定 Azure VM 的[定价层（基本/标准）](../virtual-machines/windows/sizes-general.md)。 这是为了迁移到合适的 Azure VM 系列，具体取决于是否在生产环境中。 默认使用[标准](../virtual-machines/windows/sizes-general.md)层。
**性能历史记录** | 默认情况下，Azure Migrate 使用一个月的历史记录来评估本地计算机的性能，百分位数为 95%。 可以修改此设置。
**舒适因子** | Azure Migrate 在评估期间会考虑到缓冲（舒适因子）。 该缓冲应用到 VM 的机器使用率数据（CPU、内存、磁盘和网络）上。 舒适因子考虑到季节性使用特点、短期性能历史记录，以及未来使用量可能会增加等问题。<br/><br/> 例如，一个使用率为 20% 的 10 核 VM 通常相当于一个 2 核 VM。 但是，如果舒适因子为 2.0x，则结果就变成一个 4 核 VM。 默认的舒适设置为 1.3x。


## <a name="how-does-azure-migrate-work"></a>Azure Migrate 工作原理

1.  创建 Azure Migrate 项目。
2.  Azure Migrate 使用名为“收集器设备”的本地 VM 来发现有关本地计算机的信息。 若要创建该设备，请以开放虚拟化设备 (.ova) 格式下载安装程序文件，然后将其作为 VM 导入到本地 vCenter Server。
3.  使用 vCenter Server 的只读凭据连接到 VM，然后运行收集器。
4.  收集器使用 VMware PowerCLI cmdlet 收集 VM 元数据。 发现是无代理发现，且不在 VMware 主机或 VM 上安装任何内容。 收集的元数据包括 VM 信息（核心、内存、磁盘、磁盘大小、网络适配器）。 此外还收集 VM 的性能数据，包括 CPU 和内存使用情况、磁盘 IOPS、磁盘吞吐量 (MBps)、网络输出 (MBps)。
5.  元数据推送到 Azure Migrate 项目， 可以在 Azure 门户中查看。
6.  将 VM 按组收集，以便进行评估。 例如，可以将运行同一应用的 VM 作为一组。 可以在 vCenter 或 vCenter 门户中使用标记对 VM 分组。 请使用可视化来验证特定计算机或一个组中所有计算机的依赖关系。
7.  为组创建评估。
8.  评估完成以后，可以在门户中查看，也可以通过 Excel 格式来下载。



  ![Azure 规划器体系结构](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>有哪些端口要求？

下表汇总了进行 Azure Migrate 通信所需的端口。

|组件          |通信对象     |所需端口  |原因   |
|-------------------|------------------------|---------------|---------|
|收集器          |Azure Migrate 服务   |TCP 443        |连接器通过 SSL 端口 443 连接到服务|
|收集器          |vCenter Server          |默认为 9443   | 默认情况下，收集器在端口 9443 上连接到 vCenter Server。 如果服务器在另一端口上侦听，则应在收集器 VM 上将该端口配置为传出端口。 |
|本地 VM     | OMS 工作区          |[TCP 443](../log-analytics/log-analytics-windows-agents.md#system-requirements-and-required-configuration) |MMA 代理使用 TCP 443 连接到 Log Analytics。 只有在使用依赖关系可视化功能且要安装 MMA 代理的情况下，才需要此端口。 |


  
## <a name="what-happens-after-assessment"></a>评估后会发生什么情况？

通过 Azure Migrate 服务评估要迁移的本地计算机以后，即可使用多种工具进行迁移：

- **Azure Site Recovery**：可以使用 Azure Site Recovery 迁移到 Azure，如下所示：
  - 准备 Azure 资源，其中包括 Azure 订阅、Azure 虚拟网络和存储帐户。
  - 准备要迁移的本地 VMware 服务器。 请验证 Site Recovery 的 VMware 支持要求，准备要发现的 VMware 服务器，并准备将 Site Recovery 移动服务安装在要迁移的 VM 上。 
  - 设置迁移。 先设置恢复服务保管库，配置源和目标迁移设置，设置复制策略，然后启用复制。 可以运行灾难恢复演练，确保将 VM 正确迁移到 Azure。
  - 运行故障转移，将本地计算机迁移到 Azure。 
  - 在 Site Recovery 迁移教程中[了解详情](../site-recovery/tutorial-migrate-on-premises-to-azure.md)。

- **Azure 数据库迁移**：如果本地计算机在运行 SQL Server、MySQL 或 Oracle 之类的数据库，则可使用 Azure 数据库迁移服务将其迁移到 Azure。 [了解详细信息](https://azure.microsoft.com/campaigns/database-migration/)。



## <a name="next-steps"></a>后续步骤 
[按教程](tutorial-assessment-vmware.md)创建本地 VMware VM 的评估。