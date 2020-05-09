---
title: Windows 虚拟桌面诊断问题-Azure
description: 如何使用 Windows 虚拟桌面诊断功能来诊断问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e529144198d0c635e74955e98d47dd46ac4fb733
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615144"
---
# <a name="identify-and-diagnose-issues"></a>识别和诊断问题

>[!IMPORTANT]
>此内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的秋季2019版本。 如果尝试管理春季2020更新中引入的 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[此文](../diagnostics-role-service.md)。

Windows 虚拟桌面提供一项诊断功能，使管理员能够通过单个界面识别问题。 每当用户与系统交互时，Windows 虚拟桌面角色就会记录诊断活动。 每个日志都包含相关信息，例如事务中涉及的 Windows 虚拟桌面角色、错误消息、租户信息和用户信息。 诊断活动由最终用户和管理操作创建，可分为三个主要的存储桶：

* 源订阅活动：最终用户在尝试通过 Microsoft 远程桌面应用程序连接到其源时触发这些活动。
* 连接活动：最终用户在尝试通过 Microsoft 远程桌面应用程序连接到桌面或 RemoteApp 时，将触发这些活动。
* 管理活动：无论何时在系统上执行管理操作（如创建主机池、将用户分配到应用组和创建角色分配），管理员都会触发这些活动。
  
由于诊断角色服务本身是 Windows 虚拟桌面的一部分，因此无法访问 Windows 虚拟桌面的连接将不会显示在诊断结果中。 当最终用户遇到网络连接问题时，可能会出现 Windows 虚拟桌面连接问题。

若要开始，请下载并导入要在 PowerShell 会话中使用[的 Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)（如果尚未这样做）。 然后，运行以下 cmdlet 登录到你的帐户：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>诊断 PowerShell 问题

Windows 虚拟桌面诊断只使用一个 PowerShell cmdlet，但包含许多可选参数来帮助缩小和隔离问题。 以下部分列出了可用于诊断问题的 cmdlet。 大多数筛选器可以一起应用。 括号中列出的值（如`<tenantName>`）应替换为适用于你的情况的值。

>[!IMPORTANT]
>诊断功能适用于单用户故障排除。 使用 PowerShell 的所有查询都必须包含 *-UserName*或 *-ActivityID*参数。 对于监视功能，请使用 Log Analytics。 有关如何向工作区发送诊断数据的详细信息，请参阅[使用 Log Analytics 诊断功能](diagnostics-log-analytics-2019.md)。 

### <a name="filter-diagnostic-activities-by-user"></a>按用户筛选诊断活动

**-UserName**参数返回由指定用户启动的诊断活动列表，如下面的示例 cmdlet 中所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

**-UserName**参数也可以与其他可选筛选参数组合。

### <a name="filter-diagnostic-activities-by-time"></a>按时间筛选诊断活动

可以用 **-StartTime**和 **-EndTime**参数筛选返回的诊断活动列表。 **-StartTime**参数将返回从特定日期开始的诊断活动列表，如下面的示例中所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

**-EndTime**参数可以添加到 cmdlet，其中 **-StartTime**参数用于指定要接收结果的特定时间段。 下面的示例 cmdlet 将从8月1日到8月10日之间返回诊断活动列表。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

**-StartTime**和 **-EndTime**参数也可以与其他可选筛选参数结合。

### <a name="filter-diagnostic-activities-by-activity-type"></a>按活动类型筛选诊断活动

还可以使用 **-ActivityType**参数按活动类型筛选诊断活动。 以下 cmdlet 将返回最终用户连接的列表：

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

以下 cmdlet 将返回管理员管理任务的列表：

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

**RdsDiagnosticActivities** cmdlet 当前不支持指定源作为 ActivityType。

### <a name="filter-diagnostic-activities-by-outcome"></a>按结果筛选诊断活动

您可以通过使用 **-结果**参数的结果来筛选返回的诊断活动列表。 下面的示例 cmdlet 将返回成功诊断活动的列表。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

下面的示例 cmdlet 将返回失败的诊断活动的列表。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

**-结果**参数还可与其他可选筛选参数组合。

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>按活动 ID 检索特定诊断活动

**-ActivityId**参数将返回特定的诊断活动（如果存在），如下面的示例 cmdlet 中所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>查看活动 ID 失败的活动的错误消息

若要查看失败的活动的错误消息，你必须使用 **-详细**参数运行 cmdlet。 您可以通过运行**选择对象**cmdlet 来查看错误列表。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>检索详细诊断活动

