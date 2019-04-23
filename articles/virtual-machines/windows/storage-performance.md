---
title: 优化 Azure Lsv2 系列虚拟机的存储上的性能 |Microsoft Docs
description: 了解如何优化您的解决方案 Lsv2 系列虚拟机上的性能。
services: virtual-machines-windows
author: laurenhughes
manager: jeconnoc
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: f84e81a5a9e9c9cf6f477adefa0869d776f7dd71
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013524"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>优化 Lsv2 系列虚拟机上的性能

Lsv2 系列虚拟机支持多种工作负载的需要对多种应用程序和行业的高 I/O 和在本地存储的吞吐量。  Lsv2 系列非常适合于大数据、 SQL、 NoSQL 数据库、 数据仓库和大型事务数据库，包括 Cassandra、 MongoDB、 Cloudera 和 Redis。

Lsv2 系列虚拟机 (Vm) 的设计可最大化 AMD EPYC™ 7551 处理器提供的处理器、 内存、 NVMe 设备以及在 Vm 之间的最佳性能。 除了最大化硬件性能，Lsv2 系列 Vm 都设计成可更好的性能与硬件和软件的 Windows 和 Linux 操作系统的需求。

优化的软件和硬件导致的优化版本[Windows Server 2019 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview)、 中到 Azure Marketplace Lsv2 系列中的 NVMe 设备支持最大性能年 12 月 2018 年初发布虚拟机。

本文提供提示和建议，确保你的工作负荷和应用程序实现设计到的 Vm 的最大性能。 随着更多优化 Lsv2 映像添加到 Azure Marketplace，此页上的信息将不断更新。

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™ 芯片体系结构

Lsv2 系列的 Vm 使用 AMD EYPC™ 服务器处理器基于 Zen 微体系结构。 AMD 开发为 EYPC™ 无穷大 Fabric （如果） 为可缩放互联进行可用于在骰子、 包，以及多个程序包通信其 NUMA 模型。 使用 QPI （快速路径互连） 和 UPI （超高路径互连） 用于 Intel 现代整体式骰子处理器相比，AMD 的多 NUMA 小骰子体系结构可能会使这两个性能优势和挑战。 内存带宽和延迟约束的实际影响无法运行的工作负荷的类型而异。

## <a name="tips-for-maximizing-performance"></a>最大程度提高性能的提示

* 为提供支持的 Lsv2 系列虚拟机的硬件利用具有 8 个 I/O 队列对 (QP) s NVMe 设备。 NVMe 设备 I/O 的每个队列都是实际对： 提交队列和完成队列。 NVMe 驱动程序设置以优化通过分发我这些八个 I/O QPs 的使用率/O 在轮循机制计划。 若要获得最大性能，运行每个设备匹配的八个作业。

* 避免在活动的工作负荷期间混合 NVMe 管理员命令 （例如，智能 NVMe 信息查询等） 使用 NVMe I/O 命令。 Lsv2 NVMe 设备受 HYPER-V NVMe 直接技术，只要任何 NVMe 管理员命令处于挂起状态切换为"慢速模式"。 Lsv2 用户可能会看到显著的性能删除 NVMe I/O 性能，如果发生这种情况。

* Lsv2 用户不应依赖于从报告中的数据驱动器 VM，以决定其应用程序的 NUMA 关联设备 NUMA 信息 (所有 0)。 更好的性能的建议的方法是在可能的情况在 Cpu 之间分散工作负荷。 

