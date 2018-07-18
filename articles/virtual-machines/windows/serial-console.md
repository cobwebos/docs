---
title: Azure 虚拟机串行控制台 | Microsoft Docs
description: Azure 虚拟机的双向串行控制台。
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 8e9e98a0c9f1db805d62cdee038fb4754ff047e2
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2018
ms.locfileid: "36960434"
---
# <a name="virtual-machine-serial-console-preview"></a>虚拟机串行控制台（预览版） 


使用 Azure 上的虚拟机串行控制台可以访问适用于 Linux 和 Windows 虚拟机的基于文本的控制台。 此控制台与虚拟机的 COM1 串行端口建立串行连接，可用于访问虚拟机，且不与虚拟机的网络/操作系统状态相关。 目前，只能通过 Azure 门户访问虚拟机的串行控制台，并且只允许对虚拟机拥有 VM 参与者或更高访问权限的用户执行此操作。 

> [!Note] 
> 同意使用条款即可使用预览版。 有关详细信息，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 目前，此服务以**公共预览版**提供，可以访问已在 Azure 全球区域开通的虚拟机的串行控制台。 目前，串行控制台不可用于 Azure 政府云、Azure 德国云和 Azure 中国云。

 

## <a name="prerequisites"></a>先决条件 

* 必须使用资源管理部署模型。 不支持经典部署。 
* 虚拟机上必须已启用[启动诊断](boot-diagnostics.md) 
* 使用串行控制台的帐户必须对 VM 和[启动诊断](boot-diagnostics.md)存储帐户拥有[参与者角色](../../role-based-access-control/built-in-roles.md)。 

