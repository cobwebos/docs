---
title: 监视和 Azure AD 密码保护-Azure Active Directory 中的日志记录
description: 了解 Azure AD 密码保护中的监视和日志记录
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: a029135da79d1a0b24b2941873a0fe3187ac9f7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60414794"
---
# <a name="azure-ad-password-protection-monitoring-and-logging"></a>Azure AD 密码保护中的监视和日志记录

部署 Azure AD 密码保护后，监视和报告是至关重要的任务。 本文详细介绍了各种监视技术，包括每项服务在哪里记录信息，以及如何报告 Azure AD 密码保护使用情况。

监视和报告完成事件日志消息或通过运行 PowerShell cmdlet。 DC 代理和代理服务这两个记录事件日志消息。 如下所述的所有 PowerShell cmdlet 都才可用代理服务器上 （请参阅 AzureADPasswordProtection PowerShell 模块）。 DC 代理软件不会安装 PowerShell 模块。

## <a name="dc-agent-event-logging"></a>DC 代理事件日志记录

在每个域控制器上，DC 代理服务软件将每个密码验证操作的结果（和其他状态）写入本地事件日志：

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

DC 代理管理日志是软件行为方式信息的主要来源。

请注意，跟踪日志默认处于禁用状态。

各个 DC 代理组件记录的事件属于以下范围：

|组件 |事件 ID 范围|
| --- | --- |
|DC 代理密码筛选器 dll| 10000-19999|
|DC 代理服务宿主进程| 20000-29999|
|DC 代理服务策略验证逻辑| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>DC 代理管理事件日志

### <a name="password-validation-outcome-events"></a>密码验证结果事件

在每个域控制器上，DC 代理服务软件将每个密码验证的结果写入 DC 代理管理事件日志。

对于成功的密码验证操作，DC 代理密码筛选器 dll 通常会记录一个事件。 对于失败的密码验证操作，通常会记录两个事件，一个来自 DC 代理服务，另一个来自 DC 代理密码筛选器 dll。

根据以下因素记录用于捕获这些状况的离散事件：

* 是在设置还是更改给定的密码。
* 给定密码的验证是已通过还是失败。
* 验证是否因 Microsoft 全局策略、组织策略或这两者而失败。
* 目前是对当前密码策略启用还是禁用了仅限审核模式。

关键的密码验证相关事件如下：

|   |密码更改 |密码设置|
| --- | :---: | :---: |
|通过 |10014 |10015|
|失败（因客户密码策略所致）| 10016、30002| 10017、30003|
|失败（因 Microsoft 密码策略所致）| 10016、30004| 10017、30005|
|失败（因 Microsoft 密码策略和客户密码策略这两种策略所致）| 10016、30026| 10017、30027|
|仅限审核通过（未通过客户密码策略验证）| 10024、30008| 10025、30007|
|仅限审核通过（未通过 Microsoft 密码策略验证）| 10024、30010| 10025、30009|
|仅审核通过（未通过 Microsoft 密码策略和客户密码策略这两种策略的验证）| 10024、30028| 10025、30029|

上表中的“两种策略”情况是指，发现用户密码至少包含 Microsoft 禁止密码列表和客户禁止密码列表中的一个令牌。

一起记录的一对事件因具有相同的 CorrelationId 而显式关联。

### <a name="password-validation-summary-reporting-via-powershell"></a>通过 PowerShell 报告密码验证摘要

`Get-AzureADPasswordProtectionSummaryReport` cmdlet 可用于生成密码验证活动的摘要视图。 此 cmdlet 的示例输出如下所示：

