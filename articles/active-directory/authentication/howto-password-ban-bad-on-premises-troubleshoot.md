---
title: 解决本地 Azure AD 密码保护故障
description: 了解如何为本地活动目录域服务环境解决 Azure AD 密码保护问题
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79ebf543a3880a4f2c8ee8c0d706c268ef3f08d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263642"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>故障排除：本地 Azure AD 密码保护

部署 Azure AD 密码保护后，可能需要进行故障排除。 本文详细介绍如何帮助你了解一些常见的故障排除步骤。

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>DC 代理无法在目录中找到代理

此问题的主要症状是 DC 代理管理事件日志中的 30017 事件。

此问题的通常原因是代理尚未注册。 如果代理已注册，则由于 AD 复制延迟，可能会出现一些延迟，直到特定 DC 代理能够看到该代理。

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>DC 代理无法与代理通信

此问题的主要症状是 DC 代理管理事件日志中的 30018 事件。 此问题可能有几个可能的原因：

1. DC 代理位于网络的隔离部分，不允许与注册代理进行网络连接。 只要其他 DC 代理可以与代理通信以便从 Azure 下载密码策略，此问题可能是良性的。 下载后，这些策略将由隔离的 DC 通过复制 sysvol 共享中的策略文件获得。

1. 代理主机正在阻止对 RPC 终结点映射器终结点（端口 135）的访问

   Azure AD 密码保护代理安装程序会自动创建允许访问端口 135 的 Windows 防火墙入站规则。 如果以后删除或禁用此规则，则 DC 代理将无法与代理服务通信。 如果内置 Windows 防火墙已禁用以替代其他防火墙产品，则必须配置该防火墙以允许访问端口 135。

1. 代理主机阻止对代理服务侦听的 RPC 终结点（动态或静态）的访问

   Azure AD 密码保护代理安装程序会自动创建 Windows 防火墙入站规则，该规则允许访问 Azure AD 密码保护代理服务侦听的任何入站端口。 如果以后删除或禁用此规则，则 DC 代理将无法与代理服务通信。 如果内置 Windows 防火墙已禁用以替代其他防火墙产品，则必须配置该防火墙以允许访问 Azure AD 密码保护代理服务侦听的任何入站端口。 如果代理服务已配置为侦听特定的静态 RPC 端口（使用`Set-AzureADPasswordProtectionProxyConfiguration`cmdlet），则此配置可能会更加具体。

1. 代理主机未配置为允许域控制器登录到计算机。 此行为通过"从网络访问此计算机"用户权限分配进行控制。 必须授予林中的所有域控制器此权限。 此设置通常受限于更大的网络强化工作。

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>代理服务无法与 Azure 通信

1. 确保代理计算机与[部署要求](howto-password-ban-bad-on-premises-deploy.md)中列出的终结点具有连接。

1. 确保林和所有代理服务器都针对同一 Azure 租户进行注册。

   您可以通过运行`Get-AzureADPasswordProtectionProxy`和`Get-AzureADPasswordProtectionDCAgent`PowerShell cmdlet 来检查此要求，然后`AzureTenant`比较每个返回项的属性。 为了正确操作，报告的所有 DC 代理和代理服务器中的租户名称必须相同。

   如果 Azure 租户注册不匹配条件确实存在，则可以根据需要运行`Register-AzureADPasswordProtectionProxy`和/或`Register-AzureADPasswordProtectionForest`PowerShell cmdlet 来解决此问题，确保对所有注册使用来自同一 Azure 租户的凭据。

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>DC 代理无法加密或解密密码策略文件

Azure AD 密码保护依赖于 Microsoft 密钥分发服务提供的加密和解密功能。 加密或解密失败可能表现为各种症状，并有多种潜在原因。

