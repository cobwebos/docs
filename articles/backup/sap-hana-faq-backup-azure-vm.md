---
title: 常见问题解答 - 备份 Azure VM 上的 SAP HANA 数据库
description: 本文介绍有关使用 Azure 备份服务备份 SAP HANA 数据库的常见问题的解答。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: d9d10e38885ba814045d8476b83671153feb7b8c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919679"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>常见问题–在 Azure Vm 上备份 SAP HANA 数据库

本文解答了有关使用 Azure 备份服务备份 SAP HANA 数据库的常见问题。

## <a name="backup"></a>备份

### <a name="how-many-full-backups-are-supported-per-day"></a>每天支持多少次完整备份？

每天仅支持一次完整备份。 不能在同一天触发差异备份和完整备份。

### <a name="do-successful-backup-jobs-create-alerts"></a>成功的备份作业是否会创建警报？

不是。 成功的备份作业不会生成警报。 仅针对失败的备份作业发送警报。 [此处](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)介绍了门户警报的详细行为。 但是，如果你有兴趣获得成功作业的警报，可以使用[Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>是否可以在 "备份作业" 菜单中看到计划的备份作业？

"备份作业" 菜单将仅显示即席备份作业。 对于计划作业，请使用[Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

### <a name="are-future-databases-automatically-added-for-backup"></a>未来的数据库会自动添加备份吗？

不能，目前不支持这种情况。

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>如果从实例中删除数据库，备份会发生什么情况呢？

如果从 SAP HANA 实例中删除数据库，则仍将尝试数据库备份。 这意味着，已删除的数据库在 "**备份项**" 下开始显示为 "不正常"，并且仍受保护。
停止保护此数据库的正确方法是对此数据库上的**删除数据执行停止备份**。

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>如果在保护数据库后更改该数据库的名称，会出现什么情况？

已重命名的数据库被视为新数据库。 因此，该服务将处理此情况，就好像找不到数据库，备份失败。 已重命名的数据库将显示为新数据库，并且必须配置为进行保护。

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>在 Azure VM 上备份 SAP HANA 数据库的先决条件是什么？

请参阅[先决条件](tutorial-backup-sap-hana-db.md#prerequisites)和[预注册脚本执行的](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)操作部分。

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>应为 Azure 设置哪些权限才能备份 SAP HANA 数据库？

运行预注册脚本会将所需的权限设置为允许 Azure 备份 SAP HANA 数据库。 你可以在[此处](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)找到预注册脚本执行的操作。

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>将 SAP HANA 从1.0 迁移到2.0 后是否会运行备份？

请参阅故障排除指南中的[此部分](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20)。

## <a name="restore"></a>还原

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>为什么我看不到要将数据库还原到的 HANA 系统？

检查是否满足还原到目标 SAP HANA 实例的所有先决条件。 有关详细信息，请参阅[先决条件-Restore SAP HANA AZURE VM 中的数据库](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites)。

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>为什么为数据库覆盖数据库还原失败？

确保还原时选择了 "**强制覆盖**" 选项。

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>为什么会看到 "还原的源系统和目标系统不兼容" 错误？

请参阅 SAP HANA 说明[1642148](https://launchpad.support.sap.com/#/notes/1642148) ，查看当前支持的还原类型。

## <a name="next-steps"></a>后续步骤

了解如何备份在 Azure Vm 上运行的[SAP HANA 数据库](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)。
