---
title: Windows 停止错误 - 0x00000074 错误系统配置信息
description: 本文提供了相关步骤，用于解决由于 Azure 虚拟机 (VM) 中的系统配置信息不正确而导致 Windows 无法启动并且需要重启的问题。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a1e47f6a-c7d5-4327-a7b0-ad48ed543641
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: 7d1233c97ec80d5a2efa8b53c68e9e07a823165d
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977025"
---
# <a name="windows-stop-error---0x00000074-bad-system-config-info"></a>Windows 停止错误 - 0x00000074 错误系统配置信息

本文提供了相关步骤，用于解决由于 Azure 虚拟机 (VM) 中的系统配置信息不正确而导致 Windows 无法启动并且需要重启的问题。

## <a name="symptom"></a>症状

使用[启动诊断](./boot-diagnostics.md)查看 VM 的屏幕截图时，可看到屏幕截图显示 Windows 停止代码“#0x00000074”或“BAD_SYSTEM_CONFIG_INFO” 。

你的电脑遇到问题，需要重启 *。你可以重启。
有关此问题和可能的修补程序的详细信息，请访问 http://windows.com/stopcode
如果致电支持人员，请向他们提供以下信息：
停止代码* ** ** *：BAD_SYSTEM_CONFIG_INFO*

  ![Windows 停止代码 0x00000074，也显示为“BAD_SYSTEM_CONFIG_INFO”。 Windows 会通知用户其电脑遇到问题，需要重启。](./media/windows-stop-error-bad-system-config-info/1.png)

## <a name="cause"></a>原因

如果“SYSTEM”注册表配置单元似乎已损坏，则会出现“BAD_SYSTEM_CONFIG_INFO”停止代码 。 此错误可能由以下原因引起：

- 注册表配置单元未正常关闭。
- 注册表配置单元损坏。
- 缺少注册表项或值。

## <a name="solution"></a>解决方案

### <a name="process-overview"></a>过程概述：

1. 创建和访问修复 VM。
1. 检查配置单元损坏情况。
1. 启用串行控制台和内存转储收集。
1. 重新生成 VM。

> [!NOTE]
> 遇到此错误时，来宾操作系统 (OS) 无法正常运行。 将在脱机模式下进行故障排除才能解决此问题。

### <a name="create-and-access-a-repair-vm"></a>创建和访问修复 VM

1. 使用 [VM 修复命令](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) 的步骤 1-3 准备一个修复 VM。
1. 检查配置单元损坏情况。
1. 使用远程桌面连接来连接到修复 VM。
1. 复制 `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config` 文件夹并将其保存在运行正常的磁盘分区或其他安全位置。 请备份此文件夹以防万一，因为你将编辑关键注册表文件。 

> [!NOTE]
> 复制 `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config` 文件夹作为备份，以备你需要回退对注册表所做的任何更改。

### <a name="check-for-hive-corruption"></a>检查配置单元损坏情况

下面的说明将帮助你确定原因是否是 hive 损坏，或者配置单元是否未正确关闭。 如果未正确关闭 hive，你将能够解锁文件并修复 VM。

1. 在修复 VM 上，打开“注册表编辑器”应用程序。 在 Windows 搜索栏中键入 "REGEDIT" 以找到它。
1. 在注册表编辑器中，选择“HKEY_LOCAL_MACHINE”以突出显示它，然后选择“文件”>“加载配置单元…”  。
1. 浏览到 `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM` 并选择“打开”。
1. 系统提示输入名称时，请输入 BROKENSYSTEM。

   1. 如果配置单元未能打开，或者为空，则说明配置单元已损坏。 如果配置单元已损坏，请[开具支持工单](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

     ![出现错误，指出注册表编辑器无法加载配置单元。](./media/windows-stop-error-bad-system-config-info/2.png)

   1. 如果配置单元正常打开，则 hive 未正确关闭。 继续执行步骤 5.

1. 若要修复未正确关闭的配置单元，请突出显示 " **BROKENSYSTEM** "，然后选择 "**文件 > 卸载 hive ...** " 以解锁该文件。

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