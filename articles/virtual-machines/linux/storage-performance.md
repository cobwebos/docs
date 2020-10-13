---
title: 优化 Azure Lsv2 系列虚拟机上的性能 - 存储
description: 了解如何使用 Linux 示例为 Lsv2 系列虚拟机上的解决方案优化性能。
services: virtual-machines-linux
author: laurenhughes
ms.service: virtual-machines-linux
ms-subservice: sizes
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/05/2019
ms.author: joelpell
ms.openlocfilehash: 650164556223a73a722bc91ecb31491ee98cb8a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91307095"
---
# <a name="optimize-performance-on-the-lsv2-series-linux-virtual-machines"></a>优化 Lsv2 系列 Linux 虚拟机上的性能

Lsv2 系列虚拟机支持需要在各种应用与行业的本地存储中提供高 I/O 和吞吐量的各种工作负荷。  Lsv2 系列非常适用于大数据、SQL、NoSQL 数据库、数据仓库，以及大型事务数据库（包括 Cassandra、MongoDB、Cloudera 和 Redis）。

Lsv2 系列虚拟机 (VM) 的设计发挥了 AMD EPYC™ 7551 处理器的最大价值，使其能够在处理器、内存、NVMe 设备和 VM 之间提供最佳性能。 通过与 Linux 方面的合作伙伴合作，我们在 Azure 市场中提供了几个针对 Lsv2 系列性能进行了优化的版本，目前有：

- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 8。0
- Debian 9
- Debian 10

本文提供了一些技巧和建议来确保你的工作负荷和应用程序达到 VM 中设计的最高性能。 随着越来越多的 Lsv2 优化映像添加到 Azure 市场，本页上的信息会不断更新。

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™ 芯片组体系结构

Lsv2 系列 VM 使用基于 Zen 微体系结构的 AMD EYPC™ 服务器处理器。 AMD 开发了 Infinity Fabric (IF) for EYPC™ 作为其 NUMA 模型的可缩放互连结构，可用于晶片上通信、封装上通信和多封装通信。 与 Intel 新式单晶片处理器上使用的 QPI（快速路径互连）和 UPI（超路径互连）相比，AMD 的多 NUMA 小晶片体系结构既可以带来性能优势，同时也会带来挑战。 内存带宽和延迟约束的实际影响因运行的工作负荷类型而异。

## <a name="tips-to-maximize-performance"></a>最大程度地提高性能的技巧

* 如果要为工作负荷上传自定义的 Linux GuestOS，请注意，默认情况下，加速网络将处于**关闭**状态。 如果打算启用加速网络以获得最佳性能，请在创建 VM 时启用它。

* 为 Lsv2 系列 VM 提供支持的硬件利用了具有八个 I/O 队列对 (QP) 的 NVMe 设备。 每个 NVMe 设备 I/O 队列实际上是一个对：一个提交队列和一个完成队列。 NVMe 驱动程序在设置上可以按轮循机制计划分配 I/O，以此优化这八个 I/O QP 的利用。 若要获得最大性能，请在每个设备上运行八个作业以进行匹配。

* 在工作负荷处于活动状态期间，避免将 NVMe 管理命令（例如 NVMe SMART 信息查询等）与 NVMe I/O 命令混合使用。 Lsv2 NVMe 设备以 Hyper-v NVMe Direct 技术为后盾，每当有任何 NVMe 管理命令处于挂起状态时，该技术就会切换到“慢速模式”。 如果发生这种情况，Lsv2 用户可能会发现 NVMe I/O 性能显著下降。

* Lsv2 用户不应依赖于从 VM 内部报告的数据驱动器设备 NUMA 信息（全部为 0）来确定其应用的 NUMA 关联性。 若要获得更好的性能，建议的方法是尽可能在多个 CPU 之间分散工作负荷。

* Lsv2 VM NVMe 设备的每个 I/O 队列对支持的最大队列深度为 1024（相比之下，Amazon i3 QD 限制为 32 个）。 Lsv2 用户应将其（合成）基准工作负荷限制为队列深度 1024 或更低，以免触发“队列已满”条件，从而降低性能。

## <a name="utilizing-local-nvme-storage"></a>利用本地 NVMe 存储

所有 Lsv2 VM 上的 1.92 TB NVMe 磁盘中的本地存储是临时性的。 在以标准方式成功重启 VM 期间，本地 NVMe 磁盘上的数据将会保存。 如果重新部署、解除分配或删除 VM，则不会在 NVMe 上保存数据。 如果其他问题导致 VM 或运行 VM 的硬件变得不正常，则不会保存数据。 发生这种情况时，将会安全擦除旧主机上的所有数据。

