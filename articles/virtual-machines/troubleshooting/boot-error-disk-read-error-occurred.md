---
title: 排查启动错误 - 发生磁盘读取错误
description: 本文提供了解决在 Azure VM 中无法读取磁盘的问题的步骤。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 817c9c5c-6a81-4b42-a6ad-0a0a11858b84
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/01/2020
ms.author: v-miegge
ms.openlocfilehash: f59903ed111be1fe414f4b3ded250d754c91d323
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87069147"
---
# <a name="troubleshoot-boot-error---disk-read-error-occurred"></a>排查启动错误 - 发生磁盘读取错误

本文提供了解决在 Azure 虚拟机 (VM) 中无法读取磁盘的问题的步骤。

## <a name="symptoms"></a>症状

使用[启动诊断](./boot-diagnostics.md)查看 VM 的屏幕截图时，你将看到屏幕截图显示以下消息提示：“发生磁盘读取错误。 然后按 Ctrl+Alt+Del 重启”。

   ![错误消息：发生磁盘读取错误。 请按 Ctrl+Alt+Del 重启。](./media/disk-read-error-occurred/1.png)

## <a name="cause"></a>原因

此错误消息指示磁盘结构已损坏且无法读取。 如果使用的是第 1 代 VM，则也可能是包含启动配置数据的磁盘分区未设置为“活动”。

## <a name="solution"></a>解决方案

### <a name="process-overview"></a>过程概述

1. 创建和访问修复 VM。
1. 选择解决方案：
   - [将分区状态设置为活动](#set-partition-status-to-active)
   - [修复磁盘分区](#fix-the-disk-partition)
1. 启用串行控制台和内存转储收集。
1. 重新生成 VM。

> [!NOTE]
> 遇到此启动错误时，来宾操作系统 (OS) 无法正常运行。 需要在脱机模式下进行故障排除来解决此问题。

### <a name="create-and-access-a-repair-vm"></a>创建和访问修复 VM

1. 使用 [VM 修复命令](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)的步骤 1-3 来准备一个修复 VM。
1. 使用远程桌面连接来连接到修复 VM。

### <a name="set-partition-status-to-active"></a>将分区状态设置为活动

第 1 代 VM 应先验证是否已将 BCD 存储所在的 OS 分区标记为“活动”。 如果你有第 2 代虚拟机，请跳至[修复磁盘分区](#fix-the-disk-partition)，因为“状态”标志在下一代中弃用。

1. 打开提升的命令提示符 (cmd.exe)。
1. 输入“diskpart”以启动“DISKPART”工具。
1. 输入“list disk”以列出系统上的磁盘，并确定附加的 OS 虚拟硬盘 (VHD)。
1. 在找到附加的 OS VHD 后，输入“sel disk #”以选择相应磁盘。 请参阅下图中的示例，其中磁盘 1 是附加的 OS VHD。

   ![带有 **list disk** 命令的输出内容的 diskpart 窗口，其中磁盘 0 和磁盘 1 显示在表中。 该窗口还会显示 **sel disk 1** 命令的输出内容，其中磁盘 1 是所选磁盘](./media/disk-read-error-occurred/2.png)

1. 选择该磁盘后，输入“list partition”以列出所选磁盘的分区。
1. 确定启动分区后，输入“sel partition #”以选择相应分区。 启动分区的大小通常约为 350 MB。  请参阅下图中的示例，其中分区 1 是启动分区。

   ![带有“list partition”命令的输出内容的 diskpart 窗口，其中分区 1 和分区 2 显示在表中。 该窗口还会显示“sel partition 1”命令的输出内容，其中分区 1 是所选磁盘。](./media/disk-read-error-occurred/3.png)

1. 输入“detail partition”以检查分区的状态。 请参阅以下屏幕截图中的示例，其中分区设置为“活动:否”或“活动:是”。

   “活动:否

   ![带有“detail partition”命令的输出内容的 diskpart 窗口，其中分区 1 设置为“活动:否”。](./media/disk-read-error-occurred/4.png)

   “活动:是

   ![带有“detail partition”命令的输出内容的 diskpart 窗口，其中分区 1 设置为“活动:是”。](./media/disk-read-error-occurred/5.png)

1. 如果分区未设置为“活动”，请输入“active”以更改“活动”标志。
1. 输入“detail partition”以检查状态更改是否已正确完成，并确认输出内容中是否包含“活动:是”。 
1. 输入“exit”以关闭 DISKPART 工具并保存配置更改。

### <a name="fix-the-disk-partition"></a>修复磁盘分区

1. 打开提升的命令提示符 (cmd.exe)。
1. 使用以下命令对磁盘运行“CHKDSK”，并执行错误修复：

   `chkdsk <DRIVE LETTER>: /f`

   添加“/f”命令选项将修复磁盘上的任何错误。 请确保将“<驱动器号>”替换为附加的 OS VHD 的盘符。

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>启用串行控制台和内存转储收集

**建议**：在重新生成 VM 之前，通过运行以下脚本来启用串行控制台和内存转储收集：

1. 以管理员身份打开权限提升的命令提示符会话。
1. 运行以下命令：

   **启用串行控制台**：
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. 验 OS 磁盘上的可用空间是否大于 VM 上的内存大小 (RAM)。

   如果 OS 磁盘上没有足够的空间，请更改将要创建内存转储文件的位置，并将该位置引用到具有足够可用空间的 VM 上附加的任何数据磁盘。 若要更改位置，请在以下命令中将 %SystemRoot% 替换为数据磁盘的驱动器号（例如，F:）。

   用于启用 OS 转储的建议配置：

   **从损坏的 OS 磁盘加载注册表配置单元：**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **在 ControlSet001 上启用：**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **在 ControlSet002 上启用：**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **卸载损坏的 OS 磁盘：**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>重新生成 VM

使用 [VM 修复命令的步骤 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 重新生成 VM。
