---
title: 使用 RDP 连接到 Azure VM 时排查身份验证错误 | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: b7a561907e3f1968eb9adead3606822d7a1321c8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266970"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>使用 RDP 连接到 Azure VM 时排查身份验证错误

你可通过本文解决使用远程桌面协议 (RDP) 连接来连接到 Azure 虚拟机 (VM) 时出现的身份验证错误。

## <a name="symptoms"></a>症状

捕获 Azure VM 的屏幕截图，其中显示了欢迎屏幕并指示操作系统正在运行。 但尝试使用远程桌面连接连接到 VM 时，将收到以下错误消息之一。

### <a name="error-message-1"></a>错误消息 1

**出现身份验证错误。无法联系本地安全机构。**

### <a name="error-message-2"></a>错误消息 2

**你尝试连接到的远程计算机需要网络级别身份验证（NLA），但无法联系你的 Windows 域控制器来执行 NLA。如果你是远程计算机上的管理员，则可以使用 "系统属性" 对话框的 "远程" 选项卡上的选项来禁用 NLA。**

### <a name="error-message-3-generic-connection-error"></a>错误消息 3（泛型连接错误）

**此计算机无法连接到远程计算机。再次尝试连接，如果问题仍然存在，请与远程计算机的所有者或网络管理员联系。**

## <a name="cause"></a>原因

NLA 可能阻止对 VM 的 RDP 访问有多种原因。

### <a name="cause-1"></a>原因 1

VM 无法与域控制器 (DC) 通信。 此问题可能会阻止 RDP 会话使用域凭据访问 VM。 但是，你仍可以使用本地管理员凭据登录。 在以下情况下可能会发生此问题：

1. 此 VM 与 DC 之间的 Active Directory 安全通道已损坏。

2. VM 的帐户密码为旧副本，而 DC 的为新副本。

3. 此 VM 连接的 DC 运行不正常。

### <a name="cause-2"></a>原因 2

VM 的加密级别高于客户端计算机使用的加密级别。

### <a name="cause-3"></a>原因 3

VM 上禁用了 TLS 1.0、1.1 或 1.2（服务器）协议。

### <a name="cause-4"></a>原因 4

VM 已设置为使用域凭据禁用登录，并且本地安全机构 (LSA) 设置不正确。

### <a name="cause-5"></a>原因 5

VM 被设置为仅接受符合美国联邦信息处理标准 (FIPS) 的算法连接。 这通常通过使用 Active Directory 策略来完成。 这是一种罕见的配置，但只能对远程桌面连接强制执行 FIPS。

## <a name="before-you-troubleshoot"></a>在进行故障排除之前

### <a name="create-a-backup-snapshot"></a>创建备份快照

要创建备份快照，请执行[创建磁盘快照](../windows/snapshot-copy-managed-disk.md)中的步骤。

### <a name="connect-to-the-vm-remotely"></a>远程连接到 VM

要远程连接到 VM，请使用[如何使用远程工具来解决 Azure VM 问题](remote-tools-troubleshoot-azure-vm-issues.md)中的某种方法。

### <a name="group-policy-client-service"></a>组策略客户端服务

如果这是加入域的 VM，请首先停止组策略客户端服务，以防止任何 Active Directory 策略覆盖更改。 为此，请运行以下命令：

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

解决问题后，还原此 VM 与域联系以从域中检索最新 GPO 的功能。 为此，请运行以下命令：

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

如果已还原更改，则表示是 Active Directory 策略导致的问题。 

### <a name="workaround"></a>解决方法

要解决此问题，请在命令窗口中运行以下命令以禁用 NLA：

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

然后，重启 VM。

要重新启用 NLA，请运行以下命令，然后重启 VM：

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>故障排除

### <a name="for-domain-joined-vms"></a>对于加入域的 VM

要解决此问题，请首先检查 VM 是否可以连接到 DC，以及 DC 是否处于“正常”状态并且可以处理来自 VM 的请求。

