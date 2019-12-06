---
title: 密码保护疑难解答-Azure Active Directory
description: 了解 Azure AD 密码保护常见故障排除
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
ms.openlocfilehash: bd609eb1f289c0a104bddaa08a60e7dc6202acee
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847654"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Azure AD 密码保护故障排除

部署 Azure AD 密码保护后，可能需要进行故障排除。 本文详细介绍如何帮助你了解一些常见的故障排除步骤。

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>DC 代理在目录中找不到代理

此问题的主要症状是 DC 代理管理事件日志中的30017事件。

此问题的常见原因是尚未注册代理。 如果代理已经注册，可能会由于 AD 复制延迟而导致某些延迟，直到特定 DC 代理可以查看该代理。

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>DC 代理无法与代理进行通信

此问题的主要症状是 DC 代理管理事件日志中的30018事件。 此问题可能有几个可能的原因：

1. DC 代理位于网络的隔离部分，不允许与已注册的代理建立网络连接。 此问题可能是良性的，因为其他 DC 代理可以与代理进行通信，以便从 Azure 下载密码策略。 下载之后，这些策略将通过复制 sysvol 共享中的策略文件，由独立 DC 获得。

1. 代理主机正在阻止访问 RPC 终结点映射器终结点（端口135）

   Azure AD 密码保护代理安装程序会自动创建 Windows 防火墙入站规则，以允许访问端口135。 如果以后删除或禁用此规则，DC 代理将无法与代理服务通信。 如果已禁用内置 Windows 防火墙而不是另一个防火墙产品，则必须配置该防火墙以允许访问端口135。

