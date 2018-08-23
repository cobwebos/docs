---
title: Azure Stack syslog 转发
description: 了解如何将 Azure Stack 与通过使用 syslog 转发监视解决方案集成
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 08/14/2018
ms.author: patricka
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: 8e59f2e7e2fceda7f30e12571cd9e2a552f76231
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2018
ms.locfileid: "42139463"
---
# <a name="azure-stack-datacenter-integration---syslog-forwarding"></a>Azure Stack 数据中心集成-syslog 转发

本文介绍如何使用 syslog 与已在你的数据中心中部署的外部安全解决方案集成 Azure Stack 基础结构。 例如，安全信息事件管理 (SIEM) 系统。 Syslog 通道公开审核、 警报和 Azure Stack 基础结构的所有组件中的安全日志。 使用 syslog 转发与安全监视解决方案集成和/或检索所有审核、 警报和安全日志，以将其存储为需保留。 

Azure Stack 从 1805年更新开始，有的集成系统日志客户端，配置完成后，将发出包含有效负载中通用事件格式 (CEF) 的 syslog 消息。 

> [!IMPORTANT]
> Syslog 转发处于预览状态。 它应不依赖在生产环境中。 

下图显示在系统日志集成中参与的主要组件。

![Syslog 转发关系图](media/azure-stack-integrate-security/syslog-forwarding.png)

## <a name="configuring-syslog-forwarding"></a>配置 syslog 转发

在 Azure Stack 中的 syslog 客户端支持以下配置：

1. **通过 TCP 使用相互身份验证 （客户端和服务器） 和 TLS 1.2 加密 Syslog:** 此配置中，在 syslog 服务器和 syslog 客户端可以验证通过证书彼此的身份。 通过 TLS 1.2 加密通道发送消息。

2. **通过 TCP 与服务器身份验证和 TLS 1.2 加密 Syslog:** syslog 客户端可以在此配置中，验证证书通过 syslog 服务器的标识。 通过 TLS 1.2 加密通道发送消息。

3. **通过 TCP，未加密的 Syslog:** syslog 客户端和 syslog 服务器都不在此配置中，验证彼此的身份。 通过 TCP，以明文形式发送消息。

4. **通过 UDP，未加密的 Syslog:** syslog 客户端和 syslog 服务器都不在此配置中，验证彼此的身份。 通过 UDP 以明文形式发送消息。

