---
title: Windows 虚拟桌面 (经典) 诊断问题-Azure
description: 如何使用 Windows 虚拟桌面 (经典) 诊断功能来诊断问题。
author: Heidilohr
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7e652f04b42b132e7c1307503b1764dda7b2036b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009335"
---
# <a name="identify-and-diagnose-issues-in-windows-virtual-desktop-classic"></a>确定和诊断 Windows 虚拟桌面 (经典) 中的问题

>[!IMPORTANT]
>本教程的内容适用于 Windows 虚拟桌面（经典），后者不支持 Azure 资源管理器 Windows 虚拟桌面对象。 要尝试管理 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[本文](../diagnostics-role-service.md)。

Windows 虚拟桌面提供了一项诊断功能，使管理员能够通过单个界面识别问题。 每当用户与系统交互时，Windows 虚拟桌面角色就会记录诊断活动。 每个日志都包含相关信息，例如事务中涉及的 Windows 虚拟桌面角色、错误消息、租户信息和用户信息。 诊断活动由最终用户和管理操作创建，可分为三个主要的存储桶：

* 源订阅活动：最终用户每当尝试通过 Microsoft 远程桌面应用程序连接到其源时都会触发这些活动。
* 连接活动：最终用户每当尝试通过 Microsoft 远程桌面应用程序连接到桌面或 RemoteApp 时都会触发这些活动。
* 管理活动：管理员每当在系统上执行管理操作（如创建主机池、将用户分配到应用组和创建角色分配）时都会触发这些活动。

由于诊断角色服务本身是 Windows 虚拟桌面的一部分，因此无法访问 Windows 虚拟桌面的连接将不会显示在诊断结果中。 当最终用户遇到网络连接问题时，可能会出现 Windows 虚拟桌面连接问题。

首先，[下载并导入 Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)（如果尚未这样做），以便在 PowerShell 会话中使用。 然后，运行以下 cmdlet 登录到你的帐户：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>诊断 PowerShell 问题

Windows 虚拟桌面诊断只使用一个 PowerShell cmdlet，但包含许多可选参数来帮助缩小问题范围和隔离问题。 以下部分列出了可用于诊断问题的 cmdlet。 大多数筛选器可以一起应用。 括在括号中的值（如 `<tenantName>`）应替换为适用于你的情况的值。

>[!IMPORTANT]
>诊断功能适用于单用户故障排除。 使用 PowerShell 的所有查询都必须包含 -UserName 或 -ActivityID 参数。  对于监视功能，请使用 Log Analytics。 有关如何向工作区发送诊断数据的详细信息，请参阅[将 Log Analytics 用于诊断功能](diagnostics-log-analytics-2019.md)。

### <a name="filter-diagnostic-activities-by-user"></a>按用户筛选诊断活动

-UserName 参数返回由指定用户启动的诊断活动列表，如下面的示例 cmdlet 中所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

-UserName 参数也可以与其他可选筛选参数结合。

### <a name="filter-diagnostic-activities-by-time"></a>按时间筛选诊断活动

可以用 -StartTime 和 -EndTime 参数筛选返回的诊断活动列表。  -StartTime 参数将返回从特定日期开始的诊断活动列表，如下面的示例中所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

可以将 -EndTime 参数添加到 cmdlet，其中使用了 -StartTime 参数指定要接收结果的特定时间段。  下面的示例 cmdlet 将返回 8 月 1 日到 8 月 10 日之间的诊断活动的列表。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

-StartTime 和 -EndTime 参数也可以与其他可选筛选参数结合。 

### <a name="filter-diagnostic-activities-by-activity-type"></a>按活动类型筛选诊断活动

你还可以使用 -ActivityType 参数按活动类型筛选诊断活动。 以下 cmdlet 将返回最终用户连接的列表：

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

以下 cmdlet 将返回管理员管理任务的列表：

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

Get-RdsDiagnosticActivities cmdlet 当前不支持指定源作为 ActivityType。

### <a name="filter-diagnostic-activities-by-outcome"></a>按结果筛选诊断活动

可以使用 -Outcome 参数按结果筛选返回的诊断活动列表。 下面的示例 cmdlet 将返回成功的诊断活动的列表。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

下面的示例 cmdlet 将返回失败的诊断活动的列表。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

-Outcome 参数也可以与其他可选筛选参数结合。

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>按活动 ID 检索特定诊断活动

-ActivityId 参数将返回特定的诊断活动（如果存在），如下面的示例 cmdlet 中所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>按活动 ID 查看失败的活动的错误消息

若要查看失败的活动的错误消息，必须使用 -Detailed 参数运行 cmdlet。 可以通过运行 Select-Object cmdlet 来查看错误列表。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>检索详细诊断活动

-Detailed 参数为返回的每个诊断活动提供其他详细信息。 每个活动的格式因其活动类型而异。 可以将 -Detailed 参数添加到任何 Get-RdsDiagnosticActivities 查询，如下面的示例中所示。 

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>常见错误方案

错误方案分类为服务的内部和 Windows 虚拟桌面的外部。