* 每个 I/O 队列对 Lsv2 VM NVMe 设备的受支持的最大队列深度为 1024 (vs。Amazon i3 QD 32 限制）。 Lsv2 用户应限制为 1024年或更低，若要避免触发队列完整条件，可能会降低性能的队列深度其 （综合） 的基准测试工作负荷。

## <a name="utilizing-local-nvme-storage"></a>利用本地 NVMe 存储

所有 Lsv2 Vm 上的 1.92 TB NVMe 磁盘上的本地存储是暂时的。 在成功标准重启 VM，将会保留本地 NVMe 磁盘上的数据。 如果重新部署、 取消分配，或删除 VM，NVMe 上将不会保留数据。 如果另一个问题导致 VM 或硬件运行，变得不正常，将不会保留数据。 在此情况下，被安全地删除旧主机上的任何数据。

当 VM 需要移动到不同的主机计算机，例如，在计划内的维护操作期间，也将用例。 可以使用预期的计划内的维护操作和某些硬件故障[计划事件](scheduled-events.md)。 计划的事件用于保持更新任何预测的维护和恢复操作状态。

在计划内的维护事件所需的 VM 具有空的本地磁盘的新主机上重新创建它的情况下，数据将需要重新同步 （同样，与任何数据要安全地删除旧主机上）。 这是因为 Lsv2 系列的 Vm 当前不支持实时迁移的本地 NVMe 磁盘上。

有两种模式进行计划内维护。

### <a name="standard-vm-customer-controlled-maintenance"></a>标准 VM 客户控制维护

- 将 VM 移动到更新的主机中，在 30 天时段。
- 因此，事件之前的备份数据，则可能会丢失，Lsv2 本地存储数据。

### <a name="automatic-maintenance"></a>自动维护

- 如果客户不会执行客户控制维护或发生紧急过程，例如安全零天事件发生。
- 用于保留客户数据，但 VM 冻结或重新启动的小小的风险。
- 因此，事件之前的备份数据，则可能会丢失，Lsv2 本地存储数据。

对于任何即将推出的服务事件，用于实施受控性维护过程选择最方便的更新的时间。 在该事件之前可能会备份高级存储中的数据。 维护事件完成后，你可以返回到刷新 Lsv2 Vm 本地 NVMe 存储你的数据。

维护本地 NVMe 磁盘上的数据的方案包括：

- VM 正在运行且正常运行。
- VM 在位置重新启动 （由你或 Azure）。
- VM 已暂停 （停止而无需解除分配）。
- 大多数处理操作的计划内维护。

安全地擦除数据来保护客户的方案包括：

- 重新部署 VM 后，已停止 （解除分配），或删除 （由你）。
- VM 将变为不正常以及对服务修复到由于硬件问题的另一个节点。
- 少量的服务操作的计划内维护的所需的 VM 可以重新分配到另一台主机的维护服务。

若要了解有关用于备份数据存储在本地存储选项的详细信息，请参阅[的 Azure IaaS 磁盘备份和灾难恢复](backup-and-disaster-recovery-for-azure-iaas-disks.md)。

## <a name="frequently-asked-questions"></a>常见问题

* **如何启动部署 Lsv2 系列 Vm？**  
   就像任何其他 VM，使用[门户](quick-create-portal.md)， [Azure CLI](quick-create-cli.md)，或[PowerShell](quick-create-powershell.md)创建 VM。

* **将一次 NVMe 磁盘故障导致无法在主机上的所有 Vm？**  
   如果硬件节点上检测到磁盘故障，硬件处于失败状态。 当发生这种情况时，在节点上的所有 Vm 自动取消分配并移动到正常的节点。 对于 Lsv2 系列 Vm，这意味着故障节点上的客户的数据还可安全擦除并将需要由客户在新节点上重新创建。 如所述，实时迁移变得上 Lsv2 可用之前，故障节点上的数据将主动移动虚拟机在传输到另一个节点。

* **我是否需要在 Windows Server 2012 中的 Windows 或 Windows Server 2016 中进行轮询调整？**  
   在 Azure 上的 Windows Server 2019 上才 NVMe 轮询。  

* **我可以切换回传统中断服务例程 (ISR) 模型？**  
   Lsv2 系列 Vm 进行了优化的 NVMe 轮询。 持续提供更新以提高轮询性能。

* **可以调整 Windows Server 2019 中的轮询设置？**  
   轮询设置不是用户可调整。
   
## <a name="next-steps"></a>后续步骤

* 请参阅所有规范[虚拟机以存储性能优化](sizes-storage.md)在 Azure 上
