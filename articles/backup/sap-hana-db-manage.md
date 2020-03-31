---
title: 在 Azure VM 上管理备份的 SAP HANA 数据库
description: 在本文中，了解用于管理和监视在 Azure 虚拟机上运行的 SAP HANA 数据库的常见任务。
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 89fd7f23163d301817e767771257d9bc6f4ed526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480056"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>管理和监视已备份的 SAP HANA 数据库

本文介绍用于管理和监视在 Azure 虚拟机 （VM） 上运行并由[Azure 备份](https://docs.microsoft.com/azure/backup/backup-overview)服务备份到 Azure 备份恢复服务保管库的 SAP HANA 数据库的常见任务。 您将学习如何监视作业和警报、触发按需备份、编辑策略、停止和恢复数据库保护以及取消注册 VM 的备份。

如果尚未为 SAP HANA 数据库配置备份，请参阅在[Azure VM 上备份 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)。

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>在门户中监视手动备份作业

Azure 备份在 Azure 门户的 **"备份作业**"部分中显示所有手动触发的作业。

![备份作业部分](./media/sap-hana-db-manage/backup-jobs.png)

此门户中显示的作业包括数据库发现和注册，以及备份和还原操作。 本节中未显示计划作业（包括日志备份）。 来自 SAP HANA 本机客户端（工作室/驾驶舱/DBA 驾驶舱）手动触发的备份也不会在此处显示。

![备份作业列表](./media/sap-hana-db-manage/backup-jobs-list.png)

要了解有关监视的更多内容，请使用[Azure 门户转到监视](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)和[使用 Azure 监视器进行监视](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

## <a name="view-backup-alerts"></a>查看备份警报

警报是监视 SAP HANA 数据库备份的一种简单方法。 警报可帮助您专注于您最关心的事件，而不会在备份生成的众多事件中丢失。 Azure 备份允许您设置警报，并且可以按照如下方式监视警报：

* 登录到 Azure[门户](https://portal.azure.com/)。
* 在保管库仪表板上，选择 **"备份警报**"。

  ![保管库仪表板上的备份警报](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* 您将能够看到警报：

  ![备份警报列表](./media/sap-hana-db-manage/backup-alerts-list.png)

* 单击警报以查看更多详细信息：

  ![警报详细信息](./media/sap-hana-db-manage/alert-details.png)

如今，Azure 备份允许通过电子邮件发送警报。 这些警报包括：

* 只要出现备份失败就会触发。
* 在数据库级别按错误代码进行合并。
* 仅在数据库首次备份失败时发送。

要了解有关监视的更多内容，请使用[Azure 门户转到监视](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)，并使用 Azure 监视器进行[监视](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)。

## <a name="management-operations"></a>管理操作

Azure 备份通过支持的大量管理操作，使备份的 SAP HANA 数据库的管理变得简单。 以下各节将更详细地讨论这些操作。

### <a name="run-an-on-demand-backup"></a>运行按需备份

备份根据策略计划运行。 您可以按需运行备份，如下所示：

1. 在保管库菜单中，单击“备份项”****。
2. 在**备份项**中，选择运行 SAP HANA 数据库的 VM，然后单击 **"立即备份**"。
3. 在“立即备份”中，使用日历控件选择恢复点的最后保留日期****。 然后单击“确定”****。
4. 监视门户通知。 您可以在保管库仪表板>**正在进行的备份作业** > **中**监视作业进度。 根据数据库的大小，创建初始备份可能需要一段时间。

### <a name="hana-native-client-integration"></a>HANA 本机客户端集成

现在，从任何 HANA 本机客户端触发的按需完整备份将在 **"备份项目"** 页上作为完整备份显示。

![上次备份运行](./media/sap-hana-db-manage/last-backups.png)

这些临时完整备份也会显示在还原点的列表中。

![还原点列表](./media/sap-hana-db-manage/list-restore-points.png)

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>在启用 Azure 备份的数据库上运行 SAP HANA 本机客户端备份

如果要获取正在使用 Azure 备份备份的数据库的本地备份（使用 HANA Studio / 驾驶舱），请执行以下操作：

1. 等待数据库完成的任何完整备份或日志备份。 检查 SAP HANA 工作室/驾驶舱中的状态。
2. 禁用日志备份，并将备份目录设置为相关数据库的文件系统。
3. 为此，双击**系统db** > **配置** > **选择数据库** > **筛选器（日志）。**
4. **enable_auto_log_backup**设置为 **"否**"。
5. **将log_backup_using_backint**设置为**false**。
6. 按需对数据库进行完整备份。
7. 等待完整备份和目录备份完成。
8. 将以前的设置还原到 Azure 的设置：
   * **将enable_auto_log_backup**设置为 **"是**"。
   * **将log_backup_using_backint**设置为 **"真**"。

### <a name="change-policy"></a>更改策略

您可以更改 SAP HANA 备份项的基础策略。

* 在保管库仪表板中，转到 **"备份项目**" ：

  ![选择备份项目](./media/sap-hana-db-manage/backup-items.png)

* **在 Azure VM 中选择 SAP HANA**

  ![在 Azure VM 中选择 SAP HANA](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* 选择要更改其基础策略的备份项
* 单击现有备份策略

  ![选择现有备份策略](./media/sap-hana-db-manage/existing-backup-policy.png)

* 更改策略，从列表中选择。 如果需要[，创建新的备份策略](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database#create-a-backup-policy)。

  ![从下拉列表中选择策略](./media/sap-hana-db-manage/choose-backup-policy.png)

* 保存更改

  ![保存更改](./media/sap-hana-db-manage/save-changes.png)

* 策略修改将影响所有关联的备份项，并触发相应的**配置保护**作业。

>[!NOTE]
> 在保留期内进行的任何更改将应用到新恢复点，并以追溯方式应用到所有旧恢复点。
>
> 增量备份策略不能用于 SAP HANA 数据库。 这些数据库当前不支持增量备份。

### <a name="modify-policy"></a>修改策略

修改策略以更改备份类型、频率和保留范围。

>[!NOTE]
>保留期的任何更改都将追溯应用于除新恢复点之外的所有旧恢复点。

1. 在保管库仪表板中，转到 **"管理>备份策略**并选择要编辑的策略。

   ![选择要编辑的策略](./media/sap-hana-db-manage/manage-backup-policies.png)

1. 选择“修改”。****

   ![选择"修改"](./media/sap-hana-db-manage/modify-policy.png)

1. 选择备份类型的频率。

   ![选择备份频率](./media/sap-hana-db-manage/choose-frequency.png)

策略修改将影响所有相关的备份项，并触发相应的**配置保护**作业。

### <a name="inconsistent-policy"></a>不一致的策略

有时，修改策略操作可能会导致某些备份项的策略版本**不一致**。 如果在触发修改策略操作后对备份项执行相应的**配置保护**作业失败，则会出现这种不一致。 这种情况会反映在备份项视图中，如下所示：

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
* 在**备份管理类型**下，**在 Azure VM 中选择 SAP HANA**

  ![在 Azure VM 中选择 SAP HANA](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* 选择要停止保护的数据库：

  ![选择数据库以停止保护](./media/sap-hana-db-manage/select-database.png)

* 在数据库菜单中，选择“停止备份”****。

  ![选择停止备份](./media/sap-hana-db-manage/stop-backup.png)

* 在“停止备份”菜单中，选择是要保留还是删除数据。**** 根据需要提供原因和注释。

  ![选择保留或删除数据](./media/sap-hana-db-manage/retain-backup-data.png)

* 选择 **"停止备份**"。

### <a name="resume-protection-for-an-sap-hana-database"></a>恢复 SAP HANA 数据库的保护

停止 SAP HANA 数据库的保护时，如果选择"**保留备份数据"** 选项，则可以稍后恢复保护。 如果不保留备份的数据，您将无法恢复保护。

要恢复 SAP HANA 数据库的保护，

* 打开备份项，并选择“恢复备份”。****

   ![选择恢复备份](./media/sap-hana-db-manage/resume-backup.png)

* 在“备份策略”菜单中选择策略，然后选择“保存”。********

### <a name="upgrading-from-sap-hana-10-to-20"></a>从 SAP HANA 1.0 升级到 2.0

了解如何[在从 SAP HANA 1.0 升级到 2.0 后继续为 SAP HANA](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20)数据库进行备份。

### <a name="upgrading-without-a-sid-change"></a>升级时无需更改 SID

了解如何继续备份其[SID 在升级后未更改](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change)的 SAP HANA 数据库。

### <a name="unregister-an-sap-hana-instance"></a>取消注册 SAP HANA 实例

在禁用保护后，但在删除保管库之前取消注册 SAP HANA 实例：

* 在保管库仪表板上，在 **"管理**"下，选择 **"备份基础结构**"。

   ![选择“备份基础结构”。](./media/sap-hana-db-manage/backup-infrastructure.png)

* 选择**备份管理类型**作为**Azure VM 中的工作负荷**

   ![选择备份管理类型作为 Azure VM 中的工作负荷](./media/sap-hana-db-manage/backup-management-type.png)

* 在**受保护服务器**中，选择要取消注册的实例。 要删除保管库，必须取消注册所有服务器/实例。

* 右键单击受保护的实例并选择 **"取消注册**"。

   ![选择取消注册](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>在 SAP HANA 服务器 VM 上重新注册扩展

有时，VM 上的工作负载扩展可能会由于以下或其他原因受到影响。 在这种情况下，在该 VM 上触发的所有操作将开始失败。 然后，可能需要在 VM 上重新注册该扩展。 重新注册操作会在 VM 上重新安装工作负荷备份扩展，使操作能够继续。

使用此选项时要小心：在具有已正常运行的扩展的 VM 上触发时，此操作将导致扩展重新启动。 这可能会导致所有正在进行的作业失败。 在触发重新注册操作之前，请检查一个或多个[症状](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures)。

## <a name="next-steps"></a>后续步骤

* 了解如何[在备份 SAP HANA 数据库时解决常见问题。](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
