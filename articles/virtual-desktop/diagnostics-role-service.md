---
title: Windows 虚拟桌面诊断问题 - Azure
description: 如何使用 Windows 虚拟桌面诊断功能来诊断问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ce85fb70e1480ad285eee78fe20faa8d77b9a147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254256"
---
# <a name="identify-and-diagnose-issues"></a>识别和诊断问题

Windows 虚拟桌面提供诊断功能，允许管理员通过单个界面识别问题。 每当用户与系统交互时，Windows 虚拟桌面角色都会记录诊断活动。 每个日志都包含相关信息，如事务中涉及的 Windows 虚拟桌面角色、错误消息、租户信息和用户信息。 诊断活动由最终用户和管理操作创建，可以分为三个主要存储桶：

* 源订阅活动：最终用户在尝试通过 Microsoft 远程桌面应用程序连接到其源时触发这些活动。
* 连接活动：最终用户在尝试通过 Microsoft 远程桌面应用程序连接到桌面或远程应用时触发这些活动。
* 管理活动：管理员在系统上执行管理操作（如创建主机池、将用户分配给应用组以及创建角色分配）时触发这些活动。
  
无法访问 Windows 虚拟桌面的连接不会显示在诊断结果中，因为诊断角色服务本身是 Windows 虚拟桌面的一部分。 当最终用户遇到网络连接问题时，可能会发生 Windows 虚拟桌面连接问题。

要开始使用[，请下载并导入 Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)，以便如果您尚未在 PowerShell 会话中使用。 然后，运行以下 cmdlet 登录到你的帐户：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>诊断 PowerShell 的问题

Windows 虚拟桌面诊断仅使用一个 PowerShell cmdlet，但包含许多可选参数，以帮助缩小和隔离问题。 以下各节列出了可以运行以诊断问题的 cmdlet。 大多数筛选器可以一起应用。 括号中列出的值（如`<tenantName>`）应替换为适用于您的情况的值。

>[!IMPORTANT]
>诊断功能用于单用户故障排除。 使用 PowerShell 的所有查询都必须包括 *-用户名*或 *-ActivityID*参数。 要监视功能，请使用日志分析。 有关如何将诊断数据发送到工作区的详细信息，请参阅[使用日志分析进行诊断功能](diagnostics-log-analytics.md)。 

### <a name="filter-diagnostic-activities-by-user"></a>按用户筛选诊断活动

**-UserName**参数返回由指定用户启动的诊断活动的列表，如下例 cmdlet 所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

**-用户名**参数也可以与其他可选筛选参数组合。

### <a name="filter-diagnostic-activities-by-time"></a>按时间筛选诊断活动

您可以使用 **-Starttime**和 **-EndTime**参数筛选返回的诊断活动列表。 **-StartTime**参数将返回从特定日期开始的诊断活动列表，如以下示例所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

**-EndTime**参数可以添加到带有 **-StartTime**参数的 cmdlet 中，以指定要接收结果的特定时间段。 以下示例 cmdlet 将返回 8 月 1 日至 8 月 10 日期间的诊断活动列表。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

**-Starttime**和 **-EndTime**参数也可以与其他可选筛选参数组合使用。

### <a name="filter-diagnostic-activities-by-activity-type"></a>按活动类型筛选诊断活动

您还可以使用 **-ActivityType**参数按活动类型筛选诊断活动。 以下 cmdlet 将返回最终用户连接的列表：

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

以下 cmdlet 将返回管理员管理任务的列表：

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

**Get-Rds 诊断活动**cmdlet 当前不支持将源指定为活动类型。

### <a name="filter-diagnostic-activities-by-outcome"></a>按结果筛选诊断活动

您可以使用 **-结果**参数按结果筛选返回的诊断活动列表。 以下示例 cmdlet 将返回成功诊断活动的列表。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

以下示例 cmdlet 将返回失败的诊断活动的列表。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

**-结果**参数也可以与其他可选的筛选参数组合。

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>按活动 ID 检索特定诊断活动

**-ActivityId**参数如果存在，将返回特定的诊断活动，如下例 cmdlet 所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>按活动 ID 查看失败活动的错误消息

要查看失败活动的错误消息，必须使用 **-详细**参数运行 cmdlet。 您可以通过运行**选择对象**cmdlet 来查看错误列表。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>检索详细的诊断活动

**-详细**参数提供了返回的每个诊断活动的其他详细信息。 每个活动的格式因活动类型而异。 **-详细**参数可以添加到任何**获取诊断活动**查询，如以下示例所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>常见错误方案

