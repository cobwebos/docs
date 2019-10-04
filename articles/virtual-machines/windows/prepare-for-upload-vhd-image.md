---
title: 准备好要上传到 Azure 的 Windows VHD | Microsoft Docs
description: 了解如何准备要上传到 Azure 的 Windows VHD 或 VHDX
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: 86ce2ada9ebd19c88414fab33a62dda5ba41ecb0
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949654"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>准备好要上传到 Azure 的 Windows VHD 或 VHDX

在将 Windows 虚拟机 (VM) 从本地上传到 Azure 之前，必须准备好虚拟硬盘（VHD 或 VHDX）。 Azure 支持采用 VHD 文件格式且具有固定大小磁盘的第 1 代和第 2 代 VM。 VHD 允许的最大大小为 1,023 GB。 

在第 1 代 VM 中，可将 VHDX 文件系统转换成 VHD。 还可以将动态扩展磁盘转换为固定大小的磁盘。 但无法更改 VM 的代次。 有关详细信息，请参阅[应在 Hyper-V 中创建第 1 代还是第 2 代 VM？](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)和 [Azure 对第 2 代 VM 的支持（预览版）](generation-2.md)。

有关 Azure VM 的支持策略的信息，请参阅 [Microsoft 服务器软件支持 Azure VM](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)。

> [!NOTE]
> 本文中的说明适用于：
>1. 64 位版本的 Windows Server 2008 R2 以及更高版本的 Windows Server 操作系统。 若要了解如何在 Azure 中运行 32 位操作系统，请参阅 [Azure VM 中的 32 位操作系统支持](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)。
>2. 如果将使用任何灾难恢复工具（如 Azure Site Recovery 或 Azure Migrate）来迁移工作负荷，则仍需要在来宾 OS上执行此过程以在迁移之前准备映像。

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>将虚拟磁盘转换为固定大小和 VHD

如果需要将虚拟磁盘转换为 Azure 所需的格式，请使用本部分所述的方法之一：

1. 在运行虚拟磁盘转换过程之前备份 VM。

1. 确保 Windows VHD 在本地服务器上正常工作。 在尝试转换磁盘或将其上传到 Azure 之前，请解决 VM 本身内部的所有错误。

1. 关于 VHD 的大小：

   1. Azure 上的所有 VHD 必须已将虚拟大小调整为 1MB。 从原始磁盘转换为 VHD 时，必须确保在转换前的原始磁盘大小是 1 MB 的倍数。 基于上传的 VHD 创建映像时，MB 的小数部分将导致错误。

   2. OS VHD 允许的最大大小为 2TB。


转换磁盘后，创建一个使用该磁盘的 VM。 启动并登录到 VM，以完成其上传准备工作。

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>使用 Hyper-V 管理器转换磁盘 
1. 打开 Hyper-V 管理器，在左侧选择本地计算机。 在计算机列表上方的菜单中，选择“操作” > “编辑磁盘”。
2. 在“查找虚拟硬盘”页上，选择你的虚拟磁盘。
3. 在“选择操作”页上选择“转换” > “下一步”。
4. 如果需要从 VHDX 进行转换，请选择“VHD” > “下一步”。
5. 如果需要从动态扩展磁盘进行转换，请选择“固定大小” > “下一步”。
6. 找到并选择新 VHD 文件的保存路径。
7. 选择“完成”。

> [!NOTE]
> 使用提升权限的 PowerShell 会话中运行本文中所述的命令。

### <a name="use-powershell-to-convert-the-disk"></a>使用 PowerShell 转换磁盘 
可以使用 Windows PowerShell 中的 [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) 命令转换虚拟磁盘。 启动 PowerShell 时，选择“以管理员身份运行”。 

以下示例命令将磁盘从 VHDX 转换为 VHD。 该命令还会将动态扩展磁盘转换为固定大小的磁盘。

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

