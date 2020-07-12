---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2589c2abf13edc19b930d597a4d75a2be823f45d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277682"
---
Azure 共享磁盘（预览版）是 Azure 托管磁盘的一项新功能，可同时将托管磁盘附加到多个虚拟机 (VM)。 通过将托管磁盘附加到多个 VM，可以向 Azure 部署新的群集应用程序或迁移现有的群集应用程序。

## <a name="how-it-works"></a>工作原理

群集中的 VM 可以根据群集应用程序使用 [SCSI 永久预留](https://www.t10.org/members/w_spc3.htm) (SCSI PR) 选择的预留来读取或写入附加的磁盘。 SCSI PR 是一种行业标准，可供本地存储区域网络 (SAN) 上运行的应用程序利用。 在托管磁盘上启用 SCSI PR，可以将这些应用程序按原样迁移到 Azure。

共享托管磁盘提供了可以从多个 VM 进行访问的共享块存储，这些存储作为逻辑单元号 (LUN) 公开。 然后，会将 LUN 从目标（磁盘）提供给发起程序 (VM)。 这些 LUN 看起来像直接附加存储 (DAS) 或 VM 的本地驱动器。

共享托管磁盘本身并不提供可以使用 SMB/NFS 访问的完全托管的文件系统。 需要使用群集管理器（如 Windows Server 故障转移群集 (WSFC) 或 Pacemaker）来处理群集节点通信以及写入锁定。

## <a name="limitations"></a>限制

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>磁盘大小

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>示例工作负载

### <a name="windows"></a>Windows

大多数基于 Windows 的群集构建于 WSFC 上，它处理群集节点通信的所有核心基础结构，使应用程序能够利用并行访问模式。 WSFC 根据 Windows Server 的版本启用 CSV 和非 CSV 的选项。 有关详细信息，请参阅[创建故障转移群集](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster)。

WSFC 上运行的热门应用程序包括：

- [在 Azure Vm 上创建 FCI 和 Azure 共享磁盘 (SQL Server) ](../articles/azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md)
- 横向扩展文件服务器 (SoFS)
- 常规用途的文件服务器（IW 工作负载）
- 远程桌面服务器用户配置文件磁盘 (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Linux 群集可以利用群集管理器，例如 [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker)。 Pacemaker 基于 [Corosync](http://corosync.github.io/corosync/) 构建，可为部署在高可用环境中的应用程序启用群集通信。 一些常见的群集文件系统包括 [ocfs2](https://oss.oracle.com/projects/ocfs2/) 和 [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2)。 可以使用 [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) 和 [sg_persist](https://linux.die.net/man/8/sg_persist) 之类的实用程序来处理预留和注册。

#### <a name="ubuntu"></a>Ubuntu

若要了解如何在 Azure 共享磁盘上通过 Corosync 和 Pacemaker 设置 Ubuntu 高可用性，请参阅 [Ubuntu Community Discourse](https://discourse.ubuntu.com/t/ubuntu-high-availability-corosync-pacemaker-shared-disk-environments/14874)。

## <a name="persistent-reservation-flow"></a>永久预留流

下图演示了一个示例 2 节点群集数据库应用程序，该应用程序利用 SCSI PR 启用从一个节点到另一个节点的故障转移。

![双节点群集。 群集上运行的应用程序正在处理对磁盘的访问](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

流程如下所示：

1. 在 Azure VM1 和 VM2 上运行的群集应用程序均注册了其读取或写入磁盘的意图。
1. 然后，VM1 上的应用程序实例将使用独占预留以写入磁盘。
1. 已在 Azure 磁盘上强制执行此预留，并且数据库现在可以独占方式写入磁盘。 从 VM2 上的应用程序实例进行的任何写入都不会成功。
1. 如果 VM1 上的应用程序实例关闭，则 VM2 上的实例现在可以启动数据库故障转移和磁盘接管。
1. 现在，已在 Azure 磁盘上强制执行此预留，并且该磁盘将不再接受来自 VM1 的写入。 它将只接受来自 VM2 的写入。
1. 群集应用程序可以完成数据库故障转移并处理来自 VM2 的请求。

下图说明了另一个常见的群集工作负载，该工作负载由多个节点组成，这些节点从磁盘读取数据以运行并行进程，例如机器学习模型训练。

![四节点 VM 群集，每个节点注册写入意图，应用程序进行独占预留以正确处理写入结果](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

流程如下所示：

1. 所有 VM 上运行的群集应用程序均注册了其读取或写入磁盘的意图。
1. 然后，VM1 上的应用程序实例将使用独占预留以写入磁盘，同时开放其他 VM 的磁盘读取。
1. 将在 Azure 磁盘上强制执行此预留。
1. 群集中的所有节点现在都可以从磁盘中读取。 只有一个节点代表群集中的所有节点将结果写回磁盘。

### <a name="ultra-disks-reservation-flow"></a>超级磁盘预留流

超级磁盘提供附加限制，总共有两个限制。 因此，超级磁盘预留流可以按前面部分所述工作，也可以更精细地限制和分配性能。

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text="描述预留持有者、注册者和其他人的只读或读/写访问权限的表的图像。":::

## <a name="performance-throttles"></a>性能限制

### <a name="premium-ssd-performance-throttles"></a>高级 ssd 性能限制
使用高级 ssd 时，磁盘 IOPS 和吞吐量是固定的，例如 P30 的 IOPS 为5000。 如果磁盘在2个 Vm 或5个 Vm 之间共享，则会保留此值。 可以从单个 VM 或跨越两个或多个 Vm 来访问磁盘限制。 

### <a name="ultra-disk-performance-throttles"></a>超级磁盘性能限制

超级磁盘具有独特的功能，允许你通过公开可修改的属性并允许对其进行修改来设置性能。 默认情况下，只有两个可修改的属性，但共享的超级磁盘具有两个附加属性。


|Attribute  |说明  |
|---------|---------|
|DiskIOPSReadWrite     |所有装载具有写入访问权限的共享磁盘的 VM 所允许的 IOPS 总数。         |
|DiskMBpsReadWrite     |所有装载具有写入访问权限的共享磁盘的 VM 所允许的总吞吐量 (MB/s)。         |
|DiskIOPSReadOnly*     |所有以只读方式装载共享磁盘的 VM 所允许的 IOPS 总数。         |
|DiskMBpsReadOnly*     |所有以只读方式装载共享磁盘的 VM 所允许的总吞吐量 (MB/s)。         |

\* 仅适用于共享的超级磁盘

以下公式说明了如何设置性能属性，因为这些属性是用户可修改的：

- DiskIOPSReadWrite/DiskIOPSReadOnly： 
    - 300 IOPS/GiB 的 IOPS 限制，每个磁盘最高可达 160 K IOPS
    - 最小值为 100 IOPS
    - DiskIOPSReadWrite + DiskIOPSReadOnly 至少为 2 IOPS/GiB
- DiskMBpsRead    Write/DiskMBpsReadOnly：
    - 单个磁盘对应于每个预配 IOPS 的吞吐量限制为 256 KiB/秒，每个磁盘的最大吞吐量为 2000 MBps。
    - 对于每个预配的 IOPS，每个磁盘的最低保证吞吐量为 4KiB/s，总体基线最低为 1 MBps

#### <a name="examples"></a>示例

以下示例描述了一些方案，这些方案具体说明了限制如何作用于共享超级磁盘。

##### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>使用群集共享卷的双节点群集

以下是使用群集共享卷的 2 节点 WSFC 的示例。 使用此配置，两个 VM 都可以同时对磁盘进行写入访问，这将导致 ReadWrite 限制将由两个 VM 共享，且不使用 ReadOnly 限制。

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="CSV 双节点超级性能示例":::

##### <a name="two-node-cluster-without-cluster-share-volumes"></a>无群集共享卷的双节点群集

以下是未使用群集共享卷的 2 节点 WSFC 的示例。 使用此配置时，只有一个 VM 对磁盘具有写入访问权限。 这会导致 ReadWrite 限制专用于主 VM，ReadOnly 限制专用于辅助 VM。

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="CSV 双节点无 csv 超级磁盘示例":::

##### <a name="four-node-linux-cluster"></a>四节点 Linux 群集

下面是具有一个编写器和三个横向扩展读取器的 4 节点 Linux 群集的示例。 使用此配置时，只有一个 VM 对磁盘具有写入访问权限。 这会导致将 ReadWrite 限制专用于主 VM，而 ReadOnly 限制则由辅助 VM 共享。

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="四节点超级限制示例":::
