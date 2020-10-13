---
title: 与 Azure 虚拟机建立 RDP 连接时发生内部错误 | Microsoft Docs
description: 了解如何排查 Microsoft Azure 中的 RDP 内部错误 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: d4736515d8e011ce1e72c51ecb3ce1cfd2e1e1cd
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978520"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>尝试通过远程桌面连接到 Azure VM 时发生内部错误

本文描述在 Microsoft Azure 中尝试连接到虚拟机 (VM) 时可能遇到的错误。


## <a name="symptoms"></a>症状

无法使用远程桌面协议 (RDP) 连接到 Azure VM。 连接停滞在 " **配置远程** " 部分，或者您收到以下错误消息：

- RDP 内部错误
- 发生了内部错误
- 此计算机无法连接到远程计算机。 请再次尝试连接。 如果问题持续出现，请与远程计算机的所有者或网络管理员联系。


## <a name="cause"></a>原因

此问题可能是由以下原因引起的：

- 虚拟机可能已遭到攻击。
- 不能访问本地 RSA 加密密钥。
- 已禁用 TLS 协议。
- 证书已损坏或过期。

## <a name="solution"></a>解决方案

若要解决此问题，请完成以下部分中的步骤。 在开始之前，请将受影响 VM 的 OS 磁盘作为备份快照。 有关详细信息，请参阅[拍摄磁盘快照](../windows/snapshot-copy-managed-disk.md)。

### <a name="check-rdp-security"></a>检查 RDP 安全性

首先，查看 RDP 端口3389的网络安全组是否不安全 (打开) 。 如果它不安全，并显示 \* 为入站的源 IP 地址，则将 rdp 端口限制为特定用户的 ip 地址，然后测试 rdp 访问。 如果此操作失败，请完成下一部分中的步骤。

### <a name="use-serial-control"></a>使用串行控制台

