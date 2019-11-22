---
title: 在 Azure Vm 上管理备份的 SAP HANA 数据库
description: 本文介绍管理和监视 Azure 虚拟机上运行的 SAP HANA 数据库的常见任务。
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: f76054c7c78c55a9754975267ee4fa3caab968a3
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74288342"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>管理和监视备份 SAP HANA 数据库

本文介绍了在 Azure 虚拟机（VM）上运行的管理和监视 SAP HANA 数据库的常见任务，以及由[Azure 备份](https://docs.microsoft.com/azure/backup/backup-overview)服务备份到 Azure 备份恢复服务保管库的数据库。 你将了解如何监视作业和警报，触发按需备份，编辑策略，停止和恢复数据库保护并从备份中取消注册 VM。

如果尚未为 SAP HANA 数据库配置备份，请参阅[在 Azure vm 上备份 SAP HANA 的数据库](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)。

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>在门户中监视手动备份作业

Azure 备份在 Azure 门户上的 "**备份作业**" 部分显示所有手动触发的作业。

![备份作业部分](./media/sap-hana-db-manage/backup-jobs.png)

在此门户中看到的作业包括数据库发现和注册以及备份和还原操作。 此部分未显示计划作业，包括日志备份。 不会在此处显示从 SAP HANA 本机客户端（Studio/考核中心考核中心）手动触发的备份。

![备份作业列表](./media/sap-hana-db-manage/backup-jobs-list.png)

若要了解有关监视的详细信息，请参阅[Azure 门户中的 "监视" 和 "](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) [使用 Azure Monitor 进行监视](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)"。

## <a name="view-backup-alerts"></a>查看备份警报

警报是监视 SAP HANA 数据库的备份的一种简单方法。 警报可帮助你专注于你最关注的事件，而无需在备份生成的多个事件中丢失。 Azure 备份允许设置警报，并可按如下所示进行监视：

* 登录到 [Azure 门户](https://portal.azure.com/)。
* 在保管库仪表板上，选择 "**备份警报**"。

  ![保管库仪表板上的备份警报](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* 你将能够看到警报：

  ![备份警报列表](./media/sap-hana-db-manage/backup-alerts-list.png)

* 单击警报以查看更多详细信息：

  ![警报详细信息](./media/sap-hana-db-manage/alert-details.png)

如今，Azure 备份允许通过电子邮件发送警报。 这些警报包括：

* 触发所有备份失败。
* 按错误代码在数据库级别合并。
* 仅在数据库首次备份失败时发送。

给了解有关监视的详细信息，请参阅[Azure 门户中的 "监视" 和 "](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) [使用 Azure Monitor 进行监视](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)"。

## <a name="management-operations"></a>管理操作

使用 Azure 备份，可以轻松地管理已备份的 SAP HANA 数据库，同时提供丰富的管理操作。 以下各节将更详细地讨论这些操作。

### <a name="run-an-ad-hoc-backup"></a>运行即席备份

备份按照策略计划运行。 可以按需运行备份，如下所示：

1. 在保管库菜单中，单击“备份项”。
2. 在 "**备份项目**" 中，选择运行 SAP HANA 数据库的 VM，然后单击 "**立即备份**"。
3. 在“立即备份”中，使用日历控件选择恢复点的最后保留日期。 然后单击“确定”。
4. 监视门户通知。 可以在保管库仪表板 >“备份作业” **“进行中”监视作业进度。**  >  创建初始备份可能需要一些时间，具体取决于数据库的大小。

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>在启用了 Azure 备份的数据库上运行 SAP HANA native client 备份

如果要使用 Azure 备份进行备份的数据库的本地备份（使用 HANA Studio/考核中心），请执行以下操作：

1. 等待数据库的所有完整备份或日志备份完成。 检查 SAP HANA Studio/考核中心中的状态。
2. 禁用日志备份，并将备份目录设置为相关数据库的文件系统。
3. 为此，请双击 " **systemdb** > **配置** > **选择" 数据库** > **筛选器（日志）** "。
4. 将**enable_auto_log_backup**设置为 "**否**"。
5. 将**log_backup_using_backint**设置为**False**。
6. 对数据库进行临时完整备份。
7. 等待完整备份和目录备份完成。
8. 将以前的设置还原为适用于 Azure 的设置：
   * 将**enable_auto_log_backup**设置为 **"是"** 。
   * 将**log_backup_using_backint**设置为**True**。

### <a name="edit-underlying-policy"></a>编辑底层策略

修改策略以更改备份频率或保持期：

* 在保管库仪表板中，请参阅**管理** > **备份策略**

  ![保管库仪表板中的备份策略](./media/sap-hana-db-manage/backup-policies-dashboard.png)

* 选择要编辑的策略：

  ![备份策略列表](./media/sap-hana-db-manage/backup-policies-list.png)

  ![备份策略详细信息](./media/sap-hana-db-manage/backup-policy-details.png)

>[!NOTE]
> 保留期内的任何更改都将应用于所有旧恢复点（新的恢复点除外）。
>
> 增量备份策略不能用于 SAP HANA 的数据库。 对于这些数据库，目前不支持增量备份。

### <a name="stop-protection-for-an-sap-hana-database"></a>停止保护 SAP HANA 数据库

可以通过以下几种方式停止保护 SAP HANA 数据库：

* 停止所有将来的备份作业并删除所有恢复点。
* 停止所有将来的备份作业，并将恢复点保留不变。

如果选择保留恢复点，请记住以下详细信息：

* 所有恢复点都将永久保持原样，所有修剪都应在停止保护时停止，并保留数据。
* 将为受保护的实例和使用的存储付费。 有关详细信息，请参阅 [Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)。
* 如果在不停止备份的情况下删除数据源，则新备份将会失败。

停止数据库的保护：

* 在保管库仪表板上，选择 "**备份项**"。
* 在 "**备份管理类型**" 下，选择**Azure VM 中的 SAP HANA**

  ![选择 Azure VM 中的 SAP HANA](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* 选择要在其上停止保护的数据库：

  ![选择数据库以停止保护](./media/sap-hana-db-manage/select-database.png)

* 在数据库菜单中，选择“停止备份”。

  ![选择停止备份](./media/sap-hana-db-manage/stop-backup.png)

* 在 "**停止备份**" 菜单中，选择是保留还是删除数据。 如果需要，请提供原因和注释。

  ![选择保留或删除数据](./media/sap-hana-db-manage/retain-backup-data.png)

* 选择 "**停止备份**"。

### <a name="resume-protection-for-an-sap-hana-database"></a>恢复 SAP HANA 数据库的保护

停止对 SAP HANA 数据库的保护时，如果选择 "**保留备份数据**" 选项，则可以在以后恢复保护。 如果不保留备份的数据，则将无法恢复保护。

继续保护 SAP HANA 数据库：

* 打开备份项，然后选择 "**恢复备份**"。

   ![选择恢复备份](./media/sap-hana-db-manage/resume-backup.png)

* 在“备份策略”菜单中选择策略，然后选择“保存”。

### <a name="upgrading-from-sap-hana-10-to-20"></a>从 SAP HANA 1.0 升级到2。0

了解[从 SAP HANA 1.0 升级到2.0 后](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20)，如何继续 SAP HANA 数据库的备份。

### <a name="upgrading-without-a-sid-change"></a>升级而不更改 SID

了解如何[在升级后继续备份其 SID 尚未更改](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change)的 SAP HANA 数据库。

### <a name="unregister-an-sap-hana-database"></a>取消注册 SAP HANA 数据库

在禁用保护之后但在删除保管库之前取消注册 SAP HANA 实例：

* 在保管库仪表板上的 "**管理**" 下，选择 "**备份基础结构**"。

   ![选择“备份基础结构”。](./media/sap-hana-db-manage/backup-infrastructure.png)

* **在 AZURE VM 中**选择**备份管理类型**作为工作负荷

   ![在 Azure VM 中选择备份管理类型作为工作负荷](./media/sap-hana-db-manage/backup-management-type.png)

* 在 "**受保护的服务器**" 中，选择要注销的实例。 若要删除保管库，必须取消注册所有服务器/实例。

* 右键单击受保护的实例，然后选择 "**注销**"。

   ![选择注销](./media/sap-hana-db-manage/unregister.png)

## <a name="next-steps"></a>后续步骤

* 了解如何[在备份数据库时排查常见问题 SAP HANA。](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)

