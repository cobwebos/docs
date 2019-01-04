---
title: 按事件 ID 排查 Azure VM RDP 连接问题 | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 4c783c70217a84bbe5ccf15accc4a2bec0b7cca8
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959676"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>按事件 ID 排查 Azure VM RDP 连接问题 

本文介绍如何使用事件 ID 解决阻止远程桌面协议 (RDP) 连接到 Azure 虚拟机 (VM) 的问题。

## <a name="symptoms"></a>症状

尝试使用远程桌面协议 (RDP) 会话连接到 Azure VM。 输入凭据后，连接失败，并收到以下错误消息：

此计算机无法连接到远程计算机。再次尝试连接，如果问题仍然存在，请与远程计算机的所有者或网络管理员联系。

若要解决此问题，请查看 VM 上的事件日志，并参阅以下方案。

## <a name="before-you-troubleshoot"></a>在进行故障排除之前

### <a name="create-a-backup-snapshot"></a>创建备份快照

要创建备份快照，请执行[创建磁盘快照](../windows/snapshot-copy-managed-disk.md)中的步骤。

### <a name="connect-to-the-vm-remotely"></a>远程连接到 VM

若要远程连接到 VM，请使用[如何使用远程工具来解决 Azure VM 问题](remote-tools-troubleshoot-azure-vm-issues.md)中的方法。

## <a name="scenario-1"></a>方案 1

### <a name="event-logs"></a>事件日志

在 CMD 实例中，运行以下命令以检查在过去 24 小时内是否在系统日志中记录了事件 1058 或事件 1057：

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**日志名称：**    系统 <br />
**源：**      Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**日期：**          时间 <br />
**事件 ID：**    1058 <br />
**任务类别：** 无 <br />
**级别：**       错误 <br />
**关键字：**    经典 <br />
**用户：**        不适用 <br />
**计算机：**      计算机 <br />
**说明：** RD 会话主机服务器无法替换 RD 会话主机服务器在 SSL 连接上进行身份验证使用的过期自签名证书。 相关的状态代码为“访问被拒”。

**日志名称：**    系统 <br />
**源：**      Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**日期：**          时间 <br />
**事件 ID：**    1058 <br />
**任务类别：** 无 <br />
**级别：**       错误 <br />
**关键字：**    经典 <br />
**用户：**        不适用 <br />
**计算机：**      计算机 <br />
**说明：** RD 会话主机服务器无法创建 RD 会话主机服务器在 SSL 连接上进行身份验证使用的新自签名证书，相关状态代码为“对象已存在”。

**日志名称：**    系统 <br />
**源：**      Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**日期：**          时间 <br />
**事件 ID：**    1057 <br />
**任务类别：** 无 <br />
**级别：**       错误 <br />
**关键字：**    经典 <br />
**用户：**        不适用 <br />
**计算机：**      计算机 <br />
**说明：** RD 会话主机服务器无法创建 RD 会话主机服务器在 SSL 连接上进行身份验证使用的新自签名证书。 相关状态代码为“不存在 Keyset”

此外可通过运行以下命令检查 SCHANNEL 错误事件 36872 和 36870：

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**日志名称：**    系统 <br />
**源：**      SChannel <br />
**日期：**          — <br />
**事件 ID：**    36870 <br />
**任务类别：** 无 <br />
**级别：**       错误 <br />
**关键字：**       <br />
**用户：**        SYSTEM <br />
**计算机：**      计算机 <br />
**说明：** 尝试访问 SSL 服务器凭据私钥时发生灾难性错误。 加密模块返回的错误代码是 0x8009030D。  <br />
内部错误状态为 10001。

### <a name="cause"></a>原因
出现此问题的原因是无法访问 VM 上 MachineKeys 文件夹中的本地 RSA 加密密钥。 存在以下任一原因时，可能出现此问题：

1. Machinekeys 文件夹或 RSA 文件上的权限配置错误。

2. RSA 密钥已损坏或丢失。

### <a name="resolution"></a>解决方法

若要解决此问题，必须使用这些步骤在 RDP 证书上设置正确的权限。

#### <a name="grant-permission-to-the-machinekeys-folder"></a>授予 MachineKeys 文件夹的权限

1. 使用以下内容创建脚本：

   ```powershell
   remove-module psreadline 
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
   takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
   Restart-Service TermService -Force
   ```

2.  运行此脚本以重置 MachineKey 文件夹的权限并将 RSA 文件重置为默认值。

3.  再次尝试访问 VM。

运行脚本后，可以检查以下遇到权限问题的文件：

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>更新 RDP 自签名证书

如果问题仍然存在，请运行以下脚本，确保更新 RDP 自签名证书：

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

如果无法更新证书，请按照下列步骤尝试删除该证书：

1. 在同一 VNET 中的另一台 VM 上，打开“运行”框，键入“mmc”，然后按“确定”。 

2. 在“文件”菜单中，选择“添加/删除管理单元”。

3. 在“可用的管理单元”列表中，选择“证书”，然后选择“添加”。

4. 选择“计算机帐户”，然后选择“下一步”。

