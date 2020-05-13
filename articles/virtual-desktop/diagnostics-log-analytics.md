---
title: Windows 虚拟桌面诊断日志分析-Azure
description: 如何将 log analytics 用于 Windows 虚拟桌面诊断功能。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 76a5e12eee7a325a73b3c17dba6c775b6984b89a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195908"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>使用诊断功能 Log Analytics

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 2020 春季更新版。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面 2019 秋季版，请参阅[此文](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md)。
>
> Windows 虚拟桌面 2020 春季更新版目前为公共预览版。 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Windows 虚拟桌面使用[Azure Monitor](../azure-monitor/overview.md)来监视和警报，例如许多其他 Azure 服务。 这样，管理员便可以通过单个界面识别问题。 此服务为用户和管理操作创建活动日志。 每个活动日志分为以下类别：  

- 管理活动：
    - 跟踪尝试使用 Api 还是 PowerShell 更改 Windows 虚拟桌面对象是否成功。 例如，某人是否可以使用 PowerShell 成功创建主机池？
- 馈电 
    - 用户能否成功订阅工作区？ 
    - 用户是否能看到远程桌面客户端中发布的所有资源？
- 连接: 
    - 当用户启动和完成与服务的连接时。 
- 主机注册： 
    - 会话主机是否在连接时已成功注册到服务？
- 错误： 
    - 用户是否遇到特定活动的任何问题？ 只要信息与活动联接，此功能就会生成一个跟踪活动数据的表。
- 检查点  
    - 已达到活动生存期的特定步骤。 例如，在会话期间，用户负载平衡到特定主机，然后用户在连接过程中登录，等等。

由于诊断角色服务本身是 Windows 虚拟桌面的一部分，因此无法访问 Windows 虚拟桌面的连接将不会显示在诊断结果中。 当用户遇到网络连接问题时，可能会出现 Windows 虚拟桌面连接问题。

Azure Monitor 使你能够分析 Windows 虚拟桌面数据并查看虚拟机（VM）性能计数器，这些计数器都在同一工具内。 本文将介绍如何为 Windows 虚拟桌面环境启用诊断。 

>[!NOTE] 
>若要了解如何在 Azure 中监视 Vm，请参阅[利用 Azure Monitor 监视 azure 虚拟机](../azure-monitor/insights/monitor-vm-azure.md)。 此外，请确保[查看性能计数器阈值](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds)，以便更好地了解你在会话主机上的用户体验。

## <a name="before-you-get-started"></a>开始操作之前

你需要创建工作区，然后才能使用 Log Analytics。 为此，请按照以下两篇文章之一中的说明进行操作：

- 如果你更喜欢使用 Azure 门户，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。
- 如果喜欢 PowerShell，请参阅[使用 Powershell 创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace-posh.md)。

创建工作区后，请按照[将 Windows 计算机连接到 Azure Monitor](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)中的说明获取以下信息：

- 工作区 ID
- 工作区的主密钥

稍后将在安装过程中需要此信息。

请确保查看 Azure Monitor 的权限管理，为监视和维护你的 Windows 虚拟桌面环境的用户启用数据访问。 有关详细信息，请参阅[Azure Monitor 的角色、权限和安全入门](../azure-monitor/platform/roles-permissions-security.md)。 

## <a name="push-diagnostics-data-to-your-workspace"></a>将诊断数据推送到你的工作区

可以将 Windows 虚拟桌面对象中的诊断数据推送到工作区的 Log Analytics 中。 首次创建对象时，可以立即设置此功能。

为新对象设置 Log Analytics：

1. 登录到 Azure 门户并前往**Windows 虚拟桌面**。 

2. 导航到要捕获其日志和事件的对象（例如，主机池、应用组或工作区）。 

3. 在屏幕左侧的菜单中选择 "**诊断设置**"。 

4. 在屏幕右侧显示的菜单中，选择 "**添加诊断设置**"。 
   
    "诊断设置" 页中显示的选项会因所编辑的对象类型而异。

    例如，在为应用组启用诊断时，将看到用于配置检查点、错误和管理的选项。 对于工作区，这些类别将配置源以跟踪用户订阅应用列表的时间。 若要了解有关诊断设置的详细信息，请参阅[创建诊断设置以收集 Azure 中的资源日志和指标](../azure-monitor/platform/diagnostic-settings.md)。 

     >[!IMPORTANT] 
     >请记得为要监视的每个 Azure 资源管理器对象启用诊断。 启用诊断后，数据将可用于活动。 首次设置后可能需要几个小时。  

5. 输入设置配置的名称，然后选择 "**发送到 Log Analytics**"。 你使用的名称不应包含空格，并且应符合[Azure 命名约定](../azure-resource-manager/management/resource-name-rules.md)。 作为日志的一部分，你可以选择想要添加到 Log Analytics 的所有选项，如检查点、错误、管理等。

6. 选择“保存”  。

>[!NOTE]
>Log Analytics 提供将数据流式传输到[事件中心](../event-hubs/event-hubs-about.md)或将其存档在存储帐户中的选项。 若要了解有关此功能的详细信息，请参阅将[azure 监视数据流式传输到事件中心](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)和[将 Azure 资源日志存档到存储帐户](../azure-monitor/platform/resource-logs-collect-storage.md)。 

