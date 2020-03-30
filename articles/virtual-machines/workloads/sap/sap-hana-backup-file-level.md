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
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 93b67936166eb73db5e9a15db42c2c6135794108
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271389"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>文件级别的 SAP HANA Azure 备份

## <a name="introduction"></a>介绍

本文是有关[Azure 虚拟机上的 SAP HANA 备份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)的相关文章，它提供了有关入门的概述和信息，以及有关 Azure 备份服务和存储快照的更多详细信息。 

Azure 中的不同 VM 类型允许附加不同数量的 VHD。 确切的详细信息记录在 [Azure 中 Linux 虚拟机的大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)中。 对于本文档中提到的测试，我们使用 GS5 Azure VM，它允许 64 个附加的数据磁盘。 在大型 SAP HANA 系统中，可能已经为数据和日志文件准备了大量的磁盘，这些磁盘可能与软件条带化相结合，目的是提供最佳的磁盘 IO 吞吐量。 有关 Azure VM 上 SAP HANA 部署建议磁盘配置的更多详细信息，请阅读文章[SAP HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)。 提供的建议还包括本地备份的磁盘空间建议。

管理文件级备份/还原的标准方法是通过 SAP HANA Studio 或 SAP HANA SQL 语句使用基于文件的备份。 有关详细信息，请阅读文章 SAP [HANA SQL 和系统视图参考](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf)。

![此图显示 SAP HANA Studio 中备份菜单项的对话框](media/sap-hana-backup-file-level/backup-menue-dialog.png)

此图显示 SAP HANA Studio 中备份菜单项的对话框。 选择&quot;文件&quot;作为类型时，必须在文件系统中指定 SAP HANA 要将备份文件写入到的路径。 还原的工作方式与此相同。

尽管此选项看上去简单而直接，但仍然需要注意一些事项。 Azure VM 具有可附加的数据磁盘数的限制。 根据数据库和磁盘吞吐量的要求，VM 文件系统上可能没有足够的容量用于存储 SAP HANA 备份文件，因此，可能涉及跨多个数据磁盘进行软件条带化。 本文稍后会提供用于移动这些备份文件，以及在处理 TB 量级的数据时管理文件大小限制和性能的选项。

在总容量方面能够提供更高自由度的另一个选项是 Azure Blob 存储。 尽管单个 Blob 的容量也限制为 1 TB，但是，单个 Blob 容器的总容量目前可以达到 500 TB。 此外，该选项可让客户选择性价比较高的所谓&quot;冷&quot; Blob 存储。 有关冷 Blob 存储的详细信息，请参阅[Azure Blob 存储：热、酷和存档访问层](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)。

为进一步提高安全性，可以使用异地复制的存储帐户来存储 SAP HANA 备份。 有关存储冗余和存储复制的详细信息，请参阅[Azure 存储冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)。

可将专门用于 SAP HANA 备份的 VHD 放在异地复制的专用备份存储帐户中。 或者，可将保存 SAP HANA 备份的 VHD 复制到异地复制的存储帐户或位于不同区域的存储帐户。

## <a name="azure-blobxfer-utility-details"></a>Azure blobxfer 实用工具详细信息