* 内部问题：指定租户管理员无法缓解并且需要作为支持问题进行解决的方案。 当通过 [Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)提供反馈时，请包括活动 ID 和问题发生的大概时间范围。
* 外部问题：与系统管理员可以缓解的方案相关。 这些问题发生在 Windows 虚拟桌面的外部。

下表列出了管理员可能会遇到的常见错误。

>[!NOTE]
>此列表包括最常见的错误，并且会定期更新。 若要确保获得最新的信息，请务必每月至少查看一次本文。

### <a name="external-management-error-codes"></a>外部管理错误代码

|数字代码|错误代码|建议的解决方案|
|---|---|---|
|1322|ConnectionFailedNoMappingOfSIDinAD|用户不是 Azure Active Directory 的成员。 按照 [Active Directory 管理中心](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center)中的说明进行添加。|
|3|UnauthorizedAccess|尝试运行管理 PowerShell cmdlet 的用户不具有执行此操作的权限，或者键入了错误的用户名。|
|1000|TenantNotFound|输入的租户名称与任何现有租户都不匹配。 请查看租户名称是否有拼写错误，然后重试。|
|1006|TenantCannotBeRemovedHasSessionHostPools|如果租户包含对象，则不能删除它。 请先删除该会话主机池，然后重试。|
|2000|HostPoolNotFound|你输入的主机池名称与现有主机池不匹配。 请查看主机池名称是否有拼写错误，然后重试。|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|如果主机池包含对象，则不能删除它。 请先删除主机池中的所有应用组。|
|2004|HostPoolCannotBeRemovedHasSessionHosts|删除会话主机池之前，请先删除所有会话主机。|
|5001|SessionHostNotFound|你所查询的会话主机可能处于脱机状态。 请检查主机池的状态。|
|5008|SessionHostUserSessionsExist |在执行预期的管理活动之前，必须注销会话主机上的所有用户。|
|6000|AppGroupNotFound|输入的应用组名称与任何现有的应用组都不匹配。 请查看应用组名称是否有拼写错误，然后重试。|
|6022|RemoteAppNotFound|输入的 RemoteApp 名称与任何 RemoteApps 都不匹配。 请查看 RemoteApp 名称是否有拼写错误，然后重试。|
|6010|PublishedItemsExist|尝试发布的资源的名称与已经存在的资源的名称相同。 请更改资源名称，然后重试。|
|7002|NameNotValidWhiteSpace|不要在名称中使用空格。|
|8000|InvalidAuthorizationRoleScope|输入的角色名称与任何现有的角色名称都不匹配。 请查看角色名称是否有拼写错误，然后重试。 |
|8001|UserNotFound |输入的用户名与任何现有的用户名都不匹配。 请查看名称是否有拼写错误，然后重试。|
|8005|UserNotFoundInAAD |输入的用户名与任何现有的用户名都不匹配。 请查看名称是否有拼写错误，然后重试。|
|8008|TenantConsentRequired|请按照[此处](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop)的说明为租户提供许可。|

### <a name="external-connection-error-codes"></a>外部连接错误代码

|数字代码|错误代码|建议的解决方案|
|---|---|---|
|-2147467259|ConnectionFailedAdErrorNoSuchMember|用户不是 Active Directory 的成员。 按照 [Active Directory 管理中心](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center)中的说明进行添加。|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|会话主机未正确加入到 Active Directory。|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|由于会话主机不可用，连接失败。 请检查会话主机的运行状况。|
|-2146233088|ConnectionFailedClientDisconnect|如果经常看到此错误，请确保用户的计算机已连接到网络。|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|主机用户尝试连接到的会话不正常。 调试虚拟机。|
|-2146233088|ConnectionFailedUserNotAuthorized|用户无权访问已发布的应用或桌面。 在管理员删除已发布资源后，可能会出现此错误。 要求用户刷新远程桌面应用程序中的源。|
|2|FileNotFound|用户尝试访问的应用程序未正确安装或设置为不正确的路径。|
|3|InvalidCredentials|用户输入的用户名或密码与任何现有的用户名或密码都不匹配。 请查看凭据是否有拼写错误，然后重试。|
|8|ConnectionBroken|客户端和网关或服务器之间的连接已断开。 除非意外发生，否则不需要执行任何操作。|
|14|UnexpectedNetworkDisconnect|断开与网络的连接。 要求用户再次连接。|
|24|ReverseConnectFailed|主机虚拟机没有直通 RD 网关。 请确保可以解析网关 IP 地址。|
|1322|ConnectionFailedNoMappingOfSIDinAD|用户不是 Active Directory 的成员。 按照 [Active Directory 管理中心](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center)中的说明进行添加。|

## <a name="next-steps"></a>后续步骤

若要了解有关 Windows 虚拟桌面中的角色的详细信息，请参阅 [Windows 虚拟桌面环境](environment-setup-2019.md)。

若要查看适用于 Windows 虚拟桌面的可用 PowerShell cmdlet 的列表，请参阅 [PowerShell 参考](/powershell/windows-virtual-desktop/overview)。
