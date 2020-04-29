---
title: 常见问题解答 - 备份 Azure VM 上的 SAP HANA 数据库
description: 本文解答有关使用 Azure 备份服务备份 SAP HANA 数据库的常见问题。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a46c4d6cccc00452a56567880400ef5779e6aed4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80155386"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>常见问题 - 备份基于 Azure VM 的 SAP HANA 数据库

本文解答有关使用 Azure 备份服务备份 SAP HANA 数据库的常见问题。

## <a name="backup"></a>备份

### <a name="how-many-full-backups-are-supported-per-day"></a>每天支持多少次完整备份？

每天仅支持一次完整备份。 不能在同一天触发差异备份和完整备份。

### <a name="do-successful-backup-jobs-create-alerts"></a>成功的备份作业是否会创建警报？

不是。 成功的备份作业不会生成警报。 仅针对失败的备份作业发送警报。 [此文](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)介绍了门户警报的详细行为。 但是，如果希望在作业成功的情况下也收到警报，可以使用 [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>“备份作业”菜单中是否会显示计划的备份作业？

“备份作业”菜单只显示临时备份作业。 对于计划的作业，请使用 [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

### <a name="are-future-databases-automatically-added-for-backup"></a>未来的数据库会自动添加备份吗？

否，暂不支持该功能。

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>如果从实例中删除数据库，备份会发生什么情况？

如果从 SAP HANA 实例中删除某个数据库，仍会尝试数据库备份。 这意味着，已删除的数据库会开始在“备份项”下面显示为不正常状态，但它仍受保护。 
停止保护此数据库的正确方法是针对此数据库执行“停止备份并删除数据”操作。 

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>如果在保护数据库后更改其名称，会出现怎样的行为？

已重命名的数据库被视为新数据库。 因此，服务会将此情况视为找不到数据库，同时会使备份失败。 已重命名的数据库会显示为新数据库，必须对其进行保护性配置。

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>备份 Azure VM 上的 SAP HANA 数据库的先决条件是什么？

请参阅[先决条件](tutorial-backup-sap-hana-db.md#prerequisites)和[预注册脚本的功能](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)部分。

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>应为 Azure 设置哪些权限才能备份 SAP HANA 数据库？

运行预注册脚本即可设置所需的权限，这样 Azure 就可以备份 SAP HANA 数据库。 可以在[此处](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)详细了解注册前脚本的功能。

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>将 SAP HANA 从 1.0 迁移到 2.0 后备份是否有效？

请参阅故障排除指南的[此部分](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20)。

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>是否可以针对虚拟 IP （负载均衡器）而不是虚拟机设置 Azure HANA 备份？

目前，我们不能对单独的虚拟 IP 设置解决方案。 需要虚拟机来执行解决方案。

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>我有 SAP HANA 系统复制（HSR），那么应该如何为此设置配置备份？

将 HSR 的主节点和辅助节点视为两个与不相关的独立 Vm。 需要在主节点上配置备份，当发生故障转移时，需要在辅助节点上配置备份（现在会成为主节点）。 不会对其他节点进行自动的 "故障转移" 备份。

## <a name="restore"></a>还原

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>为什么我看不到要将数据库还原到的 HANA 系统？

检查是否满足还原到目标 SAP HANA 实例的所有先决条件。 有关详细信息，请参阅[先决条件-Restore SAP HANA AZURE VM 中的数据库](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites)。

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>为什么为数据库覆盖数据库还原失败？

确保还原时选择了 "**强制覆盖**" 选项。

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>为什么会看到 "还原的源系统和目标系统不兼容" 错误？

请参阅 SAP HANA 说明[1642148](https://launchpad.support.sap.com/#/notes/1642148) ，查看当前支持的还原类型。

## <a name="next-steps"></a>后续步骤

了解如何备份在 Azure Vm 上运行的[SAP HANA 数据库](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)。
