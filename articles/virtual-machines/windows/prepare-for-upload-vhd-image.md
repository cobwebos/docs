---
title: 准备好要上传到 Azure 的 Windows VHD | Microsoft Docs
description: 了解如何准备 Windows VHD 或 VHDX, 以将其上传到 Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: a0cd2952633293bfa1d29bf3a399c67bf092d288
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68318321"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>准备好要上传到 Azure 的 Windows VHD 或 VHDX

将 Windows 虚拟机 (VM) 从本地上传到 Azure 之前, 必须准备好虚拟硬盘 (VHD 或 VHDX)。 Azure 支持以 VHD 文件格式且具有固定大小磁盘的第1代和第2代 Vm。 VHD 允许的最大大小为 1,023 GB。 

在第1代虚拟机中, 你可以将 VHDX 文件系统转换为 VHD。 你还可以将动态扩展磁盘转换为固定大小磁盘。 但无法更改 VM 的代次。 有关详细信息, 请参阅是否[应在 hyper-v 中创建第1代或第2代 vm？](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)和[适用于第2代 vm (预览版) 的 Azure 支持](generation-2.md)。

有关 Azure Vm 的支持策略的信息, 请参阅[Microsoft server software support For Azure vm](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)。

> [!NOTE]
> 本文中的说明适用于:
>1. 64位版本的 Windows Server 2008 R2 和更高版本的 Windows Server 操作系统。 有关在 Azure 中运行32位操作系统的信息, 请参阅对[Azure vm 中的32位操作系统的支持](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)。
>2. 如果任何灾难恢复工具将用于迁移工作负荷 (如 Azure Site Recovery 或 Azure Migrate), 则仍需执行此过程, 然后在来宾 OS 上执行迁移之前准备映像的操作。

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>将虚拟磁盘转换为固定大小和 VHD

如果需要将虚拟磁盘转换为 Azure 所需的格式, 请使用此部分中的方法之一:

1. 在运行虚拟磁盘转换过程之前备份 VM。

1. 确保 Windows VHD 在本地服务器上正常工作。 在尝试转换磁盘或将其上传到 Azure 之前，请解决 VM 本身内部的所有错误。

1. 关于 VHD 的大小:

   1. Azure 上的所有 VHD 必须已将虚拟大小调整为 1MB。 将原始磁盘转换为 VHD 时, 必须确保原始磁盘大小为 1 MB 的倍数, 然后转换。 在从已上传的 VHD 中创建映像时, 将会导致错误。

   2. 操作系统 VHD 允许的最大大小为2TB。


转换磁盘后, 创建使用该磁盘的虚拟机。 启动并登录到该 VM, 以完成准备上传。

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>使用 Hyper-v 管理器转换磁盘 
1. 打开 Hyper-V 管理器，在左侧选择本地计算机。 在计算机列表上方的菜单中, 选择 "**操作** > " "**编辑磁盘**"。
2. 在 "**查找虚拟硬盘**" 页面上, 选择虚拟磁盘。
3. 在 "**选择操作**" 页上 **, 选择** > "**下一步**"。
4. 如果需要从 VHDX 进行转换, 请选择 " **VHD** >  **"。**
5. 如果需要从动态扩展磁盘进行转换, 请选择 "**固定大小** >  **"。**
6. 找到并选择新 VHD 文件的保存路径。
7. 选择“完成”。 

> [!NOTE]
> 使用提升的 PowerShell 会话运行本文中的命令。

### <a name="use-powershell-to-convert-the-disk"></a>使用 PowerShell 转换磁盘 
可以使用 Windows PowerShell 中的 [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) 命令转换虚拟磁盘。 启动 PowerShell 时，选择“以管理员身份运行”  。 

下面的示例命令将磁盘从 VHDX 转换为 VHD。 此命令还会将磁盘从动态扩展磁盘转换为固定大小磁盘。

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

在此命令中, 将的值`-Path`替换为要转换的虚拟硬盘的路径。 将的值`-DestinationPath`替换为转换后磁盘的新路径和名称。

