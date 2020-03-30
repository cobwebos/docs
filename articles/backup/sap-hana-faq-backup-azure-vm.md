---
title: 常见问题解答 - 备份 Azure VM 上的 SAP HANA 数据库
description: 在本文中，了解有关使用 Azure 备份服务备份 SAP HANA 数据库的常见问题的解答。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a46c4d6cccc00452a56567880400ef5779e6aed4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155386"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>常见问题 - 在 Azure VM 上备份 SAP HANA 数据库

本文回答了有关使用 Azure 备份服务备份 SAP HANA 数据库的常见问题。

## <a name="backup"></a>备份

### <a name="how-many-full-backups-are-supported-per-day"></a>每天支持多少个完整备份？

我们每天仅支持一个完整备份。 不能在同一天触发差异备份和完整备份。

### <a name="do-successful-backup-jobs-create-alerts"></a>成功的备份作业是否会创建警报？

不是。 成功的备份作业不会生成警报。 仅针对失败的备份作业发送警报。 [此文](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)介绍了门户警报的详细行为。 但是，如果您有兴趣对成功作业有警报，则可以使用[Azure 监视器](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>“备份作业”菜单中是否会显示计划的备份作业？

“备份作业”菜单只显示临时备份作业。 对于计划作业，请使用[Azure 监视器](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

### <a name="are-future-databases-automatically-added-for-backup"></a>未来的数据库会自动添加备份吗？

否，当前不支持此功能。

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>如果我从实例中删除数据库，备份将会发生什么情况？

如果从 SAP HANA 实例中删除数据库，则仍尝试数据库备份。 这意味着，已删除的数据库会开始在“备份项”下面显示为不正常状态，但它仍受保护。****
停止保护此数据库的正确方法是使用此数据库上**删除数据的停止备份**。

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>如果我在数据库受到保护后更改它的名称，那么行为将是什么？

已重命名的数据库被视为新数据库。 因此，服务将视此情况为找不到数据库且备份失败。 重命名的数据库将显示为新数据库，必须配置为保护。

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>在 Azure VM 上备份 SAP HANA 数据库的先决条件是什么？

请参阅[先决条件](tutorial-backup-sap-hana-db.md#prerequisites)和[预注册脚本执行哪些](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)部分。

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>应该为 Azure 设置哪些权限才能备份 SAP HANA 数据库？

运行预注册脚本将设置必要的权限，以允许 Azure 备份 SAP HANA 数据库。 您可以在此处找到预注册脚本所做的更多[内容](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)。

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>将 SAP HANA 从 1.0 迁移到 2.0 后，备份会起作用吗？

请参阅故障排除指南的[这一部分](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20)。

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>是否可以针对虚拟 IP（负载均衡器）而不是虚拟机设置 Azure HANA 备份？

目前，我们无法单独针对虚拟 IP 设置解决方案。 我们需要一个虚拟机来执行解决方案。

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>我有一个 SAP HANA 系统复制 （HSR），我应该如何配置此设置的备份？

HSR 的主节点和辅助节点将被视为两个单独的、不相关的 VM。 您需要在主节点上配置备份，当发生故障转移时，需要在辅助节点（现在成为主节点）上配置备份。 没有自动"故障转移"备份到其他节点。

## <a name="restore"></a>还原

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>为什么我看不到希望还原到的 HANA 系统？

检查是否满足还原目标 SAP HANA 实例的所有先决条件。 有关详细信息，请参阅先决条件[- 在 Azure VM 中还原 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites)。

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>为什么覆盖数据库还原失败？

确保在还原时选择了 **"强制覆盖**"选项。

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>为什么我看到"用于还原的源系统和目标系统不兼容"错误？

请参阅 SAP HANA 注释[1642148，](https://launchpad.support.sap.com/#/notes/1642148)了解当前支持哪些还原类型。

## <a name="next-steps"></a>后续步骤

了解如何备份在 Azure VM 上运行的[SAP HANA 数据库](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)。