还存在需要将 VM 转移到其他主机的情况（例如，在计划内维护操作期间这样做）。 在发生[计划事件](scheduled-events.md)期间，预期会出现计划内维护操作失败和硬件故障的情况。 应使用计划事件来随时了解任何预测性维护和恢复操作的最新情况。

如果计划内维护事件要求在具有空本地磁盘的新主机上重新创建 VM，则需要重新同步数据（同样，会安全擦除旧主机上的所有数据）。 之所以发生这种情况，是因为 Lsv2 系列 VM 目前不支持在本地 NVMe 磁盘上进行实时迁移。

计划内维护有两种模式。

### <a name="standard-vm-customer-controlled-maintenance"></a>VM 客户控制的标准维护

- VM 将在 30 天期限内转移到更新的主机。
- Lsv2 本地存储数据可能会丢失，因此建议在发生该事件之前备份数据。

### <a name="automatic-maintenance"></a>自动维护

- 如果客户不执行自己控制的维护，或者需要执行应急过程（例如零日安全事件），则会发生此事件。
- 旨在保留客户数据，但存在 VM 冻结或重启的风险（可能性很小）。
- Lsv2 本地存储数据可能会丢失，因此建议在发生该事件之前备份数据。

对于即将发生的任何服务事件，请使用受控维护过程，选择最方便的更新时间。 在该事件发生之前，可以在高级存储中备份数据。 维护事件完成后，可将数据返回到已刷新的 Lsv2 VM 本地 NVMe 存储中。

在本地 NVMe 磁盘上维护数据的场景包括：

- VM 正在运行且正常。
- VM 已由你或 Azure 就地重启。
- VM 已暂停（已停止但未解除分配）。
- 大部分计划内维护检修操作。

会安全擦除数据以保护客户的场景包括：

- VM 已由你重新部署、停止（解除分配）或删除。
- VM 由于硬件问题而变得不正常，必须在另一个节点上进行检修和修复。
- 少量的计划内维护检修操作要求将 VM 重新分配到另一台主机进行检修。

若要详细了解用于在本地存储中备份数据的选项，请参阅 [Azure IaaS 磁盘的备份和灾难恢复](../backup-and-disaster-recovery-for-azure-iaas-disks.md)。

## <a name="frequently-asked-questions"></a>常见问题

* **如何开始部署 Lsv2 系列 VM？**  
   可以像创建任何其他 VM 一样，使用[门户](quick-create-portal.md)、[Azure CLI](quick-create-cli.md) 或 [PowerShell](quick-create-powershell.md) 创建此类 VM。

* **单个 NVMe 磁盘发生故障是否会导致主机上的所有 VM 都发生故障？**  
   如果在硬件节点上检测到磁盘故障，意味着该硬件处于故障状态。 出现这种情况时，该节点上的所有 VM 都将解除分配并转移到正常的节点。 对于 Lsv2 系列 VM，这意味着故障节点上的客户数据也会安全擦除，需要由客户在新节点上重新创建。 如前所述，在 Lsv2 上可以进行实时迁移之前，当故障节点转移到另一节点时，其上的数据会连同 VM 一起主动移动。

* **是否需要对 rq_affinity 进行调整以提高性能？**  
   当使用绝对的每秒最大输入/输出操作 (IOPS) 时，rq_affinity 设置是一个微小调整。 如果其他一切正常，请尝试将 rq_affinity 设置为 0，看它是否会造成差异。

* **是否需要更改 blk_mq 设置？**  
   RHEL/CentOS 7、windows 对于 NVMe 设备自动使用 blk。 无需进行任何配置更改或设置。 scsi_mod.use_blk_mq 设置仅适用于 SCSI 并且之前在 Lsv2 预览版中使用，因为那时 NVMe 设备在来宾 VM 中显示为 SCSI 设备。 目前，NVMe 设备显示为 NVMe 设备，因此 SCSI blk-mq 设置无关紧要。

* **是否需要更改 "fio"？**  
   若要获取性能测量工具的最大 IOPS （如 L64v2 和 L80v2 VM 大小中的 "fio"），请在每个 NVMe 设备上将 "rq_affinity" 设置为0。  例如，对于 L80v2 VM 中的所有10个 NVMe 设备，此命令行都将 "rq_affinity" 设置为零：

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   另请注意，直接对具有未分区、无文件系统、无 RAID 0 配置等特点的每个原始 NVMe 设备执行 I/O 操作时，会获得最佳性能。启动测试会话之前，请在每个 NVMe 设备上运行 `blkdiscard`，确保配置处于已知的最新/干净状态。
   
## <a name="next-steps"></a>后续步骤

* 参阅 Azure 上[已针对存储性能进行优化的所有 VM](../sizes-storage.md) 的规格
