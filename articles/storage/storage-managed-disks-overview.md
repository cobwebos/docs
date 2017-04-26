---
title: "Azure 高级和标准托管磁盘概述 |Microsoft 文档"
description: "概述了 Azure 托管磁盘，该磁盘在你使用 Azure VM 时为你处理存储帐户"
services: storage
documentationcenter: na
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 272250b3-fd4e-41d2-8e34-fd8cc341ec87
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 53bd62688aa0d1a06d2d012c8da664d2de4b0b45
ms.lasthandoff: 04/13/2017


---

# <a name="azure-managed-disks-overview"></a>Azure 托管磁盘概述

Azure 托管磁盘通过管理与 VM 磁盘关联的[存储帐户](storage-introduction.md)简化了 Azure IaaS VM 的磁盘管理。 你只需指定所需的类型（[高级](storage-premium-storage.md)或[标准](storage-standard-storage.md)）和磁盘大小，Azure 将为你创建和管理磁盘。

>[!NOTE]
>使用托管磁盘的 VM 要求端口 8443 上的出站流量将已安装 [VM 扩展](../virtual-machines/windows/extensions-features.md)的状态报告给 Azure 平台。 如果无法使用该端口，则为 VM 预配扩展会失败。 另外，如果将扩展安装在正在运行的 VM 上，该扩展的部署状态将为未知。 如果不能解除对端口 8443 的阻止，则必须使用非托管磁盘。 我们正在努力解决此问题。 有关更多详细信息，请参阅 [IaaS VM 磁盘常见问题](storage-faq-for-disks.md#managed-disks-and-port-8443)。 
>
>

## <a name="benefits-of-managed-disks"></a>托管磁盘的好处

接下来让我们看一下使用托管磁盘可以获得的一些好处。

### <a name="simple-and-scalable-vm-deployment"></a>简单且可缩放的 VM 部署

托管磁盘在幕后为你处理存储。 以前，你必须创建存储帐户来存储你的 Azure VM 的磁盘（VHD 文件）。 进行扩展时，必须确保创建了额外的存储帐户，以便任何磁盘都不会超出对存储的 IOPS 限制。 使用托管磁盘处理存储时，不再受限于存储帐户限制（例如 20,000 IOPS / 帐户）。 也不再需要将自定义映像（VHD 文件）复制到多个存储帐户。 可以在一个中心位置管理自定义映像（每个 Azure 区域一个存储帐户），并使用它们在一个订阅中创建数百台 VM。

托管磁盘允许在一个订阅中创建最多 10,000 个 VM **磁盘**，这使得你可以在单个订阅中创建数百台 **VM**。 通过允许使用某个应用商店映像在一个 VMSS 中创建多达一千台 VM，此功能还可以进一步增加[虚拟机规模集 (VMSS)](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) 的可伸缩性。

### <a name="better-reliability-for-availability-sets"></a>可用性集更加可靠

通过确保可用性集中的 VM 的磁盘彼此之间完全隔离以避免单点故障，托管磁盘为可用性集提供了更佳的可靠性。 它通过自动将磁盘放置在不同的存储缩放单位（模块）中来实现这一点。 如果某个模块因硬件或软件故障而失败，则只有其磁盘在该模块上的 VM 实例会失败。 例如，假定某个应用程序在 5 台 VM 上运行并且这些 VM 位于一个可用性集中。 这些 VM 的磁盘不会存储在同一个模块中，因此，如果一个模块失败，该应用程序的其他实例可以继续运行。

### <a name="granular-access-control"></a>粒度访问控制

可以使用 [Azure 基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-what-is.md) 将对托管磁盘的特定权限分配给一个或多个用户。 托管磁盘公开了各种操作，包括读取、写入（创建/更新）、删除，以及检索磁盘的[共享访问签名 (SAS) URI](storage-dotnet-shared-access-signature-part-1.md)。 可以仅将某人员执行其工作所需的操作的访问权限授予该人员。 例如，如果不希望某人员将某个托管磁盘复制到存储帐户，则可以选择不授予对该托管磁盘的导出操作的访问权限。 类似地，如果不希望某人员使用 SAS URI 复制某个托管磁盘，则可以选择不授予对该托管磁盘的该权限。

### <a name="azure-backup-service-support"></a>Azure 备份服务支持 
将 Azure 备份服务与托管磁盘配合使用，创建具有基于时间的备份、轻松 VM 还原和备份保留策略的备份作业。 托管磁盘仅支持使用本地冗余存储 (LRS) 作为复制选项；这意味着它在单个区域中保留三个数据副本。 对于区域性灾难恢复，必须使用 [Azure 备份服务](../backup/backup-introduction-to-azure-backup.md)和作为备份保管库的 GRS 存储帐户来备份不同区域中的 VM 磁盘。 有关详细信息，请参阅[为具有托管磁盘的 VM 使用 Azure 备份服务](../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup)。 

## <a name="pricing-and-billing"></a>定价和计费 

使用托管磁盘时，将考虑以下计费事项：

* 存储类型

* 磁盘大小

* 事务数

* 出站数据传输

* 托管磁盘快照（全磁盘复制）

下面将更详细地介绍各项。

**存储类型：**托管磁盘提供了 2 个性能层：[高级](storage-premium-storage.md)（基于 SSD）和[标准](storage-standard-storage.md)（基于 HDD）。 托管磁盘的计费取决于你为磁盘选择的存储类型。


**磁盘大小**：托管磁盘的计费取决于磁盘的预配大小。 Azure 会将预配大小映射（向上舍入）到下面各表中指定的最接近的托管磁盘选项。 每个托管磁盘都映射到其中一种受支持的预配大小并相应地进行计费。 例如，如果你创建了一个标准托管磁盘并将预配大小指定为 200 GB，则会根据 S20 磁盘类型的定价向你收费。

下面是高级托管磁盘可用的磁盘大小：

| **高级托管<br>磁盘类型**  | **P10** | **P20** | **P30**        |
|------------------|---------|---------|----------------|
| 磁盘大小        | 128 GB  | 512 GB  | 1024 GB (1 TB) |

下面是标准托管磁盘可用的磁盘大小： 

| **标准托管<br>磁盘类型** | **S4**  | **S6**  | **S10**        | **S20** | **S30**        |
|------------------|---------|---------|----------------|---------|----------------|
| 磁盘大小        | 32 GB   | 64 GB   | 128 GB  | 512 GB  | 1024 GB (1 TB) |

**事务数**：将根据你对标准托管磁盘执行的事务数向你收费。 高级托管磁盘没有事务费用。

**出站数据传输**：[出站数据传输](https://azure.microsoft.com/pricing/details/data-transfers/)（Azure 数据中心送出的数据）会产生带宽使用费。

**托管磁盘快照（全磁盘复制）**：托管快照是托管磁盘的只读副本，它作为标准托管磁盘进行存储。 使用快照，可以在任意时间点备份托管磁盘。 这些快照独立于源磁盘而存在，并可用来创建新的托管磁盘。 托管快照的费用与标准托管磁盘相同。 例如，如果创建 128 GB 高级托管磁盘的快照，则托管快照的费用将等于 128 GB 标准托管磁盘。

托管磁盘当前不支持[增量快照](storage-incremental-snapshots.md)，但将来会支持。

若要了解有关如何使用托管磁盘创建快照的详细信息，请查看下列资源：

* [在 Windows 中使用快照创建存储为托管磁盘的 VHD 的副本](../virtual-machines/windows/snapshot-copy-managed-disk.md)
* [在 Linux 中使用快照创建存储为托管磁盘的 VHD 的副本](../virtual-machines/linux/snapshot-copy-managed-disk.md)


有关托管磁盘的详细定价信息，请参阅[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks)。

## <a name="images"></a>映像

托管磁盘还支持创建托管自定义映像。 可以从存储帐户中的自定义 VHD 创建映像或者直接从通用化 (sys-prepped) VM 创建映像。 这会将与 VM 关联的所有托管磁盘捕获到单个映像中，包括 OS 和数据磁盘。 这将允许使用自定义映像创建数百台 VM，且不需要复制或管理任何存储帐户。

有关创建映像的信息，请查看以下文章：
* [如何在 Azure 中捕获通用 VM 的托管映像](../virtual-machines/windows/capture-image-resource.md)
* [如何使用 Azure CLI 2.0 用化和捕获 Linux 虚拟机](../virtual-machines/linux/capture-image.md)

## <a name="images-versus-snapshots"></a>映像与快照

你经常看到词语“映像”与 VM 一起使用，现在你还看到了“快照”。 了解它们之间的区别很重要。 使用托管磁盘，可以创建已解除分配的通用 VM 的映像。 此映像将包括附加到该 VM 的所有磁盘。 可以使用此映像创建新的 VM，并且它将包括所有磁盘。

快照是磁盘在创建快照那一刻的副本。 它仅应用于一个磁盘。 如果存在仅具有一个磁盘（OS）的 VM，则可以为其创建快照或映像，并且可以通过该快照或映像创建 VM。

如果 VM 具有五个磁盘且这些磁盘是条带化的，将会怎样？ 你可以创建每个磁盘的快照，但是系统对于 VM 中的磁盘状况没有意识 – 快照只知道那一个磁盘的状况。 在这种情况下，快照彼此之间需要相互协调，目前不支持此功能。

## <a name="managed-disks-and-encryption"></a>托管磁盘和加密

以下介绍托管磁盘的两种加密方式。 第一种是存储服务加密 (SSE)，由存储服务执行。 第二种是 Azure 磁盘加密，可以在 VM 的 OS 和数据磁盘上启用。 

### <a name="storage-service-encryption-sse"></a>存储服务加密 (SSE)

Azure 存储支持自动对写入到存储帐户中的数据进行加密。 有关更多详细信息，请参考[静态数据的 Azure 存储服务加密](storage-service-encryption.md)。 托管磁盘上的数据是怎样的情况？ 当前，无法为托管磁盘启用存储服务加密。 但以后会发布此功能。 在此期间，你需要知道如何使用位于加密的存储帐户中且已将其自身加密的 VHD 文件。 

SSE 在数据写入到存储帐户时会对数据进行加密。 如果你有曾经使用 SSE 进行了加密的 VHD 文件，则无法使用该 VHD 文件来创建使用托管磁盘的 VM。 也无法将加密的非托管磁盘转换为托管磁盘。 最后，如果在该存储帐户上禁用加密，它不会反过来对 VHD 文件进行解密。 

若要使用已加密的磁盘，必须首先将 VHD 文件复制到一个从未加密过的存储帐户。 然后，你可以创建具有托管磁盘的 VM 并在创建期间指定该 VHD 文件，或者将复制的 VHD 文件附加到某个具有托管磁盘且正在运行的 VM。 

### <a name="azure-disk-encryption-ade"></a>Azure 磁盘加密 (ADE)

Azure 磁盘加密允许加密 IaaS 虚拟机使用的 OS 磁盘和数据磁盘。 这包括托管磁盘。 对于 Windows，驱动器是使用行业标准 BitLocker 加密技术加密的。 对于 Linux，磁盘是使用 DM-Crypt 技术加密的。 这将与 Azure 密钥保管库集成，可让你控制和管理磁盘加密密钥。 有关详细信息，请参阅[适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密](../security/azure-security-disk-encryption.md)。

## <a name="next-steps"></a>后续步骤

有关托管磁盘的详细信息，请参考以下文章。

### <a name="get-started-with-managed-disks"></a>托管磁盘入门 

* [使用 Resource Manager 和 PowerShell 创建 VM](../virtual-machines/virtual-machines-windows-ps-create.md)

* [使用 Azure CLI 2.0 创建 Linux VM](../virtual-machines/linux/quick-create-cli.md)

* [使用 PowerShell 将托管数据磁盘附加到 Windows VM](../virtual-machines/windows/attach-disk-ps.md)

* [将托管磁盘添加到 Linux VM](../virtual-machines/linux/add-disk.md)

### <a name="compare-managed-disks-storage-options"></a>比较托管磁盘存储选项 

* [高级存储和磁盘](storage-premium-storage.md)

* [标准存储和磁盘](storage-standard-storage.md)

### <a name="operational-guidance"></a>操作指南

* [从 AWS 和其他平台迁移到 Azure 中的托管磁盘](../virtual-machines/windows/on-prem-to-azure.md)

* [将 Azure VM 转换为 Azure 中的托管磁盘](../virtual-machines/windows/migrate-to-managed-disks.md)

