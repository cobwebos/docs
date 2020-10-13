---
title: 应用 Windows 更新时 Azure VM 无响应并出现 C01A001D 错误
description: 本文提供的步骤可以解决 Windows 更新产生错误并且 Azure VM 无响应的问题。
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3528
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: v-mibufo
ms.openlocfilehash: 76c3f729a8520c7bff7b49a1d2200d7950f8a9f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074308"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>应用 Windows 更新时 VM 无响应并出现“C01A001D”错误

本文提供的步骤可以解决 Windows 更新 (KB) 产生错误并且 Azure VM 无响应的问题。

## <a name="symptoms"></a>症状

使用[启动诊断](./boot-diagnostics.md)查看 VM 的屏幕截图时，其中显示 Windows 更新 (KB) 正在进行，但更新失败并出现错误代码：“C01A001D”。

![Windows 更新无响应](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>原因

无法在文件系统中创建某个核心文件。 操作系统无法将文件写入磁盘。

## <a name="resolution"></a>解决方法

### <a name="process-overview"></a>过程概述

1. [创建并访问修复 VM](#create-and-access-a-repair-vm)。
2. [释放硬盘上的空间](#free-up-space-on-the-hard-disk)。
3. [建议：在重建 VM 之前，启用串行控制台和内存转储收集](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection)。
4. [重建 VM](#rebuild-the-vm)。

> [!NOTE]
> 发生此错误时，来宾 OS 不会正常运行。 必须在脱机模式下进行故障排除才能解决此问题。

### <a name="create-and-access-a-repair-vm"></a>创建并访问修复 VM

1. 按照 [VM 修复命令的步骤 1-3](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) 来准备一个修复 VM。
2. 使用远程桌面连接来连接到修复 VM。

### <a name="free-up-space-on-the-hard-disk"></a>释放硬盘上的空间

如果磁盘空间尚不足 1 TB，必须调整其大小。 磁盘空间达到 1 TB 后，执行磁盘清理和驱动器碎片整理。

1. 检查磁盘是否已满。 如果磁盘空间低于 1 TB，请[使用 PowerShell 将其扩展到最大 1 TB](../windows/expand-os-disk.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)。
2. 磁盘空间达到 1 TB 后，执行磁盘清理。
    - [从损坏的 VM 中分离数据磁盘](../windows/detach-disk.md)。
    - [将数据磁盘附加到正常运行的 VM](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)。
    - 使用[磁盘清理工具](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup)来释放空间。
3. 调整大小并清理后，对驱动器进行碎片整理：

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    此过程可能需要几个小时，具体取决于碎片化的程度。

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>建议：在重建 VM 之前，启用串行控制台和内存转储收集

1. 打开提升的命令提示符会话（以管理员身份运行）。
2. 运行以下命令：

    启用串行控制台：

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. 确保 OS 磁盘上的可用空间至少等于 VM 内存 (RAM) 大小。

    如果 OS 磁盘上没有足够的空间，请更改要在其中创建内存转储文件的位置，并将该位置引用到 VM 上附加的且具有足够可用空间的数据磁盘。 若要更改位置，请将以下命令中的 `%SystemRoot%` 替换为数据磁盘的驱动器号（例如“F:”）：

    **启用 OS 转储的建议配置：**

    加载损坏的 OS 磁盘：

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    在 ControlSet001 上启用：

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    在 ControlSet002 上启用：

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    卸载损坏的 OS 磁盘：

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>重建 VM

使用 [VM 修复命令的步骤 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 重新装配 VM。
