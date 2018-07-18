---
title: Azure AD 密码保护预览版中的故障排除和日志记录
description: 了解 Azure AD 密码保护预览版的日志记录和一般故障排除方法
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: e5b3dc1bfa7c7890be83529e863907ec056f188f
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36292012"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>预览版：Azure AD 密码保护监视、报告和故障排除

|     |
| --- |
| Azure AD 密码保护和自定义禁止密码列表是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

部署 Azure AD 密码保护后，监视和报告是至关重要的任务。 本文提供详细信息来帮助你了解每项服务日志信息的位置，以及如何报告 Azure AD 密码保护的用法。

## <a name="on-premises-logs-and-events"></a>本地日志和事件

### <a name="dc-agent-service"></a>DC 代理服务

在每个域控制器上，DC 代理服务软件会将其密码验证的结果（和其他状态）写入本地事件日志：\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin

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

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>事件 ID 10014 密码设置成功的示例事件日志消息

指定用户的已更改密码经验证符合当前的 Azure 密码策略。

 UserName: BPL_02885102771 FullName:

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>事件 ID 10017 和 30003 密码设置失败的示例事件日志消息

10017:

指定用户的重置密码被拒绝，因为它不符合当前的 Azure 密码策略。 请参阅相关的事件日志消息了解更多详细信息。

 UserName: BPL_03283841185 FullName:

30003:

指定用户的重置密码被拒绝，因为它与当前 Azure 密码策略的每个租户的禁止密码列表中的一个令牌相匹配。

 UserName: BPL_03283841185 FullName:

要注意的其他关键事件日志消息包括：

#### <a name="sample-event-log-message-for-event-id-30001"></a>事件 ID 30001 的示例事件日志消息

指定用户的密码已被接受，因为 Azure 密码策略尚不可用

UserName: <user> FullName: <user>

这种情况可能是以下一个或多个原因造成的:%n

1. 林尚未注册到 Azure。

   解决方法步骤：管理员必须使用 Register-AzureADPasswordProtectionForest cmdlet 注册林。

2. Azure AD 密码保护代理至少在当前林中的一台计算机上不可用。

   解决方法步骤：管理员必须使用 Register-AzureADPasswordProtectionProxy cmdlet 安装并注册代理。

3. 此 DC 尚未与任何 Azure AD 密码保护代理实例建立网络连接。

   解决方法步骤：确保与至少一个 Azure AD 密码保护代理实例建立网络连接。

4. 此 DC 尚未与域中的其他域控制器建立连接。

   解决方法步骤：确保与域建立网络连接。

#### <a name="sample-event-log-message-for-event-id-30006"></a>事件 ID 30006 的示例事件日志消息

服务正在实施以下 Azure 密码策略。

 AuditOnly: 1

 全局策略日期: 2018年-05-15T00:00:00.000000000Z

 租户策略日期: 2018年-06-10T20:15:24.432457600Z

 实施租户策略: 1

#### <a name="dc-agent-log-locations"></a>DC 代理日志位置

DC 代理服务还会将操作相关的事件记录到以下日志：\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational

DC 代理服务还会将详细的调试级跟踪事件记录到以下日志：\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace

> [!WARNING]
> 默认已禁用跟踪日志。 如果启用跟踪日志，此日志会收到大量的事件，从而可能影响域控制器的性能。 因此，仅当某个问题需要更深入的调查，并且只需短时间启用此增强型日志时，才启用此日志。

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD 密码保护代理服务

密码保护代理服务向以下事件日志发出少量的事件：\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational

密码保护代理服务还会将详细的调试级跟踪事件记录到以下日志：\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace

> [!WARNING]
> 默认已禁用跟踪日志。 如果启用跟踪日志，此日志会收到大量的事件，从而可能影响代理主机的性能。 因此，仅当某个问题需要更深入的调查，并且只需短时间启用此日志时，才启用此日志。

### <a name="dc-agent-discovery"></a>DC 代理发现

`Get-AzureADPasswordProtectionDCAgent` cmdlet 可用于显示域或林中运行的各个 DC 代理的基本信息。 从正在运行的 DC 代理服务注册的 serviceConnectionPoint 对象检索此信息。 此 cmdlet 的示例输出如下所示：

```
PS C:\> Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

每个 DC 代理服务大约每隔一小时更新各种属性。 数据仍可能遇到 Active Directory 复制延迟。

使用 –Forest 或 –Domain 参数可以影响 cmdlet 查询的范围。

### <a name="emergency-remediation"></a>紧急补救

如果 DC 代理服务不幸造成了问题，可以立即关闭 DC 代理服务。 DC 代理密码筛选器 dll 会尝试调用未运行的服务并记录警告事件（10012、10013），但在此期间会接受所有传入的密码。 然后，也可以通过 Windows 服务控制管理器并根据需要使用启动类型“Disabled”来配置 DC 代理服务。

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
> 必须按顺序执行这些步骤。 如果密码保护代理服务的任一实例正在运行，它会定期重新创建其 serviceConnectionPoint 对象，并定期重新创建 sysvol 状态。

1. 从所有计算机中卸载密码保护代理软件。 完成此步骤后**不需要**重新启动。
2. 从所有域控制器中卸载 DC 代理软件。 完成此步骤后**需要**重新启动。
3. 在每个域命名上下文中手动删除所有代理服务连接点。 可使用以下 Active Directory Powershell 命令发现这些对象的位置：
   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{EBEFB703-6113-413D-9167-9F8DD4D24468}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -eq $keywords }
   ```

   不要省略 $keywords 变量值末尾的星号（“*”）。

   然后，可通过管道将 `Get-ADObject` 命令找到的结果对象传送到 `Remove-ADObject`，或手动将其删除。 

4. 在每个域命名上下文中手动删除所有 DC 代理连接点。 根据公共预览版软件的部署范围，林中的每个域控制器可能存在其中的一个对象。 可使用以下 Active Directory Powershell 命令发现该对象的位置：

   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{B11BB10A-3E7D-4D37-A4C3-51DE9D0F77C9}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -eq $keywords }
   ```

   然后，可通过管道将 `Get-ADObject` 命令找到的结果对象传送到 `Remove-ADObject`，或手动将其删除。

5. 手动删除林级配置状态。 林配置状态保留在 Active Directory 配置命名上下文中的某个容器内。 可按如下所示发现和删除该状态：

   ```
   $passwordProtectonConfigContainer = "CN=Azure AD password protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject $passwordProtectonConfigContainer
   ```

6. 通过手动删除以下文件夹及其所有内容，手动删除 sysvol 相关的所有状态：

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   如果需要，也可以在给定域控制器的本地访问此路径；默认位置类似于以下路径：

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   如果在非默认位置配置了 sysvol 共享，则此路径不同。

## <a name="next-steps"></a>后续步骤

有关全局和自定义禁止密码列表的详细信息，请参阅[禁止错误的密码](concept-password-ban-bad.md)一文
