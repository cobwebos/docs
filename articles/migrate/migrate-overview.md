---
title: 关于 Azure Migrate | Microsoft Docs
description: 概述 Azure 迁移服务。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 10/23/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 85873dc023e63b7cc9f5ba3ff87214c49ac16e34
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246729"
---
# <a name="about-azure-migrate"></a>关于 Azure 迁移

Azure 迁移服务会评估要迁移到 Azure 的本地工作负载。 该服务评估本地机器的迁移适用性，会根据性能进行大小调整，并为在Azure中运行本地机器提供成本估算。 如果打算进行直接迁移，或者处于迁移的早期评估阶段，则不妨选择此服务。 评估以后，即可使用 [Azure 站点恢复](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 和 [Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/dms-overview)之类的服务将计算机迁移到 Azure。

## <a name="why-use-azure-migrate"></a>为何使用 Azure 迁移？

Azure 迁移有助于：

- **评估 Azure 迁移就绪性**：评估本地计算机是否适合在 Azure 中运行。
- **获取大小建议**：获取 Azure虚拟机的大小建议，具体取决于本地 VM 的性能历史记录。
- **估算每月成本**：获取在 Azure 中运行本地计算机的估算成本。  
- **自信地迁移**：可视化本地机器的依赖关系，创建可以一起进行评估和迁移的计算机组。

## <a name="current-limitations"></a>当前限制

- 您只能评估要迁移到 Azure 虚拟机的本地 VMware 虚拟机 (VM)。 VMware虚拟机必须由 vCenter Server（5.5、6.0 或 6.5 版本）进行管理。
- 若要评估 Hyper-VM 和物理服务器，请使用 [Azure 站点恢复部署规划器](https://aka.ms/asr-dp-hyperv-doc)进行 Hyper-V 评估，使用我们的[合作伙伴工具](https://azure.microsoft.com/migration/partners/)进行物理计算机评估。
- 单个遍历最多可以遍历1500 个虚拟机，在单个项目中最多可以遍历1500 个虚拟机。 另外，单次评估最多可以评估 1500 个虚拟机。
- 若要发现更大的环境，可以拆分发现并创建多个项目。 [了解详细信息](how-to-scale-assessment.md)。 Azure 迁移最多允许每个订阅 20 个项目。
- Azure 迁移仅支持使用托管磁盘进行迁移评估。
-  只能在地理位置为“美国”的区域创建一个 Azure 迁移项目。 但是，可以计划到任意目标 Azure 位置的迁移。
    - 只有在本地环境中发现的元数据才会存储在迁移项目区域中。
    - 元数据存储在地理上的一个区域:美国中西部/美国东部。
    - 如果您在日志分析工作区中使用依赖关系可视化，则会在项目所在的区域中创建它。


## <a name="what-do-i-need-to-pay-for"></a>需要支付哪些费用？

[详细了解](https://azure.microsoft.com/pricing/details/azure-migrate/) Azure 迁移定价。


## <a name="whats-in-an-assessment"></a>评估内容有哪些？

可以根据需要自定义评估设置。 下表汇总了评估属性。

**属性** | **详细信息**
--- | ---
**目标位置** | 要迁移到的 Azure 位置。<br/><br/>Azure Migrate 目前支持 30 个区域。 [查看区域](https://azure.microsoft.com/global-infrastructure/services/)。 默认情况下，目标区域设置为“美国西部 2”。
**存储类型** | 要在 Azure 中分配的磁盘类型。 这适用于大小调整条件为“按本地”的情况。 请将目标磁盘类型指定为高级（默认）或标准托管磁盘。 对于基于性能的大小调整，将根据虚拟机的性能数据自动提供磁盘大小调整建议。
**“大小调整”条件** | 可以根据本地虚拟机的**性能历史记录**进行大小调整，也可以**按本地**（默认设置）要求来进行，不考虑性能历史记录。
**Azure 产品/服务** | 加入的 [Azure 套餐](https://azure.microsoft.com/support/legal/offer-details/)。 Azure 迁移会进行相应的成本估算。
**Azure 混合权益** | 是否有软件保证，以及是否有资格享受带成本折扣的 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。
**预留实例** |  是否在 Azure 中有[预留实例](https://azure.microsoft.com/pricing/reserved-vm-instances/)。 Azure 迁移会进行相应的成本估算。
**虚拟机运行时间** |虚拟机将在 Azure 中运行的持续时间。 会相应地进行成本估算。
**定价层** | 目标 Azure虚拟机的[定价层（基本/标准）](../virtual-machines/windows/sizes-general.md)。 例如，如果打算迁移生产环境，则可考虑“标准”层，其提供的虚拟机延迟较低但成本可能较高。 而在测试环境中，则可使用基本层，其延迟较高，但成本较低。 默认使用[标准](../virtual-machines/windows/sizes-general.md)层。
**性能历史记录** | 默认情况下，Azure 迁移使用过去一天的性能历史记录来评估本地计算机的性能，百分位数为 95%。
**虚拟机系列** | 用于大小评估的虚拟机系列。 例如，如果不打算将生产环境迁移到 Azure 中的 A 系列虚拟机，可以从列表或系列中排除 A 系列。 大小调整仅取决于所选系列。   
**缓冲因素** | Azure 迁移在评估期间会考虑到缓冲（舒适因子）。 该缓冲应用到虚拟机的机器使用率数据（CPU、内存、磁盘和网络）上。 缓冲考虑到季节性使用特点、短期性能历史记录，以及未来使用量可能会增加等问题。<br/><br/> 例如，一个使用率为 20% 的 10 核虚拟机通常相当于一个 2 核虚拟机。 但是，如果缓冲为 2.0x，则结果就变成一个 4 核虚拟机。 默认的缓冲设置为 1.3x。


## <a name="how-does-azure-migrate-work"></a>Azure 迁移工作原理

1.  创建 Azure 迁移项目。
2.  Azure 迁移使用名为“收集器设备”的本地虚拟机来发现有关本地计算机的信息。 若要创建该设备，需要下载Open Virtualization appliance (.ova)格式的安装文件，并将其作为虚拟机导入到本地的vCenter服务器上。
3. 请从 vCenter Server 连接到虚拟机，并在连接时为其指定新密码。
4. 在要启动发现的虚拟机上运行收集器。
5. 收集器使用 VMware PowerCLI cmdlet 收集虚拟机元数据。 发现是无代理发现，且不在 VMware 主机或 VM 上安装任何内容。 收集的元数据包括虚拟机信息（内核、内存、磁盘、磁盘大小、网络适配器）。 此外还收集虚拟机 的性能数据，包括 CPU 和内存使用情况、磁盘 IOPS、磁盘吞吐量 (MBps)、网络输出 (MBps)。
6.  元数据推送到 Azure 迁移项目， 可以在 Azure 门户中查看。
7.  为了进行评估，您可以将发现的虚拟机按组收集。 例如，可以将运行同一应用程序的虚拟机作为一组。 若要进行更精确的分组，还可以使用依赖关系可视化来查看特定计算机的依赖关系，或者查看一个组中所有计算机的依赖关系并优化组。
8.  定义某个组以后，请为其创建评估。
9.  评估完成以后，可以在门户中查看，也可以通过 Excel 格式来下载。

  ![Azure 迁移体系结构](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>有哪些端口要求？

下表汇总了进行 Azure 迁移通信所需的端口。

组件 | 通信对象 |  详细信息
--- | --- |---
收集器  | Azure Migrate 服务 | 连接器通过 SSL 端口 443 连接到服务。
收集器 | vCenter Server | 默认情况下，收集器在端口 443 上连接到 vCenter Server。 如果服务器在另一端口上侦听，请在收集器虚拟机上将其配置为传出端口。
本地虚拟机| 日志分析器工作区 | [TCP 443] | [Microsoft Monitoring Agent (MMA)](../log-analytics/log-analytics-windows-agent.md) 使用 TCP 端口 443 连接到日志分析器。 只有在使用需要 MMA 代理的依赖关系可视化功能时，才需要此端口。


## <a name="what-happens-after-assessment"></a>评估后会发生什么情况？

评估本地计算机以后，即可使用多种工具进行迁移：

- **Azure 网站恢复**：可以使用 Azure 网站恢复迁移到 Azure。 为此，请根据需要[准备 Azure 组件](../site-recovery/tutorial-prepare-azure.md)，包括存储帐户和虚拟网络。 在本地，请[准备 VMware 环境](../site-recovery/vmware-azure-tutorial-prepare-on-premises.md)。 一切准备好以后，请设置并启用目标为 Azure 的复制，然后迁移 VM。 [了解详细信息](../site-recovery/vmware-azure-tutorial.md)。
- **Azure 数据库迁移**：如果本地计算机在运行 SQL Server、MySQL 或 Oracle 之类的数据库，则可使用 [Azure 数据库迁移服务](../dms/dms-overview.md)将其迁移到 Azure。


## <a name="next-steps"></a>下一步骤

- [按教程](tutorial-assessment-vmware.md)创建本地 VMware 虚拟机的评估。
- [查看常见问题解答](resources-faq.md)（关于 Azure 迁移）。
