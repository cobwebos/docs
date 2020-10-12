---
title: Windows 停止错误 -
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5b3ed56a-5a11-4ff9-9ee8-76aea4a5689b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: c04f3b27c7214dcf821c7698796bfaea399b947d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86509097"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Windows 停止错误 - #0x000000EF“关键进程已终止”

本文提供用于解决在 Azure VM 中启动期间关键进程终止的问题的步骤。

## <a name="symptom"></a>症状

使用[启动诊断](./boot-diagnostics.md)查看 VM 的屏幕截图时，看到屏幕截图中显示了错误“#0x000000EF”和消息“关键进程已终止”   。

![“你的电脑遇到问题，需要重启。 我们将收集一些错误信息，然后你就可以重启。 (已完成 ##%)如果想了解更多信息，可以稍后联机搜索以下错误:0x000000EF”](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>原因

通常，此错误是在启动过程中关键的系统进程失败造成的。 可以在“[Bug 检查 0xEF:CRITICAL_PROCESS_DIED](/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)”中详细了解关键进程。

## <a name="solution"></a>解决方案

### <a name="process-overview"></a>过程概述：

1. 创建并访问修复 VM。
2. 修复任何 OS 损坏情况。
3. **建议**：在重建 VM 之前，启用串行控制台和内存转储收集。
4. 重建 VM。

> [!NOTE]
> 遇到此启动错误时，来宾 OS 不会正常运行。 需要在脱机模式下进行故障排除才能解决此问题。

### <a name="create-and-access-a-repair-vm"></a>创建并访问修复 VM

1. 使用 [VM 修复命令的步骤 1-3](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) 来准备一个修复 VM。
2. 使用远程桌面连接来连接到修复 VM。

### <a name="fix-any-os-corruption"></a>修复任何 OS 损坏情况

1. 打开权限提升的命令提示符。
2. 运行以下系统文件检查程序 (SFC) 命令：

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * 其中，< BOOT DISK DRIVE > 是修复 VM 的启动卷（通常为“C:”），< BROKEN DISK DRIVE > 是已损坏 VM 中的附加磁盘的驱动器号。 请将大于号/小于号及其包含的文本（例如“< text here >”）替换为相应的驱动器号。

3. 接下来，使用 [VM 修复命令的步骤 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 重新装配 VM，并查看它是否可启动。
4. 如果 VM 仍然不能启动，请继续收集内存转储文件。

### <a name="collect-the-memory-dump-file"></a>收集内存转储文件

如果在运行 SFC 后仍然出现问题，则需要分析内存转储文件以确定问题的原因。 若要收集内存转储文件，请执行以下步骤：

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>将 OS 磁盘附加到新的修复 VM

1. 使用 [VM 修复命令的步骤 1-3](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) 来准备一个新的修复 VM。
2. 使用远程桌面连接来连接到修复 VM。

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找到转储文件并提交支持票证

3. 在修复 VM 上，转到附加的 OS 磁盘中的 Windows 文件夹。 如果分配给附加 OS 磁盘的驱动器号为 F，则需转到 F:\Windows   。
4. 找到 memory.dmp 文件，然后使用该内存转储文件[提交支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)  。

   > [!NOTE]
   > 如果找不到转储文件，请完成以下步骤以启用内存转储收集和串行控制台，然后返回到本部分，并重复执行上述任务中的步骤来收集内存转储文件。

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>建议：在重建 VM 之前，启用串行控制台和内存转储收集

若要启用内存转储收集和串行控制台，请运行以下脚本：

1. 打开提升的命令提示符会话（以管理员身份运行）。
2. 运行以下命令：

   启用串行控制台

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   替换所有大于号或小于号以及其中的文本，例如“< text here >”。

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