若要在 Azure 存储中存储目录和文件，可以使用 CLI 或 PowerShell，或者使用某个 [Azure SDK](https://azure.microsoft.com/downloads/) 开发一个工具。 还有一个现成的实用程序 AzCopy，用于将数据复制到 Azure 存储。 （请参阅[使用 AzCopy 命令行实用程序传输数据](../../../storage/common/storage-use-azcopy.md)）。

因此，我们使用了 blobxfer 来复制 SAP HANA 备份文件。 blobxfer 是许多客户在生产环境中使用的开源工具，可在 [GitHub](https://github.com/Azure/blobxfer) 上获取。 使用此工具可将数据直接复制到 Azure Blob 存储或 Azure 文件共享。 它还提供了一系列有用的功能，如 md5 哈希，或复制具有多个文件的目录时的自动并行性。

## <a name="sap-hana-backup-performance"></a>SAP HANA 备份性能
本章将讨论性能注意事项。 实现的数字可能并不代表最新状态，因为有稳定的开发来实现更好的 Azure 存储吞吐量。 因此，应对配置和 Azure 区域执行单独的测试。

![这是 SAP HANA Studio 中 SAP HANA 备份控制台的屏幕截图](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

此屏幕截图显示了 SAP HANA 工作室的 SAP HANA 备份控制台。 使用一个磁盘上的 XFS 文件系统，在连接到 HANA VM 的单个 Azure 标准 HDD 存储磁盘上执行 230 GB 的备份大约需要 42 分钟。

![这是 SAP HANA 测试 VM 中 YaST 的屏幕截图](media/sap-hana-backup-file-level/one-backup-disk.png)

这是 SAP HANA 测试 VM 中 YaST 的屏幕截图。 您可以看到用于 SAP HANA 备份的 1 TB 单个磁盘。 备份 230 GB 数据花费了大约 42 分钟时间。 此外，已附加五个 200-GB 磁盘并创建了软件 RAID md0，这五个 Azure 数据磁盘的顶层使用了条带化。

![在跨五个附加的 Azure 标准存储数据磁盘条带化的软件 RAID 上重复相同的备份](media/sap-hana-backup-file-level/five-backup-disks.png)

在跨五个附加的 Azure 标准存储数据磁盘条带化的软件 RAID 上重复相同的备份时，备份时间从 42 分钟下降到 10 分钟。 附加的磁盘未缓存到 VM。 本练习演示磁盘写入吞吐量对于实现良好备份时间的重要性。 您可以切换到 Azure 标准 SSD 存储或 Azure 高级存储，以进一步加快该过程以实现最佳性能。 通常，不建议使用 Azure 标准 HDD 存储，并且仅用于演示目的。 建议为生产系统使用最少的 Azure 标准 SSD 存储或 Azure 高级存储。

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>将 SAP HANA 备份文件复制到 Azure Blob 存储
上面提到的性能编号、备份持续时间编号和复制持续时间编号可能并不代表 Azure 技术的最新状态。 Microsoft 正在稳步改进 Azure 存储，以提供更多吞吐量和更低的延迟。 因此，这些数字仅用于演示目的。 您需要在您选择的 Azure 区域中测试您的个人需求，以便能够判断使用方法最适合您。

快速存储 SAP HANA 备份文件的另一个选项是 Azure Blob 存储。 单个 Blob 容器的限制约为 500 TB，对于 SAP HANA 系统来说，使用 M32ts、M32ls、M64ls 和 GS5 VM 类型的 Azure，足以保留足够的 SAP HANA 备份。 &quot;客户可以选择&quot;热 blob 存储&quot;和&quot;冷 Blob 存储（请参阅[Azure Blob 存储：热、酷和存档访问层](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)）。

使用 blobxfer 工具可以轻松地将 SAP HANA 备份文件直接复制到 Azure Blob 存储。

![下图显示了完整 SAP HANA 文件备份的文件](media/sap-hana-backup-file-level/list-of-backups.png)

您可以看到完整的 SAP HANA 文件备份的文件。 在四个文件中，最大的文件大约有 230 GB 大小。

![将 230 GB 数据复制到 Azure 标准存储帐户 Blob 容器花费了大约 3000 秒](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

初始测试中未使用 md5 哈希，将 230 GB 数据复制到 Azure 标准存储帐户 Blob 容器花费了大约 3000 秒。

使用 HANA Studio 备份控制台可以限制 HANA 备份文件的最大文件大小。 在示例环境中，它拥有多个较小的备份文件，而不是一个大型的 230 GB 文件，从而提高了性能。

在 HANA 端设置备份文件大小限制不会&#39;缩短备份时间，因为文件是按顺序编写的。 文件大小限制设置为 60 GB，因此，备份创建了四个大型数据文件，而不是单个 230-GB 的文件。 如果您的备份目标对 Blob 大小的文件大小有限制，则使用多个备份文件可能成为备份 HANA 数据库的必要条件。

![为了测试 blobxfer 工具的并行度，已将 HANA 备份的最大文件大小设置为 15 GB](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

为了测试 blobxfer 工具的并行度，已将 HANA 备份的最大文件大小设置为 15 GB，从而生成了 19 个备份文件。 采用这种配置后，blobxfer 将 230 GB 的数据复制到 Azure Blob 存储所花费的时间从 3000 秒下降到 875 秒。

在探索将针对本地磁盘执行的备份复制到其他位置（如 Azure Blob 存储）时，请记住，最终并行复制过程使用的带宽是针对单个 VM 类型的网络或存储配额考虑的。 因此，您需要平衡复制的持续时间与 VM 中运行的正常工作负载所需的网络和存储带宽。 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>将 SAP HANA 备份文件复制到 NFS 共享

微软 Azure 通过 Azure [NetApp 文件](https://azure.microsoft.com/services/netapp/)提供本机 NFS 共享。 您可以创建不同卷的十几个 TB，以存储和管理备份。 您还可以根据 NetApp 的技术对这些卷进行快照。 Azure NetApp 文件 （ANF） 提供三个不同的服务级别，提供不同的存储吞吐量。 有关详细信息，请阅读[Azure NetApp 文件的服务级别](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)。 可以创建和装载从 ANF 的 NFS 卷，如文章["快速入门：设置 Azure NetApp 文件"并创建 NFS 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes?tabs=azure-portal)。

除了通过 ANF 使用 Azure 的本机 NFS 卷外，还有各种可能创建自己的部署，在 Azure 上提供 NFS 共享。 所有这些都有自己部署和管理这些解决方案所需的缺点。 这些文章中记录了其中一些可能性：

- [SUSE Linux Enterprise Server 上 Azure VM 中的 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- [适用于 SAP NetWeaver 的 Red Hat Enterprise Linux 上的 Azure VM 上的 GlusterFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)

通过上述方式创建的 NFS 共享可用于直接针对这些 NFS 共享执行 HANA 备份或将针对本地磁盘执行的备份复制到这些 NFS 共享。

> [!NOTE]
> SAP HANA 支持 NFS v3 和 NFS v4.x。 不支持任何其他格式（如具有 CIFS 文件系统的 SMB）编写 HANA 备份。 另请参阅[SAP 支持说明#1820529](https://launchpad.support.sap.com/#/notes/1820529)

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>将 SAP HANA 备份文件复制到 Azure 文件

可在 Azure Linux VM 内部装入 Azure 文件共享。 文章["如何在 Linux 中使用 Azure 文件存储](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux)"详细介绍了如何执行配置。 有关 Azure 文件或 Azure 高级文件的限制，请阅读文章[Azure 文件可伸缩性和性能目标](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets)。

> [!NOTE]
> SAP HANA 不支持使用 CIFS 文件系统的 SMB 编写 HANA 备份。 另请参阅[SAP 支持说明#1820529](https://launchpad.support.sap.com/#/notes/1820529)。 因此，您只能使用此解决方案作为直接针对本地连接磁盘进行的 HANA 数据库备份的最终目标
> 

在对 Azure 文件（而不是 Azure 高级文件）进行的测试中，复制 19 个备份文件（总容量为 230 GB）需要大约 929 秒。 我们期望使用 Azure 高级文件的时间更好。 但是，您需要记住，您需要在快速复制的兴趣与工作负载对网络带宽的要求之间取得平衡。 由于每种 Azure VM 类型都强制实施网络带宽配额，因此您需要在工作负荷以及备份文件副本的范围内保持该配额的范围。

![此图显示，复制 19 个 SAP HANA 备份文件花费了大约 929 秒](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


在 Azure 文件上存储 SAP HANA 备份文件可能是一个有趣的选项。 特别是随着 Azure 高级文件的延迟和吞吐量的提高。

## <a name="next-steps"></a>后续步骤
* [Azure 虚拟机上的 SAP HANA 备份指南](sap-hana-backup-guide.md)提供了概述和入门信息。
* 若要了解如何建立高可用性以及针对 Azure 上的 SAP HANA（大型实例）规划灾难恢复，请参阅 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)。
