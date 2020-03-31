---
title: Windows 虚拟桌面诊断日志分析 - Azure
description: 如何使用日志分析与 Windows 虚拟桌面诊断功能。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 355acb081afef8c78cdf971c7a82acdb91ab5593
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127962"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>使用日志分析进行诊断功能

Windows 虚拟桌面提供诊断功能，允许管理员通过单个界面识别问题。 每当分配 Windows 虚拟桌面角色的人员使用该服务时，此功能都会记录诊断信息。 每个日志包含有关活动中涉及的 Windows 虚拟桌面角色的信息、会话期间出现的任何错误消息、租户信息和用户信息。 诊断功能为用户和管理操作创建活动日志。 每个活动日志都属于三个主要类别： 

- 源订阅活动：当用户尝试通过 Microsoft 远程桌面应用程序连接到其源时。
- 连接活动：当用户尝试通过 Microsoft 远程桌面应用程序连接到桌面或远程应用时。
- 管理活动：当管理员对系统执行管理操作时，例如创建主机池、将用户分配给应用组以及创建角色分配。

无法访问 Windows 虚拟桌面的连接不会显示在诊断结果中，因为诊断角色服务本身是 Windows 虚拟桌面的一部分。 当用户遇到网络连接问题时，可能会出现 Windows 虚拟桌面连接问题。

## <a name="why-you-should-use-log-analytics"></a>为什么应该使用日志分析

我们建议您使用日志分析来分析 Azure 客户端中超出单用户故障排除的诊断数据。 您可以将 VM 性能计数器拉入日志分析中，因此有一个工具用于收集部署信息。

## <a name="before-you-get-started"></a>开始操作之前

在将日志分析与诊断功能配合使用之前，您需要[创建一个工作区](../azure-monitor/learn/quick-collect-windows-computer.md#create-a-workspace)。

创建工作区后，请按照[将 Windows 计算机连接到 Azure 监视器](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)中的说明来获取以下信息： 

- 工作区 ID
- 工作区的主键

稍后在设置过程中需要此信息。

## <a name="push-diagnostics-data-to-your-workspace"></a>将诊断数据推送到工作区 

您可以将 Windows 虚拟桌面租户的诊断数据推送到工作区的日志分析中。 首次创建租户时，可以通过将工作区链接到租户来设置此功能，也可以稍后使用现有租户进行设置。

要在设置新租户时将租户链接到日志分析工作区，请运行以下 cmdlet 以使用租户创建者用户帐户登录到 Windows 虚拟桌面： 

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com 
```

如果要链接现有租户而不是新租户，请改为运行此 cmdlet： 

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

您需要为每个要链接到日志分析的租户运行这些 cmdlet。 

>[!NOTE]
>如果不想在创建租户时链接日志分析工作区，请改为运行`New-RdsTenant`cmdlet。 

## <a name="cadence-for-sending-diagnostic-events"></a>发送诊断事件的节奏

诊断事件完成后会发送到日志分析。  

## <a name="example-queries"></a>查询示例

以下示例查询显示诊断功能如何为系统中最频繁的活动生成报告：

第一个示例显示由具有受支持的远程桌面客户端的用户启动的连接活动：

```powershell
WVDActivityV1_CL 

| where Type_s == "Connection" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```

下一个示例查询显示管理员对租户的管理活动：

```powershell
WVDActivityV1_CL 

| where Type_s == "Management" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```
 
## <a name="stop-sending-data-to-log-analytics"></a>停止将数据发送到日志分析 

要停止将数据从现有租户发送到 Log Analytics，请运行以下 cmdlet 并设置空字符串：

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

您需要为要停止从中发送数据的每个租户运行此 cmdlet。 

## <a name="next-steps"></a>后续步骤 

要查看诊断功能可以为您识别的常见错误方案，请参阅[识别和诊断问题](diagnostics-role-service.md#common-error-scenarios)。
