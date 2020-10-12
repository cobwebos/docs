---
title: 对“Windows 停止错误”进行排除故障 - 目录服务初始化失败
description: 解决 Azure 中的 Active Directory 域控制器虚拟机 (VM) 陷入循环并声明其需要重启的问题。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3396f8fe-7573-4a15-a95d-a1e104c6b76d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/05/2020
ms.author: v-miegge
ms.openlocfilehash: 909481964f8aa3272715e235fa011562225a9422
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87028356"
---
# <a name="troubleshoot-windows-stop-error--directory-service-initialization-failure"></a>对“Windows 停止错误”进行排除故障 - 目录服务初始化失败

本文提供了解决 Azure 中的 Active Directory 域控制器虚拟机 (VM) 陷入循环并声明其需要重启的问题。

## <a name="symptom"></a>症状

当使用[启动诊断](./boot-diagnostics.md)查看 VM 的屏幕截图时，屏幕截图显示 VM 因错误而需要重启，并在 Windows Server 2008 R2 中显示停止代码“0xC00002E1”，在 Windows Server 2012 或更高版本中显示停止代码“0xC00002E2” 。

![Windows Server 2012 启动屏幕显示“你的电脑遇到问题，需要重启。 我们将收集一些错误信息，然后为你重启电脑。”](./media/troubleshoot-directory-service-initialization-failure/1.png)

## <a name="cause"></a>原因

错误代码“0xC00002E2”表示状态“STATUS_DS_INIT_FAILURE”，错误代码“0xC00002E1”表示状态“STATUS_DS_CANT_START”   。 当目录服务出现问题时，这两个错误都会发生。

当 OS 启动时，本地安全身份验证服务器 (LSASS.exe) 会强制它自动重新启动，该服务器用于对用户登录进行身份验证。 如果 VM 上的操作系统是对其本地 Active Directory 数据库没有读/写访问权限的域控制器，则无法进行身份验证。 因为无法访问 Active Directory (AD)，LSASS.exe 无法进行身份验证，因此会强制重启 OS。

此错误可能由以下任何情况引起：

- 无法访问托管本地 AD 数据库的磁盘 (NTDS.DIT)。
- 托管本地 AD 数据库的磁盘 (NTDS.DIT) 已无可用空间。
- 本地 AD 数据库 (NTDS.DIT) 文件缺失。
- VM 有多个磁盘，存储区域网络 (SAN) 策略配置不正确。 SAN 策略未设置为“ONLINEALL”，并且非 OS 磁盘在磁盘管理器上以脱机模式连接。
- 本地 AD 数据库 (NTDS.DIT) 文件已损坏。

## <a name="solution"></a>解决方案

### <a name="process-overview"></a>过程概述：

1. 创建和访问修复 VM。
1. 磁盘上的可用空间。
1. 检查确认是否已附加包含 AD 数据库的驱动器。
1. 目录服务还原模式。
1. **建议**：在重建 VM 之前，启用串行控制台和内存转储收集。
1. 重新生成 VM。
1. 重新配置 SAN 策略。

> [!NOTE]
> 遇到此错误时，来宾操作系统无法正常运行。 需要在脱机模式下进行故障排除以解决此问题。

### <a name="create-and-access-a-repair-vm"></a>创建和访问修复 VM

