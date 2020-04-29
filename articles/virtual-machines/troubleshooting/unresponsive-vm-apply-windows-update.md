---
title: 应用 Windows 更新时，Azure VM 无响应 C01A001D 错误
description: 本文提供了一些步骤，用于解决 Windows update 在 Azure VM 中产生错误并使其变得无响应的问题。
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
ms.openlocfilehash: 16c8eed3377c2191b4345ec59ec1eba8be01369d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633952"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>应用 Windows 更新时，VM 无响应 "C01A001D" 错误

本文提供了一些步骤，用于解决 Windows 更新（KB）生成错误并在 Azure VM 中变得无响应的问题。

## <a name="symptoms"></a>症状

使用 "[启动诊断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)" 查看 VM 的屏幕截图时，会显示 WINDOWS 更新（KB），但会失败，错误代码为： "C01A001D"。

![无响应 Windows 更新](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>原因

无法在文件系统中创建核心文件。 操作系统无法将文件写入磁盘。

## <a name="resolution"></a>解决方法

### <a name="process-overview"></a>过程概述

1. [创建和访问修复 VM](#create-and-access-a-repair-vm)。
2. [释放硬盘上的空间](#free-up-space-on-the-hard-disk)。
3. [建议：在重新生成 VM 之前，启用串行控制台和内存转储收集](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection)。
4. [重新生成 VM](#rebuild-the-vm)。

> [!NOTE]
> 发生此错误时，来宾操作系统不能正常工作。 若要解决此问题，必须在脱机模式下进行故障排除。

### <a name="create-and-access-a-repair-vm"></a>创建和访问修复 VM

1. 遵循[VM 修复命令的步骤 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)来准备修复 VM。
2. 使用远程桌面连接连接到修复 VM。

### <a name="free-up-space-on-the-hard-disk"></a>释放硬盘上的空间

如果磁盘不是 1 Tb，则必须调整其大小。 磁盘为 1 TB 后，执行磁盘清理和驱动器碎片整理。

1. 检查磁盘是否已满。 如果磁盘低于 1 Tb，请[使用 PowerShell 将其扩展到最大 1 tb](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)。
2. 磁盘为 1 Tb 后，执行磁盘清理。
    - 将[数据磁盘与损坏的 VM 分离](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk)。
    - [将数据磁盘附加到正常运行的 VM](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm)。
    - 使用 "[磁盘清理" 工具](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup)释放空间。
3. 调整大小并清理后，对驱动器进行碎片整理：

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    这可能需要几个小时，具体取决于碎片级别。

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>建议：在重新生成 VM 之前，启用串行控制台和内存转储收集

1. 打开提升的命令提示符会话（以管理员身份运行）。
2. 运行以下命令：

    启用串行控制台：

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. 请确保 OS 磁盘上的可用空间至少等于 VM 内存（RAM）大小。

    如果 OS 磁盘上没有足够的空间，请更改将创建内存转储文件的位置，并将其引用到已附加到 VM 的数据磁盘，并且具有足够的可用空间。 若要更改位置，请`%SystemRoot%`在以下命令中将替换为数据磁盘的驱动器号（例如 "F："）：

    **启用 OS 转储建议的配置：**

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

### <a name="rebuild-the-vm"></a>重新生成 VM

使用[vm 修复命令的步骤 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)重新组装 vm。