### <a name="convert-from-vmware-vmdk-disk-format"></a>从 VMware VMDK 磁盘格式转换
如果具有[VMDK 文件格式](https://en.wikipedia.org/wiki/VMDK)的 Windows VM 映像, 请使用[Microsoft 虚拟机转换器](https://www.microsoft.com/download/details.aspx?id=42497)将其转换为 VHD 格式。 有关详细信息, 请参阅[如何将 VMWARE VMDK 转换为 HYPER-V VHD](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)。

## <a name="set-windows-configurations-for-azure"></a>设置 Azure 的 Windows 配置

在计划上传到 Azure 的 VM 上, 在[提升的命令提示符窗口](https://technet.microsoft.com/library/cc947813.aspx)中运行以下命令:

1. 删除路由表中的所有静态持久性路由：
   
   * 若要查看路由表，请在命令提示符处运行 `route print`。
   * 请检查`Persistence Routes`部分。 如果有持久性路由, 请使用`route delete`命令将其删除。
2. 删除 WinHTTP 代理：
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    如果 vm 需要使用特定的代理, 请将代理例外添加到 azure IP 地址 ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)), 以便 VM 可以连接到 azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. 将磁盘 SAN 策略设置为[`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx):
   
    ```PowerShell
    diskpart 
    ```
    在 "打开命令提示符" 窗口中, 键入以下命令:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. 为 Windows 设置协调世界时 (UTC) 时间。 还将 Windows 时间服务 (`w32time`) 的启动类型设置为: `Automatic`
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. 将电源配置文件设置为 "高性能":

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. 请确保环境变量`TEMP`和`TMP`设置为其默认值:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>查看 Windows 服务
请确保以下每个 Windows 服务都设置为 Windows 默认值。 必须设置这些服务才能确保 VM 连接。 若要重置启动设置，请运行以下命令：
   
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
请确保为远程访问正确配置以下设置:

>[!NOTE] 
>运行`Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name &lt;object name&gt; -value &lt;value&gt;`时, 可能会收到一条错误消息。 您可以放心地忽略此消息。 这意味着只有域不会通过组策略对象推送该配置。

1. 已启用远程桌面协议 (RDP)：
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. 已正确设置 RDP 端口。 默认端口是 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    部署 VM 时，默认规则是针对端口 3389 创建的。 若要更改端口号, 请在 VM 部署到 Azure 中后执行此操作。

3. 侦听器在每个网络接口中侦听：
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. 为 RDP 连接配置网络级身份验证 (NLA) 模式:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -force
     ```

5. 设置 keep-alive 值：
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" -Value 1 -Type DWord -force
    ```
6. 重新连接：
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" -Value 0 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" -Value 1 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" -Value 0 -Type DWord -force
    ```
7. 限制并发连接数：
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" -Value 4294967295 -Type DWord -force
    ```
8. 删除绑定到 RDP 侦听器的任何自签名证书:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    此代码可确保你可以在部署 VM 时开始连接。 如果以后需要查看此项, 可以在将 VM 部署到 Azure 中后执行此操作。

9. 如果 VM 将是域的一部分, 请检查以下策略, 以确保不会还原以前的设置。 
    
    | 目标                                     | 策略                                                                                                                                                       | 值                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP 已启用                           | 计算机配置\策略\Windows 设置\管理模板\组件\远程桌面服务\远程桌面会话主机\连接         | 允许用户使用远程桌面进行远程连接                                  |
    | NLA 组策略                         | 设置\管理模板\组件\远程桌面服务\远程桌面会话主机\安全性                                                    | 使用 NLA 需要用户身份验证才能进行远程访问 |
    | Keep-alive 设置                      | 计算机配置\策略\Windows 设置\管理模板\Windows 组件\远程桌面服务\远程桌面会话主机\连接 | 配置保持活动状态的连接间隔                                                 |
    | 重新连接设置                       | 计算机配置\策略\Windows 设置\管理模板\Windows 组件\远程桌面服务\远程桌面会话主机\连接 | 自动重新连接                                                                   |
    | 连接设置数量有限 | 计算机配置\策略\Windows 设置\管理模板\Windows 组件\远程桌面服务\远程桌面会话主机\连接 | 限制连接数                                                              |

## <a name="configure-windows-firewall-rules"></a>配置 Windows 防火墙规则
1. 在三个配置文件 ("域"、"标准" 和 "公共") 上启用 Windows 防火墙:

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. 在 PowerShell 中运行以下命令, 允许 WinRM 通过三个防火墙配置文件 ("域"、"专用" 和 "公用"), 并启用 PowerShell 远程服务:
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. 启用以下防火墙规则以允许 RDP 流量：

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. 启用 "文件和打印机共享" 规则, 以便 VM 可以在虚拟网络中响应 ping 命令:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. 如果 VM 将是域的一部分, 请检查以下 Azure AD 策略, 以确保不会还原以前的设置。 

    | 目标                                 | 策略                                                                                                                                                  | 值                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | 启用 Windows 防火墙配置文件 | 计算机配置\策略\Windows 设置\管理模板\网络\网络连接\Windows 防火墙\域配置文件\Windows 防火墙   | 保护所有网络连接         |
    | 启用 RDP                           | 计算机配置\策略\Windows 设置\管理模板\网络\网络连接\Windows 防火墙\域配置文件\Windows 防火墙   | 允许入站远程桌面异常 |
    |                                      | 计算机配置\策略\Windows 设置\管理模板\网络\网络连接\Windows 防火墙\标准配置文件\Windows 防火墙 | 允许入站远程桌面异常 |
    | 启用 ICMP-V4                       | 计算机配置\策略\Windows 设置\管理模板\网络\网络连接\Windows 防火墙\域配置文件\Windows 防火墙   | 允许 ICMP 异常                   |
    |                                      | 计算机配置\策略\Windows 设置\管理模板\网络\网络连接\Windows 防火墙\标准配置文件\Windows 防火墙 | 允许 ICMP 异常                   |

## <a name="verify-the-vm"></a>验证 VM 

请确保 VM 运行状况正常、安全且可访问 RDP: 

1. 若要确保磁盘正常运行且一致, 请在下一个 VM 重新启动时检查磁盘:

    ```PowerShell
    Chkdsk /f
    ```
    请确保报表显示 "干净" 和 "正常" 的磁盘。

2. 设置引导配置数据 (BCD) 设置。 

    > [!NOTE]
    > 使用权限提升的 PowerShell 窗口运行这些命令。
   
   ```powershell
    cmd

    bcdedit /set {bootmgr} integrityservices enable
    bcdedit /set {default} device partition=C:
    bcdedit /set {default} integrityservices enable
    bcdedit /set {default} recoveryenabled Off
    bcdedit /set {default} osdevice partition=C:
    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set {bootmgr} displaybootmenu yes
    bcdedit /set {bootmgr} timeout 5
    bcdedit /set {bootmgr} bootems yes
    bcdedit /ems {current} ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200

    exit
   ```
3. 转储日志可帮助排查 Windows 崩溃问题。 启用转储日志收集:

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name CrashDumpEnabled -Type DWord -force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name DumpFile -Type ExpandString -force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name NMICrashDump -Type DWord -force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -name DumpFolder -Type ExpandString -force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -name CrashCount -Type DWord -force -Value 10
    New-ItemProperty -Path $key -name DumpType -Type DWord -force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. 验证 Windows Management Instrumentation (WMI) 存储库是否一致:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    如果存储库已损坏，请参阅 [WMI：存储库损坏](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)。

5. 请确保没有其他应用程序正在使用端口3389。 此端口用于 Azure 中的 RDP 服务。 若要查看 VM 上使用的端口, 请运行`netstat -anob`:

    ```PowerShell
    netstat -anob
    ```

6. 上传域控制器的 Windows VHD:

   * 请执行[这些额外的步骤](https://support.microsoft.com/kb/2904015)来准备磁盘。

   * 请确保了解目录服务还原模式 (DSRM) 密码, 以防在某个时间点必须在 DSRM 中启动 VM。 有关详细信息, 请参阅[设置 DSRM 密码](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx)。

7. 请确保知道内置的管理员帐户和密码。 你可能需要重置当前的本地管理员密码, 并确保可以使用此帐户通过 RDP 连接登录 Windows。 此访问权限由 "允许通过远程桌面服务登录" 组策略对象控制。 在此处本地组策略编辑器查看此对象:

    计算机配置\Windows 设置\安全设置\本地策略\用户权限分配

8. 检查以下 Azure AD 策略, 以确保不会通过 RDP 或从网络阻止 RDP 访问:

    - 计算机配置\Windows 设置\安全设置\本地策略\用户权限分配\拒绝从网络访问这台计算机

    - 计算机配置\Windows 设置\安全设置\本地策略\用户权限分配\拒绝通过远程桌面服务登录


9. 检查以下 Azure AD 策略, 确保不会删除任何所需的访问帐户:

   - 计算机配置 \Windows 设置 \ 安全设置 \ 本地策略 \ 计算机从网络 Assignment\Access 此计算机

   策略应列出以下组:

   - 管理员

   - 备份操作员

   - 所有人

   - 用户

10. 重新启动 VM, 确保 Windows 仍正常运行, 并可通过 RDP 连接访问。 此时, 你可能需要在本地 Hyper-v 中创建一个 VM, 确保 VM 完全启动。 然后进行测试, 以确保可通过 RDP 访问 VM。

11. 删除任何额外的传输驱动程序接口 (TDI) 筛选器。 例如, 删除分析 TCP 数据包或额外防火墙的软件。 如果以后需要查看此项, 可以在将 VM 部署到 Azure 中后执行此操作。

12. 卸载与物理组件或任何其他虚拟化技术相关的任何其他第三方软件或驱动程序。

### <a name="install-windows-updates"></a>安装 Windows 更新
理想情况下, 应将计算机更新为*修补程序级别*。 如果无法做到这一点, 请确保安装以下更新:

| 组件               | Binary         | Windows 7 SP1、Windows Server 2008 R2 SP1 | Windows 8、Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607、Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709、Windows Server 2016 v1709 | Windows 10 v1803、Windows Server 2016 v1803 |
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
       
### 确定何时使用 Sysprep<a id="step23"></a>    

系统准备工具 (Sysprep) 是可用于重置 Windows 安装的进程。 Sysprep 通过删除所有个人数据和重置多个组件提供 "现成" 的体验。 

你通常会运行 Sysprep 来创建一个模板, 你可以从该模板部署多个具有特定配置的其他 Vm。 模板称为*通用化映像*。

如果只需从一个磁盘创建一个 VM, 则无需使用 Sysprep。 相反, 你可以从*专用映像*创建 VM。 有关如何从专用磁盘创建 VM 的信息, 请参阅:

- [从专用磁盘创建 VM](create-vm-specialized.md)
- [Create a VM from a specialized VHD disk](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)（从专用 VHD 磁盘创建 VM）

如果要创建通用化映像, 则需运行 Sysprep。 有关详细信息, 请[参阅如何使用 Sysprep:简介](https://technet.microsoft.com/library/bb457073.aspx)。 

并非每个安装在基于 Windows 的计算机上的角色或应用程序都支持通用化映像。 因此, 在运行此过程之前, 请确保 Sysprep 支持计算机的角色。 有关详细信息，请参阅 [Sysprep 对服务器角色的支持](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)。

### <a name="generalize-a-vhd"></a>通用化 VHD

>[!NOTE]
> 在执行`sysprep.exe`以下步骤后, 关闭 VM。 在 Azure 中创建映像之前, 请不要将其重新打开。

1. 登录到 Windows VM。
1. 以管理员身份运行命令提示符。  
1. 将目录更改为`%windir%\system32\sysprep`。 然后运行 `sysprep.exe`。
1. 在“系统准备工具”对话框中，选择“进入系统全新体验(OOBE)”，确保已选中“通用化”复选框。   

    ![系统准备工具](media/prepare-for-upload-vhd-image/syspre.png)
1. 在“关机选项”  中选择“关机”  。
1. 选择“确定”  。
1. Sysprep 完成后, 关闭 VM。 不要使用 "**重新启动**" 来关闭 VM。

现在，VHD 已准备就绪，可以上传了。 有关如何从通用化磁盘创建 VM 的详细信息, 请参阅[上传通用化 VHD 并使用它在 Azure 中创建新 vm](sa-upload-generalized.md)。


>[!NOTE]
> 自定义*unattend.xml*文件不受支持。 尽管我们支持`additionalUnattendContent`属性, 但它仅提供有限的支持, 以便将[microsoft windows shell 安装](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup)选项添加到 Azure 预配代理使用的*unattend.xml*文件中。 例如, 可以使用[additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet)添加 FirstLogonCommands 和 LogonCommands。 有关详细信息, 请参阅[AdditionalUnattendContent FirstLogonCommands 示例](https://github.com/Azure/azure-quickstart-templates/issues/1407)。


## <a name="complete-the-recommended-configurations"></a>完成建议的配置
以下设置不影响 VHD 上传。 但是，我们强烈建议配置这些设置。

* 安装[Azure 虚拟机代理](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 然后即可启用 VM 扩展。 VM 扩展实现了可能需要用于 Vm 的大多数关键功能。 例如, 你将需要这些扩展来重置密码或配置 RDP。 有关详细信息，请参阅 [Azure 虚拟机代理概述](../extensions/agent-windows.md)。
* 在 Azure 中创建 VM 后, 建议将页面文件放在*时态驱动器卷*上, 以提高性能。 您可以设置文件位置, 如下所示:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -force
   ```
  如果数据磁盘已附加到 VM, 则临时驱动器卷的驱动器号通常为*D*。此指定可能会不同, 具体取决于你的设置和可用驱动器的数量。

## <a name="next-steps"></a>后续步骤
* [将 Windows VM 映像上传到 Azure 以进行 Resource Manager 部署](upload-generalized-managed.md)
* [排查 Azure Windows VM 激活问题](troubleshoot-activation-problems.md)