1. 代理主机正在阻止对代理服务侦听的 RPC 终结点（动态或静态）的访问

   Azure AD 密码保护代理安装程序会自动创建 Windows 防火墙入站规则，该规则允许访问 Azure AD 密码保护代理服务侦听的任何入站端口。 如果以后删除或禁用此规则，DC 代理将无法与代理服务通信。 如果已禁用内置 Windows 防火墙而不是另一个防火墙产品，则必须将该防火墙配置为允许 Azure AD 密码保护代理服务访问所侦听的任何入站端口。 如果已将代理服务配置为侦听特定的静态 RPC 端口（使用 `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet），则此配置可能更具体。

1. 代理主机未配置为允许域控制器登录到计算机。 此行为是通过 "从网络访问此计算机" 用户权限分配来控制的。 林中所有域中的所有域控制器都必须被授予此权限。 此设置通常会被约束为更大的网络强化工作量。

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>代理服务无法与 Azure 通信

1. 确保代理计算机已连接到[部署要求](howto-password-ban-bad-on-premises-deploy.md)中列出的终结点。

1. 确保为同一 Azure 租户注册了林和所有代理服务器。

   你可以通过运行 `Get-AzureADPasswordProtectionProxy` 和 `Get-AzureADPasswordProtectionDCAgent` PowerShell cmdlet 来检查此要求，然后比较每个返回项的 `AzureTenant` 属性。 对于正确的操作，报告的租户名称在所有 DC 代理和代理服务器上必须相同。

   如果存在 Azure 租户注册不匹配条件，可以根据需要运行 `Register-AzureADPasswordProtectionProxy` 和/或 `Register-AzureADPasswordProtectionForest` PowerShell cmdlet 来解决此问题，并确保使用同一 Azure 租户中的凭据进行所有注册。

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>DC 代理无法加密或解密密码策略文件

Azure AD 密码保护对 Microsoft 密钥分发服务提供的加密和解密功能有重要的依赖性。 加密或解密失败可能会导致各种症状，并有几个可能的原因。

1. 确保已在域中的所有 Windows Server 2012 和更高版本的域控制器上启用并运行 KDS 服务。

   默认情况下，KDS 服务的服务启动模式配置为手动（触发器启动）。 此配置意味着，当客户端首次尝试使用该服务时，它将按需启动。 此默认服务启动模式可用于 Azure AD 密码保护才能工作。

   如果已将 KDS 服务启动模式配置为 "已禁用"，则必须先修复此配置，然后 Azure AD 密码保护才能正常工作。

   此问题的简单测试是通过 Service management MMC 控制台或使用其他管理工具（例如，从命令提示符控制台运行 "net start kdssvc.dll"）手动启动 KDS 服务。 KDS 服务应成功启动并保持运行状态。

   KDS 服务无法启动的最常见根本原因是 Active Directory 域控制器对象位于默认域控制器 OU 外。 此配置不受 KDS 服务支持，且不受 Azure AD 密码保护的限制。 此问题的解决方法是将域控制器对象移动到默认域控制器 OU 下的某个位置。

1. 从 Windows Server 2012 R2 到 Windows Server 2016 的 KDS 加密缓冲区格式更改不兼容

   Windows Server 2016 中引入了 KDS 安全修补程序，用于修改 KDS 加密缓冲区的格式;有时，这些缓冲区在 Windows Server 2012 和 Windows Server 2012 R2 上无法解密。 反向方向是 KDS 在 Windows Server 2012 上加密的缓冲区，Windows Server 2012 R2 在 Windows server 2016 及更高版本上始终会成功解密。 如果 Active Directory 域中的域控制器运行的是这些操作系统的混合，则可能会报告偶尔 Azure AD 密码保护解密失败。 由于安全修补程序的性质，无法准确预测这些故障的时间或症状，并且假设它是不确定的，这 Azure AD 密码保护 DC 代理，在该代理上，域控制器将在给定的时间对数据进行加密。

   Microsoft 正在调查此问题的修补程序，但尚未提供 ETA。 同时，除了不在 Active Directory 域中混合使用这些不兼容的操作系统以外，此问题没有解决方法。 换句话说，你应该只运行 Windows Server 2012 和 Windows Server 2012 R2 域控制器，或者只应运行 Windows Server 2016 及更高版本的域控制器。

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>正在接受弱密码，但不应

此问题可能有几个原因。

1. 你的 DC 代理正在运行已过期的公共预览版软件版本。 请参阅[公共预览版 DC 代理软件已过期](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired)。

1. 你的 DC 代理无法下载策略或者无法解密现有策略。 检查上述主题中的可能原因。

1. 密码策略强制模式仍设置为“审核”。 如果此配置有效，请将其重新配置为使用 Azure AD 密码保护门户。 请参阅[启用密码保护](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)。

1. 密码策略已被禁用。 如果此配置生效，请使用 Azure AD 密码保护门户将其重新配置为启用。 请参阅[启用密码保护](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)。

1. 你未在域中的所有域控制器上安装 DC 代理软件。 在这种情况下，很难确保远程 Windows 客户端在密码更改操作过程中以特定的域控制器为目标。 如果你认为已成功将安装了 DC 代理软件的特定 DC 作为目标，则可以通过双重检查 DC 代理管理事件日志进行验证：无论结果如何，都至少需要一个事件来记录密码的结果检查. 如果用户的密码更改不存在任何事件，则可能是由另一个域控制器处理密码更改。

   作为替代方案，请在登录 DC 代理软件时，尝试 setting\changing 密码，并将密码直接登录到 DC。 对于生产 Active Directory 域，不建议使用此方法。

   尽管支持对 DC 代理软件进行增量部署，但受到这些限制的限制，但 Microsoft 强烈建议尽快在域中的所有域控制器上安装 DC 代理软件。

1. 密码验证算法实际可能按预期方式工作。 请参阅[如何计算密码](concept-password-ban-bad.md#how-are-passwords-evaluated)。

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil 无法设置弱 DSRM 密码

Active Directory 将始终验证新的目录服务修复模式密码，确保它满足域的密码复杂性要求;此验证还会调用密码筛选器 dll，例如 Azure AD 密码保护。 如果拒绝新的 DSRM 密码，则会出现以下错误消息：

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

当 Azure AD 密码保护记录 Active Directory DSRM 密码的密码验证事件日志事件时，事件日志消息应该不包括用户名。 出现此行为的原因是 DSRM 帐户是不属于实际 Active Directory 域的本地帐户。  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>由于使用弱 DSRM 密码，域控制器副本升级失败

在 DC 升级过程中，会将新的目录服务修复模式密码提交到域中的现有 DC 进行验证。 如果拒绝新的 DSRM 密码，则会出现以下错误消息：

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

正如以上问题中所示，任何 Azure AD 密码保护密码验证结果事件都将在此方案中包含空的用户名。

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>由于本地管理员密码弱，域控制器降级失败

支持降级仍在运行 DC 代理软件的域控制器。 但管理员应注意，在降级过程中，DC 代理软件会继续实施当前密码策略。 将像验证其他任何密码一样验证（降级操作过程中指定的）新本地管理员帐户密码。 Microsoft 建议为本地管理员帐户选择安全密码作为 DC 降级过程的一部分。

成功降级后，如果域控制器已重新启动并再次以普通成员服务器的形式运行，则 DC 代理软件将恢复为以被动模式运行。 然后，随时可将其卸载。

## <a name="booting-into-directory-services-repair-mode"></a>启动进入目录服务修复模式

如果域控制器已启动到目录服务修复模式，则 DC 代理密码筛选器 dll 将检测到此情况，并将导致所有密码验证或强制活动处于禁用状态，而不考虑当前活动的策略configuration. DC 代理密码筛选器 dll 会将10023警告事件记录到管理事件日志，例如：

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>公共预览版 DC 代理软件已过期

在 Azure AD 密码保护公共预览版期间，DC 代理软件进行了硬编码，以便在以下日期停止处理密码验证请求：

* 版本1.2.65.0 将停止在 1 2019 年9月处理密码验证请求。
* 版本1.2.25.0 和先前在7月 1 2019 处理密码验证请求。

截止到截止时间，在启动时，所有限时的 DC 代理版本都将在 DC agent 管理事件日志中发出10021事件，如下所示：

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

截止到截止时间之后，所有受时间限制的 DC 代理版本都将在启动时在 DC 代理管理事件日志中发出10022事件，如下所示：

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

由于仅在初始启动时检查截止时间，因此在日历截止时间过去之前，你可能看不到这些事件。 一旦识别到截止时间，就不会再对域控制器或更大的环境产生负面影响。

> [!IMPORTANT]
> Microsoft 建议将过期的公共预览版 DC 代理立即升级到最新版本。

若要在需要升级的环境中发现 DC 代理，一种简单的方法是运行 `Get-AzureADPasswordProtectionDCAgent` cmdlet，例如：

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

对于本主题，SoftwareVersion 字段显然是要查看的关键属性。 你还可以使用 PowerShell 筛选来筛选出已在所需基准版本或更高版本上的 DC 代理，例如：

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

在任何版本中，Azure AD 密码保护代理软件不受时间限制。 Microsoft 仍建议将 DC 和代理代理发布到最新版本。 `Get-AzureADPasswordProtectionProxy` cmdlet 可用于查找需要升级的代理程序，类似于针对 DC 代理的示例。

有关具体的升级过程的详细信息，请参阅[升级 DC 代理](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent)和[升级代理程序](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-agent)。

## <a name="emergency-remediation"></a>紧急补救

如果 DC 代理服务造成了问题，可以立即关闭 DC 代理服务。 DC 代理密码筛选器 dll 仍会尝试调用未运行的服务并记录警告事件（10012、10013），但在此期间会接受所有传入的密码。 然后，也可以通过 Windows 服务控制管理器并根据需要使用启动类型“Disabled”来配置 DC 代理服务。

另一种补救措施是在 Azure AD 密码保护门户中将“启用”模式设置为“否”。 下载更新的策略后，各 DC 代理服务将进入静默模式，在这种模式下，将按原样接受所有密码。 有关详细信息，请参阅[强制模式](howto-password-ban-bad-on-premises-operations.md#enforce-mode)。

## <a name="removal"></a>删除

如果决定卸载 Azure AD 密码保护软件并清除域和林中的所有相关状态，则可以使用以下步骤完成此任务：

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

4. 在每个域命名上下文中手动删除所有 DC 代理连接点。 林中每个域控制器可能有一个这样的对象，具体取决于软件的部署范围。 可使用以下 Active Directory PowerShell 命令发现该对象的位置：

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