1. 确保 KDS 服务在域中的所有 Windows Server 2012 和更高版本的域控制器上启用并正常工作。

   默认情况下，KDS 服务的服务启动模式配置为手动（触发启动）。 此配置意味着客户端首次尝试使用服务时，会按需启动该服务。 Azure AD 密码保护可使用此默认服务启动模式。

   如果 KDS 服务启动模式已配置为禁用，则必须修复此配置，然后才能正常工作。

   此问题的简单测试是通过服务管理 MMC 控制台或使用其他管理工具（例如，从命令提示控制台运行"网络启动 kdsvc"）手动启动 KDS 服务。 KDS 服务预期已成功启动并保持运行。

   KDS 服务无法启动的最常见根本原因是 Active Directory 域控制器对象位于默认域控制器 OU 之外。 KDS 服务不支持此配置，并且不是 Azure AD 密码保护施加的限制。 此条件的解决方法是将域控制器对象移动到默认域控制器 OU 下的位置。

1. 不兼容的 KDS 加密缓冲区格式从 Windows 服务器 2012 R2 更改为 Windows 服务器 2016

   Windows Server 2016 引入了 KDS 安全修补程序，该修补程序修改了 KDS 加密缓冲区的格式;这些缓冲区有时无法在 Windows 服务器 2012 和 Windows 服务器 2012 R2 上解密。 相反的方向是好的 - 在 Windows 服务器 2012 和 Windows Server 2012 R2 上加密的 KDS 缓冲区将始终在 Windows Server 2016 和更高版本上成功解密。 如果 Active Directory 域中的域控制器运行这些操作系统的组合，则可能会报告偶尔出现 Azure AD 密码保护解密失败。 鉴于安全修补程序的性质，并且由于 Azure AD 密码保护 DC 代理在给定时间对哪个域控制器进行加密数据，因此无法准确预测这些故障的时间或症状。

   微软正在调查此问题的修复程序，但尚未提供 ETA。 同时，除了在 Active Directory 域中不运行这些不兼容操作系统的组合之外，没有解决此问题的解决方法。 换句话说，您应该只运行 Windows Server 2012 和 Windows Server 2012 R2 域控制器，或者只应运行 Windows Server 2016 和域控制器以上。

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>正在接受弱密码，但不应

此问题可能有多种原因。

1. 您的 DC 代理正在运行已过期的公共预览软件版本。 请参阅[公共预览 DC 代理软件已过期](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired)。

1. 您的 DC 代理无法下载策略或无法解密现有策略。 检查上述主题中可能存在的原因。

1. 密码策略强制模式仍设置为“审核”。 如果此配置有效，请将其重新配置为使用 Azure AD 密码保护门户强制执行。 有关详细信息，请参阅[操作模式](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)。

1. 密码策略已被禁用。 如果此配置有效，请使用 Azure AD 密码保护门户将其重新配置为启用。 有关详细信息，请参阅[操作模式](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)。

1. 您尚未在域中的所有域控制器上安装 DC 代理软件。 在此情况下，很难确保远程 Windows 客户端在密码更改操作期间针对特定域控制器。 如果您认为自己已成功定位安装了 DC 代理软件的特定 DC，则可以通过仔细检查 DC 代理管理事件日志进行验证：无论结果如何，将至少有一个事件来记录密码的结果验证。 如果密码更改的用户不存在事件，则密码更改可能由其他域控制器处理。

   作为替代测试，请尝试在直接登录到安装 DC 代理软件的 DC 时设置更改密码。 不建议用于生产活动目录域。

   虽然受这些限制支持 DC 代理软件的增量部署，但 Microsoft 强烈建议尽快将 DC 代理软件安装在域中的所有域控制器上。

