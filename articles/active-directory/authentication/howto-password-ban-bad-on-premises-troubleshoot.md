---
title: Troubleshooting password protection - Azure Active Directory
description: Understand Azure AD password protection common troubleshooting
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06169056c9b17abfda2af67ec0784dfbf2ed04d5
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381648"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Azure AD 密码保护故障排除

部署 Azure AD 密码保护后，可能需要进行故障排除。 本文详细介绍如何帮助你了解一些常见的故障排除步骤。

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>The DC agent cannot locate a proxy in the directory

The main symptom of this problem is 30017 events in the DC agent Admin event log.

The usual cause of this issue is that a proxy has not yet been registered. If a proxy has been registered, there may be some delay due to AD replication latency until a particular DC agent is able to see that proxy.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>The DC agent is not able to communicate with a proxy

The main symptom of this problem is 30018 events in the DC agent Admin event log. This problem may have several possible causes:

1. The DC agent is located in an isolated portion of the network that does not allow network connectivity to the registered proxy(s). This problem may be benign as long as other DC agents can communicate with the proxy(s) in order to download password policies from Azure. Once downloaded, those policies will then be obtained by the isolated DC via replication of the policy files in the sysvol share.

1. The proxy host machine is blocking access to the RPC endpoint mapper endpoint (port 135)

   The Azure AD Password Protection Proxy installer automatically creates a Windows Firewall inbound rule that allows access to port 135. If this rule is later deleted or disabled, DC agents will be unable to communicate with the Proxy service. If the builtin Windows Firewall has been disabled in lieu of another firewall product, you must configure that firewall to allow access to port 135.