使用串行控制台或通过将 VM 的 OS 磁盘附加到恢复 VM 来 [脱机修复 vm](#repair-the-vm-offline) 。

若要开始，请连接到 [串行控制台并打开 PowerShell 实例](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
)。 如果 VM 上未启用串行控制台，请转到[修复 VM 脱机](#repair-the-vm-offline)部分。

#### <a name="step-1-check-the-rdp-port"></a>步骤 1：检查 RDP 端口

1. 在 PowerShell 实例中，使用 [NETSTAT](/windows-server/administration/windows-commands/netstat) 检查端口3389是否由其他应用程序使用：

    ```powershell
    Netstat -anob |more
    ```

2. 如果 Termservice.exe 使用3389端口，请跳到步骤2。 如果 Termservice.exe 的其他服务或应用程序使用3389端口，请执行以下步骤：

    1. 停止正在使用 3389 服务的应用程序的服务：

        ```powershell
        Stop-Service -Name <ServiceName> -Force
        ```

    2. 启动终端服务：

        ```powershell
        Start-Service -Name Termservice
        ```

2. 如果无法停止该应用程序或者此方法不适用，请更改 RDP 的端口：

    1. 更改端口：

        ```powershell
        Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

        Stop-Service -Name Termservice -Force

        Start-Service -Name Termservice
        ```

    2. 设置新端口的防火墙：

        ```powershell
        Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>
        ```

    3. 在 Azure 门户的“RDP 端口”中[更新新端口的网络安全组](../../virtual-network/network-security-groups-overview.md)。

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>步骤 2：设置对 RDP 自签名证书的正确权限

1. 在 PowerShell 实例中逐条运行以下命令，以续订 RDP 自签名证书：

    ```powershell
    Import-Module PKI

    Set-Location Cert:\LocalMachine 

    $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 

    Remove-Item -Path $RdpCertThumbprint

    Stop-Service -Name "SessionEnv"

    Start-Service -Name "SessionEnv"
    ```

2. 如果无法使用此方法续订证书，请尝试远程续订 RDP 自签名证书：

    1. 在已连接到有问题 VM 的正常 VM 上，在“运行”框中键入 **mmc** 打开 Microsoft 管理控制台。****
    2. 在“文件”菜单中，依次选择“添加/删除管理单元”、“证书”、“添加”。****************
    3. 依次选择“计算机帐户”、“另一台计算机”，然后添加有问题 VM 的 IP 地址。********
    4. 转到“远程桌面\证书”文件夹，右键单击证书，然后选择“删除”********。
    5. 在串行控制台上的 PowerShell 实例中，重启“远程桌面配置”服务：

        ```powershell
        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"
        ```

3. 重置 MachineKeys 文件夹的权限。

    ```powershell
    remove-module psreadline icacls

    md c:\temp

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 

    takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 

    Restart-Service TermService -Force
    ```

4. 重启 VM，然后尝试开始与 VM 建立远程桌面连接。 如果仍然出错，请转到下一步。

#### <a name="step-3-enable-all-supported-tls-versions"></a>步骤 3：启用所有受支持的 TLS 版本

RDP 客户端使用 TLS 1.0 作为默认协议。 但是，可将此协议更改为新标准协议 TLS 1.1。 如果在 VM 上禁用了 TLS 1.1，则连接将会失败。

1. 在 CMD 实例中启用 TLS 协议：

    ```console
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
    ```

2. 为了防止 AD 策略覆盖所做的更改，请暂时停止组策略更新：

    ```console
    REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
    ```

3. 重启 VM，使更改生效。 如果解决了问题，请运行以下命令重新启用组策略：

    ```console
    sc config gpsvc start= auto sc start gpsvc

    gpupdate /force
    ```

    如果更改已还原，则表示公司域中存在 Active Directory 策略。 必须更改该策略才能避免此问题再次发生。

### <a name="repair-the-vm-offline"></a>修复 VM 脱机

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>将 OS 磁盘附加到恢复 VM

1. [将 OS 磁盘附加到恢复 VM](./troubleshoot-recovery-disks-portal-windows.md)。
2. 将 OS 磁盘附加到恢复 VM 后，请确保磁盘在磁盘管理控制台中标记为“联机”  。 请注意分配给附加的 OS 磁盘的驱动器号。
3. 开始与恢复 VM 建立远程桌面连接。

#### <a name="enable-dump-log-and-serial-console"></a>启用转储日志和串行控制台

若要启用转储日志和串行控制台，请运行以下脚本。

1. 打开权限提升的命令提示符会话（“以管理员身份运行”）。 
2. 运行以下脚本：

    对于此脚本，我们假设分配给附加 OS 磁盘的驱动器号为 F。请将此驱动器号替换为 VM 中的相应值。

    ```console
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

#### <a name="reset-the-permission-for-machinekeys-folder"></a>重置 MachineKeys 文件夹的权限

1. 打开权限提升的命令提示符会话（“以管理员身份运行”）。 
2. 运行以下脚本。 对于此脚本，我们假设分配给附加 OS 磁盘的驱动器号为 F。请将此驱动器号替换为 VM 中的相应值。

    ```console
    Md F:\temp

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt

    takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
    ```

#### <a name="enable-all-supported-tls-versions"></a>启用所有受支持的 TLS 版本

1. 打开权限提升的命令提示符会话（“以管理员身份运行”），然后运行以下命令。  以下脚本假设分配给附加 OS 磁盘的驱动器号为 F。请将此驱动器号替换为 VM 中的相应值。
2. 检查启用了哪个 TLS：

    ```console
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO
    ```

3. 如果该密钥不存在或者其值为 **0**，请运行以下脚本来启用该协议：

    ```console
    REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
    ```

4. 启用 NLA：

    ```console
    REM Enable NLA

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
    ```

5. [拆离 OS 磁盘并重新创建 VM](./troubleshoot-recovery-disks-portal-windows.md)，然后检查问题是否得以解决。