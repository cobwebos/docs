---
title: "基于 HD 的高性价比标准存储和 Azure VM 磁盘 | Microsoft Docs"
description: "介绍高性价比标准存储以及非托管和托管 VM 磁盘。"
services: storage
documentationcenter: 
author: yuemlu
manager: aungoo-msft
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2017
ms.author: yuemlu
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: 77e48af7ba59ed1e5b2ebcda0760e0eda1f407df
ms.lasthandoff: 04/06/2017


---
# <a name="cost-effective-standard-storage-and-unmanaged-and-managed-azure-vm-disks"></a>高性价比标准存储以及非托管和托管 Azure VM 磁盘

Azure 标准存储为运行不区分延迟的工作负荷提供可靠、低成本的磁盘支持。 它还支持 blob、表、队列和文件。 使用标准存储，将数据存储在硬盘驱动器 (HDD)。 使用 VM 时，可将标准存储磁盘用于开发/测试方案和不太重要的工作负荷，将高级存储磁盘用于任务关键型生产应用程序。 所有 Azure 区域均提供标准存储。 

本文将重点介绍将标准存储用于 VM 磁盘。 有关将存储用于 blob、表、队列和文件的详细信息，请参阅[存储简介](storage-introduction.md)。

## <a name="disk-types"></a>磁盘类型

有两种方法可为 Azure VM 创建标准磁盘：

**非托管磁盘**：这是管理用于存储与 VM 磁盘对应的 VHD 文件的存储帐户的原始方法。 VHD 文件作为页 blob 存储在存储帐户中。 可将非托管磁盘附加到任意大小的 Azure VM，包括主要使用高级存储的 VM，如 DSv2 和 GS 系列。 Azure VM 支持附加多个标准磁盘，每个 VM 最多支持 64 TB 的存储容量。

[**Azure 托管磁盘**](storage-managed-disks-overview.md)：此功能管理用于 VM 磁盘的存储帐户。 你指定所需的类型（高级或标准）和磁盘大小，Azure 将为你创建和管理磁盘。 你不必煞费苦心地将磁盘放置在多个存储帐户中，以确保保持在存储帐户的可伸缩性限制内 - Azure 会为你处理这一切。

尽管这两种类型的磁盘都可用，但是我们建议使用托管磁盘以利用其许多功能。

