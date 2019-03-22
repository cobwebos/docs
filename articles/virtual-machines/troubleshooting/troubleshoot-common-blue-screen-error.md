---
title: 启动 Azure VM 时出现蓝屏错误 | Microsoft Docs
description: 了解如何排查启动时收到蓝屏错误的问题 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: genli
ms.openlocfilehash: 7e37d8e732408e70dbcdc86d3e21556f553506cd
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57768478"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>Windows 在启动 Azure VM 时显示蓝屏错误
本文介绍在 Microsoft Azure 中启动 Windows 虚拟机 (VM) 时可能出现的蓝屏错误。 本文提供用于收集支持票证数据的步骤。 

> [!NOTE] 
> Azure 具有用于创建和处理资源的两个不同部署模型：[资源管理器部署模型和经典部署模型](../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用 Resource Manager 部署模型。建议对新部署使用该模型，而不是经典部署模型。

## <a name="symptom"></a>症状 

Windows VM 不启动。 检查[启动诊断](./boot-diagnostics.md)中的启动屏幕截图时，在蓝色屏幕中看到以下错误消息之一：

- 你的电脑遇到问题，需要重启。 我们将收集一些错误信息，然后你就可以重启。
- 你的电脑遇到问题，需要重启。

本部分列出管理 VM 时可能遇到的常见错误消息：

## <a name="cause"></a>原因

收到停止错误可能有多个原因。 最常见原因是：

- 驱动程序存在问题
- 系统文件或内存损坏
- 应用程序访问内存的禁止扇区

## <a name="collect-memory-dump-file"></a>收集内存转储文件

若要解决此问题，需先收集故障转储文件，然后使用此转储文件联系支持部门。 若要收集转储文件，请执行以下步骤：

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>将 OS 磁盘附加到恢复 VM

1. 拍摄受影响的 VM 的 OS 磁盘的快照作为备份。 有关详细信息，请参阅[拍摄磁盘快照](../windows/snapshot-copy-managed-disk.md)。
2. [将 OS 磁盘附加到恢复 VM](../windows/troubleshoot-recovery-disks-portal.md)。 
3. 通过远程桌面连接到恢复 VM。

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>找到转储文件并提交支持票证

1. 在恢复 VM 上，转到附加的 OS 磁盘中的 Windows 文件夹。 如果分配给附加 OS 磁盘的驱动器号为 F，则需转到 F:\Windows。
2. 找到 memory.dmp 文件，然后使用该转储文件[提交支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 

如果找不到转储文件，请转到下一步以启用转储日志和串行控制台。

### <a name="enable-dump-log-and-serial-console"></a>启用转储日志和串行控制台

若要启用转储日志和串行控制台，请运行以下脚本。

1. 打开提升的命令提示符会话（以管理员身份运行）。
2. 运行以下脚本：

    在此脚本中，假定分配给附加 OS 磁盘的驱动器号为 F。将其替换为 VM 中的相应值。

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

    1. 请确保磁盘上有足够的空间来分配与 RAM 一样多的内存，具体取决于为此 VM 选择的大小。
    2. 如果没有足够的空间，或者这是大型 VM（G、GS 或 E 系列），则可随后更改创建此文件时所在的位置，将该位置指向任何其他附加到 VM 的数据磁盘。 为此，需更改以下密钥：

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [分离 OS 磁盘，然后将 OS 磁盘重新附加到受影响的 VM](../windows/troubleshoot-recovery-disks-portal.md)。
4. 启动 VM 以重现问题，然后就会生成转储文件。
5. 将 OS 磁盘附加到恢复 VM，收集转储文件，然后使用该转储文件[提交支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。



