---
title: Azure 虚拟机关机停滞在重启、关闭或停止服务 |Microsoft Docs
description: 本文将帮助你解决 Azure Windows 虚拟机中的服务错误。
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
ms.openlocfilehash: 5d6396efc9ab25baa0d32e7c33c7715863516249
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371358"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Azure Windows VM 关机停滞在 "正在重新启动"、"正在关闭" 或 "正在停止服务"

本文提供了解决在 Microsoft Azure 中重新启动 Windows 虚拟机（VM）时可能遇到的 "重新启动"、"关闭" 或 "停止服务" 消息问题的步骤。

## <a name="symptoms"></a>症状

使用 "[启动诊断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)" 查看 VM 的屏幕截图时，可能会看到屏幕截图显示消息 "正在重新启动"、"正在关闭" 或 "正在停止服务"。

![重启、关闭和停止服务屏幕](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>原因

Windows 使用关闭过程来执行系统维护操作，并处理更新、角色和功能等更改。 建议不要中断此关键过程，直到完成。 此过程可能需要较长时间，具体取决于更新/更改数量和 VM 大小。 如果进程已停止，则操作系统可能会损坏。 如果进程耗时过长，则仅中断进程。

## <a name="solution"></a>解决方案

### <a name="collect-a-process-memory-dump"></a>收集进程内存转储

1. 将[Procdump 工具](http://download.sysinternals.com/files/Procdump.zip)下载到新的或现有的数据磁盘，该磁盘已附加到同一区域中的工作 VM。

2. 从工作 VM 分离包含所需文件的磁盘，并将该磁盘附加到损坏的 VM。 我们将在此磁盘上调用**实用工具磁盘**。

使用[串行控制台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows)完成以下步骤：

1. 打开管理 Powershell 并检查停止时挂起的服务。

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. 在管理 CMD 上，获取挂起服务的 PID。

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. 从挂起的进程获取内存转储示例 <STOPPING SERVICE>。

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. 现在终止挂起的进程以解锁关闭进程。

   ``
   taskkill /PID <PID> /t /f
   ``

操作系统重新启动后，如果它正常启动，只需确保 OS 一致性正常。 如果报告损坏，请运行以下命令，直到磁盘出现损坏：

``
dism /online /cleanup-image /restorehealth
``

如果无法收集进程内存转储，或此问题是递归的，并且你需要根本原因分析，请继续收集下面的 OS 内存转储，然后继续打开支持请求。

### <a name="collect-an-os-memory-dump"></a>收集 OS 内存转储

如果在等待更改处理后无法解决该问题，则需要收集内存转储文件并与支持人员联系。 若要收集转储文件，请执行以下步骤：

**将 OS 磁盘附加到恢复 VM**

1. 拍摄受影响的 VM 的 OS 磁盘的快照作为备份。 有关详细信息，请参阅[拍摄磁盘快照](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk)。

2. [将 OS 磁盘附加到恢复 VM](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal)。

3. 通过远程桌面连接到恢复 VM。

4. 如果 OS 磁盘已加密，则必须在转到下一步之前关闭加密。 有关详细信息，请参阅[在无法启动的 VM 中解密已加密的 OS 磁盘](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution)。

**查找转储文件并提交支持票证**

1. 在恢复 VM 上，转到附加的 OS 磁盘中的 Windows 文件夹。 如果分配给附加 OS 磁盘的驱动器号为 F，则需转到 F:\Windows。

2. 找到内存 dmp 文件，然后使用转储文件[提交支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

如果找不到转储文件，请转到下一步以启用转储日志和串行控制台。

**启用转储日志和串行控制台**

若要启用转储日志和串行控制台，请运行以下脚本。

1. 打开提升的命令提示符会话（以管理员身份运行）。

2. 运行以下脚本：

   在此脚本中，我们假设分配给附加 OS 磁盘的驱动器号为 F。请将其替换为 VM 中的相应值。

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

3. 验证磁盘上是否有足够的空间来分配尽可能多的内存作为 RAM，具体取决于为此 VM 选择的大小。

4. 如果空间不足或 VM 太大（G、GS 或 E 系列），可以更改创建此文件的位置，并将其指向附加到 VM 的任何其他数据磁盘。 若要更改位置，必须更改以下项：

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [分离 os 磁盘，然后将 os 磁盘重新连接到受影响的 VM](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal)。

6. 启动 VM 并访问串行控制台。

7. 选择 "发送不可屏蔽中断（NMI）" 以触发内存转储。

   ![发送不可屏蔽中断](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. 再次将 OS 磁盘附加到恢复 VM，收集转储文件。

## <a name="contact-microsoft-support"></a>请与 Microsoft 支持部门联系

收集转储文件后，请联系 Microsoft 支持部门以确定根本原因。
