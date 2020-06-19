---
title: 常见问题解答 - 备份 Azure VM 上的 SAP HANA 数据库
description: 本文介绍有关如何使用 Azure 备份服务备份 SAP HANA 数据库的常见问题解答。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 56f98dddb00eb3ffc87eb27da73066de807a1ee1
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701007"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>常见问题解答 - 备份 Azure VM 上的 SAP HANA 数据库

本文介绍有关如何使用 Azure 备份服务备份 SAP HANA 数据库的常见问题解答。

## <a name="backup"></a>备份

### <a name="how-many-full-backups-are-supported-per-day"></a>每天支持多少次完整备份？

每天仅支持一次完整备份。 不能在同一天触发差异备份和完整备份。

### <a name="do-successful-backup-jobs-create-alerts"></a>成功的备份作业是否会创建警报？

不是。 成功的备份作业不会生成警报。 仅针对失败的备份作业发送警报。 [此处](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)记录了门户警报的详细行为。 但是，如果你有兴趣对成功的作业启用警报，可以使用 [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>“备份作业”菜单中是否会显示计划的备份作业？

“备份作业”菜单中仅显示临时备份作业。 如需查看计划的作业，请使用 [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

### <a name="are-future-databases-automatically-added-for-backup"></a>未来的数据库会自动添加备份吗？

不会，暂不支持。

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>如果从实例中删除某个数据库，备份会出现什么情况？

如果从 SAP HANA 实例中删除某个数据库，系统仍会尝试数据库备份。 这意味着，已删除的数据库会在“备份项”下显示为不正常状态，但仍受保护。
停止保护此数据库的正确方法是对该数据库执行“Stop Backup with delete data”。

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>如果在保护数据库之后更改其名称，会出现什么行为？

已重命名的数据库被视为新数据库。 因此，该服务将此情况视为未找到数据库且备份会失败。 已重命名的数据库将显示为新数据库，并且必须对其配置以获得保护。

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>备份 Azure VM 上的 SAP HANA 数据库的先决条件是什么？

请参考[先决条件](tutorial-backup-sap-hana-db.md#prerequisites)和[预注册脚本的功能](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)部分。

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>应为 Azure 设置哪些权限才能备份 SAP HANA 数据库？

运行预注册脚本将设置允许 Azure 备份 SAP HANA 数据库所需的权限。 可在[此处](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)找到预注册脚本的更多功能。

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>将 SAP HANA 从 SDC 迁移到 MDC 后，备份是否正常进行？

请参考故障排除指南中的[说明](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#sdc-to-mdc-upgrade-with-a-change-in-sid)。

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>是否可以针对虚拟 IP（负载均衡器）而不是虚拟机设置 Azure HANA 备份？

目前，我们无法提供单独针对虚拟 IP 的解决方案。 执行此解决方案需要使用虚拟机。

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>对 SAP HANA 系统复制 (HSR) 应如何配置备份？

HSR 的主节点和辅助节点将被视为两个不相关的独立 VM。 需要在主节点上配置备份，当发生故障转移时，需要在辅助节点（现在变为主节点）上配置备份。 不会自动将备份“故障转移”到另一个节点。

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

## <a name="restore"></a>还原

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>为什么我看不到要将数据库还原到的 HANA 系统？

检查是否满足还原到目标 SAP HANA 实例所需的所有先决条件。 有关详细信息，请参阅[先决条件 - 还原 Azure VM 中的 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites)。

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>为什么我的数据库覆盖 DB 还原失败？

请确保在还原时选中“强制覆盖”选项。

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>为什么会出现“还原的源系统和目标系统不兼容”错误？

请参考 SAP HANA 说明 [1642148](https://launchpad.support.sap.com/#/notes/1642148) 以了解当前支持的还原类型。

## <a name="next-steps"></a>后续步骤

了解如何[备份在 Azure VM 上运行的 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)。
