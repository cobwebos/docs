---
title: 恢复服务保管库概述
description: 恢复服务保管库和 Azure 备份保管库的概述和比较。
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: a0dacd82b7cf4258c0147bbaf9dc39ee6fc0fa25
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82597938"
---
# <a name="recovery-services-vaults-overview"></a>恢复服务保管库概述

本文介绍恢复服务保管库的功能。 恢复服务保管库是 Azure 中用于存储数据的存储实体。 数据通常是虚拟机 (VM)、工作负荷、服务器或工作站的数据或配置信息的副本。 可以使用恢复服务保管库为各种 Azure 服务（例如 IaaS VM（Linux 或 Windows））和 Azure SQL 数据库存储备份数据。 恢复服务保管库支持 System Center DPM、Windows Server、Azure 备份服务器等。 使用恢复服务保管库可以方便地组织备份数据，并将管理开销降至最低。

在 Azure 订阅中，每个区域每个订阅最多可以创建 500 个恢复服务保管库。

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>比较恢复服务保管库和备份保管库

如果仍有备份保管库，它们会自动升级到恢复服务保管库。 在 2017 年 11 月 之前，所有备份保管库均已升级到恢复服务保管库。

恢复服务保管库基于 Azure 的 Azure 资源管理器模型，而备份保管库则基于 Azure Service Manager 模型。 将备份保管库升级到恢复服务保管库时，备份数据在升级过程中和升级后均会保持不变。 恢复服务保管库提供不适用于备份保管库的功能，例如：

- **有助于确保备份数据安全的增强功能**：使用恢复服务保管库时，Azure 备份提供用于保护云备份的安全功能。 这些安全功能确保可以保护备份并安全地恢复数据，即使生产服务器和备份服务器受到危害。 [了解详细信息](backup-azure-security-feature.md)

- **针对混合 IT 环境进行集中监视**：使用恢复服务保管库时，可以通过中心门户监视 [Azure IaaS VM](backup-azure-manage-vms.md) 和[本地资产](backup-azure-manage-windows-server.md#manage-backup-items)。 [了解详细信息](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **基于角色的访问控制 (RBAC)** ：RBAC 在 Azure 中提供精细的访问管理控制。 [Azure 提供各种内置角色](../role-based-access-control/built-in-roles.md)，而 Azure 备份包含三个[用于管理恢复点的内置角色](backup-rbac-rs-vault.md)。 恢复服务保管库兼容 RBAC，后者会限制对已定义用户角色集的备份和还原访问权限。 [了解详细信息](backup-rbac-rs-vault.md)

- **保护 Azure 虚拟机的所有配置**：恢复服务保管库保护基于 Resource Manager 的 VM，其中包括高级磁盘、托管磁盘和加密 VM。 将备份保管库升级到恢复服务保管库以后，即可将基于 Service Manager 的 VM 升级到基于 Resource Manager 的 VM。 在升级保管库的同时，可以保留基于 Service Manager 的 VM 恢复点，并为已升级（已启用 Resource Manager）的 VM 配置保护。 [了解详细信息](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **适用于 IaaS VM 的即时还原**：使用恢复服务保管库时，可以从 IaaS VM 还原文件和文件夹，不需还原整个 VM，从而缩短还原时间。 适用于 IaaS VM 的即时还原可供 Windows 和 Linux VM 使用。 [了解详细信息](backup-instant-restore-capability.md)

## <a name="storage-settings-in-the-recovery-services-vault"></a>恢复服务保管库中的存储设置

恢复服务保管库是用于存储在不同时间创建的备份和恢复点的实体。 恢复服务保管库还包含与受保护虚拟机关联的备份策略。

Azure 备份会自动处理保管库的存储。 请参阅如何[更改存储设置](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy)。

若要了解有关存储冗余的详细信息，请参阅有关[异地](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)和[本地](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)冗余的文章。

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>在门户中管理恢复服务保管库

在 Azure 门户中创建和管理恢复服务保管库很容易，因为备份服务可集成到其他 Azure 服务中。 这种集成意味着，可以在目标服务上下文中** 创建和管理恢复服务保管库。 例如，若要查看 VM 的恢复点，请选中该 VM，并在“操作”菜单中单击“备份”。****

![恢复服务保管库详细信息 VM](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

如果 VM 未配置备份，则会提示你配置备份。 如果已配置备份，你将看到有关 VM 的备份信息，包括还原点列表。  

![VM 中的恢复服务保管库详细信息](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

在前面的示例中，ContosoVM**** 是虚拟机的名称。 **ContosoVM-demovault** 是恢复服务保管库的名称。 不需记住用于存储恢复点的恢复服务保管库的名称，可以在虚拟机中访问该信息。  

如果一个恢复服务保管库保护多个服务器，则查看恢复服务保管库可能更符合逻辑。 可以在订阅中搜索所有恢复服务保管库，并从列表中选择一个。

以下部分包含多篇文章的链接，这些文章说明了如何在每类活动中使用恢复服务保管库。

> [!NOTE]
> 如果恢复服务保管库在 24 小时内被删除，则无法以相同的名称创建它。 请使用其他资源名称或选择其他资源组，或者在 24 小时后重试。

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

## <a name="azure-advisor"></a>Azure 顾问

[Azure 顾问](https://docs.microsoft.com/azure/advisor/)是一个个性化的云顾问，可帮助优化 Azure 的使用。 它会分析你的 Azure 使用情况，并提供及时建议来帮助优化和保护你的部署。 它提供四个类别的建议：高可用性、安全性、性能和成本。

Azure 顾问为未备份的 Vm 提供每小时[建议](https://docs.microsoft.com/azure/advisor/advisor-high-availability-recommendations#protect-your-virtual-machine-data-from-accidental-deletion)，因此，你永远不会错过重要的 vm 的备份。 你还可以通过暂停来控制建议。  你可以单击该建议，并通过指定保管库（将存储备份的位置）和备份策略（备份的备份和备份副本的保留期）来启用 Vm 上的备份。

![Azure 顾问](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="next-steps"></a>后续步骤

使用以下文章了解相关操作：</br>
[备份 IaaS VM](backup-azure-arm-vms-prepare.md)</br>
[备份 Azure 备份服务器](backup-azure-microsoft-azure-backup.md)</br>
[备份 Windows Server](backup-windows-with-mars-agent.md)
