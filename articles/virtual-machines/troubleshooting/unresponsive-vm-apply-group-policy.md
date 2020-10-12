---
title: Azure 虚拟机在应用策略时无响应
description: 本文提供了一些步骤，用于解决在 Azure VM 中启动过程中应用策略时加载屏幕不响应的问题。
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5628
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.author: v-mibufo
ms.openlocfilehash: cbf2fe491e1fe0b553eab04ca7190da0413a3ba6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526004"
---
# <a name="vm-is-unresponsive-when-applying-group-policy-local-users-and-groups-policy"></a>应用“组策略本地用户和组”策略时，VM 无响应

本文提供了一些步骤，用于解决 Azure 虚拟机 (VM) 在启动过程中应用策略时加载屏幕不响应的问题。

## <a name="symptoms"></a>症状

使用[启动诊断](./boot-diagnostics.md)查看 VM 的屏幕截图时，屏幕在加载时会卡住并显示以下消息：“正在应用‘组策略本地用户和组’策略。”

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="显示正在应用和加载组策略本地用户的屏幕截图 (Windows Server 2012 R2)。":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="显示正在应用和加载组策略本地用户的屏幕截图 (Windows Server 2012 R2)。":::

## <a name="cause"></a>原因

当策略尝试清除旧的用户配置文件时，会发生锁定冲突。

> [!NOTE]
> 这仅针对于 Windows Server 2012 和 Windows Server 2012 R2。

下面是有问题的策略：

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>解决方法

### <a name="process-overview"></a>过程概述

1. [创建和访问修复 VM](#step-1-create-and-access-a-repair-vm)
1. [禁用策略](#step-2-disable-the-policy)
1. [启用串行控制台和内存转储收集](#step-3-enable-serial-console-and-memory-dump-collection)
1. [重新生成 VM](#step-4-rebuild-the-vm)

> [!NOTE]
> 如果遇到此启动错误时，来宾 OS 无法运行。 必须在脱机模式下进行故障排除。

### <a name="step-1-create-and-access-a-repair-vm"></a>步骤 1：创建和访问修复 VM

1. 使用 [VM 修复命令的步骤 1-3](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 来准备一个修复 VM。
2. 使用远程桌面连接来连接到修复 VM。

### <a name="step-2-disable-the-policy"></a>步骤 2：禁用策略

1. 在修复 VM 上，打开“注册表编辑器”。
1. 找到“HKEY_LOCAL_MACHINE”项，然后从菜单中选择“文件” > “加载配置单元”  。

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="显示正在应用和加载组策略本地用户的屏幕截图 (Windows Server 2012 R2)。" /v CleanupProfiles /f
    ```
1.  使用以下命令卸载 BROKENSOFTWARE 配置单元：

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>步骤 3：启用串行控制台和内存转储收集

若要启用内存转储收集和串行控制台，请运行以下脚本：

1. 打开提升的命令提示符会话。 （以管理员身份运行。）
1. 运行以下命令启用串行控制台：
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
1. 验证 OS 磁盘上的可用空间是否至少等于 VM 的内存大小 (RAM)。

    如果 OS 磁盘上的空间不足，请更改内存转储位置，并将其引用到具有足够可用空间的附加数据磁盘。 若要更改位置，请将以下命令中的“%SystemRoot%”替换为数据磁盘的驱动器号（例如“F:”）。

    用于启用 OS 转储的建议配置

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

### <a name="step-4-rebuild-the-vm"></a>步骤 4：重建 VM

使用 [VM 修复命令的步骤 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 重新装配 VM。

如果问题得到解决，说明已在本地禁用策略。 对于永久性解决方案，请勿在 VM 上使用 CleanupProfiles 策略。 使用其他方法执行配置文件清理。

请勿使用此策略：

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>后续步骤

如果在应用 Windows 更新时遇到问题，请参阅 [VM 在应用 Windows 更新时无响应并收到“C01A001D”错误](./unresponsive-vm-apply-windows-update.md)。
