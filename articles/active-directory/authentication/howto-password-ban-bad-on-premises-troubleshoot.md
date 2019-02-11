---
title: Azure AD 密码保护预览版中的故障排除和日志记录
description: 了解 Azure AD 密码保护预览版的日志记录和一般故障排除方法
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 55139d7270d602d73605c484eeace7012ff3c1b8
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2019
ms.locfileid: "55076071"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>预览版：Azure AD 密码保护监视、报告和故障排除

|     |
| --- |
| Azure AD 密码保护是 Azure Active Directory 的一项公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

部署 Azure AD 密码保护后，监视和报告是至关重要的任务。 本文提供详细信息来帮助你了解每项服务日志信息的位置，以及如何报告 Azure AD 密码保护的用法。

## <a name="on-premises-logs-and-events"></a>本地日志和事件

### <a name="dc-agent-admin-log"></a>DC 代理管理日志

在每个域控制器上，DC 代理服务软件会将其密码验证结果（和其他状态）写入本地事件日志：

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

各个 DC 代理组件使用以下范围记录事件：

|组件 |事件 ID 范围|
| --- | --- |
|DC 代理密码筛选器 dll| 10000-19999|
|DC 代理服务宿主进程| 20000-29999|
|DC 代理服务策略验证逻辑| 30000-39999|

对于成功的密码验证操作，DC 代理密码筛选器 dll 通常会记录一个事件。 对于失败的密码验证操作，通常会记录两个事件，一个来自 DC 代理服务，另一个来自 DC 代理密码筛选器 dll。

根据以下因素记录用于捕获这些状况的离散事件：

* 是在设置还是更改给定的密码。
* 给定密码的验证是已通过还是失败。
* 验证是否因 Microsoft 全局策略或组织策略而失败。
* 目前是对当前密码策略启用还是禁用了仅限审核模式。

关键的密码验证相关事件如下：

|   |密码更改 |密码设置|
| --- | :---: | :---: |
|通过 |10014 |10015|
|失败（未通过客户密码策略验证）| 10016、30002| 10017、30003|
|失败（未通过 Microsoft 密码策略验证）| 10016、30004| 10017、30005|
|仅限审核通过（未通过客户密码策略验证）| 10024、30008| 10025、30007|
|仅限审核通过（未通过 Microsoft 密码策略验证）| 10024、30010| 10025、30009|

> [!TIP]
> 首先根据 Microsoft 全局密码列表验证传入的密码；如果失败，则不执行进一步的处理。 此行为与针对 Azure 中的密码更改执行的操作相同。

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>事件 ID 10014（密码设置成功）的示例事件日志消息

```
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>事件 ID 10017 和 30003（密码设置失败）的示例事件日志消息

10017:

```
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

#### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>事件 ID 30001（由于没有可用的策略，因此已接受密码）的示例事件日志消息

```
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

#### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>事件 ID 30006（正在实施新策略）的示例事件日志消息

```
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

#### <a name="dc-agent-operational-log"></a>DC 代理操作日志

DC 代理服务还会将操作相关的事件记录到以下日志：

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

#### <a name="dc-agent-trace-log"></a>DC 代理跟踪日志

DC 代理服务还会将详细的调试级别跟踪事件记录到以下日志：

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

默认已禁用跟踪日志记录。

> [!WARNING]
>  如果启用跟踪日志，此日志会收到大量的事件，从而可能影响域控制器的性能。 因此，仅当某个问题需要更深入的调查，并且只需短时间启用此增强型日志时，才启用此日志。

#### <a name="dc-agent-text-logging"></a>DC 代理文本日志记录

可以通过设置以下注册表值，将 DC 代理服务配置为写入到文本日志：

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1（REG_DWORD 值）

默认已禁用文本日志记录。 需要重启 DC 代理服务才能使此值的更改生效。 启用后，DC 代理服务会写入到以下目录下的日志文件：

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> 文本日志接收可记录到跟踪日志的相同调试级别条目，但通常采用更简单的格式，可方便查看和分析。

> [!WARNING]
> 如果启用跟踪日志，此日志会收到大量的事件，从而可能影响域控制器的性能。 因此，仅当某个问题需要更深入的调查，并且只需短时间启用此增强型日志时，才启用此日志。

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD 密码保护代理服务

#### <a name="proxy-service-event-logs"></a>代理服务事件日志

代理服务将少量的事件发出到以下事件日志：

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

代理服务还会将详细的调试级别跟踪事件记录到以下日志：

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

默认已禁用跟踪日志记录。

> [!WARNING]
> 如果启用跟踪日志，此日志会收到大量的事件，从而可能影响代理主机的性能。 因此，仅当某个问题需要更深入的调查，并且只需短时间启用此日志时，才启用此日志。

#### <a name="proxy-service-text-logging"></a>代理服务文本日志记录

可以通过设置以下注册表值，将代理服务配置为写入到文本日志：

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters!EnableTextLogging = 1（REG_DWORD 值）

默认已禁用文本日志记录。 需要重启代理服务才能使此值的更改生效。 启用后，代理服务会写入到以下目录下的日志文件：

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> 文本日志接收可记录到跟踪日志的相同调试级别条目，但通常采用更简单的格式，可方便查看和分析。

