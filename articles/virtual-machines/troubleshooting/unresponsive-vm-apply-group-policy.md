---
title: Azure 虚拟机在应用策略时无响应
description: 本文提供了一些步骤，用于解决在 Azure VM 中的启动过程中应用策略时加载屏幕卡住的问题。
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
ms.openlocfilehash: 30f833bc49f92dcabfc75f0a1507c6f540bdea24
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83748726"
---
# <a name="vm-becomes-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>VM 在应用“组策略本地用户和组”策略时无响应

本文提供了一些步骤，用于解决在 Azure VM 中启动过程中应用策略时加载屏幕卡住的问题。

## <a name="symptoms"></a>症状

使用[启动诊断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)查看 VM 的屏幕截图时，屏幕在加载时会卡住并显示以下消息：“应用组策略本地用户和组策略”。

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="显示正在应用和加载组策略本地用户的屏幕截图 (Windows Server 2012 R2)。":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="显示正在应用和加载组策略本地用户的屏幕截图 (Windows Server 2012)。":::

## <a name="cause"></a>原因

当策略尝试清除旧的用户配置文件时，会发生锁定冲突。

> [!NOTE]
> 这仅针对于 Windows Server 2012 和 Windows Server 2012 R2。

下面是有问题的策略：

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>解决方法

### <a name="process-overview"></a>过程概述

1. [创建和访问修复 VM](#step-1-create-and-access-a-repair-vm)
2. [禁用策略](#step-2-disable-the-policy)
3. [启用串行控制台和内存转储集合](#step-3-enable-serial-console-and-memory-dump-collection)
4. [重新生成 VM](#step-4-rebuild-the-vm)

> [!NOTE]
> 如果遇到此启动错误时，来宾 OS 无法运行。 必须在脱机模式下进行故障排除。

### <a name="step-1-create-and-access-a-repair-vm"></a>步骤 1：创建和访问修复 VM

1. 使用 [VM 修复命令的步骤 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 来准备一个修复 VM。
2. 使用远程桌面连接来连接到修复 VM。

### <a name="step-2-disable-the-policy"></a>步骤 2：禁用策略

1. 在修复 VM 上，打开“注册表编辑器”。
2. 找到“HKEY_LOCAL_MACHINE”项，然后从菜单中选择“文件” > “加载配置单元...”  。

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="屏幕截图，显示了突出显示的 HKEY_LOCAL_MACHINE 和包含“加载配置单元”的菜单。":::

    - 通过“加载配置单元”，可以从某个脱机系统中加载注册表项，在本例中，该系统是附加到修复 VM 的损坏的磁盘。
    - 系统范围内的设置存储在 `HKEY_LOCAL_MACHINE` 上，可以缩写为“HKLM”。
3. 在附加的磁盘中，转到 `\windows\system32\config\SOFTWARE` 文件并将其打开。

    1. 系统会提示你输入名称。 输入 BROKENSOFTWARE。<br/>
    2. 若要验证是否已加载 BROKENSOFTWARE，展开“HKEY_LOCAL_MACHINE”并查找已添加的 BROKENSOFTWARE 项。
4. 导航到 BROKENSOFTWARE，并检查加载的配置单元中是否有 CleanupProfile 项。

    1. 如果该项存在，说明已设置 CleanupProfile 策略，该项的值用天数来表示保留策略。 继续删除该项。<br/>
    2. 如果该项不存在，说明未设置 CleanupProfile 策略。 [提交支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，包括位于附加的 OS 磁盘的 Windows 目录中的内存 .dmp 文件。

5. 使用以下命令删除 CleanupProfiles 项：

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
6.  使用以下命令卸载 BROKENSOFTWARE 配置单元：

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>步骤 3：启用串行控制台和内存转储集合

若要启用内存转储集合和串行控制台，请运行以下脚本：

1. 打开提升的命令提示符会话（以管理员身份运行）。
2. 运行以下命令：

    **启用串行控制台**： 
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
3. 验证 OS 磁盘上的可用空间是否至少等于 VM 的内存大小 (RAM)。

    如果 OS 磁盘上的空间不足，请更改内存转储位置，并将其引用到具有足够可用空间的附加数据磁盘。 若要更改位置，请将“%SystemRoot%”替换为以下命令中的数据磁盘的驱动器号（如“F:”）。

    **用于启用 OS 转储的建议配置**：

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

### <a name="step-4-rebuild-the-vm"></a>步骤 4：重新生成 VM

使用 [VM 修复命令的步骤 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 重新装配 VM。

如果问题得到解决，说明已在本地禁用策略。 对于永久性解决方案，请勿在 VM 上使用 CleanupProfiles 策略。 使用其他方法执行配置文件清理。

请勿使用此策略：

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>后续步骤

如果在应用 Windows 更新时遇到问题，请参阅 [VM 在应用 Windows 更新时无响应并收到“C01A001D”错误](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/unresponsive-vm-apply-windows-update)。