> [!IMPORTANT]
> Microsoft 强烈建议使用 TCP 使用身份验证和加密 (#1 配置，或者在最低限度下，#2) 用于生产环境，以防止人为干预攻击和窃听的消息。

### <a name="cmdlets-to-configure-syslog-forwarding"></a>Cmdlet 来配置 syslog 转发
配置 syslog 转发需要访问特权终结点 (PEP)。 两个 PowerShell cmdlet 已添加到 PEP 来配置 syslog 转发：


```powershell
### cmdlet to pass the syslog server information to the client and to configure the transport protocol, the encryption and the authentication between the client and the server

Set-SyslogServer [-ServerName <String>] [-NoEncryption] [-SkipCertificateCheck] [-SkipCNCheck] [-UseUDP] [-Remove]

### cmdlet to configure the certificate for the syslog client to authenticate with the server

Set-SyslogClient [-pfxBinary <Byte[]>] [-CertPassword <SecureString>] [-RemoveCertificate] 
```
#### <a name="cmdlets-parameters"></a>Cmdlet 参数

为参数*集 SyslogServer* cmdlet:

| 参数 | 说明 | Type |
|---------|---------| ---------|
| *服务器名称* | Syslog 服务器的 FQDN 或 IP 地址 | String |
|*NoEncryption*| 强制客户端将以明文形式发送的 syslog 消息 | 标志 | 
|*SkipCertificateCheck*| 跳过初始 TLS 握手期间提供 syslog 服务器的证书的验证 | 标志 |
|*SkipCNCheck*| 在初始 TLS 握手期间提供的 syslog 服务器的证书的公用名值跳过验证 | 标志 |
|*UseUDP*| Syslog 与 UDP 用作传输协议 |标志 |
|*删除*| 从客户端中删除服务器的配置并停止 syslog 转发| 标志 |

为参数*集 SyslogClient* cmdlet:
| 参数 | 说明 | Type |
|---------|---------| ---------|
| *pfxBinary* | 包含要用作客户端标识针对 syslog 服务器进行身份验证的证书的 pfx 文件  | Byte[] |
| *CertPassword* |  若要导入与 pfx 文件相关联的私钥的密码 | SecureString |
|*RemoveCertificate* | 从客户端中删除证书 | 标志|

### <a name="configuring-syslog-forwarding-with-tcp-mutual-authentication-and-tls-12-encryption"></a>使用 TCP、 相互身份验证和 TLS 1.2 加密配置 syslog 转发

在此配置中，Azure Stack 中的 syslog 客户端将消息转发到 syslog 服务器通过 TCP、 使用 TLS 1.2 加密。 在初次握手，客户端会验证该服务器提供的有效的、 受信任的证书。同样，客户端还允许到服务器的证书用作其标识的证明。 此配置是标识的最安全的因为它提供了完全验证的客户端和服务器并通过加密通道发送的消息。 

> [!IMPORTANT]
> Microsoft 强烈建议用于生产环境中使用此配置。 

若要配置 TCP、 相互身份验证和 TLS 1.2 加密 syslog 转发，请运行这两个这些 cmdlet:
```powershell
# Configure the server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server>

# Provide certificate to the client to authenticate against the server
Set-SyslogClient -pfxBinary <Byte[] of pfx file> -CertPassword <SecureString, password for accessing the pfx file>
```
客户端证书必须具有在 Azure Stack 部署过程中提供相同的根。 它还必须包含私钥。

```powershell
##Example on how to set your syslog client with the ceritificate for mutual authentication. 
##Run these cmdlets from your hardware lifecycle host or privileged access workstation.

$ErcsNodeName = "<yourPEP>"
$password = ConvertTo-SecureString -String "<your cloudAdmin account password" -AsPlainText -Force
 
$cloudAdmin = "<your cloudAdmin account name>"
$CloudAdminCred = New-Object System.Management.Automation.PSCredential ($cloudAdmin, $password)
 
$certPassword = $password
$certContent = Get-Content -Path C:\cert\<yourClientCertificate>.pfx -Encoding Byte
 
$params = @{ 
    ComputerName = $ErcsNodeName 
    Credential = $CloudAdminCred 
    ConfigurationName = "PrivilegedEndpoint" 
}

$session = New-PSSession @params
 
$params = @{ 
    Session = $session 
    ArgumentList = @($certContent, $certPassword) 
}
Write-Verbose "Invoking cmdlet to set syslog client certificate..." -Verbose 
Invoke-Command @params -ScriptBlock { 
    param($CertContent, $CertPassword) 
    Set-SyslogClient -PfxBinary $CertContent -CertPassword $CertPassword 
```

### <a name="configuring-syslog-forwarding-with-tcp-server-authentication-and-tls-12-encryption"></a>使用 TCP，配置 syslog 转发服务器身份验证和 TLS 1.2 加密

在此配置中，Azure Stack 中的 syslog 客户端将消息转发到 syslog 服务器通过 TCP、 使用 TLS 1.2 加密。 在初次握手，客户端会还验证该服务器提供有效的受信任证书。 这可以防止客户端将消息发送到不受信任的目标。
使用身份验证和加密的 TCP 的默认配置，表示最小的 Microsoft 建议为生产环境的安全级别。 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server>
```

如果你想要通过使用自签名和/或不受信任的证书来测试与 Azure Stack 客户端的 syslog 服务器的集成，可以使用这些标志来跳过服务器验证客户端在初次握手期间执行。

```powershell
 #Skip validation of the Common Name value in the server certificate. Use this flag if you provide an IP address for your syslog server
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -SkipCNCheck
 
 #Skip entirely the server certificate validation
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -SkipCertificateCheck
```
> [!IMPORTANT]
> Microsoft 不提倡使用-SkipCertificateCheck 标志用于生产环境。 


### <a name="configuring-syslog-forwarding-with-tcp-and-no-encryption"></a>使用 TCP 和任何加密配置 syslog 转发

在此配置中，Azure Stack 中的 syslog 客户端将消息转发到 syslog 服务器通过 TCP，未加密。 客户端不会验证服务器的标识，也不它提供其自己的标识与服务器进行验证。 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -NoEncryption
```
> [!IMPORTANT]
> Microsoft 建议不要用于生产环境中使用此配置。 


### <a name="configuring-syslog-forwarding-with-udp-and-no-encryption"></a>使用 UDP 和未加密配置 syslog 转发

在此配置中，Azure Stack 中的 syslog 客户端将消息转发到 syslog 服务器通过 UDP，未加密。 客户端不会验证服务器的标识，也不它提供其自己的标识与服务器进行验证。 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -UseUDP
```
尽管未加密的 UDP 是最容易配置，它不提供任何保护它们免受拦截的攻击和窃听的消息。 

> [!IMPORTANT]
> Microsoft 建议不要用于生产环境中使用此配置。 


## <a name="removing-syslog-forwarding-configuration"></a>删除 syslog 转发配置

若要完全删除 syslog 服务器配置并停止 syslog 转发：

**从客户端中删除 syslog 服务器配置**

```PowerShell  
Set-SyslogServer -Remove
```

**从客户端中删除客户端证书**

```PowerShell  
Set-SyslogClient -RemoveCertificate
```

## <a name="verifying-the-syslog-setup"></a>验证 syslog 设置

如果您已成功连接到 syslog 服务器系统日志客户端，您很快就应开始接收事件。 如果看不到任何事件，通过运行以下 cmdlet 来验证 syslog 客户端，配置：

**验证 syslog 客户端中的服务器配置**

```PowerShell  
Get-SyslogServer
```

**验证 syslog 客户端中的证书设置**

```PowerShell  
Get-SyslogClient
```

## <a name="syslog-message-schema"></a>Syslog 消息架构

在 Azure Stack 基础结构 syslog 转发发送的消息格式的通用事件格式 (CEF)。
每个 syslog 消息结构根据此架构： 

```Syslog
<Time> <Host> <CEF payload>
```

CEF 有效负载为基础结构，但每个字段的映射取决于消息的类型 （Windows 事件创建警报，警报已关闭）。

```CEF
# Common Event Format schema
CEF: <Version>|<Device Vendor>|<Device Product>|<Device Version>|<Signature ID>|<Name>|<Severity>|<Extensions>
* Version: 0.0 
* Device Vendor: Microsoft
* Device Product: Microsoft Azure Stack
* Device Version: 1.0
```

### <a name="cef-mapping-for-windows-events"></a>CEF 映射的 Windows 事件
```
* Signature ID: ProviderName:EventID
* Name: TaskName
* Severity: Level (for details, see the severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Windows 事件的严重等级列表： 
| CEF 的严重性值 | Windows 事件级别 | 数字值 |
|--------------------|---------------------| ----------------|
|0|Undefined|值： 0。 指示所有级别的日志|
|10|严重|值： 1。 指示日志的严重警报|
|8|错误| 值： 2。 指示错误日志|
|5|警告|值： 3。 指示警告的日志|
|2|信息|值： 4。 指示信息性消息的日志|
|0|详细|值： 5。 指示所有级别的日志|

在 Azure Stack 中的 Windows 事件的自定义扩展表：
| 自定义扩展插件名称 | Windows 事件示例 | 
|-----------------------|---------|
|MasChannel | 系统|
|MasComputer | test.azurestack.contoso.com|
|MasCorrelationActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasCorrelationRelatedActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasEventData| svchost!!4132，G，0!!!EseDiskFlushConsistency!!ESENT!!0x800000|
|MasEventDescription| 用户的组策略设置都已成功处理。 没有检测到因为组策略的最后一个成功处理的更改。|
|MasEventID|1501|
|MasEventRecordID|26637|
|MasExecutionProcessID | 29380|
|MasExecutionThreadID |25480|
|MasKeywords |0x8000000000000000|
|MasKeywordName |审核成功|
|MasLevel |4|
|MasOpcode |1|
|MasOpcodeName |info|
|MasProviderEventSourceName ||
|MasProviderGuid |AEA1B4FA-97D1-45F2-A64C-4D69FFFD92C9|
|MasProviderName |组--策略 Microsoft Windows|
|MasSecurityUserId |\<Windows SID\> |
|MasTask |0|
|MasTaskCategory| 进程创建|
|MasUserData|KB4093112!!5112!!安装!!0x0!!WindowsUpdateAgent Xpath: /Event/UserData / *|
|MasVersion|0|

### <a name="cef-mapping-for-alerts-created"></a>CEF 映射创建的警报
```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Alert Severity (for details, see alerts severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```
警报严重性表：
| Severity | 级别 |
|----------|-------|
|0|Undefined|
|10|严重|
|5|警告|

在 Azure Stack 中创建的警报的自定义扩展表：
| 自定义扩展插件名称 | 示例 | 
|-----------------------|---------|
|MasEventDescription|说明： 用户帐户\<TestUser\>为创建\<TestDomain\>。 它是潜在的安全风险。 -修正： 请联系支持人员。 自定义帮助需要解决此问题。 不要尝试解决此问题，而无需其帮助。 打开支持请求之前，请启动日志文件收集过程使用的指导原则 https://aka.ms/azurestacklogfiles |

### <a name="cef-mapping-for-alerts-closed"></a>CEF 映射为关闭的警报的
```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Information
```

下面的示例显示了具有 CEF 有效负载的 syslog 消息：
```
2018:05:17:-23:59:28 -07:00 TestHost CEF:0.0|Microsoft|Microsoft Azure Stack|1.0|3|TITLE: User Account Created -- DESCRIPTION: A user account \<TestUser\> was created for \<TestDomain\>. It's a potential security risk. -- REMEDIATION: Please contact Support. Customer Assistance is required to resolve this issue. Do not try to resolve this issue without their assistance. Before you open a support request, start the log file collection process using the guidance from https://aka.ms/azurestacklogfiles|10
```
## <a name="next-steps"></a>后续步骤

[服务策略](azure-stack-servicing-policy.md)