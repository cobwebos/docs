---
title: 故障排除系统状态备份
description: 在本文中，了解如何在本地 Windows 服务器的系统状态备份中解决问题。
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 28647b72334d592692c5fe1b031735330d1a0509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969575"
---
# <a name="troubleshoot-system-state-backup"></a>故障排除系统状态备份

本文介绍了在使用系统状态备份时可能会遇到的问题的解决方案。

## <a name="basic-troubleshooting"></a>基本故障排除

我们建议您在开始对系统状态备份进行故障排除之前执行以下验证：

- [确保 Microsoft Azure 恢复服务 (MARS) 代理是最新版](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [确保在 MARS 代理和 Azure 之间存在网络连接](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- 确保 Microsoft Azure 恢复服务正在运行（在服务控制台中）。 如有必要，请重启并重试操作
- [确保在暂存文件夹位置有 5-10% 的可用卷空间](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- [检查其他进程或防病毒软件是否正在干扰 Azure 备份](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [计划的备份失败，但手动备份成功](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)
- 确保 OS 有最新更新
- [确保从备份中排除不受支持的驱动器和具有不支持属性的文件](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- 请确保受保护系统上的系统时钟配置为正确时区**** <br>
- [确保服务器至少具有 .Net Framework 版本 4.5.2 和更高版本](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- 如果您尝试将**服务器重新注册**到保管库，则： <br>
  - 确保代理在服务器上卸载，并且从门户中删除 <br>
  - 使用的密码正是一开始用于注册服务器的 <br>
- 如果这是脱机备份，请确保在开始脱机备份操作之前在源和复制计算机上安装 Azure PowerShell 版本 3.7.0
- [备份代理在 Azure 虚拟机上运行时的注意事项](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>限制

- Microsoft 不建议使用系统状态恢复来恢复到不同的硬件
- 系统状态备份当前支持"本地"Windows 服务器。 此功能不适用于 Azure VM。

## <a name="prerequisites"></a>先决条件

在使用 Azure 备份对系统状态备份进行故障排除之前，请执行以下先决条件检查。  

### <a name="verify-windows-server-backup-is-installed"></a>验证安装了 Windows 服务器备份

确保在服务器中安装并启用 Windows 服务器备份。 要检查安装状态，请运行此 PowerShell 命令：

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

如果输出显示 **"安装状态****"为可用**，则意味着 Windows Server 备份功能可用于安装，但未安装在服务器上。 但是，如果未安装 Windows 服务器备份，请使用以下方法之一安装它。

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>方法 1：使用 PowerShell 安装 Windows 服务器备份

要使用 PowerShell 安装 Windows 服务器备份，请运行以下命令：

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>方法 2：使用服务器管理器安装 Windows 服务器备份

要使用服务器管理器安装 Windows 服务器备份，请执行以下步骤：

1. 在 **"服务器管理器"中**，单击"**添加角色和功能**"。 将显示 **"添加角色和功能"向导**。

    ![仪表板](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. 选择**安装类型**，然后单击 **"下一步**"。

    ![安装类型](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. 从服务器池中选择服务器，然后单击 **"下一步**"。 在"服务器角色"中，保留默认选择，然后单击"**下一步**"。
4. 在**功能**中选择**Windows 服务器备份**选项卡，然后单击 **"下一步**"。

    ![features](./media/backup-azure-system-state-troubleshoot/features.png)

5. 在 **"确认"** 选项卡中，单击"**安装**"以启动安装过程。
6. 在 **"结果"** 选项卡中，将显示 Windows 服务器备份功能已成功安装在 Windows 服务器上。

    ![result](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>系统卷信息权限

确保本地系统完全控制位于安装 Windows 的卷中的 **"系统卷信息"** 文件夹。 通常这是**C：\系统卷信息**。 如果上述权限设置不正确，Windows 服务器备份可能会失败

### <a name="dependent-services"></a>从属服务

确保以下服务处于运行状态：

**服务名称** | **启动类型**
--- | ---
远程程序呼叫（RPC） | 自动
COM+ 事件系统（事件系统） | 自动
系统事件通知服务（SENS） | 自动
卷影副本（VSS） | 手动
微软软件影子复制提供商（SWPRV） | 手动

### <a name="validate-windows-server-backup-status"></a>验证 Windows 服务器备份状态

要验证 Windows 服务器备份状态，请执行以下步骤：

- 确保 WSB 电源外壳正在运行

  - 从`Get-WBJob`提升的 PowerShell 运行并确保它不会返回以下错误：

    > [!WARNING]
    > 获取 WBJob： 术语"获取-WBJob"不被识别为 cmdlet、函数、脚本文件或可操作程序的名称。 检查名称的拼写，如果包含路径，请验证该路径是否正确，并重试。

    - 如果此错误失败，则按照先决条件的步骤 1 中所述，在服务器计算机上重新安装 Windows 服务器备份功能。

  - 通过从提升的命令提示符运行以下命令，确保 WSB 备份正常工作：

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >将 X 替换为要存储系统状态备份映像的卷的驱动器号。

    - 通过从提升的 PowerShell 运行`Get-WBJob`命令来定期检查作业的状态
    - 备份作业完成后，通过运行`Get-WBJob -Previous 1`命令检查作业的最终状态

如果作业失败，它指示 WSB 问题将导致 MARS 代理系统状态备份失败。

## <a name="common-errors"></a>常见错误

### <a name="vss-writer-timeout-error"></a>VSS 编写器超时错误

| 症状 | 原因 | 解决方法
| -- | -- | --
| - MARS 代理失败，错误消息："WSB 作业失败 VSS 错误。 检查 VSS 事件日志以解决故障"<br/><br/> - VSS 应用程序事件日志中存在以下错误日志："VSS 编写器已拒绝错误为 0x800423f2 的事件，编写器的超时在冻结和解冻事件之间过期。| 由于计算机上缺少 CPU 和内存资源，VSS 编写器无法及时完成 <br/><br/> 另一个备份软件已在使用 VSS 编写器，因此无法为此备份完成快照操作 | 等待 CPU/内存在系统上释放或中止占用太多内存/CPU 的进程，然后重试该操作。 <br/><br/>  等待正在进行的备份完成，并在以后计算机上未运行备份时尝试操作。

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>磁盘空间不足，无法扩展卷影副本

| 症状 | 解决方法
| -- | --
| - MARS 代理失败，出现错误消息：由于包含系统文件的卷上的磁盘空间不足，卷影副本卷无法增长，因此备份失败 <br/><br/> - volsnap 系统事件日志中存在以下错误/警告日志："卷 C 上磁盘空间不足：用于扩展 C 卷卷副本的卷影副本存储：由于此故障，C 卷的所有卷影副本都有被删除的风险" | - 释放事件日志中突出显示的卷中的空间，以便在备份过程中有足够的空间来扩展卷影副本 <br/><br/> - 在配置卷影复制空间时，我们可以限制用于卷影复制的空间量。 有关详细信息，请参阅[本文](https://docs.microsoft.com/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage)

### <a name="efi-partition-locked"></a>EFI 分区锁定

| 症状 | 解决方法
| -- | --
| MARS 代理失败，出现错误消息："当 EFI 系统分区锁定时，系统状态备份失败。 这可能是由于第三方安全或备份软件的系统分区访问" | - 如果问题是由于第三方安全软件，那么您需要联系防病毒供应商，以便他们可以允许 MARS 代理 <br/><br/> - 如果第三方备份软件正在运行，则等待它完成，然后重试备份

## <a name="next-steps"></a>后续步骤

- 有关资源管理器部署中的 Windows 系统状态的详细信息，请参阅[备份 Windows 服务器系统状态](backup-azure-system-state.md)
