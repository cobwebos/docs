---
title: 窗口停止错误 -
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373357"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>窗口停止错误 - #0x000000EF"关键进程已死"

本文提供了解决在 Azure VM 中引导过程中关键进程死亡的问题的步骤。

## <a name="symptom"></a>症状

当您使用[引导诊断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)来查看 VM 的屏幕截图时，您将看到屏幕截图显示错误 *#0x000000EF*与消息 *"关键进程已死*"。

![“你的电脑遇到问题，需要重启。 我们将收集一些错误信息，然后你就可以重启。 （=% 完成）如果您想了解更多，您可以稍后在线搜索此错误：0x000000EF"](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>原因

通常，这是因为在引导过程中的关键系统进程失败。 您可以在["Bug 检查 0xEF：CRITICAL_PROCESS_DIED"](https://docs.microsoft.com/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)上阅读更多有关关键流程问题的文章。

## <a name="solution"></a>解决方案

### <a name="process-overview"></a>流程概述：

1. 创建和访问修复 VM。
2. 修复任何操作系统损坏。
3. **建议**：在重建 VM 之前，请启用串行控制台和内存转储集合。
4. 重建 VM。

> [!NOTE]
> 遇到此启动错误时，来宾操作系统无法运行。 您将在脱机模式下进行故障排除以解决此问题。

### <a name="create-and-access-a-repair-vm"></a>创建和访问修复 VM

1. 使用[VM 修复命令的步骤 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)准备修复 VM。
2. 使用远程桌面连接连接到修复 VM。

### <a name="fix-any-os-corruption"></a>修复任何操作系统损坏

1. 打开权限提升的命令提示符。
2. 运行以下系统文件检查器 （SFC） 命令：

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * 其中<引导磁盘驱动器>是修复 VM 的启动卷（通常为"C："）和< BROKEN 磁盘驱动器>将是损坏的 VM 中连接的磁盘的驱动器号。 将大于 /小于符号的符号以及符号中包含的文本（例如"此处<文本>"替换为相应的字母。

3. 接下来，使用[VM 修复命令的步骤 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)重新组装 VM，并查看 VM 是否启动。
4. 如果 VM 仍未启动，则继续收集内存转储文件。

### <a name="collect-the-memory-dump-file"></a>收集内存转储文件

如果运行 SFC 后问题仍然存在，则需要分析内存转储文件以确定问题的原因。 要收集内存转储文件，请按照以下步骤操作：

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>将操作系统磁盘连接到新的修复 VM

1. 使用[VM 修复命令的步骤 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)准备新的修复 VM。
2. 使用远程桌面连接连接到修复 VM。

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找到转储文件并提交支持票证

3. 在修复 VM 上，转到连接的 OS 磁盘中的窗口文件夹。 如果分配给附加的 OS 磁盘的驱动程序字母为*F，* 则需要转到*F：_Windows*。
4. 找到*内存.dmp*文件，然后提交包含内存转储文件[的支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

   > [!NOTE]
   > 如果找不到转储文件，请完成以下步骤以启用内存转储集合和串行控制台，然后返回到此部分，并重复上述任务中的步骤以收集内存转储文件。

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>建议：在重建 VM 之前，请启用串行控制台和内存转储集合

要启用内存转储集合和串行控制台，请运行以下脚本：

1. 打开提升的命令提示会话（以管理员身份运行）。
2. 运行以下命令：

   启用串行控制台

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   替换任何大于或小于符号以及符号内的文本，例如"<文本>"。

3. 验证 OS 磁盘上的可用空间是否与 VM 上的内存大小 （RAM） 相同。

如果 OS 磁盘上没有足够的空间，则应更改将创建内存转储文件的位置，并将其引用到连接到具有足够可用空间的 VM 的任何数据磁盘。 要更改位置，请将"%SystemRoot%"替换为以下命令中数据磁盘的驱动器号（例如"F："）。

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

### <a name="rebuild-the-original-vm"></a>重建原始 VM

使用[VM 修复命令的步骤 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)重新组装 VM。
