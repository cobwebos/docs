---
title: Azure 虚拟机关机停滞在重启、关闭或停止服务 |Microsoft Docs
description: 本文帮助你排查 Azure Windows 虚拟机中的服务错误。
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/19/2019
ms.author: tibasham
ms.openlocfilehash: e5ab1262def78da4971ea6e5535f3ac915a38ec8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526752"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Azure Windows VM 关机停滞在 "正在重新启动"、"正在关闭" 或 "正在停止服务"

本文提供了一些步骤，用于解决在 Microsoft Azure 重启 Windows 虚拟机时出现的 "重新启动"、"正在关闭" 或 "正在停止服务" 的问题， (VM) 。

## <a name="symptoms"></a>症状

使用 " [启动诊断](./boot-diagnostics.md) " 查看 VM 的屏幕截图时，可能会看到屏幕截图显示消息 "正在重新启动"、"正在关闭" 或 "正在停止服务"。

![“正在重启”、“正在关闭”或“正在停止服务”屏幕](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>原因

Windows 使用关闭进程来执行系统维护操作，并处理更新、角色和功能等方面的更改。 建议不要中断此关键进程，而是让它完成。 根据更新/更改数量和 VM 大小，此进程可能会花费较长的时间。 如果停止该进程，OS 可能会损坏。 仅当该进程耗时过长时才将其中断。

## <a name="solution"></a>解决方案

### <a name="collect-a-process-memory-dump"></a>收集进程内存转储

1. 将 [Procdump 工具](http://download.sysinternals.com/files/Procdump.zip) 下载到新的或现有的数据磁盘，该磁盘已附加到同一区域中的工作 VM。

2. 从工作 VM 分离包含所需文件的磁盘，并将该磁盘附加到损坏的 VM。 我们将在此磁盘上调用 **实用工具磁盘**。

使用 [串行控制台](./serial-console-windows.md) 完成以下步骤：

1. 打开管理 PowerShell 并检查在停止时停止响应的服务。

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. 在管理 CMD 上，获取无响应服务的 PID。

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. 从无响应进程获取内存转储示例 <STOPPING SERVICE> 。

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. 现在终止无响应的进程来解锁关闭进程。

   ``
   taskkill /PID <PID> /t /f
   ``

操作系统重新启动后，如果它正常启动，只需确保 OS 一致性正常。 如果报告损坏，请运行以下命令，直到磁盘出现损坏：

``
dism /online /cleanup-image /restorehealth
``

如果无法收集进程内存转储，或此问题是递归的，并且你需要根本原因分析，请继续收集下面的 OS 内存转储，然后继续打开支持请求。

### <a name="collect-an-os-memory-dump"></a>收集 OS 内存转储

如果等待更改处理后问题仍未解决，则需要收集内存转储文件并与支持部门联系。 若要收集转储文件，请执行以下步骤：

**将 OS 磁盘附加到恢复 VM**

1. 拍摄受影响的 VM 的 OS 磁盘的快照作为备份。 有关详细信息，请参阅[拍摄磁盘快照](../windows/snapshot-copy-managed-disk.md)。

2. [将 OS 磁盘附加到恢复 VM](./troubleshoot-recovery-disks-portal-windows.md)。

3. 通过远程桌面连接到恢复 VM。

4. 如果 OS 磁盘已加密，则必须先关闭加密，然后才能进入下一步。 有关详细信息，请参阅[在无法启动的 VM 中解密加密的 OS 磁盘](./troubleshoot-bitlocker-boot-error.md#solution)。

**找到转储文件并提交支持票证**

1. 在恢复 VM 上，转到附加的 OS 磁盘中的 Windows 文件夹。 如果分配给附加 OS 磁盘的驱动器号为 F，则需转到 F:\Windows。

2. 找到 memory.dmp 文件，然后使用该转储文件[提交支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

如果找不到转储文件，请转到下一步以启用转储日志和串行控制台。

**启用转储日志和串行控制台**

若要启用转储日志和串行控制台，请运行以下脚本。

1. 打开提升的命令提示符会话（以管理员身份运行）。

2. 运行以下脚本：

   在此脚本中，假定分配给附加 OS 磁盘的驱动器号为 F。将其替换为 VM 中的相应值。

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
   
   REM Enable Serial Console
   bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
   bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
   bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
   bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
   bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   
   REM Suggested configuration to enable OS Dump
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump / t REG_DWORD /d 1 /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

3. 确认磁盘上有足够的空间来分配与 RAM 一样多的内存，具体取决于为此 VM 选择的大小。

4. 如果没有足够的空间或者 VM 较大（G、GS 或 E 系列），则可随后更改创建此文件时所在的位置，将该位置指向任何其他附加到 VM 的数据磁盘。 若要更改位置，必须更改以下注册表项：

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [分离 OS 磁盘，然后将 OS 磁盘重新附加到受影响的 VM](./troubleshoot-recovery-disks-portal-windows.md)。

6. 启动 VM 并访问串行控制台。

7. 选择“发送不可屏蔽中断(NMI)”以触发内存转储。

   ![发送不可屏蔽的中断](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. 再次将 OS 磁盘附加到恢复 VM，收集转储文件。

## <a name="contact-microsoft-support"></a>联系 Microsoft 支持部门

收集转储文件后，请联系 Microsoft 支持部门以确定根本原因。
