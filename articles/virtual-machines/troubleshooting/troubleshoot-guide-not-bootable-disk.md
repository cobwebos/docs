---
title: 启动错误 = "这不是可引导磁盘"
description: 本文提供了解决磁盘在 Azure 虚拟机中无法启动的问题的步骤
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
ms.openlocfilehash: 9f0c6350b89dcfecefcadcc166f7af35abc4b128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80300974"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>启动错误 = 这不是可引导磁盘

本文提供了解决磁盘在 Azure 虚拟机 （VM） 中无法启动的问题的步骤。

## <a name="symptoms"></a>症状

当您使用[Boot 诊断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)来查看 VM 的屏幕截图时，您将看到屏幕截图显示提示，提示显示消息"这不是可引导磁盘"。 请插入可启动软盘，然后按任意键重试..."。

   图 1

   ![图 1 显示了消息 #"这不是可引导磁盘。 请插入可启动软盘，然后按任意键重试..."。](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>原因

此错误消息表示操作系统启动进程找不到活动系统分区。 此错误还可能意味着启动配置数据 （BCD） 存储中缺少引用，从而阻止它定位 Windows 分区。

## <a name="solution"></a>解决方案

### <a name="process-overview"></a>流程概述

1. 创建和访问修复 VM。
2. 将分区状态设置为"活动"。
3. 修复磁盘分区。
4. **建议**：在重建 VM 之前，请启用串行控制台和内存转储集合。
5. 重建原始 VM。

   > [!NOTE]
   > 遇到此启动错误时，来宾操作系统无法运行。 您将在脱机模式下进行故障排除以解决此问题。

### <a name="create-and-access-a-repair-vm"></a>创建和访问修复 VM

1. 使用[VM 修复命令](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)的步骤 1-3 准备修复 VM。
2. 使用远程桌面连接连接到修复 VM。

### <a name="set-partition-status-to-active"></a>将分区状态设置为活动

第 1 代 VM 应首先验证保存 BCD 存储的操作系统分区是否标记为*活动*。 如果您有第 2 代 VM，请跳到修复[磁盘分区](#fix-the-disk-partition)，因为*状态*标志在后一代中被弃用。

1. 打开提升的命令提示*符 （cmd.exe）。*
2. 输入*磁盘部分*以启动 DISKPART 工具。
3. 输入*列表磁盘*以列出系统上的磁盘并标识附加的 OS VHD。
4. 找到连接的 OS VHD 后，输入*sel 磁盘 +* 以选择磁盘。  参见图 2，其中磁盘 1 是附加的 OS VHD。

   图 2

   ![图 2 显示了表中显示列表磁盘命令、磁盘 0 和磁盘 1 的输出的 [DISKPART] 窗口。  还显示 sel 磁盘 1 命令的输出，磁盘 1 是所选磁盘](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. 选择磁盘后，输入*列表分区*以列出所选磁盘的分区。
6. 识别引导分区后，输入*sel 分区 +* 以选择分区。  通常引导分区的大小约为 350 MB。  参见图 3，其中分区 1 是引导分区。

   图 3

   ![图 3 显示了具有 [列表分区] 命令输出的 [DISKPART] 窗口。 分区 1 和分区 2 显示在表中。 当分区 1 是所选磁盘时，它还显示 [sel 分区 1] 命令的输出。](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. 输入"详细分区"以检查分区的状态。 参见图 4，其中分区为 *"活动：否*"或图 5，其中分区为"活动：是"。

   图 4

   ![图 4 显示了 [详细分区] 命令的输出的 [DISKPART] 窗口，当分区 1 设置为 [活动：否] 时](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   图 5

   ![图 5 显示了 [磁盘部分] 窗口，其中输出了 [详细分区] 命令，当分区 1 设置为 [活动：是]时。](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. 如果分区**未处于活动状态**，请输入*活动*以更改*活动*标志。
9. 通过键入*详细信息分区*，检查状态更改是否正确完成。

   图 6

   ![图 6 显示了具有 [详细分区] 命令输出的磁盘部分窗口，当分区 1 设置为 [活动：是]](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. 输入*退出*以关闭 DISKPART 工具并保存配置更改。

### <a name="fix-the-disk-partition"></a>修复磁盘分区

1. 打开提升的命令提示符 （cmd.exe）。
2. 使用以下命令在磁盘上运行*CHKDSK*并修复错误：

   `chkdsk <DRIVE LETTER>: /f`

   添加"/f"命令选项将修复磁盘上的任何错误。 请确保替换为<DRIVE LETTER>随附的 OS VHD 的字母。

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>建议：在重建 VM 之前，请启用串行控制台和内存转储集合

要启用内存转储集合和串行控制台，请运行以下脚本：

1. 打开提升的命令提示会话（以管理员身份运行）。
2. 运行以下命令：

   启用串行控制台

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

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