错误方案在服务内部和 Windows 虚拟桌面外部分类。

* 内部问题：指定租户管理员无法缓解且需要作为支持问题解决的方案。 当通过 Windows[虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)提供反馈时 ，包括活动 ID 和问题发生的大致时间范围。
* 外部问题：与系统管理员可以缓解的方案相关。 这些是 Windows 虚拟桌面的外部。

下表列出了管理员可能会遇到的常见错误。

>[!NOTE]
>此列表包含最常见的错误，并在常规节奏上更新。 为了确保您拥有最新信息，请确保每月至少查看一次本文。

### <a name="external-management-error-codes"></a>外部管理错误代码

|数字代码|错误代码|建议的解决方案|
|---|---|---|
|3|未经授权的访问|尝试运行管理 PowerShell cmdlet 的用户没有这样做的权限或键入了错误的用户名。|
|1000|租户未找到|您输入的租户名称与任何现有租户不匹配。 查看租户名称中的拼写错误，然后重试。|
|1006|无法删除的租户具有会话主机池|不能删除租户，只要它包含对象。 请先删除会话主机池，然后重试。|
|2000|主机池未找到|您输入的主机池名称与任何现有主机池不匹配。 查看主机池名称中的拼写错误，然后重试。|
|2005|主机池不可删除具有应用程序组|只要主机池包含对象，它就不会删除它。 首先删除主机池中的所有应用组。|
|2004|主机池不可删除的会话主机|在删除会话主机池之前，请先删除所有会话主机。|
|5001|会话主机未找到|您查询的会话主机可能处于脱机状态。 检查主机池的状态。|
|5008|会话主机用户会话存在 |在执行预期的管理活动之前，必须注销会话主机上的所有用户。|
|6000|应用组未找到|您输入的应用组名称与任何现有应用组不匹配。 查看应用组名称中的拼写错误，然后重试。|
|6022|远程应用未找到|您输入的 RemoteApp 名称与任何远程应用不匹配。 查看 RemoteApp 名称中有拼写错误，然后重试。|
|6010|已发布项目存在|您尝试发布的资源的名称与已存在的资源的名称相同。 请更改资源名称，然后重试。|
|7002|名称 无效空白|不要在名称中使用空格。|
|8000|无效授权功能范围|您输入的角色名称与任何现有角色名称不匹配。 查看拼写错误的角色名称，然后重试。 |
|8001|UserNotFound |您输入的用户名与任何现有用户名不匹配。 查看拼写错误的名称，然后重试。|
|8005|用户不芬兰 |您输入的用户名与任何现有用户名不匹配。 查看拼写错误的名称，然后重试。|
|8008|租户同意要求|按照[此处](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop)的说明为您的租户提供同意。|

### <a name="external-connection-error-codes"></a>外部连接错误代码

|数字代码|错误代码|建议的解决方案|
|---|---|---|
|-2147467259|连接失败AdTrusted关系失败|会话主机未正确加入活动目录。|
|-2146233088|连接失败 用户有效会话但 Rdshis 不健康|连接失败，因为会话主机不可用。 检查会话主机的运行状况。|
|-2146233088|连接失败客户端断开连接|如果经常看到此错误，请确保用户的计算机已连接到网络。|
|-2146233088|连接失败 无健康Rdsh可用|主机用户尝试连接到的会话不正常。 调试虚拟机。|
|-2146233088|连接失败用户未授权|用户没有访问已发布的应用或桌面的权限。 管理员删除已发布资源后可能会出现错误。 要求用户刷新远程桌面应用程序中的源。|
|2|FileNotFound|用户尝试访问的应用程序安装不正确或设置为错误的路径。|
|3|InvalidCredentials|用户输入的用户名或密码与任何现有用户名或密码不匹配。 查看拼写错误凭据，然后重试。|
|8|连接中断|客户端与网关或服务器之间的连接断开。 除非意外发生，否则无需执行任何操作。|
|14|意外网络断开|与网络的连接断开。 要求用户再次连接。|
|24|反向连接失败|主机虚拟机没有直接的视线到 RD 网关。 确保可以解析网关 IP 地址。|

## <a name="next-steps"></a>后续步骤

要了解有关 Windows 虚拟桌面中角色的更多信息，请参阅[Windows 虚拟桌面环境](environment-setup.md)。

要查看适用于 Windows 虚拟桌面的可用 PowerShell cmdlet 的列表，请参阅[PowerShell 参考](/powershell/windows-virtual-desktop/overview)。