在此命令中，请将 `-Path` 的值替换为要转换的虚拟硬盘的路径。 将 `-DestinationPath` 的值替换为已转换的磁盘的新路径和名称。

### <a name="convert-from-vmware-vmdk-disk-format"></a>从 VMware VMDK 磁盘格式转换
如果你的 Windows VM 映像采用 [VMDK 文件格式](https://en.wikipedia.org/wiki/VMDK)，请使用 [Microsoft 虚拟机转换器](https://www.microsoft.com/download/details.aspx?id=42497)将其转换为 VHD 格式。 有关详细信息，请参阅[如何将 VMware VMDK 转换为 Hyper-V VHD](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)。

## <a name="set-windows-configurations-for-azure"></a>设置 Azure 的 Windows 配置

在计划要上传到 Azure 的 VM 上，通过[提升权限的命令提示符窗口](https://technet.microsoft.com/library/cc947813.aspx)运行以下命令：

1. 删除路由表中的所有静态持久性路由：
   
   * 若要查看路由表，请在命令提示符处运行 `route print`。
   * 检查 `Persistence Routes` 节。 如果有持久性路由，请使用 `route delete` 命令将其删除。
2. 删除 WinHTTP 代理：
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    如果 VM 需要使用特定代理，请向 Azure IP 地址 ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)) 添加代理例外，使 VM 能够连接到 Azure：
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. 将磁盘 SAN 策略设置为 [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx)：
   
    ```PowerShell
    diskpart 
    ```
    在打开的命令提示符窗口中键入以下命令：

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. 为 Windows 设置协调世界时 (UTC) 时间。 此外，将 Windows 时间服务 (`w32time`) 的启动类型设置为 `Automatic`：
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -Name "RealTimeIsUniversal" -Value 1 -Type DWord -Force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. 将电源配置文件设置为“高性能”：

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. 确保将环境变量 `TEMP` 和 `TMP` 设为其默认值：

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
    ```

## <a name="check-the-windows-services"></a>查看 Windows 服务
确保下面的每个 Windows 服务均设置为 Windows 默认值。 最起码需要设置这些服务，才能确保 VM 能够建立连接。 若要重置启动设置，请运行以下命令：
   
```PowerShell
Set-Service -Name bfe -StartupType Automatic
Set-Service -Name dhcp -StartupType Automatic
Set-Service -Name dnscache -StartupType Automatic
Set-Service -Name IKEEXT -StartupType Automatic
Set-Service -Name iphlpsvc -StartupType Automatic
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Automatic
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Automatic
Set-Service -Name RemoteRegistry -StartupType Automatic
```

## <a name="update-remote-desktop-registry-settings"></a>更新远程桌面注册表设置
确保正确配置以下设置以进行远程访问：

>[!NOTE] 
>运行 `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <object name> -Value <value>` 时，可能会显示一条错误消息。 可以放心忽略此消息。 它的意思只是域未将该配置推送到组策略对象。

1. 已启用远程桌面协议 (RDP)：
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force
    ```
   
2. 已正确设置 RDP 端口。 默认端口为 3389：
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "PortNumber" -Value 3389 -Type DWord -Force
    ```
    部署 VM 时，默认规则是针对端口 3389 创建的。 若要更改端口号，请在 VM 部署到 Azure 以后再进行。

3. 侦听器在每个网络接口中侦听：
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "LanAdapter" -Value 0 -Type DWord -Force
   ```
4. 配置用于 RDP 连接的网络级身份验证 (NLA) 模式：
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "UserAuthentication" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SecurityLayer" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -Force
     ```

5. 设置 keep-alive 值：
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveEnable" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveInterval" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "KeepAliveTimeout" -Value 1 -Type DWord -Force
    ```
6. 重新连接：
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDisableAutoReconnect" -Value 0 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fInheritReconnectSame" -Value 1 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fReconnectSame" -Value 0 -Type DWord -Force
    ```
7. 限制并发连接数：
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "MaxInstanceCount" -Value 4294967295 -Type DWord -Force
    ```
