---
title: 关于 Azure 虚拟机还原过程
description: 了解 Azure 备份服务如何还原 Azure 虚拟机
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: b9a2636a2144ea40457bdc3d88786785cb012e0d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84325672"
---
# <a name="about-azure-vm-restore"></a>关于 Azure VM 还原

本文介绍[Azure 备份服务](https://docs.microsoft.com/azure/backup/backup-overview)如何还原 azure 虚拟机（vm）。 有多个还原选项。 我们将讨论它们支持的各种方案。

## <a name="concepts"></a>概念

- **恢复点**（也称为 "**还原**点"）： "恢复点" 是要备份的原始数据的副本。

- **层（快照与保管库）**： Azure VM 备份分两个阶段进行：

  - 在阶段1中，拍摄的快照与磁盘一起存储。 这称为**快照层**。 快照层还原的速度更快（而不是从保管库还原），因为它们消除了在触发还原之前快照复制到保管库的等待时间。 因此从快照层还原也称为 "[即时还原](https://docs.microsoft.com/azure/backup/backup-instant-restore-capability)"。
  - 在阶段2中，会传输快照并将其存储在由 Azure 备份服务管理的保管库中。 这称为**保管库层**。

- **原始位置恢复（OLR）**：从还原点到源 Azure VM 的恢复已进行备份，并将其替换为恢复点中存储的状态。 这会替换源 VM 的 OS 磁盘和数据磁盘。

- **备用位置恢复（ALR）**：从恢复点到执行备份的原始服务器以外的服务器进行的恢复。

- **项级还原（ILR）：** 从恢复点还原 VM 内的单个文件或文件夹

- **可用性（复制类型）**： Azure 备份提供两种类型的复制，使存储/数据高度可用：
  - [本地冗余存储（LRS）](../storage/common/storage-redundancy-lrs.md)将数据复制到数据中心中的存储缩放单位三次（创建三个数据副本）。 数据的所有副本存在于同一区域。 LRS 是一种低成本选项，用于保护数据免受本地硬件故障的影响。
  - [异地冗余存储（GRS）](../storage/common/storage-redundancy-grs.md)是默认和推荐的复制选项。 GRS 将数据复制到离源数据主位置数英里之外的次要区域中。 GRS 的成本比 LRS 的高，但 GRS 提供更高的数据持久度，即使出现区域性服务中断也是如此。

- **跨区域还原（CRR）**：作为一个[还原选项](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options)，跨区域还原（crr）允许你在辅助区域（即[azure 配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)）中还原 Azure vm。

## <a name="restore-scenarios"></a>还原方案

![还原方案 ](./media/about-azure-vm-restore/recovery-scenarios.png)

| **方案**                                                 | **完成操作**                                             | **何时使用**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [还原以创建新的虚拟机](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms) | 将整个 VM 还原到 OLR （如果源 VM 仍然存在）或 ALR | <li> 如果源 VM 丢失或损坏，则可以还原整个 VM  <li> 你可以创建 VM 的副本  <li> 您可以执行审核或符合性的还原钻取  <li> 此选项不适用于从 Marketplace 映像创建的 Azure Vm （即，如果由于许可证已过期，它们不可用）。 |
| [还原 VM 的磁盘](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) | 还原附加到 VM 的磁盘                             |  所有磁盘：此选项创建模板并还原磁盘。 你可以使用特殊配置（例如，可用性集）编辑此模板以满足你的要求，然后使用模板并还原磁盘来重新创建 VM。 |
| [还原 VM 内的特定文件](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) | 选择还原点，浏览，选择文件，然后将其还原到与备份 VM 相同（或兼容）的操作系统。 |  如果知道要还原哪些特定文件，请使用此选项，而不是还原整个 VM。 |
| [还原已加密的 VM](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) | 在门户中，还原磁盘，然后使用 PowerShell 创建 VM | <li> [Azure Active Directory （AAD）的加密 VM](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-windows-aad)  <li> [未 AAD 的加密 VM](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-windows) <li> [已将*aad*迁移到*不带 AAD*的加密 VM](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-faq#can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app) |
| [跨区域还原](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#cross-region-restore) | 创建新的 VM，或将磁盘还原到次要区域（Azure 配对区域） | <li> **完全中断**：通过跨区域还原功能，无等待时间恢复次要区域中的数据。 即使在 Azure 声明服务中断之前，也可以在次要区域中启动还原。 <li> **部分中断**：停机可能发生在特定的存储群集中，其中 azure 备份存储已备份的数据或甚至是网络，连接与已备份数据关联的 azure 备份和存储群集。 使用跨区域还原，你可以使用辅助区域中已备份数据的副本在辅助区域中执行还原。 <li> **无中断**：你可以执行业务连续性和灾难恢复（BCDR）演练，以便对辅助区域数据执行审核或符合性目的。 这样一来，即使主要区域中的备份数据在业务连续性和灾难恢复演练中没有完全或部分中断，您也可以对辅助区域执行已备份数据的还原。  |

------





## <a name="next-steps"></a>后续步骤

- [有关 VM 还原的常见问题](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#restore)
- [支持的还原方法](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#supported-restore-methods)
- [排查还原问题](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#restore)