若要开始使用 Azure 标准存储，请访问[开始免费试用](https://azure.microsoft.com/pricing/free-trial/)。 

有关如何使用托管磁盘创建 VM 的信息，请参阅以下文章之一。

* [使用 Resource Manager 和 PowerShell 创建 VM](../virtual-machines/virtual-machines-windows-ps-create.md)
* [使用 Azure CLI 2.0 创建 Linux VM](../virtual-machines/linux/quick-create-cli.md)

## <a name="standard-storage-features"></a>标准存储功能 

让我们看一下标准存储的一些功能。 有关详细信息，请参阅 [Azure 存储简介](storage-introduction.md)。

**标准存储**：Azure 标准存储支持 Azure 磁盘、Azure Blob、Azure 文件、Azure 表和 Azure 队列。 要使用标准存储服务，请从[创建 Azure 存储帐户](storage-create-storage-account.md#create-a-storage-account)开始。

**标准存储磁盘：**可将标准存储磁盘附加到所有 Azure VM，包括与高级存储配合使用的 VM 系列，如 DSv2 和 GS 系列。 标准存储磁盘只能附加到一个 VM。 但可以将一个或多个此类磁盘附加到 VM，最多为该 VM 大小定义的最大磁盘计数。 在下一部分标准存储的可伸缩性和性能目标中将详细介绍规范。 

**标准页 blob**：标准页 blob 用于保留 VM 的永久磁盘，也可通过 REST 直接访问它（这与其他类型的 Azure Blob 类似）。 [页 blob](/rest/api/storageservices/fileservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) 是 512 字节页的集合，已针对随机读写操作优化。 

**存储复制：**在大多数区域中，标准存储帐户中的数据可以在本地复制，或跨多个数据中心实现异地复制。 可用的四种复制类型包括本地冗余存储 (LRS)、区域冗余存储 (ZRS)、异地冗余存储 (GRS) 和读取访问异地冗余存储 (RA-GRS)。 标准存储中的托管磁盘目前仅支持本地冗余存储 (LRS)。 有关详细信息，请参阅[存储复制](storage-redundancy.md)。

## <a name="scalability-and-performance-targets"></a>可缩放性和性能目标

本部分将介绍使用标准存储时需要考虑的可伸缩性和性能目标。

### <a name="account-limits--does-not-apply-to-managed-disks"></a>帐户限制 - 不适用于托管磁盘

| **资源** | **默认限制** |
|--------------|-------------------|
| 每个存储帐户的 TB  | 500 TB |
| 每个存储帐户的最大入口 <sup>1</sup>（美国区域） | 如果已启用 GRS/ZRS，则为 10 Gbps；对于 LRS，为 20 Gbps |
| 每个存储帐户的最大出口 <sup>1</sup>（美国区域） | 如果已启用 RA-GRS/GRS/ZRS，则为 20 Gbps；对于 LRS，为 30 Gbps |
| 每个存储帐户的最大入口 <sup>1</sup>（欧洲和亚洲区域） | 如果已启用 GRS/ZRS，则为 5 Gbps；对于 LRS，为 10 Gbps |
| 每个存储帐户的最大出口 <sup>1</sup>（欧洲和亚洲区域） | 如果已启用 RA-GRS/GRS/ZRS，则为 10 Gbps；对于 LRS，为 15 Gbps |
| 每个存储帐户的总请求速率（假设对象大小为 1 KB） | 每秒实体或每秒消息数目最高 20,000 IOPS |

<sup>1</sup>传入是指发送到存储帐户的所有数据（请求）。 传出是指从存储帐户接收的所有数据（响应）。

有关详细信息，请参阅 [Azure 存储可伸缩性和性能目标](storage-scalability-targets.md)。

如果应用程序的需求超过了单个存储帐户的可伸缩性目标，则在生成应用程序时请让它使用多个存储帐户，并将数据分布在这些存储帐户中。 或者，可使用 Azure 托管磁盘，Azure 将管理数据分区和放置。

### <a name="standard-disks-limits"></a>标准磁盘限制

与高级磁盘不同，标准磁盘不预配每秒输入/输出操作数 (IOPS) 和吞吐量（带宽）。 标准磁盘的性能因其附加到的 VM 大小（而非磁盘大小）而异。 其性能预期可达到下表所列的性能限制。

**标准磁盘限制（托管和非托管）**

| **VM 层**            | **基本层 VM** | **标准层 VM** |
|------------------------|-------------------|----------------------|
| 最大磁盘大小          | 1023 GB           | 1023 GB              |
| 每个磁盘最大 8 KB IOPS | 最多 300         | 最多 500            |
| 每个磁盘的最大带宽 | 最高 60 MB/秒     | 最高 60 MB/秒        |

如果工作负荷要求高性能、低延迟磁盘支持，则应考虑使用高级存储。 若要深入了解高级存储的优点，请访问[高性能高级存储和 Azure VM 磁盘](storage-premium-storage.md)。 

## <a name="snapshots-and-copy-blob"></a>快照和复制 Blob

对于存储服务而言，VHD 文件是页 blob。 可以拍摄页 blob 的快照，然后将其复制到其他位置，例如其他存储帐户。

### <a name="unmanaged-disks"></a>非托管磁盘

可以为非托管标准磁盘创建[增量快照](storage-incremental-snapshots.md)，这与对标准存储使用快照的方式相同。 如果源磁盘是本地冗余存储帐户，则建议创建快照，并将那些快照复制到地域冗余的标准存储帐户。 有关详细信息，请参阅 [Azure 存储冗余选项](storage-redundancy.md)。

如果磁盘已附加到 VM，则磁盘上将不允许某些 API 操作。 例如，磁盘附加到 VM 后，无法在该 Blob 上执行 [Copy Blob](/rest/api/storageservices/fileservices/Copy-Blob)（复制 Blob）操作。 此时，必须先使用 [Snapshot Blob](/rest/api/storageservices/fileservices/Snapshot-Blob)（快照 Blob）REST API 方法创建该 Blob 的快照，然后对该快照执行 [Copy Blob](/rest/api/storageservices/fileservices/Copy-Blob)（复制 Blob）以复制附加的磁盘。 或者，可以中断附加磁盘，然后执行任何必要的操作。

若要维护快照的异地冗余副本，可以使用 AzCopy 或“复制 Blob”将本地冗余存储帐户中的快照复制到异地冗余的标准存储帐户。 有关详细信息，请参阅[使用 AzCopy 命令行实用程序传输数据](storage-use-azcopy.md)和 [Copy Blob](/rest/api/storageservices/fileservices/Copy-Blob)（复制 Blob）。

有关在标准存储帐户中针对页 blob 执行 REST 操作的详细信息，请参阅 [Azure 存储服务 REST API](/rest/api/storageservices/fileservices/Azure-Storage-Services-REST-API-Reference)。

### <a name="managed-disks"></a>托管磁盘

托管磁盘的快照是托管磁盘的只读副本，其作为标准托管磁盘存储。 托管磁盘当前不支持增量快照，但以后将支持增量快照。

如果托管磁盘已附加到 VM，则磁盘上将不允许某些 API 操作。 例如，磁盘附加到 VM 时，无法通过生成共享访问签名 (SAS) 来执行复制操作。 请先创建磁盘快照，然后对该快照执行复制操作。 或者，可以分离磁盘，然后通过生成共享访问签名 (SAS) 执行复制操作。

## <a name="pricing-and-billing"></a>定价和计费

使用标准存储时，请注意以下计费方式：

* 标准存储非托管磁盘/数据大小 
* 标准托管磁盘
* 标准存储快照
* 出站数据传输
* 事务

**非托管存储数据和磁盘大小：**对于非托管磁盘和其他数据（blob、表、队列和文件），只需为所使用的存储空间付费。 例如，如果 VM 的页 blob 预配为 127 GB，但 VM 实际只使用了 10 GB 的空间，则只需为 10 GB 空间付费。 

**托管磁盘：**托管磁盘按预配大小计费。 如果磁盘预配为 10 GB，即使只使用了 5 GB，也还是要为预配的 10 GB 大小付费。

**快照**：对标准磁盘的快照使用的额外容量计费。 有关快照的详细信息，请参阅 [Creating a Snapshot of a Blob](/rest/api/storageservices/fileservices/Creating-a-Snapshot-of-a-Blob)（创建 Blob 的快照）。

**出站数据传输**：[出站数据传输](https://azure.microsoft.com/pricing/details/data-transfers/)（Azure 数据中心送出的数据）会产生带宽使用费。

**事务**：Azure 对标准存储的每 100,000 个事务收费 0.0036 美元。 事务包括对存储区的读操作和写操作。

有关标准存储、虚拟机和托管磁盘定价的详细信息，请参阅：

* [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)
* [虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks)

## <a name="azure-backup-service-support"></a>Azure 备份服务支持 

可以使用 Azure 备份来备份具有非托管磁盘的虚拟机。 [更多详细信息](../backup/backup-azure-vms-first-look-arm.md)。

还可将 Azure 备份服务与托管磁盘配合使用，以创建具有基于时间的备份、轻松 VM 还原和备份保留策略的备份作业。 有关详细信息，请参阅[将 Azure 备份服务用于具有托管磁盘的 VM](../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup)。

## <a name="next-steps"></a>后续步骤

* [Azure 存储简介](storage-introduction.md)

* [创建存储帐户](storage-create-storage-account.md)

* [托管磁盘概述](storage-managed-disks-overview.md)

* [使用 Resource Manager 和 PowerShell 创建 VM](../virtual-machines/virtual-machines-windows-ps-create.md)

* [使用 Azure CLI 2.0 创建 Linux VM](../virtual-machines/linux/quick-create-cli.md)
