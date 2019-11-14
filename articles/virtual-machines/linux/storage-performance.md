---
title: 优化 Azure Lsv2 系列虚拟机上的性能-存储
description: 了解如何为 Lsv2 系列虚拟机上的解决方案优化性能。
services: virtual-machines-linux
author: laurenhughes
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/05/2019
ms.author: joelpell
ms.openlocfilehash: 8d99f63ae084b4f1dae3c0125420eaecf5655e2d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034755"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>优化 Lsv2 系列虚拟机上的性能

Lsv2 系列虚拟机支持多种工作负载，这些工作负载需要在各种应用程序和行业上跨本地存储提供高 i/o 和吞吐量。  Lsv2 系列适用于大数据、SQL、NoSQL 数据库、数据仓库和大型事务数据库，包括 Cassandra、MongoDB、Cloudera 和 Redis。

Lsv2 系列虚拟机（Vm）的设计可最大程度地提高 AMD EPYC™7551处理器的最大性能，以提供处理器、内存、NVMe 设备和 Vm 之间的最佳性能。 使用 Linux 中的合作伙伴时，有几个版本适用于 Lsv2 系列性能优化的 Azure Marketplace，当前包括：

- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 8。0
- Debian 9
- Debian 10

本文提供了一些提示和建议，以确保你的工作负荷和应用程序实现在 Vm 中设计的最大性能。 当将更多的 Lsv2 优化映像添加到 Azure Marketplace 时，此页上的信息会不断更新。

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™芯片体系结构

Lsv2 系列 Vm 使用 AMD EYPC™ server 基于 Zen 微体系结构的服务器处理器。 适用于 EYPC 的 AMD 开发的无穷 Fabric （IF）™作为其 NUMA 模型的可伸缩互连，可用于片间、包外和多包通信。 与 Intel 现代单片处理器上使用的 QPI （快速路径互连）和 UPI （超高路径互连）相比，AMD 的多 NUMA 小型芯片体系结构可带来性能优势和挑战。 内存带宽和延迟约束的实际影响取决于运行的工作负荷类型。

## <a name="tips-to-maximize-performance"></a>最大化性能的提示

* 如果要为工作负荷上传自定义 Linux GuestOS，请注意，默认情况下，加速网络将处于**关闭状态**。 如果要启用加速网络，请在创建 VM 时启用它以获得最佳性能。

* Lsv2 系列 Vm 支持的硬件使用具有八个 i/o 队列对（QP）的 NVMe 设备。 每个 NVMe 设备 i/o 队列实际上是一对：提交队列和完成队列。 NVMe 驱动程序设置为通过按轮循机制计划分发 i/o 来优化这八个 i/o QPs 的利用率。 若要获得最大性能，请运行每个设备的8个作业以进行匹配。

* 避免在活动工作负荷期间混合用 nvme i/o 命令来混合 NVMe 管理命令（例如，NVMe 智能信息查询等）。 Lsv2 NVMe 设备由 Hyper-v NVMe 直接技术支持，只要任何 NVMe 管理命令挂起，该技术就会切换为 "慢速模式"。 如果发生这种情况，Lsv2 的用户可以在 NVMe i/o 性能下看到显著的性能下降。

* Lsv2 用户不应依赖于 VM 内报告的设备 NUMA 信息（全部0）来确定应用的 NUMA 关联。 为了获得更好的性能，建议在可能的情况上将工作负荷分散到多个 Cpu。

* 对于 Lsv2 VM NVMe 设备，每个 i/o 队列对受支持的最大队列深度为1024（与 Amazon i3 QD 32 限制）。 Lsv2 用户应将其（合成）基准工作负荷限制为队列深度1024或更低，以避免触发队列的全部情况，这会降低性能。

## <a name="utilizing-local-nvme-storage"></a>利用本地 NVMe 存储

所有 Lsv2 Vm 上 1.92 TB NVMe 磁盘上的本地存储是暂时的。 在成功进行 VM 的标准重启期间，本地 NVMe 磁盘上的数据将会保留。 如果重新部署、释放或删除 VM，则不会在 NVMe 上保留数据。 如果其他问题导致 VM 或运行的硬件出现故障，则数据不会持久保存。 发生这种情况时，将安全地清除旧主机上的任何数据。

此外，在计划内维护操作期间，需要将 VM 移到不同的主机。 [Scheduled Events](scheduled-events.md)，可能会预计计划内维护操作和某些硬件故障。 应该使用 Scheduled Events 来及时更新任何预测的维护和恢复操作。

