---
title: 启动错误– "这不是可启动磁盘"
description: 本文提供的步骤用于解决无法在 Azure 虚拟机中启动磁盘的问题
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5d6db4e3-c2f5-40c7-afea-54edf745f5eb
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: v-mibufo
ms.openlocfilehash: 16f6919577955bda5b04db26deb9fe78a467e364
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86509029"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>启动错误-这不是可启动磁盘

本文提供的步骤用于解决无法在 Azure 虚拟机 (VM) 中启动磁盘的问题。

## <a name="symptoms"></a>症状

使用[启动诊断](./boot-diagnostics.md)查看 VM 的屏幕截图时，会看到屏幕截图显示一个提示，其消息为“这不是可启动磁盘。 请插入可启动软盘并按任意键重试…”。

   图 1

   ![图 1 显示了消息“这不是可启动磁盘。* *请插入可启动软盘并按任意键重试…”](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>原因

出现此错误消息表示 OS 引导进程找不到活动的系统分区。 此错误还可能表示引导配置数据 (BCD) 存储中缺少引用，这会阻止它查找 Windows 分区。

## <a name="solution"></a>解决方案

### <a name="process-overview"></a>流程概述

1. 创建并访问修复 VM。
2. 将“分区状态”设置为“活动”。
3. 修复磁盘分区。
4. **建议**：在重建 VM 之前，启用串行控制台和内存转储收集。
5. 重建原始 VM。

   > [!NOTE]
   > 遇到此启动错误时，来宾 OS 不会正常运行。 需要在脱机模式下进行故障排除才能解决此问题。

### <a name="create-and-access-a-repair-vm"></a>创建并访问修复 VM

1. 使用 [VM 修复命令](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)的步骤 1-3 来准备一个修复 VM。
2. 使用远程桌面连接来连接到修复 VM。

### <a name="set-partition-status-to-active"></a>将“分区状态”设置为“活动”

第 1 代 VM 会先验证 BCD 存储所在的 OS 分区是否已标记为“活动”  。 如果使用第 2 代 VM，请跳转到[修复磁盘分区](#fix-the-disk-partition)，因为在更高的代系中，“状态”标志已弃用  。

1. 打开提升的命令提示符 (cmd.exe)  。
2. 输入 diskpart 以启动 DISKPART 工具  。
3. 输入 list disk 以列出系统上的磁盘，确定附加的 OS VHD  。
4. 找到附加的 OS VHD 后，输入 sel disk # 来选择磁盘  。  请参阅图 2，其中的“磁盘 1”是附加的 OS VHD。

   图 2

   ![图 2 显示了“DISKPART”窗口，其中显示了 list disk 命令的输出，表中显示了“磁盘 0”和“磁盘 1”。**  该图还显示了 sel disk 1 命令的输出，“磁盘 1”是选定的磁盘](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. 选择磁盘后，输入 list partition 以列出所选磁盘的分区  。
6. 确定启动分区后，输入 sel partition # 以选择该分区  。  通常，启动分区的大小约为 350 MB。  请参阅图 3，其中的“分区 1”是启动分区。

   图 3

   ![图 3 显示了“DISKPART”窗口，其中包含 list partition 命令的输出。**** 表中显示了“分区 1”和“分区 2”。 该图还显示了 sel partition 1 命令的输出，其中“分区 1”是选定的磁盘。**](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. 输入“detail partition”，检查分区状态。 请参阅图 4，其中的分区为“活动:  否”；或参阅图 5，其中的分区为“活动:是”。

   图 4

   ![图 4 显示了“DISKPART”窗口，其中包含 detail partition 命令的输出，“分区 1”设置为“活动:*****否*](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   图 5

   ![图 5 显示了“DISKPART”窗口，其中包含 detail partition 命令的输出，“分区 1”设置为“活动:******是”。](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. 如果分区为“非活动”，请输入 active 来更改“活动”标志    。
9. 键入 detail partition，检查是否正确完成了状态更改  。

   图 6

   ![图 6 显示了“diskpart”窗口，其中包含 detail partition 命令的输出，“分区 1”设置为“活动:***是*](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. 输入 exit，关闭 DISKPART 工具并保存配置更改  。

### <a name="fix-the-disk-partition"></a>修复磁盘分区

1. 打开提升的命令提示符 (cmd.exe)。
2. 使用以下命令在磁盘上运行 CHKDSK 并修复错误  ：

   `chkdsk <DRIVE LETTER>: /f`

   添加“/f”命令选项会修复磁盘上的任何错误。 请务必将 <DRIVE LETTER> 替换为附加的 OS VHD 的盘符。

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>建议：在重建 VM 之前，启用串行控制台和内存转储收集

若要启用内存转储收集和串行控制台，请运行以下脚本：

1. 打开提升的命令提示符会话（以管理员身份运行）。
2. 运行以下命令：

   启用串行控制台

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. 验证 OS 磁盘上的可用空间是否与 VM 上的内存大小 (RAM) 相当。

   如果 OS 磁盘上没有足够的空间，则应更改内存转储文件的创建位置，将其指向任何已附加到 VM 且具有足够可用空间的数据磁盘。 若要更改位置，请将以下命令中的“%SystemRoot%”替换为数据磁盘的驱动器号（例如“F:”）。

#### <a name="suggested-configuration-to-enable-os-dump"></a>用于启用 OS 转储的建议配置

**加载损坏的 OS 磁盘**：

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**在 ControlSet001 上启用：**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**在 ControlSet002 上启用：**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**卸载损坏的 OS 磁盘：**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>重建原始 VM

使用 [VM 修复命令的步骤 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 重新装配 VM。