5. 选择“另一台计算机”，然后添加有问题的 VM 的 IP 地址。
   >[!Note]
   >请尝试使用内部网络以避免使用虚拟 IP 地址。

6. 选择“完成”，然后选择“确认”。

   ![选择计算机](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. 展开证书，转到“远程桌面\证书”文件夹，右键单击证书，然后选择“删除”。

8. 重启远程桌面配置服务：

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >此时，如果从 mmc 刷新存储，则证书会再次出现。 

请尝试再次使用 RDP 访问 VM。

#### <a name="update-secure-sockets-layer-ssl-certificate"></a>更新安全套接字层 (SSL) 证书

如果将 VM 设置为使用 SSL 证书，请运行以下命令以获取指纹。 然后检查它是否与证书的指纹相同：

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

如果不同，请更改指纹：

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

此外可尝试删除密钥，以便 RDP 使用 RDP 的自签名证书：

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>方案 2

### <a name="event-log"></a>事件日志

在 CMD 实例中，运行以下命令以检查在过去 24 小时内是否在系统日志中记录了 SCHANNEL 错误事件 36871：

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**日志名称：**    系统 <br />
**源：**      SChannel <br />
**日期：**          — <br />
**事件 ID：**    36871 <br />
**任务类别：** 无 <br />
**级别：**       错误 <br />
**关键字：**       <br />
**用户：**        SYSTEM <br />
**计算机：**      计算机 <br />
**说明：** 创建 TLS 服务器凭据时发生灾难性错误。 内部错误状态为 10013。
 
### <a name="cause"></a>原因

此问题由安全策略造成。 禁用较早版本的 TLS（例如 1.0）时，RDP 访问失败。

### <a name="resolution"></a>解决方法

RDP 默认使用 TLS 1.0 协议。 但是，该协议可能会更改为 TLS 1.1（这是新标准）。

若要解决此问题，请参阅[使用 RDP 连接到 Azure VM 时排查身份验证错误](troubleshoot-authentication-error-rdp-vm.md#tls-version)。

## <a name="scenario-3"></a>方案 3

如果已在 VM 上安装了“远程桌面连接代理”角色，请检查过去 24 小时内是否存在事件 2056 或事件 1296。 在 CMD 实例中，运行以下命令： 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**日志名称：**    Microsoft-Windows-TerminalServices-SessionBroker/Operational <br />
**源：**      Microsoft-Windows-TerminalServices-SessionBroker <br />
**日期：**          时间 <br />
**事件 ID：**    2056 <br />
**任务类别：**(109) <br />
**级别：**       错误 <br />
**关键字：**       <br />
**用户：**        网络服务 <br />
**计算：**      *计算机 fqdn* <br />
**说明：** 无法找到源 Microsoft-Windows-TerminalServices-SessionBroker 的事件 ID 2056 的说明。 未在本地计算机上安装引发此事件的组件，或者安装已损坏。 可在本地计算机上安装或修复该组件。 <br />
如果另一台计算机上生成该事件，则必须保存该事件的显示信息。 <br />
事件中包含以下信息： <br />
Null <br />
Null <br />
登录数据库失败。

**日志名称：**    Microsoft-Windows-TerminalServices-SessionBroker-Client/Operational <br />
**源：**      Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**日期：**          时间 <br />
**事件 ID：**    1296 <br />
**任务类别：**(104) <br />
**级别：**       错误 <br />
**关键字：**       <br />
**用户：**        网络服务 <br />
**计算：**      *计算机 fqdn* <br />
**说明：** 无法找到源 Microsoft-Windows-TerminalServices-SessionBroker-Client 的事件 ID 1296 的说明。 未在本地计算机上安装引发此事件的组件，或者安装已损坏。 可在本地计算机上安装或修复该组件。
如果另一台计算机上生成该事件，则必须保存该事件的显示信息。
事件中包含以下信息：  <br />
*text* <br />
*text* <br />
远程桌面连接代理尚未准备好进行 RPC 通信。

### <a name="cause"></a>原因

出现此问题的原因是远程桌面连接代理服务器的主机名已更改，此更改不受支持。 

主机名具有 Windows 内部数据库的条目和依赖项，这是远程桌面服务场正常工作所必需的。 在场已生成后更改主机名会导致许多错误，并且会使代理服务器停止工作。

### <a name="resolution"></a>解决方法 

若要解决此问题，必须重新安装远程桌面连接代理角色和 Windows 内部数据库。

## <a name="next-steps"></a>后续步骤

[Schannel Events](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)（Schannel 事件）

[Schannel SSP 技术概述](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[RDP Fails with Event ID 1058 & Event 36870 with Remote Desktop Session Host Certificate & SSL Communication](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)（使用远程桌面会话托管证书和 SSL 通信导致 RDP 失败，出现事件 ID 1058 和事件 36870）

[Schannel 36872 or Schannel 36870 on a Domain Controller](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)（域控制器上的 Schannel 36872 或 Schannel 36870）

[Event ID 1058 — Remote Desktop Services Authentication and Encryption](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)（事件 ID 1058 - 远程桌面服务身份验证和加密）

