---
title: 还原 Azure VM 上的 SQL Server 数据库
description: 本文介绍如何还原 Azure VM 上运行的、使用 Azure 备份服务备份的 SQL Server 数据库。 你还可以使用跨区域还原将数据库还原到次要区域。
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 0d6feb512ab4ebcc5b5eaffafe607602fc552984
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985434"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>还原 Azure VM 上的 SQL Server 数据库

本文介绍如何还原 Azure 虚拟机 (VM) 上运行的、已由 [Azure 备份](backup-overview.md)服务备份到 Azure 备份恢复服务保管库的 SQL Server 数据库。

本文介绍如何还原 SQL Server 数据库。 有关详细信息，请参阅[备份 Azure VM 上的 SQL Server 数据库](backup-azure-sql-database.md)。

## <a name="restore-to-a-time-or-a-recovery-point"></a>还原到某个时间点或恢复点

Azure 备份可以还原 Azure VM 上运行的 SQL Server 数据库，如下所述：

- 使用事务日志备份还原到特定的日期或时间（精确到秒）。 Azure 备份可自动确定相应的完整备份、差异备份和日志链备份，这些是根据所选时间进行还原所必需的。
- 还原特定的完整备份或差异备份，这样就可以还原到特定的恢复点。

## <a name="prerequisites"></a>先决条件

在还原数据库之前，请注意以下事项：

