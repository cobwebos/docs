---
title: 优化 Azure Lsv2 系列虚拟机的性能 - 存储
description: 了解如何在 Lsv2 系列虚拟机上优化解决方案的性能。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034755"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>优化 Lsv2 系列虚拟机的性能

Lsv2 系列虚拟机支持各种工作负载，这些工作负载需要跨各种应用程序和行业的本地存储实现高 I/O 和吞吐量。  Lsv2 系列是大数据、SQL、NoSQL 数据库、数据仓库和大型事务数据库（包括卡桑德拉、蒙戈DB、Cloudera 和 Redis）的理想选择。

Lsv2 系列虚拟机 （VM） 的设计使 AMD EPYC™ 7551 处理器最大化，从而在处理器、内存、NVMe 设备和 VM 之间提供最佳性能。 与 Linux 中的合作伙伴合作，可以使用多个生成 Azure 应用商店，这些版本针对 Lsv2 系列性能进行了优化，目前包括：

- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 8.0
- Debian 9
- Debian 10

本文提供提示和建议，以确保工作负载和应用程序实现在 VM 中设计的最大性能。 随着更多 Lsv2 优化映像添加到 Azure 应用商店，此页面上的信息将不断更新。

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™芯片组架构

Lsv2 系列 VM 使用基于 Zen 微架构的 AMD EYPC ™服务器处理器。 AMD 为 EYPC 开发了无限交换矩阵 （IF™ 作为其 NUMA 型号的可扩展互连，可用于模上、封装和多包通信。 与英特尔现代单片式处理器中使用的 QPI（快速路径互连）和 UPI（超路径互连）相比，AMD 的多 NUMA 小片式架构既可能带来性能优势，也可能带来挑战。 内存带宽和延迟限制的实际影响可能因运行的工作负载类型而异。

## <a name="tips-to-maximize-performance"></a>最大化性能的提示

* 如果要为工作负载上传自定义 Linux GuestOS，请注意，默认情况下，加速网络将**处于关闭状态**。 如果要启用加速网络，则在创建 VM 时启用它，以实现最佳性能。

* 为 Lsv2 系列 VM 供电的硬件使用具有八个 I/O 队列对 （QP） 的 NVMe 设备。 每个 NVMe 设备 I/O 队列实际上是一对：提交队列和完成队列。 NVMe 驱动程序通过循环计划中分发 I/O 来优化这八个 I/O 季度的利用率。 为了获得最大性能，每个设备运行八个作业以匹配。

* 避免在活动工作负载期间将 NVMe 管理命令（例如，NVMe SMART 信息查询等）与 NVMe I/O 命令混合。 Lsv2 NVMe 设备由 Hyper-V NVMe 直接技术支持，该技术在任何 NVMe 管理命令挂起时都会切换到"慢速模式"。 如果发生这种情况，Lsv2 用户可能会看到 NVMe I/O 性能的显著性能下降。

* Lsv2 用户不应依赖从 VM 中报告的设备 NUMA 信息（所有 0）来处理数据驱动器，以决定其应用的 NUMA 相关性。 如果可能，建议用于提高性能的方法是跨 CPU 扩展工作负载。

* Lsv2 VM NVMe 设备每个 I/O 队列对受支持的最大队列深度为 1024（与 Amazon i3 QD 32 限制）。 Lsv2 用户应将其（综合）基准工作负荷限制为队列深度 1024 或更低，以避免触发队列完全条件，这可能会降低性能。

## <a name="utilizing-local-nvme-storage"></a>利用本地 NVMe 存储

所有 Lsv2 VM 上的 1.92 TB NVMe 磁盘上的本地存储是短暂的。 在 VM 成功标准重新启动期间，本地 NVMe 磁盘上的数据将保持不变。 如果重新部署、取消分配或删除 VM，则该数据将不会保留在 NVMe 上。 如果另一个问题导致 VM 或正在运行的硬件不正常，则数据不会持久化。 发生这种情况时，旧主机上的所有数据将安全地擦除。

有时，VM 需要移动到其他主机，例如，在计划维护操作期间。 计划中的维护操作和一些硬件故障可以预期与[计划事件](scheduled-events.md)。 应使用计划事件来更新任何预测的维护和恢复操作。

