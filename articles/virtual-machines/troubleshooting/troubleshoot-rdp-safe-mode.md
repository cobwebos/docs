---
title: 由于 VM 启动到安全模式，因此无法远程连接 Azure 虚拟机 | Microsoft Docs
description: 了解如何解决由于 VM 启动到安全模式而无法对虚拟机进行 RDP 的问题 | Microsoft Docs
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
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 3ff1db9ee7dc34ce529702d61b3ac5970bb5d9df
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2018
ms.locfileid: "52309857"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>由于 VM 启动到安全模式，因此无法对 VM 进行 RDP

本文展示了如何解决由于将 Azure Windows 虚拟机 (VM) 配置为启动到安全模式而导致无法连接到该 VM 的问题。

> [!NOTE] 
> Azure 具有用于创建和处理资源的两个不同的部署模型： [Resource Manager 和经典](../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用资源管理器部署模型。建议对新部署使用该模型，而不要使用经典部署模型。 

## <a name="symptoms"></a>症状 

不能与 Azure 中的 VM 建立 RDP 连接或其他连接（如 HTTP），因为已将该 VM 配置为启动到安全模式。 在 Azure 门户的[启动诊断](../troubleshooting/boot-diagnostics.md)中检查屏幕截图时，可能会看到 VM 启动正常，但网络接口不可用：

![安全模式下网络接口的图像](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>原因

RDP 服务在安全模式下不可用。 VM 启动到安全模式时，只会加载必要的系统程序和服务。 这适用于两种不同版本的安全模式，即“最小化安全启动”和“连接安全启动”。


## <a name="solution"></a>解决方案 

在执行这些步骤之前，请创建受影响 VM 的 OS 磁盘的快照作为备份。 有关详细信息，请参阅[拍摄磁盘快照](../windows/snapshot-copy-managed-disk.md)。

若要解决此问题，请使用串行控制台将 VM 配置为启动到正常模式，或使用恢复 VM [脱机修复 VM](#repair-the-vm-offline)。

### <a name="use-serial-control"></a>使用串行控制台

1. 连接到[串行控制台并打开 CMD 实例](./serial-console-windows.md#open-cmd-or-powershell-in-serial-console
)。 如果 VM 上未启用串行控制台，请参阅[脱机修复 VM](#repair-the-vm-offline)。
2. 检查启动配置数据： 

        bcdedit /enum

    如果 VM 配置为启动到安全模式，则可在“Windows 启动加载程序”部分下看到一个名为“安全启动”的额外标志。 如果未看到“安全启动”标志，则 VM 未处于安全模式。 本文不适用于这种情况。

    “安全启动”标志可能显示有以下值：
    - 轻微
    - 网络

    在这两种模式下，RDP 都不会启动。 因此，修补程序保持不变。

    ![“安全模式”标志相关图像](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. 删除“安全模式”标志，使 VM 启动到正常模式：

        bcdedit /deletevalue {current} safeboot
        
4. 检查启动配置数据，确保删除“安全启动”标志：

        bcdedit /enum

5. 重启 VM，然后检查问题是否已解决。

### <a name="repair-the-vm-offline"></a>修复 VM 脱机

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>将 OS 磁盘附加到恢复 VM

1. [将 OS 磁盘附加到恢复 VM](../windows/troubleshoot-recovery-disks-portal.md)。
2. 开始与恢复 VM 建立远程桌面连接。 
3. 确保磁盘在磁盘管理控制台中标记为“联机”。 请注意分配给附加的 OS 磁盘的驱动器号。

#### <a name="enable-dump-log-and-serial-console-optional"></a>启用转储日志和串行控制台（可选）

如果本文中的解决方案无法解决问题，转储日志和串行控制台可帮助我们执行进一步的故障排除。

若要启用转储日志和串行控制台，请运行以下脚本。

1. 打开权限提升的命令提示符会话（“以管理员身份运行”）。
2. 运行以下脚本：

    对于此脚本，我们假设分配给附加 OS 磁盘的驱动器号为 F。请将此驱动器号替换为 VM 中的相应值。

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

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

#### Configure the Windows to boot into normal mode

1. Open an elevated command prompt session (**Run as administrator**).
2. Check the boot configuration data. In the following commands, we assume that the drive letter that is assigned to the attached OS disk is F. Replace this drive letter with the appropriate value for your VM. 

        bcdedit /store F:\boot\bcd /enum
    Take note of the Identifier name of the partition that has the **\windows** folder. By default, the  Identifier name is "Default".  

    If the VM is configured to boot into Safe Mode, you will see an extra flag under the **Windows Boot Loader** section called **safeboot**. If you do not see the **safeboot** flag, this article does not apply to your scenario.

    ![The image about boot Identifier](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Remove the **safeboot** flag, so the VM will boot into normal mode:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Check the boot configuration data to make sure that the **safeboot** flag is removed:

        bcdedit /store F:\boot\bcd /enum
5. [Detach the OS disk and recreate the VM](../windows/troubleshoot-recovery-disks-portal.md). Then check whether the issue is resolved.
