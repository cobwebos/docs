---
title: Windows 虚拟桌面诊断日志分析-Azure
description: 如何将 log analytics 用于 Windows 虚拟桌面诊断功能。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 05bb7274fe598df45ce14bfc89b606aec3f869c9
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615534"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>使用诊断功能 Log Analytics

>[!IMPORTANT]
>此内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的秋季2019版本。 如果尝试管理春季2020更新中引入的 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[此文](../diagnostics-log-analytics.md)。

Windows 虚拟桌面提供一项诊断功能，使管理员能够通过单个界面识别问题。 每次分配 Windows 虚拟桌面角色的用户使用该服务时，此功能都会记录诊断信息。 每个日志都包含有关活动中涉及的 Windows 虚拟桌面角色、在会话过程中出现的任何错误消息、租户信息和用户信息的信息。 诊断功能将为用户和管理操作创建活动日志。 每个活动日志分为以下三个主要类别： 

- 源订阅活动：当用户尝试通过 Microsoft 远程桌面应用程序连接到其源时。
- 连接活动：当用户尝试通过 Microsoft 远程桌面应用程序连接到桌面或 RemoteApp 时。
- 管理活动：管理员在系统上执行管理操作，例如创建主机池、将用户分配到应用组以及创建角色分配。

由于诊断角色服务本身是 Windows 虚拟桌面的一部分，因此无法访问 Windows 虚拟桌面的连接将不会显示在诊断结果中。 当用户遇到网络连接问题时，可能会出现 Windows 虚拟桌面连接问题。

## <a name="why-you-should-use-log-analytics"></a>为什么要使用 Log Analytics

建议使用 Log Analytics 分析 Azure 客户端中超出单用户故障排除的诊断数据。 你可以将 VM 性能计数器纳入到 Log Analytics 你有一个工具来为你的部署收集信息。

## <a name="before-you-get-started"></a>开始操作之前

你需要[创建工作区](../../azure-monitor/learn/quick-collect-windows-computer.md#create-a-workspace)，然后才能将 Log Analytics 用于诊断功能。

创建工作区后，请按照[将 Windows 计算机连接到 Azure Monitor](../../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)中的说明获取以下信息： 

- 工作区 ID
- 工作区的主密钥

稍后将在安装过程中需要此信息。

## <a name="push-diagnostics-data-to-your-workspace"></a>将诊断数据推送到你的工作区 

你可以将 Windows 虚拟桌面租户中的诊断数据推送到你的工作区的 Log Analytics 中。 首次创建租户时，你可以立即设置此功能，方法是将你的工作区链接到你的租户，或者可以在以后使用现有租户进行设置。

若要在设置新租户时将租户链接到 Log Analytics 工作区，请运行以下 cmdlet，以通过 TenantCreator 用户帐户登录到 Windows 虚拟桌面： 

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com 
```

如果要链接现有租户而不是新租户，请改为运行此 cmdlet： 

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

需要为要链接到 Log Analytics 的每个租户运行这些 cmdlet。 

>[!NOTE]
>如果你不想在创建租户时链接 Log Analytics 工作区，请改为运行`New-RdsTenant` cmdlet。 

## <a name="cadence-for-sending-diagnostic-events"></a>用于发送诊断事件的节奏

完成后，会将诊断事件发送到 Log Analytics。  

## <a name="example-queries"></a>查询示例

以下示例查询显示诊断功能如何为系统中最常见的活动生成报告：

第一个示例显示用户使用受支持的远程桌面客户端启动的连接活动：

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

下面的示例查询显示了租户上管理员的管理活动：

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
 
## <a name="stop-sending-data-to-log-analytics"></a>停止将数据发送到 Log Analytics 

若要停止将数据从现有租户发送到 Log Analytics，请运行以下 cmdlet 并设置空字符串：

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

需要为想要停止发送数据的每个租户运行此 cmdlet。 

## <a name="next-steps"></a>后续步骤 

若要查看诊断功能可以识别的常见错误方案，请参阅[确定和诊断问题](diagnostics-role-service-2019.md#common-error-scenarios)。
