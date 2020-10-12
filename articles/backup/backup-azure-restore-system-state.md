---
title: 将系统状态还原为 Windows Server
description: 逐步介绍如何从 Azure 备份中还原 Windows Server 系统状态。
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 824ed5e71934af370211bfa8b4c266fe4323b4ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89377431"
---
# <a name="restore-system-state-to-windows-server"></a>将系统状态还原为 Windows Server

本文介绍如何从 Azure 恢复服务保管库还原 Windows Server 系统状态备份。 若要还原系统状态，必须具有系统状态备份（使用[备份系统状态](backup-azure-system-state.md#back-up-windows-server-system-state)中的说明创建），并确保已安装[最新版本的 Microsoft Azure 恢复服务 (MARS) 代理](https://aka.ms/azurebackup_agent)。 需通过两个步骤才能从 Azure 恢复服务保管库恢复 Windows Server 系统状态数据：

1. 从 Azure 备份将系统状态还原为文件。 从 Azure 备份将系统状态还原为文件时，可以:
   * 将系统状态还原到进行备份的同一服务器，或者
   * 将系统状态文件还原到备用服务器。

2. 使用 Windows Server 备份实用工具将还原的系统状态文件应用到 Windows Server。

## <a name="recover-system-state-files-to-the-same-server"></a>将系统状态文件恢复到同一服务器

以下步骤介绍如何将 Windows Server 配置回退到以前的状态。 将服务器配置回退到已知的稳定状态至关重要。 以下步骤从恢复服务保管库还原服务器的系统状态。

1. 打开“Microsoft Azure 备份”管理单元。 如果不知道管理单元的安装位置，请在计算机或服务器中搜索“Microsoft Azure 备份”。

    该桌面应用应会显示在搜索结果中。

2. 选择“恢复数据”以启动向导。

    ![恢复数据](./media/backup-azure-restore-windows-server/recover.png)

3. 若要将数据还原到同一台服务器或计算机，请在“开始使用”窗格中选择“此服务器(`<server name>`)”，然后选择“下一步”。  

    ![选择“此服务器”选项将数据还原到同一台计算机](./media/backup-azure-restore-system-state/samemachine.png)

4. 在“选择恢复模式”窗格中选择“系统状态”，然后选择“下一步”  。

    ![浏览文件](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. 在“选择卷和日期”窗格的日历中，选择一个恢复点****。

    可从任意恢复时间点还原。 以 **粗体** 显示的日期表示至少有一个可用的恢复点。 选择日期后，如果有多个恢复点可用，请从“时间” **** 下拉菜单中选择特定的恢复点。

    ![卷和日期](./media/backup-azure-restore-system-state/select-date.png)

6. 选择要还原的恢复点后，选择“下一步”。

    Azure 备份会装载本地恢复点，并将其用作恢复卷。

7. 在下一窗格中，指定恢复的系统状态文件的目标。 然后选择“浏览”以打开 Windows 资源管理器，并找到所需的文件和文件夹。 “创建副本以便具有两个版本”选项将创建现有系统状态文件存档中各个文件的副本，而不是创建整个系统状态存档的副本****。

    ![恢复选项](./media/backup-azure-restore-system-state/recover-as-files.png)

8. 验证“确认”窗格中的恢复详细信息，并选择“恢复” 。

   ![选择“恢复”确认恢复操作](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. 将恢复目标中的 WindowsImageBackup 目录复制到服务器的非关键卷中**。 通常，Windows OS 卷是关键卷。

10. 恢复成功后，按照[对 Windows Server 应用已恢复的系统状态](#apply-restored-system-state-on-a-windows-server)中的步骤进行操作，完成系统状态恢复过程。

## <a name="recover-system-state-files-to-an-alternate-server"></a>将系统状态文件恢复到备用服务器

如果 Windows Server 已损坏或不可访问，并想要通过恢复 Windows Server 系统状态将其恢复到稳定状态，则可以从另一服务器还原已损坏服务器的系统状态。 在单独服务器上使用以下步骤还原系统状态。  

这些步骤中使用的术语包括：

* *源计算机* –从中创建备份并且当前不可用的原始计算机。
* *目标计算机* – 要将数据恢复到的计算机。
* *示例保管库* – *源计算机* 和 *目标计算机* 注册到的恢复服务保管库。

> [!NOTE]
> 从一台计算机创建的备份无法还原到运行较低版本操作系统的计算机。 例如，从 Windows Server 2016 计算机创建的备份不能还原到 Windows Server 2012 R2。 但是，可进行反向操作。 可使用 Windows Server 2012 R2 中的备份还原 Windows Server 2016。
>

1. 在*目标计算机*上打开 " **Microsoft Azure 备份**" 管理单元。
2. 确保“*目标计算机*”和“*源计算机*”已还原到同一个恢复服务保管库。
3. 选择“恢复数据”以启动工作流。
4. 选择“另一台服务器” ****

    ![另一台服务器](./media/backup-azure-restore-system-state/anotherserver.png)

5. 提供对应于“*示例保管库*”的保管库凭据文件。 如果保管库凭据文件无效（或已过期），请在 Azure 门户中从“示例保管库”下载新的保管库凭据文件。** 提供保管库凭据文件后，会显示与保管库凭据文件相关联的恢复服务保管库。

6. 在“选择备份服务器”窗格中，从显示的计算机列表中选择“源计算机”**。
7. 在“选择恢复模式”窗格中选择“系统状态”，并选择“下一步” 。

    ![搜索](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. 在“选择卷和日期”窗格的日历中，选择一个恢复点****。 可从任意恢复时间点还原。 以 **粗体** 显示的日期表示至少有一个可用的恢复点。 选择日期后，如果有多个恢复点可用，请从“时间” **** 下拉菜单中选择特定的恢复点。

    ![搜索项](./media/backup-azure-restore-system-state/select-date.png)

9. 选择要还原的恢复点后，选择“下一步”。

10. 在“选择系统状态恢复模式”窗格中，指定要在其中恢复系统状态文件的目标，然后选择“下一步” 。

    ![Encryption](./media/backup-azure-restore-system-state/recover-as-files.png)

    “创建副本以便具有两个版本”选项将创建现有系统状态文件存档中各个文件的副本，而不是创建整个系统状态存档的副本****。

11. 验证“确认”窗格中的恢复详细信息，并选择“恢复”。

    ![选择“恢复”按钮确认恢复过程](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. 将 WindowsImageBackup 目录复制到服务器的非关键卷（如 D:\)）**。 通常，Windows OS 卷是关键卷。

13. 若要完成恢复过程，请使用下面的[对 Windows 服务器应用已恢复的系统状态文件](#apply-restored-system-state-on-a-windows-server)部分。

## <a name="apply-restored-system-state-on-a-windows-server"></a>对 Windows Server 应用已恢复的系统状态

使用 Azure 恢复服务代理将系统状态还原为文件后，使用 Windows Server 备份实用工具将已恢复的系统状态应用到 Windows Server。 已在服务器上提供 Windows Server 备份实用工具。 以下步骤介绍如何应用已恢复的系统状态。

1. 打开 Windows Server 备份管理单元。 如果不知道该管理单元的安装位置，请在计算机或服务器中搜索“Windows Server 备份”****。

    该桌面应用将显示在搜索结果中。 如果没有显示应用，或者在打开应用程序时遇到错误，则必须安装“服务器管理器”的“添加功能向导”中提供的“Windows Server 备份功能”及其下面的依赖组件  。

1. 在该管理单元中，选择“本地备份”****。

    ![选择“本地备份”以便从本地备份还原](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

1. 在本地备份控制台中的“操作窗格”中，选择“恢复”，打开“恢复向导” 。

1. 选择“存储在其他位置的备份”选项，然后选择“下一步” 。

   ![选择恢复到其他服务器](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

1. 指定位置类型时，如果系统状态备份已恢复到另一服务器，请选择“远程共享文件夹”****。 如果在本地恢复系统状态，则选择“本地驱动器”****。

    ![选择是从本地服务器还是从另一服务器进行恢复](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

1. 输入 WindowsImageBackup 目录的路径，或选择包含此目录的本地驱动器（如 D:\WindowsImageBackup）（该目录已在系统状态文件恢复过程中使用 Azure 恢复服务代理恢复），然后选择“下一步”。

    ![共享文件的路径](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

1. 选择要还原的系统状态版本，然后选择“下一步”。

1. 在“选择恢复类型”窗格中选择“系统状态”，然后选择“下一步” 。

1. 对于系统状态恢复位置，请选择“原始位置”，然后选择“下一步” 。

    如果要还原域控制器，则会看到以下附加选项：

    ![系统状态恢复位置](./media/backup-azure-restore-system-state/location-for-system-state-recovery.png)

    >[!NOTE]
    >如果你明确想要执行所有 Active Directory 数据的权威还原，请仅选择 "对 Active Directory 文件执行权威还原"。

1. 查看确认详细信息，验证重新启动设置，然后选择“恢复”，应用已还原的系统状态文件。

    ![启动还原系统状态文件](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

    >[!NOTE]
    >如果要在 DSRM 模式下执行还原，请不要选择“自动重新启动服务器”选项。

1. 成功完成还原后，需要在正常模式下重启服务器。 打开命令提示符并键入以下命令：`bcdedit /deletevalue safeboot`
1. 重新启动服务器。

## <a name="special-considerations-for-system-state-recovery-on-a-domain-controller"></a>域控制器上的系统状态恢复的特殊注意事项

系统状态备份包括 Active Directory 数据。 使用以下步骤将 Active Directory 域服务 (AD DS) 从当前状态还原为以前的状态。 可在两种方案中完成此类型的还原：

* 当林中没有剩下正常运行的域控制器时，还原所有 Active Directory 数据
* 当这些对象已删除或损坏时，还原部分 Active Directory 数据

本文仅讨论第一种方案，其要求对 AD DS 进行非授权还原，对 sysvol 文件夹进行授权还原。  如果需要执行第二种方案（其中域控制器仍可正常运行，但需要还原特定 AD 对象），请参阅[这些说明](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac)。

1. 请按照此处的步骤[将系统状态文件恢复到备用服务器](#recover-system-state-files-to-an-alternate-server)。
1. 使用以下命令在目录服务修复模式下重新启动服务器**。 在提升的命令提示符中：

    ```cmd
    Bcdedit /set safeboot dsrepair
    Shutdown /r /t 0
    ```

1. 若要在系统状态还原过程中恢复 Active Directory，可以选择以下两种方法之一：

    * 请按照上述说明，使用 Windows Server 备份实用工具[对 Windows Server 应用已恢复的系统状态](#apply-restored-system-state-on-a-windows-server)。

        >[!NOTE]
        >如果要还原所有 Active Directory 数据（且林中没有剩下正常运行的域控制器），请确保在上面的步骤 9 中选择“对 Active Directory 文件执行授权还原”。

    * 使用 [wbadmin](/windows-server/administration/windows-commands/wbadmin-start-systemstaterecovery) 实用工具从命令行执行还原。

        将需要想要使用的备份的版本标识符。 可以通过运行以下命令来获取版本标识符列表：

        ```cmd
        wbadmin get versions -backuptarget <servername\sharename>
        ```

        然后，可以使用该版本标识符来运行还原。

        例如，若要使用 2020 年 4 月 30 日上午 9 点的备份（存储在 `server01` 的共享资源 `\\servername\share` 上）[对 AD DS 执行非授权还原以及对 sysvol 文件夹执行授权还原](/windows-server/identity/ad-ds/manage/ad-forest-recovery-nonauthoritative-restore)，请键入：

        ```cmd
        wbadmin start systemstaterecovery -version:04/30/2020-09:00 -backupTarget:\\servername\share -machine:server01 -authsysvol
        ```

1. 成功完成还原后，应在正常模式下重启服务器。 打开命令提示符并键入以下命令：`bcdedit /deletevalue safeboot`
1. 重新启动服务器。

## <a name="troubleshoot-failed-system-state-restore"></a>解决系统状态还原失败的问题

如果未成功完成上一过程（即应用系统状态），请使用 Windows 恢复环境 (Win RE) 恢复 Windows Server。 以下步骤介绍如何使用 Win RE 进行恢复。 仅当 Windows Server 在系统状态还原后无法正常启动时使用此选项。 以下过程将清除非系统数据，务必谨慎使用。

1. 将 Windows Server 引导到 Windows 恢复环境 (Win RE)。

2. 从三个可用选项中选择“故障排除”。

    ![选择“故障排除”](./media/backup-azure-restore-system-state/winre-1.png)

3. 在“高级选项”屏幕中，选择“命令提示符”并提供服务器管理员用户名和密码**** ****。

   ![选择“命令提示符”](./media/backup-azure-restore-system-state/winre-2.png)

4. 提供服务器管理员用户名和密码。

    ![输入密码](./media/backup-azure-restore-system-state/winre-3.png)

5. 在管理员模式下打开命令提示符后，运行以下命令获取系统状态备份版本。

    ```cmd
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```

    ![获取系统状态备份版本](./media/backup-azure-restore-system-state/winre-4.png)

6. 运行以下命令获取备份中的所有可用卷。

    ```cmd
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![获取所有可用卷](./media/backup-azure-restore-system-state/winre-5.png)

7. 以下命令恢复属于系统状态备份一部分的所有卷。 请注意，此步骤仅恢复属于系统状态一部分的关键卷。 将清除所有非系统数据。

    ```cmd
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```

     ![恢复所有卷](./media/backup-azure-restore-system-state/winre-6.png)

## <a name="next-steps"></a>后续步骤

* 恢复文件和文件夹后，可 [管理备份](backup-azure-manage-windows-server.md)。
