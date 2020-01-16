---
title: 启动 Azure VM 时关键服务失败 | Microsoft Docs
description: 了解如何解决在启动时出现的“0x0000005A 关键服务失败”错误 |Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: 590505d954d52ebec9f8a5c344d6e750f11ef677
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981368"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>启动 Azure VM 时 Windows 在蓝屏上显示“关键服务失败”
本文介绍了在 Microsoft Azure 中启动 Windows 虚拟机 (VM) 时可能会遇到的“关键服务失败”错误。 它提供了故障排除步骤来帮助解决问题。 

> [!NOTE] 
> Azure 具有用于创建和处理资源的两个不同的部署模型： [Resource Manager 和经典](../../azure-resource-manager/management/deployment-models.md)。 本文介绍如何使用 Resource Manager 部署模型。建议对新部署使用该模型，而不是经典部署模型。

## <a name="symptom"></a>症状 

Windows VM 不启动。 在[启动诊断](./boot-diagnostics.md)中检查启动屏幕截图时，在蓝色屏幕中看到以下错误消息之一：

- “你的电脑遇到问题，需要重启。 你可以重启。 有关此问题和可能的修复项的详细信息，请访问 https://windows.com/stopcode 。 如果你呼叫支持人员，请向其提供以下信息: 停止代码: 关键服务失败” 
- “你的电脑遇到问题，需要重启。 我们将收集一些错误信息，然后我们将为你重启。 如果想了解更多信息，稍后可以联机搜索以下错误: CRITICAL_SERVICE_FAILED”

## <a name="cause"></a>原因

有多种原因会导致产生停止错误。 最常见原因是：
- 驱动程序存在问题
- 系统文件或内存损坏
- 应用程序访问了内存的禁止扇区

## <a name="solution"></a>解决方案 

若要解决此问题，请[联系支持人员并提交一个转储文件](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file)（这有助于我们更快地诊断问题），或者尝试以下自助解决方案。

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>将 OS 磁盘附加到恢复 VM

1. 拍摄受影响的 VM 的 OS 磁盘的快照作为备份。 有关详细信息，请参阅[拍摄磁盘快照](../windows/snapshot-copy-managed-disk.md)。
2. [将 OS 磁盘附加到恢复 VM](./troubleshoot-recovery-disks-portal-windows.md)。 
3. 建立到恢复 VM 的远程桌面连接。

### <a name="enable-dump-logs-and-serial-console"></a>启用转储日志和串行控制台

转储日志和[串行控制台](./serial-console-windows.md)可帮助我们进一步进行故障排除。

若要启用转储日志和串行控制台，请运行以下脚本。

1. 打开提升的命令提示符会话（以管理员身份运行）。
2. 运行以下脚本：

    在此脚本中，我们假定分配给附加 OS 磁盘的驱动器号为 F。你应将其替换为适用于你的 VM 的相应值。

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 10
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

### <a name="replace-the-unsigned-drivers"></a>替换未签名的驱动程序

1. 在恢复 VM 上，在提升的命令提示符下运行以下命令。 此命令将受影响的 OS 磁盘设置为在下次启动时以安全模式启动：

        bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal

    例如，如果你附加的 OS 磁盘是驱动器 F，则运行以下命令：

        bcdedit /store F: boot\bcd /set {default} safeboot minimal

2. [分离 OS 磁盘，然后将 OS 磁盘重新附加到受影响的 VM](troubleshoot-recovery-disks-portal-windows.md)。 VM 将以安全模式启动。 如果仍然遇到错误，请转到可选步骤。
3. 打开“运行”框，运行 **verifier** 来启动驱动程序验证程序管理器工具。
4. 选择“自动选择未经签名的驱动程序”，然后单击“下一步”。
5. 此时将显示未经签名的驱动程序文件的列表。 请记住这些文件名。
6. 从正常工作的 VM 复制这些文件的相同版本，然后替换这些未签名的文件。 

7. 删除安全启动设置：

        bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
8.  重启 VM。 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>可选：在故障转储模式下分析转储日志

若要自行分析转储日志，请执行以下步骤：

