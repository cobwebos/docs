---
title: 查询 Azure 自动化更新管理日志
description: 本文介绍如何在 Log Analytics 工作区中查询更新管理的日志。
services: automation
ms.subservice: update-management
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: 777d794716c7c17caf8d4c73007b91a625f40043
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91264297"
---
# <a name="query-update-management-logs"></a>查询更新管理日志

除了在部署更新管理期间提供的详细信息以外，你还可以搜索 Log Analytics 工作区中存储的日志。 若要从自动化帐户中搜索日志，请选择“更新管理”，并打开与部署关联的 Log Analytics 工作区。

还可以自定义日志查询，或者从不同的客户端使用日志查询。 请参阅 [Log Analytics 搜索 API 文档](https://dev.loganalytics.io/)。

## <a name="query-update-records"></a>查询更新记录

更新管理收集 Windows 和 Linux VM 的记录，以及显示在日志搜索结果中的数据类型。 以下部分将介绍这些记录。

### <a name="query-required-updates"></a>查询所需的更新

一条 `RequiredUpdate` 类型的记录会被创建，以表示计算机所需的更新。 这些记录的属性在下表中列出：

| 属性 | 说明 |
|----------|-------------|
| Computer | 报告计算机的完全限定域名。 |
| KBID | Windows 更新的知识库文章 ID。 |
| ManagementGroupName | Operations Manager 管理组或 Log Analytics 工作区的名称。 |
| 产品 | 该更新所适用的产品。 |
| PublishDate | 该更新可供从 Windows 更新下载和安装的日期。 |
| 服务器 | | 
| SourceHealthServiceId | 表示 Log Analytics Windows 代理 ID 的唯一标识符。 |
| SourceSystem | *OperationsManager* |
| TenantId | 表示组织的 Azure Active Directory 实例的唯一标识符。 |
| TimeGenerated | 创建记录的日期和时间。 |
| 类型 | *Update* |
| UpdateClassification | 指示可应用的更新类型。 对于 Windows：<br> 关键更新<br> *安全更新*<br> 更新汇总<br> 功能包<br> 服务包<br> 定义更新<br> *工具*<br> 更新。 对于 Linux：<br> 关键和安全更新<br> *其他* |
| UpdateSeverity | 漏洞的严重性分级。 值为：<br> *严重*<br> 重要说明<br> 中<br> *低* |
| UpdateTitle | 更新的标题。|

### <a name="query-update-record"></a>查询更新记录

一条 `Update` 类型的记录会被创建，以表示计算机的可用更新及其在计算机上的安装状态。 这些记录的属性在下表中列出：

| 属性 | 说明 |
|----------|-------------|
| ApprovalSource | 仅适用于 Windows 操作系统。 记录的批准来源。 值为 Microsoft Update。 |
| 已批准 | 如果记录已获批准，则为 True，否则为 False。 |
| 分类 | 批准分类。 值为 Updates。 |
| Computer | 报告计算机的完全限定域名。 |
| ComputerEnvironment | 环境。 可能的值为 Azure 或 Non-Azure。 |
| MSRCBulletinID | 安全公告 ID 号。 |
| MSRCSeverity | 漏洞的严重性分级。 值为：<br> 严重<br> 重要<br> 中等<br> 低 |  
| KBID | Windows 更新的知识库文章 ID。 |
| ManagementGroupName | Operations Manager 管理组或 Log Analytics 工作区的名称。 |
| UpdateID | 软件更新的唯一标识符。 |
| RevisionNumber | 特定更新修订版的修订号。 |
| 可选 | 如果记录是可选的，则为 True，否则为 False。 |
| RebootBehavior | 安装/卸载更新后的重启行为。 |
| _ResourceId | 与记录关联的资源的唯一标识符。 |
| 类型 | 记录类型。 值为 Update。 |
| VMUUID | 虚拟机的唯一标识符。 |
| MG | 管理组或 Log Analytics 工作区的唯一标识符。 |
| TenantId | 表示组织的 Azure Active Directory 实例的唯一标识符。 |
| SourceSystem | 记录的源系统。 该值为 `OperationsManager`。 |
| TimeGenerated | 创建记录的日期和时间。 |
| SourceComputerId | 表示源计算机的唯一标识符。 |
| 标题 | 更新的标题。 |
| PublishedDate (UTC) | 该更新可供从 Windows 更新下载和安装的日期。  |
| UpdateState | 该更新的当前状态。 |
| 产品 | 该更新所适用的产品。 |
| SubscriptionId | Azure 订阅的唯一标识符。 |
| ResourceGroup | 资源所属的资源组的名称。 |
| ResourceProvider | 资源提供程序。 |
| 资源 | 资源的名称。 |
| ResourceType | 资源类型。 |

### <a name="query-update-agent-record"></a>查询更新代理记录

一条 `UpdateAgent` 类型的记录会被创建，以提供计算机上的更新代理的详细信息。 这些记录的属性在下表中列出：

| 属性 | 说明 |
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | |
| AutomaticUpdateEnabled | |
| Computer | 报告计算机的完全限定域名。 |
| DaySinceLastUpdateBucket | |
| ManagementGroupName | Operations Manager 管理组或 Log Analytics 工作区的名称。 |
| OSVersion | 操作系统的版本。 |
| 服务器 | |
| SourceHealthServiceId | 表示 Log Analytics Windows 代理 ID 的唯一标识符。 |
| SourceSystem | 记录的源系统。 该值为 `OperationsManager`。 |
| TenantId | 表示组织的 Azure Active Directory 实例的唯一标识符。 |
| TimeGenerated | 创建记录的日期和时间。 |
| 类型 | 记录类型。 值为 Update。 |
| WindowsUpdateAgentVersion | Windows 更新代理的版本。 |
| WSUSServer | 如果 Windows 更新代理出现问题，则会显示错误以帮助进行故障排除。 |

### <a name="query-update-deployment-status-record"></a>查询更新部署状态记录

一条 `UpdateRunProgress` 类型的记录会被创建，以便按计算机提供计划部署的更新部署状态。 这些记录的属性在下表中列出：

| 属性 | 说明 |
|----------|-------------|
| Computer | 报告计算机的完全限定域名。 |
| ComputerEnvironment | 环境。 值为 Azure 或 Non-Azure。 |
| CorrelationId | 用于该更新的 Runbook 作业运行的唯一标识符。 |
| EndTime | 结束同步过程的时间。 *当前未使用此属性。请参阅 TimeGenerated。* |
| ErrorResult | 无法安装更新时生成的 Windows 更新错误代码。 |
| InstallationStatus | 客户端计算机上可能的更新安装状态：<br> `NotStarted` - 作业尚未触发。<br> `FailedToStart` - 无法在计算机上启动作业。<br> `Failed` - 作业已启动，但失败并发生异常。<br> `InProgress` - 作业正在进行。<br> `MaintenanceWindowExceeded` - 执行尚未完成，但已达到维护时段间隔。<br> `Succeeded` - 作业成功。<br> `InstallFailed` - 无法成功安装更新。<br> `NotIncluded`<br> `Excluded` |
| KBID | Windows 更新的知识库文章 ID。 |
| ManagementGroupName | Operations Manager 管理组或 Log Analytics 工作区的名称。 |
| OSType | 操作系统的类型。 值为 Windows 或 Linux。 |
| Products | 该更新所适用的产品。 |
| 资源 | 资源的名称。 |
| ResourceId | 与记录关联的资源的唯一标识符。 |
| ResourceProvider | 资源提供程序。 |
| ResourceType | 资源类型。 |
| SourceComputerId | 表示源计算机的唯一标识符。 |
| SourceSystem | 记录的源系统。 该值为 `OperationsManager`。 |
| StartTime | 计划要安装更新的时间。 *当前未使用此属性。请参阅 TimeGenerated。* |
| SubscriptionId | Azure 订阅的唯一标识符。 |
| SucceededOnRetry | 该值指示是否首次尝试执行更新时失败，以及当前操作是否为重试。 |
| TimeGenerated | 创建记录的日期和时间。 |
| 标题 | 更新的标题。 |
| 类型 | 更新的类型。 该值为 `UpdateRunProgress`。 |
| UpdateId | 软件更新的唯一标识符。 |
| VMUUID | 虚拟机的唯一标识符。 |
| ResourceId | 与记录关联的资源的唯一标识符。 |

### <a name="query-update-summary-record"></a>查询更新摘要记录

一条 `UpdateSummary` 类型的记录会被创建，以便按计算机提供更新摘要。 这些记录的属性在下表中列出：

| properties | 说明 |
|----------|-------------|
| Computer | 报告计算机的完全限定域名。 |
| ComputerEnvironment | 环境。 值为 Azure 或 Non-Azure。 |
| CriticalUpdatesMissing | 缺少的适用关键更新数。 |
| ManagementGroupName | Operations Manager 管理组或 Log Analytics 工作区的名称。 |
| NETRuntimeVersion | 在 Windows 计算机上安装的 .NET Framework 版本。 |
| OldestMissingSecurityUpdateBucket | 最早的缺失安全桶的说明符。 值为：<br> 值小于 30 天表示最近<br> 30 天前<br> 60 天前<br> 90 天前<br> 120 天前<br> 150 天前<br> 180 天前<br> 值大于 180 天表示较早。 |
| OldestMissingSecurityUpdateInDays | 检测为适用更新的最早更新的未安装总天数。 |
| OsVersion | 操作系统的版本。 |
| OtherUpdatesMissing | 检测到的缺失更新计数。 |
| 资源 | 记录的资源名称。 |
| ResourceGroup | 包含该资源的资源组的名称。 |
| ResourceId | 与记录关联的资源的唯一标识符。 |
| ResourceProvider | 资源提供程序。 |
| ResourceType | 资源类型。 |
| RestartPending | 如果正在等待重启，则为 True，否则为 False。 |
| SecurityUpdatesMissing | 适用的缺失安全更新计数。|
| SourceComputerId | 虚拟机的唯一标识符。 |
| SourceSystem | 记录的源系统。 该值为 `OpsManager`。 |
| SubscriptionId | Azure 订阅的唯一标识符。 |
| TimeGenerated | 创建记录的日期和时间。 |
| TotalUpdatesMissing | 适用的缺失更新总数。 |
| 类型 | 记录类型。 该值为 `UpdateSummary`。 |
| VMUUID | 虚拟机的唯一标识符。 |
| WindowsUpdateAgentVersion | Windows 更新代理的版本。 |
| WindowsUpdateSetting | Windows 更新代理的状态。 可能的值包括：<br> `Scheduled installation`<br> `Notify before installation`<br> `Error returned from unhealthy WUA agent` |
| WSUSServer | 如果 Windows 更新代理出现问题，则会显示错误以帮助进行故障排除。 |
| _ResourceId | 与记录关联的资源的唯一标识符。 |

## <a name="sample-queries"></a>示例查询

以下部分提供了为更新管理收集的更新记录的示例日志查询。

### <a name="confirm-that-non-azure-machines-are-enabled-for-update-management"></a>确认是否为非 Azure 计算机启用了更新管理

若要确认直接连接的计算机是否正在与 Azure Monitor 日志通信，请运行以下日志搜索之一。

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

在 Windows 计算机上，可以通过查看以下信息来验证代理与 Azure Monitor 日志的连接：

1. 在控制面板中，打开 **Microsoft Monitoring Agent**。 在“Azure Log Analytics”选项卡上，代理会显示以下消息：“Microsoft Monitoring Agent 已成功连接到 Log Analytics。”

1. 打开“Windows 事件日志”。 转到“应用程序和服务日志\Operations Manager”，搜索来自“服务连接器”源的事件 ID 3000 和事件 ID 5002。  这些事件指示计算机已注册到 Log Analytics 工作区并且正在接收配置。

如果代理无法与 Azure Monitor 日志通信且已配置为通过防火墙或代理服务器与 Internet 通信，请确认是否正确配置了防火墙或代理服务器。 若要了解如何验证防火墙或代理服务器是否已正确配置，请参阅 [Windows 代理的网络配置](../../azure-monitor/platform/agent-windows.md)或 [Linux 代理的网络配置](../../azure-monitor/learn/quick-collect-linux-computer.md)。

> [!NOTE]
> 如果 Linux 系统配置为与代理或 Log Analytics 网关通信，并且你要启用更新管理，请使用以下命令更新 `proxy.conf` 权限，以向 omiuser 组授予对文件的读取权限：
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

执行评估后，新添加的 Linux 代理会显示状态“已更新”。 此过程可能需要长达 6 小时的时间。

若要确认 Operations Manager 管理组是否正在与 Azure Monitor 日志通信，请参阅[验证 Operations Manager 与 Azure Monitor 日志的集成](../../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor)。

### <a name="single-azure-vm-assessment-queries-windows"></a>单个 Azure VM 评估查询 (Windows)

将 VMUUID 值替换为要查询的虚拟机的 VM GUID。 在 Azure Monitor 日志中运行以下查询可找到应使用的 VMUUID：`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>缺少更新摘要

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>缺少更新列表

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

### <a name="single-azure-vm-assessment-queries-linux"></a>单个 Azure VM 评估查询 (Linux)

对于某些 Linux 发行版，来自 Azure 资源管理器的 VMUUID 值与 Azure Monitor 日志中存储的 VMUUID 值存在[字节序](https://en.wikipedia.org/wiki/Endianness)不匹配情况。 以下查询可检查任一字节序的匹配情况。 使用 GUID 的 big-endian 和 little-endian 格式替换 VMUUID 值可正常地返回结果。 在 Azure Monitor 日志中运行以下查询可找到应使用的 VMUUID：`Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>缺少更新摘要

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>缺少更新列表

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

### <a name="multi-vm-assessment-queries"></a>多 VM 评估查询

#### <a name="computers-summary"></a>计算机摘要

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount
```

#### <a name="missing-updates-summary"></a>缺少更新摘要

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>计算机列表

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

#### <a name="missing-updates-list"></a>缺少更新列表

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="next-steps"></a>后续步骤

* 有关 Azure Monitor 日志的详细信息，请参阅 [Azure Monitor 日志](../../azure-monitor/log-query/log-query-overview.md)。
* 如需警报方面的帮助，请参阅[配置警报](update-mgmt-configure-alerts.md)。
