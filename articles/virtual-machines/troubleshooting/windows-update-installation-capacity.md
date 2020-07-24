---
title: 操作系统启动疑难解答 - Windows 更新安装容量
description: 解决 Windows 更新 (KB) 在 Azure VM 中收到错误且无响应的问题的步骤。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 6359e486-7b02-4c1e-995c-ef6d505f85f4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: 596303223554589ef26938486ccfd2281ccd46f5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999099"
---
# <a name="troubleshoot-os-start-up--windows-update-installation-capacity"></a>操作系统启动疑难解答 - Windows 更新安装容量

本文提供了相关步骤来解决 Azure 虚拟机 (VM) 中 Windows 更新 (KB) 收到错误且无响应的问题。

## <a name="symptom"></a>症状

使用引导诊断查看 VM 的屏幕截图时，将看到屏幕截图显示 Windows 更新 (KB) 正在进行中，但却失败并显示错误代码：C01A001D。 下图显示 Windows 更新 (KB) 停滞且出现“错误 C01A001D: 正在应用更新操作 #####/##### (######)”消息：

![Windows 更新 (KB) 停滞且出现“错误 C01A001D: 正在应用更新操作 X/Y (Z)”消息。](./media/troubleshoot-windows-update-installation-capacity/1.png)

## <a name="cause"></a>原因

在这种情况下，操作系统 (OS) 无法完成 Windows 更新 (KB) 安装，因为无法在文件系统上创建核心文件。 此错误代码指出操作系统无法将任何文件写入磁盘。

## <a name="solution"></a>解决方案

### <a name="process-overview"></a>流程概述：

1. 创建和访问修复 VM。
1. 释放磁盘上的空间。
1. 启用串行控制台和内存转储集合。
1. 重新生成 VM。

> [!NOTE]
> 遇到此错误时，来宾操作系统无法正常运行。 在脱机模式下进行故障排除来解决此问题。

### <a name="create-and-access-a-repair-vm"></a>创建和访问修复 VM

1. 使用 [VM 修复命令](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) 的步骤 1-3 准备一个修复 VM。
1. 使用远程桌面连接来连接到修复 VM。

### <a name="free-up-space-on-the-disk"></a>释放磁盘上的空间

若要解决此问题：

- 如果磁盘尚未达到最大大小 1 TB，请将其大小调整为 1 TB。
- 执行磁盘清理。
- 对驱动器进行碎片整理。

1. 检查磁盘是否已满。 如果磁盘大小小于 1 TB，请[使用 PowerShell](../windows/expand-os-disk.md) 将其扩展到最大 1 TB。
1. 如果磁盘已是 1 TB，则需要执行磁盘清理。
   1. [从已损坏的 VM 中](../windows/detach-disk.md)分离数据磁盘。
   1. 将数据磁盘附加[到正常运行的 VM](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)。
   1. 使用[磁盘清理工具](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup)来释放空间。
1. 完成大小调整和清理后，使用以下命令对驱动器进行碎片整理：

   ```
   defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
   ```
   
碎片整理可能需要几个小时，具体取决于碎片级别。

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>启用串行控制台和内存转储集合

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

    **加载损坏的 OS 磁盘：**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM 
   ```
   
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

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>重新生成 VM

使用 [VM 修复命令的步骤 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 重新生成 VM。
