---
title: 管理 Azure VM 上的已备份 SAP HANA 数据库
description: 本文介绍了管理和监视在 Azure 虚拟机上运行的 SAP HANA 数据库时执行的常见任务。
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 89fd7f23163d301817e767771257d9bc6f4ed526
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480056"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>管理和监视已备份的 SAP HANA 数据库

本文介绍对 Azure 虚拟机 (VM) 上运行的、已通过 [Azure 备份](https://docs.microsoft.com/azure/backup/backup-overview)服务备份到 Azure 备份恢复服务保管库的 SAP HANA 数据库进行管理和监视所要执行的常见任务。 其中将会介绍如何监视作业和警报、触发按需备份、编辑策略、停止和恢复数据库保护，以及从备份中取消注册 VM。

如果尚未为 SAP HANA 数据库配置备份，请参阅[备份 Azure VM 上的 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)。

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>在门户中监视手动备份作业

Azure 备份在 Azure 门户上的“备份作业”部分中显示所有手动触发的作业。 

![“备份作业”部分](./media/sap-hana-db-manage/backup-jobs.png)

此门户中显示的作业包括数据库发现和注册，以及备份和还原操作。 此部分中不显示计划的作业，包括日志备份。 此处也不显示从 SAP HANA 本机客户端 (Studio/ Cockpit/ DBA Cockpit) 手动触发的备份。

![“备份作业”列表](./media/sap-hana-db-manage/backup-jobs-list.png)

若要详细了解监视，请参阅[在 Azure 门户中进行监视](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)和[使用 Azure Monitor 进行监视](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

## <a name="view-backup-alerts"></a>查看备份警报

警报是监视 SAP HANA 数据库备份的一种简单方法。 警报可以帮助你集中精力处理最关心的事件，而不会在备份生成的众多事件中迷失方向。 Azure 备份允许你设置警报，可以如下所述来监视警报：

* 登录 [Azure 门户](https://portal.azure.com/)。
* 在保管库仪表板中，选择“备份警报”。 

  ![保管库仪表板上的备份警报](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* 你将能够看到警报：

  ![备份警报的列表](./media/sap-hana-db-manage/backup-alerts-list.png)

* 单击警报以查看更多详细信息：

  ![警报详细信息](./media/sap-hana-db-manage/alert-details.png)

如今，Azure 备份允许通过电子邮件发送警报。 这些警报：

* 只要出现备份失败就会触发。
* 在数据库级别按错误代码进行合并。
* 仅在数据库首次备份失败时发送。

若要详细了解监视，请参阅[在 Azure 门户中进行监视](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)和[使用 Azure Monitor 进行监视](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

## <a name="management-operations"></a>管理操作

Azure 备份通过其支持的大量管理操作，使备份的 SAP HANA 数据库的管理变得很容易。 以下各部分详细讨论了这些更改。

### <a name="run-an-on-demand-backup"></a>运行按需备份

备份按照策略计划运行。 可以如下所述运行按需备份：

1. 在保管库菜单中，单击“备份项”  。
2. 在“备份项”  中，选择运行 SAP HANA 数据库的 VM，然后单击“立即备份”  。
3. 在“立即备份”中，使用日历控件选择恢复点的最后保留日期  。 然后单击“确定”  。
4. 监视门户通知。 可以在保管库仪表板 >“备份作业” **“进行中”监视作业进度。**  >   创建初始备份可能需要一些时间，具体取决于你的数据库的大小。

### <a name="hana-native-client-integration"></a>HANA 本机客户端集成

现在，在 "**备份项**" 页上，从任何 HANA 本机客户端触发的按需完全备份将显示为完整备份。

![上次备份运行时间](./media/sap-hana-db-manage/last-backups.png)

这些临时完整备份还将显示在还原的还原点列表中。

![还原点的列表](./media/sap-hana-db-manage/list-restore-points.png)

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>在启用了 Azure 备份的数据库上运行 SAP HANA native client 备份

如果要使用 Azure 备份进行备份的数据库的本地备份（使用 HANA Studio/考核中心），请执行以下操作：

1. 等待数据库的所有完整备份或日志备份完成。 检查 SAP HANA Studio/考核中心中的状态。
2. 禁用日志备份，并将备份目录设置为相关数据库的文件系统。
3. 为此，请双击 " **systemdb** > **配置** > " "**选择数据库** > **筛选器（日志）**"。
4. 将**enable_auto_log_backup**设置为 "**否**"。
5. 将**log_backup_using_backint**设置为**False**。
6. 采用数据库的按需完整备份。
7. 等待完整备份和目录备份完成。
8. 将以前的设置还原为适用于 Azure 的设置：
   * 将**enable_auto_log_backup**设置为 **"是"**。
   * 将**log_backup_using_backint**设置为**True**。

### <a name="change-policy"></a>更改策略

可以更改 SAP HANA 备份项的基础策略。

* 在保管库仪表板中转到 "**备份项**"：

  ![选择备份项目](./media/sap-hana-db-manage/backup-items.png)

* 选择**AZURE VM 中的 SAP HANA**

  ![选择 Azure VM 中的 SAP HANA](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* 选择要更改其基础策略的备份项目
* 单击现有备份策略

  ![选择现有的备份策略](./media/sap-hana-db-manage/existing-backup-policy.png)

* 更改策略，从列表中进行选择。 如果需要，请[创建新的备份策略](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database#create-a-backup-policy)。

  ![从下拉列表中选择策略](./media/sap-hana-db-manage/choose-backup-policy.png)

* 保存更改

  ![保存更改](./media/sap-hana-db-manage/save-changes.png)

* 策略修改将影响所有关联的备份项，并触发相应的**配置保护**作业。

>[!NOTE]
> 在保留期内进行的任何更改将应用到新恢复点，并以追溯方式应用到所有旧恢复点。
>
> 增量备份策略不能用于 SAP HANA 的数据库。 对于这些数据库，目前不支持增量备份。

### <a name="modify-policy"></a>修改策略

修改策略以更改备份类型、频率和保持期。

>[!NOTE]
>在保留期内的任何更改都将应用于所有旧恢复点（新的恢复点除外）。

1. 在保管库仪表板中，请参阅**管理 > 备份策略**并选择要编辑的策略。

   ![选择要编辑的策略](./media/sap-hana-db-manage/manage-backup-policies.png)

1. 选择“修改”  。

   ![选择修改](./media/sap-hana-db-manage/modify-policy.png)

1. 选择备份类型的频率。

   ![选择备份频率](./media/sap-hana-db-manage/choose-frequency.png)

策略修改将影响所有关联的备份项，并触发相应的**配置保护**作业。

### <a name="inconsistent-policy"></a>不一致的策略

有时，修改策略操作可能导致某些备份项的策略版本**不一致**。 如果在触发修改策略操作后对备份项执行相应的**配置保护**作业失败，则会出现这种不一致。 这种情况会反映在备份项视图中，如下所示：

![不一致的策略](./media/sap-hana-db-manage/inconsistent-policy.png)

只需单击一下鼠标即可修复所有受影响项的策略版本：

![修复策略版本](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>停止对 SAP HANA 数据库的保护

可以通过以下几种方式停止保护 SAP HANA 数据库：

* 停止所有将来的备份作业并删除所有恢复点。
* 停止所有将来的备份作业，且恢复点保留不变。

如果选择保留恢复点，请记住以下详细内容：

* 所有恢复点都将永久保持不变，所有删除操作都应在停止保护时停止，并保留数据。
* 需要为受保护的实例和消耗的存储空间付费。 有关详细信息，请参阅 [Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)。
* 如果在不停止备份的情况下删除数据源，则新备份会失败。

停止数据库的保护：

* 在保管库仪表板中，选择“备份项”。****
* 在 "**备份管理类型**" 下，选择**Azure VM 中的 SAP HANA**

  ![选择 Azure VM 中的 SAP HANA](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* 选择要在其上停止保护的数据库：

  ![选择数据库以停止保护](./media/sap-hana-db-manage/select-database.png)

* 在数据库菜单中，选择“停止备份”****。

  ![选择停止备份](./media/sap-hana-db-manage/stop-backup.png)

* 在“停止备份”菜单中，选择是要保留还是删除数据。**** 根据需要提供原因和注释。

  ![选择保留或删除数据](./media/sap-hana-db-manage/retain-backup-data.png)

* 选择 "**停止备份**"。

### <a name="resume-protection-for-an-sap-hana-database"></a>恢复 SAP HANA 数据库的保护

停止对 SAP HANA 数据库的保护时，如果选择 "**保留备份数据**" 选项，则可以在以后恢复保护。 如果不保留备份的数据，则将无法恢复保护。

继续保护 SAP HANA 数据库：

* 打开备份项，并选择“恢复备份”。****

   ![选择恢复备份](./media/sap-hana-db-manage/resume-backup.png)

* 在“备份策略”菜单中选择策略，然后选择“保存”。********

### <a name="upgrading-from-sap-hana-10-to-20"></a>从 SAP HANA 1.0 升级到2。0

了解[从 SAP HANA 1.0 升级到2.0 后](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20)，如何继续 SAP HANA 数据库的备份。

### <a name="upgrading-without-a-sid-change"></a>升级而不更改 SID

了解如何[在升级后继续备份其 SID 尚未更改](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change)的 SAP HANA 数据库。

### <a name="unregister-an-sap-hana-instance"></a>取消注册 SAP HANA 实例

在禁用保护之后但在删除保管库之前取消注册 SAP HANA 实例：

* 在保管库仪表板上的 "**管理**" 下，选择 "**备份基础结构**"。

   ![选择“备份基础结构”。](./media/sap-hana-db-manage/backup-infrastructure.png)

* **在 AZURE VM 中**选择**备份管理类型**作为工作负荷

   ![在 Azure VM 中选择备份管理类型作为工作负荷](./media/sap-hana-db-manage/backup-management-type.png)

* 在 "**受保护的服务器**" 中，选择要注销的实例。 若要删除保管库，必须取消注册所有服务器/实例。

* 右键单击受保护的实例，然后选择 "**注销**"。

   ![选择注销](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>在 SAP HANA server VM 上重新注册扩展

有时，VM 上的工作负荷扩展可能会因为某种原因而受到影响。 在这种情况下，在该 VM 上触发的所有操作将开始失败。 然后，可能需要在 VM 上重新注册该扩展。 重新注册操作会在 VM 上重新安装工作负荷备份扩展，使操作能够继续。

请谨慎使用此选项：当在具有正常扩展的 VM 上触发时，此操作会导致扩展重新启动。 这可能会导致所有正在进行的作业失败。 在触发重新注册操作之前，请检查是否有一个或多个[症状](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures)。

## <a name="next-steps"></a>后续步骤

* 了解如何[在备份数据库时排查常见问题 SAP HANA。](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