**-详细**参数为返回的每个诊断活动提供其他详细信息。 每个活动的格式因其活动类型而异。 **-详细**参数可添加到任何**RdsDiagnosticActivities**查询，如下面的示例中所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>常见错误方案

错误方案在服务的内部和 Windows 虚拟桌面的外部分类。

* 内部问题：指定租户管理员无法缓解的方案，需要将这些方案解析为支持问题。 当通过[Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)提供反馈时，请包括发生问题时的活动 ID 和大致时间范围。
* 外部问题：与系统管理员可以缓解的方案相关。 这些是 Windows 虚拟桌面的外部。

下表列出了管理员可能会遇到的常见错误。

>[!NOTE]
>此列表包括最常见的错误，并会定期更新。 若要确保获得最新的信息，请务必每月至少查看一次本文。

### <a name="external-management-error-codes"></a>外部管理错误代码

|数值代码|错误代码|建议的解决方案|
|---|---|---|
|3|UnauthorizedAccess|尝试运行管理 PowerShell cmdlet 的用户不具有执行此操作或键入用户名错误的权限。|
|1000|TenantNotFound|输入的租户名称与任何现有租户都不匹配。 请查看租户名称中的拼写错误，然后重试。|
|1006|TenantCannotBeRemovedHasSessionHostPools|如果租户包含对象，则不能删除它。 请先删除该会话主机池，然后重试。|
|2000|HostPoolNotFound|输入的主机池名称与任何现有主机池都不匹配。 检查主机池名称是否有拼写错误，然后重试。|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|如果主机池包含对象，则无法将其删除。 先删除主机池中的所有应用组。|
|2004|HostPoolCannotBeRemovedHasSessionHosts|删除会话主机池之前，请先删除所有会话主机。|
|5001|SessionHostNotFound|您所查询的会话主机可能处于脱机状态。 检查主机池的状态。|
|5008|SessionHostUserSessionsExist |在执行预期的管理活动之前，必须注销会话主机上的所有用户。|
|6000|AppGroupNotFound|输入的应用组名称与任何现有的应用组都不匹配。 查看应用组名称中的拼写错误，然后重试。|
|6022|RemoteAppNotFound|输入的 RemoteApp 名称与任何 RemoteApps 都不匹配。 请检查 RemoteApp 名称是否有拼写错误，然后重试。|
|6010|PublishedItemsExist|要发布的资源的名称与已经存在的资源的名称相同。 请更改资源名称，然后重试。|
|7002|NameNotValidWhiteSpace|不要在名称中使用空格。|
|8000|InvalidAuthorizationRoleScope|输入的角色名称与任何现有角色名称都不匹配。 请检查角色名称中的拼写错误，然后重试。 |
|8001|UserNotFound |输入的用户名与任何现有用户名都不匹配。 查看拼写错误，然后重试。|
|8005|UserNotFoundInAAD |输入的用户名与任何现有用户名都不匹配。 查看拼写错误，然后重试。|
|8008|TenantConsentRequired|请按照[此处](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop)的说明为你的租户提供许可。|

### <a name="external-connection-error-codes"></a>外部连接错误代码

|数值代码|错误代码|建议的解决方案|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|会话主机未正确加入到 Active Directory。|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|由于会话主机不可用，连接失败。 检查会话主机的运行状况。|
|-2146233088|ConnectionFailedClientDisconnect|如果经常看到此错误，请确保用户的计算机已连接到网络。|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|主机用户尝试连接到的会话不正常。 调试虚拟机。|
|-2146233088|ConnectionFailedUserNotAuthorized|用户无权访问已发布的应用或桌面。 在管理员删除已发布资源后，可能会出现此错误。 要求用户刷新远程桌面应用程序中的源。|
|2|FileNotFound|用户尝试访问的应用程序未正确安装或设置为不正确的路径。|
|3|InvalidCredentials|用户输入的用户名或密码与任何现有的用户名或密码都不匹配。 查看输入错误的凭据，然后重试。|
|8|ConnectionBroken|客户端和网关或服务器之间的连接已断开。 除非意外发生，否则不需要执行任何操作。|
|14|UnexpectedNetworkDisconnect|断开与网络的连接。 要求用户再次连接。|
|24|ReverseConnectFailed|主机虚拟机没有可直接连接到 RD 网关的行为。 确保可以解析网关 IP 地址。|

## <a name="next-steps"></a>后续步骤

若要了解有关 Windows 虚拟桌面中的角色的详细信息，请参阅[Windows 虚拟桌面环境](environment-setup-2019.md)。

若要查看适用于 Windows 虚拟桌面的可用 PowerShell cmdlet 的列表，请参阅[PowerShell 参考](/powershell/windows-virtual-desktop/overview)。