```powershell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

可以使用 –Forest、-Domain 和 –DomainController 参数之一来影响 cmdlet 的报告范围。 不指定参数表示使用 –Forest。

`Get-AzureADPasswordProtectionSummaryReport` cmdlet 的工作原理是，先查询 DC 代理管理事件日志，再计算所显示的每个结果类别对应的事件总数。 下表列出了每个结果及其相应的事件 ID 之间的映射关系：

|Get-AzureADPasswordProtectionSummaryReport property |相应的事件 ID|
| :---: | :---: |
|PasswordChangesValidated |10014|
|PasswordSetsValidated |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejected |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

请注意，`Get-AzureADPasswordProtectionSummaryReport` cmdlet 以 PowerShell 脚本形式提供。如果需要，可以在下面的位置直接引用它：

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> 此 cmdlet 通过打开与每个域控制器的 Powershell 会话进行工作。 若要获得成功，必须在每个域控制器上启用 PowerShell 远程会话支持，并且客户端必须具有足够的权限。 有关 PowerShell 远程会话要求的详细信息，请在 PowerShell 窗口中运行“Get-Help about_Remote_Troubleshooting”。

> [!NOTE]
> 此 cmdlet 的工作方式是远程查询每个 DC 代理服务的管理事件日志。 如果事件日志包含大量事件，此 cmdlet 可能需要很长时间才能完成。 此外，对大型数据集执行批量网络查询可能会影响域控制器的性能。 因此，在生产环境中应慎用此 cmdlet。

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>事件 ID 10014（密码更改成功）的示例事件日志消息

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>事件 ID 10017 和 30003（密码设置失败）的示例事件日志消息

10017:

```text
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```text
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>事件 ID 30001（由于没有可用的策略，因此已接受密码）的示例事件日志消息

```text
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

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>事件 ID 30006（正在实施新策略）的示例事件日志消息

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>事件 ID 30019（已禁用 Azure AD 密码保护）的示例事件日志消息

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>DC 代理操作日志

DC 代理服务还会将操作相关的事件记录到以下日志：

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>DC 代理跟踪日志

DC 代理服务还会将详细的调试级别跟踪事件记录到以下日志：

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

默认已禁用跟踪日志记录。

> [!WARNING]
> 如果启用跟踪日志，此日志会收到大量的事件，从而可能影响域控制器的性能。 因此，仅当某个问题需要更深入的调查，并且只需短时间启用此增强型日志时，才启用此日志。

## <a name="dc-agent-text-logging"></a>DC 代理文本日志记录

可以通过设置以下注册表值，将 DC 代理服务配置为写入到文本日志：

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

默认已禁用文本日志记录。 需要重启 DC 代理服务才能使此值的更改生效。 启用后，DC 代理服务会写入到以下目录下的日志文件：

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> 文本日志接收可记录到跟踪日志的相同调试级别条目，但通常采用更简单的格式，可方便查看和分析。

> [!WARNING]
> 如果启用跟踪日志，此日志会收到大量的事件，从而可能影响域控制器的性能。 因此，仅当某个问题需要更深入的调查，并且只需短时间启用此增强型日志时，才启用此日志。

## <a name="dc-agent-performance-monitoring"></a>DC 代理性能监视

DC 代理服务软件安装名为“Azure AD 密码保护”的性能计数器对象。 目前提供以下性能计数器：

|性能计数器名称 | 描述|
| --- | --- |
|处理的密码数 |此计数器显示自上次重启以来已处理的密码（接受或拒绝）总数。|
|接受的密码数 |此计数器显示自上次重启以来已接受的密码总数。|
|拒绝的密码数 |此计数器显示自上次重启以来已拒绝的密码总数。|
|正在处理的密码筛选器请求数 |此计数器显示当前正在处理的密码筛选器请求数。|
|峰值密码筛选器请求数 |此计数器显示自上次重启以来的并发密码筛选器请求的峰值数量。|
|密码筛选器请求错误数 |此计数器显示自上次重启以来因出错而失败的密码筛选器请求总数。 当 Azure AD 密码保护 DC 代理服务未运行时，可能会发生错误。|
|每秒密码筛选器请求数 |此计数器显示密码的处理速率。|
|密码筛选器请求处理时间 |此计数器显示处理一个密码筛选器请求所需的平均时间。|
|峰值密码筛选器请求处理时间 |此计数器显示自上次重启以来的峰值密码筛选器请求处理时间。|
|由于审核模式而接受的密码数 |此计数器显示通常会被拒绝，但由于在审查模式下配置了密码策略配置而被接受的密码总数（自上次重启以来）。|

