---
title: 关于 Azure Migrate | Microsoft Docs
description: 概述 Azure Migrate 服务。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 08/08/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 8371a160d129586f63b2f14946ed34a8d0637f6c
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714234"
---
# <a name="about-azure-migrate"></a>关于 Azure Migrate

Azure Migrate 服务会评估要迁移到 Azure 的本地工作负荷。 该服务会评估是否适合迁移本地计算机，会根据性能进行大小调整，并会提供在 Azure 中运行本地计算机的成本估算。 如果打算进行直接迁移，或者处于迁移的早期评估阶段，则不妨选择此服务。 进行评估以后，即可使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 和 [Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/dms-overview)之类的服务将计算机迁移到 Azure。

## <a name="why-use-azure-migrate"></a>为何使用 Azure Migrate？

Azure Migrate 有助于：

- **评估 Azure 迁移就绪性**：评估本地计算机是否适合在 Azure 中运行。
- **获取大小建议**：获取 Azure VM 的大小建议，具体取决于本地 VM 的性能历史记录。
- **估算每月成本**：获取在 Azure 中运行本地计算机的估算成本。  
- **充满信心地进行迁移**：将本地计算机的依赖关系可视化，创建可以一起进行评估和迁移的计算机组。

## <a name="current-limitations"></a>当前限制

