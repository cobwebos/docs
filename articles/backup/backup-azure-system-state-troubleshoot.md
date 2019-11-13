---
title: 将系统状态备份与 Azure 备份进行故障排除
description: 本文介绍如何解决本地 Windows server 的系统状态备份中的问题。
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
keywords: 如何备份;备份系统状态
ms.service: backup
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: dacurwin
ms.openlocfilehash: eb8bf1891f5ce96507c20e196d20ae499f30fe34
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012130"
---
# <a name="troubleshoot-system-state-backup"></a>系统状态备份故障排除

本文介绍了使用系统状态备份时可能遇到的问题的解决方案。

## <a name="basic-troubleshooting"></a>基本故障排除

建议你在开始排查系统状态备份之前执行以下验证：

- [确保 Microsoft Azure 恢复服务 (MARS) 代理是最新版](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [确保在 MARS 代理和 Azure 之间存在网络连接](https://aka.ms/AB-A4dp50)
- 确保 Microsoft Azure 恢复服务正在运行（在服务控制台中）。 如有必要，请重新启动并重试该操作
- [确保在暂存文件夹位置有 5-10% 的可用卷空间](https://aka.ms/AB-AA4dwtt)
- [检查其他进程或防病毒软件是否正在干扰 Azure 备份](https://aka.ms/AB-AA4dwtk)
- [计划的备份失败，但手动备份成功](https://aka.ms/ScheduledBackupFailManualWorks)
- 确保 OS 有最新更新
- [确保不受支持的驱动器和具有不支持的属性的文件被排除在备份之外](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- 请确保受保护系统上的系统时钟配置为正确时区 <br>
- [确保服务器至少具有 .Net Framework 版本 4.5.2 和更高版本](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- 如果尝试**将服务器重新注册**到保管库，则请执行以下操作： <br>
  - 确保在服务器上卸载代理并将其从门户中删除 <br>
  - 使用的密码正是一开始用于注册服务器的 <br>
- 如果是脱机备份，请确保在开始脱机备份操作之前在源计算机和副本计算机上都安装 Azure PowerShell 版本3.7。0
- [备份代理在 Azure 虚拟机上运行时的注意事项](https://aka.ms/AB-AA4dwtr)

### <a name="limitation"></a>限制

- Microsoft 不建议使用系统状态恢复来恢复到不同的硬件
- 系统状态备份当前支持 "本地" Windows 服务器，此功能不适用于 Azure Vm。

## <a name="prerequisites"></a>先决条件

在对 Azure 备份进行系统状态备份之前，请执行以下先决条件检查。  

### <a name="verify-windows-server-backup-is-installed"></a>验证是否已安装 Windows Server 备份

确保在服务器上安装并启用 Windows Server 备份。 若要检查安装状态，请运行以下 PowerShell 命令：

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

如果输出显示 "**可用**"**安装状态**，则表示 Windows server backup 功能可用于安装但未安装在服务器上。 但是，如果未安装 Windows Server 备份，请使用以下方法之一来安装它。

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>方法1：使用 PowerShell 安装 Windows Server 备份

若要使用 PowerShell 安装 Windows Server 备份，请运行以下命令：

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>方法2：使用服务器管理器安装 Windows Server 备份

若要使用服务器管理器安装 Windows Server 备份，请执行以下步骤：

1. 在**服务器管理**器中，单击 "**添加角色和功能**"。 此时将显示 "**添加角色和功能向导**"。

    ![仪表板](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. 选择**安装类型**，然后单击 "**下一步**"。

    ![安装类型](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. 从服务器池中选择一个服务器，然后单击 "**下一步**"。 在服务器角色中，保留默认选择，然后单击 "**下一步**"。
4. 选择 "**功能**" 选项卡中**Windows Server 备份**，并单击 "**下一步**"。

    ![features](./media/backup-azure-system-state-troubleshoot/features.png)

5. 在 "**确认**" 选项卡中，单击 "**安装**" 以启动安装过程。
6. 在 "**结果**" 选项卡中，它将显示 "Windows Server 备份" 功能已成功安装在 Windows Server 上。

    ![结果](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>系统卷信息权限

确保本地系统对安装了 windows 的卷中的 "**系统卷信息**" 文件夹具有 "完全控制"。 通常，这是**C:\System 的卷信息**。 如果未正确设置上述权限，Windows Server backup 可能会失败

### <a name="dependent-services"></a>从属服务

确保以下服务正在运行状态：

**服务名称** | **启动类型**
--- | ---
远程过程调用（RPC） | 自动
COM + 事件系统（EventSystem） | 自动
系统事件通知服务（SENS） | 自动
卷影复制（VSS） | 手动
Microsoft 软件卷影复制提供程序（SWPRV） | 手动

### <a name="validate-windows-server-backup-status"></a>验证 Windows Server 备份状态

若要验证 Windows Server 备份状态，请执行以下步骤：

- 确保 WSB PowerShell 正在运行

  - 从权限提升的 PowerShell 运行 `Get-WBJob`，确保它不返回以下错误：

    > [!WARNING]
    > WBJob：术语 "WBJob" 未被识别为 cmdlet、函数、脚本文件或可运行程序的名称。 请检查名称的拼写，如果包含路径，请验证该路径是否正确，并重试。

    - 如果失败并出现此错误，请在服务器计算机上重新安装 Windows Server 备份功能，如步骤1先决条件中所述。

  - 通过从提升的命令提示符运行以下命令，确保 WSB 备份正常工作：

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >将 X 替换为要在其中存储系统状态备份映像的卷的驱动器号。

    - 通过从提升的 PowerShell 运行 `Get-WBJob` 命令来定期检查作业状态
    - 备份作业完成后，通过运行 `Get-WBJob -Previous 1` 命令来检查作业的最终状态

如果作业失败，它表示将导致 MARS 代理系统状态备份失败的 WSB 问题。

## <a name="common-errors"></a>常见错误

### <a name="vss-writer-timeout-error"></a>VSS 编写器超时错误

| 症状 | 原因 | 解决方法
| -- | -- | --
| -MARS 代理失败，并出现以下错误消息： "WSB 作业失败，出现 VSS 错误。 检查 VSS 事件日志以解决失败 "<br/><br/> -VSS 应用程序事件日志中出现以下错误日志： "VSS 编写器拒绝了包含错误0x800423f2 的事件，该编写器在冻结和解冻事件之间已过期。"| 由于计算机上的 CPU 和内存资源不足，VSS 编写器无法完成 <br/><br/> 其他备份软件已在使用 VSS 编写器，因为无法为此备份完成快照操作 | 等待 CPU/内存释放的系统或中止进程占用太多内存/CPU，并重试该操作 <br/><br/>  等待正在进行的备份完成，稍后在计算机上没有运行任何备份时尝试该操作

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>磁盘空间不足，无法扩展卷影副本

| 症状 | 解决方法
| -- | --
| -MARS 代理失败，并出现以下错误消息：备份失败，因为卷上的磁盘空间不足，导致卷影副本卷包含系统文件 <br/><br/> -Volsnap 系统事件日志中出现以下错误/警告日志： "卷 C：上的磁盘空间不足，无法为 C：卷的卷影副本存储增加卷的卷影副本存储 | -释放事件日志中突出显示的卷中的空间，以便在备份过程中有足够的空间来使卷影副本增长 <br/><br/> -在配置卷影副本空间时，可以限制用于卷影复制的空间量。 有关详细信息，请参阅此[文](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax)

### <a name="efi-partition-locked"></a>已锁定 EFI 分区

| 症状 | 解决方法
| -- | --
| MARS 代理失败，并出现以下错误消息： "系统状态备份失败，因为 EFI 系统分区被锁定。 这可能是由第三方安全或备份软件的系统分区访问造成的 | -如果问题是由第三方安全软件引起的，则需要联系反病毒供应商，使其可以允许 MARS 代理 <br/><br/> -如果第三方备份软件正在运行，请等待它完成，然后重试备份

## <a name="next-steps"></a>后续步骤

- 有关资源管理器部署中的 Windows 系统状态的详细信息，请参阅[备份 Windows Server 系统状态](backup-azure-system-state.md)
