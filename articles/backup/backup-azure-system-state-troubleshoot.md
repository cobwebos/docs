---
title: 排查使用 Azure 备份系统状态备份问题
description: 对系统状态备份中的问题进行故障排除。
services: backup
author: srinathvasireddy
manager: sivan
keywords: 如何备份;备份系统状态
ms.service: backup
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: srinathv
ms.openlocfilehash: 87b5fff58ecf9e89bc94f31a0bc3a591c146c88f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704996"
---
# <a name="troubleshoot-system-state-backup"></a>解决系统状态备份问题

本指南介绍了使用系统状态备份时可能遇到的问题的解决方案。

## <a name="basic-troubleshooting"></a>基本故障排除
我们建议你执行以下验证，在开始操作之前进行系统状态备份故障排除：

- [请确保是最新的 Microsoft Azure 恢复服务 (MARS) 代理](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [确保在 MARS 代理和 Azure 之间存在网络连接](https://aka.ms/AB-A4dp50)
- 确保 Microsoft Azure 恢复服务正在运行（在服务控制台中）。 根据需要重启，然后重试此操作
- [确保在暂存文件夹位置有 5-10% 的可用卷空间](https://aka.ms/AB-AA4dwtt)
- [检查其他进程或防病毒软件是否正在干扰 Azure 备份](https://aka.ms/AB-AA4dwtk)
- [计划的备份失败，但手动备份成功](https://aka.ms/ScheduledBackupFailManualWorks)
- 确保 OS 有最新更新
- [确保不受支持的驱动器，并具有不受支持的属性的文件从备份中排除](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- 请确保受保护系统上的系统时钟配置为正确时区  <br>
- [确保服务器至少具有 .Net Framework 版本 4.5.2 和更高版本](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- 如果尝试**将服务器重新注册**到保管库，则请执行以下操作： <br>
  - 确保在服务器上卸载代理并将其从门户中删除 <br>
  - 使用的密码正是一开始用于注册服务器的 <br>
- 如果脱机备份出现，请确保在开始脱机备份操作之前，源和副本计算机上安装 Azure PowerShell 版本 3.7.0
- [Azure 虚拟机上运行备份代理时的考虑事项](https://aka.ms/AB-AA4dwtr)

### <a name="limitation"></a>限制
- Microsoft 不建议使用系统状态恢复来恢复到不同的硬件
- 系统状态备份目前支持"本地"Windows 服务器，此功能适用于 Azure Vm。

## <a name="pre-requisite"></a>先决条件

我们对使用 Azure 备份系统状态备份进行故障排除之前，请确保您执行以下系统必备组件检查。  

### <a name="verify-windows-server-backup-is-installed"></a>验证安装了 Windows Server Backup

确保安装并在服务器中启用 Windows Server Backup。 若要检查安装状态，请运行以下 PowerShell 命令：

 ```
 PS C:\> Get-WindowsFeature Windows-Server-Backup
 ```
如果该输出会显示**安装状态**作为**可用**，则意味着 Windows Server 备份功能是可用于安装但未安装在服务器上。 但是如果未安装 Windows Server Backup，然后执行下列任一以下方法来安装它。

**方法 1：安装 Windows Server Backup 使用 PowerShell**

若要安装 Windows Server Backup 使用 PowerShell，运行以下命令：

  ```
  PS C:\> Install-WindowsFeature -Name Windows-Server-Backup
  ```

**方法 2：安装 Windows Server Backup 使用服务器管理器**

若要安装 Windows Server Backup 使用服务器管理器，请执行如下：

1. 在中**Sever Manger**然后单击**添加角色和功能**。 **添加角色和功能向导**出现。

    ![仪表板](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. 选择**安装类型**然后单击**下一步**。

    ![安装类型](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. 从服务器池中选择服务器，然后单击**下一步**。 在服务器角色中，保留默认选择，然后单击**下一步**。
4. 选择**Windows Server Backup**中**功能**选项卡，单击**下一步**。

    ![features](./media/backup-azure-system-state-troubleshoot/features.png)

5. 在中**确认**选项卡上，单击**安装**启动安装过程。
6. 在中**结果**选项卡上，它将显示在 Windows Server 成功安装功能的 Windows Server Backup。

    ![结果](./media/backup-azure-system-state-troubleshoot/results.jpg)


### <a name="system-volume-information-permission"></a>系统卷信息的权限

请确保本地系统具有完全控制**系统卷信息**文件夹位于安装了 windows 的卷中。 这是通常**C:\System Volume Information**。 Windows Server 备份可能失败，如果未正确设置上述权限

### <a name="dependent-services"></a>从属服务

请确保以下服务是处于运行状态：

**服务名称** | **启动类型**
--- | ---
远程过程调用 （rpc) | 自动
COM + 事件 System(EventSystem) | 自动
系统事件通知 Service(SENS) | 自动
卷影 Copy(VSS) | 手动
Microsoft 软件卷影副本 Provider(SWPRV) | 手动

### <a name="validate-windows-server-backup-status"></a>验证 Windows Server 备份状态

若要验证 Windows Server Backup 状态，请执行如下：

  * 确保 WSB PowerShell 运行

    -   运行`Get-WBJob`从提升的 PowerShell 并确保它不返回以下错误：

    > [!WARNING]
    > Get-WBJob:术语 Get WBJob 不是识别为 cmdlet、 函数、 脚本文件或可操作程序的名称。 检查名称的拼写，如果包含路径，请验证该路径是否正确，并重试。

    -   如果将失败并出现此错误，重新安装步骤 1 先决条件中所述的服务器计算机上的 Windows Server Backup 功能。

  * 确保 WSB 备份正常运行，通过运行以下命令从提升的命令提示符：

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >使用你想要存储系统状态的卷的驱动器号替换 X 备份映像。

    - 定期检查通过运行作业的状态`Get-WBJob`从提升的 PowerShell 命令        
    - 备份作业完成后通过运行检查作业的最终状态`Get-WBJob -Previous 1`命令

如果作业失败时，它表明 WSB 问题这将导致 MARS 代理系统状态备份失败。

## <a name="common-errors"></a>常见错误

### <a name="vss-writer-timeout-error"></a>VSS 编写器超时错误

| 症状 | 原因 | 解决方法
| -- | -- | --
| MARS 代理失败，出现错误消息："WSB 作业失败，出现 VSS 错误。 检查 VSS 事件日志以解决失败"<br/><br/> -按照错误日志中存在 VSS 应用程序事件日志："VSS 编写器已拒绝具有错误 0x800423f2 的事件，该编写器的超时时间已到之间的冻结和解冻事件"。| VSS 编写器不能在到期的时间内完成的计算机上的 CPU 和内存资源不足 <br/><br/> 另一个备份软件已在使用 VSS 编写器，因此快照的操作无法完成此备份 | 等待 CPU/内存来释放了系统上或中止花费过多的内存/CPU 的进程，然后重试该操作 <br/><br/>  等待正在进行的备份完成，然后重试该操作在以后，当计算机上不运行的任何备份


### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>没有足够的磁盘空间来增加卷影副本

| 症状 | 解决方法
| -- | --
| MARS 代理失败，出现错误消息：备份失败，因为卷影副本卷无法增长由于没有足够的磁盘空间上包含系统文件的卷 <br/><br/> -在错误/警告日志文件出现在系统事件日志中 volsnap 中："出现没有足够的磁盘空间卷 c： 增长的 c： 由于此失败的卷影副本卷影副本存储上的卷 c： 会面临风险的要删除的所有卷影副本" | -释放事件日志中突出显示的卷中的空间以便有足够的空间用于卷影副本以增长，而备份正在进行中 <br/><br/> -在配置卷影副本空间，我们可以限制用于卷影副本的空间量时，有关详细信息请参阅此[文章](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax)


### <a name="efi-partition-locked"></a>锁定的 EFI 分区

| 症状 | 解决方法
| -- | --
| MARS 代理失败，出现错误消息："系统状态返回最多失败，因为 EFI 系统分区已锁定。 这可以是由于通过第三方安全访问的系统分区或备份软件" | -如果此问题是由于第三方安全软件，则需要联系的防病毒软件供应商，以便它们可让 MARS 代理 <br/><br/> -如果运行第三方备份软件，然后等待它完成，然后重试后，启动


## <a name="next-steps"></a>后续步骤

- 有关资源管理器部署中的 Windows 系统状态的详细信息，请参阅[备份 Windows Server 系统状态](backup-azure-system-state.md)
