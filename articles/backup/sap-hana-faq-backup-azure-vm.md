---
title: 常见问题解答 - 备份 Azure VM 上的 SAP HANA 数据库
description: 本文介绍有关如何使用 Azure 备份服务备份 SAP HANA 数据库的常见问题解答。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: ddc4af9a164de3a822e8aebd6c0a4db769ec62a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262576"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>常见问题解答 - 备份 Azure VM 上的 SAP HANA 数据库

本文介绍有关如何使用 Azure 备份服务备份 SAP HANA 数据库的常见问题解答。

## <a name="backup"></a>备份

### <a name="how-many-full-backups-are-supported-per-day"></a>每天支持多少次完整备份？

每天仅支持一次完整备份。 不能在同一天触发差异备份和完整备份。

### <a name="do-successful-backup-jobs-create-alerts"></a>成功的备份作业是否会创建警报？

不是。 成功的备份作业不会生成警报。 仅针对失败的备份作业发送警报。 [此处](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)记录了门户警报的详细行为。 但是，如果你有兴趣获得成功作业的警报，可以使用[Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>“备份作业”菜单中是否会显示计划的备份作业？

“备份作业”菜单中仅显示临时备份作业。 如需查看计划的作业，请使用 [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

### <a name="are-future-databases-automatically-added-for-backup"></a>未来的数据库会自动添加备份吗？

不能，目前不支持这种情况。

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>如果从实例中删除某个数据库，备份会出现什么情况？

如果从 SAP HANA 实例中删除某个数据库，系统仍会尝试数据库备份。 这意味着，已删除的数据库会在“备份项”下显示为不正常状态，但仍受保护。
停止保护此数据库的正确方法是对该数据库执行“Stop Backup with delete data”。

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>如果在保护数据库之后更改其名称，会出现什么行为？

已重命名的数据库被视为新数据库。 因此，该服务将处理此情况，就好像找不到数据库，备份失败。 已重命名的数据库将显示为新数据库，并且必须对其配置以获得保护。

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>备份 Azure VM 上的 SAP HANA 数据库的先决条件是什么？

请参考[先决条件](tutorial-backup-sap-hana-db.md#prerequisites)和[预注册脚本的功能](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)部分。

### <a name="what-permissions-should-be-set-so-azure-can-back-up-sap-hana-databases"></a>应设置哪些权限以便 Azure 可以备份 SAP HANA 数据库？

运行预注册脚本将设置允许 Azure 备份 SAP HANA 数据库所需的权限。 可在[此处](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)找到预注册脚本的更多功能。

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>将 SAP HANA 从 SDC 迁移到 MDC 后，备份是否正常进行？

请参考故障排除指南中的[说明](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#sdc-to-mdc-upgrade-with-a-change-in-sid)。

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>是否可以针对虚拟 IP（负载均衡器）而不是虚拟机设置 Azure HANA 备份？

目前，我们无法对虚拟 IP 单独设置解决方案。 执行此解决方案需要使用虚拟机。

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>如何将按需备份移动到本地文件系统而不是 Azure 保管库？

1. 等待当前正在运行的备份在目标数据库上完成（可从 studio 检查是否已完成）。
1. 按照以下步骤禁用日志备份并将目录备份设置为所需 DB 的文件系统：
1. 双击“SYSTEMDB” -> “配置” -> “选择数据库” -> “筛选器(日志)”   。
    1. 将 enable_auto_log_backup 设置为“否”
    1. 将 log_backup_using_backint 设置为 false
1. 在所需的数据库上执行按需备份，并等待备份和目录备份完成。
1. 恢复到以前的设置，以允许备份流向 Azure 保管库：
    1. 将 enable_auto_log_backup 设置为“是”
    1. 将 log_backup_using_backint 设置为 true

### <a name="how-can-i-use-sap-hana-backup-with-my-hana-replication-set-up"></a>如何在 HANA 复制设置中使用 SAP HANA 备份？

目前，Azure 备份不能了解 HSR 设置。 这意味着，HSR 的主节点和辅助节点将被视为两个独立的、不相关的 Vm。 首先需要在主节点上配置备份。 发生故障转移时，必须在辅助节点上配置备份（现在会成为主节点）。 不会自动将备份故障转移到另一个节点。

若要在任意给定时间点对活动（主）节点中的数据进行备份，可以将**保护切换**到辅助节点，这在故障转移后就成为了主节点。

若要执行此**交换机保护**，请执行以下步骤：

- 在主副本上[停止保护](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)（保留数据）
- 在辅助节点上运行[预注册脚本](https://aka.ms/scriptforpermsonhana)
- 发现辅助节点上[的数据库](tutorial-backup-sap-hana-db.md#discover-the-databases)并在其上[配置备份](tutorial-backup-sap-hana-db.md#configure-backup)

需要在每次故障转移后手动执行这些步骤。 除了 Azure 门户之外，还可以通过命令行/HTTP REST 执行这些步骤。 若要自动执行这些步骤，可以使用 Azure runbook。

下面是有关如何执行**交换机保护**的详细示例：

在此示例中，HSR 设置中有两个节点-节点1（主节点）和节点2（辅助节点）。  在节点1上配置备份。 如上所述，不要尝试在节点2上配置备份。

发生第一次故障转移时，节点2变成主节点。 那么：

1. 利用 "保留数据" 选项停止对节点1（以前的主节点）的保护。
1. 在节点2上运行预注册脚本（现在是主节点）。
1. 发现节点2上的数据库，分配备份策略并配置备份。

然后，在节点2上触发第一次完整备份，完成后，将启动日志备份。

发生下一次故障转移时，节点1再次成为主节点，节点2变为辅助节点。 现在，重复此过程：

1. 停止保护具有 "保留数据" 选项的节点2。
1. 在节点1上运行预注册脚本（再次成为主节点）
1. 然后，在节点1上[恢复备份](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database)，并具有所需的策略（因为之前在节点1上停止了备份）。

然后，将再次在节点1上触发完整备份，完成后，将启动日志备份。

## <a name="restore"></a>还原

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>为什么我看不到要将数据库还原到的 HANA 系统？

检查是否满足还原到目标 SAP HANA 实例所需的所有先决条件。 有关详细信息，请参阅[先决条件 - 还原 Azure VM 中的 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites)。

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>为什么我的数据库覆盖 DB 还原失败？

请确保在还原时选中“强制覆盖”选项。

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>为什么会出现“还原的源系统和目标系统不兼容”错误？

请参考 SAP HANA 说明 [1642148](https://launchpad.support.sap.com/#/notes/1642148) 以了解当前支持的还原类型。

### <a name="can-i-use-a-backup-of-a-database-running-on-sles-to-restore-to-an-rhel-hana-system-or-vice-versa"></a>是否可以使用在 SLES 上运行的数据库的备份还原到 RHEL HANA 系统，反之亦然？

是的，可以使用在 SLES 上运行的 HANA 数据库上触发的流式处理备份将其还原到 RHEL HANA 系统，反之亦然。 也就是说，使用流式备份可以进行跨操作系统还原。 但是，必须确保要还原到的 HANA 系统以及用于还原的 HANA 系统都与 SAP 兼容，以便进行还原。 请参阅 SAP HANA 说明[1642148](https://launchpad.support.sap.com/#/notes/1642148)查看哪些还原类型是兼容的。

## <a name="next-steps"></a>后续步骤

了解如何[备份在 Azure VM 上运行的 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)。
