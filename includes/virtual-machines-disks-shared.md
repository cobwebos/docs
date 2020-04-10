---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c3e5beaef7fcc9d407103834e2040957ff32984c
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008500"
---
Azure 共享磁盘（预览）是 Azure 托管磁盘的新功能，它启用同时将托管磁盘附加到多个虚拟机 （VM）。 通过将托管磁盘附加到多个 VM，可以部署新的群集应用程序或将现有群集应用程序迁移到 Azure。

## <a name="how-it-works"></a>工作原理

群集中的 VM 可以根据群集应用程序使用[SCSI 持久保留 （SCSI](https://www.t10.org/members/w_spc3.htm) PR） 选择的保留读取或写入您的连接磁盘。 SCSI PR 是一种行业标准，由本地存储区域网络 （SAN） 上运行的应用程序利用。 在托管磁盘上启用 SCSI PR 允许您将这些应用程序迁移到 Azure。

共享托管磁盘提供可以从多个 VM 访问的共享块存储，这些存储公开为逻辑单元号 （LUN）。 然后，LUN 从目标（磁盘）呈现给启动器 （VM）。 这些 LUN 看起来像直接连接存储 （DAS） 或 VM 的本地驱动器。

共享托管磁盘不提供可以使用 SMB/NFS 访问的完全托管文件系统。 您需要使用群集管理器，如 Windows 服务器故障转移群集 （WSFC） 或起搏器，该群集处理群集节点通信和写入锁定。

## <a name="limitations"></a>限制

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>磁盘大小

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>示例工作负载

### <a name="windows"></a>Windows

大多数基于 Windows 的群集都基于 WSFC 构建，WSFC 处理群集节点通信的所有核心基础结构，允许应用程序利用并行访问模式。 WSFC 支持基于 CSV 和非基于 CSV 的选项，具体取决于您的 Windows 服务器版本。 有关详细信息，请参阅[创建故障转移群集](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster)。

在 WSFC 上运行的一些常用应用程序包括：

- SQL 服务器故障转移群集实例 （FCI）
- 横向扩展文件服务器 （SoFS）
- 一般使用文件服务器（IW 工作负载）
- 远程桌面服务器用户配置文件磁盘 （RDS UPD）
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Linux集群可以利用集群管理器，如[起搏器](https://wiki.clusterlabs.org/wiki/Pacemaker)。 起搏器以[Corosync](http://corosync.github.io/corosync/)为基础，为部署在高可用环境中的应用程序提供集群通信。 一些常见的群集文件系统包括[ocfs2](https://oss.oracle.com/projects/ocfs2/)和[gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2)。 您可以使用[fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html)和[sg_persist](https://linux.die.net/man/8/sg_persist)等实用程序操作预订和注册。

## <a name="persistent-reservation-flow"></a>持久预留流程

下图演示了一个示例 2 节点群集数据库应用程序，该应用程序利用 SCSI PR 启用从一个节点到另一个节点的故障转移。

![两个节点群集。 在群集上运行的应用程序正在处理对磁盘的访问](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

流程如下所示：

1. 在 Azure VM1 和 VM2 上运行的群集应用程序注册其读取或写入磁盘的意图。
1. 然后，VM1 上的应用程序实例需要独占保留才能写入磁盘。
1. 此保留在 Azure 磁盘上强制执行，数据库现在可以专门写入磁盘。 从 VM2 上的应用程序实例的任何写入都不会成功。
1. 如果 VM1 上的应用程序实例出现故障，VM2 上的实例现在可以启动数据库故障转移并接管磁盘。
1. 此保留现在在 Azure 磁盘上强制执行，磁盘将不再接受来自 VM1 的写入。 它仅接受来自 VM2 的写入。
1. 群集应用程序可以完成数据库故障转移并处理来自 VM2 的请求。

下图说明了另一个常见的群集工作负载，该工作负载由多个节点组成，这些节点从磁盘读取数据以运行并行进程，例如机器学习模型的培训。

![四个节点 VM 群集，每个节点注册写入意向，应用程序需要独占保留才能正确处理写入结果](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

流程如下所示：

1. 在所有 VM 上运行的群集应用程序注册读取或写入磁盘的意图。
1. VM1 上的应用程序实例需要一个独占保留来写入磁盘，同时打开从其他 VM 读取磁盘。
1. 此保留在 Azure 磁盘上强制执行。
1. 群集中的所有节点现在可以从磁盘读取。 只有一个节点代表群集中的所有节点将结果写回磁盘。

### <a name="ultra-disks-reservation-flow"></a>超磁盘预留流

超盘提供额外的油门，总共两个油门。 因此，超磁盘预留流可以如前面部分所述工作，也可以更精细地限制和分发性能。

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text=" ":::

## <a name="ultra-disk-performance-throttles"></a>超磁盘性能限制

Ultra 磁盘具有独特的功能，允许您通过公开可修改的属性并允许您修改属性来设置性能。 默认情况下，只有两个可修改的属性，但是，共享超磁盘有两个附加属性。


|特性  |描述  |
|---------|---------|
|磁盘阅读编写     |使用写入访问权限安装共享磁盘的所有 VM 中允许的 IOPS 总数。         |
|磁盘BpsReadWrite     |使用写入访问权限安装共享磁盘的所有 VM 中允许的总吞吐量 （MB/s）。         |
|仅磁盘阅读*     |在所有将共享磁盘安装为 ReadOnly 的 VM 中允许的 IOPS 总数。         |
|仅磁盘BpsRead*     |在将共享磁盘安装为 ReadOnly 的所有 VM 中允许的总吞吐量 （MB/s）。         |

\*仅适用于共享超级磁盘

以下公式解释了如何设置性能属性，因为它们是用户可修改的：

- 磁盘阅读写入/磁盘操作只读： 
    - IOPS 限制为 300 IOPS/GiB，每个磁盘最多 160K IOPS
    - 最少 100 IOPS
    - 磁盘阅读写入 = 磁盘阅读仅是至少 2 IOPS/GiB
- 磁盘Bps读取写入/磁盘BP只读取：
    - 每个预配 IOPS 单个磁盘的吞吐量限制为 256 KiB/s，每个磁盘最多 2000 MBps
    - 每个预配 IOPS 的最小保证吞吐量为 4KiB/s，总体基线最小为 1 MBps

### <a name="examples"></a>示例

以下示例描述了一些方案，这些场景显示了限制如何与共享超级磁盘配合使用，具体操作。

#### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>使用群集共享卷的两个节点群集

下面是使用群集共享卷的 2 节点 WSFC 的示例。 使用此配置，两个 VM 都同时具有对磁盘的写入访问权限，从而导致在两个 VM 上拆分 ReadWrite 限制和不使用 ReadOnly 限制。

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="CSV 两个节点超示例":::

:::image-end:::

#### <a name="two-node-cluster-without-cluster-share-volumes"></a>两个节点群集，没有群集共享卷

下面是不使用群集共享卷的 2 节点 WSFC 的示例。 使用此配置，只有一个 VM 具有对磁盘的写入访问权限。 这将导致仅针对主 VM 使用 ReadWrite 油门，而仅由辅助 VM 使用的 ReadOnly 节流。

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="CSV 两个节点无 csv 超磁盘示例":::

:::image-end:::

#### <a name="four-node-linux-cluster"></a>四个节点 Linux 群集

下面是一个 4 节点 Linux 群集的示例，该群集具有单个编写器和三个横向扩展读取器。 使用此配置，只有一个 VM 具有对磁盘的写入访问权限。 这将导致仅针对主 VM 的 ReadWrite 节流，并且由辅助 VM 拆分的 ReadOnly 节流。

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="四个节点超限制示例":::

:::image-end:::