如果计划中的维护事件要求在具有空本地磁盘的新主机上重新创建 VM，则需要重新同步数据（同样，旧主机上的任何数据都将安全擦除）。 这是因为 Lsv2 系列 VM 当前不支持本地 NVMe 磁盘上的实时迁移。

有两种模式用于计划维护。

### <a name="standard-vm-customer-controlled-maintenance"></a>标准 VM 客户控制维护

- 在 30 天的时段内，VM 将移动到更新的主机。
- Lsv2 本地存储数据可能会丢失，因此建议在事件发生之前备份数据。

### <a name="automatic-maintenance"></a>自动维护

- 如果客户不执行客户控制的维护，或者在发生紧急程序（如安全零日事件）时发生。
- 旨在保留客户数据，但存在 VM 冻结或重新启动的小风险。
- Lsv2 本地存储数据可能会丢失，因此建议在事件发生之前备份数据。

对于任何即将发生的服务事件，请使用受控维护过程选择最方便的时间进行更新。 在活动之前，您可以将数据备份到高级存储中。 维护事件完成后，您可以将数据返回到刷新的 Lsv2 VR 本地 NVMe 存储。

维护本地 NVMe 磁盘上数据的方案包括：

- VM 正在运行且正常。
- VM 已就地重新启动（由您或 Azure 重新启动）。
- VM 已暂停（未取消分配而停止）。
- 大多数计划的维护维修操作。

安全擦除数据以保护客户的方案包括：

- VM 被重新部署、停止（取消分配）或删除（由您删除）。
- VM 变得不正常，由于硬件问题，必须将服务修复到另一个节点。
- 少数计划维护服务操作，这些操作要求将 VM 重新分配到另一个主机进行维修。

要了解有关在本地存储中备份数据的选项的详细信息，请参阅[Azure IaaS 磁盘的备份和灾难恢复](backup-and-disaster-recovery-for-azure-iaas-disks.md)。

## <a name="frequently-asked-questions"></a>常见问题

* **如何开始部署 Lsv2 系列 VM？**  
   与任何其他 VM 一样，请使用[门户](quick-create-portal.md) [、Azure CLI](quick-create-cli.md)或[PowerShell](quick-create-powershell.md)创建 VM。

* **单个 NVMe 磁盘故障是否会导致主机上的所有 VM 失败？**  
   如果在硬件节点上检测到磁盘故障，则硬件处于故障状态。 发生这种情况时，节点上的所有 VM 将自动取消分配并移动到正常节点。 对于 Lsv2 系列 VM，这意味着客户在故障节点上的数据也会被安全地擦除，并且需要客户在新节点上重新创建。 如前所述，在 Lsv2 上提供实时迁移之前，故障节点上的数据将在 VM 传输到另一个节点时主动移动。

* **我需要对性能rq_affinity进行任何调整吗？**  
   当使用每秒绝对最大输入/输出操作 （IOPS） 时，rq_affinity设置是一个次要调整。 一旦其他一切工作良好，然后尝试将rq_affinity设置为 0，看看是否会有所不同。

* **我需要更改blk_mq设置吗？**  
   RHEL/CentOS 7.x 自动对 NVMe 设备使用 blk-mq。 无需更改配置或设置。 scsi_mod.use_blk_mq 设置仅适用于 SCSI，在 Lsv2 预览期间使用，因为 NVMe 设备在来宾 VM 中作为 SCSI 设备可见。 目前，NVMe 设备作为 NVMe 设备可见，因此 SCSI blk-mq 设置不相关。

* **我需要更改"fio"吗？**  
   要使用 L64v2 和 L80v2 VM 大小中的"fio"等性能测量工具获得最大 IOPS，在每个 NVMe 设备上将"rq_affinity"设置为 0。  例如，对于 L80v2 VM 中的所有 10 个 NVMe 设备，此命令行将"rq_affinity"设置为零：

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   另请注意，当 I/O 直接到每个原始 NVMe 设备时，无需分区、没有文件系统、没有 RAID 0 配置等，则可获得最佳性能。在开始测试会话之前，通过在每个 NVMe 设备上运行`blkdiscard`，确保配置处于已知的新鲜/干净状态。
   
## <a name="next-steps"></a>后续步骤

* 查看针对 Azure 上的[存储性能优化](sizes-storage.md)的所有 VM 的规范