8. 删除任何已绑定到 RDP 侦听器的自签名证书：
    
    ```PowerShell
    if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains "SSLCertificateSHA1Hash")
    {
        Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SSLCertificateSHA1Hash" -Force
    }
    ```
    此代码确保在部署 VM 时一开始就可以连接。 以后，可以在将 VM 部署到 Azure 中后进行此项检查。

9. 如果 VM 会成为域的一部分，请检查以下策略，确保未还原以前的设置。 
    
    | 目标                                     | 策略                                                                                                                                                       | ReplTest1                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP 已启用                           | 计算机配置\策略\Windows 设置\管理模板\组件\远程桌面服务\远程桌面会话主机\连接         | 允许用户使用远程桌面进行远程连接                                  |
    | NLA 组策略                         | 设置\管理模板\组件\远程桌面服务\远程桌面会话主机\安全性                                                    | 需要完成用户身份验证才能使用 NLA 进行远程访问 |
    | Keep-Alive 设置                      | 计算机配置\策略\Windows 设置\管理模板\Windows 组件\远程桌面服务\远程桌面会话主机\连接 | 配置保持活动状态的连接间隔                                                 |
    | 重新连接设置                       | 计算机配置\策略\Windows 设置\管理模板\Windows 组件\远程桌面服务\远程桌面会话主机\连接 | 自动重新连接                                                                   |
    | “限制连接数”设置 | 计算机配置\策略\Windows 设置\管理模板\Windows 组件\远程桌面服务\远程桌面会话主机\连接 | 限制连接数                                                              |

## <a name="configure-windows-firewall-rules"></a>配置 Windows 防火墙规则
1. 在三个配置文件（“域”、“标准”和“公共”）上启用 Windows 防火墙：

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. 在 PowerShell 中运行以下命令，允许 WinRM 通过三个防火墙配置文件（“域”、“专用”和“公共”）并启用 PowerShell 远程服务：
   
   ```PowerShell
    Enable-PSRemoting -Force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. 启用以下防火墙规则以允许 RDP 流量：

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. 启用文件和打印机共享规则，使 VM 能够在虚拟网络中响应 ping 命令：

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. 如果 VM 会成为域的一部分，请检查以下 Azure AD 策略，确保未还原以前的设置。 

    | 目标                                 | 策略                                                                                                                                                  | ReplTest1                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | 启用 Windows 防火墙配置文件 | 计算机配置\策略\Windows 设置\管理模板\网络\网络连接\Windows 防火墙\域配置文件\Windows 防火墙   | 保护所有网络连接         |
    | 启用 RDP                           | 计算机配置\策略\Windows 设置\管理模板\网络\网络连接\Windows 防火墙\域配置文件\Windows 防火墙   | 允许入站远程桌面异常 |
    |                                      | 计算机配置\策略\Windows 设置\管理模板\网络\网络连接\Windows 防火墙\标准配置文件\Windows 防火墙 | 允许入站远程桌面异常 |
    | 启用 ICMP-V4                       | 计算机配置\策略\Windows 设置\管理模板\网络\网络连接\Windows 防火墙\域配置文件\Windows 防火墙   | 允许 ICMP 异常                   |
    |                                      | 计算机配置\策略\Windows 设置\管理模板\网络\网络连接\Windows 防火墙\标准配置文件\Windows 防火墙 | 允许 ICMP 异常                   |

## <a name="verify-the-vm"></a>验证 VM 

确保 VM 正常、安全并可使用 RDP 访问： 

1. 若要确保磁盘运行状况正常且一致，请在下次重启 VM 时检查磁盘：

    ```PowerShell
    Chkdsk /f
    ```
    确保报告显示磁盘干净且运行状况正常。

2. 设置引导配置数据 (BCD) 设置。 

    > [!NOTE]
    > 使用提升权限的 PowerShell 窗口运行这些命令。
   
   ```powershell
    bcdedit /set "{bootmgr}" integrityservices enable
    bcdedit /set "{default}" device partition=C:
    bcdedit /set "{default}" integrityservices enable
    bcdedit /set "{default}" recoveryenabled Off
    bcdedit /set "{default}" osdevice partition=C:
    bcdedit /set "{default}" bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set "{bootmgr}" displaybootmenu yes
    bcdedit /set "{bootmgr}" timeout 5
    bcdedit /set "{bootmgr}" bootems yes
    bcdedit /ems "{current}" ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