## <a name="dc-agent-discovery"></a>DC 代理发现

`Get-AzureADPasswordProtectionDCAgent` cmdlet 可用于显示域或林中运行的各个 DC 代理的基本信息。 从正在运行的 DC 代理服务注册的 serviceConnectionPoint 对象检索此信息。

此 cmdlet 的示例输出如下所示：

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

每个 DC 代理服务大约每隔一小时更新各种属性。 数据仍可能遇到 Active Directory 复制延迟。

使用 –Forest 或 –Domain 参数可以影响 cmdlet 查询的范围。

如果 HeartbeatUTC 值过时，可能会有以下症状：相应域控制器上的 Azure AD 密码保护 DC 代理未运行或已卸载，或者计算机已降级，不再是域控制器。

如果 PasswordPolicyDateUTC 值过时，可能会有以下症状：相应计算机上的 Azure AD 密码保护 DC 代理未正常运行。

## <a name="proxy-service-event-logging"></a>代理服务事件日志记录

代理服务将少量的事件发出到以下事件日志：

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

请注意，跟踪日志默认处于禁用状态。

> [!WARNING]
> 如果启用跟踪日志，此日志会收到大量的事件，从而可能影响代理主机的性能。 因此，仅当某个问题需要更深入的调查，并且只需短时间启用此日志时，才启用此日志。

各个代理组件使用以下范围记录事件：

|组件 |事件 ID 范围|
| --- | --- |
|代理服务主机进程| 10000-19999|
|代理服务核心业务逻辑| 20000-29999|
|PowerShell cmdlet| 30000-39999|

## <a name="proxy-service-text-logging"></a>代理服务文本日志记录

可以通过设置以下注册表值，将代理服务配置为写入到文本日志：

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters!EnableTextLogging = 1（REG_DWORD 值）

默认已禁用文本日志记录。 需要重启代理服务才能使此值的更改生效。 启用后，代理服务会写入到以下目录下的日志文件：

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> 文本日志接收可记录到跟踪日志的相同调试级别条目，但通常采用更简单的格式，可方便查看和分析。

> [!WARNING]
> 如果启用，此日志会收到大量事件，可能会影响计算机性能。 因此，仅当某个问题需要更深入的调查，并且只需短时间启用此增强型日志时，才启用此日志。

## <a name="powershell-cmdlet-logging"></a>PowerShell cmdlet 日志记录

导致状态更改的 PowerShell cmdlet（例如，Register-AzureADPasswordProtectionProxy）通常会将结果事件记录到运行日志中。

此外，大多数 Azure AD 密码保护 PowerShell cmdlet 将写入到下一个文本日志：

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

如果发生 cmdlet 错误并且原因和/或解决方法暂时并不明显，则也可以查阅这些文本日志。

## <a name="proxy-discovery"></a>代理发现

`Get-AzureADPasswordProtectionProxy` cmdlet 可用于显示域或林中运行的各个 Azure AD 密码保护代理服务的基本信息。 此信息是从正在运行的一个或多个代理服务注册的一个或多个 serviceConnectionPoint 对象检索而来。

此 cmdlet 的示例输出如下所示：

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

每个代理服务大约每小时更新一次各种属性。 数据仍可能遇到 Active Directory 复制延迟。

使用 –Forest 或 –Domain 参数可以影响 cmdlet 查询的范围。

如果 HeartbeatUTC 值过时，可能会有以下症状：相应计算机上的 Azure AD 密码保护代理未运行或已卸载。

## <a name="next-steps"></a>后续步骤

[Azure AD 密码保护故障排除](howto-password-ban-bad-on-premises-troubleshoot.md)

有关全局和自定义禁止密码列表的详细信息，请参阅[禁止错误的密码](concept-password-ban-bad.md)一文