如果计划内维护事件要求在包含空本地磁盘的新主机上重新创建 VM，则需要重新同步数据（同样，需要安全地消除旧主机上的任何数据）。 出现这种情况的原因是，Lsv2 系列 Vm 当前不支持本地 NVMe 磁盘上的实时迁移。

计划内维护有两种模式。

### <a name="standard-vm-customer-controlled-maintenance"></a>标准 VM 客户控制的维护

- VM 将在30天内移动到已更新的主机。
- Lsv2 本地存储数据可能会丢失，因此建议在事件之前备份数据。

### <a name="automatic-maintenance"></a>自动维护

- 如果客户不执行客户控制的维护，或在出现紧急情况（如安全零天事件）时执行此操作。
- 旨在保留客户数据，但 VM 冻结或重启的风险很小。
- Lsv2 本地存储数据可能会丢失，因此建议在事件之前备份数据。

对于即将发生的任何服务事件，请使用受控维护过程为更新选择最方便的时间。 在事件发生之前，可以在高级存储中备份数据。 维护事件完成后，可以将数据返回到刷新后的 Lsv2 Vm 本地 NVMe 存储。

在本地 NVMe 磁盘上维护数据的方案包括：

- VM 正在运行且运行正常。
- VM 将就地重新启动（由你或 Azure）。
- VM 已暂停（停止，无需解除分配）。
- 大部分计划内维护服务操作。

安全擦除数据以保护客户的方案包括：

- 重新部署、停止（释放）或删除 VM。
- VM 将变得不正常，并且由于硬件问题，必须将其维修到另一个节点。
- 需要将 VM 重新分配到另一台主机进行维护的少量计划维护服务操作。

若要了解有关在本地存储中备份数据的选项的详细信息，请参阅[Azure IaaS 磁盘的备份和灾难恢复](backup-and-disaster-recovery-for-azure-iaas-disks.md)。

## <a name="frequently-asked-questions"></a>常见问题

* **如何实现开始部署 Lsv2 系列 Vm？**  
   与任何其他 VM 一样，使用[门户](quick-create-portal.md)、 [Azure CLI](quick-create-cli.md)或[PowerShell](quick-create-powershell.md)来创建 vm。

* **单个 NVMe 磁盘故障是否会导致主机上的所有 Vm 都出现故障？**  
   如果在硬件节点上检测到磁盘故障，则硬件处于故障状态。 出现这种情况时，将自动取消分配节点上的所有 Vm，并将其移动到正常节点。 对于 Lsv2 系列 Vm，这意味着在发生故障的节点上，客户的数据也将被安全地清除，并需要由客户在新节点上重新创建。 如前所述，在实时迁移在 Lsv2 上可用时，失败节点上的数据将在 Vm 传输到另一个节点时主动与 Vm 一起移动。

* **是否需要对 rq_affinity 进行调整以提高性能？**  
   当使用绝对最大每秒输入/输出操作数（IOPS）时，rq_affinity 设置是次要调整。 如果一切正常，请尝试将 rq_affinity 设置为0，查看其是否有差异。

* **是否需要更改 blk_mq 设置？**  
   RHEL/CentOS 7、windows 对于 NVMe 设备自动使用 blk。 无需更改配置或设置。 Scsi_mod use_blk_mq 设置仅适用于 SCSI，在 Lsv2 预览版中使用，因为 NVMe 设备在来宾 Vm 中作为 SCSI 设备可见。 目前，NVMe 设备可作为 NVMe 设备显示，因此，SCSI blk-mq 设置是不相关的。

* **是否需要更改 "fio"？**  
   若要获取性能测量工具的最大 IOPS （如 L64v2 和 L80v2 VM 大小中的 "fio"），请在每个 NVMe 设备上将 "rq_affinity" 设置为0。  例如，对于 L80v2 VM 中的所有10个 NVMe 设备，此命令行都将 "rq_affinity" 设置为零：

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   另请注意，在未分区、没有文件系统、无 RAID 0 配置等的每个原始 NVMe 设备上执行 i/o 操作时，会获得最佳性能。在开始测试会话之前，通过在每个 NVMe 设备上运行 `blkdiscard`，确保配置处于已知的 "最新/干净" 状态。
   
## <a name="next-steps"></a>后续步骤

* 请参阅在 Azure 上为[存储性能优化的所有 vm](sizes-storage.md)的规范
