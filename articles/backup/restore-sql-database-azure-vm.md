---
title: 还原 Azure VM 上的 SQL Server 数据库
description: 本文介绍如何还原 Azure VM 上运行的、使用 Azure 备份服务备份的 SQL Server 数据库。
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 642476c98ca223da01bda5c6eb79ee9b53732468
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79252449"
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
- 若要将 TDE 加密的数据库还原到另一个 SQL Server，需先[将证书还原到目标服务器](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)。
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
2. 在保管库仪表板的“使用情况”下，选择“备份项”   。
3. 在“备份项”中的“备份管理类型”下，选择“Azure VM 中的 SQL”。   

    ![选择“Azure VM 中 SQL”](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. 选择要还原的数据库。

    ![选择要还原的数据库](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. 查看数据库菜单。 它提供有关数据库备份的信息，其中包括：

    - 最旧和最新的还原点。
    - 处于完整和批量日志记录恢复模式的、且已配置事务日志备份的数据库在过去 24 小时的日志备份状态。

6. 选择“还原”。 

    ![选择“还原”](./media/backup-azure-sql-database/restore-db.png)

7. 在“还原配置”中，指定要将数据还原到何处（或如何还原）： 
   - **备用位置**：将数据库还原到备用位置，同时保留原始源数据库。
   - **覆盖 DB**：将数据还原到原始源所在的同一 SQL Server 实例。 此选项将覆盖原始数据库。

    > [!IMPORTANT]
    > 如果选定的数据库属于 Always On 可用性组，则 SQL Server 不允许覆盖数据库。 仅“备用位置”可用。 
    >
   - **作为文件还原**：不是作为数据库还原，而是以后使用 SQL Server Management Studio 在包含备份文件的计算机上，还原可作为数据库恢复的备份文件。
     ![“还原配置”菜单](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>还原到备用位置

1. 在“还原配置”菜单中的“还原位置”下，选择“备用位置”。   
2. 选择要将数据库还原到其中的 SQL Server 名称和实例。
3. 在“还原数据库名称”框中，输入目标数据库的名称。 
4. 如果适用，请选择“当选定的 SQL 实例上已存在同名的 DB 时覆盖”。 
5. 选择“确定”  。

    ![为“还原配置”菜单提供值](./media/backup-azure-sql-database/restore-configuration.png)

6. 在“选择还原点”中，选择是要[还原到特定的时间点](#restore-to-a-specific-point-in-time)，还是[还原到特定的恢复点](#restore-to-a-specific-restore-point)。 

    > [!NOTE]
    > 时间点还原仅适用于采用完整和批量日志记录恢复模式的数据库日志备份。

### <a name="restore-and-overwrite"></a>还原并覆盖

1. 在“还原配置”菜单中的“还原位置”下，选择“覆盖数据库” > “确定”。    

    ![选择“覆盖数据库”](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. 在“选择还原点”中选择“日志(时间点)”，以[还原到特定的时间点](#restore-to-a-specific-point-in-time)。   或者选择“完整和差异”以还原到[特定的恢复点](#restore-to-a-specific-restore-point)。 

    > [!NOTE]
    > 时间点还原仅适用于采用完整和批量日志记录恢复模式的数据库日志备份。

### <a name="restore-as-files"></a>作为文件还原

若要将备份数据作为 .bak 文件而不是数据库还原，请选择“作为文件还原”。  将文件转储到指定的路径后，可将这些文件放在要将其作为数据库还原到的任何计算机上。 由于可将这些文件移到任何计算机，因此现在可以跨订阅和区域还原数据。

1. 在“还原配置”菜单中的“还原位置”下，选择“作为文件还原”。   
2. 选择要将备份还原到的 SQL Server 名称。
3. 在“服务器上的目标路径”中，输入在步骤 2 中选择的服务器上的文件夹路径。  此位置是服务要将全部所需备份文件转储到的位置。 通常，将网络共享路径或已装载的 Azure 文件共享的路径指定为目标路径，可让同一网络中的其他计算机或者装载了相同 Azure 文件共享的计算机更轻松地访问这些文件。<BR>

    >若要将数据库备份文件还原到安装在目标注册 VM 上的 Azure 文件共享上，请确保 NT AUTHORITY\SYSTEM 有权访问该文件共享。 你可以执行以下步骤来向 VM 上装载的 AFS 授予读取/写入权限：
    >
    >- 运行`PsExec -s cmd`以进入 NT AUTHORITY\SYSTEM shell
    >   - 执行 `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>`
    >   - 使用验证访问`dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- 将备份保管库中的文件作为路径进行`\\<storageacct>.file.core.windows.net\<filesharename>`还原<BR>
    可以通过下载 Psexec<https://docs.microsoft.com/sysinternals/downloads/psexec>

4. 选择“确定”  。

    ![选择“作为文件还原”](./media/backup-azure-sql-database/restore-as-files.png)

5. 选择与所有可用 .bak 文件要还原到的时间对应的**还原点**。

    ![选择还原点](./media/backup-azure-sql-database/restore-point.png)

6. 与所选恢复点关联的所有备份文件将转储到目标路径中。 可以使用 SQL Server Management Studio 在包含这些文件的任何计算机上将其作为数据库还原。

    ![已在目标路径中还原备份文件](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>还原到特定时间点

如果已选择“日志(时间点)”作为还原类型，请执行以下操作：****

1. 在“还原日期/时间”下，打开日历。**** 在“日历”中，包含恢复点的日期以粗体显示，当前日期已突出显示。
1. 选择包含恢复点的日期。 不能选择没有恢复点的日期。

    ![打开日历](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. 选择日期后，时间线图会显示连续范围内的可用恢复点。
1. 在时间线图表中指定恢复时间，或选择一个时间。 然后选择“确定”  。

    ![选择还原点](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

1. 在 "**高级配置**" 菜单上，如果要在还原后保留数据库 nonoperational，请启用 "**使用 NORECOVERY 还原**"。
1. 若要更改目标服务器上的还原位置，请输入新的目标路径。
1. 选择“确定”  。

    ![高级配置菜单](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. 在“还原”菜单中，选择“还原”启动还原作业。********
1. 在“通知”区域中跟踪还原进度，或者在数据库菜单中选择“还原作业”来跟踪进度。********

    ![还原作业的进度](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>还原到特定还原点

如果已选择“完整和差异”作为还原类型，请执行以下操作：****

1. 在列表中选择一个恢复点，然后选择“确定”完成还原点过程。****

    ![选择完整恢复点](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

    >[!NOTE]
    > 默认情况下，将显示过去30天内的恢复点。 可以通过单击 "**筛选器**" 并选择自定义范围来显示30天之前的恢复点。

1. 在 "**高级配置**" 菜单上，如果要在还原后保留数据库 nonoperational，请启用 "**使用 NORECOVERY 还原**"。
1. 若要更改目标服务器上的还原位置，请输入新的目标路径。
1. 选择“确定”  。

    ![高级配置菜单](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. 在“还原”菜单中，选择“还原”启动还原作业。********
1. 在“通知”区域中跟踪还原进度，或者在数据库菜单中选择“还原作业”来跟踪进度。********

    ![还原作业的进度](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>还原包含大量文件的数据库

如果数据库中文件的总字符串大小超过[特定的限制](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)，Azure 备份会将数据库文件列表存储在不同的 PIT 组件中，因此在执行还原操作期间无法设置目标还原路径。 文件将还原到 SQL 默认路径。

  ![还原包含大文件的数据库](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>后续步骤

[管理和监视](manage-monitor-sql-database-backup.md) Azure 备份服务备份的 SQL Server 数据库。
