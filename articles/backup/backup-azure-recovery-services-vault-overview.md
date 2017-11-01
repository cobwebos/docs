---
title: "恢复服务保管库概述 | Microsoft Docs"
description: "恢复服务保管库和 Azure 备份保管库的概述和比较。"
services: backup
documentationcenter: " "
author: markgalioto
manager: carmonm
ms.assetid: 38d4078b-ebc8-41ff-9bc8-47acf256dc80
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/15/2017
ms.author: markgal;arunak
ms.openlocfilehash: a3d50d0066f1d0fe38bd7c5474386f54df81bec5
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2017
---
# <a name="recovery-services-vaults-overview"></a>恢复服务保管库概述

本文介绍恢复服务保管库的功能。 恢复服务保管库是 Azure 中用于存储数据的存储实体。 数据通常是虚拟机 (VM)、工作负荷、服务器或工作站的数据或配置信息的副本。 可以使用恢复服务保管库为各种 Azure 服务（例如 IaaS VM（Linux 或 Windows））和 Azure SQL 数据库存储备份数据。 恢复服务保管库支持 System Center DPM、Windows Server、Azure 备份服务器等。 使用恢复服务保管库可以方便地组织备份数据，并将管理开销降至最低。 

在 Azure 订阅中，最多可以创建 25 个恢复服务保管库。

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>比较恢复服务保管库和备份保管库

如果仍然使用备份保管库，它们将自动升级到恢复服务保管库。 在 2017 年 11 月 之前，所有备份保管库均已升级到恢复服务保管库。 

恢复服务保管库基于 Azure 的 Azure 资源管理器模型，而备份保管库则基于 Azure Service Manager 模型。 将备份保管库升级到恢复服务保管库时，备份数据在升级过程中和升级后均会保持不变。 恢复服务保管库提供不适用于备份保管库的功能，例如：

- **有助于确保备份数据安全的增强功能**：使用恢复服务保管库时，Azure 备份提供用于保护云备份的安全功能。 这些安全功能确保可以保护备份并安全地恢复数据，即使生产服务器和备份服务器受到危害。 [了解详细信息](backup-azure-security-feature.md)

- **针对混合 IT 环境进行集中监视**：使用恢复服务保管库时，可以通过中心门户监视 [Azure IaaS VM](backup-azure-manage-vms.md) 和[本地资产](backup-azure-manage-windows-server.md#manage-backup-items)。 [了解详细信息](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **基于角色的访问控制 (RBAC)**：RBAC 在 Azure 中提供精细的访问管理控制。 [Azure 提供各种内置角色](../active-directory/role-based-access-built-in-roles.md)，而 Azure 备份包含三个[用于管理恢复点的内置角色](backup-rbac-rs-vault.md)。 恢复服务保管库兼容 RBAC，后者会限制对已定义用户角色集的备份和还原访问权限。 [了解详细信息](backup-rbac-rs-vault.md)

- **保护 Azure 虚拟机的所有配置**：恢复服务保管库保护基于 Resource Manager 的 VM，其中包括高级磁盘、托管磁盘和加密 VM。 将备份保管库升级到恢复服务保管库以后，即可将基于 Service Manager 的 VM 升级到基于 Resource Manager 的 VM。 在升级保管库的同时，可以保留基于 Service Manager 的 VM 恢复点，并为已升级（已启用 Resource Manager）的 VM 配置保护。 [了解详细信息](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **适用于 IaaS VM 的即时还原**：使用恢复服务保管库时，可以从 IaaS VM 还原文件和文件夹，不需还原整个 VM，从而缩短还原时间。 适用于 IaaS VM 的即时还原可供 Windows 和 Linux VM 使用。 [了解详细信息](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>在门户中管理恢复服务保管库
在 Azure 门户中创建和管理恢复服务保管库很容易，因为备份服务已集成到“Azure 设置”菜单中。 这种集成意味着，可以在目标服务上下文中创建和管理恢复服务保管库。 例如，若要查看 VM 的恢复点，请选中该 VM，并在“设置”菜单中单击“备份”。 特定于该 VM 的备份信息就会显示。 在以下示例中，ContosoVM 是虚拟机的名称。 **ContosoVM-demovault** 是恢复服务保管库的名称。 不需记住用于存储恢复点的恢复服务保管库的名称，可以在虚拟机中访问该信息。  

![VM 中的恢复服务保管库详细信息](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context.png)

如果多个服务器使用同一恢复服务保管库进行保护，则更应查看恢复服务保管库。 可以在订阅中搜索所有恢复服务保管库，并从列表中选择一个。

以下部分包含多篇文章的链接，这些文章说明了如何在每类活动中使用恢复服务保管库。

### <a name="back-up-data"></a>备份数据
- [备份 Azure VM](backup-azure-vms-first-look-arm.md)
- [备份 Windows Server 或 Windows 工作站](backup-try-azure-backup-in-10-mins.md)
- [将 DPM 工作负荷备份到 Azure](backup-azure-dpm-introduction.md)
- [准备使用 Azure 备份服务器来备份工作负荷](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>管理恢复点
- [管理 Azure VM 备份](backup-azure-manage-vms.md)
- [管理文件和文件夹](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>从保管库还原数据
- [从 Azure VM 恢复单个文件](backup-azure-restore-files-from-vm.md)
- [还原 Azure VM](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>保护保管库
- [保护恢复服务保管库中的云备份数据](backup-azure-security-feature.md)



## <a name="next-steps"></a>后续步骤
使用以下文章了解相关操作：</br>
[备份 IaaS VM](backup-azure-arm-vms-prepare.md)</br>
[备份 Azure 备份服务器](backup-azure-microsoft-azure-backup.md)</br>
[备份 Windows Server](backup-configure-vault.md)