1. The proxy host machine is blocking access to the RPC endpoint (dynamic or static) listened on by the Proxy service

   The Azure AD Password Protection Proxy installer automatically creates a Windows Firewall inbound rule that allows access to any inbound ports listened to by the Azure AD Password Protection Proxy service. If this rule is later deleted or disabled, DC agents will be unable to communicate with the Proxy service. If the builtin Windows Firewall has been disabled in lieu of another firewall product, you must configure that firewall to allow access to any inbound ports listened to by the Azure AD Password Protection Proxy service. This configuration may be made more specific if the Proxy service has been configured to listen on a specific static RPC port (using the `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet).

1. The proxy host machine is not configured to allow domain controllers the ability to log on to the machine. This behavior is controlled via the "Access this computer from the network" user privilege assignment. All domain controllers in all domains in the forest must be granted this privilege. This setting is often constrained as part of a larger network hardening effort.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Proxy service is unable to communicate with Azure

1. Ensure the proxy machine has connectivity to the endpoints listed in the [deployment requirements](howto-password-ban-bad-on-premises-deploy.md).

1. Ensure that the forest and all proxy servers are registered against the same Azure tenant.

   You can check this requirement by running the  `Get-AzureADPasswordProtectionProxy` and `Get-AzureADPasswordProtectionDCAgent` PowerShell cmdlets, then compare the `AzureTenant` property of each returned item. For correct operation, the reported tenant name must be the same across all DC agents and proxy servers.

   If an Azure tenant registration mismatch condition does exist, this problem can be fixed by running the `Register-AzureADPasswordProtectionProxy` and/or `Register-AzureADPasswordProtectionForest` PowerShell cmdlets as needed, making sure to use credentials from the same Azure tenant for all registrations.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>DC agent is unable to encrypt or decrypt password policy files

Azure AD Password Protection has a critical dependency on the encryption and decryption functionality supplied by the Microsoft Key Distribution Service. Encryption or decryption failures can manifest with a variety of symptoms and have several potential causes.

1. Ensure that the KDS service is enabled and functional on all Windows Server 2012 and later domain controllers in a domain.

   By default the KDS service's service start mode is configured as Manual (Trigger Start). This configuration means that the first time a client tries to use the service, it is started on-demand. This default service start mode is acceptable for Azure AD Password Protection to work.

   If the KDS service start mode has been configured to Disabled, this configuration must be fixed before Azure AD Password Protection will work properly.

   A simple test for this issue is to manually start the KDS service, either via the Service management MMC console, or using other management tools (for example, run "net start kdssvc" from a command prompt console). The KDS service is expected to start successfully and stay running.

   The most common root cause for the KDS service being unable to start is that the Active Directory domain controller object is located outside of the default Domain Controllers OU. This configuration is not supported by the KDS service and is not a limitation imposed by Azure AD Password Protection. The fix for this condition is to move the domain controller object to a location under the default Domain Controllers OU.

1. Incompatible KDS encrypted buffer format change from Windows Server 2012 R2 to Windows Server 2016

   A KDS security fix was introduced in Windows Server 2016 that modifies the format of KDS encrypted buffers; these buffers will sometimes fail to decrypt on Windows Server 2012 and Windows Server 2012 R2. The reverse direction is okay - buffers that are KDS-encrypted on Windows Server 2012 and Windows Server 2012 R2 will always successfully decrypt on Windows Server 2016 and later. If the domain controllers in your Active Directory domains are running a mix of these operating systems, occasional Azure AD Password Protection decryption failures may be reported. It is not possible to accurately predict the timing or symptoms of these failures given the nature of the security fix, and given that it is non-deterministic which Azure AD Password Protection DC Agent on which domain controller will encrypt data at a given time.

   Microsoft is investigating a fix for this issue but no ETA is available yet. In the meantime, there is no workaround for this issue other than to not run a mix of these incompatible operating systems in your Active Directory domain(s). In other words, you should run only Windows Server 2012 and Windows Server 2012 R2 domain controllers, OR you should only run Windows Server 2016 and above domain controllers.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Weak passwords are being accepted but should not be

This problem may have several causes.

1. Your DC agent(s) are running a public preview software version that has expired. See [Public preview DC agent software has expired](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).

1. Your DC agent(s) cannot download a policy or is unable to decrypt existing policies. Check for possible causes in the above topics.

1. 密码策略强制模式仍设置为“审核”。 If this configuration is in effect, reconfigure it to Enforce using the Azure AD Password Protection portal. See [Enable Password protection](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. 密码策略已被禁用。 If this configuration is in effect, reconfigure it to enabled using the Azure AD Password Protection portal. See [Enable Password protection](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. You have not installed the DC agent software on all domain controllers in the domain. In this situation, it is difficult to ensure that remote Windows clients target a particular domain controller during a password change operation. If you think you have successfully targeted a particular DC where the DC agent software is installed, you can verify by double-checking the DC agent admin event log: regardless of outcome, there will be at least one event to document the outcome of the password validation. If there is no event present for the user whose password is changed, then the password change was likely processed by a different domain controller.

   As an alternative test, try setting\changing passwords while logged in directly to a DC where the DC agent software is installed. This technique is not recommended for production Active Directory domains.

   While incremental deployment of the DC agent software is supported subject to these limitations, Microsoft strongly recommends that the DC agent software is installed on all domain controllers in a domain as soon as possible.

1. The password validation algorithm may actually be working as expected. See [How are passwords evaluated](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe fails to set a weak DSRM password

Active Directory will always validate a new Directory Services Repair Mode password to make sure it meets the domain's password complexity requirements; this validation also calls into password filter dlls like Azure AD Password Protection. If the new DSRM password is rejected, the following error message results:

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

When Azure AD Password Protection logs the password validation event log event(s) for an Active Directory DSRM password, it is expected that the event log messages will not include a user name. This behavior occurs because the DSRM account is a local account that is not part of the actual Active Directory domain.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>Domain controller replica promotion fails because of a weak DSRM password

During the DC promotion process, the new Directory Services Repair Mode password will be submitted to an existing DC in the domain for validation. If the new DSRM password is rejected, the following error message results:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

Just like in the above issue, any Azure AD Password Protection password validation outcome event will have empty user names for this scenario.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>Domain controller demotion fails due to a weak local Administrator password

支持降级仍在运行 DC 代理软件的域控制器。 但管理员应注意，在降级过程中，DC 代理软件会继续实施当前密码策略。 将像验证其他任何密码一样验证（降级操作过程中指定的）新本地管理员帐户密码。 Microsoft recommends that secure passwords be chosen for local Administrator accounts as part of a DC demotion procedure.

成功降级后，如果域控制器已重新启动并再次以普通成员服务器的形式运行，则 DC 代理软件将恢复为以被动模式运行。 然后，随时可将其卸载。

## <a name="booting-into-directory-services-repair-mode"></a>Booting into Directory Services Repair Mode

If the domain controller is booted into Directory Services Repair Mode, the DC agent password filter dll detects this condition and will cause all password validation or enforcement activities to be disabled, regardless of the currently active policy configuration. The DC agent password filter dll will log a 10023 warning event to the Admin event log, for example:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>Public preview DC agent software has expired

During the Azure AD Password Protection public preview period, the DC agent software was hard-coded to stop processing password validation requests on the following dates:

* Version 1.2.65.0 will stop processing password validation requests on September 1 2019.
* Version 1.2.25.0 and prior stopped processing password validation requests on July 1 2019.

As the deadline approaches, all time-limited DC agent versions will emit a 10021 event in the DC agent Admin event log at boot time that looks like this:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

Once the deadline has passed, all time-limited DC agent versions will emit a 10022 event in the DC agent Admin event log at boot time that looks like this:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Since the deadline is only checked on initial boot, you may not see these events until long after the calendar deadline has passed. Once the deadline has been recognized, no negative effects on either the domain controller or the larger environment will occur other than all passwords will be automatically approved.

> [!IMPORTANT]
> Microsoft recommends that expired public preview DC agents be immediately upgraded to the latest version.

An easy way to discover DC agents in your environment that need to be upgrade is by running the `Get-AzureADPasswordProtectionDCAgent` cmdlet, for example:

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

For this topic, the SoftwareVersion field is obviously the key property to look at. You can also use PowerShell filtering to filter out DC agents that are already at or above the required baseline version, for example:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

The Azure AD Password Protection Proxy software is not time-limited in any version. Microsoft still recommends that both DC and proxy agents be upgraded to the latest versions as they are released. The `Get-AzureADPasswordProtectionProxy` cmdlet may be used to find Proxy agents that require upgrades, similar to the example above for DC agents.

Refer to [Upgrading the DC agent](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) and [Upgrading the Proxy agent](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-agent) for more details on specific upgrade procedures.

## <a name="emergency-remediation"></a>紧急补救

如果 DC 代理服务造成了问题，可以立即关闭 DC 代理服务。 DC 代理密码筛选器 dll 仍会尝试调用未运行的服务并记录警告事件（10012、10013），但在此期间会接受所有传入的密码。 然后，也可以通过 Windows 服务控制管理器并根据需要使用启动类型“Disabled”来配置 DC 代理服务。

另一种补救措施是在 Azure AD 密码保护门户中将“启用”模式设置为“否”。 下载更新的策略后，各 DC 代理服务将进入静默模式，在这种模式下，将按原样接受所有密码。 有关详细信息，请参阅[强制模式](howto-password-ban-bad-on-premises-operations.md#enforce-mode)。

## <a name="removal"></a>删除

If it is decided to uninstall the Azure AD password protection software and cleanup all related state from the domain(s) and forest, this task can be accomplished using the following steps:

> [!IMPORTANT]
> 必须按顺序执行这些步骤。 如果代理服务的任何实例仍在运行，它会定期重新创建其 serviceConnectionPoint 对象。 如果 DC 代理服务的任何实例仍在运行，它会定期重新创建其 serviceConnectionPoint 对象和 sysvol 状态。

1. 从所有计算机中卸载代理软件。 完成此步骤后**不需要**重新启动。
2. 从所有域控制器中卸载 DC 代理软件。 完成此步骤后**需要**重新启动。
3. 在每个域命名上下文中手动删除所有代理服务连接点。 可使用以下 Active Directory PowerShell 命令发现这些对象的位置：

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   不要省略 $keywords 变量值末尾的星号（“*”）。

   然后，可通过管道将 `Get-ADObject` 命令找到的结果对象传送到 `Remove-ADObject`，或手动将其删除。

4. 在每个域命名上下文中手动删除所有 DC 代理连接点。 There may be one these objects per domain controller in the forest, depending on how widely the software was deployed. 可使用以下 Active Directory PowerShell 命令发现该对象的位置：

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   然后，可通过管道将 `Get-ADObject` 命令找到的结果对象传送到 `Remove-ADObject`，或手动将其删除。

   不要省略 $keywords 变量值末尾的星号（“*”）。

5. 手动删除林级配置状态。 林配置状态保留在 Active Directory 配置命名上下文中的某个容器内。 可按如下所示发现和删除该状态：

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. 通过手动删除以下文件夹及其所有内容，手动删除 sysvol 相关的所有状态：

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   如果需要，也可以在给定域控制器的本地访问此路径；默认位置类似于以下路径：

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   如果在非默认位置配置了 sysvol 共享，则此路径不同。

## <a name="next-steps"></a>后续步骤

[Azure AD 密码保护的常见问题解答](howto-password-ban-bad-on-premises-faq.md)

有关全局和自定义禁止密码列表的详细信息，请参阅[禁止错误的密码](concept-password-ban-bad.md)一文
