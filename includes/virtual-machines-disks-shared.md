---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 09e5a6c5eee21e5432c4cf96a63fd2337307954a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211414"
---
Azure 共享磁盘（预览版）是 Azure 托管磁盘的一项新功能，可同时将 Azure 托管磁盘附加到多个虚拟机（Vm）。 通过将托管磁盘附加到多个 Vm，可将现有的群集应用程序部署到 Azure 或将其迁移到 Azure。

## <a name="how-it-works"></a>工作原理

群集中的 Vm 可以根据群集应用程序使用[Scsi 永久保留](https://www.t10.org/members/w_spc3.htm)（scsi PR）选择的保留来读取或写入附加的磁盘。 SCSI PR 是在本地的存储区域网络（SAN）上运行的应用程序使用的一种著名的行业标准。 启用托管磁盘上的 SCSI PR 后，可以按原样将这些应用程序迁移到 Azure。

启用了共享磁盘的托管磁盘提供了可由多个虚拟机访问的共享块存储，这是作为逻辑单元号（Lun）公开的。 然后，会将 Lun 从目标（磁盘）提供给发起程序（VM）。 这些 Lun 看似直接附加存储（DAS）或本地驱动器到 VM。

启用了共享磁盘的托管磁盘并不提供可使用 SMB/NFS 访问的完全托管的文件系统。 你将需要使用群集管理器（如 Windows Server 故障转移群集（WSFC）或 Pacemaker）来处理群集节点通信以及写入锁定。

## <a name="limitations"></a>限制

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>磁盘大小

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>示例工作负荷

### <a name="windows"></a>Windows

大多数基于 Windows 的群集构建于 WSFC 上，后者处理群集节点通信的所有核心基础结构，使应用程序能够利用并行访问模式。 WSFC 根据你的 Windows Server 版本启用 CSV 和非 CSV 的选项。 有关详细信息，请参阅[创建故障转移群集](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster)。

WSFC 上运行的一些热门应用程序包括：

- SQL Server 故障转移群集实例（FCI）
- 横向扩展文件服务器（SoFS）
- 一般用途的文件服务器（IW 工作负荷）
- 远程桌面服务器用户配置文件磁盘（RDS UPD）
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Linux 群集可以利用群集管理器，如[Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker)。 Pacemaker 在[Corosync](http://corosync.github.io/corosync/)上构建，为在高可用性环境中部署的应用程序启用群集通信。 一些常见的群集文件系统包括[ocfs2](https://oss.oracle.com/projects/ocfs2/)和[gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2)。 您可以使用[fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html)和[sg_persist](https://linux.die.net/man/8/sg_persist)等实用工具来处理预留和注册。

## <a name="persistent-reservation-flow"></a>持久保留流

下图演示了一个示例2节点群集数据库应用程序，该应用程序利用 SCSI PR 来实现从一个节点到另一个节点的故障转移。

![双节点群集。 群集上运行的应用程序正在处理对磁盘的访问](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

流程如下所示：

1. 在 Azure VM1 和 VM2 上运行的群集应用程序将注册其目的以便读取或写入磁盘。
1. 然后，VM1 上的应用程序实例将使用独占保留来写入磁盘。
1. 此保留在 Azure 磁盘上强制实施，数据库现在可以独占写入磁盘。 从 VM2 上的应用程序实例进行的任何写入都不会成功。
1. 如果 VM1 上的应用程序实例关闭，则 VM2 上的实例现在可以启动数据库故障转移并接管该磁盘。
1. 现在，此保留在 Azure 磁盘上强制实施，磁盘将不再接受来自 VM1 的写入。 它将只接受来自 VM2 的写入。
1. 群集应用程序可以完成数据库故障转移并提供来自 VM2 的请求。

下图说明了由多个节点从磁盘读取数据以运行并行进程（例如机器学习模型定型）的另一个常见群集工作负载。

![四个节点 VM 群集，每个节点都注册要写入的意图，应用程序使用独占保留来正确处理写入结果](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

流程如下所示：

1. 所有 Vm 上运行的群集应用程序都注册了读取或写入磁盘的意图。
1. VM1 上的应用程序实例需要独占保留才能写入磁盘，同时从其他 Vm 打开到磁盘的读取。
1. 此保留在 Azure 磁盘上强制实施。
1. 群集中的所有节点现在都可以从磁盘中读取。 只有一个节点代表群集中的所有节点将结果写回磁盘。