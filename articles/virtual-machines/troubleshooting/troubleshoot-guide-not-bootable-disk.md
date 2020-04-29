---
title: 启动错误– "这不是可启动磁盘"
description: 本文提供了一些步骤，用于解决无法在 Azure 虚拟机中启动磁盘的问题
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80300974"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>启动错误-这不是可启动磁盘

本文介绍了在 Azure 虚拟机（VM）中无法启动磁盘的问题的步骤。

## <a name="symptoms"></a>症状

使用 "[启动诊断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)" 查看 VM 的屏幕截图时，你会看到屏幕截图显示一条消息 "此不是可启动磁盘" 的提示。 请插入可启动软盘，并按任意键重试 ... "。

   图 1

   ![图1显示消息 * "这不是可启动磁盘。 请插入可启动软盘，并按任意键重试 ... "*](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>原因

此错误消息表示 OS 引导过程找不到活动的系统分区。 此错误还可能表示引导配置数据（BCD）存储中缺少引用，从而导致它无法定位 Windows 分区。

## <a name="solution"></a>解决方案

### <a name="process-overview"></a>流程概述

1. 创建和访问修复 VM。
2. 将分区状态设置为 "活动"。
3. 修复磁盘分区。
4. **建议**：在重建 VM 之前，启用串行控制台和内存转储收集。
5. 重新生成原始 VM。

   > [!NOTE]
   > 如果遇到此启动错误，来宾操作系统将无法正常运行。 你将在脱机模式下进行故障排除，以解决此问题。

### <a name="create-and-access-a-repair-vm"></a>创建和访问修复 VM

1. 使用[VM 修复命令](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)的步骤1-3 来准备修复 VM。
2. 使用远程桌面连接连接到修复 VM。

### <a name="set-partition-status-to-active"></a>将分区状态设置为活动

第1代 Vm 应首先验证包含 BCD 存储的 OS 分区是否被标记为 "*活动*"。 如果有第2代 VM，请跳到[修复磁盘分区](#fix-the-disk-partition)，因为在以后的版本中不推荐使用*状态*标志。

1. 打开提升的命令提示符 *（cmd.exe）*。
2. 输入*diskpart*启动 diskpart 工具。
3. 输入*list disk*列出系统上的磁盘，并识别连接的操作系统 VHD。
4. 找到附加的 OS VHD 后，输入 " *sel disk #* " 以选择该磁盘。  请参阅图2，其中磁盘1是附加的操作系统 VHD。

   图 2

   ![图2显示了 * DISKPART * 窗口，其中显示了表中显示的 "列出磁盘" 命令、"磁盘 0" 和 "磁盘 1" 的输出。  还显示 sel disk 1 命令的输出，磁盘1是所选磁盘](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. 选择磁盘后，输入 "*列表分区*" 以列出所选磁盘的分区。
6. 确定启动分区后，输入*sel partition #* 以选择分区。  通常，启动分区的大小大约为 350 MB。  请参阅图3，其中分区1是启动分区。

   图 3

   ![图3显示了 * DISKPART * 窗口，其中包含 * list partition * 命令的输出。 分区1和分区2显示在表中。 它还显示 * sel partition 1 * 命令的输出（当分区1是选定磁盘时）。](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. 输入 "详细信息分区" 以检查分区的状态。 请参阅图4，其中分区处于*活动状态： No*或图5，其中分区为 "活动：是"。

   图 4

   ![图4显示了 * DISKPART * 窗口，其中包含 * detail partition * 命令的输出（当分区1设置为 * 活动： No *](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   图 5

   ![图5显示了 * DISKPART * 窗口，其中包含 * detail partition * 命令的输出（当分区1设置为 * 活动： Yes * 时）。](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. 如果该分区**不处于活动状态**，请输入 "*活动*" 以更改*活动*标志。
9. 通过键入*详细信息分区*来检查状态更改是否已正确完成。

   图 6

   ![图6显示了当分区1设置为 * Active： Yes * 时输出为 * 详细信息 partition * 命令的 diskpart 窗口](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. 输入*exit*关闭 DISKPART 工具并保存配置更改。

### <a name="fix-the-disk-partition"></a>修复磁盘分区

1. 打开提升的命令提示符（cmd.exe）。
2. 使用以下命令在磁盘上运行*CHKDSK*并修复错误：

   `chkdsk <DRIVE LETTER>: /f`

   添加 "/f" 命令选项将修复磁盘上的任何错误。 请确保将替换<DRIVE LETTER>为附加 OS VHD 的盘符。

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>建议：在重建 VM 之前，启用串行控制台和内存转储收集

若要启用内存转储收集和串行控制台，请运行以下脚本：

1. 打开提升的命令提示符会话（以管理员身份运行）。
2. 运行以下命令：

   启用串行控制台

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

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