1. 使用 [VM 修复命令的步骤 1-3](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 来准备一个修复 VM。
1. 使用远程桌面连接来连接到修复 VM。

### <a name="free-up-space-on-disk"></a>释放磁盘上的空间

由于磁盘现在连接到修复 VM，请验证托管 Active Directory 内部数据库的磁盘是否有足够的空间来正确执行操作。

1. 右键单击该驱动器并选择“属性”，检查磁盘是否已满。
1. 如果磁盘的可用空间少于 300 Mb，[请使用 PowerShell 将其最大扩展到 1 Tb](../windows/expand-os-disk.md)。
1. 如果磁盘已用空间达到 1 Tb，请清理磁盘。

   1. 使用 PowerShell 从损坏的 VM 中[分离数据磁盘](../windows/detach-disk.md#detach-a-data-disk-using-powershell)。
   1. 从损坏的 VM 上分离数据磁盘后，[附加数据磁盘](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)到正常运行的 VM。
   1. 使用[磁盘清理工具](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup)释放更多空间。

1. **可选** - 如果需要更多空间，请打开 CMD 实例并输入 `defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g` 命令以在驱动器上执行碎片整理：

  * 在命令中，用 OS 磁盘号替换 `<LETTER ASSIGNED TO THE OS DISK>`。 例如，如果磁盘号为 `F:`，则命令为 `defrag F: /u /x /g`。

  * 根据碎片级别，碎片整理可能需要几个小时。

如果磁盘上有足够的空间，请继续执行下一个任务。

### <a name="check-that-the-drive-containing-the-active-directory-database-is-attached"></a>检查包含 Active Directory 数据库的驱动器是否已附加

1. 打开权限提升的 CMD 实例，并运行以下命令：

   1. 加载注册表文件：

      `REG LOAD HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM`

      名称 `f:` 指示磁盘是驱动器 `F:`。 使用包含 OS 磁盘的驱动器的驱动器号。

   1. 确定“NTDS.DIT”的驱动器号和文件夹：

      ```
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Working Directory"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Database file"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database backup path"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database log files path"
      ```

   1. 卸载注册表文件：

      `REG UNLOAD HKLM\BROKENSYSTEM`

1. 使用 Azure 门户，验证是否已将设置 NTDS.DIT 的驱动器添加到 VM。
1. 使用来宾 OS 中的磁盘管理控制台，验证包含 NTDS.DIT 的磁盘是否为联机状态。
   1. 磁盘管理工具可以在“管理工具”>“计算机管理”>“存储”中找到，也可以使用 CMD 实例中的 `diskmgmt.msc` 命令访问。
1. 如果磁盘未连接到 VM，请重新连接数据磁盘以解决此问题。

   如果磁盘已正常附加，则继续执行下一个任务。

### <a name="enable-directory-services-restore-mode"></a>目录服务还原模式

将 VM 设置为在“目录服务还原模式 (DSRM)”模式下启动，以在启动期间跳过检查 NTDS.DIT 文件是否存在。

1. 继续操作之前，请确认已完成之前将磁盘附加到修复虚拟机的任务，并已确定 NTDS.DIT 文件位于哪个磁盘中。
1. 使用提升的 CMD 实例，列出该存储上的启动分区信息，以便从活动分区中查找标识符：

   `bcdedit /store <Drive Letter>:\boot\bcd /enum`

   用前面步骤中确定的号替换 `< Drive Letter >`。

   ![屏幕截图显示了在输入“bcdedit /store <Drive Letter>:\boot\bcd /enum”命令后提升的 CMD 实例，该命令显示带有标识符的 Windows 启动管理器。](./media/troubleshoot-directory-service-initialization-failure/2.png)

1. 在启动分区上启用 `safeboot DsRepair` 标志：

   `bcdedit /store <Drive Letter>:\boot\bcd /set {<Identifier>} safeboot dsrepair`

   将 `< Drive Letter >` 和 `< Identifier >` 替换为在前面步骤中确定的值。

1. 再次查询启动选项以确保正确设置了更改。

   ![屏幕截图显示启用 safeboot DsRepair 标志后提升的 CMD 实例。](./media/troubleshoot-directory-service-initialization-failure/3.png)

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>建议：在重新生成 VM 之前，启用串行控制台和内存转储收集

为启用内存转储收集和串行控制台，请打开提升的命令提示符会话（以管理员身份运行）并运行以下脚本，然后运行以下命令。

1. 启用串行控制台：

  ```
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
  ```

1. 验证 OS 磁盘上的可用空间是否至少等于 VM 上的内存大小 (RAM)。

  1. 如果 OS 磁盘上没有足够的空间，请更改将要创建内存转储文件的位置，并将其引用到具有足够可用空间的 VM 上附加的任何数据磁盘。

     若要更改位置，请在以下命令中将 `%SystemRoot%` 替换为数据磁盘的驱动器号（例如，`F:`）。

  #### <a name="the-following-configuration-is-suggested-to-enable-os-dump"></a>建议使用以下配置启用 OS 转储：

  **加载损坏的 OS 磁盘**：

  `REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

  **在 ControlSet001 上启用**：

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **在 ControlSet002 上启用**：

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **卸载损坏的 OS 磁盘**：

  `REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-vm"></a>重新生成 VM

1. 使用 [VM 修复命令的步骤 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 重新装配 VM。

### <a name="reconfigure-the-storage-area-network-policy"></a>重新配置存储区域网络策略

1. 在 DSRM 模式下启动时，唯一可登录的用户是恢复管理员，在 VM 升级为域控制器时使用过该用户。 如果使用其他用户，将显示身份验证错误。

   1. 如果没有其他可用的 DC，则必须使用 `.\administrator` 或 `machinename\administrator` 和 DSRM 密码在本地登录。

1. 设置 SAN 策略，使所有磁盘都处于联机状态。

   1. 打开权限提升的 CMD 实例，并输入 `DISKPART`。
   1. 查询磁盘列表。

      `DISKPART> list disk`

   1. 输入以下命令以选择需要联机的磁盘并更改 SAN 策略：

      ```
      DISKPART> select disk 1
      Disk 1 is now the selected disk.

      DISKPART> attributes disk clear readonly
      Disk attributes cleared successfully.

      DISKPART> attributes disk
      Current Read-only State : No
      Read-only : No
      Boot Disk : No
      Pagefile Disk : No
      Hibernation File Disk : No
      Crashdump Disk : No
      Clustered Disk : No

      DISKPART> online disk
      DiskPart successfully onlined the selected disk.

      DISKPART> san
      SAN Policy : Online All
      ```

1. 解决问题后，请确保删除标志 `DsRepair safeboot`：

   `bcdedit /deletevalue {default} safeboot dsrepair`

1. 重新启动 VM。

   > [!NOTE]
   > 如果 VM 刚刚从本地迁移，并且想要将更多域控制器从本地迁移到 Azure，则应考虑执行以下文章中的步骤，以防止在以后的迁移中发生此问题：
   >
   > [如何使用 Azure PowerShell 将现有本地 Hyper-V 域控制器上传到 Azure](https://support.microsoft.com/help/2904015)