## <a name="how-to-access-log-analytics"></a>如何访问 Log Analytics

可以在 Azure 门户或 Azure Monitor 上访问 Log Analytics 工作区。

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Log Analytics 工作区上的访问 Log Analytics

1. 登录到 Azure 门户。  

2. 搜索**Log Analytics 工作区**。 

3. 在 "服务" 下，选择 " **Log Analytics 工作区**"。 
   
4. 从列表中，选择为 Windows 虚拟桌面对象配置的工作区。

5. 进入工作区后，选择 "**日志**"。 可以通过**搜索**功能筛选菜单列表。 

### <a name="access-log-analytics-on-azure-monitor"></a>访问 Azure Monitor 上的 Log Analytics

1. 登录到 Azure 门户

2. 搜索并选择 "**监视器**"。 

3. 选择 "**日志**"。

4. 按照 "日志记录" 页中的说明设置查询的作用域。  

5. 你已准备好查询诊断。 所有诊断表都具有 "WVD" 前缀。

## <a name="cadence-for-sending-diagnostic-events"></a>用于发送诊断事件的节奏

完成后，会将诊断事件发送到 Log Analytics。

仅 Log Analytics 连接活动的以下中间状态的报表：

- 已启动：用户在远程桌面客户端中选择并连接到应用或桌面。
- 已连接：当用户成功连接到承载应用或桌面的 VM 时。
- 已完成：当用户或服务器断开与活动发生时所在的会话时。

## <a name="example-queries"></a>查询示例

以下示例查询显示诊断功能如何为系统中最常见的活动生成报告。

若要获取用户所建立的连接列表，请运行以下 cmdlet：

```kusto
WVDConnections 
| project-away TenantId,SourceSystem 
| summarize arg_max(TimeGenerated, *), StartTime =  min(iff(State== 'Started', TimeGenerated , datetime(null) )), ConnectTime = min(iff(State== 'Connected', TimeGenerated , datetime(null) ))   by CorrelationId 
| join kind=leftouter ( 
    WVDErrors 
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId 
    ) on CorrelationId     
| join kind=leftouter ( 
   WVDCheckpoints 
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId 
   | mv-apply Checkpoints on 
    ( 
        order by todatetime(Checkpoints['Time']) asc 
        | summarize Checkpoints=makelist(Checkpoints) 
    ) 
   ) on CorrelationId 
| project-away CorrelationId1, CorrelationId2 
| order by  TimeGenerated desc 
```

查看用户的源活动：

```kusto
WVDFeeds  
| project-away TenantId,SourceSystem  
| join kind=leftouter (  
    WVDErrors  
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId  
    ) on CorrelationId      
| join kind=leftouter (  
   WVDCheckpoints  
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId  
   | mv-apply Checkpoints on  
    (  
        order by todatetime(Checkpoints['Time']) asc  
        | summarize Checkpoints=makelist(Checkpoints)  
    )  
   ) on CorrelationId  
| project-away CorrelationId1, CorrelationId2  
| order by  TimeGenerated desc 
```

查找单个用户的所有连接： 

```kusto
|where UserName == "userupn" 
|take 100 
|sort by TimeGenerated asc, CorrelationId 
```
 

要查找用户每天连接的次数，请执行以下操作：

```kusto
WVDConnections 
|where UserName == "userupn" 
|take 100 
|sort by TimeGenerated asc, CorrelationId 
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d) 
```
 

按用户查找会话持续时间：

```kusto
let Events = WVDConnections | where UserName == "userupn" ; 
Events 
| where State == "Connected" 
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated 
| join (Events 
| where State == "Completed" 
| project EndTime=TimeGenerated, CorrelationId) 
on CorrelationId 
| project Duration = EndTime - StartTime, ResourceAlias 
| sort by Duration asc 
```

查找特定用户的错误：

```kusto
WVDErrors
| where UserName == "userupn" 
|take 100
```

确定是否发生了特定错误：

```kusto
WVDErrors 
| where CodeSymbolic =="ErrorSymbolicCode" 
| summarize count(UserName) by CodeSymbolic 
```

查找所有用户的错误发生次数：

```kusto
WVDErrors 
| where ServiceError =="false" 
| summarize usercount = count(UserName) by CodeSymbolic 
| sort by usercount desc
| render barchart 
```

>[!NOTE]
>最重要的故障排除表是 WVDErrors。 使用此查询来了解用户在订阅应用或桌面列表时用户活动（如连接或源）出现的问题。 该表会显示管理错误和主机注册问题。
>
>在公共预览版期间，如果需要帮助解决问题，请确保为帮助请求中的错误提供了 CorrelationID。 此外，请确保服务错误值始终显示 ServiceError = "false"。 如果值为 "false"，则表示您的最终管理员任务可以解决此问题。 如果 ServiceError = "true"，则需要将问题升级到 Microsoft。

## <a name="next-steps"></a>后续步骤 

若要查看诊断功能可以识别的常见错误方案，请参阅[确定和诊断问题](diagnostics-role-service.md#common-error-scenarios)。
