---
title: 解决系统状态备份的问题
description: 本文介绍如何为本地 Windows 服务器解决系统状态备份中的问题。
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 7c8e68da1c5da7b25d1385a82bf7dcc2f876306d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89376275"
---
# <a name="troubleshoot-system-state-backup"></a>解决系统状态备份的问题

本文介绍使用系统状态备份时可能遇到的问题的解决方法。

## <a name="basic-troubleshooting"></a>基本故障排除

建议你在开始排查系统状态备份之前执行以下验证步骤：

- [确保 Microsoft Azure 恢复服务 (MARS) 代理是最新版本](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [确保 MARS 代理与 Azure 之间存在网络连接](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- 确保 Microsoft Azure 恢复服务正在运行（在服务控制台中）。 如有必要，请重启并重试操作
- [确保在暂存文件夹位置有 5-10% 的可用卷空间](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [检查其他进程或防病毒软件是否正在干扰 Azure 备份](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [计划的备份失败，但手动备份成功](./backup-azure-mars-troubleshoot.md#backups-dont-run-according-to-schedule)
- 确保 OS 有最新更新
- [确保从备份中排除使用不受支持的属性的不受支持驱动器和文件](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- 请确保受保护系统上的系统时钟配置为正确时区 <br>
- [确保服务器至少具有 .Net Framework 版本 4.5.2 和更高版本](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- 如果你正在尝试将服务器注册到保管库，请执行以下操作： <br>
  - 确保在服务器上卸载代理并将其从门户中删除 <br>
  - 使用的密码正是一开始用于注册服务器的 <br>
- 如果是脱机备份，请在开始脱机备份操作之前，确保源和副本计算机上都安装了 Azure PowerShell 版本 3.7.0
- [在 Azure 虚拟机上运行备份代理时的注意事项](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>限制

- Microsoft 不建议使用系统状态恢复恢复到不同的硬件
- 系统状态备份当前支持“本地”Windows 服务器。 此功能不适用于 Azure VM。

## <a name="prerequisites"></a>先决条件

在对 Azure 备份进行系统状态备份的故障排除之前，请执行以下先决条件检查。  

### <a name="verify-windows-server-backup-is-installed"></a>验证是否已安装 Windows Server 备份

确保服务器中安装并启用了 Windows Server 备份。 若要检查安装状态，请运行以下 PowerShell 命令：

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

如果输出显示“安装状态”为“可用”，则表示 Windows Server 备份功能可供安装，但尚未安装到服务器上 。 另一方面，如果未安装 Windows Server 备份，请按照以下方法之一进行安装。

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>方法 1：使用 PowerShell 安装 Windows Server 备份

若要使用 PowerShell 安装 Windows Server 备份，请运行以下命令：

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>方法 2：使用服务器管理器安装 Windows Server 备份

若要使用服务器管理器安装 Windows Server 备份，请执行以下步骤：

1. 在 **服务器管理**器中，选择 " **添加角色和功能**"。 随即会显示“添加角色和功能向导”。

    ![仪表板](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. 选择 " **安装类型** " 并选择 " **下一步**"。

    ![安装类型](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. 从服务器池中选择一个服务器，然后选择 " **下一步**"。 在服务器角色中，保留默认选择，然后选择 " **下一步**"。
4. 选择 "**功能**" 选项卡中**Windows Server 备份**，然后选择 "**下一步**"。

    ![选择功能窗口](./media/backup-azure-system-state-troubleshoot/features.png)

5. 在 " **确认** " 选项卡中，选择 " **安装** " 以启动安装过程。
6. “结果”选项卡中将显示 Windows Server 备份功能已成功安装到 Windows Server 上。

    ![安装结果](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>系统卷信息权限

确保本地系统可以完全控制安装有 Windows 的卷中的“系统卷信息”文件夹。 通常，该文件夹为 C:\System Volume Information。 如果上面的权限设置不正确，Windows Server backup 可能会失败。

### <a name="dependent-services"></a>依赖的服务

请确保下面的服务处于 "正在运行" 状态：

**服务名称** | **启动类型**
--- | ---
远程过程调用 (RPC) | 自动
COM+ 事件系统 (EventSystem) | 自动
系统事件通知服务 (SENS) | 自动
卷影复制 (VSS) | 手动
Microsoft 软件影子副本提供程序(SWPRV) | 手动

### <a name="validate-windows-server-backup-status"></a>验证 Windows Server 备份状态

若要验证 Windows Server 备份状态，请执行以下步骤：

- 确保 WSB PowerShell 正在运行

  - 从权限提升的 PowerShell 运行 `Get-WBJob`，并确保它不会返回以下错误：

    > [!WARNING]
    > Get-WBJob：无法将“Get-WBJob”一词识别为 cmdlet、函数、脚本文件或可运行程序的名称。 请检查名称的拼写，如果包含路径，请验证该路径是否正确，并重试。

    - 如果失败并提示此错误，请在服务器计算机上重新安装 Windows Server 备份功能，如先决条件的步骤 1 中所述。

  - 通过从提升的命令提示符运行以下命令，确保 WSB 备份正常工作：

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >将 X 替换为要在其中存储系统状态备份映像的卷的驱动器号。

    - 从权限提升的 PowerShell 运行 `Get-WBJob` 命令，定期检查作业的状态
    - 备份作业完成后，运行 `Get-WBJob -Previous 1` 命令，检查作业的最终状态

如果作业失败，则表明存在 WSB 问题，该问题会导致 MARS 代理系统状态备份失败。

## <a name="common-errors"></a>常见错误

### <a name="vss-writer-timeout-error"></a>VSS 写入器超时错误

| 症状 | 原因 | 解决方法
| -- | -- | --
| - MARS 代理失败，显示错误消息：“WSB 作业失败，出现了 VSS 错误。 检查 VSS 事件日志以解决失败”<br/><br/> - VSS 应用程序事件日志中出现以下错误日志：“VSS 编写器拒绝了出现 0x800423f2 错误的事件，该编写器的超时在冻结和解冻事件之间到期。”| 由于计算机的 CPU 和内存资源不足，VSS 编写器无法及时执行完毕 <br/><br/> 其他备份软件已在使用 VSS 编写器，因此无法完成针对此备份的快照操作 | 等待系统上的 CPU/内存释放，或者中止占用过多内存/CPU 的进程，然后重试操作。 <br/><br/>  等待正在进行的备份执行完毕，然后在计算机没有运行任何备份时尝试操作。

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>磁盘空间不足，影子副本无法增长

| 症状 | 解决方法
| -- | --
| - MARS 代理失败，显示错误消息：由于包含系统文件的卷上磁盘空间不足，使影子副本卷无法增长，备份失败 <br/><br/> - volsnap 系统事件日志中出现以下错误/警告日志：“卷 C: 上的磁盘空间不足，C: 影子副本的影子副本存储无法增长，由于此故障，卷 C: 的所有影子副本都存在被删除的风险” | -释放事件日志中突出显示的卷中的空间，以便在备份过程中有足够的空间用于卷影副本增长 <br/><br/> - 配置影子副本空间时，可以限制用于影子副本的空间量。 有关详细信息，请参阅[此文](/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage)

### <a name="efi-partition-locked"></a>EFI 分区已锁定

| 症状 | 解决方法
| -- | --
| MARS 代理失败，显示错误消息：“由于 EFI 系统分区已锁定，系统状态备份失败。 原因可能是第三方安全软件或备份软件访问了系统分区” | - 如果问题源自第三方安全软件，则需要联系反病毒软件供应商，让他们允许 MARS 代理 <br/><br/> - 如果第三方备份软件正在运行，请等待其完成运行，然后重试备份

## <a name="next-steps"></a>后续步骤

- 若要详细了解资源管理器部署中的 Windows 系统状态，请参阅[备份 Windows Server 系统状态](backup-azure-system-state.md)
