---
title: 由于 Windows 引导管理器，Windows 虚拟机无法启动
description: 本文提供了解决 Windows 引导管理器阻止启动 Azure 虚拟机的问题的步骤。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373344"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>由于 Windows 引导管理器，Windows VM 无法启动

本文提供了解决 Windows 引导管理器阻止启动 Azure 虚拟机 （VM） 的问题的步骤。

## <a name="symptom"></a>症状

VM 卡在用户提示后处于等待，除非手动指示，否则不会启动。

当您使用[引导诊断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)来查看 VM 的屏幕截图时，您将看到屏幕截图显示 Windows 启动管理器，并显示消息 *"选择要启动的操作系统"，或者按 TAB 选择工具：*。

图 1
 
![Windows 启动管理器指出"选择要启动的操作系统，或按 TAB 选择工具：](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>原因

该错误是由于 Windows 启动管理器中的 BCD 标志*显示引导菜单*造成的。 启用标志后，Windows 引导管理器会提示用户在引导过程中选择要运行的加载程序，从而导致启动延迟。 在 Azure 中，此功能可以添加到启动 VM 所需的时间。

## <a name="solution"></a>解决方案

流程概述：

1. 使用串行控制台配置更快的启动时间。
2. 创建和访问修复 VM。
3. 配置修复 VM 上的启动时间更快。
4. **建议**：在重建 VM 之前，请启用串行控制台和内存转储集合。
5. 重建 VM。

### <a name="configure-for-faster-boot-time-using-serial-console"></a>使用串行控制台配置更快的启动时间

如果您有权访问串行控制台，可通过两种方式实现更快的启动时间。 减少*显示引导菜单*等待时间，或完全删除标志。

1. 按照指示访问[Windows 的 Azure 串行控制台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows)以访问基于文本的控制台。

   > [!NOTE]
   > 如果无法访问串行控制台，请跳到"[创建和访问修复 VM"。](#create-and-access-a-repair-vm)

2. **选项 A**： 减少等待时间

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 默认情况下，等待时间设置为 30 秒，但可以更改为更快速的时间（例如 5 秒）。

   b.保留“数据库类型”设置，即设置为“共享”。 在串行控制台中使用以下命令来调整超时值：

      `bcdedit /set {bootmgr} timeout 5`

3. **选项 B**： 删除 BCD 标志

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 要完全防止显示引导菜单提示，请输入以下命令：

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > 如果无法使用串行控制台在上述步骤中配置更快的启动时间，则可以继续执行以下步骤。 现在，您将在脱机模式下进行故障排除以解决此问题。

### <a name="create-and-access-a-repair-vm"></a>创建和访问修复 VM

1. 使用[VM 修复命令的步骤 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)准备修复 VM。
2. 使用远程桌面连接连接到修复 VM。

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>配置修复 VM 上的更快的启动时间

1. 打开权限提升的命令提示符。
2. 输入以下内容以启用 DisplayBootMenu：

   对于**第 1 代 VM**使用此命令：

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   对于**第 2 代 VM**使用此命令 ：

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   替换任何大于或小于符号以及符号内的文本，例如"<文本>"。

3. 将超时值更改为 5 秒：

   对于**第 1 代 VM**使用此命令：

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   对于**第 2 代 VM**使用此命令 ：

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   替换任何大于或小于符号以及符号内的文本，例如"<文本>"。

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