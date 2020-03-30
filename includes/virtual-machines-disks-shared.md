---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a14ae76e15c1adb59917e61fbcbdaa34a7efa2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77472004"
---
Azure 共享磁盘（预览）是 Azure 托管磁盘的新功能，它允许同时将 Azure 托管磁盘附加到多个虚拟机 （VM）。 通过将托管磁盘附加到多个 VM，可以部署新的群集应用程序或将现有群集应用程序迁移到 Azure。

## <a name="how-it-works"></a>工作原理

群集中的 VM 可以根据群集应用程序使用[SCSI 持久保留 （SCSI](https://www.t10.org/members/w_spc3.htm) PR） 选择的保留读取或写入您的连接磁盘。 SCSI PR 是一个众所周知的行业标准，由本地存储区域网络 （SAN） 上运行的应用程序利用。 在托管磁盘上启用 SCSI PR 允许您将这些应用程序迁移到 Azure。

启用共享磁盘的托管磁盘提供可由多个 VM 访问的共享块存储，这公开为逻辑单元号 （LUN）。 然后，LUN 从目标（磁盘）呈现给启动器 （VM）。 这些 LUN 看起来像直接连接存储 （DAS） 或 VM 的本地驱动器。

启用了共享磁盘的托管磁盘在本机上不会提供可以使用 SMB/NFS 访问的完全托管文件系统。 您需要使用群集管理器，如 Windows 服务器故障转移群集 （WSFC） 或起搏器，该群集处理群集节点通信和写入锁定。

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

## <a name="persistent-reservation-flow"></a>持久预留流

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