>[!Note] 
>要测试 DC 运行状况，可以在共享同一登录服务器的同一 VNET 和子网上使用另一个 VM。

根据“远程连接到 VM”部分中的步骤，使用串行控制台、远程 CMD 或远程 PowerShell 连接到出现问题的 VM。

要确定 VM 连接到的 DC，请在控制台中运行以下命令： 

```cmd
set | find /i "LOGONSERVER"
```

然后，检查 VM 和 DC 之间的安全通道的运行状况。 为此，请在已提升权限的 PowerShell 实例中运行以下命令。 此命令返回一个布尔标志，指示安全通道是否处于活动状态：

```powershell
Test-ComputerSecureChannel -verbose
```

如果通道损坏，请运行以下命令进行修复：

```powershell
Test-ComputerSecureChannel -repair
```

确保在 VM 和 DC 上更新 Active Directory 中的计算机帐户密码：

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

如果 DC 和 VM 之间的通信良好，但 DC 的运行状况不佳而无法打开 RDP 会话，则可以尝试重启 DC。

如果上述命令未能修复到域的通信问题，则可以将此 VM 重新加入域。 为此，请按照下列步骤进行操作：

1. 使用以下内容创建名为 Unjoin.ps1 的脚本，然后将该脚本部署为 Azure 门户上的自定义脚本扩展：

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    此脚本强制将 VM 从域中取出并在 10 秒后重启。 然后，必须清理域侧的 Computer 对象。

2.  清理完成后，将此 VM 重新加入域。 为此，请使用以下内容创建名为 JoinDomain.ps1 的脚本，然后将该脚本部署为 Azure 门户上的自定义脚本扩展： 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >这将使用指定的凭据加入域上的 VM。

如果 Active Directory 通道处于正常状态，计算机密码已更新，并且域控制器按预期工作，请尝试以下步骤。

如果问题仍然存在，请检查域凭据是否已禁用。 为此，请打开提升的命令提示符窗口，然后运行以下命令以确定 VM 是否已设置为禁用用于登录 VM 的域帐户：

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

如果此项设置为“1”，则表示服务器已设置为不允许使用域凭据。 请将此项更改为“0”。

### <a name="for-standalone-vms"></a>对于独立 VM

#### <a name="check-minencryptionlevel"></a>检查 MinEncryptionLevel

在 CMD 实例中，运行以下命令以查询 MinEncryptionLevel 注册表值：

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

根据注册表值，按照下列步骤操作：

* 4 (FIPS)：转到[检查符合 FIP 的算法连接](#fips-compliant)。

* 3（128 位加密）：通过运行以下命令将严重性设置为“2”：

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2（可能的最高加密，由客户端指定）：你可以尝试通过运行以下命令将加密设置为最小值“1”：

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
重启 VM，使对注册表的更改生效。

#### <a name="tls-version"></a>TLS 版本

根据系统，RDP 使用 TLS 1.0、1.1 或 1.2（服务器）协议。 要查询在 VM 上如何设置这些协议，请打开 CMD 实例，然后运行以下命令：

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

如果返回值不全是“1”，则表示协议已禁用。 要启用这些协议，请运行以下命令：

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

对于其他协议版本，可运行以下命令：

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> 从来宾操作系统日志获取 SCHANNEL 错误的 SSH/TLS 版本 x.x.

#### <a name="fips-compliant"></a>检查符合 FIP 的算法连接

可以强制执行远程桌面以仅使用符合 FIP 的算法连接。 可使用注册表项设置该操作。 为此，请打开提升的命令提示符窗口，然后查询以下项：

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

如果该命令返回“1”，请将注册表值更改为“0”。

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

检查 VM 上当前的 MinEncryptionLevel：

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

如果该命令返回“4”，请将注册表值更改为“2”

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

重启 VM，使对注册表的更改生效。

## <a name="next-steps"></a>后续步骤

[Win32_TSGeneralSetting 类的 SetEncryptionLevel 方法](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[配置服务器身份验证和加密级别](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Win32_TSGeneralSetting 类](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
