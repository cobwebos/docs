---
title: 文件级别的 SAP HANA Azure 备份 | Microsoft Docs
description: 对于 Azure 虚拟机上的 SAP HANA，可以采用两种可行的主要备份方法，本文介绍文件级别的 SAP HANA 备份
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: hermannd
ms.openlocfilehash: 9696ca8f2063f7e294628505eeebde363f3521ae
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255458"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>文件级别的 SAP HANA Azure 备份

## <a name="introduction"></a>介绍

本文是有关[Azure 虚拟机上 SAP HANA 的备份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)的相关文章，其中提供了有关 azure 备份服务和存储快照的入门和详细信息。 

Azure 中的不同 VM 类型允许附加不同数量的 VHD。 确切的详细信息记录在 [Azure 中 Linux 虚拟机的大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)中。 对于本文档中所述的测试，我们使用了 GS5 Azure VM，这允许使用64附加的数据磁盘。 在大型 SAP HANA 系统中，可能已经为数据和日志文件准备了大量的磁盘，这些磁盘可能与软件条带化相结合，目的是提供最佳的磁盘 IO 吞吐量。 有关 Azure Vm 上 SAP HANA 部署的建议磁盘配置的详细信息，请参阅[SAP HANA azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)一文。 提供的建议还包括本地备份的磁盘空间建议。

管理文件级备份/还原的标准方法是通过 SAP HANA Studio 或 SAP HANA SQL 语句使用基于文件的备份。 有关详细信息，请参阅[SQL 和系统视图参考 SAP HANA](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf)一文。

![此图显示 SAP HANA Studio 中备份菜单项的对话框](media/sap-hana-backup-file-level/backup-menue-dialog.png)

此图显示 SAP HANA Studio 中备份菜单项的对话框。 选择&quot;文件&quot;作为类型时，必须在文件系统中指定 SAP HANA 要将备份文件写入到的路径。 还原的工作方式与此相同。

尽管此选项看上去简单而直接，但仍然需要注意一些事项。 Azure VM 限制了可附加的数据磁盘数。 根据数据库和磁盘吞吐量的要求，VM 文件系统上可能没有足够的容量用于存储 SAP HANA 备份文件，因此，可能涉及跨多个数据磁盘进行软件条带化。 本文稍后会提供用于移动这些备份文件，以及在处理 TB 量级的数据时管理文件大小限制和性能的选项。

在总容量方面能够提供更高自由度的另一个选项是 Azure Blob 存储。 尽管单个 Blob 的容量也限制为 1 TB，但是，单个 Blob 容器的总容量目前可以达到 500 TB。 此外，该选项可让客户选择性价比较高的所谓&quot;冷&quot; Blob 存储。 有关冷 Blob 存储的详细信息，请参阅[Azure Blob 存储：热、冷和存档访问层](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)。

为进一步提高安全性，可以使用异地复制的存储帐户来存储 SAP HANA 备份。 有关存储冗余和存储复制的详细信息，请参阅[Azure 存储冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)。

可将专门用于 SAP HANA 备份的 VHD 放在异地复制的专用备份存储帐户中。 或者，可将保存 SAP HANA 备份的 VHD 复制到异地复制的存储帐户或位于不同区域的存储帐户。

## <a name="azure-blobxfer-utility-details"></a>Azure blobxfer 实用工具详细信息

