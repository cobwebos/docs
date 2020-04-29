---
title: Windows 停止错误-
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
ms.openlocfilehash: 9e4c4b9c809a626c71b4a7e9235d917b442be160
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80373357"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Windows Stop 错误-#0x000000EF "关键进程终止"

本文提供了一些步骤，用于解决在 Azure VM 中启动时关键过程停止的问题。

## <a name="symptom"></a>症状

使用 "[启动诊断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)" 查看 VM 的屏幕截图时，你会看到屏幕截图显示错误 " *#0x000000EF*消息"*关键进程终止*"。

![“你的电脑遇到问题，需要重启。 我们将收集一些错误信息，然后你就可以重启。 （完成 # #%）如果你想了解更多详细信息，可以稍后在 online 中搜索此错误： 0x000000EF "](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>原因

通常，这是由于在启动过程中出现关键系统进程失败所致。 你可以在 "[Bug 检查0xEF： CRITICAL_PROCESS_DIED](https://docs.microsoft.com/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)" 中阅读有关关键流程问题的详细信息。

## <a name="solution"></a>解决方案

### <a name="process-overview"></a>过程概述：

1. 创建和访问修复 VM。
2. 修复任何操作系统损坏。
3. **建议**：在重建 VM 之前，启用串行控制台和内存转储收集。
4. 重新生成 VM。

> [!NOTE]
> 当遇到此启动错误时，来宾操作系统不能正常工作。 你将在脱机模式下进行故障排除，以解决此问题。

### <a name="create-and-access-a-repair-vm"></a>创建和访问修复 VM

1. 使用[VM 修复命令的步骤 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)来准备修复 VM。
2. 使用远程桌面连接连接到修复 VM。

### <a name="fix-any-os-corruption"></a>修复任何 OS 损坏

1. 打开权限提升的命令提示符。
2. 运行以下系统文件检查器（SFC）命令：

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * 其中 < BOOT DISK DRIVE > 是修复 VM 的启动卷（通常为 "C："），< 损坏的磁盘驱动器 > 将是已损坏 VM 的附加磁盘的驱动器号。 替换大于或小于符号以及其中包含的文本（例如，"在此处 < 文本 >"），并将相应的字母替换为。

3. 接下来，使用[Vm 修复命令的步骤 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)重新组装 vm，并查看其是否已启动。
4. 如果 VM 仍未启动，则继续收集内存转储文件。

### <a name="collect-the-memory-dump-file"></a>收集内存转储文件

如果在运行 SFC 后问题仍然存在，则需要分析内存转储文件以确定问题的原因。 若要收集内存转储文件，请执行以下步骤：

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>将 OS 磁盘附加到新的修复 VM

1. 使用[VM Repair 命令的步骤 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)准备新的修复 VM。
2. 使用远程桌面连接连接到修复 VM。

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找到转储文件并提交支持票证

3. 在修复 VM 上，在附加的 OS 磁盘中中转到 windows 文件夹。 如果分配给附加 OS 磁盘的驱动程序号是*F*，则需要切换到*F:\Windows*。
4. 找到*内存 dmp*文件，然后使用内存转储文件[提交支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

   > [!NOTE]
   > 如果找不到转储文件，请完成以下步骤以启用内存转储收集和串行控制台，然后返回到此部分并重复执行上述任务中的步骤来收集内存转储文件。

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>建议：在重建 VM 之前，启用串行控制台和内存转储收集

若要启用内存转储收集和串行控制台，请运行以下脚本：

1. 打开提升的命令提示符会话（以管理员身份运行）。
2. 运行以下命令：

   启用串行控制台

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   替换任意大于或小于符号以及其中的文本，例如 "此处 < 文本 >"。

3. 验证 OS 磁盘上的可用空间是否与 VM 上的内存大小（RAM）一样多。

如果 OS 磁盘上没有足够的空间，则应更改内存转储文件的创建位置，并将其引用到任何附加到具有足够可用空间的 VM 的数据磁盘。 要更改位置，请在以下命令中将 "% SystemRoot%" 替换为数据磁盘的驱动器号（例如，"F："）。

#### <a name="suggested-configuration-to-enable-os-dump"></a>启用 OS 转储的建议配置

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

### <a name="rebuild-the-original-vm"></a>重新生成原始 VM

使用[Vm 修复命令的步骤 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)重新组装 vm。
