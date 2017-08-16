
---
title: "Azure VM 备份常见问题解答 | Microsoft Docs"
description: "针对下述常见问题的解答：Azure VM 备份原理、限制以及更改策略时会发生什么情况"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "azure vm 备份, azure vm 还原, 备份策略"
ms.assetid: c4cd7ff6-8206-45a3-adf5-787f64dbd7e1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/18/2017
ms.author: trinadhk;pullabhk;
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: 8c06a90f58cf56ebb4e75e7567e237de7414a300
ms.contentlocale: zh-cn
ms.lasthandoff: 07/20/2017

---
# <a name="questions-about-the-azure-vm-backup-service"></a>有关 Azure VM 备份服务的问题
本文提供常见问题的解答，有助于快速了解 Azure VM 备份组件。 某些答案提供内含全面信息的文章的链接。 你也可以在 [论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)中发布有关 Azure 备份服务的问题。

## <a name="configure-backup"></a>配置备份
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>恢复服务保管库是支持基于经典 VM 还是支持基于 Resource Manager 的 VM？ <br/>
恢复服务保管库同时支持这两种模式。  可以将经典 VM（在经典门户中创建的）或 Resource Manager VM（在 Azure 门户中创建的）备份到恢复服务保管库。

### <a name="what-configurations-are-not-supported-by-azure-vm-backup-"></a>Azure VM 备份不支持哪些配置？
请参阅[支持的操作系统](backup-azure-arm-vms-prepare.md#supported-operating-system-for-backup)和 [VM 备份限制](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm)

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>为什么在配置备份向导中看不到我的 VM？
在配置备份向导中，Azure 备份只列出符合以下条件的 VM：
* 尚未进行保护 - 可以验证 VM 的备份状态，方法是：转到 VM 边栏选项卡，在其中的“设置”菜单中查看备份状态。 详细了解如何[查看 VM 的备份状态](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-management-blade)
* 与 VM 属于同一区域

## <a name="backup"></a>备份
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>按需备份作业是否与计划内备份遵循相同的保留计划？
否。 需为按需备份作业指定保留期。 默认情况下，在从门户触发时，该作业会保留 30 天。 

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>我最近在一些 VM 上启用了 Azure 磁盘加密。 我的备份是否继续有效？
需提供 Azure 备份服务访问 Key Vault 所需的权限。 可以在 PowerShell 中使用 [PowerShell](backup-azure-vms-automation.md) 文档的“启用备份”部分所述步骤提供这些权限。

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>我将 VM 的磁盘迁移到了托管磁盘。 我的备份是否继续有效？
是的，备份可以无缝工作，无需重新配置备份。 

## <a name="restore"></a>还原
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>如何决定是进行磁盘还原，还是进行完整的 VM 还原？
可以将 Azure 的完整 VM 还原视为已还原 VM 的一种快速创建选项。 “还原 VM”选项会更改磁盘名称、磁盘所用容器、公共 IP 地址、网络接口名称，确保在 VM 创建过程中创建的资源的唯一性。另外，它不会将 VM 添加到可用性集。 

“还原磁盘”可以用于：
* 自定义根据时间点配置创建的 VM，例如更改备份配置中的大小
* 添加在备份时不存在的配置 
* 控制所要创建资源的命名约定
* 将 VM 添加到可用性集
* 提供只能通过 PowerShell/声明性模板定义实现的任何配置

## <a name="manage-vm-backups"></a>管理 VM 备份
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>在 VM 上更改备份策略时，会发生什么情况？
在 VM 上应用新策略时，将遵循新策略的计划和保留期。 如果延长保留期，则会对现有的恢复点进行标记，按新策略要求保留它们。 如果缩短保留期，则会将其标记为在下一清理作业中删除，然后就会将其删除。 

