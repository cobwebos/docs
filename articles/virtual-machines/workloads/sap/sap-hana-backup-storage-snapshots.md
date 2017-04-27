---
title: "基于存储快照的 SAP HANA Azure 备份 | Microsoft Docs"
description: "对于 Azure 虚拟机上的 SAP HANA，可以采用两种可行的主要备份方法，本文介绍基于存储快照的 SAP HANA 备份"
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
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 5d395da0779d84e414341d6d73151fd7e77e3e8e
ms.lasthandoff: 04/03/2017


---

# <a name="sap-hana-backup-based-on-storage-snapshots"></a>基于存储快照的 SAP HANA 备份

## <a name="introduction"></a>介绍

本文是有关 SAP HANA 备份的系列教程所包含的三篇文章中的一篇。 [Azure 虚拟机上的 SAP HANA 备份指南](sap-hana-backup-guide.md)提供概述和入门信息，[文件级别的 SAP HANA Azure 备份](sap-hana-backup-file-level.md)介绍基于文件的备份选项。

针对单实例多合一演示系统使用 VM 备份功能时，应该考虑执行 VM 备份，而不是在 OS 级别管理 HANA 备份。 创建 Azure Blob 快照的替代做法是为附加到虚拟机的单个虚拟磁盘创建副本，并保留 HANA 数据文件。 但一个要点是，在系统已启动并运行的情况下，应该在创建 VM 备份或磁盘快照时保持应用一致性。 请参阅相关文章[Azure 虚拟机上的 SAP HANA 备份指南](sap-hana-backup-guide.md)中的_创建存储快照时保持 SAP HANA 数据一致性_。 SAP HANA 提供了一项功能用于支持此类存储快照。

## <a name="sap-hana-snapshots"></a>SAP HANA 快照

