---
title: "文件级别的 SAP HANA Azure 备份 | Microsoft Docs"
description: "对于 Azure 虚拟机上的 SAP HANA，可以采用两种可行的主要备份方法，本文介绍文件级别的 SAP HANA 备份"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: 5db0ceb1648b5afa278e1cbe1c42fce8033bfdc1
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="sap-hana-azure-backup-on-file-level"></a>文件级别的 SAP HANA Azure 备份

## <a name="introduction"></a>介绍

本文是有关 SAP HANA 备份的系列教程所包含的三篇文章中的一篇。 [Azure 虚拟机上的 SAP HANA 备份指南](./sap-hana-backup-guide.md)提供概述和入门信息，[基于存储快照的 SAP HANA Azure 备份](./sap-hana-backup-storage-snapshots.md)介绍基于存储快照的备份选项。

查看 Azure VM 大小时，我们可以看到，GS5 允许 64 个附加的数据磁盘。 在大型 SAP HANA 系统中，可能已经为数据和日志文件准备了大量的磁盘，这些磁盘可能与软件 RAID 相结合，目的是提供最佳的磁盘 IO 吞吐量。 那么问题来了，要将 SAP HANA 备份文件存储在哪个位置，以防在一段时间后这些文件填满附加的数据磁盘？ 有关 Azure VM 大小的表格，请参阅 [Azure 中 Linux 虚拟机的大小](../../linux/sizes.md)。

目前，SAP HANA 备份与 Azure 备份服务之间不存在任何形式的集成。 管理文件级备份/还原的标准方法是通过 SAP HANA Studio 或 SAP HANA SQL 语句使用基于文件的备份。 有关详细信息，请参阅 [SAP HANA SQL 和系统视图参考](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf)。

![此图显示 SAP HANA Studio 中备份菜单项的对话框](media/sap-hana-backup-file-level/image022.png)

此图显示 SAP HANA Studio 中备份菜单项的对话框。 选择&quot;文件&quot;作为类型时，必须在文件系统中指定 SAP HANA 要将备份文件写入到的路径。 还原的工作方式与此相同。

尽管此选项看上去简单而直接，但仍然需要注意一些事项。 如前所述，Azure VM 上可附加的数据磁盘数有限制。 根据数据库和磁盘吞吐量的要求，VM 文件系统上可能没有足够的容量用于存储 SAP HANA 备份文件，因此，可能需要使用软件 RAID 来跨多个数据磁盘进行条带化。 本文稍后会提供用于移动这些备份文件，以及在处理 TB 量级的数据时管理文件大小限制和性能的选项。

在总容量方面能够提供更高自由度的另一个选项是 Azure Blob 存储。 尽管单个 Blob 的容量也限制为 1 TB，但是，单个 Blob 容器的总容量目前可以达到 500 TB。 此外，该选项可让客户选择性价比较高的所谓&quot;冷&quot; Blob 存储。 有关冷 Blob 存储的详细信息，请参阅 [Azure Blob 存储：热存储层和冷存储层](../../../storage/blobs/storage-blob-storage-tiers.md)。

为进一步提高安全性，可以使用异地复制的存储帐户来存储 SAP HANA 备份。 有关存储帐户复制的详细信息，请参阅 [Azure 存储复制](../../../storage/common/storage-redundancy.md)。

可将专门用于 SAP HANA 备份的 VHD 放在异地复制的专用备份存储帐户中。 或者，可将保存 SAP HANA 备份的 VHD 复制到异地复制的存储帐户或位于不同区域的存储帐户。

## <a name="azure-backup-agent"></a>Azure 备份代理

Azure 备份提供的选项不仅可以备份整个 VM，而且还能通过备份代理（必须在来宾 OS 上安装）备份文件和目录。 但是截止 2016 年 12 月，仅支持在 Windows 上使用此代理（请参阅[使用 Resource Manager 部署模型将 Windows Server 或客户端备份到 Azure](../../../backup/backup-configure-vault.md)）。

一种解决方法是先将 SAP HANA 备份文件复制到 Azure 上的 Windows VM（例如通过 SAMBA 共享），然后从该 VM 使用 Azure 备份代理。 尽管这种方法在技术上是可行的，但由于要在 Linux 和 Windows VM 之间复制，因此复杂性会增大，同时会大大减慢备份或还原过程的速度。 我们不建议采用此方法。