## <a name="open-the-serial-console"></a>打开串行控制台
只能通过 [Azure 门户](https://portal.azure.com)访问虚拟机的串行控制台。 下面是通过门户访问虚拟机串行控制台的步骤 

  1. 打开 Azure 门户
  2. 在左侧菜单中选择虚拟机。
  3. 在列表中单击所需的 VM。 此时会打开该 VM 的概述页。
  4. 向下滚动到“支持 + 故障排除”部分，单击“串行控制台(预览版)”选项。 此时会打开一个包含串行控制台的新窗格，并启动连接。

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

### <a name="disable-feature"></a>禁用功能
可以通过禁用特定 VM 的启动诊断设置，针对该 VM 停用串行控制台功能。

## <a name="serial-console-security"></a>串行控制台安全性 

### <a name="access-security"></a>访问安全性 
只有对虚拟机拥有 [VM 参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)或更高访问权限的用户才能访问串行控制台。 如果 AAD 租户需要多重身份验证 (MFA)，则访问串行控制台时也需要执行 MFA，因为串行控制台是通过 [Azure 门户](https://portal.azure.com)访问的。

### <a name="channel-security"></a>通道安全性
通过网络来回发送的所有数据都经过加密。

### <a name="audit-logs"></a>审核日志
对串行控制台的所有访问目前都会记录在虚拟机的[启动诊断](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics)日志中。 Azure 虚拟机管理员拥有并可控制这些日志的访问权限。  

>[!CAUTION] 
尽管不会记录控制台的访问密码，但如果在控制台中运行的命令包含或输出密码、机密、用户名或其他任何形式的个人身份信息 (PII)，则在实现串行控制台的回滚功能过程中，会将这些信息连同其他所有可见文本一起写入虚拟机的启动诊断日志。 这些日志不断轮替，只有对诊断存储帐户拥有读取权限的个人才能访问它们。但是，我们建议遵循有关将远程桌面用于涉及机密和/或 PII 的任何操作的最佳做法。 

### <a name="concurrent-usage"></a>并发使用
如果某个用户已连接到串行控制台，而另一个用户已成功请求访问同一个虚拟机，则第一个用户将断开连接，第二用户将建立连接，就好比是第一个用户站起来离开物理控制台，然后一个新用户在控制台旁边坐下来。

>[!CAUTION] 
这意味着，断开连接的用户并未注销！ 断开连接后强制注销（通过 SIGHUP 或类似机制）的功能目前仍在规划中。 对于 Windows，SAC 中会启用自动超时；但对于 Linux，可以配置终端超时设置。 


## <a name="accessing-serial-console-for-windows"></a>访问适用于 Windows 的串行控制台 
Azure 上较新的 Windows Server 映像默认情况下已启用[特殊管理控制台](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC)。 SAC 在服务器版本的 Windows 上受支持，但在客户端版本（例如 Windows 10、Windows 8 或 Windows 7）上不可用。 若要为使用 Feb2018 或更低版本的映像创建的 Windows 虚拟机启用串行控制台，请执行以下步骤： 

1. 通过远程桌面连接到 Windows 虚拟机
2. 从管理命令提示符运行以下命令 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. 重新启动系统以启用 SAC 控制台

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

如果需要，也可以使 SAC 脱机。 

1. 将想要为其配置 SAC 的 Windows 磁盘作为数据磁盘附加到现有 VM。 
2. 从管理命令提示符运行以下命令 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>如何知道是否已启用 SAC 

如果未启用 [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx)，串行控制台将不会显示 SAC 提示符。 在某些情况下它可能会显示 VM 运行状况信息，或者会为空。  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>让启动菜单显示在串行控制台中 

如果需要让 Windows 启动加载程序提示符显示在串行控制台中，可以将以下附加选项添加到 Windows 启动加载程序。

1. 通过远程桌面连接到 Windows 虚拟机
2. 从管理命令提示符运行以下命令 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. 重新启动系统以启用启动菜单

> [!NOTE] 
> 就目前而言，对功能键的支持未启用，如果需要高级启动选项，请使用 use bcdedit /set {current} onetimeadvancedoptions on，有关详细信息，请参阅 [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set)

## <a name="windows-commands---cmd"></a>Windows 命令 - CMD 

本部分包含需要使用 SAC 访问 VM（例如，需要排查 RDP 连接失败问题）时，用于执行常见任务的示例命令。

SAC 已包含在 Windows Server 2003 和所有更高版本中，但默认已禁用。 SAC 依赖于 `sacdrv.sys` 内核驱动程序、`Special Administration Console Helper` 服务 (`sacsvr`) 和 `sacsess.exe` 进程。 有关详细信息，请参阅[紧急管理服务工具和设置](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10))。

使用 SAC 可以通过串行端口连接到正在运行的 OS。 从 SAC 启动 CMD 时，`sacsess.exe` 会从正在运行的 OS 内部启动 `cmd.exe`。 如果通过 RDP 连接到 VM，同时通过串行控制台功能连接到 SAC 的话，可在任务管理器中看到此进程。 通过 SAC 访问的 CMD 也就是通过 RDP 连接时使用的 `cmd.exe`。 可以使用所有相同的命令和工具，包括可以从该 CMD 实例启动 PowerShell。 SAC 与 Windows 恢复环境 (WinRE) 之间的主要差别在于，使用 SAC 可以管理正在运行的 OS，而 WinRE 会引导进入一个不同的精简 OS。 尽管 Azure VM 不支持访问 WinRE，但使用串行控制台功能可以通过 SAC 管理 Azure VM。

由于 SAC 限制为 80x24 屏幕缓冲且不支持回滚，因此，需添加 `| more` 命令来每次显示一页输出。 使用 `<spacebar>` 查看下一页，或使用 `<enter>` 查看下一行。  

`SHIFT+INSERT` 是串行控制台窗口中的粘贴快捷键。

由于 SAC 的屏幕缓冲受限，因此，在本地文本编辑器中键入较长的命令，然后将其粘贴到 SAC 中可能会更方便。

### <a name="view-and-edit-windows-registry-settings"></a>查看和编辑 Windows 注册表设置
#### <a name="verify-rdp-is-enabled"></a>验证是否已启用 RDP
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

仅当已配置相关的组策略设置时，第二个键（在 \Policies 下）才存在。

#### <a name="enable-rdp"></a>启用 RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

仅当已配置相关的组策略设置时，才需要第二个键（在 \Policies 下）。 下一次刷新组策略时，将重写值（如果已在组策略中配置此设置）。

### <a name="manage-windows-services"></a>管理 Windows 服务

#### <a name="view-service-state"></a>查看服务状态
`sc query termservice`
####  <a name="view-service-logon-account"></a>查看服务登录帐户
`sc qc termservice`
#### <a name="set-service-logon-account"></a>设置服务登录帐户 
`sc config termservice obj= "NT Authority\NetworkService"`

需要在等号后面添加空格。
#### <a name="set-service-start-type"></a>设置服务启动类型
`sc config termservice start= demand` 

需要在等号后面添加空格。 可能的启动值包括 `boot`、`system`、`auto`、`demand`、`disabled`、`delayed-auto`。
#### <a name="set-service-dependencies"></a>设置服务依赖关系
`sc config termservice depend= RPCSS`

需要在等号后面添加空格。
#### <a name="start-service"></a>启动服务
`net start termservice`

或

`sc start termservice`
#### <a name="stop-service"></a>停止服务
`net stop termservice`

或

`sc stop termservice`
### <a name="manage-networking-features"></a>管理网络功能
#### <a name="show-nic-properties"></a>显示 NIC 属性
`netsh interface show interface` 
#### <a name="show-ip-properties"></a>显示 IP 属性
`netsh interface ip show config`
#### <a name="show-ipsec-configuration"></a>显示 IPSec 配置
`netsh nap client show configuration`  
#### <a name="enable-nic"></a>启用 NIC
`netsh interface set interface name="<interface name>" admin=enabled`
#### <a name="set-nic-to-use-dhcp"></a>将 NIC 设置为使用 DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

应始终在来宾 OS 中将 Azure VM 配置为使用 DHCP 获取 IP 地址。 Azure 静态 IP 设置仍使用 DHCP 向 VM 提供静态 IP。
#### <a name="ping"></a>Ping
`ping 8.8.8.8` 
#### <a name="port-ping"></a>端口 ping  
安装 telnet 客户端

`dism /online /Enable-Feature /FeatureName:TelnetClient`

测试连接

`telnet bing.com 80`

删除 telnet 客户端

`dism /online /Disable-Feature /FeatureName:TelnetClient`

如果默认限制为 Windows 中可用的方法，则 PowerShell 可能是用于测试端口连接的更好方法。 有关示例，请参阅下面的 PowerShell 部分。
#### <a name="test-dns-name-resolution"></a>测试 DNS 名称解析
`nslookup bing.com`
#### <a name="show-windows-firewall-rule"></a>显示 Windows 防火墙规则
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
#### <a name="disable-windows-firewall"></a>禁用 Windows 防火墙
`netsh advfirewall set allprofiles state off`

故障排除时，可以使用此命令暂时排除 Windows 防火墙。 下一次重启时，将会启用防火墙；也可以使用以下命令来启用它。 不要通过停止 Windows 防火墙服务 (MPSSVC) 或基本筛选引擎 (BFE) 服务来排除 Windows 防火墙。 停止 MPSSVC 或 BFE 会导致所有连接被阻止。
#### <a name="enable-windows-firewall"></a>启用 Windows 防火墙
`netsh advfirewall set allprofiles state on`
### <a name="manage-users-and-groups"></a>管理用户和组
#### <a name="create-local-user-account"></a>创建本地用户帐户
`net user /add <username> <password>`
#### <a name="add-local-user-to-local-group"></a>将本地用户添加到本地组
`net localgroup Administrators <username> /add`
#### <a name="verify-user-account-is-enabled"></a>验证是否已启用用户帐户
`net user <username> | find /i "active"`

基于通用化映像创建的 Azure VM 的本地管理员帐户将重命名为 VM 预配期间指定的名称。 因此，该帐户通常不是 `Administrator`。
#### <a name="enable-user-account"></a>启用用户帐户
`net user <username> /active:yes`  
#### <a name="view-user-account-properties"></a>查看用户帐户属性
`net user <username>`

本地管理员帐户中的相关示例代码行：

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

#### <a name="view-local-groups"></a>查看本地组
`net localgroup`
### <a name="manage-the-windows-event-log"></a>管理 Windows 事件日志
#### <a name="query-event-log-errors"></a>查询事件日志错误
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

将 `/c:10` 更改为要返回的所需事件数，或者移动它，以返回与筛选器匹配的所有事件。
#### <a name="query-event-log-by-event-id"></a>按事件 ID 查询事件日志
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>按事件 ID 和提供程序查询事件日志
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>按事件 ID 和提供程序查询过去 24 小时的事件日志
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

使用 `604800000` 查看过去 7 天而不是 24 小时的事件。
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>按事件 ID、提供程序和 EventData 查询过去 7 天的事件日志
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>查看或删除已安装的应用程序
#### <a name="list-installed-applications"></a>列出已安装的应用程序
`wmic product get Name,InstallDate | sort /r | more`

`sort /r` 按安装日期的降序排序，以方便查看最近安装的应用程序。 使用 `<spacebar>` 转到下一页输出，或使用 `<enter>` 转到下一行。
#### <a name="uninstall-an-application"></a>卸载应用程序
`wmic path win32_product where name="<name>" call uninstall`

将 `<name>` 替换为上述命令中针对所要删除的应用程序返回的名称。

### <a name="file-system-management"></a>文件系统管理
#### <a name="get-file-version"></a>获取文件版本
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

此示例返回虚拟 NIC 驱动程序（即 netvsc.sys、netvsc63.sys 或 netvsc60.sys，具体取决于 Windows 版本）的文件版本。
#### <a name="scan-for-system-file-corruption"></a>在系统中扫描文件损坏情况
`sfc /scannow`

另请参阅[修复 Windows 映像](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image)。
#### <a name="scan-for-system-file-corruption"></a>在系统中扫描文件损坏情况
`dism /online /cleanup-image /scanhealth`

另请参阅[修复 Windows 映像](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image)。
#### <a name="export-file-permissions-to-text-file"></a>将文件权限导出到文本文件
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
#### <a name="save-file-permissions-to-acl-file"></a>将文件权限保存到 ACL 文件
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
#### <a name="restore-file-permissions-from-acl-file"></a>从 ACL 文件还原文件权限
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

使用 `/restore` 时的路径需是使用 `/save` 指定的文件夹的父文件夹。 在此示例中，`\RSA` 是在上述 `/save` 示例中指定的 `\MachineKeys` 文件夹的父级。
#### <a name="take-ntfs-ownership-of-a-folder"></a>取得文件夹的 NTFS 所有权
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
#### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>以递归方式授予对文件夹的 NTFS 权限
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
### <a name="manage-devices"></a>管理设备
#### <a name="remove-non-present-pnp-devices"></a>删除不存在的 PNP 设备
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
### <a name="manage-group-policy"></a>管理组策略
#### <a name="force-group-policy-update"></a>强制组策略更新
`gpupdate /force /wait:-1`
### <a name="miscellaneous-tasks"></a>其他任务
#### <a name="show-os-version"></a>显示 OS 版本
`ver`

或 

`wmic os get caption,version,buildnumber /format:list`

或 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
#### <a name="view-os-install-date"></a>查看 OS 安装日期
`systeminfo | find /i "original"`

或 

`wmic os get installdate`
#### <a name="view-last-boot-time"></a>查看上次启动时间
`systeminfo | find /i "system boot time"`
#### <a name="view-time-zone"></a>查看时区
`systeminfo | find /i "time zone"`

或

`wmic timezone get caption,standardname /format:list`
#### <a name="restart-windows"></a>重启 Windows
`shutdown /r /t 0`

添加 `/f` 会强制正在运行的应用程序关闭，且不向用户发出警告。
#### <a name="detect-safe-mode-boot"></a>检测安全模式启动
`bcdedit /enum | find /i "safeboot"` 

## <a name="windows-commands---powershell"></a>Windows 命令 - PowerShell

若要在 SAC 中运行 PowerShell，请在打开 CMD 提示符后键入：

`powershell <enter>`

>[!CAUTION]
在运行其他任何 PowerShell 命令之前，请从 PowerShell 会话中删除 PSReadLine 模块。 目前有一个已知问题：如果在 SAC 中的 PowerShell 会话内运行 PSReadLine，从剪贴板粘贴的文本中会出现额外的字符。

请先检查是否已加载 PSReadLine。 在 Windows Server 2016、Windows 10 和更高版本的 Windows 上，默认已加载 PSReadLine。 在更低的 Windows 版本上，只有手动安装的 PSReadLine 才会加载。 

如果此命令返回提示符而未提供任何输出，则表示未加载该模块，此时，可以在 SAC 中继续照常使用 PowerShell 会话。

`get-module psreadline`

如果上述命令返回了 PSReadLine 模块版本，请运行以下命令将其卸载。 此命令不会删除或卸载该模块，而只会从当前 PowerShell 会话中将其卸载。

`remove-module psreadline`

### <a name="view-and-edit-windows-registry-settings"></a>查看和编辑 Windows 注册表设置
#### <a name="verify-rdp-is-enabled"></a>验证是否已启用 RDP
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

仅当已配置相关的组策略设置时，第二个键（在 \Policies 下）才存在。
#### <a name="enable-rdp"></a>启用 RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

仅当已配置相关的组策略设置时，才需要第二个键（在 \Policies 下）。 下一次刷新组策略时，将重写值（如果已在组策略中配置此设置）。
### <a name="manage-windows-services"></a>管理 Windows 服务
#### <a name="view-service-details"></a>查看服务详细信息
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` 可用，但不包含服务登录帐户。 `Get-WmiObject win32-service` 包含服务登录帐户。
#### <a name="set-service-logon-account"></a>设置服务登录帐户
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

使用除 `NT AUTHORITY\LocalService`、`NT AUTHORITY\NetworkService` 或 `LocalSystem` 以外的服务帐户时，请将帐户密码指定为帐户名后面的最后一个（第八个）参数。
#### <a name="set-service-startup-type"></a>设置服务启动类型
`set-service termservice -startuptype Manual`

`Set-service` 接受使用 `Automatic`、`Manual` 或 `Disabled` 作为启动类型。
#### <a name="set-service-dependencies"></a>设置服务依赖关系
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
#### <a name="start-service"></a>启动服务
`start-service termservice`
#### <a name="stop-service"></a>停止服务
`stop-service termservice`
### <a name="manage-networking-features"></a>管理网络功能
#### <a name="show-nic-properties"></a>显示 NIC 属性
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

或 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` 适用于 2012 和更高版本；对于 2008R2，请使用 `Get-WmiObject`。
#### <a name="show-ip-properties"></a>显示 IP 属性
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
#### <a name="enable-nic"></a>启用 NIC
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

或

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` 适用于 2012 和更高版本；对于 2008R2，请使用 `Get-WmiObject`。
#### <a name="set-nic-to-use-dhcp"></a>将 NIC 设置为使用 DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` 适用于 2012+ 和更高版本。 对于 2008R2，请使用 `Get-WmiObject`。 应始终在来宾 OS 中将 Azure VM 配置为使用 DHCP 获取 IP 地址。 Azure 静态 IP 设置仍使用 DHCP 向 VM 提供 IP。
#### <a name="ping"></a>Ping
`test-netconnection`

或

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

不带任何参数的 `Test-Netconnection` 会尝试 ping `internetbeacon.msedge.net`。 该命令适用于 2012 和更高版本。 对于 2008R2，请使用 `Get-WmiObject`，如第二个示例中所示。
#### <a name="port-ping"></a>端口 Ping
`test-netconnection -ComputerName bing.com -Port 80`

或

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` 适用于 2012+ 和更高版本。 对于 2008R2，请使用 `Net.Sockets.TcpClient`
#### <a name="test-dns-name-resolution"></a>测试 DNS 名称解析
`resolve-dnsname bing.com` 

或 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` 适用于 2012+ 和更高版本。 对于 2008R2，请使用 `System.Net.DNS`。
#### <a name="show-windows-firewall-rule-by-name"></a>按名称显示 Windows 防火墙规则
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
#### <a name="show-windows-firewall-rule-by-port"></a>按端口显示 Windows 防火墙规则
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

或

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` 适用于 2012+ 和更高版本。 对于 2008R2，请使用 `hnetcfg.fwpolicy2` COM 对象。 
#### <a name="disable-windows-firewall"></a>禁用 Windows 防火墙
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` 适用于 2012+ 和更高版本。 对于 2008R2，请使用前面“CMD”部分中提到的 `netsh advfirewall`。
### <a name="manage-users-and-groups"></a>管理用户和组
#### <a name="create-local-user-account"></a>创建本地用户帐户
`new-localuser <name>`
#### <a name="verify-user-account-is-enabled"></a>验证是否已启用用户帐户
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

或 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` 适用于 2012+ 和更高版本。 对于 2008R2，请使用 `Get-WmiObject`。 此示例演示始终具有 SID `S-1-5-21-*-500` 的内置本地管理员帐户。 基于通用化映像创建的 Azure VM 的本地管理员帐户将重命名为 VM 预配期间指定的名称。 因此，该帐户通常不是 `Administrator`。
#### <a name="add-local-user-to-local-group"></a>将本地用户添加到本地组
`add-localgroupmember -group Administrators -member <username>`
#### <a name="enable-local-user-account"></a>启用本地用户帐户
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

此示例启用始终具有 SID `S-1-5-21-*-500` 的内置本地管理员帐户。 基于通用化映像创建的 Azure VM 的本地管理员帐户将重命名为 VM 预配期间指定的名称。 因此，该帐户通常不是 `Administrator`。
#### <a name="view-user-account-properties"></a>查看用户帐户属性
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

或 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` 适用于 2012+ 和更高版本。 对于 2008R2，请使用 `Get-WmiObject`。 此示例演示始终具有 SID `S-1-5-21-*-500` 的内置本地管理员帐户。
#### <a name="view-local-groups"></a>查看本地组
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` 适用于 2012+ 和更高版本。 对于 2008R2，请使用 `Get-WmiObject`。
### <a name="manage-the-windows-event-log"></a>管理 Windows 事件日志
#### <a name="query-event-log-errors"></a>查询事件日志错误
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

将 `/c:10` 更改为要返回的所需事件数，或者移动它，以返回与筛选器匹配的所有事件。
#### <a name="query-event-log-by-event-id"></a>按事件 ID 查询事件日志
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>按事件 ID 和提供程序查询事件日志
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>按事件 ID 和提供程序查询过去 24 小时的事件日志
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

使用 `604800000` 查看过去 7 天而不是 24 小时的事件。 |
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>按事件 ID、提供程序和 EventData 查询过去 7 天的事件日志
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>查看或删除已安装的应用程序
#### <a name="list-installed-software"></a>列出已安装的软件
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
#### <a name="uninstall-software"></a>卸载软件
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
### <a name="file-system-management"></a>文件系统管理
#### <a name="get-file-version"></a>获取文件版本
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

此示例返回虚拟 NIC 驱动程序（名为 netvsc.sys、netvsc63.sys 或 netvsc60.sys，具体取决于 Windows 版本）的文件版本。
#### <a name="download-and-extract-file"></a>下载并提取文件
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

此示例创建 `c:\bin` 文件夹，然后下载 Sysinternals 工具套件并将其提取到 `c:\bin`。
### <a name="miscellaneous-tasks"></a>其他任务
#### <a name="show-os-version"></a>显示 OS 版本
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
#### <a name="view-os-install-date"></a>查看 OS 安装日期
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
#### <a name="view-last-boot-time"></a>查看上次启动时间
`(get-wmiobject win32_operatingsystem).lastbootuptime`
#### <a name="view-windows-uptime"></a>查看 Windows 运行时间
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

返回 `<days>:<hours>:<minutes>:<seconds>:<milliseconds>` 形式的运行时间，例如 `49:16:48:00.00`。 
#### <a name="restart-windows"></a>重启 Windows
`restart-computer`

添加 `-force` 会强制正在运行的应用程序关闭，且不向用户发出警告。
### <a name="instance-metadata"></a>实例元数据

可以从 Azure VM 内部查询 Azure 实例元数据，以查看 osType、Location、vmSize、vmId、name、resourceGroupName、subscriptionId、privateIpAddress 和 publicIpAddress 等详细信息。

查询实例元数据需要来宾网络连接正常，因为需要通过 Azure 主机向实例元数据服务发出 REST 调用。 因此，如果能够查询实例元数据，则结果会告知来宾能够通过网络来与 Azure 托管服务通信。

有关详细信息，请参阅 [Azure 实例元数据服务](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)。

#### <a name="instance-metadata"></a>实例元数据
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
#### <a name="os-type-instance-metadata"></a>OS 类型（实例元数据）
`$im.Compute.osType`
#### <a name="location-instance-metadata"></a>位置（实例元数据）
`$im.Compute.Location`
#### <a name="size-instance-metadata"></a>大小（实例元数据）
`$im.Compute.vmSize`
#### <a name="vm-id-instance-metadata"></a>VM ID（实例元数据）
`$im.Compute.vmId`
#### <a name="vm-name-instance-metadata"></a>VM 名称（实例元数据）
`$im.Compute.name`
#### <a name="resource-group-name-instance-metadata"></a>资源组名称（实例元数据）
`$im.Compute.resourceGroupName`
#### <a name="subscription-id-instance-metadata"></a>订阅 ID（实例元数据）
`$im.Compute.subscriptionId`
#### <a name="tags-instance-metadata"></a>标记（实例元数据）
`$im.Compute.tags`
#### <a name="placement-group-id-instance-metadata"></a>位置组 ID（实例元数据）
`$im.Compute.placementGroupId`
#### <a name="platform-fault-domain-instance-metadata"></a>平台容错域（实例元数据）
`$im.Compute.platformFaultDomain`
#### <a name="platform-update-domain-instance-metadata"></a>平台更新域（实例元数据）
`$im.Compute.platformUpdateDomain`
#### <a name="ipv4-private-ip-address-instance-metadata"></a>IPv4 专用 IP 地址（实例元数据）
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
#### <a name="ipv4-public-ip-address-instance-metadata"></a>IPv4 公共 IP 地址（实例元数据）
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
#### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>IPv4 子网地址/前缀（实例元数据）
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
#### <a name="ipv6-ip-address-instance-metadata"></a>IPv6 IP 地址（实例元数据）
`$im.network.interface.ipv6.ipAddress`
#### <a name="mac-address-instance-metadata"></a>MAC 地址（实例元数据）
`$im.network.interface.macAddress`

## <a name="errors"></a>Errors
大多数错误都是暂时性的，重试连接即可解决。 下表显示了错误和缓解措施的列表 

错误                            |   缓解措施 
:---------------------------------|:--------------------------------------------|
无法检索“<VMNAME>”的启动诊断设置。 若要使用串行控制台，请确保为此 VM 启用了启动诊断。 | 确保 VM 已启用[启动诊断](boot-diagnostics.md)。 
VM 处于已停止/已解除分配状态。 启动 VM，然后重试串行控制台连接。 | 虚拟机必须处于已启动状态才能访问串行控制台
没有所需的权限，无法使用此 VM 的串行控制台。 请确保至少拥有 VM 参与者角色权限。| 需要特定的访问权限才能访问串行控制台。 有关详细信息，请参阅[访问要求](#prerequisites)
无法确定启动诊断存储帐户“<STORAGEACCOUNTNAME>”的资源组。 确认是否为此 VM 启用了启动诊断，以及是否有权访问此存储帐户。 | 需要特定的访问权限才能访问串行控制台。 有关详细信息，请参阅[访问要求](#prerequisites)

## <a name="known-issues"></a>已知问题 
由于串行控制台访问功能目前仍处于预览阶段，我们正在努力解决一些已知问题。下面是已知问题及其可能的解决方法的列表 

问题                           |   缓解措施 
:---------------------------------|:--------------------------------------------|
没有相应的选项用于访问虚拟机规模集实例的串行控制台 | 在预览期，不支持访问虚拟机规模集实例的串行控制台。
在出现连接标题后按 Enter 不会显示登录提示 | [按 Enter 不起任何作用](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
连接到 Windows VM 时，仅显示运行状况信息| [Windows 运行状况信号](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)
如果已启用内核调试，则无法在 SAC 提示符下键入内容 | 通过 RDP 连接到 VM，并从权限提升的命令提示符运行 `bcdedit /debug {current} off`。 如果无法建立 RDP 连接，可将 OS 磁盘附加到另一个 Azure VM，在该磁盘附加为数据磁盘的情况下使用 `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` 对其进行修改，然后换回磁盘。

## <a name="frequently-asked-questions"></a>常见问题 
**问：如何发送反馈？**

A. 可以访问 https://aka.ms/serialconsolefeedback 来提供问题反馈。 也可以通过 azserialhelp@microsoft.com，或者 http://feedback.azure.com 上的虚拟机类别发送反馈（不太建议） 

**问：我遇到了错误“现有控制台的 OS 类型‘Windows’与请求的 OS 类型‘Linux’相冲突”，为什么？**

A. 这是一个已知问题，若要解决此问题，只需在 bash 模式下打开 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)，然后重试。

**问：我无法访问串行控制台，在哪里可以提出支持案例？**

A. 此预览功能遵守 Azure 预览条款。 最好是通过上面所述的渠道请求此功能的支持。 

## <a name="next-steps"></a>后续步骤
* 串行控制台也适用于 [Linux](../linux/serial-console.md) VM
* 详细了解[启动诊断](boot-diagnostics.md)