SAP HANA 中提供了一项用于支持创建存储快照的功能。 但是，截止 2016 年 12 月，仅限于对单容器系统使用该功能。 多租户容器配置不支持此类数据库快照（请参阅[创建存储快照 (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)）。

此功能的工作原理如下：

- 通过启动 SAP HANA 快照来准备存储快照
- 运行存储快照（例如 Azure Blob 快照）
- 确认 SAP HANA 快照

![此屏幕截图显示，可以通过 SQL 语句创建 SAP HANA 数据快照](media/sap-hana-backup-storage-snapshots/image011.png)

此屏幕截图显示，可以通过 SQL 语句创建 SAP HANA 数据快照。

![然后，该快照将显示在 SAP HANA Studio 的备份目录中](media/sap-hana-backup-storage-snapshots/image012.png)

然后，该快照将显示在 SAP HANA Studio 的备份目录中。

![在磁盘上，该快照将显示在 SAP HANA 数据目录中](media/sap-hana-backup-storage-snapshots/image013.png)

在磁盘上，该快照将显示在 SAP HANA 数据目录中。

当 SAP HANA 处于快照准备模式时，在运行存储快照之前，用户还必须保证文件系统一致性。 请参阅相关文章[Azure 虚拟机上的 SAP HANA 备份指南](sap-hana-backup-guide.md)中的_创建存储快照时保持 SAP HANA 数据一致性_。

存储快照完成后，务必确认 SAP HANA 快照。 可以运行一个相应的 SQL 语句：BACKUP DATA CLOSE SNAPSHOT（请参阅 [BACKUP DATA CLOSE SNAPSHOT 语句（备份和恢复）](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm)）。

> [!IMPORTANT]
> 确认 HANA 快照。 由于使用了&quot;写入时复制&quot;，SAP HANA 在处于快照准备模式时，可能需要额外的磁盘空间，以外，只有在确认 SAP HANA 快照后，才能启动新的备份。

## <a name="hana-vm-backup-via-azure-backup-service"></a>通过 Azure 备份服务进行 HANA VM 备份

截止 2016 年 12 月，Azure 备份服务的备份代理不适用于 Linux VM。 若要利用文件/目录级别的 Azure 备份，需将 SAP HANA 备份文件复制到 Windows VM，然后使用备份代理。 否则，只能通过 Azure 备份服务进行完整的 Linux VM 备份。 有关详细信息，请参阅 [Azure 备份中的功能概述](../../../backup/backup-introduction-to-azure-backup.md)。

Azure 备份服务提供一个选项用于备份和还原 VM。 有关此服务及其工作原理的详细信息，请参阅[在 Azure 中规划 VM 备份基础结构](../../../backup/backup-azure-vms-introduction.md)一文。

根据该文章中的描述，需要注意两个重要事项：

_&quot;对于 Linux 虚拟机，只能执行文件一致性备份，因为 Linux 没有与 VSS 相当的平台。&quot;_

_&quot;应用程序需要对还原的数据实施自身的&quot;修复&quot;机制。&quot;_

因此，在启动备份时，必须确保 SAP HANA 在磁盘上处于一致状态。 请参阅本文档前面所述的 _SAP HANA 快照_。 但是，SAP HANA 处于此快照准备模式时有一个潜在的问题。 有关详细信息，请参阅[创建存储快照 (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)。

该文章指出：

_&quot;我们强烈建议在创建存储快照后，尽快确认或丢弃该快照。准备或创建存储快照时，快照相关的数据将被冻结。当快照相关的数据保持冻结状态时，仍可在数据库中进行更改。此类更改不会导致冻结的快照相关数据发生更改。这些更改将写入到数据区域中独立于存储快照的位置。另外，这些更改还会写入到日志中。但是，快照相关数据保持冻结的时间越长，增长的数据量可能越大。&quot;_

Azure 备份通过 Azure VM 扩展来处理文件系统一致性。 这些扩展不可单独使用，只能与 Azure 备份服务结合使用。 但无论如何，都必须管理 SAP HANA 快照，以保证应用一致性。

Azure 备份包括两个主要阶段：

- 创建快照
- 将数据传输到保管库

因此，在完成 Azure 备份服务的创建快照阶段后，便可以确认 SAP HANA 快照。 可能需要在几分钟后，快照才会显示在 Azure 门户中。

![此图显示了 Azure 备份服务的备份作业列表的一部分](media/sap-hana-backup-storage-snapshots/image014.png)

此图显示了用于备份 HANA 测试 VM 的 Azure 备份服务备份作业列表的一部分。

![若要显示作业详细信息，请在 Azure 门户中单击备份作业](media/sap-hana-backup-storage-snapshots/image015.png)

若要显示作业详细信息，请在 Azure 门户中单击备份作业。 此处显示了两个阶段。 可能需要在几分钟后，门户中才显示快照阶段已完成。 大部分时间花费在数据传输阶段。

## <a name="hana-vm-backup-automation-via-azure-backup-service"></a>通过 Azure 备份服务自动进行 HANA VM 备份

完成 Azure 备份的快照阶段后，可按前文所述手动确认 SAP HANA 快照，但最好是考虑自动备份，因为管理员可能不会在 Azure 门户中监视备份作业列表。

下面介绍了如何通过 Azure PowerShell cmdlet 实现自动备份。

![已创建名为 hana-backup-vault 的 Azure 备份服务](media/sap-hana-backup-storage-snapshots/image016.png)

已创建名为 &quot;hana-backup-vault&quot; 的 Azure 备份服务。 PS 命令 **Get-AzureRmRecoveryServicesVault -Name hana-backup-vault** 检索相应的对象。 然后，使用此对象设置下图所示的备份上下文。

![用户可以检查当前正在进行的备份作业](media/sap-hana-backup-storage-snapshots/image017.png)

设置正确的上下文后，可以检查当前正在进行的备份作业，然后查看其作业详细信息。 子任务列表显示 Azure 备份作业的快照阶段是否已完成：

```
$ars = Get-AzureRmRecoveryServicesVault -Name hana-backup-vault
Set-AzureRmRecoveryServicesVaultContext -Vault $ars
$jid = Get-AzureRmRecoveryServicesBackupJob -Status InProgress | select -ExpandProperty jobid
Get-AzureRmRecoveryServicesBackupJobDetails -Jobid $jid | select -ExpandProperty subtasks
```

![在循环中轮询值，直到状态变为 Completed](media/sap-hana-backup-storage-snapshots/image018.png)

将作业详细信息存储到变量后，只需使用 PS 语法来获取第一个数组项并检索状态值。 若要完成自动化脚本，请在循环中轮询值，直到状态变为 &quot;Completed&quot;。

```
$st = Get-AzureRmRecoveryServicesBackupJobDetails -Jobid $jid | select -ExpandProperty subtasks
$st[0] | select -ExpandProperty status
```

## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>HANA 许可证密钥和通过 Azure 备份服务还原 VM

Azure 备份服务可在还原过程中创建新的 VM。 我们暂时没有针对现有 Azure VM 执行&quot;就地&quot;还原的计划。

![此图显示 Azure 门户中 Azure 服务的还原选项](media/sap-hana-backup-storage-snapshots/image019.png)

此图显示 Azure 门户中 Azure 服务的还原选项。 可以选择在还原期间创建 VM，或者选择还原磁盘。 还原磁盘后，仍然需要在其顶层创建新的 VM。 每当在 Azure 中创建新的 VM 后，唯一的 VM ID 会发生更改（请参阅[访问和使用 Azure VM 唯一 ID](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)）。

![此图显示通过 Azure 备份服务还原之前和之后的 Azure VM 唯一 ID](media/sap-hana-backup-storage-snapshots/image020.png)

此图显示通过 Azure 备份服务还原之前和之后的 Azure VM 唯一 ID。 用于 SAP 许可的 SAP 硬件密钥将使用此唯一 VM ID。 因此，在还原 VM 后，必须安装新的 SAP 许可证。

在编写本备份指南期间，已推出一个预览模式的新 Azure 备份功能。 使用该功能可以基于针对 VM 备份创建的 VM 快照实现文件级还原。 这样，便不需要部署新的 VM，从而可让唯一的 VM ID 保持不变，并且无需使用新的 SAP HANA 许可证密钥。 在全面测试此功能后，我们将提供有关此功能的更多文档。

Azure 备份最终会允许备份单个 Azure 虚拟磁盘，以及 VM 内部的文件和目录。 Azure 备份的主要优势是能够管理所有备份，使客户不需要执行这些工作。 如果需要还原，Azure 备份将选择要使用的适当备份。

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>通过手动磁盘快照进行 SAP HANA VM 备份

可以不使用 Azure 备份服务，而是通过 PowerShell 手动创建 Azure VHD 的 Blob 快照，以此配置单个备份解决方案。 有关步骤的说明，请参阅[通过 PowerShell 使用 Blob 快照](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)。

这种方法提供更大的灵活性，但无法解决本文档前面所述的问题：

- 仍然必须确保 SAP HANA 处于一致状态
- 即使解除分配 VM，也无法覆盖 OS 磁盘，因为有一条错误消息指出存在租约。 这种方法只适合在删除 VM 后使用，而这又会导致生成新的唯一 VM ID，并且需要安装新的 SAP 许可证。

![可以只还原 Azure VM 的数据磁盘](media/sap-hana-backup-storage-snapshots/image021.png)

可以只还原 Azure VM 的数据磁盘，这样就可以避免出现生成新的唯一 VM ID，从而使 SAP 许可证失效的问题：

- 在测试时，我们将两个 Azure 数据磁盘附加到了 VM，并在这些磁盘的顶层定义了软件 RAID 
- 经确认，SAP HANA 快照功能能使 SAP HANA 保持一致状态
- 文件系统冻结（请参阅相关文章[Azure 虚拟机上的 SAP HANA 备份指南](sap-hana-backup-guide.md)中的_创建存储快照时保持 SAP HANA 数据一致性_）
- Blob 快照是从这两个数据磁盘创建的
- 文件系统解冻
- SAP HANA 快照确认
- 为了还原数据磁盘，已关闭 VM 并分离了两个磁盘
- 分离磁盘后，这些磁盘被以前的 Blob 快照覆盖
- 然后，将还原的虚拟磁盘再次附加到 VM
- 启动 VM 后，软件 RAID 中的所有组件都能正常工作，并已设置回到 Blob 快照时间
- HANA 已设置回到 HANA 快照

如果在创建 Blob 快照之前能够关闭 SAP HANA，则过程不会很复杂。 在这种情况下，可以跳过 HANA 快照；如果系统中没有其他正在进行的操作，则还可以跳过文件系统冻结。 如果在所有内容都联机的情况下需要执行快照，则复杂性会明显增大。 请参阅相关文章[Azure 虚拟机上的 SAP HANA 备份指南](sap-hana-backup-guide.md)中的_创建存储快照时保持 SAP HANA 数据一致性_。

## <a name="next-steps"></a>后续步骤
* [Azure 虚拟机上的 SAP HANA 备份指南](sap-hana-backup-guide.md)提供了概述和入门信息。
* [基于文件级别的 SAP HANA 备份](sap-hana-backup-file-level.md)介绍了基于存储快照的备份选项。
* 若要了解如何建立高可用性以及针对 Azure 上的 SAP HANA（大型实例）规划灾难恢复，请参阅 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)。