1. 密码验证算法实际上可能按预期工作。 请参阅[如何计算密码](concept-password-ban-bad.md#how-are-passwords-evaluated)。

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe 无法设置弱 DSRM 密码

Active Directory 将始终验证新的目录服务修复模式密码，以确保它满足域的密码复杂性要求;此验证还调用密码筛选器 dll，如 Azure AD 密码保护。 如果新的 DSRM 密码被拒绝，则会导致以下错误消息：

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

当 Azure AD 密码保护记录活动目录 DSRM 密码的密码验证事件日志事件时，预计事件日志消息将不包含用户名。 发生此行为是因为 DSRM 帐户是不属于实际活动目录域的本地帐户。  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>域控制器副本升级失败，因为 DSRM 密码较弱

在 DC 升级过程中，新的目录服务修复模式密码将提交到域中的现有 DC 进行验证。 如果新的 DSRM 密码被拒绝，则会导致以下错误消息：

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

与上述问题一样，任何 Azure AD 密码保护密码验证结果事件都将为此方案具有空用户名。

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>由于本地管理员密码薄弱，域控制器降级失败

支持降级仍在运行 DC 代理软件的域控制器。 但管理员应注意，在降级过程中，DC 代理软件会继续实施当前密码策略。 将像验证其他任何密码一样验证（降级操作过程中指定的）新本地管理员帐户密码。 Microsoft 建议为本地管理员帐户选择安全密码，作为 DC 降级过程的一部分。

成功降级后，如果域控制器已重新启动并再次以普通成员服务器的形式运行，则 DC 代理软件将恢复为以被动模式运行。 然后，随时可将其卸载。

## <a name="booting-into-directory-services-repair-mode"></a>引导到目录服务修复模式

如果域控制器已引导到目录服务修复模式，则 DC 代理密码筛选器 dll 将检测到此情况，并将导致禁用所有密码验证或强制活动，而不管当前处于活动状态的策略如何配置。 DC 代理密码筛选器 dll 将 10023 警告事件记录到管理事件日志，例如：

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>公共预览 DC 代理软件已过期

在 Azure AD 密码保护公共预览期间，DC 代理软件经过硬编码，以停止处理以下日期的密码验证请求：

* 版本 1.2.65.0 将于 2019 年 9 月 1 日停止处理密码验证请求。
* 版本 1.2.25.0 和之前停止处理密码验证请求在 2019 年 7 月 1 日。

随着截止时间的临近，所有时间限制的 DC 代理版本都将在启动时在 DC 代理管理事件日志中发出 10021 事件，如下所示：

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

截止时间过后，所有时间限制的 DC 代理版本将在启动时在 DC 代理管理事件日志中发出 10022 事件，如下所示：

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

由于仅在初始启动时检查截止时间，因此在日历截止时间过后很长时间内，您才能看到这些事件。 一旦识别了截止时间，除了自动批准所有密码之外，不会对域控制器或更大的环境产生负面影响。

> [!IMPORTANT]
> Microsoft 建议立即将过期的公共预览 DC 代理升级到最新版本。

在环境中发现需要升级的 DC 代理的一种简单方法是运行`Get-AzureADPasswordProtectionDCAgent`cmdlet，例如：

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

对于本主题，"软件版本"字段显然是要查看的关键属性。 您还可以使用 PowerShell 筛选来筛选出已经达到或高于所需基准版本的 DC 代理，例如：

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

Azure AD 密码保护代理软件在任何版本中都是不受限制的。 Microsoft 仍然建议将 DC 代理和代理代理在发布时升级到最新版本。 `Get-AzureADPasswordProtectionProxy` cmdlet 可用于查找需要升级的代理代理，类似于上述 DC 代理示例。

有关特定升级过程的更多详细信息[，请参阅升级 DC 代理](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent)和[升级代理服务](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service)。

## <a name="emergency-remediation"></a>紧急补救

如果 DC 代理服务造成了问题，可以立即关闭 DC 代理服务。 DC 代理密码筛选器 dll 仍会尝试调用未运行的服务并记录警告事件（10012、10013），但在此期间会接受所有传入的密码。 然后，也可以通过 Windows 服务控制管理器并根据需要使用启动类型“Disabled”来配置 DC 代理服务。

另一种补救措施是在 Azure AD 密码保护门户中将“启用”模式设置为“否”。 下载更新的策略后，各 DC 代理服务将进入静默模式，在这种模式下，将按原样接受所有密码。 有关详细信息，请参阅[操作模式](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)。

## <a name="removal"></a>删除

如果决定卸载 Azure AD 密码保护软件并从域和林中清除所有相关状态，则可以使用以下步骤完成此任务：

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

4. 在每个域命名上下文中手动删除所有 DC 代理连接点。 林中每个域控制器可能有一个这些对象，具体取决于软件的部署范围。 可使用以下 Active Directory PowerShell 命令发现该对象的位置：

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