## <a name="azure-blobxfer-utility-details"></a>Azure blobxfer 实用工具详细信息

若要在 Azure 存储中存储目录和文件，可以使用 CLI 或 PowerShell，或者使用某个 [Azure SDK](https://azure.microsoft.com/downloads/) 开发一个工具。 此外，还可以使用即时可用的实用工具 AzCopy 将数据复制到 Azure 存储，但只能在 Windows 上执行此操作（请参阅[使用 AzCopy 命令行实用工具传输数据](../../../storage/common/storage-use-azcopy.md)）。

因此，我们使用了 blobxfer 来复制 SAP HANA 备份文件。 blobxfer 是许多客户在生产环境中使用的开源工具，可在 [GitHub](https://github.com/Azure/blobxfer) 上获取。 使用此工具可将数据直接复制到 Azure Blob 存储或 Azure 文件共享。 它还提供多种有用功能，例如 md5 哈希，或者在复制包含多个文件的目录时自动调整并行度。

## <a name="sap-hana-backup-performance"></a>SAP HANA 备份性能

![这是 SAP HANA Studio 中 SAP HANA 备份控制台的屏幕截图](media/sap-hana-backup-file-level/image023.png)

这是 SAP HANA Studio 中 SAP HANA 备份控制台的屏幕截图。 在附加到使用 XFS 文件系统的 HANA VM 的单个 Azure 标准存储磁盘上，执行 230 GB 的备份花费了大约 42 分钟时间。

![这是 SAP HANA 测试 VM 中 YaST 的屏幕截图](media/sap-hana-backup-file-level/image024.png)

这是 SAP HANA 测试 VM 中 YaST 的屏幕截图。 可以看到，这里使用了单个 1-TB 磁盘用于存储前面所述的 SAP HANA 备份。 备份 230 GB 数据花费了大约 42 分钟时间。 此外，已附加五个 200-GB 磁盘并创建了软件 RAID md0，这五个 Azure 数据磁盘的顶层使用了条带化。

![在跨五个附加的 Azure 标准存储数据磁盘条带化的软件 RAID 上重复相同的备份](media/sap-hana-backup-file-level/image025.png)

在跨五个附加的 Azure 标准存储数据磁盘条带化的软件 RAID 上重复相同的备份时，备份时间从 42 分钟下降到 10 分钟。 附加的磁盘未缓存到 VM。 因此，明显地可以看出磁盘写入吞吐量对于备份时间的重要性。 然后，用户可以切换到 Azure 高级存储，进一步加快该过程，获得最佳性能。 一般情况下，应将 Azure 高级存储用于生产系统。

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>将 SAP HANA 备份文件复制到 Azure Blob 存储

截止 2016 年 12 月，快速存储 SAP HANA 备份文件的最佳选项是 Azure Blob 存储。 单个 Blob 容器的容量限制为 500 TB，这足以让 Azure 上的 GS5 VM 中运行的 SAP HANA 系统保存足够多的 SAP HANA 备份。 客户可以在&quot;热&quot;和&quot;冷&quot; Blob 存储之间选择（请参阅 [Azure Blob 存储：热存储层和冷存储层](../../../storage/blobs/storage-blob-storage-tiers.md)）。

使用 blobxfer 工具可以轻松地将 SAP HANA 备份文件直接复制到 Azure Blob 存储。

![下图显示了完整 SAP HANA 文件备份的文件](media/sap-hana-backup-file-level/image026.png)

下图显示了完整 SAP HANA 文件备份的文件。 有四个文件，最大的文件大约有 230 GB。

![将 230 GB 数据复制到 Azure 标准存储帐户 Blob 容器花费了大约 3000 秒](media/sap-hana-backup-file-level/image027.png)

初始测试中未使用 md5 哈希，将 230 GB 数据复制到 Azure 标准存储帐户 Blob 容器花费了大约 3000 秒。

![此屏幕截图显示了该容器在 Azure 门户上的外观](media/sap-hana-backup-file-level/image028.png)

此屏幕截图显示了该容器在 Azure 门户上的外观。 已创建名为 &quot;sap-hana-backups&quot; 的 Blob 容器，其中包含四个表示 SAP HANA 备份文件的 Blob。 其中一个 Blob 的大小约为 230 GB。

使用 HANA Studio 备份控制台可以限制 HANA 备份文件的最大文件大小。 在示例环境中，可以包含多个较小的备份文件而不是包含一个 230-GB 的大文件，因此性能得到提升。

![在 HANA 端设置备份文件大小限制不会改善备份时间](media/sap-hana-backup-file-level/image029.png)

在 HANA 端设置备份文件大小限制不会改善备份时间，因为文件是按顺序写入的，如此图所示。 文件大小限制设置为 60 GB，因此，备份创建了四个大型数据文件，而不是单个 230-GB 的文件。

![为了测试 blobxfer 工具的并行度，已将 HANA 备份的最大文件大小设置为 15 GB](media/sap-hana-backup-file-level/image030.png)

为了测试 blobxfer 工具的并行度，已将 HANA 备份的最大文件大小设置为 15 GB，从而生成了 19 个备份文件。 采用这种配置后，blobxfer 将 230 GB 的数据复制到 Azure Blob 存储所花费的时间从 3000 秒下降到 875 秒。

之所以出现这种结果，是因为写入 Azure Blob 的速度限制为 60 MB/秒。 通过多个 Blob 实现的并行度解决了瓶颈，但也带来了一个弊端：提高 blobxfer 工具将所有这些 HANA 备份文件复制到 Azure Blob 存储的性能会在 HANA VM 和网络中施加负载。 因此，HANA 系统的操作会受到影响。

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>备份软件 RAID 中专用 Azure 数据磁盘的 Blob 副本

与手动 VM 数据磁盘备份不同，此方法不是通过备份 VM 上的所有数据磁盘来保存整个 SAP 安装，包括 HANA 数据、HANA 日志文件和配置文件。 其思路是使用跨多个 Azure 数据 VHD 条带化的专用软件 RAID 来存储完整的 SAP HANA 文件备份。 用户只需复制这些包含 SAP HANA 备份的磁盘。 可以轻松地将这些磁盘保存在专用的 HANA 备份存储帐户中，或者附加到专用&quot;备份管理 VM&quot;做进一步处理。

![已使用 **start-azurestorageblobcopy** PowerShell 命令复制所有相关的 VHD](media/sap-hana-backup-file-level/image031.png)

备份到本地软件 RAID 的过程完成后，已使用 **start-azurestorageblobcopy** PowerShell 命令复制所有相关的 VHD（请参阅 [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy)）。 此命令只影响用于保存备份文件的专用文件系统，因此，我们无需担心磁盘上 SAP HANA 数据或日志文件的一致性。 此命令的优点是可以在 VM 保持联机时运行。 为确保不会有任何进程向备份带区集写入数据，请务必在执行 blob copy 之前卸载带区集，事后再次装入。 或者，可以采用适当的方式来&quot;冻结&quot;文件系统。 例如，对 XFS 文件系统运行 xfs\_freeze。

![此屏幕截图显示 Azure 门户上 vhds 容器中的 Blob 列表](media/sap-hana-backup-file-level/image032.png)

此屏幕截图显示 Azure 门户上 &quot;vhds&quot; 容器中的 Blob 列表。 该屏幕截图显示了五个 VHD，它们已附加到 SAP HANA 服务器 VM，充当用于保存 SAP HANA 备份文件的软件 RAID。 屏幕截图中还显示了通过 blob copy 命令创建的五个副本。

![为了测试，已将 SAP HANA 备份软件 RAID 磁盘的副本附加到应用服务器 VM](media/sap-hana-backup-file-level/image033.png)

为了测试，已将 SAP HANA 备份软件 RAID 磁盘的副本附加到应用服务器 VM。

![为了附加磁盘副本，已关闭应用服务器 VM](media/sap-hana-backup-file-level/image034.png)

为了附加磁盘副本，已关闭应用服务器 VM。 启动 VM 后，已正常发现磁盘和 RAID（通过 UUID 装入）。 只缺失了通过 YaST 分区程序创建的装入点。 随后，SAP HANA 备份文件副本在 OS 级别可见。

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>将 SAP HANA 备份文件复制到 NFS 共享

从性能或磁盘空间的角度看，为了减小对 SAP HANA 系统的潜在影响，可以考虑在 NFS 共享中存储 SAP HANA 备份文件。 这种做法在技术上是可行的，但意味着要使用另一个 Azure VM 作为 NFS 共享的主机。 由于 VM 网络带宽方面的原因，该 VM 不应太小。 有效的做法是暂时关闭&quot;备份 VM&quot;，只有在执行 SAP HANA 备份时才将它打开。 在 NFS 共享中写入数据会在网络中施加负载，影响 SAP HANA 系统，但是，以后只在&quot;备份 VM&quot; 中管理备份文件则不会对 SAP HANA 系统造成任何影响。

![已将另一个 Azure VM 中的 NFS 共享装入 SAP HANA 服务器 VM](media/sap-hana-backup-file-level/image035.png)

为了验证 NFS 用例，已将另一个 Azure VM 中的 NFS 共享装入 SAP HANA 服务器 VM。 未应用任何特殊的 NFS 优化。

![直接执行备份花费了 1 小时 46 分钟](media/sap-hana-backup-file-level/image036.png)

NFS 共享是一个高速带区集，与 SAP HANA 服务器上的带区集类似。 尽管如此，在写入本地带区集时，在 NFS 共享中执行直接备份还是花费了 1 小时 46 分钟，而不是 10 分钟。

![替代方法花费了 1 小时 43 分钟，并没有快很多](media/sap-hana-backup-file-level/image037.png)

在 OS 级别备份到本地带区集和复制到 NFS 共享的替代方法（使用一个简单的 **cp -avr** 命令）并没有快很多， 花费了 1 小时 43 分钟。

因此，这种方法可正常工作，但在 230-GB 备份测试中并未体现出良好的性能。 如果备份几个 TB 的数据，性能还会更差。

## <a name="copy-sap-hana-backup-files-to-azure-file-service"></a>将 SAP HANA 备份文件复制到 Azure 文件服务

可以在在 Azure Linux VM 内部装入 Azure 文件共享。 [如何通过 Linux 使用 Azure 文件存储](../../../storage/files/storage-how-to-use-files-linux.md)一文提供了有关如何执行此操作的详细信息。 请记住，一个 Azure 文件共享目前有 5-TB 的配额限制，每个文件的大小限制为 1 TB。 有关存储限制的信息，请参阅 [Azure 存储的可伸缩性和性能目标](../../../storage/common/storage-scalability-targets.md)。

但是测试表明，SAP HANA 备份目前无法直接与此类 CIFS 装入配合工作。 [SAP 说明 1820529](https://launchpad.support.sap.com/#/notes/1820529) 中也提到不建议使用 CIFS。

![此图显示 SAP HANA Studio 的备份对话框中的错误](media/sap-hana-backup-file-level/image038.png)

此图显示尝试直接备份到 CIFS 装入的 Azure 文件共享时，SAP HANA Studio 的备份对话框中出现的错误。 因此，必须先在 VM 文件系统中执行标准 SAP HANA 备份，然后将备份文件从 VM 文件系统复制到 Azure 文件服务。

![此图显示，复制 19 个 SAP HANA 备份文件花费了大约 929 秒](media/sap-hana-backup-file-level/image039.png)

此图显示，将 19 个总大小约为 230 GB 的 SAP HANA 备份文件复制到 Azure 文件共享共费了大约 929 秒。

![SAP HANA VM 上的源目录结构已复制到 Azure 文件共享](media/sap-hana-backup-file-level/image040.png)

在此屏幕截图中可以看到，SAP HANA VM 上的源目录结构已复制到 Azure 文件共享：一个目录 (hana\_backup\_fsl\_15gb) 和 19 个独立的备份文件。

以后，当 SAP HANA 文件备份直接支持在 Azure 文件中存储 SAP HANA 备份文件时，这种做法可能很有效。 或者，当可以通过 NFS 装入 Azure 文件，并且最大配额限制远高于 5 TB 时，这种做法也可能很有效。

## <a name="next-steps"></a>后续步骤
* [Azure 虚拟机上的 SAP HANA 备份指南](sap-hana-backup-guide.md)提供了概述和入门信息。
* [基于存储快照的 SAP HANA 备份](sap-hana-backup-storage-snapshots.md)介绍了基于存储快照的备份选项。
* 若要了解如何建立高可用性以及针对 Azure 上的 SAP HANA（大型实例）规划灾难恢复，请参阅 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)。