- 目前只能评估要迁移到 Azure VM 的本地 VMware 虚拟机 (VM)。 VMware VM 必须通过 vCenter Server（5.5、6.0 或 6.5 版）进行管理。
- 若要评估 Hyper-VM 和物理服务器，请使用 [Azure Site Recovery 部署规划器](http://aka.ms/asr-dp-hyperv-doc)进行 Hyper-V 评估，使用我们的[合作伙伴工具](https://azure.microsoft.com/migration/partners/)进行物理计算机评估。
- 一次发现最多可以发现 1500 个 VM，单个项目中最多可以发现 1500 个 VM。 另外，单次评估最多可以评估 1500 个 VM。
- 若要发现更大的环境，可以拆分发现，然后创建多个项目。 [了解详细信息](how-to-scale-assessment.md)。 Azure Migrate 最多允许每个订阅 20 个项目。
- 只能在“美国中西部”或“美国东部”区域创建一个 Azure Migrate 项目。 这不会影响迁移计划的制定。可以迁移到任何 Azure 目标位置。 迁移项目的位置只用来存储在本地环境中发现的元数据。
- Azure Migrate 仅支持使用托管磁盘进行迁移评估。


## <a name="what-do-i-need-to-pay-for"></a>需要支付哪些费用？

[详细了解](https://azure.microsoft.com/pricing/details/azure-migrate/) Azure Migrate 定价。


## <a name="whats-in-an-assessment"></a>评估内容有哪些？

可以根据需要自定义评估设置。 下表汇总了评估属性。

**属性** | **详细信息**
--- | ---
**目标位置** | 要迁移到的 Azure 位置。<br/><br/>Azure Migrate 目前支持 30 个区域。 [查看区域](https://azure.microsoft.com/global-infrastructure/services/)。 默认情况下，目标区域设置为“美国西部 2”。
**存储类型** | 要在 Azure 中分配的磁盘的类型。 这适用于大小调整条件为“按本地”的情况。 请将目标磁盘类型指定为高级（默认）或标准托管磁盘。 对于基于性能的大小调整，将根据 VM 的性能数据自动提供磁盘大小调整建议。 
**“大小调整”条件** | 可以根据本地 VM 的**性能历史记录**进行大小调整，也可以**按本地**（默认设置）要求来进行，不考虑性能历史记录。 
**Azure 产品/服务** | 加入的 [Azure 套餐](https://azure.microsoft.com/support/legal/offer-details/)。 Azure Migrate 会进行相应的成本估算。
**Azure 混合权益** | 是否有软件保证，以及是否有资格享受带成本折扣的 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。
**预留实例** |  是否在 Azure 中有[预留实例](https://azure.microsoft.com/pricing/reserved-vm-instances/)。 Azure Migrate 会进行相应的成本估算。
**VM 运行时间** | VM 将在 Azure 中运行的持续时间。 会相应地进行成本估算。
**定价层** | 目标 Azure VM 的[定价层（基本/标准）](../virtual-machines/windows/sizes-general.md)。 例如，如果打算迁移生产环境，则可考虑“标准”层，其提供的 VM 延迟较低但成本可能较高。 而在测试环境中，则可使用基本层，其延迟较高，但成本较低。 默认使用[标准](../virtual-machines/windows/sizes-general.md)层。
**性能历史记录** | 默认情况下，Azure Migrate 使用过去一天的性能历史记录来评估本地计算机的性能，百分位数为 95%。 
**VM 系列** | 用于大小评估的 VM 系列。 例如，如果不打算将生产环境迁移到 Azure 中的 A 系列 VM，可以从列表或系列中排除 A 系列。 大小调整仅取决于所选系列。   
**舒适因子** | Azure Migrate 在评估期间会考虑到缓冲（舒适因子）。 该缓冲应用到 VM 的机器使用率数据（CPU、内存、磁盘和网络）上。 舒适因子考虑到季节性使用特点、短期性能历史记录，以及未来使用量可能会增加等问题。<br/><br/> 例如，一个使用率为 20% 的 10 核 VM 通常相当于一个 2 核 VM。 但是，如果舒适因子为 2.0x，则结果就变成一个 4 核 VM。 默认的舒适设置为 1.3x。


## <a name="how-does-azure-migrate-work"></a>Azure Migrate 工作原理

1.  创建 Azure Migrate 项目。
2.  Azure Migrate 使用名为“收集器设备”的本地 VM 来发现有关本地计算机的信息。 若要创建该设备，请以开放虚拟化设备 (.ova) 格式下载安装程序文件，然后将其作为 VM 导入到本地 vCenter Server。
3. 请从 vCenter Server 连接到 VM，并在连接时为其指定新密码。
4. 在要启动发现的 VM 上运行收集器。
5. 收集器使用 VMware PowerCLI cmdlet 收集 VM 元数据。 发现是无代理发现，且不在 VMware 主机或 VM 上安装任何内容。 收集的元数据包括 VM 信息（核心、内存、磁盘、磁盘大小、网络适配器）。 此外还收集 VM 的性能数据，包括 CPU 和内存使用情况、磁盘 IOPS、磁盘吞吐量 (MBps)、网络输出 (MBps)。
5.  元数据推送到 Azure Migrate 项目， 可以在 Azure 门户中查看。
6.  将发现的 VM 按组收集，以便进行评估。 例如，可以将运行同一应用程序的 VM 作为一组。 若要进行更精确的分组，还可以使用依赖关系可视化来查看特定计算机的依赖关系，或者查看一个组中所有计算机的依赖关系，然后对组进行优化。
7.  定义某个组以后，请为其创建评估。
8.  评估完成以后，可以在门户中查看，也可以通过 Excel 格式来下载。

  ![Azure Migrate 体系结构](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>有哪些端口要求？

下表汇总了进行 Azure Migrate 通信所需的端口。

组件 | 通信对象 |  详细信息
--- | --- |--- 
收集器  | Azure Migrate 服务 | 连接器通过 SSL 端口 443 连接到服务。
收集器 | vCenter Server | 默认情况下，收集器在端口 443 上连接到 vCenter Server。 如果服务器在另一端口上侦听，请在收集器 VM 上将其配置为传出端口。 
本地 VM | Log Analytics 工作区 | [TCP 443] | [Microsoft Monitoring Agent (MMA)](../log-analytics/log-analytics-windows-agent.md) 使用 TCP 端口 443 连接到 Log Analytics。 只有在使用需要 MMA 代理的依赖关系可视化功能时，才需要此端口。 


## <a name="what-happens-after-assessment"></a>评估后会发生什么情况？

评估本地计算机以后，即可使用多种工具进行迁移：

- **Azure Site Recovery**：可以使用 Azure Site Recovery 迁移到 Azure。 为此，请根据需要[准备 Azure 组件](../site-recovery/tutorial-prepare-azure.md)，包括存储帐户和虚拟网络。 在本地，请[准备 VMware 环境](../site-recovery/vmware-azure-tutorial-prepare-on-premises.md)。 一切准备好以后，请设置并启用目标为 Azure 的复制，然后迁移 VM。 [了解详细信息](../site-recovery/vmware-azure-tutorial.md)。
- **Azure 数据库迁移**：如果本地计算机在运行 SQL Server、MySQL 或 Oracle 之类的数据库，则可使用 [Azure 数据库迁移服务](../dms/dms-overview.md)将其迁移到 Azure。 


## <a name="next-steps"></a>后续步骤

- [按教程](tutorial-assessment-vmware.md)创建本地 VMware VM 的评估。
- [查看常见问题解答](resources-faq.md)（关于 Azure Migrate）。