若要在 Azure 存储中存储目录和文件，可以使用 CLI 或 PowerShell，或者使用某个 [Azure SDK](https://azure.microsoft.com/downloads/) 开发一个工具。 还有一个可供使用的实用程序，AzCopy，用于将数据复制到 Azure 存储。 （请参阅[通过 AzCopy 命令行实用工具传输数据](../../../storage/common/storage-use-azcopy.md)）。

因此，我们使用了 blobxfer 来复制 SAP HANA 备份文件。 blobxfer 是许多客户在生产环境中使用的开源工具，可在 [GitHub](https://github.com/Azure/blobxfer) 上获取。 使用此工具可将数据直接复制到 Azure Blob 存储或 Azure 文件共享。 它还提供了一系列有用的功能，如 md5 哈希或自动并行复制包含多个文件的目录。

## <a name="sap-hana-backup-performance"></a>SAP HANA 备份性能
本章介绍了性能注意事项。 达到的数量可能不代表最新状态，因为进行稳定的开发以获得更好的 Azure 存储吞吐量。 因此，你应该针对配置和 Azure 区域执行单独的测试。

![这是 SAP HANA Studio 中 SAP HANA 备份控制台的屏幕截图](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

此屏幕截图显示了 SAP HANA Studio 的 SAP HANA 备份控制台。 使用一个磁盘上的 XFS 文件系统在附加到 HANA VM 的单个 Azure 标准 HDD 存储磁盘上执行 230 GB 备份大约需要42分钟。

![这是 SAP HANA 测试 VM 中 YaST 的屏幕截图](media/sap-hana-backup-file-level/one-backup-disk.png)

这是 SAP HANA 测试 VM 中 YaST 的屏幕截图。 可以查看 SAP HANA 备份的 1 TB 单一磁盘。 备份 230 GB 数据花费了大约 42 分钟时间。 此外，已附加五个 200-GB 磁盘并创建了软件 RAID md0，这五个 Azure 数据磁盘的顶层使用了条带化。

![在跨五个附加的 Azure 标准存储数据磁盘条带化的软件 RAID 上重复相同的备份](media/sap-hana-backup-file-level/five-backup-disks.png)

在跨五个附加的 Azure 标准存储数据磁盘条带化的软件 RAID 上重复相同的备份时，备份时间从 42 分钟下降到 10 分钟。 附加的磁盘未缓存到 VM。 此练习将演示磁盘写入吞吐量的重要性，以实现良好的备份时间。 可以切换到 Azure 标准 SSD 存储或 Azure 高级存储，以进一步加快性能。 通常，不建议使用 Azure 标准 HDD 存储，仅用于演示目的。 建议至少使用 Azure 标准 SSD 存储或 Azure 高级存储用于生产系统。

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>将 SAP HANA 备份文件复制到 Azure Blob 存储
提到的性能号、备份持续时间号和复制持续时间数可能不代表 Azure 技术的最新状态。 Microsoft 不断改进 Azure 存储，以提供更高的吞吐量和更低的延迟。 因此，数字仅用于演示目的。 你需要在所选的 Azure 区域中测试你的个人需求，才能通过方法判断最适合你的方法。

快速存储 SAP HANA 备份文件的另一个选项是 Azure Blob 存储。 单个 blob 容器的限制为大约 500 TB （对于 SAP HANA 系统而言，使用 M32ts、M32ls、M64ls 和 GS5 VM 类型的 Azure）可以保持足够的 SAP HANA 备份。 客户可选择 &quot;热&quot; 和 &quot;cold&quot; blob 存储（请参阅[Azure blob 存储： "热"、"冷" 和 "存档" 访问层](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)）。

使用 blobxfer 工具可以轻松地将 SAP HANA 备份文件直接复制到 Azure Blob 存储。

![下图显示了完整 SAP HANA 文件备份的文件](media/sap-hana-backup-file-level/list-of-backups.png)

可以查看完整 SAP HANA 文件备份的文件。 对于这四个文件，最大的文件大小约为 230 GB。

![将 230 GB 数据复制到 Azure 标准存储帐户 Blob 容器花费了大约 3000 秒](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

初始测试中未使用 md5 哈希，将 230 GB 数据复制到 Azure 标准存储帐户 Blob 容器花费了大约 3000 秒。

使用 HANA Studio 备份控制台可以限制 HANA 备份文件的最大文件大小。 在示例环境中，它通过使用多个较小的备份文件而不是一个大型 230 GB 文件来提高性能。

在 HANA 端&#39;设置备份文件大小限制不会缩短备份时间，因为文件按顺序写入。 文件大小限制设置为 60 GB，因此，备份创建了四个大型数据文件，而不是单个 230-GB 的文件。 如果备份目标对 blob 大小的文件大小有限制，则可以使用多个备份文件来备份 HANA 数据库。

![为了测试 blobxfer 工具的并行度，已将 HANA 备份的最大文件大小设置为 15 GB](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

为了测试 blobxfer 工具的并行度，已将 HANA 备份的最大文件大小设置为 15 GB，从而生成了 19 个备份文件。 采用这种配置后，blobxfer 将 230 GB 的数据复制到 Azure Blob 存储所花费的时间从 3000 秒下降到 875 秒。

当你探索将针对本地磁盘执行的备份复制到其他位置（例如 Azure blob 存储）时，请记住，最终并行复制进程使用的带宽是针对你的单个 VM 类型的网络或存储配额的记帐。 因此，你需要根据网络和存储带宽来平衡副本的持续时间，因为 VM 中运行的正常工作负荷是必需的。 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>将 SAP HANA 备份文件复制到 NFS 共享

Microsoft Azure 通过[Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)提供本机 NFS 共享。 可在容量中创建不同的容量来存储和管理备份。 还可以基于 NetApp 的技术来快照这些卷。 Azure NetApp 文件（和）以三个不同的服务级别提供，它们提供不同的存储吞吐量。 有关更多详细信息，请参阅[Azure NetApp 文件的服务级别](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)一文。 可以按照文章[快速入门：设置 Azure NetApp 文件和创建 nfs 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes?tabs=azure-portal)一文中所述，从和创建和装载 nfs 卷。

除了通过和使用 Azure 的本机 NFS 卷外，还可以通过多种方式创建在 Azure 上提供 NFS 共享的部署。 所有这些解决方案都具有自己部署和管理这些解决方案所需的缺点。 这些文章中介绍了其中的某些可能性：

- [SUSE Linux Enterprise Server 上的 Azure Vm 上的 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- [适用于 SAP NetWeaver 的 Red Hat Enterprise Linux 上的 Azure VM 上的 GlusterFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)

通过上述方式创建的 NFS 共享可用于直接执行 HANA 备份，或将对本地磁盘执行的备份复制到这些 NFS 共享。

> [!NOTE]
> SAP HANA 支持 NFS v3 和 NFS v4. x。 不支持将任何其他格式（如 SMB 和 CIFS 文件系统）用于写入 HANA 备份。 另请参阅[SAP 支持说明 #1820529](https://launchpad.support.sap.com/#/notes/1820529)

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>将 SAP HANA 备份文件复制到 Azure 文件

可在 Azure Linux VM 内部装入 Azure 文件共享。 [如何将 Azure 文件存储与 Linux 配合使用](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux)一文提供了有关如何执行此配置的详细信息。 有关 Azure 文件或 Azure 高级文件的限制，请参阅[Azure 文件可伸缩性和性能目标](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets)一文。

> [!NOTE]
> SAP HANA 为写入 HANA 备份，不支持具有 CIFS 文件系统的 SMB。 另请参阅[SAP 支持说明 #1820529](https://launchpad.support.sap.com/#/notes/1820529)。 因此，你只能将此解决方案用作已直接针对本地附加磁盘进行的 HANA 数据库备份的最终目标
> 

在针对 Azure 文件（而不是 Azure 高级文件）进行的测试中，将19个备份文件复制19个备份文件的总容量均为 230 GB，这大约需要929秒。 我们期待使用 Azure 高级文件的时间更好。 但是，您需要记住，您需要根据工作负荷对网络带宽的要求来平衡快速副本的兴趣。 由于每个 Azure VM 类型都会强制实施网络带宽配额，因此你需要将工作负荷和备份文件的副本保留在该配额范围内。

![此图显示，复制 19 个 SAP HANA 备份文件花费了大约 929 秒](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


将 SAP HANA 备份文件存储在 Azure 文件上可能是一个有趣的选项。 尤其是增加了 Azure 高级文件的延迟和吞吐量。

## <a name="next-steps"></a>后续步骤
* [Azure 虚拟机上的 SAP HANA 备份指南](sap-hana-backup-guide.md)提供了概述和入门信息。
* 若要了解如何建立高可用性以及针对 Azure 上的 SAP HANA（大型实例）规划灾难恢复，请参阅 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)。
