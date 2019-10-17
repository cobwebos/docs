---
title: VM 启动时停滞，并在 Azure 中显示“正在准备 Windows。 请不要关闭计算机”| Microsoft Docs
description: 介绍了解决以下问题的步骤：VM 启动时停滞并显示“正在准备 Windows。 请不要关闭计算机。”
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: 4263afe33caa4d6471848c8e7dbf9bc1eeec4bee
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332534"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>VM 启动时停滞，并在 Azure 中显示“正在准备 Windows。 请不要关闭计算机”

本文介绍了在 Microsoft Azure 中启动 Windows 虚拟机（VM）时可能会遇到的 "准备就绪" 和 "获取 Windows 就绪" 屏幕。 本文提供用于收集支持票证数据的步骤。

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptoms"></a>症状

Windows VM 无法启动。 使用**启动诊断**获取 VM 的屏幕截图时，可能会看到 vm 显示 "准备就绪" 或 "正在获取 Windows 就绪" 消息。

![Windows Server 2012 R2 的消息示例](./media/troubleshoot-vm-configure-update-boot/message1.png)

![消息示例](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>原因

当服务器在配置发生更改后执行最终重新启动时，通常会发生此问题。 配置更改可能通过 Windows 更新或通过更改服务器的角色/功能进行初始化。 对于 Windows 更新，如果更新的大小较大，则操作系统将需要更多时间来重新配置更改。

## <a name="collect-an-os-memory-dump"></a>收集 OS 内存转储

如果在等待更改处理后无法解决该问题，则需要收集内存转储文件并与支持人员联系。 若要收集转储文件，请执行以下步骤：

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>将 OS 磁盘附加到恢复 VM

1. 拍摄受影响的 VM 的 OS 磁盘的快照作为备份。 有关详细信息，请参阅[拍摄磁盘快照](../windows/snapshot-copy-managed-disk.md)。
2. [将 OS 磁盘附加到恢复 VM](../windows/troubleshoot-recovery-disks-portal.md)。
3. 通过远程桌面连接到恢复 VM。 
4. 如果 OS 磁盘已加密，则必须在转到下一步之前关闭加密。 有关详细信息，请参阅[在无法启动的 VM 中解密已加密的 OS 磁盘](troubleshoot-bitlocker-boot-error.md#solution)。

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>找到转储文件并提交支持票证

1. 在恢复 VM 上，转到附加的 OS 磁盘中的 Windows 文件夹。 如果分配给附加 OS 磁盘的驱动器号为 F，则需转到 F:\Windows。
2. 找到内存 dmp 文件，然后使用转储文件[提交支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 

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
4. 启动 VM 并访问串行控制台。
5. 选择 "**发送不可屏蔽中断（NMI）** " 以触发内存转储。
    @no__t 0the 的映像，以了解在何处发送不可屏蔽中断 @ no__t-1
6. 再次将 OS 磁盘附加到恢复 VM，收集转储文件。

## <a name="contact-microsoft-support"></a>请与 Microsoft 支持部门联系

收集转储文件后，请联系 [Microsoft 支持部门](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)来分析根本原因。