---
title: Windows 虚拟机无法启动，因为 windows 启动管理器
description: 本文介绍了在 Windows 启动管理器阻止启动 Azure 虚拟机时遇到的问题。
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3598
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 5d2fb62870e2c41af635627f5d692f08c67f8394
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80373344"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>Windows VM 无法启动，因为 Windows 启动管理器

本文提供了解决 Windows 启动管理器阻止启动 Azure 虚拟机（VM）的问题的步骤。

## <a name="symptom"></a>症状

VM 会停滞在用户提示下等待，并且除非手动指示，否则不会启动。

使用 "[启动诊断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)" 查看 VM 的屏幕截图时，你会看到屏幕截图显示 Windows 启动管理器，其中包含消息 "*选择要启动的操作系统"，或者按 tab 键以选择工具：*。

图 1
 
![Windows 启动管理器指示 "选择要启动的操作系统，或按 TAB 键以选择工具："](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>原因

此错误是由于*displaybootmenu*在 Windows 启动管理器中出现 BCD 标志错误引起的。 启用标志后，Windows 启动管理器将在启动过程中提示用户选择要运行的加载程序，从而导致启动延迟。 在 Azure 中，此功能可以添加到启动 VM 所用的时间。

## <a name="solution"></a>解决方案

过程概述：

1. 使用串行控制台配置更快启动时间。
2. 创建和访问修复 VM。
3. 配置修复 VM 上的更快启动时间。
4. **建议**：在重建 VM 之前，启用串行控制台和内存转储收集。
5. 重新生成 VM。

### <a name="configure-for-faster-boot-time-using-serial-console"></a>使用串行控制台配置更快启动时间

如果有权访问串行控制台，可以通过两种方式来实现更快的启动时间。 请缩短*displaybootmenu*等待时间，或完全删除该标志。

1. 按照说明访问[适用于 Windows 的 Azure 串行控制台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows)，以获取对基于文本的控制台的访问权限。

   > [!NOTE]
   > 如果无法访问串行控制台，请直接跳到[创建和访问修复 VM](#create-and-access-a-repair-vm)。

2. **选项 A**：缩短等待时间

   a. 等待时间默认设置为30秒，但可以更改为更快的时间（如5秒）。

   b. 在串行控制台中使用以下命令来调整超时值：

      `bcdedit /set {bootmgr} timeout 5`

3. **选项 B**：删除 BCD 标志

   a. 若要完全阻止显示启动菜单提示符，请输入以下命令：

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > 如果在上述步骤中无法使用串行控制台来配置更快的启动时间，则可继续执行以下步骤。 你现在会在脱机模式下进行故障排除，以解决此问题。

### <a name="create-and-access-a-repair-vm"></a>创建和访问修复 VM

1. 使用[VM 修复命令的步骤 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)来准备修复 VM。
2. 使用远程桌面连接连接到修复 VM。

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>配置修复 VM 上的更快启动时间

1. 打开权限提升的命令提示符。
2. 输入以下内容来启用 DisplayBootMenu：

   对于**第1代 vm**，请使用此命令：

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   对于**第2代 vm**，请使用此命令：

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   替换任意大于或小于符号以及其中的文本，例如 "此处 < 文本 >"。

3. 将超时值更改为5秒：

   对于**第1代 vm**，请使用此命令：

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   对于**第2代 vm**，请使用此命令：

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   替换任意大于或小于符号以及其中的文本，例如 "此处 < 文本 >"。

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