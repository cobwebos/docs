---
title: 管理 Azure VM 上已备份的 SAP HANA 数据库
description: 本文介绍了对在 Azure 虚拟机上运行的 SAP HANA 数据库进行管理和监视时的常见任务。
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 98dd67668d1b88a25dfa3b91174cd96730c435e1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87049469"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>管理和监视已备份的 SAP HANA 数据库

本文介绍了对在 Azure 虚拟机 (VM) 上运行且通过 [Azure 备份](./backup-overview.md)服务备份到 Azure 备份恢复服务保管库的 SAP HANA 数据库进行管理和监视时的常见任务。 你将了解如何监视作业和警报、触发按需备份、编辑策略、停止和恢复数据库保护，以及从备份中取消注册 VM。

如果尚未为 SAP HANA 数据库配置备份，请参阅[备份 Azure VM 上的 SAP HANA 数据库](./backup-azure-sap-hana-database.md)。

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>在门户中监视手动备份作业

Azure 备份在 Azure 门户的“备份作业”部分中显示所有手动触发的作业。

![“备份作业”部分](./media/sap-hana-db-manage/backup-jobs.png)

你在此门户中看到的作业包括数据库发现和注册，以及备份和还原操作。 计划作业（包括日志备份）不会显示在此部分中。 从 SAP HANA 本机客户端 (Studio/Cockpit/DBA Cockpit) 手动触发的备份也不会显示在此处。

![“备份作业”列表](./media/sap-hana-db-manage/backup-jobs-list.png)

若要了解有关监视的详细信息，请转到[在 Azure 门户中监视](./backup-azure-monitoring-built-in-monitor.md)和[使用 Azure Monitor 进行监视](./backup-azure-monitoring-use-azuremonitor.md)。

## <a name="view-backup-alerts"></a>查看备份警报

警报是监视 SAP HANA 数据库的备份的一种简单方法。 警报可帮助你专注于你最关注的事件，而不会迷失在备份生成的多个事件中。 Azure 备份允许你设置警报，并且可以按如下所示进行监视：

