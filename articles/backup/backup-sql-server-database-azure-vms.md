---
title: 将 SQL Server 数据库备份到 Azure | Microsoft Docs
description: 本教程介绍如何将 SQL Server 备份到 Azure，
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: sachdevaswati
ms.openlocfilehash: 899068141a936aa5257501e733c6600185f22b8e
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522156"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>备份 Azure VM 中的 SQL Server 数据库

SQL Server 数据库属于关键工作负荷，要求较低的恢复点目标 (RPO) 和长期保留。 可以使用 [Azure 备份](backup-overview.md)来备份 Azure VM 上运行的 SQL Server 数据库。

本文介绍如何将 Azure VM 上运行的 SQL Server 数据库备份到 Azure 备份恢复服务保管库。 在本文中，学习如何：

> [!div class="checklist"]
> * 创建并配置保管库。
> * 发现数据库并设置备份。
> * 为数据库设置自动保护。


## <a name="prerequisites"></a>必备组件

在备份 SQL Server 数据库之前，请检查以下条件：

1. 标识或[创建](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault)同一区域或区域设置用作托管的 SQL Server 实例的 VM 的恢复服务保管库。
2. 检查备份 SQL 数据库所需的 [VM 权限](backup-azure-sql-database.md#fix-sql-sysadmin-permissions)。
3. 验证 VM 是否已建立[网络连接](backup-sql-server-database-azure-vms.md#establish-network-connectivity)。
4. 检查是否根据 Azure 备份的[命名准则](#verify-database-naming-guidelines-for-azure-backup)命名了 SQL Server 数据库。
5. 验证是否未为该数据库启用了其他任何备份解决方案。 在设置此方案之前，请禁用其他所有 SQL Server 备份。 可以同时针对某个 Azure VM 以及该 VM 上运行的 SQL Server 数据库启用 Azure 备份，而不会发生任何冲突。


### <a name="establish-network-connectivity"></a>建立网络连接

对于所有操作，SQL Server VM 需要与 Azure 公共 IP 地址建立连接。 VM 操作 （数据库发现、 配置备份、 计划备份、 还原恢复点） 而无需连接到公共 IP 地址失败。 使用以下选项之一建立连接：

- **允许 Azure 数据中心 IP 范围**：允许下载中的 [IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 若要访问网络安全组 (NSG)，请使用**Set-azurenetworksecurityrule** cmdlet。
- **部署用于路由流量的 HTTP 代理服务器**：在 Azure VM 中备份 SQL Server 数据库时，该 VM 上的备份扩展将使用 HTTPS API 将管理命令发送到 Azure 备份，并将数据发送到 Azure 存储。 备份扩展还使用 Azure Active Directory (Azure AD) 进行身份验证。 通过 HTTP 代理路由这三个服务的备份扩展流量。 扩展的是配置为向公共 internet 访问的唯一组件。

每个选项各有其优点和缺点

**选项** | **优点** | **缺点**
--- | --- | ---
允许 IP 范围 | 无额外成本。 | 管理起来很复杂，因为 IP 地址范围随时会变化。 <br/><br/> 允许访问整个 Azure，而不只是 Azure 存储。
使用 HTTP 代理   | 允许在代理中对存储 URL 进行精细控制。 <br/><br/> 在单个位置通过 Internet 访问 VM。 <br/><br/> 不受 Azure IP 地址变化的影响。 | 通过代理软件运行 VM 带来的额外成本。

### <a name="set-vm-permissions"></a>设置 VM 权限

当你为 SQL Server 数据库配置备份时，Azure 备份会执行许多操作：

- 添加 **AzureBackupWindowsWorkload** 扩展。
- 为了发现虚拟机上的数据库，Azure 备份会创建帐户 NT SERVICE\AzureWLBackupPluginSvc。 此帐户用于备份和还原，需要拥有 SQL sysadmin 权限。
- Azure 备份利用 **NT AUTHORITY\SYSTEM** 帐户进行数据库发现/查询，因此此帐户需是 SQL 上的公共登录名。

如果 SQL Server VM 不是从 Azure 市场创建的，你可能会收到错误 **UserErrorSQLNoSysadminMembership**。 如果发生此错误，请[遵照这些说明](backup-azure-sql-database.md#fix-sql-sysadmin-permissions)予以解决。

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>验证 Azure 备份的数据库命名准则

避免在数据库名称中使用以下字符：

  * 尾随/前导空格
  * 尾随“!”
  * 右方括号“]”

对于 Azure 表不支持的字符，可以使用别名，但我们建议避免使用别名。 [了解详细信息](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN)。


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>发现 SQL Server 数据库

发现 VM 上运行的数据库。

1. 在 [Azure 门户](https://portal.azure.com)中，打开用于备份数据库的恢复服务保管库。

2. 在“恢复服务保管库”仪表板上选择“备份”。

   ![选择“+备份”打开“备份目标”菜单](./media/backup-azure-sql-database/open-backup-menu.png)

3. 在“备份目标”中，将“工作负荷的运行位置”设置为“Azure”（默认值）。

4. 在“要备份哪些内容”中，选择“Azure VM 中的 SQL Server”。

    ![选择“Azure VM 中的 SQL Server”进行备份](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. 在“备份目标” > “发现 VM 中的数据库”中，选择“开始发现”以搜索订阅中不受保护的 VM。 搜索过程需要花费一段时间，具体取决于订阅中不受保护的虚拟机数量。

   - 发现后，未受保护的 VM 应会按名称和资源组列在列表中。
   - 如果某个 VM 未按预期列出，请检查它是否已保管库中备份。
   - 可能有多个 VM 同名，但它们属于不同的资源组。

     ![在搜索 VM 中的数据库期间，备份将会挂起。](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. 在 VM 列表中，选择运行 SQL Server 数据库的VM，然后选择“发现数据库”。

7. 在“通知”区域跟踪数据库发现。 完成该作业可能需要一段时间，具体取决于 VM 上的数据库数量。 发现选定的数据库后，会显示一条成功消息。

    ![部署成功消息](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure 备份将发现该 VM 上的所有 SQL Server 数据库。 在发现期间，后台将发生以下情况：

    - Azure 备份将 VM 注册到用于备份工作负荷的保管库。 已注册 VM 上的所有数据库只能备份到此保管库。
    - Azure 备份在 VM 上安装 **AzureBackupWindowsWorkload** 扩展。 不会在 SQL 数据库中安装任何代理。
    - Azure 备份在 VM 上创建服务帐户 **NT Service\AzureWLBackupPluginSvc**。
      - 所有备份和还原操作使用该服务帐户。
      - **NT Service\AzureWLBackupPluginSvc** 需要 SQL sysadmin 权限。 在 Azure 市场中创建的所有 SQL Server VM 已预装 **SqlIaaSExtension**。 **AzureBackupWindowsWorkload** 扩展使用 **SQLIaaSExtension** 自动获取所需的权限。
    - 如果 VM 不是从市场创建的，则该 VM 上未安装 **SqlIaaSExtension**，并且发现操作将会失败并出现错误消息 **UserErrorSQLNoSysAdminMembership**。 请遵照 [#fix-sql-sysadmin-permissions] 中的说明解决此问题。

        ![选择 VM 和数据库](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>配置备份  

按如下所述配置备份：

1. 在“备份目标”中，选择“配置备份”。

   ![选择“配置备份”](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. 单击**配置备份**，则**选择备份的项**边栏选项卡中显示。 这会列出所有已注册的可用性组和独立 SQL Server。 若要查看该实例或 Alwayson 可用性组中所有未受保护的数据库的行的左侧展开 v 形图标。  

    ![显示包含独立数据库的所有 SQL Server 实例](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. 选择要保护的所有数据库，然后选择“确定”。

   ![保护数据库](./media/backup-azure-sql-database/select-database-to-protect.png)

   为了优化备份负载，Azure 备份会将一个备份作业中的最大数据库数目设置为 50。

     * 若要对 50 个以上的数据库提供保护，请配置多个备份。
     * 或者，可以通过在“自动保护”列中的相应下拉列表内选择“打开”选项，针对整个实例或 Always On 可用性组启用[自动保护](#enable-auto-protection)。 [自动保护](#enable-auto-protection)功能不仅可以一次性针对所有现有数据库启用保护，而且还会自动保护将来要添加到该实例或可用性组的所有新数据库。  

4. 单击**确定**以打开**备份策略**边栏选项卡。

    ![针对 Always On 可用性组启用自动保护](./media/backup-azure-sql-database/enable-auto-protection.png)

5. 在中 **选择备份策略**，选择一个策略，然后单击 **确定**。

   - 选择默认策略： HourlyLogBackup。
   - 选择前面为 SQL 创建的现有备份策略。
   - 定义基于 RPO 和保留期范围的新策略。

     ![选择“备份策略”](./media/backup-azure-sql-database/select-backup-policy.png)

6. 上 **备份**菜单中，选择 **启用备份**。

    ![启用选定的备份策略](./media/backup-azure-sql-database/enable-backup-button.png)

7. 跟踪配置进度 **通知** 门户的区域。

    ![通知区域](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>创建备份策略

备份策略定义备份创建时间以及这些备份的保留时间。

- 策略是在保管库级别创建的。
- 多个保管库可以使用相同的备份策略，但必须向每个保管库应用该备份策略。
- 创建备份策略时，每日完整备份是默认设置。
- 可以添加差异备份，但仅在将完整备份配置为每周发生时，才能这样做。
- [了解](backup-architecture.md#sql-server-backup-types)不同类型的备份策略。

创建备份策略：

1. 在保管库中，单击“备份策略” > “添加”。
2. 在“添加”菜单中，单击“Azure VM 中的 SQL Server”。 随即会定义策略类型。

   ![为新的备份策略选择策略类型](./media/backup-azure-sql-database/policy-type-details.png)

3. 在“策略名称”处输入新策略的名称。
4. 在“完整备份策略”中选择“备份频率”，然后选择“每日”或“每周”。

   - 如果选择“每日”，请选择备份作业开始时的小时和时区。
   - 必须运行完整备份；不能禁用“完整备份”选项。
   - 单击“完整备份”以查看策略。
   - 对于每日完整备份，无法创建差异备份。
   - 如果选择“每周”，请选择备份作业开始时的星期、小时和时区。

     ![新备份策略字段](./media/backup-azure-sql-database/full-backup-policy.png)  

5. 对于“保留范围”，默认已选择所有选项。 清除你不需要的所有保留范围限制，并设置要使用的间隔。

    - 任何类型的备份 （完整/差异/日志） 的最小保持期为 7 天。
    - 恢复点已根据其保留范围标记为保留。 例如，如果选择每日完整备份，则每天只触发一次完整备份。
    - 根据每周保留范围和每周保留设置，将会标记并保留特定日期的备份。
    - 每月和每年保留范围的行为类似。

   ![保留范围间隔设置](./media/backup-azure-sql-database/retention-range-interval.png)

6. 在“完整备份策略”菜单中，选择“确定”接受设置。
7. 若要添加差异备份策略，请选择“差异备份”。

   ![保留范围间隔设置](./media/backup-azure-sql-database/retention-range-interval.png)
   ![打开差异备份策略菜单](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. 在“差异备份策略”中，选择“启用”打开频率和保留控件。

    - 每天最多可以触发一次差异备份。
    - 差异备份最多可以保留 180 天。 如果需要保留更长时间，必须使用完整备份。

9. 选择“确定”保存策略，并返回“备份策略”主菜单。

10. 若要添加事务日志备份策略，请选择“日志备份”。
11. 在“日志备份”中选择“启用”，并设置频率和保留控件。 日志备份最多可以每隔 15 分钟发生一次，最多可以保留 35 天。
12. 选择“确定”保存策略，并返回“备份策略”主菜单。

    ![编辑日志备份策略](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. 在“备份策略”菜单中，选择是否启用“SQL 备份压缩”。
    - 默认已禁用压缩。
    - Azure 备份在后端使用 SQL 本机备份压缩。

14. 完成备份策略的编辑后，选择“确定”。


### <a name="modify-policy"></a>修改策略
修改策略以更改备份频率或保留期范围。

> [!NOTE]
> 在保留期内的任何更改将回顾性地应用于新的除了所有旧的恢复点。

在保管库仪表板中，转到**管理** > **备份策略**，然后选择你想要编辑的策略。

  ![管理备份策略](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>启用自动保护  

启用自动备份所有现有数据库和将在将来添加到独立 SQL Server 实例或 SQL Server Always on 可用性组的数据库的自动保护。

- 可以选择为一次性自动保护的数据库数目没有限制。
- 有选择地无法保护或从实例中时启用自动保护的保护中排除的数据库。
- 如果实例已包含一些受保护的数据库，它们将继续受其各自的策略，即使启用自动保护。 但是，所有未受保护的数据库以及将在将来添加的数据库将具有仅在启用自动保护时定义的单个策略 **配置备份**。 但是，可以更改自动保护的数据库与更高版本关联的策略。  

若要启用自动保护的步骤如下所示：

  1. 在“要备份的项”中，选择要为其启用自动保护的实例。
  2. 选择“自动保护”下面的下拉列表，并设置为“打开”。 。

      ![针对 Always On 可用性组启用自动保护](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. 备份是针对所有数据库统一配置的，可以在“备份作业”中跟踪备份。

如果你需要禁用自动保护，请单击下面的实例名称**配置备份**，然后选择**禁用自动保护**的实例。 所有数据库将都继续备份，但将来的数据库不会自动受到保护。

![针对该实例禁用自动保护](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>后续步骤

- [了解](restore-sql-database-azure-vm.md)如何还原已备份的 SQL Server 数据库。
- [了解](manage-monitor-sql-database-backup.md)如何管理已备份的 SQL Server 数据库。