> [!WARNING]
> 如果启用跟踪日志，此日志会收到大量的事件，从而可能影响域控制器的性能。 因此，仅当某个问题需要更深入的调查，并且只需短时间启用此增强型日志时，才启用此日志。

#### <a name="powershell-cmdlet-logging"></a>PowerShell cmdlet 日志记录

大多数 Azure AD 密码保护 Powershell cmdlet 会写入到以下目录下的文本日志：

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

如果发生 cmdlet 错误并且原因和/或解决方法暂时并不明显，则也可以查阅这些文本日志。

### <a name="emergency-remediation"></a>紧急补救

如果 DC 代理服务造成了问题，可以立即关闭 DC 代理服务。 DC 代理密码筛选器 dll 仍会尝试调用未运行的服务并记录警告事件（10012、10013），但在此期间会接受所有传入的密码。 然后，也可以通过 Windows 服务控制管理器并根据需要使用启动类型“Disabled”来配置 DC 代理服务。

### <a name="performance-monitoring"></a>性能监视

DC 代理服务软件安装名为“Azure AD 密码保护”的性能计数器对象。 目前提供以下性能计数器：

|性能计数器名称 | 说明|
| --- | --- |
|处理的密码数 |此计数器显示自上次重启以来已处理的密码（接受或拒绝）总数。|
|接受的密码数 |此计数器显示自上次重启以来已接受的密码总数。|
|拒绝的密码数 |此计数器显示自上次重启以来已拒绝的密码总数。|
|正在处理的密码筛选器请求数 |此计数器显示当前正在处理的密码筛选器请求数。|
|峰值密码筛选器请求数 |此计数器显示自上次重启以来的并发密码筛选器请求的峰值数量。|
|密码筛选器请求错误数 |此计数器显示自上次重启以来因出错而失败的密码筛选器请求总数。 当 Azure AD 密码保护 DC 代理服务未运行时，可以会发生错误。|
|每秒密码筛选器请求数 |此计数器显示密码的处理速率。|
|密码筛选器请求处理时间 |此计数器显示处理一个密码筛选器请求所需的平均时间。|
|峰值密码筛选器请求处理时间 |此计数器显示自上次重启以来的峰值密码筛选器请求处理时间。|
|由于审核模式而接受的密码数 |此计数器显示通常会被拒绝，但由于在审查模式下配置了密码策略配置而被接受的密码总数（自上次重启以来）。|

## <a name="directory-services-repair-mode"></a>目录服务修复模式

如果域控制器启动进入目录服务修复模式，则 DC 代理服务会检测此模式，导致所有密码验证或实施活动被禁用，无论当前活动的策略配置是什么。

## <a name="domain-controller-demotion"></a>域控制器降级

支持降级仍在运行 DC 代理软件的域控制器。 但管理员应注意，在降级过程中，DC 代理软件将保持运行，并继续实施当前密码策略。 将像验证其他任何密码一样验证（降级操作过程中指定的）新本地管理员帐户密码。 Microsoft 建议在 DC 降级过程中为本地管理员帐户选择安全密码；但是，现有的降级操作过程可能会中断 DC 代理软件对新本地管理员帐户密码执行的验证。

成功降级后，如果域控制器已重新启动并再次以普通成员服务器的形式运行，则 DC 代理软件将恢复为以被动模式运行。 然后，随时可将其卸载。

## <a name="removal"></a>删除

如果决定卸载公共预览版软件并从域和林中清理所有相关状态，可使用以下步骤完成此任务：

> [!IMPORTANT]
> 必须按顺序执行这些步骤。 如果代理服务的任何实例仍在运行，它会定期重新创建其 serviceConnectionPoint 对象。 如果 DC 代理服务的任何实例仍在运行，它会定期重新创建其 serviceConnectionPoint 对象和 sysvol 状态。

1. 从所有计算机中卸载密码保护代理软件。 完成此步骤后**不需要**重新启动。
2. 从所有域控制器中卸载 DC 代理软件。 完成此步骤后**需要**重新启动。
3. 在每个域命名上下文中手动删除所有代理服务连接点。 可使用以下 Active Directory Powershell 命令发现这些对象的位置：

   ```Powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   不要省略 $keywords 变量值末尾的星号（“*”）。

   然后，可通过管道将 `Get-ADObject` 命令找到的结果对象传送到 `Remove-ADObject`，或手动将其删除。 

4. 在每个域命名上下文中手动删除所有 DC 代理连接点。 根据公共预览版软件的部署范围，林中的每个域控制器可能存在其中的一个对象。 可使用以下 Active Directory Powershell 命令发现该对象的位置：

   ```Powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   然后，可通过管道将 `Get-ADObject` 命令找到的结果对象传送到 `Remove-ADObject`，或手动将其删除。

5. 手动删除林级配置状态。 林配置状态保留在 Active Directory 配置命名上下文中的某个容器内。 可按如下所示发现和删除该状态：

   ```Powershell
   $passwordProtectonConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectonConfigContainer
   ```

6. 通过手动删除以下文件夹及其所有内容，手动删除 sysvol 相关的所有状态：

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   如果需要，也可以在给定域控制器的本地访问此路径；默认位置类似于以下路径：

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   如果在非默认位置配置了 sysvol 共享，则此路径不同。

## <a name="next-steps"></a>后续步骤

有关全局和自定义禁止密码列表的详细信息，请参阅[禁止错误的密码](concept-password-ban-bad.md)一文