1. 将 OS 磁盘附加到恢复 VM。
2. 在附加的 OS 磁盘上，浏览到 **\windows\system32\config**。如果需要回滚，请将所有文件复制为备份。
3. 启动**注册表编辑器** (regedit.exe)。
4. 选择“HKEY_LOCAL_MACHINE”项。 在菜单上，选择“文件” > “加载配置单元”。
5. 浏览到已附加 OS 磁盘上的 **\windows\system32\config\SYSTEM** 文件夹。 输入“BROKENSYSTEM”作为配置单元名称。 新的注册表配置单元将显示在“HKEY_LOCAL_MACHINE”项之下。
6. 浏览到 **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl** 并进行以下更改：

    Autoreboot = 0

    CrashDumpEnabled = 2
7.  选择“BROKENSYSTEM”。 在菜单上，选择“文件” > “卸载配置单元”
8.  修改 BCD 设置以在调试模式下启动。 在提升的命令提示符下运行以下命令：

    ```cmd
    REM Setup some debugging flags on the boot manager
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} default {<IDENTIFIER OF THE WINDOWS BOOT LOADER>}
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices disable

    REM Setup some debugging flags on the boot loader
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootlog yes
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy displayallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} testsigning off
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservices disable
    ```
9. [分离 OS 磁盘，然后将 OS 磁盘重新附加到受影响的 VM](troubleshoot-recovery-disks-portal-windows.md)。
10. 启动 VM 以查看它是否显示了转储分析。 找到无法加载的文件。 需要使用正常工作的 VM 中的文件替换此文件。 

    下面是转储分析的示例。 可以看到 **FAILURE** 在 filecrypt.sys 中：“FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys”。

    ```
    kd> !analyze -v 

    ******************************************************************************* 
    * * * Bugcheck Analysis * * * 

    ******************************************************************************* 
    CRITICAL_SERVICE_FAILED (5a) Arguments: Arg1: 0000000000000001 Arg2: ffffd80f4bfe7070 Arg3: ffffb00b0513d320 Arg4: ffffffffc0000428 Debugging 
    Details: ------------------ 
    DUMP_CLASS: 1 DUMP_QUALIFIER: 401 BUILD_VERSION_STRING: 10.0.14393.1770 (rs1_release.170917-1700) 
    DUMP_TYPE: 1 BUGCHECK_P1: 1 BUGCHECK_P2: ffffd80f4bfe7070 BUGCHECK_P3: ffffb00b0513d320 BUGCHECK_P4: ffffffffc0000428 BUGCHECK_STR: 0x5A_c0000428 
    CPU_COUNT: 1 CPU_MHZ: a22 CPU_VENDOR: GenuineIntel CPU_FAMILY: 6 CPU_MODEL: 3d CPU_STEPPING: 4 DEFAULT_BUCKET_ID: WIN8_DRIVER_FAULT 
    PROCESS_NAME: System CURRENT_IRQL: 0 ANALYSIS_SESSION_HOST: MININT-6RMM091 ANALYSIS_SESSION_TIME: 11-15-2017 19:32:42.0841 
    ANALYSIS_VERSION: 10.0.16361.1001 amd64fre STACK_TEXT: ffffc701`1dc74948 fffff803`b2ff4b4a : 00000000`0000005a 00000000`00000001 ffffd80f`4bfe7070 ffffb00b`0513d320 : nt!KeBugCheckEx [d:\rs1\minkernel\ntos\ke\amd64\procstat.asm @ 127] ffffc701`1dc74950 fffff803`b3205df3 : ffffd80f`4bba9f58 ffffd80f`4bba9f58 ffffc701`1dc74b80 ffffd80f`00000006 : nt!IopLoadDriver+0x19f8e6 [d:\rs1\minkernel\ntos\ke\amd64\threadbg.asm @ 81] 
    RETRACER_ANALYSIS_TAG_STATUS: DEBUG_FLR_FAULTING_IP is not found THREAD_SHA1_HASH_MOD_FUNC: eb79608c07faa1af62c0e61f25ff6bc1d6dfdb25 THREAD_SHA1_HASH_MOD_FUNC_OFFSET: 96a3a314834bb4e8443a8b7201525fc5dfc1878b THREAD_SHA1_HASH_MOD: 30a3e915496deaace47137d5b90c3ecc03746bf6 FOLLOWUP_NAME: wintriag
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: https://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. 在 VM 正常工作并启动后，删除故障转储设置：

    ```cmd
    REM Restore the boot manager to default values
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {bootmgr} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices enable

    REM Restore the boot loader to default values for an azure VM
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} bootlog
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy ignoreallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} testsigning
    bcddit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservicesenable
    ```
