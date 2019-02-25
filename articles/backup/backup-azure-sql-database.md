---
title: 将 SQL Server 数据库备份到 Azure | Microsoft Docs
description: 本教程介绍如何将 SQL Server 备份到 Azure， 此外还介绍 SQL Server 的恢复。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 17ec7723044cec391ebe390bbcfba3aa6f2f29ca
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446845"
---
# <a name="back-up-sql-server-databases-on-azure-vms"></a>备份 Azure VM 上的 SQL Server 数据库 

SQL Server 数据库属于关键工作负荷，要求较低的恢复点目标 (RPO) 和长期保留。 可以使用 [Azure 备份](backup-overview.md)来备份 Azure VM 上运行的 SQL Server 数据库。 

本文介绍如何将 Azure VM 上运行的 SQL Server 数据库备份到 Azure 备份恢复服务保管库。 在本文中，学习如何：

> [!div class="checklist"]
> * 验证备份 SQL Server 实例所要满足的先决条件。
> * 创建并配置保管库。
> * 发现数据库并设置备份。
> * 为数据库设置自动保护。

> [!NOTE]
> 此功能目前处于公开预览状态。

## <a name="before-you-start"></a>开始之前

在开始之前，请验证以下条件：

1. 确保有一个 SQL Server 实例在 Azure 中运行。 可以在市场中[快速创建 SQL Server 实例](../sql-database/sql-database-get-started-portal.md)。
2. 查看下面的公共预览版限制。
3. 查看方案支持。
4. 查看有关此方案的[常见问题解答](faq-backup-sql-server.md)。


## <a name="preview-limitations"></a>预览版限制

此公共预览版存在许多限制。

- 运行 SQL Server 的 VM 需要建立 Internet 连接才能访问 Azure 公共 IP 地址。 
- 在一个保管库中最多可以备份 2000 个 SQL Server 数据库。 如果需要备份更多的数据库，请再创建一个保管库。 
- [分布式可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017)的备份不能完全正常进行。
- 不支持对 SQL Server Always On 故障转移群集实例 (FCI) 进行备份。
- 应在门户中配置 SQL Server 备份。 目前无法使用 Azure PowerShell、CLI 或 REST API 配置备份。
- 不支持对 FCI 镜像数据库、数据库快照和数据库执行备份和还原操作。
- 无法保护包含大量文件的数据库。 支持的最大文件数不确定。 这不仅仅取决于文件的数量，而且还取决于文件的路径长度。 