- 可将数据库还原到同一 Azure 区域中的 SQL Server 实例。
- 目标服务器必须注册到与源服务器相同的保管库。
- 若要将 TDE 加密的数据库还原到另一个 SQL Server，需先[将证书还原到目标服务器](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server)。
- 应使用[还原为文件](#restore-as-files)选项还原启用了 [CDC](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server) 的数据库。
- 在还原“master”数据库之前，请使用启动选项 **-m AzureWorkloadBackup** 在单用户模式下启动 SQL Server 实例。
  - **-m** 的值是客户端的名称。
  - 只能使用指定的客户端名称打开连接。
- 对于所有系统数据库（模型数据库、master 数据库、msdb 数据库），请在触发还原操作之前停止 SQL Server 代理服务。
- 关闭任何可能尝试与其中任何数据库建立连接的应用程序。
- 如果服务器上有多个实例在运行，所有实例都应启动并运行，否则，该服务器不会显示在可将数据库还原到的目标服务器列表中。

## <a name="restore-a-database"></a>还原数据库

若要进行还原，需要以下权限：

- 在其中执行还原的保管库中的“备份操作员”权限。
- 对已备份的源 VM 的**参与者（写入）** 访问权限。
- 对目标 VM 的**参与者（写入）** 访问权限：
  - 若要还原到同一 VM，则此项将是源 VM。
  - 若要还原到备用位置，则此项将是新的目标 VM。

按如下所述进行还原：

1. 打开在其中注册 SQL Server VM 的保管库。
2. 在保管库仪表板的“使用情况”下，选择“备份项”********。
3. 在“备份项”中的“备份管理类型”下，选择“Azure VM 中的 SQL”。************

    ![选择“Azure VM 中 SQL”](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. 选择要还原的数据库。

    ![选择要还原的数据库](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. 查看数据库菜单。 它提供有关数据库备份的信息，其中包括：

    - 最旧和最新的还原点。
    - 处于完整和批量日志记录恢复模式的、且已配置事务日志备份的数据库在过去 24 小时的日志备份状态。

6. 选择“还原”。

    ![选择“还原”](./media/backup-azure-sql-database/restore-db.png)

7. 在“还原配置”中，指定要将数据还原到何处（或如何还原）：****
   - **备用位置**：将数据库还原到备用位置，同时保留原始源数据库。
   - **覆盖数据库**：将数据还原到原始源所在的同一 SQL Server 实例。 此选项会覆盖原始数据库。

        > [!IMPORTANT]
        > 如果选定的数据库属于 Always On 可用性组，则 SQL Server 不允许覆盖数据库。 仅“备用位置”可用。****
        >
   - **作为文件还原**：不是作为数据库还原，而是以后使用 SQL Server Management Studio 在包含备份文件的计算机上，还原可作为数据库恢复的备份文件。
     ![“还原配置”菜单](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>还原到备用位置

1. 在“还原配置”菜单中的“还原位置”下，选择“备用位置”。  
1. 选择要将数据库还原到其中的 SQL Server 名称和实例。
1. 在“还原数据库名称”框中，输入目标数据库的名称。
1. 如果适用，请选择“当选定的 SQL 实例上已存在同名的 DB 时覆盖”。****
1. 选择“还原点”，并选择是[还原到特定时间点](#restore-to-a-specific-point-in-time)，还是[还原到特定恢复点](#restore-to-a-specific-restore-point)。

    ![选择还原点](./media/backup-azure-sql-database/select-restore-point.png)

    ![还原到时间点](./media/backup-azure-sql-database/restore-to-point-in-time.png)

1. 在“高级配置”菜单中：****

    - 若要使数据库在还原后保持不可运行状态，请启用“使用 NORECOVERY 还原”。
    - 若要更改目标服务器上的还原位置，请输入新的目标路径。

        ![输入目标路径](./media/backup-azure-sql-database/target-paths.png)

1. 选择“确定”以触发还原。 在“通知”区域中或在保管库的“备份作业”视图下跟踪还原进度。

    > [!NOTE]
    > 时间点还原仅适用于采用完整和批量日志记录恢复模式的数据库日志备份。

### <a name="restore-and-overwrite"></a>还原并覆盖

1. 在“还原配置”菜单中的“还原位置”下，选择“覆盖数据库” > “确定”。   

    ![选择“覆盖数据库”](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. 在“选择还原点”中选择“日志(时间点)”，以[还原到特定的时间点](#restore-to-a-specific-point-in-time)。  或者选择“完整和差异”以还原到[特定的恢复点](#restore-to-a-specific-restore-point)。

    > [!NOTE]
    > 时间点还原仅适用于采用完整和批量日志记录恢复模式的数据库日志备份。

### <a name="restore-as-files"></a>还原为文件

若要将备份数据作为 .bak 文件而不是数据库还原，请选择“作为文件还原”。**** 将文件转储到指定的路径后，可将这些文件放在要将其作为数据库还原到的任何计算机上。 由于可将这些文件移到任何计算机上，因此现在可以跨订阅和区域进行数据还原。

1. 在“还原位置及还原方式”下，选择“作为文件还原” 。
1. 选择要将备份还原到的 SQL Server 名称。
1. 在“服务器上的目标路径”中，输入在步骤 2 中选择的服务器上的文件夹路径。**** 此位置是服务要将全部所需备份文件转储到的位置。 通常，如果将网络共享路径或已装载的 Azure 文件共享的路径指定为目标路径，会使同一网络中的其他计算机或其上装载的同一 Azure 文件共享更加轻松地访问这些文件。<BR>

    >若要在目标注册 VM 装载的 Azure 文件共享上还原数据库备份文件，请确保 NT AUTHORITY\SYSTEM 有权访问该文件共享。 你可以执行以下步骤，以授予对 VM 上装载的 AFS 的读/写权限：
    >
    >- 运行 `PsExec -s cmd` 进入 NT AUTHORITY\SYSTEM shell
    >   - 执行 `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>`
    >   - 使用 `dir \\<storageacct>.file.core.windows.net\<filesharename>` 验证访问权限
    >- 从备份保管库启动“作为文件还原”操作，以还原到 `\\<storageacct>.file.core.windows.net\<filesharename>`（路径）<BR>
    可以从 [Sysinternals](/sysinternals/downloads/psexec) 页下载 PsExec。

1. 选择“确定”  。

    ![选择“作为文件还原”](./media/backup-azure-sql-database/restore-as-files.png)

1. 选择“还原点”，并选择是[还原到特定时间点](#restore-to-a-specific-point-in-time)，还是[还原到特定恢复点](#restore-to-a-specific-restore-point)。

1. 与所选恢复点关联的所有备份文件将转储到目标路径中。 可以使用 SQL Server Management Studio 在包含这些文件的任何计算机上将其作为数据库还原。

    ![已在目标路径中还原备份文件](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>还原到特定时间点

如果已选择“日志(时间点)”作为还原类型，请执行以下操作：

1. 在“还原日期/时间”下，打开日历。**** 在“日历”中，包含恢复点的日期以粗体显示，当前日期已突出显示。
1. 选择包含恢复点的日期。 不能选择没有恢复点的日期。

    ![打开日历](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. 选择日期后，时间线图会显示连续范围内的可用恢复点。
1. 在时间线图表中指定恢复时间，或选择一个时间。 然后选择“确定”。 

### <a name="restore-to-a-specific-restore-point"></a>还原到特定还原点

如果已选择“完整和差异”作为还原类型，请执行以下操作：

1. 在列表中选择一个恢复点，然后选择“确定”完成还原点过程。****

    ![选择完整恢复点](./media/backup-azure-sql-database/choose-full-recovery-point.png)

    >[!NOTE]
    > 默认情况下，将显示过去 30 天的恢复点。 通过选择“筛选器”并选择自定义范围，可以显示 30 天之前的恢复点。

### <a name="restore-databases-with-large-number-of-files"></a>还原包含大量文件的数据库

如果数据库中文件的总字符串大小超过[特定的限制](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)，Azure 备份会将数据库文件列表存储在不同的 PIT 组件中，因此在执行还原操作期间无法设置目标还原路径。 文件将还原到 SQL 默认路径。

  ![还原包含大文件的数据库](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="cross-region-restore"></a>跨区域还原

作为还原选项之一，跨区域还原 (CRR) 使你可以还原托管在辅助区域中的 Azure Vm 上的 SQL 数据库，这是一个 Azure 配对区域。

若要在预览版期间加入此功能，请阅读[“准备阶段”部分](./backup-create-rs-vault.md#set-cross-region-restore)。

若要查看是否启用了 CRR，请按照[配置跨区域还原](backup-create-rs-vault.md#configure-cross-region-restore)中的说明进行操作

### <a name="view-backup-items-in-secondary-region"></a>查看次要区域中的备份项

如果启用了 CRR，则可以查看次要区域中的备份项。

1. 从门户中转到 "**恢复服务保管库**  >  **备份项**"。
1. 选择 **次要区域** ，查看次要区域中的项目。

>[!NOTE]
>只有支持 CRR 功能的备份管理类型才会显示在列表中。 目前，只允许将辅助区域数据还原到次要区域。

![辅助区域中的备份项](./media/backup-azure-sql-database/backup-items-secondary-region.png)

![辅助区域中的数据库](./media/backup-azure-sql-database/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>在次要区域中进行还原

次要区域还原用户体验将类似于主要区域还原用户体验。 在 "还原配置" 窗格中配置详细信息以配置还原时，系统将提示您仅提供辅助区域参数。

![在何处以及如何还原](./media/backup-azure-sql-database/restore-secondary-region.png)

>[!NOTE]
>次要区域中的虚拟网络需要单独分配，并且不能用于该资源组中的任何其他 Vm。

![“正在触发还原”通知](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>
>- 触发还原并在数据传输阶段中，无法取消还原作业。
>- 在次要区域中恢复所需的 Azure 角色与主要区域中的角色相同。

### <a name="monitoring-secondary-region-restore-jobs"></a>监视次要区域还原作业

1. 在门户中，转到“恢复服务保管库” > “备份作业”**** ****
1. 选择 **次要区域** ，查看次要区域中的项目。

    ![筛选的备份作业](./media/backup-azure-sql-database/backup-jobs-secondary-region.png)

## <a name="next-steps"></a>后续步骤

[管理和监视](manage-monitor-sql-database-backup.md) Azure 备份服务备份的 SQL Server 数据库。