* 登录 [Azure 门户](https://portal.azure.com/)。
* 在保管库仪表板中，选择“备份警报”。

  ![保管库仪表板上的备份警报](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* 你将能够看到警报：

  ![备份警报列表](./media/sap-hana-db-manage/backup-alerts-list.png)

* 单击警报以查看更多详细信息：

  ![警报详细信息](./media/sap-hana-db-manage/alert-details.png)

如今，Azure 备份允许通过电子邮件发送警报。 这些警报包括：

* 针对所有备份失败触发的警报。
* 按错误代码在数据库级别合并的警报。
* 仅针对数据库首次备份失败发送的警报。

若要了解有关监视的详细信息，请转到[在 Azure 门户中监视](./backup-azure-monitoring-built-in-monitor.md)和[使用 Azure Monitor 进行监视](./backup-azure-monitoring-use-azuremonitor.md)。

## <a name="management-operations"></a>管理操作

借助 Azure 备份，可以通过大量支持的管理操作轻松地管理已备份的 SAP HANA 数据库。 以下部分更详细地讨论了这些操作。

### <a name="run-an-on-demand-backup"></a>运行按需备份

备份根据策略计划运行。 可以按需运行备份，如下所示：

1. 在保管库菜单中，单击“备份项”。
2. 在“备份项”中，选择运行 SAP HANA 数据库的 VM，然后单击“立即备份”。
3. 在“立即备份”中，选择要执行的备份的类型。 然后单击“确定”。 此备份将根据与此备份项关联的策略进行保留。
4. 监视门户通知。 可以在保管库仪表板 >“备份作业” > “进行中”监视作业进度。  创建初始备份可能需要一些时间，具体取决于数据库的大小。

### <a name="hana-native-client-integration"></a>HANA 本机客户端集成

#### <a name="backup"></a>备份

从任何 HANA 本机客户端触发（到 Backint）的按需备份将显示在“备份项”页上的备份列表中 。

![上次备份运行时间](./media/sap-hana-db-manage/last-backups.png)

还可以从“备份作业”页[监视这些备份](#monitor-manual-backup-jobs-in-the-portal)。

这些按需备份还将显示在用于还原的还原点列表中。

![还原点列表](./media/sap-hana-db-manage/list-restore-points.png)

#### <a name="restore"></a>还原

可以从“备份作业”页[监视](#monitor-manual-backup-jobs-in-the-portal)从 HANA 本机客户端（使用 Backint）触发以还原到同一台计算机的还原 。

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>在启用了 Azure 备份的数据库上运行 SAP HANA 本机客户端备份

如果要对正在使用 Azure 备份备份的数据库执行本地备份（使用 HANA Studio/Cockpit），请执行以下操作：

1. 等待数据库的所有完整备份或日志备份完成。 检查 SAP HANA Studio/Cockpit 中的状态。
2. 禁用日志备份，并将备份目录设置为相关数据库的文件系统。
3. 为此，请双击“systemdb” > “配置” > “选择数据库” > “筛选器(日志)”。   
4. 将 **enable_auto_log_backup** 设置为 **No**。
5. 将 **log_backup_using_backint** 设置为 **False**。
6. 创建数据库的完整备份。
7. 等待完整备份和目录备份完成。
8. 将前面的设置恢复为 Azure 的设置：
   * 将 **enable_auto_log_backup** 设置为 **Yes**。
   * 将 log_backup_using_backint 设置为 True 。

### <a name="change-policy"></a>更改策略

可以更改 SAP HANA 备份项的基础策略。

* 在保管库仪表板中，转到“备份项”：

  ![选择备份项](./media/sap-hana-db-manage/backup-items.png)

* 选择“Azure VM 中的 SAP HANA”

  ![选择 Azure VM 中的 SAP HANA](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* 选择要更改其基础策略的备份项
* 单击现有备份策略

  ![选择现有备份策略](./media/sap-hana-db-manage/existing-backup-policy.png)

* 更改策略，从列表中进行选择。 [创建新备份策略](./backup-azure-sap-hana-database.md#create-a-backup-policy)（如果需要）。

  ![从下拉列表中选择策略](./media/sap-hana-db-manage/choose-backup-policy.png)

* 保存更改

  ![保存更改](./media/sap-hana-db-manage/save-changes.png)

* 策略修改将影响所有关联的备份项，并触发相应的配置保护作业。

>[!NOTE]
> 保留期内的所有更改都将以回溯方式应用于除新恢复点以外的所有旧恢复点。
>
> 增量备份策略不能用于 SAP HANA 数据库。 这些数据库目前不支持增量备份。

### <a name="modify-policy"></a>修改策略

修改策略以更改备份类型、频率和保持期。

>[!NOTE]
>保留期内的所有更改都将以追溯方式应用于所有除新恢复点以外的旧恢复点。

1. 在保管库仪表板中，转到“管理”>“备份策略”，然后选择要编辑的策略。

   ![选择要编辑的策略](./media/sap-hana-db-manage/manage-backup-policies.png)

1. 选择“修改”。

   ![选择“修改”](./media/sap-hana-db-manage/modify-policy.png)

1. 选择备份类型的频率。

   ![选择备份频率](./media/sap-hana-db-manage/choose-frequency.png)

策略修改将影响所有关联的备份项，并触发相应的配置保护作业。

### <a name="inconsistent-policy"></a>策略不一致

有时，修改策略操作可能导致某些备份项的策略版本不一致。 如果在触发修改策略操作后，备份项的相应配置保护作业失败，则会发生这种情况。 它在备份项视图中显示如下：

![策略不一致](./media/sap-hana-db-manage/inconsistent-policy.png)

单击一下即可修复所有受影响的项的策略版本：

![修复策略版本](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>停止对 SAP HANA 数据库的保护

可以通过以下几种方式停止保护 SAP HANA 数据库：

* 停止所有将来的备份作业并删除所有恢复点。
* 停止所有将来的备份作业，且恢复点保留不变。

如果选择保留恢复点，请记住以下详细内容：

* 所有恢复点都将永久保持不变，所有删除操作都应在停止保护时停止，并保留数据。
* 你将为受保护的实例和使用的存储付费。 有关详细信息，请参阅 [Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)。
* 如果在不停止备份的情况下删除数据源，则新备份会失败。

停止数据库的保护：

* 在保管库仪表板中，选择“备份项”。
* 在“备份管理类型”下，选择“Azure VM 中的 SAP HANA” 

  ![选择“Azure VM 中的 SAP HANA”](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* 选择要停止保护的数据库：

  ![选择要停止保护的数据库](./media/sap-hana-db-manage/select-database.png)

* 在数据库菜单中，选择“停止备份”。

  ![选择“停止备份”](./media/sap-hana-db-manage/stop-backup.png)

* 在“停止备份”菜单中，选择是保留还是删除数据。 如果需要，请提供原因和注释。

  ![选择是保留还是删除数据](./media/sap-hana-db-manage/retain-backup-data.png)

* 选择“停止备份”。

### <a name="resume-protection-for-an-sap-hana-database"></a>恢复对 SAP HANA 数据库的保护

如果停止对 SAP HANA 数据库的保护且选择“保留备份数据”选项，可在稍后恢复保护。 如果不保留备份的数据，则无法继续保护。

若要恢复对 SAP HANA 数据库的保护，请执行以下操作：

* 打开备份项，然后选择“恢复备份”。

   ![选择恢复备份](./media/sap-hana-db-manage/resume-backup.png)

* 在“备份策略”菜单中选择策略，然后选择“保存”。 

### <a name="upgrading-from-sdc-to-mdc"></a>从 SDC 升级到 MDC

了解[从 SDC 升级到 MDC 后](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid)如何继续执行 SAP HANA 数据库的备份。

### <a name="upgrading-from-sdc-to-mdc-without-a-sid-change"></a>从 SDC 升级到 MDC 而未更改 SID

了解如何在[从 SDC 升级到 MDC 后](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-no-change-in-sid)，继续备份其 SID 尚未更改 SAP HANA 的数据库。

### <a name="unregister-an-sap-hana-instance"></a>取消注册 SAP HANA 实例

在禁用保护之后但删除保管库之前取消注册 SAP HANA 实例：

* 在保管库仪表板上，在“管理”下，选择“备份基础结构”。 

   ![选择“备份基础结构”。](./media/sap-hana-db-manage/backup-infrastructure.png)

* 将“备份管理类型”选择为“Azure VM 中的工作负载” 

   ![将“备份管理类型”选择为“Azure VM 中的工作负载”](./media/sap-hana-db-manage/backup-management-type.png)

* 在“受保护的服务器”中，选择要取消注册的实例。 若要删除保管库，必须取消注册所有服务器/实例。

* 右键单击受保护的实例，然后选择“取消注册”。

   ![选择“取消注册”](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>在 SAP HANA 服务器 VM 上重新注册扩展

有时，VM 上的工作负载扩展可能会由于某种原因而受到影响。 在这些情况下，在 VM 上触发的所有操作都将开始失败。 然后，你可能需要在 VM 上重新注册该扩展。 重新注册操作会重新安装 VM 上的工作负载备份扩展，以便继续操作。

请谨慎使用此选项：当在具有正常扩展的 VM 上触发时，此操作将导致扩展重新启动。 这可能会导致所有正在进行的作业失败。 在触发重新注册操作之前，请检查是否存在一种或多种[症状](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures)。

## <a name="next-steps"></a>后续步骤

* 了解[关于 SAP HANA 数据库备份常见问题的疑难解答](./backup-azure-sap-hana-database-troubleshoot.md)。
