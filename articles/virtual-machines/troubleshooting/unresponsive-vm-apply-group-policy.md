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
ms.openlocfilehash: 187098f557cb691e023abb282a265b11e975c544
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84629263"
---
# <a name="vm-is-unresponsive-when-applying-group-policy-local-users-and-groups-policy"></a>应用组策略本地用户和组策略时，VM 无响应

本文提供了一些步骤，用于解决在启动过程中，Azure 虚拟机（VM）应用策略时，加载屏幕不响应的问题。

## <a name="symptoms"></a>症状

使用 "[启动诊断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)" 查看 VM 的屏幕截图时，屏幕会停滞，并显示以下消息： "应用组策略本地用户和组策略"。

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="显示正在应用和加载组策略本地用户的屏幕截图 (Windows Server 2012 R2)。":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="显示正在应用和加载组策略本地用户的屏幕截图 (Windows Server 2012)。":::

## <a name="cause"></a>原因

如果策略尝试清理旧的用户配置文件，则会发生冲突的锁定。

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
> 如果遇到此启动错误，来宾操作系统将无法正常运行。 必须在脱机模式下进行故障排除。

### <a name="step-1-create-and-access-a-repair-vm"></a>步骤 1：创建和访问修复 VM

1. 使用 [VM 修复命令的步骤 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 来准备一个修复 VM。
2. 使用远程桌面连接连接到修复 VM。

### <a name="step-2-disable-the-policy"></a>步骤 2：禁用策略

1. 在修复 VM 上，打开“注册表编辑器”。
1. 找到**HKEY_LOCAL_MACHINE**的密钥，然后从菜单中选择 "**文件**"  >  "**加载配置单元**"。

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="屏幕截图，显示了突出显示的 HKEY_LOCAL_MACHINE 和包含“加载配置单元”的菜单。":::

    - 可以使用 Load Hive 从脱机系统加载注册表项。 在这种情况下，系统是附加到修复 VM 的损坏磁盘。
    - 系统范围内的设置存储在上 `HKEY_LOCAL_MACHINE` ，可以缩写为 "HKLM"。
1. 在附加的磁盘中，转到 `\windows\system32\config\SOFTWARE` 文件并将其打开。

    1. 当系统提示你输入名称时，请输入 "BROKENSOFTWARE"。
    1. 若要验证是否已加载 BROKENSOFTWARE，展开“HKEY_LOCAL_MACHINE”并查找已添加的 BROKENSOFTWARE 项。
1. 请参阅 "BROKENSOFTWARE"，并检查 CleanupProfile 项是否存在于已加载的配置单元中。

    1. 如果该注册表项存在，则会设置 CleanupProfile 策略。 它的值表示以天为单位的保留策略。 继续删除该项。
    1. 如果该项不存在，说明未设置 CleanupProfile 策略。 [提交支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，包括位于附加的 OS 磁盘的 Windows 目录中的内存 .dmp 文件。

1. 使用以下命令删除 CleanupProfiles 项：

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
1.  使用以下命令卸载 "BROKENSOFTWARE" hive：

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>步骤3：启用串行控制台和内存转储收集

若要启用内存转储收集和串行控制台，请运行以下脚本：

1. 打开提升的命令提示符会话。 （以管理员身份运行。）
1. 运行以下命令以启用串行控制台：
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
1. 验证 OS 磁盘上的可用空间是否至少等于 VM 的内存大小（RAM）。

    如果 OS 磁盘上没有足够的空间，请更改内存转储位置，并将其引用到具有足够可用空间的附加数据磁盘。 若要更改位置，请在以下命令中将 "% SystemRoot%" 替换为数据磁盘的驱动器号（例如，"F："）。

    **启用 OS 转储的建议配置**

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

如果问题得到解决，则策略现在已在本地禁用。 对于永久解决方案，不要使用 Vm 上的 CleanupProfiles 策略。 使用其他方法执行配置文件清理。

请勿使用此策略：

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>后续步骤

如果在应用 Windows 更新时遇到问题，请参阅[应用 Windows 更新时，VM 未响应 "C01A001D" 错误](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/unresponsive-vm-apply-windows-update)。