3. 转储日志可帮助排查 Windows 崩溃问题。 启用转储日志收集：

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
    New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. 验证 Windows Management Instrumentation (WMI) 存储库是否一致：

    ```PowerShell
    winmgmt /verifyrepository
    ```
    如果存储库已损坏，请参阅 [WMI：存储库是否损坏](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)。

5. 确保没有其他应用程序正在使用端口 3389。 此端口用于 Azure 中的 RDP 服务。 若要查看 VM 上使用的端口，请运行 `netstat -anob`：

    ```PowerShell
    netstat -anob
    ```

6. 若要上传用作域控制器的 Windows VHD：

   * 请执行[这些额外的步骤](https://support.microsoft.com/kb/2904015)来准备磁盘。

   * 确保知道目录服务还原模式 (DSRM) 密码，因为有时候必须在 DSRM 下启动 VM。 有关详细信息，请参阅[设置 DSRM 密码](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx)。

7. 确保知道内置的管理员帐户和密码。 可能需要重置当前的本地管理员密码，确保可以使用此帐户通过 RDP 连接登录 Windows。 此访问权限由“允许通过远程桌面服务登录”组策略对象控制。 在以下位置的本地组策略编辑器中查看此对象：

    计算机配置\Windows 设置\安全设置\本地策略\用户权限分配

8. 检查以下 Azure AD 策略，确保不是通过 RDP 或从网络阻止 RDP 访问：

    - 计算机配置\Windows 设置\安全设置\本地策略\用户权限分配\拒绝从网络访问这台计算机

    - 计算机配置\Windows 设置\安全设置\本地策略\用户权限分配\拒绝通过远程桌面服务登录


9. 检查以下 Azure AD 策略，并确保不要删除任何必需的访问帐户：

   - 计算机配置\Windows 设置\安全设置\本地策略\用户权限分配\从网络访问这台计算机

   策略应列出以下组：

   - 管理员

   - 备份操作员

   - 每个人

   - 用户

10. 重启 VM，确保 Windows 仍可正常运行，并可通过 RDP 连接来访问。 此时，可能需要在本地 Hyper-V 中创建一个 VM，以确保该 VM 完全启动。 然后通过测试来确保可通过 RDP 来访问该 VM。

11. 删除所有其他传输驱动程序接口 (TDI) 筛选器。 例如，删除用于分析 TCP 数据包的软件或多余的防火墙。 以后，可以在将 VM 部署到 Azure 中后进行此项检查。

12. 卸载与物理组件相关的任何其他第三方软件或驱动程序，或卸载任何其他虚拟化技术。

### <a name="install-windows-updates"></a>安装 Windows 更新
最好是在*修补程序级别*保持更新计算机。 如果这不可能，请确保安装以下更新：

| 组件               | Binary         | Windows 7 SP1、Windows Server 2008 R2 SP1 | Windows 8、Windows Server 2012               | Windows 8.1、Windows Server 2012 R2 | Windows 10 v1607、Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709、Windows Server 2016 v1709 | Windows 10 v1803、Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| 存储                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| 网络                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Core                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| 远程桌面服务 | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| 安全性                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
### 确定何时使用 sysprep <a id="step23"></a>    

系统准备工具 (Sysprep) 是一个可以重置 Windows 安装的进程。 Sysprep 会删除所有个人数据并重置多个组件，从而为你提供“全新安装”体验。 

通常你会运行 Sysprep 来创建一个模板，然后从中部署多个具有特定配置的其他 VM。 该模板称为“通用化映像”。

如果只需从一个磁盘创建一个 VM，则无需使用 Sysprep。 可以从专用化映像创建 VM。 有关如何从专用化磁盘创建 VM 的信息，请参阅：

- [从专用磁盘创建 VM](create-vm-specialized.md)
- [Create a VM from a specialized VHD disk](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)（从专用 VHD 磁盘创建 VM）

若要创建通用化映像，则需运行 Sysprep。 有关详细信息，请参阅[如何使用 Sysprep：简介](https://technet.microsoft.com/library/bb457073.aspx)。 

并非每个安装在基于 Windows 的计算机上的角色或应用程序都支持通用化映像。 因此，在运行此过程之前，请确保 Sysprep 支持该计算机的角色。 有关详细信息，请参阅 [Sysprep 对服务器角色的支持](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)。

### <a name="generalize-a-vhd"></a>通用化 VHD

>[!NOTE]
> 在以下步骤中运行 `sysprep.exe` 后，请关闭 VM。 在 Azure 中从该 VM 创建映像之前，请不要将其重新打开。

1. 登录到 Windows VM。
1. 以管理员身份运行命令提示符。 
1. 将目录切换到 `%windir%\system32\sysprep`。 然后运行 `sysprep.exe`。
1. 在“系统准备工具”对话框中，选择“进入系统全新体验(OOBE)”，确保已选中“通用化”复选框。

    ![系统准备工具](media/prepare-for-upload-vhd-image/syspre.png)
1. 在“关机选项”中选择“关机”。
1. 选择“确定”。
1. 当 Sysprep 完成后，关闭 VM。 请勿使用“重启”来关闭 VM。

现在，VHD 已准备就绪，可以上传了。 有关如何从通用化磁盘创建 VM 的详细信息，请参阅[上传通用化 VHD 并使用它在 Azure 中创建新的 VM](sa-upload-generalized.md)。


>[!NOTE]
> 不支持自定义的 *unattend.xml* 文件。 尽管我们确实支持 `additionalUnattendContent` 属性，但针对向 Azure 预配代理使用的 *unattention.xml* 文件添加 [microsoft-windows-shell-setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) 选项，它只提供有限的支持。 例如，可以使用 [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) 添加 FirstLogonCommands 和 LogonCommands。 有关详细信息，请参阅 [additionalUnattendContent FirstLogonCommands 示例](https://github.com/Azure/azure-quickstart-templates/issues/1407)。


## <a name="complete-the-recommended-configurations"></a>完成建议的配置
以下设置不影响 VHD 上传。 但是，我们强烈建议配置这些设置。

* 安装 [Azure 虚拟机代理](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 然后即可启用 VM 扩展。 VM 扩展实现了可能需要用于 VM 的大多数关键功能。 例如，需要使用这些扩展来重置密码或配置 RDP。 有关详细信息，请参阅 [Azure 虚拟机代理概述](../extensions/agent-windows.md)。
* 在 Azure 中创建 VM 后，建议将 pagefile 置于临时驱动器卷以改进性能。 可按如下所示设置文件位置：

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -Force
   ```
  如果某个数据磁盘已附加到 VM，则临时驱动器卷的驱动器号通常为 *D*。此驱动器号可能有所不同，具体取决于你的设置，以及可用驱动器的数目。
  * 建议禁用防病毒软件可能提供的脚本阻止程序。 当你从映像部署新的 VM 时，它们可能会 interfer 和阻止执行的 Windows 预配代理脚本。
  
## <a name="next-steps"></a>后续步骤
* [将 Windows VM 映像上传到 Azure 以进行 Resource Manager 部署](upload-generalized-managed.md)
* [排查 Azure Windows VM 激活问题](troubleshoot-activation-problems.md)