有关支持/不支持的方案的详细信息，请参阅[常见问题解答部分](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#faq)。

## <a name="scenario-support"></a>方案支持

**支持** | **详细信息**
--- | ---
**支持的部署** | 支持 SQL 市场 Azure VM 和非市场（手动安装的 SQL Server）VM。
**支持的地理区域** | 澳大利亚东南部 (ASE)；巴西南部 (BRS)；加拿大中部 (CNC)；加拿大东部 (CE)；美国中部 (CUS)；东亚 (EA)；澳大利亚东部 (AE)；美国东部 (EUS)；美国东部 2 (EUS2)；印度中部 (INC)；印度南部 (INS)；日本东部 (JPE)；日本西部 (JPW)；韩国中部 (KRC)；韩国南部 (KRS)；美国中北部 (NCUS)；北欧 (NE)；美国中南部 (SCUS)；东南亚 (SEA)；英国南部 (UKS)；英国西部 (UKW)；美国中西部 (WCUS)；西欧 (WE)；美国西部 (WUS)；美国西部 2 (WUS 2)
**受支持的操作系统** | Windows Server 2016、Windows Server 2012 R2、Windows Server 2012<br/><br/> 目前不支持 Linux。
**支持的 SQL Server 版本** | SQL Server 2017、SQL Server 2016、SQL Server 2014、SQL Server 2012。<br/><br/> Enterprise、Standard、Web、Developer、Express。


## <a name="prerequisites"></a>先决条件

在备份 SQL Server 数据库之前，请检查以下条件：

1. 在托管 SQL Server 实例的 VM 所在的同一区域或位置标识或[创建](backup-azure-sql-database.md#create-a-recovery-services-vault)一个恢复服务保管库。
2. 检查备份 SQL 数据库所需的 [VM 权限](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms)。
3. 验证 VM 是否已建立[网络连接](backup-azure-sql-database.md#establish-network-connectivity)。
4. 检查是否根据 Azure 备份的[命名准则](backup-azure-sql-database.md#sql-database-naming-guidelines-for-azure-backup)命名了 SQL Server 数据库。
5. 验证是否未为该数据库启用了其他任何备份解决方案。 在设置此方案之前，请禁用其他所有 SQL Server 备份。 可以同时针对某个 Azure VM 以及该 VM 上运行的 SQL Server 数据库启用 Azure 备份，而不会发生任何冲突。


### <a name="establish-network-connectivity"></a>建立网络连接

对于所有操作，SQL Server VM 需要与 Azure 公共 IP 地址建立连接。 在未连接到公共 IP 地址的情况下，VM 操作（数据库发现、配置备份、计划的备份、还原恢复点等）将会失败。 使用以下选项之一建立连接：

- **允许 Azure 数据中心 IP 范围**：允许下载中的 [IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 要在网络安全组 (NSG) 中进行访问，请使用 **Set-AzureNetworkSecurityRule** cmdlet。
- **部署用于路由流量的 HTTP 代理服务器**：在 Azure VM 中备份 SQL Server 数据库时，该 VM 上的备份扩展将使用 HTTPS API 将管理命令发送到 Azure 备份，并将数据发送到 Azure 存储。 备份扩展还使用 Azure Active Directory (Azure AD) 进行身份验证。 通过 HTTP 代理路由这三个服务的备份扩展流量。 该扩展是为了访问公共 Internet 而配置的唯一组件。

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

如果 SQL Server VM 不是从 Azure 市场创建的，你可能会收到错误 **UserErrorSQLNoSysadminMembership**。 如果发生此错误，请[遵照这些说明](#fix-sql-sysadmin-permissions)予以解决。

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

    ![在搜索 VM 中的数据库期间，备份将会挂起。](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. 在 VM 列表中，选择运行 SQL Server 数据库的VM，然后选择“发现数据库”。

7. 在“通知”区域跟踪数据库发现。 完成该作业可能需要一段时间，具体取决于 VM 上的数据库数量。 发现选定的数据库后，会显示一条成功消息。

    ![部署成功消息](./media/backup-azure-sql-database/notifications-db-discovered.png)

    - 发现后，未受保护的 VM 应会按名称和资源组列在列表中。
    - 如果某个 VM 未按预期列出，请检查它是否已保管库中备份。
    - 可能有多个 VM 同名，但它们属于不同的资源组。 

9. 选择运行 SQL Server 数据库的VM，然后选择“发现数据库”。 
10. Azure 备份将发现该 VM 上的所有 SQL Server 数据库。 在发现期间，后台将发生以下情况：

    - Azure 备份将 VM 注册到用于备份工作负荷的保管库。 已注册 VM 上的所有数据库只能备份到此保管库。
    - Azure 备份在 VM 上安装 **AzureBackupWindowsWorkload** 扩展。 不会在 SQL 数据库中安装任何代理。
    - Azure 备份在 VM 上创建服务帐户 **NT Service\AzureWLBackupPluginSvc**。
      - 所有备份和还原操作使用该服务帐户。
      - **NT Service\AzureWLBackupPluginSvc** 需要 SQL sysadmin 权限。 在 Azure 市场中创建的所有 SQL Server VM 已预装 **SqlIaaSExtension**。 **AzureBackupWindowsWorkload** 扩展使用 **SQLIaaSExtension** 自动获取所需的权限。
    - 如果 VM 不是从市场创建的，则该 VM 上未安装 **SqlIaaSExtension**，并且发现操作将会失败并出现错误消息 **UserErrorSQLNoSysAdminMembership**。 请遵照 [#fix-sql-sysadmin-permissions] 中的说明解决此问题。

        ![选择 VM 和数据库](./media/backup-azure-sql-database/registration-errors.png)



## <a name="configure-a-backup-policy"></a>配置备份策略 

为了优化备份负载，Azure 备份会将一个备份作业中的最大数据库数目设置为 50。

- 若要对 50 个以上的数据库提供保护，请配置多个备份。
- 或者，可以启用自动保护。 自动保护会统一保护现有的数据库，并自动保护添加到可用性组实例中的新数据库。


按如下所述配置备份：

1. 在保管库仪表板中，选择“备份”。 

   ![选择“+备份”打开“备份目标”菜单](./media/backup-azure-sql-database/open-backup-menu.png)

3. 在“备份目标”菜单中，将“工作负荷的运行位置”设置为“Azure”。

4. 在“要备份哪些内容”中，选择“Azure VM 中的 SQL Server”。

    ![选择“Azure VM 中的 SQL Server”进行备份](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    
5. 在“备份目标”中，选择“配置备份”。

    ![选择“配置备份”](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    ![显示包含独立数据库的所有 SQL Server 实例](./media/backup-azure-sql-database/list-of-sql-databases.png)

6. 选择要保护的所有数据库，然后选择“确定”。

    ![保护数据库](./media/backup-azure-sql-database/select-database-to-protect.png)

    - 此时会显示所有 SQL Server 实例（独立实例和可用性组）。
    - 选择实例名称/可用性组左侧的向下箭头进行筛选。

7. 在“备份”菜单中，选择“备份策略”。 

    ![选择“备份策略”](./media/backup-azure-sql-database/select-backup-policy.png)

8. 在“选择备份策略”中选择一个策略，然后单击“确定”。

    - 选择默认策略：**HourlyLogBackup**。
    - 选择前面为 SQL 创建的现有备份策略。
    - 根据 RPO 和保留范围[定义新策略](backup-azure-sql-database.md#define-a-backup-policy)。
    - 在预览期无法编辑现有的备份策略。
    
9. 在“备份”菜单中，选择“启用备份”。

    ![启用选定的备份策略](./media/backup-azure-sql-database/enable-backup-button.png)

10. 在门户的“通知”区域跟踪配置进度。

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



## <a name="enable-auto-protection"></a>启用自动保护  

启用自动保护可以自动备份所有现有数据库，以及将来会添加到独立 SQL Server 实例或 SQL Server Always On 可用性组中的数据库。 

- 启用自动保护并选择策略时，现有的受保护数据库将继续使用以前的策略。
- 可以一次性选择进行自动保护的数据库数目没有限制。

按如下所述启用自动保护：

1. 在“要备份的项”中，选择要为其启用自动保护的实例。
2. 选择“自动保护”下面的下拉列表，并设置为“打开”。 然后单击“确定”。

    ![针对 Always On 可用性组启用自动保护](./media/backup-azure-sql-database/enable-auto-protection.png)

3. 备份是针对所有数据库统一配置的，可以在“备份作业”中跟踪备份。


如果需要禁用自动保护，请单击“配置备份”下面的实例名称，然后针对该实例选择“禁用自动保护”。 所有数据库将继续备份。 但是，将来的数据库不会自动受到保护。

![针对该实例禁用自动保护](./media/backup-azure-sql-database/disable-auto-protection.png)


## <a name="fix-sql-sysadmin-permissions"></a>修复 SQL sysadmin 权限

如果由于出现 **UserErrorSQLNoSysadminMembership** 错误而需要解决权限问题，请执行以下操作：

1. 使用拥有 SQL Server sysadmin 权限的帐户登录到 SQL Server Management Studio (SSMS)。 除非需要特殊权限，否则 Windows 身份验证应该正常运行。
2. 在 SQL 服务器上，打开“安全/登录名”文件夹。

    ![打开“安全/登录名”文件夹以查看帐户](./media/backup-azure-sql-database/security-login-list.png)

3. 右键单击“登录名”文件夹并选择“新建登录名”。 在“登录名 - 新建”中，选择“搜索”。

    ![在“登录名 - 新建”对话框中选择“搜索”](./media/backup-azure-sql-database/new-login-search.png)

3. 在虚拟机注册和 SQL 发现阶段已创建 Windows 虚拟服务帐户 NT SSERVICE\AzureWLBackupPluginSvc。 输入“输入要选择的对象名称”中显示的帐户名。 选择“检查名称”以解析名称。 单击“确定”。

    ![选择“检查名称”以解析未知的服务名称](./media/backup-azure-sql-database/check-name.png)


4. 在“服务器角色”中，确保“sysadmin”角色已选中。 单击“确定”。 现在，所需的权限应会存在。

    ![确保 sysadmin 服务器角色已选中](./media/backup-azure-sql-database/sysadmin-server-role.png)

5. 现在，将数据库与恢复服务保管库相关联。 在 Azure 门户上的“受保护的服务器”列表中，右键单击处于错误状态的服务器，并选择“重新发现数据库”。

    ![验证服务器是否拥有相应的权限](./media/backup-azure-sql-database/check-erroneous-server.png)

6. 在“通知”区域查看进度。 找到选定的数据库后，会显示一条成功消息。

    ![部署成功消息](./media/backup-azure-sql-database/notifications-db-discovered.png)

或者，可以通过在“自动保护”列中的相应下拉列表内选择“打开”选项，针对整个实例或 Always On 可用性组启用[自动保护](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm)。 [自动保护](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm)功能不仅可以一次性针对所有现有数据库启用保护，而且还会自动保护将来要添加到该实例或可用性组的所有新数据库。  

   ![针对 Always On 可用性组启用自动保护](./media/backup-azure-sql-database/enable-auto-protection.png)

## <a name="next-steps"></a>后续步骤

- [了解](restore-sql-database-azure-vm.md)如何还原已备份的 SQL Server 数据库。
- [了解](manage-monitor-sql-database-backup.md)如何管理已备份的 SQL Server 数据库。

