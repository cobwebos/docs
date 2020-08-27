---
title: Windows 停止错误-0x00000074 错误的系统配置信息
description: 本文提供了一些步骤，用于解决由于 Azure 虚拟机 (VM) 中的系统配置信息不正确而导致 Windows 无法启动和需要重新启动的问题。
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
ms.openlocfilehash: 071b5786127af31a2ad3266c128dbfb7cacad656
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88942058"
---
# <a name="windows-stop-error---0x00000074-bad-system-config-info"></a>Windows 停止错误-0x00000074 错误的系统配置信息

本文提供了一些步骤，用于解决由于 Azure 虚拟机 (VM) 中的系统配置信息不正确而导致 Windows 无法启动和需要重新启动的问题。

## <a name="symptom"></a>症状

使用 " [启动诊断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) " 查看 VM 的屏幕截图时，会看到屏幕截图显示 Windows stop 代码 **#0x00000074** 或 **BAD_SYSTEM_CONFIG_INFO**。

*你的电脑遇到问题，需要重新启动。你可以重新启动。* 
有关*此问题和可能的修复的详细信息， http://windows.com/stopcode 请访问* 
*如果致电支持人员，请向他们提供以下信息：* 
*停止代码： BAD_SYSTEM_CONFIG_INFO*

  ![Windows stop code 0x00000074，也显示为 "BAD_SYSTEM_CONFIG_INFO"。 Windows 会通知用户其 PC 已遇到问题，需要重新启动。](./media/windows-stop-error-bad-system-config-info/1.png)

## <a name="cause"></a>原因

如果**系统**注册表配置单元出现损坏，则会出现**BAD_SYSTEM_CONFIG_INFO**停止代码。 此错误可能由以下原因引起：

- 注册表配置单元未正常关闭。
- 注册表配置单元已损坏。
- 缺少注册表项或值。

## <a name="solution"></a>解决方案

### <a name="process-overview"></a>过程概述：

1. 创建和访问修复 VM。
1. 检查 hive 损坏情况。
1. 启用串行控制台和内存转储收集。
1. 重新生成 VM。

> [!NOTE]
> 遇到此错误时，来宾操作系统 (操作系统) 不能正常运行。 你将在脱机模式下进行故障排除以解决此问题。

### <a name="create-and-access-a-repair-vm"></a>创建和访问修复 VM

1. 使用 [VM 修复命令](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) 的步骤 1-3 准备一个修复 VM。
1. 检查 hive 损坏情况。
1. 使用远程桌面连接连接到修复 VM。
1. 复制 `\windows\system32\config` 文件夹，并将其保存在正常的磁盘分区或另一个安全的位置。 请将此文件夹作为预防措施进行备份，因为你将编辑关键注册表文件。

> [!NOTE]
> 如果需要回滚对注册表所做的任何更改，请将该文件夹的副本创建 `\windows\system32\config` 为备份。

### <a name="check-for-hive-corruption"></a>检查 hive 损坏

下面的说明将帮助你确定原因是否是 hive 损坏，或者配置单元是否未正确关闭。 如果未正确关闭 hive，你将能够解锁文件并修复 VM。

1. 在修复 VM 上，打开 **注册表编辑器** 应用程序。 在 Windows 搜索栏中键入 "REGEDIT" 以找到它。
1. 在注册表编辑器中，选择 " **HKEY_LOCAL_MACHINE** " 将其突出显示，然后选择 "**文件 > 加载 Hive ...** " 。
1. 浏览到 `\windows\system32\config\SYSTEM` 并选择 " **打开**"。
1. 当系统提示输入名称时，输入 **BROKENSYSTEM**。

   1. 如果配置单元未能打开，或者如果为空，则配置单元已损坏。 如果该配置单元已损坏，请 [打开支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

     ![出现错误，指出注册表编辑器无法加载该配置单元。](./media/windows-stop-error-bad-system-config-info/2.png)

   1. 如果配置单元正常打开，则 hive 未正确关闭。 继续执行步骤5。

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

使用 [VM 修复命令的步骤 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 重新生成 VM。
